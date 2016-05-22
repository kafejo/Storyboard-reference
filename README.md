# Storyboard reference

## What is the problem
Common pattern for instantiating view controllers from storyboard in code is not cool 😎. We usually need to hardcode storyboard ID and force cast to the right type and if we have multiple storyboards, we need to hardcode their names too. We end up with something like this

```swift
let userProfile = UIStoryboard(name: "User", bundle: nil).instantiateViewControllerWithIdentifier("UserProfileViewControllerID") as! UserProfileViewController
```

Well we can do better. How about something like this

```swift
let userProfile = Storyboard.User.ProfileVC.instantiate()
```

## How can we do this?

We create our own Storyboard reference. Firstly we need to define our storyboard type which will be a foundation for defining our storyboards.

```swift
protocol StoryboardType {
    static var name: String { get }
}
```
Simply as that - storyboad have to have name. Now we need storyboard reference to a view controller. It should specify in which storyboard the controller lies, what its ID is and what type it should be.

```swift
struct StoryboardReference<S: StoryboardType, T> {
    let id: String
}
```
where S is our storyboard type, T is controller type and id is our storyboardID. Now we have all we need to create the `instantiate()` function.

```swift
struct StoryboardReference<S: StoryboardType, T> {
    let id: String
    
    func instantiate() -> T {
        return UIStoryboard(name: S.name, bundle: nil).instantiateViewControllerWithIdentifier(id) as! T
    }
}
```

And finally we define our storyboards

```swift
struct Storyboard {

    struct User: StoryboardType {
        static let name: String = "User"
        
        static let ProfileVC = StoryboardReference<User, UserProfileViewController>(id: "UserProfileViewControllerID")
        static let SettingVC = StoryboardReference<User, UserSettingsViewController>(id: "UserSettingsViewControllerID")
    }
    
    struct Main: StoryboardType {
        static let name: String = "Main"
        
        static let Root = StoryboardReference<Main, RootViewController>(id:"RootViewControllerID")
    }
}
```

We defined 2 storyboards with and 3 view controllers. Now we can use it

```swift
let rootViewController = Storyboard.Main.Root.instantiate()
window.rootViewController = rootViewController
```

Cool 😎


