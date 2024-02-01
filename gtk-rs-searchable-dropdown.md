Title: Creating a Searchable Dropdown of Strings in GTK-rs (Rust + GTK4)
Date: 2024-02-01
Tags: Linux
Category: Linux

A short post because this took me hours of trial and error to figure out.

```rust
// Step 1:
let my_string_vec = vec!["one", "two", "apples", "appstore", "append"];
let imgs_strlist = gtk::StringList::new(my_string_vec.as_slice());

// Step 2:
let exp = gtk::PropertyExpression::new(
    gtk::StringObject::static_type(),
    None::<gtk::Expression>,
    "string",
);

// Step 3:
let my_select = gtk::DropDown::new(Some(imgs_strlist), Some(exp));
my_select.set_enable_search(true);
my_select.set_search_match_mode(gtk::StringFilterMatchMode::Substring);
```

## Step 1
Get your vector of strings (i.e. your dropdown options) from wherever and create a `StringList` from them. This is the Model for your Dropdown.

## Step 2
Now you need an Expression. I won't pretend I fully grok what these are, even after all this research, but you need it. 

## Step 3
Now build your DropDown passing your StringList as the Model and the Expression as the Expression. Then use `set_enable_search` to allow searching, and `set_search_match_mode` to set
how the searching will work.

## That's it
It's very simple when you know what to do, but when you don't it's not at all obvious.
