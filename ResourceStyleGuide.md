# Resource Style Guidelines for Android
New projects should follow the Android Gradle project structure that is defined on the [Android Gradle plugin user guide](http://tools.android.com/tech-docs/new-build-system/user-guide#TOC-Project-Structure).

## Resource Files
Resources file names are written in `lowercase_underscore`.

### Drawable Files
Naming conventions for drawables:

| Asset Type    | Prefix            | Example                       |
| ------------- | ----------------- | ----------------------------- |
| Action bar    | `action_`         | `action_stacked.9.png`        |
| Button        | `button_`         | `button_send_pressed.9.png`   |
| Dialog        | `dialog_`         | `dialog_top.9.png`            |
| Divider       | `divider_`        | `divider_horizontal.9.png`    |
| Icon          | `ic_`             | `ic_star.png`                 |
| Menu          | `menu_`           | `menu_submenu_bg.9.png`       |
| Notification  | `notification_`   | `notification_bg.9.png`       |
| Tabs          | `tab_`            | `tab_pressed.9.png`           |

Naming conventions for icons (taken from [Android iconography guidelines](http://developer.android.com/design/style/iconography.html)):

| Asset Type                | Prefix            | Example                       |
| ------------------------- | ----------------- | ----------------------------- |
| Icons                     | `ic_`             | `ic_star.png`                 |
| Launcher icons            | `ic_launcher`     | `ic_launcher_calendar.png`    |
| Menu and Action Bar icons | `ic_menu`         | `ic_menu_archive.png`         |
| Status bar icons          | `ic_notify`       | `ic_notify_msg.png`           |
| Tab icons                 | `ic_tab`          | `ic_tab_recent.png`           |
| Dialog icons              | `ic_dialog`       | `ic_dialog_info.png`          |

Naming conventions for selector states:

| State     | Suffix        | Example                       |
| --------- | ------------- | ----------------------------- |
| Normal    | `_normal`     | `btn_order_normal.9.png`      |
| Pressed   | `_pressed`    | `btn_order_pressed.9.png`     |
| Focused   | `_focused`    | `btn_order_focused.9.png`     |
| Disabled  | `_disabled`   | `btn_order_disabled.9.png`    |
| Selected  | `_selected`   | `btn_order_selected.9.png`    |


### Layout Files
Layout files should match the name of the Android components that they are intended for but moving the top level component name to the beginning. For example, if we are creating a layout for the `SignInActivity`, the name of the layout file should be `activity_sign_in.xml`.

| Component         | Class Name                | Layout Name                   |
| ----------------- | ------------------------- | ----------------------------- |
| Activity          | `UserProfileActivity`     | `activity_user_profile.xml`   |
| Fragment          | `SignUpFragment`          | `fragment_sign_up.xml`        |
| Dialog            | `ChangePasswordDialog`    | `dialog_change_password.xml`  |
| Adapter item      | ---                       | `item_person.xml`             |
| Partial layout    | ---                       | `partial_stats_bar.xml`       |

A slightly different case is when we are creating a layout that is going to be inflated by an `Adapter`, e.g to populate a `RecyclerView`. In this case, the name of the layout should start with `item_`.

Note that there are cases where these rules will not be possible to apply. For example, when creating layout files that are intended to be part of other layouts. In this case you should use the prefix `partial_`.

### Menu Files
Similar to layout files, menu files should match the name of the component. For example, if we are defining a menu file that is going to be used in the `UserActivity`, then the name of the file should be `activity_user.xml`

A good practice is to not include the word `menu` as part of the name because these files are already located in the `menu` directory.

### Values Files
Resource files in the values folder should be plural, e.g. `strings.xml`, `styles.xml`, `colors.xml`, `dimens.xml`, `attrs.xml`

## Resource Naming
Resource IDs and names are written in `lowercase_underscore`.

### IDs
IDs should be prefixed with the name of the element in **lowercase_underscore**. For example:

| Element       | Prefix    |
| ------------- | --------- |
| `TextView`    | `text_`   |
| `ImageView`   | `image_`  |
| `Button`      | `button_` |
| `Menu`        | `menu_`   |

Image view example:

~~~xml
<ImageView
    android:id="@+id/image_profile"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content" />
~~~

Menu example:

~~~xml
<menu>
	<item
        android:id="@+id/menu_done"
        android:title="@string/menu_done" />
</menu>
~~~

### Strings
String names start with a prefix that identifies the section they belong to. For example `registration_email_hint` or `registration_name_hint`. If a string doesn’t belong to any section, then you should follow the rules below:

| Prefix    | Description                           |
| --------- | ------------------------------------- |
| `error_`  | An error message                      |
| `msg_`    | A regular information message         |
| `title_`  | A title, i.e. a dialog title          |
| `action_` | An action such as “Save” or “Create”  |
| `menu_`   | A menu item such as “Done”            |

### Styles and Themes
Style names are written in `UpperCamelCase`.

## XML Style Rules

### Use XML Comments
Every file should have a copyright statement at the top. Then layout tags should follow, each block separated by a blank line. We have provided [copyright templates in xml format](https://drive.google.com/open?id=0B5fbz6yN_ewPTXhqdl9VdEhNWFU) that can be imported into Android Studio and IntelliJ.

~~~xml
<?xml version="1.0" encoding="utf-8"?>
<!--
  ~ Copyright (C) 2013 The Android Open Source Project
  ~
  ~ Licensed under the Apache License, Version 2.0 (the "License");
  ~ you may not use this file except in compliance with the License.
  ~ You may obtain a copy of the License at
  ~
  ~      http://www.apache.org/licenses/LICENSE-2.0
  ~
  ~ Unless required by applicable law or agreed to in writing, software
  ~ distributed under the License is distributed on an "AS IS" BASIS,
  ~ WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  ~ See the License for the specific language governing permissions and
  ~ limitations under the License.
  -->

<FrameLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
~~~

### Use Self Closing Tags
When an XML element doesn’t have any contents, you **must** use self closing tags.

This is good:

~~~xml
<TextView
    android:id="@+id/text_view_profile"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content" />
~~~

This is bad:

~~~xml
<!-- Don’t do this! -->
<TextView
    android:id="@+id/text_view_profile"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content">
</TextView>
~~~

### Attribute Ordering
As a general rule you should try to group similar attributes together. A good way of ordering the most common attributes is:

1. View Id
1. Layout width and height
1. Other layout attributes, sorted alphabetically
1. Remaining attributes, sorted alphabetically
1. Style

Each attribute should also be written on a new line.

This is good:

~~~xml
<TextView
    android:id="@+id/text_view_profile"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content" />
~~~

This is bad:

~~~xml
<TextView android:id="@+id/text_view_profile"
    android:layout_width="wrap_content" android:layout_height="wrap_content" />
~~~

## License
    Copyright 2017 L4 Digital LLC. All rights reserved.

    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at

         http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.