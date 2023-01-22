# json (JavaScript Object Notation)

Data representation format just like XML or YAML. JSON is very lightweight, therefore, when you send it across the internet via APIs, it takes little space, so you can send it back and forth very fast.

Supported data types in JSON:
* strings “Hello World”
* Numbers 10 1.5 -30
* Booleans true false
* null null
* arrays [1,2,3] [“Hello”, “World”]
* Objects {“key”: “value”}

You can use any of the data types above in a json, i.e. a json file can only contain a single say string, and it is still a valid JSON. However, in most cases, you want to have an array or an object where you nest other datatypes inside it.
```
[
	{
	}
]
```
or
```
{
	[
	]
}
```
Strings in JSON are specified using double quotes " ". If the strings are enclosed using single quotes, then the JSON is an invalid JSON. JSON syntax is not Python syntax. JSON requires double quotes for its strings. Anything in JSON is a valid javascript, so you can copy paste a json content as a variable in Javascript. But most of the time when you are dealing with json, you are going to get it back as string, therefore, you have to parse that json into a javascript object.
```
s = `[
	{
	 “key”: “value”
	}
]`
console.log(JSON.parse(s))
```
