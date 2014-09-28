# Pry Commands
## User Input
- Clear the Buffer: `!`
- Suppress evaluation output: Put `;` at the end of the line
- Show Contents of Input buffer: `show-input`
- Correct an input line n: `amend-line n 'replacement'`
- Edit in external editor: `edit`
- Show history: `hist --tail N`
- Replay history: `hist --replay N`
- Show history without line number: `hist -n`
- Play files and methods as input: `'load play.rb'` `play -l N`
- \_in\_ and \_out\_ cache: Input and Output Expressions are stored in array like structures

## Navigating around Pry
### Changing the Scope
- Show current scope: `self`
- Change scope: `cd 'object'`
- Change to top level scope: `cd /`
- Change to previous scope: `cd ..`
- Switch between two scopes: `cd -`

### Learning about your context
- `ls [-m|-M|-p|-pM] [-q|-v] [-c|-i] [Object]`
- `ls [-g] [-l]`
- Wrapper for number of Ruby's intorspection mechanismns for methods, instance\_variables, constants, local\_variables, class\_variables
- Options:
  - `-m, --methods  `:      Show public methods defined on the Object (default)
  - `-M, --module   `:      Show methods defined in a Module or Class
  - `-p, --ppp      `:      Show public, protected (in yellow) and private (in green) methods
  - `-q, --quiet    `:      Show only methods defined on object.singleton_class and object.class
  - `-v, --verbose  `:      Show methods and constants on all super-classes (ignores Pry.config.ls.ceiling)
  - `-g, --globals  `:      Show global variables, including those builtin to Ruby (in cyan)
  - `-l, --locals   `:      Show locals, including those provided by Pry (in red)
  - `-c, --constants`:      Show constants, highlighting classes (in blue), and exceptions (in purple)
  - `-i, --ivars    `:      Show instance variables (in blue) and class variables (in bright blue)
  - `-G, --grep     `:      Filter output by regular expression
  - `-h, --help     `:      Show help

### Rapidly exploring
- `find-method`
- `find-method -c command scope`

### Nested Context
- Show the nesting of the scopes: `nesting`
- Switch to scope N: `switch-to N`
- Exit Context with a value: `exit VALUE`
- Exit all nested contexts and return to calling process: `exit-all`, `!!@`
- Exit the programm completely: `!!!`

## Shell Integration
- In Pry an input line, which starts with `.` will be forwarded to the shell
- Ruby-Code can be interpolated with #{}
- `shell-mode` incorporate the present working directory into the Pry Prompt and will bring limited file name completion
- `shell-mode` a second time will end this
- `cat`-command
  - automatic syntax highlighting for `.rb, .py, .cpp, .java, .json, .php, .xml`
  - `-l`: line-numbers
  - `-t [FILE-TYPE]`: syntax highlighing as specified type
    - specified types: `ruby, cpp, python, c, html, javascript, diff`
  - `-s [START_LINE]`: line, in wich cat begins
  - `-e [END_LINE]`: line, in wich cat ends
  - `-i`: option displays one or more entries from Pry's input expression historys
- `_file_ and _dir_`: commands as `show-method, show-doc, show-source, stat, cat` update these variables
- `gem-cd`: change the working directory to the dir af the given gem

## Source Browsing
- supports ri method syntax
- `show-source MyClass`
	- `-s`: super method
	- `-ss`: super super method
	- `-l`: line numbers
	- `-b`: line numbers start with 1
	- `-a`: all sources and monkey patches!!!
- `show-method MyClass#function`

## Documentation Browsing
- `show-doc` or `?`
	- ri and yard documentation format
	- `-s and -ss`: super function and supersuper function documention
	- `-l`: line numbers
	- `-b`: line numbers start with 1
	- `-a`: include monkey patches
- `stat`: show basic information
- `ri`: access ri documentation
- with `pry-doc` access c-documentation

## Special Locals
- `_`: last result
- `_ex_`: last exception
- `_in_`, `_out_`: input and output cache
- `_file_`, `_dir_`: last file and directory
- `_pry_`: local pry instance for **customization**

## Managing History
- standard: `~/.pry_history`
- set history file: `Pry.config.history.file = "~/.irb_history"` in `.pryrc` for example
- load history at beginning of pry session: `Pry.config.history.should_load = true`
- save history at end of pry session: `Pry.config.history.should_save = true`
- `hist` command
  - options:
    - `--grep`
    - `--tail N`: last N lines
    - `--head N`: first N lines
    - `--no-numbers`
    - `--replay [A..B]`
    - `--exclude`: exclude pry-commands
    - `--save [A..B] FILE`: save history to file
    - `--show`: displays line or range
    - `--clear`: clears current history

## .pryrc file
- `.pryrc` file will be checked in home directory and per-project in current directory
- `-f`: command line suppression

## Runtime invocation
- Invoking on a binding
	- `require pry;`
	- `binding.pry`
- Invoking on a object
	- `#OBJECT.pry`
- `whereami NUMBER`: how much context is shown before and after the invocation line
- `pry-backtrace`: show packtrace for the position where Pry was started

## Live help system
- viewing pry method's documentation: `? Pry#function`
- learning Pry's config options: `ls -m Pry.config`
- `help` command

## Editor Integration
- `Pry.config.editor = "subl -w"`
- `Pry.config.editor = proc { |file, line| "subl -w #{file}:#{line}" }`
- **WICHTIG 'subl'-Link muss im Standart Path liegen...**
	- Zum Beispiel `sudo ln -s /Applications/Sublime\ Text.app/Contents/SharedSupport/bin/subl /usr/bin/subl`

## Customizing Pry
- `Pry.config` settings apply to all instances
- following settings can be overwritten
  - `Pry.config.input`
    - for example: `Pry.start binding, :input => StringIO.new("ls\nexit")`
  - `Pry.config.output`
  - `Pry.config.print`
  - `Pry.config.exception_handley`
  - `Pry.config.prompt`
  - `Pry.config.prompt_name`

## Exception Handling
- `_ex_`: local variable for last exception; persists till a new exception is raised
- `_ex_.backtrace`; `wtf?`: few lines backtrace for the most recent exception
- Customizing the Exception Handler:
```ruby
Pry.config.exception_handler = proc do |output, exception, _pry_|
  output.puts "#{exception}"
  output.puts "#{exception.backtrace.first(10)}"
end
```
```ruby
Pry.config.exception_handler = proc do |output, exception, _pry_|
  _pry_.run_command 'cat --ex'
end
```
```
Pry.config.exception_whitelist = [SignalError, SystemExit, NoMemoryError]

```
- for more see `pry-rescue` **Plugin**

## Command System

## Creating Custom Commands

## Plugins
- gem install pry pry-doc pry-byebug pry-coolline

## Using Pry with Rails
- Rails3,4+ only
  - add folowing to gemfile
    - `gem 'pry-rails', :group => :development`
    - `bundle install`
- with pry in gemfile
  - Rails 2
    - `./script/console/ --irb=pry`
  - Rails3
```ruby
MyApplication::Application.configure do
  # Use Pry instead of IRB
  silence_warnings do
    begin
      require 'pry'
      IRB = Pry
    rescue LoadError
    end
  end
end
```

## Using Pry in Browser with Rails
- https://github.com/mrbrdo/rack-webconsole
- **need jquery!!!**
- Gemfile: `gem 'rack-webconsole-pry', :require => 'rack-webconsole'`

## Remote Sessions
- https://github.com/pry/pry/wiki/Remote-sessions