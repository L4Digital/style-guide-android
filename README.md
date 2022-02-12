# Style Guidelines for Android
This document includes our style guidelines for Android development. It is sourced from Android’s official [Code Style Guidelines for Contributors](https://source.android.com/source/code-style.html), JetBrain's [Kotlin Coding Conventions](http://kotlinlang.org/docs/reference/coding-conventions.html), and Ribot's [Android Guidelines](https://github.com/ribot/android-guidelines/blob/master/project_and_code_guidelines.md) with some modifications. Not all existing code follows these rules, but all new code is expected to.

You can import these style guidelines into Android Studio's Code Style from [this Scheme](StyleGuide-Android.xml).

## Consistency
If you're editing code, take a few minutes to look at the code around you and determine its style. If they use spaces around their if clauses, you should too. If their comments have little boxes of stars around them, make your comments have little boxes of stars around them too.

The point of having style guidelines is to have a common vocabulary of coding, so people can concentrate on what you're saying, rather than on how you're saying it. We present global style rules here so people know the vocabulary. But local style is also important. If code you add to a a file looks drastically different from the existing code around it, it throws readers out of their rhythm when they go to read it. Try to avoid this. Above all else: **BE CONSISTENT**.

## Style Guides
* [Java Style Guide](JavaStyleGuide.md)
* [Kotlin Style Guide](KotlinStyleGuide.md)
* [Resource Style Guide](ResourceStyleGuide.md)
