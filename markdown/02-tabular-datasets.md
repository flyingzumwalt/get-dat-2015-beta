# Write some tabular data

Let's put some data into dat.

There are three formats that dat can import: <a href="http://ndjson.org/" target="_blank">Newline-delimited JSON</a>, CSV and <a href="https://developers.google.com/protocol-buffers/" target="_blank">Protocol Buffers</a>. In this tutorial we will work with JSON and CSV.

## Newline-delimited JSON

Newline-delimited JSON is just a little bit different than regular JSON.

Standard JSON looks like this:

```JSON
[
  {"name": "alice"},
  {"name": "bob"}
]
```

The two values are wrapped in array brackets (`[...]`) and separated by a comma (`,`). Newline-delimited JSON lacks those two features, instead representing the above data like this:

```
{"name": "alice"}
{"name": "bob"}
```

As you can see, it just means JSON objects separated by only a single line break. It's a very small change, but it's necessary to support parsing arbitrary subsets of a larger JSON file.

Note that the following is _not_ valid newline-delimited JSON:

```JSON
{
  "name": "alice"
}
{
  "name": "bob"
}
```

That's because it uses line breaks to format the JSON by pretty-printing it. In newline-delimited JSON the only time you will see a line break (aka a “newline”) is to separate two objects.

## Save some JSON to a file

Create a new text file named `people.json`. The easiest way is to do that is to run `touch people.json`. This will create a new, empty file. `people.json` will show up in the file browser below, and you can double-click it to edit it.

Copy the following newline-delimited JSON data, paste it into `people.json`, and save the file:

```
{"name": "alice", "age": "35"}
{"name":"bob", "age": "34"}
```

## Import the JSON into dat

Import the data into dat, under a dataset called “people”.

```
dat import people.json -d people
```

The data about Bob and Alice is now stored within dat! But don't delete that JSON file quite yet.


## Export the data

Get the data back out of dat by running:

```
dat export -d people
```

The output will look the same as what you put into dat.

## Updating the data

So, let's say Bob had a birthday. Happy birthday, Bob! Go into Bob's row and update his age to 35. After you do that and save your changes, run:

```
$ dat import people.json -d people
Wrote [+2, -0].
Done importing data.
Version: c29d843864f8271d6f1238e150074043bef01bdfa9ab3fad3c939806a87ad2d1
```

We imported the updated data into dat. So, we should see Bob is now 35, right?

```
$ dat export -d people
```

Oh, no! We've duplicated our data:

```
{"name":"alice","age":"35"}
{"name":"bob","age":"34"}
{"name":"alice","age":"35"}
{"name":"bob","age":"35"}
```

We imported the same data into our dat again, and because our data lacks a **unique key**, dat just assumes the data is new data and appends it to the end.

## Importing JSON with keys

Let's wipe the slate and start over again. Run `dat destroy` to get rid of the data we imported above, and then run `dat init` to initialize a fresh dat store, again accepting the defaults for the three prompts.

Now import the data again, but this time let's specify that the `name` field should be used as the key (we know this isn't probably a unique key, but let's entertain it for now).

```
$ dat import people.json -d people -k name
```

Now, if we run the import a second time with a key, dat will attempt to update the rows.

```
$ dat import people.json --key=name -d people
```

Because we're using a unique key, running `dat export -d people` shows no duplicates.

If you update Alice's age to `36` and run `dat import` again, dat will update 1 row—Alice's.

*Note: If no key is found in the provided data, dat will generate it automatically. That automatic key can be found in the metadata by using `dat export -d people --full`. For some use cases, importing data without a key is perfectly fine behavior, but in other cases, you will want to make sure your incoming data has one. It all depends on the nature of the data that you are importing.*
