# Ruby Methods in Classes

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
