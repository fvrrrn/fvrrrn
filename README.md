# Boris Chernyshov CV
Sergey Bobrovsky's entry-level programming course: [first assignment](https://skillsmart.ru/self/c723s08a2c.html)

## Programming books read
  - [You Don't Know JS](https://www.oreilly.com/library/view/you-dont-know/9781491924471/)
  - [TypeScript 5 Design Patterns and Best Practices](https://www.oreilly.com/library/view/typescript-5-design/9781835883228/)
  - [Effective Haskell](https://www.oreilly.com/library/view/effective-haskell/9798888650400/)
  - [CLR via C#](https://www.oreilly.com/library/view/clr-via-c/9780735668737/)

## Object-Oriented Programming code examples

<details>
<summary>Fraction</summary>

### Encapsulation
```csharp
public abstract class Fraction : IComparable<Fraction> {
  private double numerator;
  private double denominator;
  public Fraction(double numerator, double denominator) {
    if (denominator == 0)
      throw new ArgumentException("Denominator cannot be zero.");
    this.numerator = numerator;
    this.denominator = denominator;
  }
  public double Numerator { get { return numerator; } }
  public double Denominator { get { return denominator; } }
  public abstract void Simplify();
  public abstract double GetValue();
  public int CompareTo(Fraction other) {
    if (other == null)
      return 1;
    double thisValue = this.GetValue();
    double otherValue = other.GetValue();
    if (thisValue < otherValue)
      return -1;
    if (thisValue > otherValue)
      return 1;
    return 0;
  }
}
```
### Inheritance
```csharp
public class FixedFraction : Fraction {
  public FixedFraction(double numerator, double denominator) : base(numerator, denominator) { }
  public override void Simplify() {
    double gcd = GCD(Numerator, Denominator);
    numerator /= gcd;
    denominator /= gcd;
  }
  public override double GetValue() { return Numerator / Denominator; }
  private double GCD(double a, double b) {
    while (b != 0) {
      double temp = b;
      b = a % b;
      a = temp;
    }
    return a;
  }
}
public class FloatFraction : Fraction {
  public FixedFraction Numerator { get; }
  public FixedFraction Denominator { get; }
  public IrrationalFraction(double numerator, double denominator) {
    Numerator = numerator;
    Denominator = denominator;
  }
  public double GetValue(double epsilon = 1e-6) {
    double numValue = Numerator;
    double sqrtApproximation = numValue / 2.0;
    double prevSqrtApproximation;
    do {
      prevSqrtApproximation = sqrtApproximation;
      sqrtApproximation = 0.5 * (sqrtApproximation + numValue / sqrtApproximation);
    } while (Math.Abs(sqrtApproximation - prevSqrtApproximation) > epsilon);
    return sqrtApproximation / Denominator;
  }
}
```
### Polymorphism
```csharp
public static Fraction operator +(Fraction f1, Fraction f2) {
  double resultNumerator = f1.Numerator * f2.Denominator + f2.Numerator * f1.Denominator;
  double resultDenominator = f1.Denominator * f2.Denominator;
  return new FixedFraction(resultNumerator, resultDenominator);
}
public static Fraction operator +(Fraction f, double value) {
  double resultNumerator = f.Numerator + value * f.Denominator;
  double resultDenominator = f.Denominator;
  return new FixedFraction(resultNumerator, resultDenominator);
}
public static Fraction operator +(double value, Fraction f) {
  double resultNumerator = f.Numerator + value * f.Denominator;
  double resultDenominator = f.Denominator;
  return new FixedFraction(resultNumerator, resultDenominator);
}
public static Fraction operator +(double value1, double value2) {
    double resultNumerator = value1 + value2;
    double resultDenominator = 1;
    return new FixedFraction(resultNumerator, resultDenominator);
}
```
</details>

<details>
<summary>Shape (AObject2D)</summary>

### Encapsulation
```csharp
public abstract class AObject2D {
  protected Point start;
  protected Point end;
  public virtual Point Start { get { return start; } set { start = value; } }
  public virtual Point End { get { return end; } set { end = value; } }
  public int Width => Math.Abs(Start.X - End.X);
  public int Height => Math.Abs(Start.Y - End.Y);
  public AObject2D() { }
  public AObject2D(Point point1, Point point2) { Start = point1; End = point2; }
  public static void Relocate(ref Point point1, ref Point point2) {
    if (point1.X < point2.X & point1.Y > point2.Y) {
      int height = point1.Y - point2.Y;
      point1 = new Point(point1.X, point2.Y);
      point2 = new Point(point2.X, point2.Y + height);
      return;
    }
    if (point1.X > point2.X & point1.Y < point2.Y) {
      int width = point1.X - point2.X;
      point1 = new Point(point1.X - width, point1.Y);
      point2 = new Point(point2.X + width, point2.Y);
      return;
    }
    if (point1.X > point2.X & point1.Y > point2.Y) {
      int height = point1.Y - point2.Y;
      int width = point1.X - point2.X;
      point1 = new Point(point1.X - width, point2.Y);
      point2 = new Point(point2.X + width, point2.Y + height);
      return;
    }
  }
  public bool ContainsPoint(Point p) { return ContainsPoint(p.X, p.Y); }
  public bool ContainsPoint(int x, int y) { return (new Rectangle(Start.X, Start.Y, Width, Height).Contains(x, y)); }
  public abstract void FillSolid(Graphics graphics);
  public virtual void FillLinear(Graphics graphics) { FillSolid(graphics); }
  public virtual void FillRandom(Graphics graphics) { FillSolid(graphics); }
  public abstract AObject2D Clone();
}
```
### Inheritance
```csharp
class MyTriangle : AObject2D {
  public Point[] Points { get; set; }
  public MyTriangle() : base() { Points = new Point[3]; }
  public MyTriangle(Point point1, Point point2) : base(point1, point2) {
    Points = new Point[3];
    Points[0] = new Point(point1.X, point2.Y);
    Points[1] = new Point(point1.X + Math.Abs(point2.X - point1.X) / 2, point1.Y);
    Points[2] = point2;
  }
  public override void FillSolid(Graphics graphics) {
    Points[0] = new Point(Start.X, End.Y);
    Points[1] = new Point(Start.X + Math.Abs(End.X - Start.X) / 2, Start.Y);
    Points[2] = End;
    graphics.FillPolygon(new SolidBrush(InnerColor), Points);
  }
  public override AObject2D Clone() { return (AObject2D)MemberwiseClone(); }
}
class MyRectangle : AObject2D {
  public MyRectangle() : base() {}
  public MyRectangle(Point point1, Point point2) : base(point1, point2) {}
  public override void FillSolid(Graphics graphics) {
    graphics.FillRectangle(new SolidBrush(InnerColor), Start.X, Start.Y, Width, Height);
    graphics.DrawRectangle(new Pen(OuterColor), Start.X, Start.Y, Width, Height);
  }
  public override void FillLinear(Graphics graphics) {
    int[] arrayRGB = new int[3] { 255, 0, 0 };
    int incORdec = -2;
    int currentIndex = 0;
    int currentColor = arrayRGB[currentIndex];
    for (int x = Start.X; x < Width + Start.X; x++, currentColor += incORdec) {
      arrayRGB[currentIndex] = currentColor;
      if (currentColor >= 254 && incORdec > 0) {
        if (currentIndex == 0) {
          currentIndex = 2;
          incORdec = -incORdec;
          currentColor = arrayRGB[currentIndex];
        } else {
          incORdec = -incORdec;
          currentIndex--;
          currentColor = arrayRGB[currentIndex];
        }
      }
      if (currentColor <= 2 && incORdec < 0) {
        if (currentIndex == 0) {
          currentIndex = 2;
          incORdec = -incORdec;
          currentColor = arrayRGB[currentIndex];
        } else {
          incORdec = -incORdec;
          currentIndex--;
          currentColor = arrayRGB[currentIndex];
        }
      }
      graphics.FillRectangle(new SolidBrush(Color.FromArgb(arrayRGB[0], arrayRGB[1], arrayRGB[2])), x, Start.Y, 1, Height);
    }
  }
  public override AObject2D Clone() {
    return (AObject2D)MemberwiseClone();
  }
}
```
### Polymorphism
```csharp
using (Graphics g = Graphics.FromHwnd(IntPtr.Zero)) {  
  foreach (AObject2D shape in shapes) {
    shape.FillSolid(g);
  }
}
```
</details>

## Clean Code
  - TODO [Clean Code](https://www.oreilly.com/library/view/clean-code-a/9780136083238/)
  - TODO [Clean Architecture](https://www.oreilly.com/library/view/clean-architecture-a/9780134494272/)

## Algorithms and Data Structures

## Development Platform
NixOS (GNU/Linux) with the [following config](https://github.com/fvrrrn/nixcfg)
<details>
<summary>Platform-specific code: UNIX Socket Client</summary>

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <netdb.h>
#include <netinet/in.h>
#include <sys/un.h>
#include <signal.h>
#include ".h"

int clientfd;
void int_handler(int);
int main(int argc, char *argv[])
{
    signal(SIGINT, int_handler);
    struct sockaddr_un addr;
    char buffer[MESSAGE_SIZE];
    clientfd = socket(PF_UNIX, SOCK_STREAM, 0);
    if (clientfd < 0)
    {
        perror("[CLIENT] Creation error");
        exit(EXIT_FAILURE);
    }
    printf("[CLIENT] Socket created. id = %d\n", clientfd);
    bzero((char *)&addr, sizeof(addr));
    addr.sun_family = AF_UNIX;
    strcpy(addr.sun_path, SERVER_SOCK_FILE);
    if (connect(clientfd, (struct sockaddr *)&addr, sizeof(addr)) == -1)
    {
        perror("[CLIENT] Connection error");
        exit(EXIT_FAILURE);
    }
    printf("[CLIENT] Connected to socket.\n");
    while (1)
    {
        bzero(buffer, MESSAGE_SIZE);
        fgets(buffer, MESSAGE_SIZE - 1, stdin);
        printf("buffer lentgth: %d\nid=%d", (int)(strlen(buffer) + 1), clientfd);
        if (send(clientfd, buffer, (strlen(buffer) + 1), 0) < 0)
        {
            perror("[CLIENT] Sending error");
            exit(EXIT_FAILURE);
        }
        printf("[CLIENT] Sent to server.\n\tMessage: %s\tLength: %d\n", buffer, (int)strlen(buffer));
        if ((recv(clientfd, buffer, (strlen(buffer) + 1), 0)) <= 0)
        {
            perror("[CLIENT] Receiving error");
            exit(EXIT_FAILURE);
        }
        printf("[CLIENT] Received from server.\n\tMessage: Echo - %s\tLength: %d\n", buffer, (int)strlen(buffer));
    }
    exit(EXIT_SUCCESS);
}
void int_handler(int sig)
{
    signal(sig, SIG_IGN);
    close(clientfd);
    printf("\n[CLIENT] Client closed. id = %d\n", clientfd);
    exit(EXIT_SUCCESS);
}
```
</details>

<details>
<summary>Platform-specific code: UNIX Socket Server</summary>

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/un.h>
#include <netdb.h>
#include <netinet/in.h>
#include <signal.h>
#include ".h"

void process_client(int clientfd);
void int_handler(int);
int clientfd, newsockfd;
int main(int argc, char *argv[])
{
    signal(SIGINT, int_handler);
    char buffer[256];
    struct sockaddr_un serv_addr;
    clientfd = socket(AF_UNIX, SOCK_STREAM, 0);
    if (clientfd < 0)
    {
        perror("[SERVER] Cannot create socket");
        exit(EXIT_FAILURE);
    }
    printf("[SERVER] Server is created. id = %d\n", clientfd);
    bzero((char *)&serv_addr, sizeof(serv_addr));
    serv_addr.sun_family = AF_UNIX;
    strcpy(serv_addr.sun_path, SERVER_SOCK_FILE);
    unlink(SERVER_SOCK_FILE);
    if (bind(clientfd, (struct sockaddr *)&serv_addr, sizeof(serv_addr)) < 0)
    {
        perror("[SERVER] Cannot bind socket");
        exit(EXIT_FAILURE);
    }
    printf("[SERVER] Server is bounded.\n");
    if (listen(clientfd, 5) < 0)
    {
        perror("[SERVER] Socket not listening");
        exit(EXIT_FAILURE);
    }
    printf("[SERVER] Server listening.\n");
    while (1)
    {
        newsockfd = accept(clientfd, NULL, 0);
        printf("[SERVER] Incoming connection established. id = %d\n", newsockfd);
        if (newsockfd < 0)
        {
            perror("[SERVER] Establishing error");
            exit(EXIT_FAILURE);
        }
        if (!fork())
        {
            close(clientfd);
            process_client(newsockfd);
            exit(EXIT_SUCCESS);
        }
        close(newsockfd);
    }
}
void process_client(int clientfd)
{
    while (1)
    {
        char buffer[MESSAGE_SIZE];
        bzero(buffer, MESSAGE_SIZE);
        if (recv(clientfd, buffer, MESSAGE_SIZE, 0) < 0)
        {
            perror("[SERVER] Receiving error");
            break;
        }
        printf("[SERVER] Received from client.\n\tMessage: %s\tLength: %d\n", buffer, (int)strlen(buffer));
        if (send(clientfd, buffer, (strlen(buffer) + 1), 0) < 0)
        {
            perror("[SERVER] Sending error");
            // exit(EXIT_FAILURE);
            break;
        }
        printf("[SERVER] Sent to server.\n\tMessage: Echo - %s\tLength: %d\n", buffer, (int)strlen(buffer));
    }
}
void int_handler(int sig)
{
    signal(sig, SIG_IGN);
    close(newsockfd);
    printf("\n[SERVER] Client closed. id = %d\n", newsockfd);
    close(clientfd);
    printf("[SERVER] Server closed. id = %d\n", clientfd);
    exit(EXIT_SUCCESS);
}
```
</details>

## Stack

## Databases

## Cloud Technologies

## Testing

## Development Methodologies
