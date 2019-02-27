# Target-Action

## "环引用恐慌"

I completely understand confirming something with the lack of documentation can feel unsettling. The important thing to note is that, by over a decade of convention, target-action relationships are never strong. Here's [the relevant docs](https://developer.apple.com/library/ios/documentation/general/conceptual/CocoaEncyclopedia/Target-Action/Target-Action.html). Note the part that says:

```
Control objects do not (and should not) retain their targets.
```