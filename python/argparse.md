# ArgParse

Command line argument with ArgParse. ArgParse is part of standard python library.

---
**positional and optional arguments**

* positional argument means the program should know what to do with the value, solely based on where it appears on the command line.
* keyword argument means position doesn't matter, and you need to specify the argument value via calling its name `--name value`
* optional argument means you can omit it `required=False`
* The positional arguments are always required arguments.
* If you start with the positional argument in command line, you have to provide all of them before the keyword. The order of positional arguments are the order they have been added to parser object.
* `store_true` and `store_false` - These are special cases of 'store_const' used for storing the values True and False respectively. In addition, they create default values of False and True respectively. The store_true option automatically creates a default value of False. Likewise, store_false will default to True when the command-line argument is not present.

```
# examply.py
import argparse
parser = argparse.ArgumentParser(
    prog="ProgramName",
    description="What the program does",
    epilog="Text at the bottom of help",
)

parser.add_argument('filename')           # positional argument
parser.add_argument('-c', '--count')      # option that takes a value
parser.add_argument('-v', '--verbose',
                    action='store_true')  # on/off flag, ie. an option without any argument, you just call it with `--verbose` with no value
args = parser.parse_args()
print(args)

# command-line
python3 example.py foo

# command-line: to get help on the program
python3 example.py --help
```
* NB. `argparse` treats the options we give it as strings, until told otherwise
```
parser.add_argument("square", type=int)
```

---

**nargs**

multiple input arguments: If you want the user to give a list of items, whether known number or any number of items user wishes.
```
parser.add_argument('--values', type=int, nargs=3)
parser.add_argument('--values', type=int, nargs=‘+’) # for any number
```

---
**Mutually exclusive arguments**

`add_mutually_exclusive_group`: in case one argument conflicts with another, and user needs to use only one.

```
group = parser.add_mutually_exclusive_group()
group.add_argument('--add', action='store_true')
group.add_argument('--subtract', action='store_true')
parser.add_argument('x', type=int)  # other positional argument as usual
```
---
**subparsers**

A subparser is a completely different set of arguments (independent of each other). Examples are how we use git: `git commit [OPTIONS]` `git add [OPTIONs]` etc.

```
parser = argparse.ArgumentParser()
subparser = parser.add_subparsers(dest='command')
login = subparser.add_parser('login')
register = subparser.add_parser('register')
login.add_argument('--username', type=str, required=True)
login.add_argument('--password', type=str, required=True)
register.add_argument('--username', type=str, required=True)
register.add_argument('--email', type=str, required=True)

args = parser.parse_args()
if args.command == 'login':
  print(args.username)
elif args.command == 'register':
  print(args.email, args.username)

# command-line
python example.py login --username mona --password pass
```

---