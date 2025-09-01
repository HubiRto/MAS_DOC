# MAS - implemnetacje i informacje

## Atrybuty:

- ### Opcjonalny [0..1]
```java
class Person {
    private Optional<Integer> phoneNumber;
}
```

- ### Wymagany (domyślnie każdy atrybut)
```java
class Person {
    private Integer phoneNumber;
    
    public void setPhoneNumber(Integer newPhoneNumber) {
        if(newPhoneNumber == null) {
            throw new IllegalArgumentException("Pusty numer tel");
        }
        this.phoneNumber = newPhoneNumber;
    }
}

```

- ### <u>Klasowy</u>
```java
class Student {
    private static int MAX_GRADE = 6;
}
```

- ### Powtarzalny [1..*]
```java
class Student {
    private List<Integer> grades = new ArrayList<>(); // Powtarzalny
}
```

- ### Złożony
```java
class Student {
    private School school; // Po prostu pole jako klasa
}
```

- ### / Wyliczalny
```java
class Rectangle {
    private double width;
    private double height;
    
    public double getArea() {
        return width * height; // Wyliczamy coś na podstawie pól
    }
}
```

## Ograniczenia:

- ### {Unique}
```java
class Product {
    private static final Set<Product> extension = new HashSet<>();
    private String sku;

    public Product(String sku) {
        setSku(sku);
        extension.add(this);
    }

    public void setSku(@NotNull String newSku) {
        for(Product P : extension) {
            if(p.sku.equals(newSku)){
                throw new IllegalArgumentException("SKU już istnieje: " + newSku);
            }
        }
        this.sku = newSku;
    }
}
```

- ### {Subset}
To przypadek gdzie jedna klasa ma podwójną relację z druga klasą np. Pracownik i Projekt, bo pracownik jednoczesnie może kierowac projektem i być jego cześcią, ale relacje są od siebie zależne, czyli pracownik nie może kierować projektem jak wcześniej nie był częscią.
```java
class Project {
    private final Set<Employee> members = new HashSet<>(); // Nadrżędna
    private final Set<Employee> leaders = new HashSet<>();

    public void addLeader(Employee e){
        if (!members.contains(e)) throw new IllegalStateException("Najpierw dodaj do members");
        // dodajemy do leaders i w klasie Employee do ref
    }
}
```

- ### {Ordered}
```java
class Playlist {
    private List<String> songs = new ArrayList<>(); // Zachowuje kolejność

    public void addSong(String title) {
        songs.add(title);
    }

    public List<String> getSongs() {
        return songs;
    }
}
```

- ### {Bag}
```java
class Customer {
    private final List<Article> purchasedArticles = new ArrayList<>();

    public void buy(Article a) {
        // Sprawdzamy tylko czy nie jest null
        purchasedArticles.add(a); // Nie wykonujemy sprawdzenia
    }
}

class Article {
    private final String name;
}
```

- ### {XOR}
```java
class Person {
    // Może byc albo jedną albo druga rolą, ale nie oba na raz
    private Company employer;
    private University university;
  
    private void setEmployer(Company c) {
        // sprawdzamy czy nie jest na university else throw
        // jeżeli wartość c == emplyer return
        // ustawiamy pole employer i w klasie c dodajemy Person
    }
    // To samo dla uniwersytetu
}
```

## Asocjacje

- ### Skierowana
```java
//  Company <----- Person
class Company { 
    private Set<Person> people = new HashSet<>();
}

class Person {
    private String name;
    // BRAK referencji
}
```

- ### Rekurencyjna
```java
class Person {
    private List<Person> parents = new ArrayList<>();
    private List<Person> children = new ArrayList<>();
}
```

- ### Zwykła
```java
class X {
    private List<Y> yy = new HashSet<>();
}

class Y {
    private X x; // Jedne do wiele
    private List<X> xx; // Wiele do wiele
}
```

- ### Z atrybutem
```java
class Student  {
    private List<StudentCourse> ref;
}

class Course   {
    private List<StudentCourse> ref;
}

class StudentCourse {
    private double grade;
    private Student student;
    private Course course;
}

// Przy dodawnia czy usuwaniu trzeba zadbać o spójność referencji
```

- ### Kwalifikowana
![ss](https://github.com/user-attachments/assets/08a751e5-d972-4492-a7a7-586edec94201)
```java
class Book {
    private String isbn;
}

class Library {
    private final Map<String, Book> byIsbn = new HashMap<>();
    
    // Setter - sprawdzamy czy już nie istnieje
    // Update
    // Remove
}
```

- ### Agregacja (słaba całość-część – część może istnieć niezależnie) - jak asocjacja zwykła
```java
class Team {
    private Set<Player> players = new HashSet<>();
}

class Player {
    private Team team; // może istnieć i bez teamu = null
}
```

- ### Kompozycja (silna całość-część – część nie istnieje bez całości)
```java
class Budynek {
    private Set<Pokój> pokoje = new HashSet<>();
}

class Pokój {
    private Pokój pokój; // Nigdy nie może być == null aż do końca istnienia siebie lub budynku
}
```

## Dziedziczenia:

- ### Abstract
```java
abstract class Payment  {
    protected double amount;
    public abstract void processPayment(); // <-------- METODA ABSTRAKCYJNA
}

class CreditCardPayment extends Payment {
    private String cardNumber;

    @Override
    public void processPayment() { // To jest polimorfizmy
        // Tu można dodać logikę walidacji karty
    }
}

class BankTransferPayment extends Payment {
    private String bankAccount;

    @Override
    public void processPayment() {
        // Tu można dodać logikę transferu bankowego
    }
}
```

![xd](https://64.media.tumblr.com/tumblr_m93uaqMjbD1qmtheto1_1280.gifv)

- ### Dziedziczenie overlapping (nie ma settera, ale może być kilka)
```java
class Employee   {
    private String name;
    private Teacher teacherRole;
    private Researcher researcherRole;

    public Employee(String name, Teacher teacherRole, Researcher researcherRole) {
        this.name = name;
        this.teacherRole = teacherRole;
        this.researcherRole = researcherRole;
    }

    public void printInfo() {
        if (teacherRole != null) teacherRole.printInfo();
        if (researcherRole != null) researcherRole.printInfo();
    }
}

class Teacher {
    private int teachingHours;
    public void printInfo() { System.out.println("  Teaching hours: " + teachingHours); }
}

class Researcher {
    private int researchProjects;
    public void printInfo() { System.out.println("  Research projects: " + researchProjects); }
}
```

- ### Dziedziczenie dynamiczne (jest setter, ale tylko jedno)
```java
class Vehicle {
    private String plate;
    private Car carRole;
    private Truck truckRole;

    public Vehicle(String plate, Car carRole, Truck truckRole) {
        this.plate = plate;
        this.carRole = carRole;
        this.truckRole = truckRole;
    }

    public void changeRoleToCar(int passengerCapacity) {
        truckRole = null;
        carRole = new Car(passengerCapacity);
    }

    public void changeRoleToTruck(int loadCapacity) {
        carRole = null;
        truckRole = new Truck(loadCapacity);
    }

    public void printInfo() {
        System.out.println("Vehicle: " + plate);
        if (carRole != null) carRole.printInfo();
        if (truckRole != null) truckRole.printInfo();
    }
}

class Car {
    private int passengerCapacity;
}

class Truck {
    private int loadCapacity;
}
```

- ### Wielo-aspektowe
```java
// Zwykłe dziedziczenie
class Person {
    protected String name;

    public Person(String name) {
        this.name = name;
    }

    public void printIdentity() {
        System.out.println("Name: " + name);
    }
}

// Kompozycja
class Job {
    private String position;

    public Job(String position) {
        this.position = position;
    }

    public void printJob() {
        System.out.println("Position: " + position);
    }
}

// Klasa dziedzicząca: normalne dziedziczenie + kompozycja
class Employee extends Person {
    private Job job;

    public Employee(String name, String position) {
        super(name);          // dziedziczenie
        this.job = new Job(position);  // kompozycja
    }

    public void printInfo() {
        printIdentity();
        job.printJob();
    }
}
```















