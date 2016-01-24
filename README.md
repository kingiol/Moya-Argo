# Moya-Argo

[![CI Status](http://img.shields.io/travis/Sam Watts/Moya-Argo.svg?style=flat)](https://travis-ci.org/Sam Watts/Moya-Argo)
[![Version](https://img.shields.io/cocoapods/v/Moya-Argo.svg?style=flat)](http://cocoapods.org/pods/Moya-Argo)
[![License](https://img.shields.io/cocoapods/l/Moya-Argo.svg?style=flat)](http://cocoapods.org/pods/Moya-Argo)
[![Platform](https://img.shields.io/cocoapods/p/Moya-Argo.svg?style=flat)](http://cocoapods.org/pods/Moya-Argo)

## Usage

To run the example project, clone the repo, and run `pod install` from the Example directory first.

## Requirements

## Installation

Moya-Argo is available through [CocoaPods](http://cocoapods.org). To install
it, simply add the following line to your Podfile:

```ruby
pod "Moya-Argo"
```

### Subspec

There are subspecs available for RxSwift and Reactive cocoa if you are using Moya/RxSwift or Moya/ReactiveCocoa. The pod names are `Moya-Argo/RxSwift` and `Moya-Argo/ReactiveCocoa` respectively

## Usage

### Argo
The first step is having a class / struct which can be mapped by [Argo](https://github.com/thoughtbot/Argo)

```swift
struct ArgoUser: Decodable {
    
    let id: Int
    let name: String
    
    let birthdate: String?
    
    static func decode(json: JSON) -> Decoded<ArgoUser> {
        return curry(ArgoUser.init)
            <^> json <| "id"
            <*> json <| "name"
            <*> json <|? "birthdate"
    }
}
```

### Moya.Response mapping
If you have a request setup with Moya already, you can use the `mapObject` and `mapArray` methods on the response:

```swift
provider
    .request(.AllUsers) { result in
            
        if case let .Success(response) = result {
        
            do {
                let argoUsers:[ArgoUser] = try response.mapArrayWithRootKey("users")
                print("mapped to users: \(argoUsers)")
            } catch {
                print("Error mapping useres: \(error)")
            }
        }
    }
```

### RxSwift
If you are using the Moya RxSwift extensions, there is an extension on Observable which will simplify the mapping:
```swift
provider
    .request(.AllUsers)
    .mapArray(ArgoUser.self, rootKey: "users")
    .observeOn(MainScheduler.instance)
    .subscribeNext { users in
            
        self.users = users
        self.tableView.reloadData()
            
    }.addDisposableTo(disposeBag)
```

### ReactiveCocoa
Or if ReactiveCocoa is more your style, there are similar extensions on SignalProducer:
```swift
provider
    .request(.AllUsers)
    .mapArray(ArgoUser.self, rootKey: "users")
    .observeOn(UIScheduler())
    .start { event in

        switch event {
        case .Next(let users):
            self.users = users
            self.tableView.reloadData()
        case .Failed(let error):
            print("error: \(error)")
        default: break
        }
    }
```

### Helpers
The example project shows some example methods which can be used to improve the readability of your code

## Contributing 
Issues / Pull Requests / Feedback welcome 

## Disclaimer
I'm using this in development of an upcoming app so I'm confident of its quality, but I am using the RxSwift extensions, so that is where most of my real world testing is based. There are unit tests for all extensions though. 


## Author

Sam Watts, samuel.watts@gmail.com

## License

Moya-Argo is available under the MIT license. See the LICENSE file for more info.
