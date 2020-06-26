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
- After that we'll connect our view to Firebase
- Finally we'll wrap it up with Apple authentication

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

#### Now update your `ToDoFirebaseApp.swift` `body` with our `TaskListView`.
```swift
var body: some Scene {
    WindowGroup {
        TaskListView() // <-- Replace ContentView() with this
    }
}
```

Find top Swift development talent on [Toptal.com](https://www.toptal.com/swift).
