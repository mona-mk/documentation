YAML (YAML Ain't Markup Language) is used widely for configurations nowadays in many tools. Of course it is human readable so you won’t have much problem reading and understanding the configuration. File format is either `.yml` or `.yaml`. YAML is a superset of JSON, any valid JSON is valid YAML file. In XML, data structures are defined using `<>` and in JSON using `{}`, but in YAML data structures are defined through line separation and indentation.

---
**YAML syntax**

* individual key-value pairs
```
app: myappname
version: 1.7
port: 9000
```
* you don't need quotes for strings, but you can use if you want. Both single and double quotes work. If you need to use some special characters like line carriage `\n`, then you have to enclose the string is quotes.
* comment `#`
* you can have objects by wrapping say a individual key-value pairs in a key
```
microservice:
    app: myappname
    version: 1.7
    port: 9000
```
* you can have a list
```
microservice:
    - app: myappname
      version: 1.7
      port: 9000
    - app: mysecondappname
      version: 0.1
      port: 10000
```
* you can also have list of simple values
```
microservice:
    - myappname
    - mysecondappname
    - mythirdappname
```
* if you have primitive list values (not list of objects), you can simply express them with:
```
version:
    - 0.1
    - 0.2
    - 0.3
version: [0.1, 0.2, 0.3]
```
* boolean values can be any of these: yes/no, on/off, true/false
* multiline strings & long single line strings. For instance, the whole content of a shell script can be a multiline string.
```
multilineString: "this is line break \n"
multilineString: |
    this is first line
    and this is second line
    with pipe, it keeps line break as line break.
singlelineString: >
    this is a very long string
    that for readability we have 
    it on multiple line but the
    greater than sign will make it single line.
```
* you can access environment variable with `$` sign
* you can use placeholders `{{ somevalue }}`, and the value gets replaced by a template generated (the same concept is used in ansible)
* you can have multiple components inside the same yaml and you can separate them with `---`. This is for instance very useful in Kubernetes where you want to put multiple components inside a single yaml.

---
**references**
[Yaml Tutorial | Learn YAML in 18 mins](https://youtu.be/1uFVr15xDGg)