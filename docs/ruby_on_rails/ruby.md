## Ruby Basics

Ruby is a dynamic, open-source programming language with a focus on simplicity and productivity. It has an elegant syntax that is natural to read and easy to write.

### Install Ruby

You can install Ruby using a version manager like RVM or rbenv.
Here’s how to install Ruby using RVM:

1. Install RVM:
   ```bash
   \curl -sSL https://get.rvm.io | bash -s stable
   ```
2. Load RVM:
   ```bash
   source ~/.rvm/scripts/rvm
   ```
3. Install a specific version of Ruby:
   ```bash
   rvm install 3.1.2
   ```
4. Set the default Ruby version:
   ```bash
   rvm use 3.1.2 --default
   ```
5. Verify the installation:
   ```bash
   ruby -v
   ```
6. List installed Ruby versions:
   ```bash
   rvm list
   ```
7. Switch Ruby versions:
   ```bash
   rvm use <version>
   ```
8. Remove/uninstall a Ruby version:
   ```bash
   rvm uninstall <version>
   ```
9. List all gemsets:
   ```bash
   rvm gemset list
   ```
10. Create a new gemset:
    ```bash
    rvm gemset create <gemset_name>
    ```
11. Delete all gems in the current gemset:
    ```bash
    rvm gemset empty <gemset_name>
    ```
12. Delete a gemset:
    ```bash
    rvm gemset delete <gemset_name>
    ```
13. List all current gems:
    ```bash
    gem list
    ```
14. Install/uninstall a gem:
    ```bash
    gem install <gem_name> [--version/-v <version>]
    gem uninstall <gem_name>
    ```

Here’s how to install Ruby using rbenv:

1. Install rbenv and ruby-build:
   ```bash
   # Using Homebrew on macOS
   brew install rbenv ruby-build
   ```
2. Initialize rbenv:
   ```bash
   rbenv init
   ```
3. Install a specific version of Ruby:
   ```bash
   rbenv install 3.1.2
   ```
4. Set the global Ruby version:
   ```bash
   rbenv global 3.1.2
   ```
5. Verify the installation:

   ```bash
   ruby -v
   ```

   **Cheat Sheet**: https://karloespiritu.github.io/cheatsheets/rbenv/

### [Naming Convention](https://namingconvention.org/ruby/)

| Type            | Convention              | Example        |
| --------------- | ----------------------- | -------------- |
| File            | `lower_with_under`      | `my_script.rb` |
| Module/Class    | `CapWords`/`PascalCase` | `MyClass`      |
| Function/Method | `lower_with_under`      | `my_method`    |
| Variables       | `lower_with_under`      | `my_variable`  |
| Constants       | `CAPS_WITH_UNDER`       | `MY_CONSTANT`  |

### Basic Syntax

Here are some basic Ruby syntax examples:

```ruby
# Variables
name = "World"
puts "Hello, #{name}!"
# Methods
def greet(name)
  "Hello, #{name}!"
end
puts greet("Ruby")
# Control Structures
if name == "World"
  puts "It's a beautiful day!"
else
  puts "Welcome, #{name}!"
end
# Loops
5.times do |i|
  puts "Iteration #{i}"
end
# Arrays
fruits = ["apple", "banana", "cherry"]
fruits.each do |fruit|
  puts fruit
end
# Hashes
person = {name: "Alice", age: 30}
person.each do |key, value|
  puts "#{key}: #{value}"
end
```

### Variables and Constants

- Variables: dynamically typed, no need to declare type
- Constants: start with an uppercase letter, should not be changed

### Types of Variables, and cycling scope

| Variable Type      | Example    | Scope                                             | Lifetime                           |
| ------------------ | ---------- | ------------------------------------------------- | ---------------------------------- |
| Global Variables   | `$my_var`  | Accessible from anywhere in the program           | End when the application stops     |
| Class Variables    | `@@my_var` | Accessible within the class and its subclasses    | End when the application stops     |
| Instance Variables | `@my_var`  | Accessible within the instance of the class       | End when the instance is destroyed |
| Local Variables    | `my_var`   | Accessible only within the scope they are defined | End when the scope ends            |
| Constant Variables | `MY_CONS`  | Accessible from anywhere in the program           | End when the application stops     |

### Data Types

| Data Type         | Description                        | Examples                                                               |
| ----------------- | ---------------------------------- | ---------------------------------------------------------------------- |
| `Integer`         | whole numbers                      | `42`, `-7`                                                             |
| `Float`           | decimal numbers                    | `3.14`, `-0.001`                                                       |
| `String`          | sequence of characters             | `"Hello, World!"`, `'Ruby'`                                            |
| `Boolean`         | `true` or `false`                  | `true`, `false`                                                        |
| `Array`           | collection of elements             | `[1, 2, 3]`, `["apple", "banana", "cherry"]`                           |
| `Hash`            | key-value pairs                    | `{name: "Alice", age: 30}`, `{"key1" => "value1", "key2" => "value2"}` |
| `Date` and `Time` | represent dates and times          | `Date.today`, `Time.now`                                               |
| `Symbol`          | lightweight, immutable identifier  | `:my_symbol`, `:name`                                                  |
| `NilClass`        | represents "nothing" or "no value" | `nil`                                                                  |

### Functions/Methods

- Define a method using `def` keyword
- Methods can take parameters and return values

```ruby
def add(a, b)
  return a + b
end
result = add(5, 10) # -> 15
```

### Classes and Objects

- Define a class using `class` keyword
- Create objects (instances) of the class using `new` method

```ruby
class Dog
  # Instance method
  def bark
    puts "Woof!"
  end
end

dog = Dog.new # dog is an instance of Dog class
dog.bark # -> "Woof!"
```

### Modules

- Define a module using `module` keyword, which is a collection of methods and constants
- Include modules in classes using `include` or `extend`
  - `include`: adds module methods as instance methods
  - `extend`: adds module methods as class methods
    **Note:** When a module method is defined as a module function (using `self.`), it can be called on the module itself but not on instances of classes that include or extend the module.

```ruby
module Barking
  # Instance method
  def bark
    puts "Woof!"
  end

  # Module function (class method)
  def self.info
    puts "This is the Barking module."
  end
end

class Dog
  include Barking
end

dog = Dog.new
dog.bark # -> "Woof!"
dog.info # -> NoMethodError: undefined method `info' for #<Dog:...>
Dog.bark # -> NoMethodError: undefined method `bark' for Dog:Class
Dog.info # -> NoMethodError: undefined method `info' for Dog:Class

class Cat
  extend Barking
end

cat = Cat.new
cat.bark # -> NoMethodError: undefined method `bark' for #<Cat:...>
cat.info # -> NoMethodError: undefined method `info' for #<Cat:...>
Cat.bark # -> "Woof!"
Cat.info # -> NoMethodError: undefined method `info' for Cat:Class
```

### Operators

- Arithmetic Operators: `+`, `-`, `*`, `/`, `%`, `**`

  ```ruby
  # Note "/"
  20 / 10 = 2
  7 / 3 = 2
  7.0 / 3 = 2.33333
  10 / 0 = # -> ZeroDivisionError: divided by 0

  # Float are represented using the IEEE 754 standard
  # IEEE (Institute of Electrical and Electronics Engineer)
  2.3 - 1 = 1.2999999999999998 # 1.3
  0.0 / 0.0 = NaN
  1.0 / 0 = Infinity
  -1.0 / 0 = -Infinity
  ```

- Comparison Operators: `==`, `!=`, `>`, `<`, `>=`, `<=`, `<=>`, `===`, `.eql?`, `equal?`

  ```ruby
  # a = 10, b = 20
  a == b # -> false // 1.0 == 1 -> true
  a != b # -> true
  a > b # -> false
  a >= b # -> false
  a < b # -> true
  a <= b # -> true

  # <=> Combined comparison operator
  # Return 0 if equal, 1 if left is greater, -1 if right is greater
  1 <=> 1 # -> 0
  2 <=> 1 # -> 1
  1 <=> 2 # -> -1

  # === -> true / false, use in case statement
  case 10
  when String
    p 'String here'
  when (1..10)
    p 'Range here'
  end
  # String === 10 # -> false
  # (1..10) === 10 # -> true -> 'Range here'

  # Range
  (1..10) === 10 # -> true
  (1...10) === 10 # -> false
  # NOTE
  10 === (1..10) # -> false, only Class/Module on left side

  # Class
  Integer === 10 # -> true
  Integer === '10' # -> false
  # NOTE
  10 === Integer # -> false

  # .eql? -> true if same type and equal values.
  1.eql?(1.0) # -> false

  # equal? -> true if two objects are the same object in memory.
  a = { test: 1 }
  b = { test: 1 }
  a.equal?(b) # -> false
  c = a
  a.equal?(c) # -> true
  ```

- Assignment Operators: `=`, `+=`, `-=`, `*=`, `/=`, `%=`, `**=`

  Parallel Assignment:

  ```ruby
  a, b = 10, 20
  a += 5 # a = a + 5
  b *= 2 # b = b * 2
  ```

- Logical Operators: `and`, `or`, `&&`, `||`, `!`, `not`

  ```ruby
  a = true
  b = false
  a and b # -> false
  a or b # -> true
  a && b # -> false
  a || b # -> true
  !a # -> false
  not b # -> true
  ```

- Ternary Operator: `condition ? true_value : false_value`

  ```ruby
  age = 18
  status = age >= 18 ? "Adult" : "Minor" # -> "Adult"
  ```

- Dot `.` and Double Colon `::` Operators:

  - Dot `.`: used to call methods on objects or access attributes.

    ```ruby
    str = "hello"
    str.upcase # -> "HELLO"
    ```

  - Double Colon `::`: used to access constants, class methods, or modules.

    ```ruby
    module MathModule
      PI = 3.14
      def self.square(x)
        x * x
      end
    end

    MathModule::PI # -> 3.14
    MathModule.square(4) # -> 16
    ```

- Operators Precedence:

  | Operator                                          | Description                                                                        |
  | ------------------------------------------------- | ---------------------------------------------------------------------------------- |
  | `::`                                              | Constant resolution operator                                                       |
  | `[ ] [ ]=`                                        | Element reference, element set                                                     |
  | `**`                                              | Exponentiation (raise to the power)                                                |
  | `! ~ + -`                                         | Not, complement, unary plus and minus (method names for the last two are + and -@) |
  | `* / %`                                           | Multiply, divide, and modulo                                                       |
  | `+ -`                                             | Addition and subtraction                                                           |
  | `>> <<`                                           | Right and left bitwise shift                                                       |
  | `&`                                               | Bitwise 'AND'                                                                      |
  | `^ \|`                                            | Bitwise exclusive 'OR' and regular 'OR'                                            |
  | `<= < > >=`                                       | Comparison operators                                                               |
  | `<=> == === != =~ !~`                             | Equality and pattern match operators (!= and !~ may not be defined as methods)     |
  | `&&`                                              | Logical 'AND'                                                                      |
  | `\|\|`                                            | Logical 'OR'                                                                       |
  | `.. ...`                                          | Range (inclusive and exclusive)                                                    |
  | `? :`                                             | Ternary if-then-else                                                               |
  | `= %= { /= -= += \|= &= >>= <<= *= &&= \|\|= **=` | Assignment                                                                         |
  | `defined?`                                        | Check if specified symbol defined                                                  |
  | `not`                                             | Logical negation                                                                   |
  | `or and`                                          | Logical composition                                                                |

### Control Structures

- Conditional Statements: `if`, `elsif`, `else`, `unless`, `case`
- Loops: `while`, `until`, `for`, `each`, `times`, `loop`
- break, next, redo, retry Statements [Ruby loops](https://www.tutorialspoint.com/ruby/ruby_loops.htm)

### Common Methods

- Numeric methods: `to_i`, `to_f`, `round`, `ceil`, `floor`, `abs`, `even?`, `odd?`, `times`
- String methods: `upcase`, `downcase`, `length`, `split`, `gsub`
- Array methods: `push`, `pop`, `shift`, `unshift`, `each`, `map`, `select`, `reject`, `sort`, `include?`, `compact`, `flatten`, `uniq`, `empty?`
- Hash methods: `keys`, `values`, `each`, `merge`, `delete`, `fetch`, `has_key? (key?)`, `has_value? (value?)`

### Exceptions

- Handle exceptions using `begin`, `rescue`, and `ensure` blocks
- Raise exceptions using `raise` keyword
- Retry a block of code using `retry` keyword

[Ruby - Exceptions](https://www.tutorialspoint.com/ruby/ruby_exceptions.htm)

```ruby
begin
  # Code that may raise an exception
  result = 10 / 0
  # If you want to raise a custom exception, use "raise"
  raise "Custom error message"
rescue ZeroDivisionError
  puts "Division by zero error occurred"
  # If you want to redo the begin block, use "retry"
  retry
rescue StandardError => e
  puts "An error occurred: #{e.message}"
else
  puts "Otherwise block executed if no exception"
ensure
  puts "This will always execute"
end
```

### Documents

- [Understanding The Difference puts, print, & p](https://www.rubyguides.com/2018/10/puts-vs-print/)
