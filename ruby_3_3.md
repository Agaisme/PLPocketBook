# Ruby 3.3 Developer Pocketbook

> Concise, idiomatic, and production-ready cheatsheet for Ruby developers.  
> Reading time: ~35 minutes.

---

## Table of Contents

1. [Overview](#overview)  
2. [Setup](#setup)  
3. [Fundamentals](#fundamentals)  
4. [Data Structures](#data-structures)  
5. [Functions & Paradigm](#functions--paradigm)  
6. [Error Handling & Debugging](#error-handling--debugging)  
7. [Code Organization](#code-organization)  
8. [Complete Example Program](#complete-example-program)  
9. [Best Practices](#best-practices)  
10. [Quick Reference](#quick-reference)  

---

## 1. Overview

- **Language**: Ruby 3.3  
- **Paradigm**: Pure OOP (everything is an object), with strong support for functional style.  
- **Philosophy**: *“Optimized for developer happiness”* — expressive, concise, readable.  
- **Main use cases**: Web development (Rails, Sinatra), scripting, automation, APIs, DevOps tooling.  
- **Learning curve**: Smooth; syntax is human-like, but mastery comes from idiomatic style and metaprogramming.  

---

## 2. Setup

### Install Ruby
```bash
# macOS/Linux
brew install ruby

# Ubuntu/Debian
sudo apt install ruby-full

# Windows
winget install RubyInstallerTeam.Ruby
```

### Run Code
```bash
ruby hello.rb
irb        # interactive REPL
pry        # better REPL (gem install pry)
```

### Package Manager
- **RubyGems**: built-in package manager  
```bash
gem install rails
gem list
gem uninstall pry
```

- **Bundler**: dependency manager for projects  
```bash
gem install bundler
bundle init
bundle add rspec
```

### Recommended Editors/IDEs
- VS Code (extensions: `ruby`, `ruby-rubocop`)  
- RubyMine (JetBrains)  
- Neovim with `solargraph` + `rubocop`  

---

## 3. Fundamentals

### Variables & Constants
```ruby
name = "Alice"   # local variable
$global = 42     # global variable (avoid)
@instance = []   # instance variable
@@class = {}     # class variable
PI = 3.14        # constant
```

### Data Types & Conversion
```ruby
"123".to_i    # => 123
123.to_s      # => "123"
3.5.to_i      # => 3
nil.to_s      # => ""
```

### Operators
```ruby
+, -, *, /, %, **    # arithmetic
==, !=, <, >, <=, >= # comparison
&&, ||, !            # boolean
.., ...              # ranges (inclusive/exclusive)
```

### Control Flow
```ruby
if x > 5
  puts "big"
elsif x > 0
  puts "small"
else
  puts "negative"
end

# Ternary
msg = x > 5 ? "big" : "small"

# Case
case day
when "Mon" then puts "Start"
when "Fri" then puts "Party!"
else puts "Other"
end

# Loops
3.times { puts "Hi" }
for i in 1..3 do puts i end
while x > 0 do x -= 1 end
```

### Console & File I/O
```ruby
print "Enter name: "
name = gets.chomp
puts "Hello, #{name}"

File.write("data.txt", "Hello")
puts File.read("data.txt")
```

---

## 4. Data Structures

### Arrays
```ruby
arr = [1, 2, 3]
arr << 4
arr.each { |n| puts n }
arr.map { |n| n * 2 }
arr.select { |n| n.odd? }
```

### Hashes (Dictionaries)
```ruby
h = {name: "Alice", age: 30}
h[:name]        # => "Alice"
h.keys          # => [:name, :age]
h.merge({age: 31})
```

### Sets
```ruby
require "set"
s = Set.new([1,2,3])
s.add(3)
s.include?(2)
```

### Strings
```ruby
str = "Hello"
str.upcase
str.gsub("l","x")
"Hi #{name}"
```

### Structs
```ruby
Person = Struct.new(:name, :age)
p = Person.new("Bob", 20)
p.name  # => "Bob"
```

### Symbols
Immutable, reusable identifiers: `:id`, `:name`  

---

## 5. Functions & Paradigm

### Functions / Methods
```ruby
def greet(name="World")
  "Hello, #{name}!"
end
puts greet("Ruby")
```

### Blocks, Procs, Lambdas
```ruby
[1,2,3].each { |n| puts n }   # block

square = ->(x){ x*x }         # lambda
puts square.call(5)

proc = Proc.new { |x| x*2 }
puts proc.call(3)
```

### Closures
```ruby
def multiplier(factor)
  ->(x){ x * factor }
end
double = multiplier(2)
puts double.call(5)   # => 10
```

### OOP Example
```ruby
class Animal
  attr_accessor :name

  def initialize(name)
    @name = name
  end

  def speak
    "#{@name} makes a sound."
  end
end

class Dog < Animal
  def speak
    "#{@name} barks."
  end
end

puts Dog.new("Rex").speak
```

---

## 6. Error Handling & Debugging

### Exceptions
```ruby
begin
  1/0
rescue ZeroDivisionError => e
  puts "Error: #{e.message}"
ensure
  puts "Always runs"
end
```

### Debugging
```ruby
require "pry"; binding.pry   # breakpoint
puts caller                  # stack trace
```

### Testing
```bash
gem install rspec
rspec --init
```

```ruby
# spec/example_spec.rb
RSpec.describe "Math" do
  it "adds numbers" do
    expect(1+2).to eq(3)
  end
end
```

---

## 7. Code Organization

### Modules & Namespaces
```ruby
module Utils
  def self.hello
    "hi"
  end
end
puts Utils.hello
```

### Gems & Dependencies
- Define in `Gemfile`:
```ruby
source "https://rubygems.org"
gem "rspec"
```

Run:
```bash
bundle install
```

### Project Layout
```
my_app/
├── Gemfile
├── lib/
│   └── my_app.rb
├── bin/
│   └── my_app
├── spec/
│   └── my_app_spec.rb
```

---

## 8. Complete Example Program

**Task Manager CLI** (CRUD tasks + priorities, file persistence, error handling).

### `lib/task_manager.rb`
```ruby
require "json"

class TaskManager
  FILE = "tasks.json"

  def initialize
    @tasks = load
  end

  def add(title, priority="normal")
    id = (@tasks.map{|t| t[:id]}.max || 0) + 1
    task = {id: id, title: title, priority: priority, done: false}
    @tasks << task
    save
    task
  end

  def list
    @tasks.each do |t|
      status = t[:done] ? "✓" : "✗"
      puts "[#{t[:id]}] (#{t[:priority]}) #{t[:title]} #{status}"
    end
  end

  def complete(id)
    task = @tasks.find{|t| t[:id]==id}
    raise "Task not found" unless task
    task[:done] = true
    save
  end

  def delete(id)
    @tasks.reject!{|t| t[:id]==id}
    save
  end

  private

  def load
    File.exist?(FILE) ? JSON.parse(File.read(FILE), symbolize_names:true) : []
  end

  def save
    File.write(FILE, JSON.pretty_generate(@tasks))
  end
end
```

### `bin/task`
```ruby
#!/usr/bin/env ruby
require_relative "../lib/task_manager"

tm = TaskManager.new

case ARGV[0]
when "add"      then tm.add(ARGV[1], ARGV[2])
when "list"     then tm.list
when "done"     then tm.complete(ARGV[1].to_i)
when "delete"   then tm.delete(ARGV[1].to_i)
else
  puts "Usage: task [add <title> <priority>|list|done <id>|delete <id>]"
end
```

Make executable:
```bash
chmod +x bin/task
```

### `spec/task_manager_spec.rb`
```ruby
require "task_manager"

RSpec.describe TaskManager do
  it "adds tasks" do
    tm = TaskManager.new
    t = tm.add("Test")
    expect(t[:title]).to eq("Test")
  end
end
```

Run:
```bash
bundle exec rspec
```

---

## 9. Best Practices

- Follow [Ruby Style Guide](https://rubystyle.guide/)  
- Use `rubocop` for linting/formatting  
- Keep methods short, classes focused  
- Prefer symbols over strings for keys  
- Use `Enumerable` methods (`map`, `select`, etc.) over loops  
- Avoid monkey-patching core classes  
- Handle exceptions gracefully  
- Test with RSpec / Minitest  
- Use Bundler for dependency management  
- Security: validate inputs, avoid `eval`, update gems regularly  

Popular Libraries/Frameworks:  
- **Rails** (web), **Sinatra** (micro web), **Hanami** (modular web)  
- **RSpec** (testing), **Sidekiq** (background jobs), **Sequel/ActiveRecord** (ORM)  

---

## 10. Quick Reference

### Syntax Table
| Concept        | Example |
|----------------|---------|
| String interp. | `"Hi #{name}"` |
| Symbol         | `:id` |
| Range          | `(1..5).to_a` |
| Hash           | `{a:1, b:2}` |
| Lambda         | `->(x){x+1}` |
| Block          | `[1,2].map{|n| n*2}` |

### Common Idioms
```ruby
# Safe navigation
user&.profile&.email

# Memoization
@value ||= expensive_call

# Guard clause
def foo(x)
  return unless x
  # ...
end
```

### Standard Library Highlights
- `json`, `set`, `time`, `net/http`, `open-uri`  
- `File`, `Dir`, `Enumerable`, `CSV`  

### CLI Reference
```bash
ruby file.rb
irb
gem install <name>
bundle install
rspec
```

---

## Sources
- [Ruby Official Docs](https://ruby-doc.org/)  
- [Ruby Style Guide](https://rubystyle.guide/)  
- [RSpec Docs](https://rspec.info/documentation/)  

---

✅ You are now ready to build idiomatic, production-grade Ruby 3.3 applications!

