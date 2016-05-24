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

Here we're shoveling in every new instance of `Cat` `initialized` into a constant that holds onto all cats. Then we have a class method `self.all` which we'll call on the class itself to return all of the cats.

```ruby
bub = Cat.new("Lil Bub")
maru = Cat.new("Maru")

Cat.all
#=> [#<Cat:0x007ff2ec20c330 @name="Lil Bub">, #<Cat:0x007ff2ec20c560 @name="Maru">]
```

## Public vs. Private Methods

### Public Methods

We've already been writing public methods: `meow` and `self.all`. We can call them from outside the scope of the class declaration, like on the instance of the class or the class itself. Public methods are called by an explicit receiver: the instance of `bub` explicitly receives the method `meow`.

### Private Methods

Private methods cannot be called by an explicit receiver. What does that mean? It means we can only call private methods within the context of the defining class: the receiver of a private method is always `self`.

### Why Use Private Methods?

Private methods are a way of encapsulating functionality within a class. For example, a bartender can make a drink at a customer's request. Part of the process of making a drink includes choosing liquors, mixers, garnish, and stirring everything together. As a customer, you're permitted to ask the bartender for a drink but you can't instruct him or her on each step. The smaller steps that make up the bartender's job can be considered private methods.

Private methods also signal to other developers that this method is *depended* on by other methods in your program. It signals that they should beware of removing such a method for fear of breaking other parts of the program that they may not realize rely on it.

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

  def age
    "I'm #{age} years old!"
  end

end
```

If we try to call `.age` with an instance of cat, we get an error:

```ruby
bub.age
NoMethodError: private method `age' called for #<Cat:0x000001020bfce0 @name="Lil Bub", @age=2>
```

### Why Have A Private `attr_accessor`?

Previously we were using getters/setters so the outside world could access our instance variables. But now we are using them so that we can locally access them. Why is that valuable? Bugs can result from the incorrect modification and assignment of variables. If we can limit outside access to variables, we can simplify our debugging efforts. That's the beauty of enforcing getters/setters. Let's say you want to see all the places an instance variable is modified. With a private `attr_accessor` you can just override it and put a `binding.pry`.

So instead of having this

```ruby
private

attr_accessor :age
```

You can override it with the following and put in a `binding.pry`

```ruby
private

def age=(value)
  @age = value
  binding.pry
end
```

Another nice feature of using setters/getters is future proofing. Maybe in the future you want to add validations whenever someone sets one of your instance variables? No problem! Just override the setter method. No need to find all the places that you modify that instance variable and change it.

Let's make a method in the `Cat` class that receives age, through the only way it can: `self`. A cat can have a birthday, where it ages 1 year when the birthday happens.

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

  def birthday! #why the ! at the end? The ! or "Bang" signals destructive changes, or mutations, to the underlying object. In this instance, the birthday method is changing the cat's age.
    self.age = age + 1
  end

  private
  attr_accessor :age

end
```

Then when we call `bub.birthday!`, Lil Bub's age becomes 3. Happy birthday, Bub!

![bub birthday](http://readme-pics.s3.amazonaws.com/LilBub_bday.jpg)

<p data-visibility='hidden'>View <a href='https://learn.co/lessons/ruby-public-private-methods-readme' title='Private Methods'>Private Methods</a> on Learn.co and start learning to code for free.</p>
