[Русский](solid.md) | English

# SOLID

Five principles for writing object-oriented code.

1. **Single Responsibility Principle** — each class should have one purpose and encapsulate the resources needed to fulfill it.
   <details>
   <summary>Example</summary>

   Consider an Order entity in an online store. Requirements allow opening or canceling an order. Adding a method that saves the order to a database violates the principle because persistence belongs to infrastructure.

   </details>

   <details>
   <summary>Code</summary>

   ```csharp
   public class Order
   {
       public int Id { get; set; }
       // This method occured from domain
       public void Cancel() => Console.WriteLine("Order canceled");
       // This method occured from domain
       public void Open() => Console.WriteLine("Order opened");
       // This method occured from infrastructure
       public void SaveToDb() => Console.WriteLine("Order saved");
   }
   ```

   </details>
2. **Open–Closed Principle** — software entities should be open for extension but closed for modification.
   <details>
   <summary>Example</summary>

   Different classes represent shapes, and we need their total area. A dedicated calculator that contains the area formula for every shape violates the principle because adding a shape requires changing the calculator.

   </details>

   <details>
   <summary>Code</summary>

   ```csharp
   public record Rectangle(double Height, double Width);
   public record Circle(double Radius);
   // If we add a new figure we'll must modify this class.
   public class AreaCalculator
   {
       public double TotalArea(object[] arrObjects)
       {
           double area = 0;
           foreach (var obj in arrObjects)
           {
               if (obj is Rectangle r)
                   area += r.Height * r.Width;
               else if (obj is Circle c)
                   area += c.Radius * c.Radius * Math.PI;
           }
           return area;
       }
   }
   ```

   </details>
3. **Liskov Substitution Principle** — methods using a base type should be able to use its subtypes without knowing the difference.
   <details>
   <summary>Example</summary>

   *The classic example is a Square subclass of Rectangle. A rectangle exposes setters for two sides, while a square must keep both sides equal. Using a square through a rectangle reference can therefore produce surprising setter behavior.*

   </details>

   <details>
   <summary>Code</summary>

   ```csharp
   Rectangle s = new Square();
   s.setHeight(2);
   s.setWidth(3);
   Console.WriteLine($"{s.height} {s.width}"); // Set different, but sides equal 3 and 3.

   public class Rectangle
   {
       public double height;
       public double width;
       public virtual void setHeight(double h) { height = h; }
       public virtual void setWidth(double w) { width = w; }
   }

   public class Square : Rectangle
   {
       public override void setHeight(double h)
       {
           base.setHeight(h);
           base.setWidth(h);
       }

       public override void setWidth(double w)
       {
           base.setHeight(w);
           base.setWidth(w);
       }
   }
   ```

   </details>
4. **Interface Segregation Principle** — software entities should not depend on methods they do not use.
   <details>
   <summary>Example</summary>

   *Suppose a motor-vehicle interface has methods for refueling and closing doors. It suits cars, but implementing it for a motorcycle requires an unsupported stub because motorcycles have no doors. Callers must then check the concrete vehicle type.*

   </details>

   <details>
   <summary>Code</summary>

   ```csharp
   IVehicle m = new Motocycle();
   m.CloseDoors(); // Unexpected error

   public interface IVehicle
   {
       void FillGas(int l);
       void CloseDoors();
   }
   // It works good!
   public class Car : IVehicle
   {
       public void CloseDoors() => Console.WriteLine("Doors are closing");
       public void FillGas(int l) => Console.WriteLine($"Filled {l} liters");
   }
   // It works bad!
   public class Motocycle : IVehicle
   {
       public void CloseDoors() => throw new Exception("Not supported funtion");
       public void FillGas(int l) => Console.WriteLine($"Filled {l} liters");
   }
   ```

   </details>
5. **Dependency Inversion Principle** — high-level modules should not depend on low-level modules; both should depend on abstractions.
   <details>
   <summary>Example</summary>

   *Business logic accesses a database. If replacing the database requires modifying the business logic, that logic depends on a specific database implementation and violates the principle.*

   </details>

   <details>
   <summary>Code</summary>

   ```csharp
   // Class in someone else's library
   public class PostgreSqlDBConnection
   {
       public void ExecuteSql(string command) => Console.WriteLine("Executing SQL");
   }

   public class UserService
   {
       private readonly PostgreSqlDBConnection _connection;
       public UserService(PostgreSqlDBConnection connection) => _connection = connection;

       public void AddUser(string email)
       {
           // Validating, verification and etc.

           // If we change our PostgreSQL to MongoDB we'll need to change all similar lines.
           _connection.ExecuteSql("INSERT INTO ...");

           // Mapping, pushing events and etc.
       }
   }
   ```

   </details>
