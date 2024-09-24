# LinkStack Blocks
### LinkStack blocks to expand the functionality of your instance.

*This page is still a work in progress.*

### ℹ️ About:

Blocks are self-contained add-ons that expand what you can display on your LinkStack page.

Blocks can be selected on the drop-down menu on the add links page.

All required files for the block should be included in the block folder. No editing of external files required. The block system takes care of loading assets, creating paths validation, translation etc.

> THE BLOCK SYSTEM IS STILL IN EARLY DEVELOPMENT, THINGS MAY CHANGE.
If you have any suggestions for improvements, please open an issue on this repository.

#### 📝 To do:
- [x] Dynamic file loading
- [x] Dynamic asset paths
- [x] Translation support
- [x] Validation
- [x] Variable storage
- [x] Pre-loaded libraries
- [x] Indipendent HTML
- [ ] File uploads
- [ ] Error handling
- [ ] Form submission on links page
- [ ] Page for admins to manage and upload blocks
- [ ] Place to share and download blocks
- [ ] Built-in block updater


# FAQ

<details>
<summary>What are LinkStack blocks?</summary>
LinkStack blocks are self-contained add-ons that expand the functionality of your LinkStack instance, allowing you to display additional features on your page.
</details>

<details>
<summary>How do I create my own block?</summary>
You can create your own block by following the structure outlined in the documentation, using the provided example block as a reference.
</details>

<details>
<summary>How do I add blocks to my site?</summary>
To add a block to your instance, you have to upload the block into the blocks directory on your server. Blocks can then be selected when adding a new link.
</details>

<details>
<summary>Do I need programming knowledge to create a block?</summary>
Yes. You should have prior experience with Laravel Blade, PHP, HTML, CSS, and JavaScript.
</details>

<details>
<summary>Where can I report issues or provide feedback about the block system?</summary>
You can open an issue on the repository to report problems or suggest improvements and new features.
</details>

<details>
<summary>How can I share my blocks with others?</summary>
A future feature will provide a dedicated space for sharing and downloading blocks. Currently, you can manually share your block directories.
</details>

<details>
<summary>Can I include third-party libraries in my block?</summary>
It is recommended to include all necessary assets locally to ensure compliance with regulations like GDPR.
</details>

<details>
<summary>Where is the data for my blocks stored?</summary>
Any block can store values inside the LinkStack database and are treated as any other link. The logic to store and display those values are contained in your block.
</details>

<details>
<summary>Are blocks secure?</summary>
Blocks have full access to your instance and database. It is not recommended installing blocks from untrusted sources.
</details>

# Create your own blocks

Use the [LinkStack Example Block](https://github.com/LinkStackOrg/example-block) as a practical demonstration of how to utilize the block system.
This repository features a fully functional block that you can read and modify.
It includes comprehensive comments to guide you through each step.
This example is intended to complement the existing documentation and provide further clarity.

## Introduction to Blocks

This powerful feature allows you to enhance your application by adding custom functionality. Here’s everything you need to know to get started:

### What are Blocks?

Blocks are directories added to your instance that contain all necessary components for functionality. Each installed block is read from the blocks directory and can be accessed through the blocks dropdown menu in your app.

### Block Components

Each block consists of three main components:

1. **User Interface (UI) Form**: Allows users to add or edit the block.
2. **Backend Component**: Validates and stores the block data securely.
3. **Display File**: Renders the block on the user’s links page.

### Configuration

A configuration file is essential for instructing the app on how to utilize your block effectively.

### Additional Features

- **Dynamic Asset Loading**: Any additional assets your theme requires can be dynamically loaded from your blocks folder.
- **Translation Support**: Built-in translation support enables your blocks to be displayed in multiple languages.
- **Automated Management**: The block system automates tasks such as asset loading, dynamic URLs, and database management.

With this framework, you can create anything from simple buttons to complex elements like embedded videos or dynamic feeds.

#### File tree:
```
.
└── your-theme/
    ├── config.yml
    ├── display.blade.php
    ├── form.blade.php
    ├── handler.php
    └── assets/
        └── /* Any assets you need live here */
```
# Make a block

Blocks are written in Laravel Blade and PHP, utilizing HTML, CSS, and JavaScript.
**Previous experience with all of these technologies is expected.**

The Blocks System offers a set of predefined functions designed to simplify tasks by automating specific operations. These functions are available for use across all three component files of each block.

### Loading assets from the block directory

Use `block_asset($file_path)` to generate a dynamic URL for loading assets from the block directory.
Files can be used from everywhere within the block folder. For general assets like JS or CSS files, it is recommended to put those in the `assets` directory. Subdirectories can be created as needed.

**Loading a CSS file from the block's assets folder could look something like this:**

_Blade_
```
<link rel="stylesheet" href="{{ block_asset('assets/style.css') }}">
```

### Fetching file contents from the block directory

Use `get_block_file_contents($file_path)` to load contents of a file from the block directory.
This could be used to load a custom config file for the block itself.

_Blade_
```
<p>{{ get_block_file_contents('values.json') }}</p>
```

_PHP_
```
$json_data = get_block_file_contents('values.json');
```

### Handling translated text
Always use `block_text($text)` or the shorthand `bt($text)` function for text. It uses the translated value if available, otherwise defaults to the input string.

_Blade_
```
<h1>{{ block_text('Text to translate.') }}</h1>
```

_PHP_
```
$text = block_text('Text to translate.');
```

## config.yml

```
typename: example
title: "Displayed title of your block"
description: "Short description."
icon: "bi bi-code-square"
custom_html: true
ignore_container: true

include_libraries:
  jquery: true
  sweetalert: true
```

The config is formatted as YAML.

> Most of these values are later stored with the block in the database.

- The `typename` must be the same as your folder name. The app uses this value to load your files.

- The `title` and `description` are displayed on the block selection page.

- `bi bi-code-square` is used as the icon on the blocks selection tab. The system currently only supports Bootstrap icons for this.

- `custom_html` defines if display.blade.php should be used to use custom HTML to display your block. If false, a generic button is used. In most use cases, this should be set `true`.

- `ignore_container` places your block outside the default container all LinkStack buttons are normally nested in. This is useful for elements other than buttons. To use this `custom_html` must be set to `true`.

- `include_libraries` allows you to load certain libraries that the app already includes, should your block need it. Any included libraries don't have to be included in your block files anymore and can be used as normal. To use this `custom_html` must be set to `true`.

## display.blade.php

This file is used to display the block on the user's links page.

### Loading assets once with `@once`

The `@once` directive is used to load assets only a single time, even if the block appears multiple times on the page. This is useful for preventing duplicate asset loading.

_Blade_
```
@once
    <script>
        var foo = 'bar';
    </script>
@endonce
```

### Ensuring correct asset order with `@push`

Use the `@push` directive to ensure assets are loaded in the correct order.
Combine it with `@once` for optimal performance.

Available positions include:
 - `linkstack-head`
 - `linkstack-head-end`
 - `linkstack-body-start`
 - `linkstack-body-end`

_Blade_
```
@push('linkstack-head-end')
    <style> body { background: green; } </style>
@endpush
```

### Using the `@push` and `@once` directive together

Assets and other files should be loaded, or global variables defined, only once when the page is loaded. Without using the `@once` directive, these elements may be repeated each time your block is displayed on the page.

The `@push` directive is typically used to run a section only once. You can use multiple `@push` and `@once` directives within a single file as needed.

Note that the order of the `@push` directives in your file is not significant.

_Blade_
```
@once
    @push('linkstack-head')
        <script src="{{block_asset('assets/some.js')}}"></script>
        <script>SomeFunction();</script>
        <link rel="stylesheet" href="{{block_asset('assets/some.css')}}">
        <style>
            .some-class {
                color: red;
            }
        </style>
    @endpush

    @push('linkstack-body-end')
        <script>
            const someConst = '{{block_text('Some text')}}';
        </script>
        <script src="{{block_asset('assets/library.js')}}"></script>
    @endpush
@endonce
```

### Accessing stored variables

Use `$link->some_value` to access stored variables.
Common values include:
  - `$link->id`
  - `$link->user_id`
  - `$link->button_id`
  - `$link->link`
  - `$link->title`
  - `$link->click_number`
  - `$link->created_at`

_Blade_
```
<div class="button">
    <a id="{{ $link->id }}" href="{{ $link->link }}">{{ $link->title }}</a>
    <span>This link has been clicked {{ $link->click_number ?? 0 }} times!</span>
</div>
```

## form.blade.php

This file is used to create **and** edit the block on the add/edit link page.

Your file is dynamically integrated into a form that submits your block via a POST request. Ensure that your file contains only the necessary form fields and assets, without any additional `<form>` tags.

_Structure_
```
<form type="submit" method="post">
    <!-- Loads: form.blade.php -->
</form>
```

**This page is styled using Bootstrap 5.**

### Accessing stored variables

In this file, stored values can be accessed using `$some_value` instead of `$link->some_value`. These variables should be utilized when the user is editing an existing block, ensuring that all form fields are pre-filled with existing data if available.

To enable an input field to be populated when editing a saved block, use the `??` operator to set default values and prevent errors. Set `null` if no value is necessary.

_Blade_
```
<div class="form-group">
    <input type="text" name="title" value="{{ $title }}">
    <input type="url" name="link" value="{{ $link }}">
    <input type="email" name="custom_value" value="{{ $custom_value ?? null }}">
</div>
```

## handler.php

This file validates and stores the block form input in the database.

> Everything should be handled within the `handleLinkType()` function.
> It replaces the backend controller and ensures data is processed and stored correctly.

_Structure_
```
function handleLinkType($request, $linkType) {
    $rules = [];
    $linkData = [];
    
    return ['rules' => $rules, 'linkData' => $linkData];
}
```

### Accessing submitted data

You can access any submitted value via the `$request` variable.

If the form field has `name="my_value"`, you can access it with: `$request->my_value`.
The same name should be used when validating or storing the input.

_PHP_
```
$some_value = $request->my_value;
```

### Validating user input

> **Always validate user input to ensure it's in the expected format.**

This step is crucial for ensuring the security of the application. Proper validation helps prevent malicious input and maintains data integrity, protecting against potential vulnerabilities. Always validate user inputs before processing or storing them.

- It is recommended to set maximum input lengths.
- It is **not** recommended storing large values or files this way.

Validation is performed using the `$rules` array, following standard Laravel validation rules. If the submitted data does not comply with the specified rules, the system will reject the request and redirect the user back to the form.
See: https://laravel.com/docs/11.x/validation#available-validation-rules

_PHP_
```
$rules = [
    'my_value' => [
        'required',
        'numeric',
        'max:1',
        'regex:/[0-9]%/',
    ],
    'other_value' => [
        'required',
        'string',
        'max:255',
    ],
];
```

### Storing values

After validation succeeds, values can be stored in the database.

- Add any value you want to save to the `$linkData` array.
- Predefined values include 'title' and 'link' (can be left empty).

_PHP_
```
$linkData = [
    'title' => $request->title,
    'link'  => $request->link
];
```

You can store any amount of custom values in `$linkData`. These values can be named however you like.

_PHP_
```
$linkData = [
    'title' => $request->title,
    'custom_value_1'  => $request->something,
    'custom_value_2'  => 'Some Text',
    'custom_value_3'  => $some_var,
    // Add more as needed
    ...
];
```

## General tips

### Testing your block

Thoroughly test your block alongside other elements on the links page and with various themes to ensure maximum compatibility. Additionally, verify that your block is secure against malicious attacks by validating user input on the backend.

### Styling

Most themes utilize a generic skeleton CSS template to provide default styling for elements. You may need to override styles using `!important` to achieve your desired results.

### Compliance with regulations

When creating blocks for the community, ensure that all assets, including libraries, stylesheets, and fonts, are included locally. This approach helps ensure compliance with GDPR and contributes to future-proofing your work.

### Naming variables and classes

When selecting names for CSS classes and JavaScript functions or classes, keep in mind that your block must be compatible with other blocks and themes. Since some systems may use identical names, a best practice is to add a unique prefix that corresponds to your block’s name.

---

<br>

# Development and Feedback

**This system is currently in development. Should you have any suggestions for changes or new features, please open an issue on this repository to share your ideas.**

**The overarching goal of this system is to make it as user-friendly and easy to learn as possible while maximizing its functionality.**