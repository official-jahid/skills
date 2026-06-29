---
name: csharp-inheritance
description: C# inheritance patterns - base classes, virtual/override, abstract classes, interfaces, and polymorphism in .NET.
license: MIT
metadata:
  author: csharp-skills
  version: "1.0.0"
---

# C# Inheritance

C# object-oriented inheritance patterns and best practices.

## Basic Inheritance

```csharp
public class Animal
{
    protected string name;
    
    public virtual void MakeSound()
    {
        Console.WriteLine("Some sound");
    }
}

public class Dog : Animal
{
    public override void MakeSound()
    {
        Console.WriteLine($"{name} says Woof!");
    }
}
```

## Abstract Classes

```csharp
public abstract class Shape
{
    public abstract double Area { get; }
    
    public virtual void Draw()
    {
        Console.WriteLine("Drawing shape");
    }
}

public class Circle : Shape
{
    public double Radius { get; set; }
    
    public override double Area => Math.PI * Radius * Radius;
}
```

## Interfaces

```csharp
public interface IDrawable
{
    void Draw();
    void Move(int x, int y);
}

public interface IResizable
{
    void Resize(double factor);
}

public class Rectangle : Shape, IDrawable, IResizable
{
    public override double Area => Width * Height;
    public void Draw() { /* implementation */ }
    public void Resize(double factor) { /* implementation */ }
}
```

## Polymorphism

```csharp
// Virtual functions
Shape[] shapes = { new Circle(), new Rectangle() };
foreach (var shape in shapes)
{
    shape.Draw();  // Calls overridden implementation
}

// Interface polymorphism
List<IDrawable> drawables = new() { new Circle(), new Rectangle() };
foreach (var drawable in drawables)
{
    drawable.Draw();
}
```

## Best Practices

- Prefer composition over inheritance
- Use `abstract` for base classes that shouldn't be instantiated
- Use `virtual` for methods that can be overridden
- Use `override` to implement virtual/abstract methods
- Implement interfaces for contracts