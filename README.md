<p align="center">
<img src="https://danger.systems/images/js/danger-js-sw-logo-hero-cachable@2x.png" width=350 /></br>
Formalize your Pull Request etiquette.
</p>

Write your Dangerfiles in Swift.

### Requirements

Latest version requires Swift 4.2

- If you are using Swift 4.1 use v0.4.1
- If you are using Swift 4.0, Use v0.3.6

### What it looks like today

You can make a Dangerfile that looks through PR metadata, it's fully typed.

```swift
import Danger

let danger = Danger()
let allSourceFiles = danger.git.modifiedFiles + danger.git.createdFiles

let changelogChanged = allSourceFiles.contains("CHANGELOG.md")
let sourceChanges = allSourceFiles.first(where: { $0.hasPrefix("Sources") })

if !changelogChanged && sourceChanges != nil {
  warn("No CHANGELOG entry added.")
}

// You can use these functions to send feedback:
message("Highlight something in the table")
warn("Something pretty bad, but not important enough to fail the build")
fail("Something that must be changed")

markdown("Free-form markdown that goes under the table, so you can do whatever.")
```

### Using Danger Swift

All of the docs are on the user-facing website: https://danger.systems/swift/

### Commands

- `danger-swift ci` - Use this on CI
- `danger-swift pr https://github.com/Moya/Harvey/pull/23` - Use this to build your Dangerfile
- `danger-swift local` - Use this to run danger against your local changes from master
- `danger-swift edit` - Creates a temporary Xcode project for working on a Dangerfile

#### Plugins

Infrastructure exists to support plugins, which can help you avoid repeating the same Danger rules across separate
repos.

e.g. A plugin implemented with the following at https://github.com/username/DangerPlugin.git.

```swift
// DangerPlugin.swift
import Danger

public struct DangerPlugin {
    let danger = Danger()
    public static func doYourThing() {
        // Code goes here
    }
}
```

#### Swift Package Manager (More performant)

You can use Swift PM to install both `danger-swift` and your plugins:

- Add to your `Package.swift`:

  ```swift
  let package = Package(
      ...
      products: [
          ...
          .library(name: "DangerDeps[Product name (optional)]", type: .dynamic, targets: ["DangerDependencies"]), // dev
          ...
      ],
      dependencies: [
          ...
          .package(url: "https://github.com/danger/swift.git", from: "1.0.0"), // dev
          // Danger Plugins
          .package(url: "https://github.com/username/DangerPlugin.git", from: "0.1.0") // dev
          ...
      ],
      targets: [
          .target(name: "DangerDependencies", dependencies: ["Danger", "DangerPlugin"]), // dev
          ...
      ]
  )
  ```

- Add the correct import to your `Dangerfile.swift`:

  ```swift
  import DangerPlugin

  DangerPlugin.doYourThing()
  ```

- Create a folder called `DangerDependencies` in `Sources` with an empty file inside like
  [Fake.swift](Sources/Sources/Danger-Swift/Fake.swift)
- To run `Danger` use `swift run danger-swift command`
- **(Recommended)** If you are using Swift PM to distribute your framework, use
  [Rocket](https://github.com/f-meloni/Rocket), or a similar tool, to comment out all the dev dependencies from your
  `Package.swift`. This prevents these dev dependencies from being downloaded and compiled with your framework by
  consumers.
- **(Recommended)** cache the `.build` folder on your repo

#### Marathon (Easy to use)

By suffixing `package: [url]` to an import, you can directly import Swift PM package as a dependency

For example, a plugin could be used by the following.

```swift
// Dangerfile.swift

import DangerPlugin // package: https://github.com/username/DangerPlugin.git

DangerPlugin.doYourThing()
```

You can see an [example danger-swift plugin](https://github.com/ashfurrow/danger-swiftlint#danger-swiftlint).

**(Recommended)** Cache the `~/.danger-swift` folder

### Setup

For a Mac:

```sh
# Install danger-swift, and a bundled danger-js locally
[![FOSSA Status](https://app.fossa.io/api/projects/git%2Bgithub.com%2Fdyna-dot%2FSwift-5.0-For-Linux.svg?type=shield)](https://app.fossa.io/projects/git%2Bgithub.com%2Fdyna-dot%2FSwift-5.0-For-Linux?ref=badge_shield)

brew install danger/tap/danger-swift
 # Run danger
danger-swift ci
```

For Linux:

```sh
# Install danger-swift
git clone https://github.com/danger/danger-swift.git
cd danger-swift
make install

# Install danger-js
npm install -g danger

 # Run danger
danger-swift ci
```

With Docker support ready for GitHub Actions.

#### Local compiled danger-js

To use a local compiled copy of danger-js use the `danger-js-path` argument:

```
danger-swift command --danger-js-path path/to/danger-js
```

#### Dev

You need to be using Xcode 10.

```sh
git clone https://github.com/danger/danger-swift.git
cd danger-swift
swift build
swift run komondor install
swift package generate-xcodeproj
open Danger.xcodeproj
```

Then I tend to run `danger-swift` using `swift run`:

```sh
swift run danger-swift pr https://github.com/danger/swift/pull/95
```

If you want to emulate how DangerJS's `process` will work entirely, then use:

```sh
swift build && cat Fixtures/eidolon_609.json | ./.build/debug/danger-swift
```

#### Deploying

Run `swift run rocket $VERSION` on `master` e.g. `swift run rocket 1.0.0`

### Long-term

I, orta, only plan on bootstrapping this project, as I won't be using this in production. I'm happy to help support
others who want to own this idea and really make it shine though! So if you're interested in helping out, make a few PRs
and I'll give you org access.

[m]: https://github.com/JohnSundell/Marathon
[spm-lr]: http://bhargavg.com/swift/2016/06/11/how-swiftpm-parses-manifest-file.html
[dsl]: https://github.com/danger/danger-js/pull/341


## License
[![FOSSA Status](https://app.fossa.io/api/projects/git%2Bgithub.com%2Fdyna-dot%2FSwift-5.0-For-Linux.svg?type=large)](https://app.fossa.io/projects/git%2Bgithub.com%2Fdyna-dot%2FSwift-5.0-For-Linux?ref=badge_large)