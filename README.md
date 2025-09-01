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
![](https://github.com/user-attachments/assets/6f6061d5-95e2-4bd0-8cb8-b4f74ce64bdf)
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
![](https://github.com/user-attachments/assets/bb3c0197-5c51-4d25-91fd-8321c1228788)
```java
class Person {
    private List<Person> parents = new ArrayList<>();
    private List<Person> children = new ArrayList<>();
}
```

- ### Zwykła
![](https://github.com/user-attachments/assets/a4942547-5fd7-4c4e-a8d2-e61661b05a30)
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
![](https://github.com/user-attachments/assets/3fb7bd7a-8b2a-43db-ba77-43f7bb8f2642)
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
![](https://github.com/user-attachments/assets/08a751e5-d972-4492-a7a7-586edec94201)
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
![](https://github.com/user-attachments/assets/e5073823-a81c-42d3-86e2-ba0ab4e628ba)
```java
class Team {
    private Set<Player> players = new HashSet<>();
}

class Player {
    private Team team; // może istnieć i bez teamu = null
}
```

- ### Kompozycja (silna całość-część – część nie istnieje bez całości)
![](https://github.com/user-attachments/assets/51445e0c-d15b-4c29-900d-9c56416cd6c7)
```java
class Budynek {
    private Set<Pokój> pokoje = new HashSet<>();

    public Budynek stworzBudynek(int numer) {
        Pokoj pokoj = new Pokoj(numer);
        return new Budynek(pokoj);
    }

    static class Pokój {
        private int numer;
    }
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















