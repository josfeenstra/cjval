TODO
====

Concrete Steps
--------------

  - [X] write intro to Clara 
  - [X] build markup interface, drag & drop a json, and print it out
  - [X] make a 'on dry land' cityjson validator, aka : a pure rust cli-app which correctly validates a cityjson. 
    - [X] Build simple cli interface
    - [X] get serde & jsonschema libraries to work
    - [X] print out the errors in a consistent way
  - [X] make a hello world - level connection to wasm 
    > [JF] I completed a bit of the tutorial on the rust X wasm page, where you have to implement the game of life. 

  - [ ] overcome obstacle : jsonschema crate is not compilable to wasm
    - [.] option 1 : fork the repo, remove all calls to I/O type business, we wont need them
      - [ ] how to refer to a local crate 
    - [ ] option 2 : see if a workaround can be created using wasm bindgen 
      - [ ] ...

  - [ ] give the json string to rust, somehow
  - [ ] validate an actual city-json 
  

Stretch Goals 
-------------
  
  - [ ] add extra functionality 
    - [ ] duplicate vertices
    - [ ] duplicate names
    - [ ] proper hierarchies (check all parent-child relationships)


from Hugo
---------

- make the equivalent of [gltf validator](https://github.khronos.org/glTF-Validator/) : 
  - a simple page where you drag a cityjson file and you get the report of the schema validation

- ideally should be w/o npm or large package manager. 
  - [This seems promising](http://www.furidamu.org/blog/2020/07/10/rust-webassembly-in-the-browser/) (parceljs is what Stelios uses but I’ve never used it)

- The validation should be only in the browser, no upload of a file anywhere 
> [JF] drag & drop, or 'select file', check!_

- The latest schema of cityjson (minified) is there: https://3d.bk.tudelft.nl/schemas/cityjson/1.0.2/cityjson.min.schema.json
- So the idea is to use WASM/Rust, the best/fastest validator is https://docs.rs/jsonschema/0.6.1/jsonschema/

- The rust code for validation should be minimal here, I fiddled 2h a while ago to test it: (link removed)
- The validator of cjio has more custom functions to validate, but I will code those after (eg checking duplicate vertices, cannot be done with a schema…).


Comments to myself
------------------
- TODO : convert all functions to async & await syntax, instead of this callback adventure.



Links 
=====

- inspired by: [gltf validator](https://github.khronos.org/glTF-Validator/)       
- [rust + wasm book](https://rustwasm.github.io/docs/book/introduction.html)
- [serde (json IO)](https://docs.serde.rs/serde_json/)
- [rust triangulation](https://hugoledoux.github.io/startin_wasm/www/dist/)
