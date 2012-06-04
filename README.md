_json-lenses_ is a library to query and update [JSON] data structures.

It has the following features

 * A type-safe syntax to address elements in a json data structure
 * Retrieve addressed elements
 * Easily update addressed elements of the immutable spray-json representation
 * Addressed elements can be of several cardinalities: scalar values, optional
   values, and sequences of values
 * Experimental support for [json-path] syntax

## Usage

If you use SBT you can include _json-lenses_ in your project with

    "net.virtual-void" %%  "json-lenses" % "0.5.0"

## Example

Given this example json document:
```scala
val json = """
{ "store": {
    "book": [
      { "category": "reference",
        "author": "Nigel Rees",
        "title": "Sayings of the Century",
        "price": 8.95
      },
      { "category": "fiction",
        "author": "Evelyn Waugh",
        "title": "Sword of Honour",
        "price": 12.99,
        "isbn": "0-553-21311-3"
      }
    ],
    "bicycle": {
      "color": "red",
      "price": 19.95
    }
  }
}""".asJson
```

All authors in this document are addressed by
```scala
val allAuthors = 'store / 'book / * / 'author
```

This is called a lens. You can use a lens to retrieve or update the addressed
values.

```scala
val authorNames = json.extract[String](allAuthors)
```

To update values use one of the defined operations. To overwrite a value use `set`, to change a value
based on the previous value use `modify`.

```scala
// overwrite all authors' names to "ABC"
val newJson1 = json.update(allAuthors ! set[String]("ABC"))

// prepend authors' names with "Ms or Mr "
val newJson2 = json.update(allAuthors ! modify[String]("Ms or Mr " + _))
```

## The concept

The concept of lenses is a powerful concept not only applicable to json objects. A lens is 
an updatable, composable view into a data structure. There are simple lenses which provide
just the functionality to "go one level deeper" in the data structure (for json: accessing fields of a
json object or elements of a json array) and there are lenses which compose other lenses
to allow a deeper view into the data structure.

See [this answer][so-lenses] on stack overflow and [this presentation][lenses] for more info on the
general topic of lenses.

The json lenses in this project are specialized lenses to extract and update json data. A lens has
this form (almost, in reality it's a bit more complicated):

```scala
trait Lens {
  def retr: JsValue => JsValue
  def def updated(f: JsValue => JsValue)(parent: JsValue): JsValue
}
```

It has a method `retr` which when called with a json value will return the value this lens points to.
The other method `updated` takes a parent json value and returns an updated copy of it with function `f` 
applied to the child value this lens points to.

## API Documentation

You can find the documentation for the _json-lenses_ API here:
<http://jrudolph.github.com/json-lenses/api>

## Future

It is planned that the json lenses will eventually move out of their incubator and
will then be integrated into [spray-json] proper.

## License

_spray-json_ is licensed under [APL 2.0].

[APL 2.0]: http://www.apache.org/licenses/LICENSE-2.0
[JSON]: http://json.org
[spray-json]: https://github.com/spray/spray-json
[json-path]: http://goessner.net/articles/JsonPath/
[so-lenses]: http://stackoverflow.com/a/5597750/7647
[lenses]: http://www.cis.upenn.edu/~bcpierce/papers/lenses-etapsslides.pdf