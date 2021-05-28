<style>
* {
  box-sizing: border-box;
}

/* Create two equal columns that floats next to each other */
.column {
  float: left;
  width: 50%;
  padding: 10px;
}

/* Clear floats after the columns */
.row:after {
  content: "";
  display: table;
  clear: both;
}
</style>

Cityjson validator on the web using Rust & Wasm
===============================================

| | |
|---|---|
|Student | Jos Feenstra 
|Code | 4465768
|Email | me@josfeenstra.nl  
|Supervisor | Hugo Ledoux



Opdracht
--------



Deliverables
------------




Process
=======


## 1. Website

When starting something new, it is often nice to start from what you do know, and then proceed to tackle the unknown. This is why I started out by just creating a website in which you could drag & drop or select a json file. I have done similar things before, so after a bit of wrestling with the way javascript deals with events, callbacks, and the way css works, the website itself was there: 


(Image Website). 


A bit bland maybe, but hey it works! At least, you could select a json file, and it would print it out in the console as a `.js` object. 




## 2. The Code Architecture 

Now the fun part begins. While I have tried some wasm projects before, I did so by strictly following tutorials. For this project, I wished to have a better understanding of what I was actually doing. This way, I can judge if certain tools & processes are worth it, or could be left out. 

It took some time to get the hang of rust, wasm, and the different tools aiding the interoperability between the rust & javascript worlds. There where many ways of setting this up. 

I found three main ways of doing this: 
- The [Recommended]() way of setting up a rust-wasm project, 
- The [Monorepo] style of setting up, and
- The [No Webpack]() version. 




...



## 3. Rust

It was at this point that I 

The instinct to go back to Rust, and to test code 'on dry land' (dutch saying: 'op 't droge') before diving in deep waters.

...



## 4. The Solution 

Now that the cityjson validator was working on a basic level, it had to somehow be incorporated within the website created at **1**. The funny thing is that my instinct to go back to only using rust, also appeared to be the solution to all my code architecture troubles at **2**. It's a huge feature, that the exact same piece of code can be run locally & online. Additionally, the fact that the javascript & rust environments don't need to know each other directly is a very nice [decoupling](https://gameprogrammingpatterns.com/decoupling-patterns.html) feature. 

I now knew exactly what I wanted to do








...

In the rust code itself, only three additions had to be made:

```rust
extern crate serde_json;
extern crate jsonschema;

use wasm_bindgen::prelude::*; // ADDITION

use serde_json::{Value as Json};
use jsonschema::{JSONSchema, paths::JSONPointer};
use std::collections::HashMap;

#[wasm_bindgen] // ADDITION
pub struct CityJsonValidator {
    schema: Json,
}

#[wasm_bindgen] // ADDITION
impl CityJsonValidator {

    pub fn new_from_string(schema_string: &str) -> Self {
        println!("converting jsons...");
        let schema = CityJsonValidator::str_to_json(schema_string);
        return Self::new(schema);
    }

    pub fn validate_from_str(&self, instance_string: &str) -> bool {
        let json = &CityJsonValidator::str_to_json(instance_string);
        return self.validate(json);
    }
}
```

And that's it! This crate is now ready for web deployment. I really like how little interventions are needed to add such a huge feature. It would be even more sophisticated to make these compiler statements optional, but I have not figured out how to do that yet...

The way `wasm-bindgen` works here, is that, together with `wasm-pack`, it generates a tiny javascript version of the `CityJsonValidator`. The implementation of this class is made in such a way that it correctly converts javascript data to data wasm can use, and then calls the according methods found in the wasm file. It is important to note that, even though it may feel seemless to a user thanks to these excellent tools, a conversion step always occurs. Calls in between wasm & js should therefore be kept to a minimum.




<br/><br/>



The fact that Rust allows multiple trait implementations is also a very nice feature for our use case. Here you can see how that was used to define a trait's `private`, `public`, and `'wasm public'` section.  

```rust

// wasm public 
#[wasm_bindgen] 
impl CityJsonValidator {

    pub fn new_from_string(schema_string: &str) -> Self {
        ...
    }

    pub fn validate_from_str(&self, instance_string: &str) -> bool {
        ...
    }
}

// public
impl CityJsonValidator {

    pub fn new(schema: Json) -> Self {
        ...
    }

    pub fn validate(&self, instance: &Json) -> bool {
        ...
    }

    // helper function to create a serde json
    pub fn str_to_json(json_string: &str) -> Json {
        ...
    }
}

// private 
impl CityJsonValidator {

    // validate json schema using the 'jsonschema' crate
    fn validate_schema(&self, instance: &Json) -> Result<(), &str> {
        ...
    }

    // validate some other property
    fn validate_no_duplicate_vertices(&self, instance: &Json) -> bool {
        ...
    }

    fn validate_some_other_property(&self, instance: &Json) -> bool {
        ...
    }
}
```

This way, we can control what parts of our class are exposed to rust and the cli-environment, and which parts are accessible by javascript using wasm. This is technically needed, since the 'Json' objects are Rust objects, which we do not wish to expose to javascript. Strings are much easier to pass between rust & javascript than cascading objects / enums.

But, during development, it was also really useful to think of the wasm-exposed parts as an extension of the `private` and `public` sequence: `private`, `public`, and then something like `super-public`. I've got a couple of good ideas for the code architecture