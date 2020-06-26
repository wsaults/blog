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
There's not much here... but it's all you need now to launch the app, phew.

```
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


Find top swift development talent on [Toptal.com](https://www.toptal.com/swift).







```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).
