# Private Methods

## Objectives

1. Define private methods.
2. Understand the context in which you will use such a method.
3. See how private methods are defined.

## Background: Instance and Class Methods

As we dive deeper into object orientation in Ruby, we've been working with different types of methods: instance and class methods. Remember that instance methods are called on instances of a class. Let's make the following class called `Cat`.

```ruby
class Cat
  attr_accessor :name

  def initialize(name)
    @name = name
  end

  def meow
    "Meow, my name is #{name}!"
  end
end

bub = Cat.new("Lil Bub")
bub.meow
#=> "Meow, my name is Lil Bub!"
```

`meow` is an instance method because we can call it on an instance of the `Cat` class.

And class methods we call on the entire class itself, not the instance. Like if we had a method that kept track of all of the new instances of `Cat`:

```ruby
class Cat
  attr_accessor :name

  CATS = []

  def initialize(name)
    @name = name
    CATS << self
  end

  def self.all
    CATS
  end

  def meow
    "Meow, my name is #{name}!"
  end
end
```

Here we're shoveling in every new instance of `Cat` `initialize`d into a constant that holds onto all cats. Then we have a class method `self.all` which we'll call on the class itself to return all of the cats.

```ruby
bub = Cat.new("Lil Bub")
maru = Cat.new("Maru")

Cat.all
#=> [bub, maru]
```

## Public vs. Private Methods

### Public Methods

We've already been writing public methods: `meow` and `self.all`. We can call them from outside the scope of the class declaration, like on the instance of the class or the class itself. Public methods are called by an explicit receiver: the instance of `bub` explicitly receives the method `meow`.

### Private Methods

Private methods cannot be called by an explicit receiver. What does that mean? It means we can only call private methods within the context of the defining class: the receiver of a private method is always `self`.

### Why Use Private Methods?

Private methods help us to obscure information and functionality from the user. For example, let's say we have an application on the web. Users need to sign in to our application. A user will input their username and password to do so. The action of taking in that password and using it log someone in would be a good candidate for a private method. We would never want someone to be able to call `user.password` and reveal our users' super-secret password.

There is another reason to designate a method as private. Doing so will signal to other developers that this method is *depended* on by other methods in your program. It signals that they should beware of refactoring such a method for fear of breaking other parts of the program that they may not realize rely on it.

### Building Private Methods

We've already written a private method in our `Cat` class: `initialize`.

```ruby
bub.initialize
#=>NoMethodError: private method `initialize' called for #<Cat:0x00000101d1ad10 @name="lil Bub">
```

Private methods, aside from initialize, are usually written with the word `private` above them. Let's make a private `attr_accessor` method called `age` (most cats don't like for humans to just know their ages). Remember that an `attr_accessor` is just a shorthand for two methods: a getter and a setter.

```ruby
class Cat
  attr_accessor :name

  CATS = []

  def initialize(name, age)
    @name = name
    @age = age
    CATS << self
  end

  def self.all
    CATS
  end

  def meow
    "Meow, my name is #{name}!"
  end

  private
  attr_accessor :age

end
```

If we try to call `.age` with an instance of cat, we get an error:

```ruby
bub.age
NoMethodError: private method `age' called for #<Cat:0x000001020bfce0 @name="Lil Bub", @age=2>
```

### Why Have A Private `attr_accessor`?

Previously we were using getters/setters so the outside world could access our instance variables. But now we are using them so that we can locally access them. Why is that valuable? A lot of software bugs come from the incorrect modification of state, aka variables. When we are debugging our program, because so many bugs come from modification of variables, if we can centralize all modification into one place we help with debugging. That's the beauty of enforcing getters/setters. Want to see all the places an instance variable is modified? With a private `attr_accessor` you can just override it and put a `binding.pry`.

Another nice feature of using setters/getters is future proofing. Maybe in the future you want to add validations whenever someone sets one of your instance variables? No problem! Just override the setter method. No need to find all the places that you modify that instance variable and change it.

Let's make a method in the `Cat` class that implicitly receives age, through the only way it can: `self`. A cat can have a birthday, where it ages 1 year when the birthday happens.

```ruby
class Cat
  attr_accessor :name

  CATS = []

  def initialize(name, age)
    @name = name
    @age = age
    CATS << self
  end

  def self.all
    CATS
  end

  def meow
    "Meow, my name is #{name}!"
  end

  def birthday! #why the ! at the end? Just a stylistic choice to indicate this method is an action
    self.age = age + 1
  end

  private
  attr_accessor :age

end
```

Then when we call `bub.birthday!`, Lil Bub's age becomes 3. Happy birthday, Bub!

![bub birthday](http://ihavecat.com/wp-content/uploads/2014/06/480ae292-8e53-41ff-9b5a-868ef5f75d42.jpg)
