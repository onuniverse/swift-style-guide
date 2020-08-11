# The Official Universe SwiftLint Policy

The SwiftLint configuration in this repo is designed to ensure the code we write at Universe conforms with [The Official Universe Swift Style Guide](https://github.com/onuniverse/swift-style-guide). The policies described here have a goal of achieving consistency between all of our projects.

These guides use SwiftLint as a standard. You can learn more about SwiftLint by visiting its [official documentation page](https://github.com/realm/SwiftLint).

## Table of Contents

* [Installing SwiftLint](#installing-swiftlint)
* [Using the configuration file](#using-the-configuration-file)
* [Xcode settings](#xcode-settings)
* [Running SwiftLint](#running-swiftlint)
* [Handling rule exceptions](#handling-rule-exceptions)
* [Approved exceptions](#approved-exceptions)
	* [Implicitly Unwrapped Optionals](#implicitly-unwrapped-optionals)
	* [Force cast](#force-cast)
	* [Force unwrapping](#force-unwrapping)
	* [SwiftUI and multiple trailing closures](#swiftui-and-multiple-trailing-closures)
	* [Open-source files](#open-source-files)
* [Other notes](#other-notes)

## Installing SwiftLint

SwiftLint is setup as a Cocoapod and Build step inside the iOS project already. So once you've done `pod install` you already have everything you need to see warnings from SwiftLint.

## Using the configuration file

Inside the iOS project, you'll see a `.swiftlint.yml` already present. You won't need to make any changes to get up and running. There is also a copy of the Universe SwiftLint yaml file [here](universe.swiftlint.yml) in the style guide.

## Xcode settings

You'll need to configure Xcode to remove trailing whitespace from all lines. This is not the default configuration.

In Xcode's Preferences, select **Text Editing** ▸ **Editing** and check **Including whitespace-only lines**. 

![](screens/trailing-whitespace.png)

## Running SwiftLint

SwiftLint is already setup as a Build step in the iOS project. To set it up in a new project you will:

1. Select the project document in the Project navigator.
1. Select the **Build Phases** tab.
1. Click **+** and choose **New Run Script Phase**.

![](screens/add-run-script.png)

4. Drag the new phase before the **Compile Sources** phase.
4. Click the disclosure triangle on the **Run Script** phase and ensure **Shell** is set to **/bin/sh**.

![](screens/empty-run-script.png)

6. Add the following script:
```
${PODS_ROOT}/SwiftLint/swiftlint lint --config $PROJECT_DIR/.swiftlint.yml
```

## Handling rule exceptions

Your code must compile without warnings — SwiftLint or otherwise. In general, you should change your code to eliminate all warnings where necessary. When it comes to SwiftLint, however, there will be times when this isn't possible. In these situations, you'll need to use in-line comments to temporarily disable rules. You can find appropriate syntax to do this in [the SwiftLint documentation](https://realm.github.io/SwiftLint/#disable-rules-in-code).

You may only disable a rule if it is on the list of approved exceptions listed below.

Prefer the form that disables a rule only for the next line:
```
// swiftlint:disable:next type_name
struct iTunesAPI {
```

Or, similarly, for the previous line:
```
struct iTunesAPI {
// swiftlint:disable:previous type_name
```

If there are several approved exceptions, group them together and disable the rule for that region. Be sure to enable the rule after that section. Do not leave a rule disabled throughout the source file.

```
// swiftlint:disable identifier_name
public enum Tier: String {
    case a = "A"
    case b = "B"
    case c = "C"
    case d = "D"
    case e = "E"
    case f = "F"
    case g = "G"
    case h = "H"
    case i = "I"
    case j = "J"
    case k = "K"
    case l = "L"
    case m = "M"
    case n = "N"
}
// swiftlint:enable identifier_name
```

If you must disable rules in your project, leave those in-line comments in the project for the benefit of your teammates in the editing pipeline.

Finally, if you're not sure which rule is triggering a warning, you can find the rule name in parentheses at the end of message:

![](screens/swiftlint-warning.png)

## Approved exceptions

There are certain common idioms that violate SwiftLint's strict checking. If you are unable to work around them — and you've already tried to find a better way to structure your code — you may disable rules as described in this section.

If you find that you're struggling with rules other than those described below, please reach out to your Team Lead with your specific example.

### SwiftUI and multiple trailing closures

Idiomatic SwiftUI uses trailing closures to provide the view content for certain user interface elements. `Button` is a prime example; it has an initializer form that uses a closure to provide its `label`. It's common to write something like the following:

```
Button(action: { self.isPresented.toggle() }) {
  Image(systemName: "plus")
}
```

This violates the rule that you should not use trailing closure syntax when a method accepts multiple closure parameters, so SwiftLint will flag it with a warning. 

You can work around this by extracting the `Button`'s action into a separate method. While this is frequently a better solution when the action requires several statements, it's an onerous requirement when the action is a single statement, as in the example above.

In these cases, you're permitted to disable this rule **for the declaration of a SwiftUI view** only. The rule name is `multiple_closures_with_trailing_closure`.

### Open-source files

Occasionally, you'll find it necessary to include an unmodified open-source file in the sample project. It's a virtual certainty that these files won't comply with our style guide. Our practice has always been to leave these files in their original state. In this situation, you should disable SwiftLint for the entire file:

```
// swiftlint:disable all
```

## Other notes

While SwiftLint goes a long way towards making your source code compliant with our style guide, it doesn't cover everything. For example, it won't catch or force you to correct the formatting for multi-condition `guard` statements. (See [Golden Path](https://github.com/onuniverse/swift-style-guide#golden-path) for correct formatting.)

If you find yourself butting heads with SwiftLint, please reach out to the iOS Team Lead with details.