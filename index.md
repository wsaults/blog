# A Journey to the Center of SwiftUI with Firebase 
#### June 25th 2020

## For this entry we'll be using: 🧐
- Xcode Version 12.0 beta (12A6159)
- iOS 14.0
- Firebase

## Here's the plan 📖
Today we're going to take a look at writing a simple Todo app using Swift UI that's been updated for Xcode 12 beta and iOS 14. 
- First we'll discuss the removal of AppDelegate/SceneDelegate
- Then we'll jump into structuring our code using views, viewmodels, repositories, models, and services
- Next I'll show you how to construct the main list view
- Finally we'll connect our view to Firebase

When you're done why not stop by Toptal to see their amazing Swift developers!?
[https://www.toptal.com/swift](https://www.toptal.com/swift)

> Note: You should be comfortable with: cocoapods, Firebase console setup, and Xcode project setup. I won't be diving into these setup steps, You can get up to speed by following the following Firebase video tutorial (which also happens to be the content for this post) [SwiftUI/Firebase Video Tutorial part 1](https://www.youtube.com/watch?v=4RUeW5rUcww).

## Dude, where's my AppDelegate and SceneDelegate??? 🤔
Yep, it's gone 👋. Instead when we look in the Xcode file explorer all we see is our new `ToDoFirebaseApp.swift` file.
There's not much here... but it's all you need now to launch the app, phew 😅.

```swift
import SwiftUI

@main
struct ToDoFirebaseApp: App {    
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
    }
}
```

## Models, Repos, and ViewModels. Oh my! 😱
Ok, let's setup our project structure that we'll use going forward.

First do the following:
- Create a group called `App` and move the `ToDoFirebaseApp.swift` file there.
- Create a group called `Services`
- Create a group called `Repositories`
- Create a group called `Models`
- Create a group called `ViewModels`
- Create a group called `Views`

Your project explorer should look like this.

![File Explorer](https://github.com/wsaults/Knowledge-Database/blob/master/Coding/Swift/ToDoFirebaseExplorer_1--small.png?raw=true)

> Keep the following architecture in mind as well build our Todo App. This will drive how our components communicate and interact.

![View-ViewModel-Repo](https://raw.githubusercontent.com/wsaults/Knowledge-Database/master/Coding/Swift/View-ViewModel-Repo.png)

- Views represent the portion visible to the user.
- ViewModels are responsible for holding the data the populates our views.
- Models represent the structure of our data. eg: user, task, or 🚗.
- Repositories will communicate with our data store (Firebase) to perform CRUD operations.
- Storge represents the Firebase db.

You're doing awesome! Now, let's write some code! 💻

## Next stop, TaskListView! 🚂 🙌
This view will contain the meat of our application. By the end of this post we'll be able to add, update, and complete tasks which are synced with Firebase.

Let's start by adding a `NavigationView` to a new `TaskListView` `SwiftUI` file which we'll save in our `Views` group.

### Here's the steps:
- Create a new SwiftUI file `TaskListView` inside of `Views`
- Replace the `body` contents with a `NavigationView`
- Create a list, add button, and navigation sugar 🍰
- Create our model, repository, and viewModels
- Update our `ToDoFirebaseApp` file to load the `TaskListView`

#### Here's what you'll have.
```swift
import SwiftUI

struct TaskListView: View {    
    var body: some View {
        NavigationView {
        }
    }
}

struct TaskListView_Previews: PreviewProvider {
    static var previews: some View {
        TaskListView()
    }
}
```

#### It's list time! ⏲
Let's update our `TaskListView` body to look like this...
```swift
var body: some View {
        NavigationView {
            VStack(alignment: .leading) {
                List {
                    ForEach() { 
                    }
                }
                Button(action: {  }) {
                    HStack {
                        Image(systemName: "plus.circle.fill")
                            .resizable()
                            .frame(width: 20, height: 20)
                        Text("Add new task")
                    }
                }
                .padding()
            }
            .navigationBarTitle("Tasks")
        }
    }
```

#### There be models 🐉
Now go ahead and create `Task.swift` inside your models group.

```swift
import Foundation
import FirebaseFirestore
import FirebaseFirestoreSwift

struct Task: Codable, Identifiable {
    @DocumentID var id: String?                     // Lets Firebase know that it can assign a document ID here
    var title: String                               // The text of the task
    var completed: Bool                             // Will govern the checkmark state of the task
    @ServerTimestamp var createdTime: Timestamp?    // Lets Firebase know that it can assign a timestamp here
    var userId: String?                             // Allows us to keep track of who created the task.
}
```

#### Here comes the Repoman 🦾
Create a `TaskRepository.swift` file in your repositories group with the following code.

```swift
import Foundation
import Firebase
import FirebaseFirestore
import FirebaseFirestoreSwift

class TaskRepository: ObservableObject {
    
    let db = Firestore.firestore()      // Holds the firestore database reference
    
    @Published var tasks = [Task]()     // Holds onto our tasks
    
    init() {
        loadData()                      // We'll use this to load our data very soon.
    }
    
    func loadData() {
    }
    
    func addTask(_ task: Task) {
    }
    
    func updateTask(_ task: Task) {
    }
}
```

#### Knock, knock. Who's there? ViewModels. 😬
Create a `TaskListCellViewModel.swift` and a `TaskListViewModel.swift` file in your viewModels group with the following code.

```swift
import Combine

class TaskCellViewModel: ObservableObject, Identifiable {
    @Published var taskRepository = TaskRepository()
    
    @Published var task: Task
    
    var id = ""
    @Published var completionStateIconName = ""
    
    private var cancellables = Set<AnyCancellable>()
    
    init(task: Task) {
        self.task = task
        
        $task
        .map { task in
            task.completed ? "checkmark.circle.fill" : "circle"
        }
        .assign(to: \.completionStateIconName, on: self)
        .store(in: &cancellables)
        
        $task
        .compactMap { task in
            task.id
        }
        .assign(to: \.id, on: self)
        .store(in: &cancellables)
        
        // Happens anytime there is a change
        // Includes typing updates which can be intensive
        $task
        .dropFirst()
        .debounce(for: 0.8, scheduler: RunLoop.main)
        .sink { task in
            self.taskRepository.updateTask(task)
        }
        .store(in: &cancellables)
    }
}
```

```swift
import Foundation
import Combine

class TaskListViewModel: ObservableObject {
    @Published var taskRepository = TaskRepository()
    @Published var taskCellViewModels = [TaskCellViewModel]()
    
    private var cancellables = Set<AnyCancellable>()
    
    init() {
        taskRepository.$tasks.map { tasks in
            tasks.map { task in
                TaskCellViewModel(task: task)
            }
        }
        .assign(to: \.taskCellViewModels, on: self)
        .store(in: &cancellables)
    }
    
    func addTask(task: Task) {
        taskRepository.addTask(task)
    }
}
```

#### Back to the TaskListView ⏱ 🏎💨
Jump back into your `TaskListView` and lets make the following changes.

Add a reference to `taskListViewModel` right under the `struct TaskListView` line.
```swift
struct TaskListView: View {
    @ObservedObject var taskListViewModel = TaskListViewModel()
    ...
```

#### Now update your `ToDoFirebaseApp.swift` `body` with our `TaskListView`.
```swift
var body: some Scene {
    WindowGroup {
        TaskListView() // <-- Replace ContentView() with this
    }
}
```

Next, update the ForEach.
```swift
ForEach(taskListViewModel.taskCellViewModels) { taskCellViewModel in
    TaskCell(taskCellViewModel: taskCellViewModel)
}
```

> Wait... what TaskCell?? We'll add that now.

#### TaskCell! 🎉
Add the following below the `TaskListView_Previews` section
```swift
struct TaskCell: View {
    @ObservedObject var taskCellViewModel: TaskCellViewModel
    
    var onCommit: (Task) -> (Void) = { _ in }
    
    var body: some View {
        HStack {
            Image(systemName: taskCellViewModel.task.completed ? "checkmark.circle.fill" : "circle")
                .resizable()
                .frame(width: 20, height: 20)
                .onTapGesture {
                    self.taskCellViewModel.task.completed.toggle()
                }
            TextField("Enter the task title", text: $taskCellViewModel.task.title, onCommit: {
                self.onCommit(self.taskCellViewModel.task)
            })
        }
    }
}
```

Well that's great but we still don't have any data... Keep going, we're almost there!

## Let's wire it up like the Cable Guy 🤖

In the `TaskListView` we need to handle adding tasks.

Add this line below your `taskListViewModel`.
```swift
@State var presentAddNewItem = false
```

After the `ForeEach` block let's add our `TaskCell` for creating new items in the list.
```swift
if presentAddNewItem {
    TaskCell(taskCellViewModel: TaskCellViewModel(task: Task(title: "", completed: false))) { task in
        self.taskListViewModel.addTask(task: task)
        self.presentAddNewItem.toggle()
    }
}
```

Next, we need to update our button action.
```swift
Button(action: { self.presentAddNewItem.toggle() }) { // Change this line
```

#### Ok, jump back into the `TaskRepository`

Time to give life to our `loadData`, `addTask`, and `updateTask` functions.
```swift
func loadData() {
    guard let userId = Auth.auth().currentUser?.uid else {
        fatalError("Unable to get user id")
    }

    db.collection("tasks")
        .order(by: "createdTime")
        .whereField("userId", isEqualTo: userId)
        .addSnapshotListener { (querySnapshot, error) in
        if let querySnapshot = querySnapshot {
            self.tasks = querySnapshot.documents.compactMap { document in
                do {
                    let x = try document.data(as: Task.self)
                    return x
                }
                catch {
                    print(error.localizedDescription)
                }
                return nil
            }
        }
    }
}

func addTask(_ task: Task) {
    do {
        var addedTask = task
        addedTask.userId = Auth.auth().currentUser?.uid
        try _ = db.collection("tasks").addDocument(from: addedTask)
    }
    catch {
        fatalError("Unable to encode task \(error.localizedDescription)")
    }
}

func updateTask(_ task: Task) {
    if let taskID = task.id {
        do {
            try db.collection("tasks").document(taskID).setData(from: task)
        }
        catch {
            fatalError("Unable to encode task: \(error.localizedDescription)")
        }
    }
}
```

#### Last stop `ToDoFirebaseApp`

Add the following above the `body`
```swift
init() {
    FirebaseApp.configure()
    Auth.auth().signInAnonymously()
}
```

And that's it! You're now ready to add tasks to your Firebase Firestore!!
Thanks for joining me on this adventure.

You can view the entire project on [github](https://github.com/wsaults/ToDoFirebaseSwiftUI).

Find top Swift development talent on [Toptal.com](https://www.toptal.com/swift).


---

### Things to make this post better:
- Add details to the repository and view models
- Add screenshots of the app UI state
- Add more notes about why Swift/SwiftUI is so Rad.
