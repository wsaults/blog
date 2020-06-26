## A Journey to the Center of SwiftUI with Firebase 
#### June 25th 2020

### For this entry we'll be using: 🧐
- Xcode Version 12.0 beta (12A6159)
- iOS 14.0
- Firebase

### Here's the plan 📖
Today we're going to take a look at writing a simple Todo app using Swift UI that's been updated for Xcode 12 beta and iOS 14. 
- First we'll discuss the removal of AppDelegate/SceneDelegate
- Then we'll jump into structuring our code using views, viewmodels, repositories, models, and services
- Next I'll show you how to construct the main list view
- After that we'll connect our view to Firebase
- Finally we'll wrap it up with Apple authentication

> Note: You should be comfortable with: cocoapods, Firebase console setup, and Xcode project setup. I won't be diving into these setup steps, You can get up to speed by following the following Firebase video tutorial (which also happens to be the content for this post) [SwiftUI/Firebase Video Tutorial part 1](https://www.youtube.com/watch?v=4RUeW5rUcww).

### Dude, where's my AppDelegate and SceneDelegate??? 🤔
Yep, it's gone 👋. Instead when we look in the Xcode fire explorer all we see is our new `ToDoFirebaseApp.swift` file.
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

### Models, Repos, and ViewModels. Oh my! 😱
Ok, let's setup our project structure that we'll use going forward.

First do the following:
- Create a group called `App` and move the `ToDoFirebaseApp.swift` file there.
- Create a group called `Services`
- Create a group called `Repositories`
- Create a group called `Models`
- Create a group called `ViewModels`
- Create a group called `Views`

![View-ViewModel-Repo](https://drive.google.com/file/d/15DGvue5jNMBnDiaHHI7I6JzzKkXV5zUO/view?usp=sharing)

### Next stop, TaskListView! 🙌
This view will contain the meat of our application. By the end of this post we'll be able to add, update, and complete tasks which are synced with Firebase.

Let's start by adding a `NavigationView` to a new `TaskListView` `SwiftUI` file which we'll save in a new `Views` group inside of `Xcode`.

#### Here's the steps:
- Create a new Group `Views`
- Create a new SwiftUI file `TaskListView` inside of `Views`
- Replace the `body` contents with a `NavigationView`

Here's what you'll have.
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

Now update your `ToDoFirebaseApp.swift` `body` with our `TaskListView`.
```swift
var body: some Scene {
    WindowGroup {
        TaskListView() // <-- Replace ContentView() with this
    }
}
```

Find top Swift development talent on [Toptal.com](https://www.toptal.com/swift).







```markdown
**Bold** and _Italic_ and `Code` text

![Image](src)
```
For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).
