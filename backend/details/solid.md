# SOLID

Набор из 5-ти советов по написанию объекто-ориентированного кода.

1. **Принцип единой ответственности (Single responsibility principle)** - для каждого класса должно быть определено единственное назначение. Все ресурсы, необходимые для его осуществления, должны быть инкапсулированы в этом классе.
   <details>
   <summary>Пример</summary>

   Допустим у нас есть сущность заказ в интернет магазине. По требованиям его можно открыть или отменить. В данном случае нарушением принципа будет добавление метода для сохранения заказ в БД, т.к. эта функция скорее относится к инфраструктуре.

   </details>

   <details>
   <summary>Код</summary>

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
2. **Принцип открытости/закрытости (Open-closed principle) -** программные сущности должны быть открыты для расширения, но закрыты для модификации.
   <details>
   <summary>Пример</summary>

   У нас есть различные фигуры в виде классов, и нам нужно подсчитать массив площадей различных фигур. Нарушением принципа будет, если мы созданим специальный класс куда поместим логику подсчета площадей для каждой фигуры.

   </details>

   <details>
   <summary>Код</summary>

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
3. **Принцип подстановки Лисков (Liskov substitution principle) -** методы, которые используют базовый тип, должны иметь возможность использовать подтипы базового типа не зная об этом.
   <details>
   <summary>Пример</summary>

   *Классичейский пример состоит в наследовании класса прямоугольник, где есть методы установки двух сторон, классом квадрат, где должна быть установка только одной стороны. При использовании квадрата приведенного с прямоугольнику, нас может удивить работа методов установки сторон.*

   </details>

   <details>
   <summary>Код</summary>

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
4. **Принцип разделения интерфейса (interface segregation principle) -** программные сущности не должны зависеть от методов, которые они не используют.
   <details>
   <summary>Пример</summary>

   *Допустим мы проектируем интерфейс для механического транспорта, куда закладываем методы заправки и закрывания дверей, для машин он подходит идеально. Нарушением принципа будет реализация этого интерфейса классом мотоцикл, где нету дверей, прийдется ставить заглушку и при вызове этого метода проверять не мотоцыкл ли у нас.*

   </details>

   <details>
   <summary>Код</summary>

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
5. **Принцип инверсии зависимостей (dependency inversion principle) -** модули верхних уровней не должны зависеть от модулей нижних уровней. Оба типа модулей должны зависеть от абстракций.
   <details>
   <summary>Пример</summary>

   *Бизнес логика обращается к базе данных, принцип будет нарушен, если при смене БД нам потребуется что-либо исправять код бизнес логики. Так как это будет означать, что этот код завязан на реализацию конкретной БД.*

   </details>

   <details>
   <summary>Код</summary>

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
