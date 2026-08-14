# Airline Passenger Management System — C++

**C++ · Object-Oriented Programming · STL · Polymorphism · Smart Pointers · File Processing**

A C++ airline simulation and passenger-management project modelling passengers, aircraft cabins, seats, airports, flights, frequent-flyer status, and route calculations.

> **Attribution:** This project is a **modified educational version of a program originally developed by Dr. Francisco Ortega at Colorado State University**. The original program provided instructional architecture, starter code, and coursework scaffolding. This repository represents an adaptation and extension of that program completed as part of my computer science coursework.

---

## Overview

The project explores the design of a larger object-oriented C++ application by modelling several interacting components of an airline system.

Rather than representing the application as a single procedural program, the system separates responsibilities across objects representing:

* Passengers
* Passenger databases
* Seats
* Aircraft cabins
* Airplanes
* Airports
* Flights
* Airline operators
* Frequent-flyer membership
* Route-distance calculations

The project was particularly useful for working with **class hierarchies, polymorphism, STL containers, smart pointers, file I/O, enums, optional values, custom exceptions, and modular C++ application design**.

---

# System Architecture

At a high level, the project models the following relationship:

```text
Passenger Records
       │
       ▼
Passenger Database
       │
       ▼
Seat Assignment
       │
       ▼
Aircraft Cabin
       │
       ▼
Airplane
       │
       ▼
Flight
       │
       ├── Origin Airport
       └── Destination Airport
                │
                ▼
         Haversine Distance
```

Individual components are implemented as separate C++ classes and source files rather than concentrating all application logic in `main()`.

---

# Core Components

## Passengers

Passenger information is represented by the `passenger` class.

Each passenger can contain:

```text
Name
Age
Seat preference
Frequent-flyer level
Employee status
Banned status
Accumulated miles
Money spent
```

Passenger names are represented separately using:

```cpp
class passengerName
```

while passenger records are stored through STL containers such as:

```cpp
std::map<std::string, passenger>
std::vector<passenger>
```

The map representation provides a basis for retrieving and updating passenger records by surname.

---

## Frequent-Flyer System

The project represents several levels of airline loyalty membership:

```cpp
enum class frequentFlyer
{
    InviteOnly,
    Gold,
    Silver,
    Bronze,
    Regular
};
```

This allows passenger status to be represented using strongly typed enumerations rather than arbitrary numeric or string constants.

The coursework design also includes business-rule scaffolding for applying different mileage benefits according to loyalty status.

---

## Seat Preferences

Passengers can express one of three seat preferences:

```cpp
enum class seatTypePreference
{
    window,
    middle,
    aisle
};
```

This information can then be used by cabin-assignment logic when implementing criteria-based seating.

---

# Seat Abstraction & Polymorphism

One of the main object-oriented components is the seat hierarchy.

The system defines an abstract interface:

```cpp
class iSeat
```

which specifies common seat operations such as:

```cpp
isOccupied()
getPassenger()
getLetter()
setPassenger()
removePassenger()
replacePassenger()
getEmergencySeat()
setEmergencySeat()
isReclining()
setReclining()
```

Concrete seat implementations can inherit from this interface.

Conceptually:

```text
                  iSeat
                    │
          ┌─────────┴─────────┐
          │                   │
        seat          specialized seats
```

This architecture allows different types of seats to be manipulated through a common interface.

---

# Smart Pointer Usage

Seats are represented using shared pointers:

```cpp
using iSeatPtr = std::shared_ptr<iSeat>;
```

Rows and cabins are then represented as collections of these pointers:

```cpp
using vecSeatsPtr = std::vector<iSeatPtr>;

using vecVecSeatsPtr =
    std::vector<std::vector<iSeatPtr>>;
```

This provides experience working with:

* Dynamic object lifetimes
* Shared ownership
* Polymorphic objects
* Nested STL containers
* Pointer-based class interfaces

without relying directly on manually managed raw pointers.

---

# Cabin Representation

An aircraft cabin consists of rows containing collections of seats.

The `cabin` class manages operations including:

```text
Cabin capacity
Cabin rows
Available seats
Passenger assignment
Passenger lookup
Seat lookup
Passenger processing
Cabin clearing
```

Cabins can contain multiple seating sections.

The simulation constructs aircraft with combinations such as:

```text
First Class
     +
Economy
     │
     ▼
Complete Aircraft Cabin
```

Different cabin configurations can also contain different numbers and arrangements of seats.

---

# Cabin Container Hierarchy

The project separates seating sections using a base cabin-container class:

```cpp
baseSeatCabinContainer
```

with specialised container types for cabin classes such as:

```text
First
Business
Premium Economy
Economy
Basic Economy
```

The base class provides shared operations for:

* Retrieving rows
* Determining available seat letters
* Counting rows
* Accessing the underlying cabin representation

This demonstrates the use of inheritance to provide common behaviour across related object types.

---

# Aircraft

Aircraft are represented through:

```cpp
class airplane
```

Each airplane includes:

```text
Plane ID
Airline
Cabin
Capacity
Passenger assignment
```

The program also models multiple airline operators using:

```cpp
enum class airlineName
{
    Taco_Airline,
    Ram_Airline,
    Cheap_Boulder_Airline,
    Miami_Airline,
    Bielsa_Airline
};
```

The simulation driver can generate large collections of aircraft programmatically.

---

# Airports

Airport data is represented using:

```cpp
struct airport
```

containing fields for:

```text
IATA code
Airport name
City
State
Country
Latitude
Longitude
Active status
```

Airport records are stored using:

```cpp
std::map<std::string, airport>
```

where the IATA code can act as the lookup key.

An `airports` class provides methods for retrieving airports by:

* Airport code
* Airport name

and for accessing collections of available airports.

---

# CSV File Processing

Airport data is loaded from:

```text
airports.csv
```

using standard C++ file I/O.

The processing pipeline is approximately:

```text
airports.csv
      │
      ▼
std::ifstream
      │
      ▼
Tokenize CSV row
      │
      ▼
Construct airport object
      │
      ▼
std::map<IATA, airport>
```

The project implements utility functions for reading and tokenizing external files rather than hard-coding all airport information into the application.

---

# Passenger Data Generation

The program also reads a large text file containing names:

```text
nameofpeople.txt
```

The configured input size is:

```cpp
197247
```

records.

The program uses this data to construct synthetic passenger records.

First and last names are combined after randomly shuffling indices, and additional attributes are assigned programmatically.

These include:

```text
Age
Seat preference
Frequent-flyer level
Employee status
Banned status
```

This produces a large synthetic population with which the airline classes can be exercised.

---

# Flight Modelling

Individual flights are represented through:

```cpp
class flight
```

A flight contains:

```text
Route
Aircraft
Round-trip status
Flight status
```

Flight status is represented using:

```cpp
enum class flightStatus
{
    flown,
    cancelled,
    idle
};
```

Multiple flights are managed by a higher-level:

```cpp
class flights
```

which maintains:

```cpp
std::vector<flight>
```

alongside shared passenger records.

---

# Route Generation

The simulation constructs flight routes using airport records loaded from the CSV dataset.

Airport identifiers are extracted from the airport map, shuffled, and used to generate routes.

Conceptually:

```text
Airport Dataset
      │
      ▼
Extract IATA keys
      │
      ▼
Shuffle airports
      │
      ▼
Select origin
      │
      ▼
Select destination
      │
      ▼
Construct flight
```

This provides a simple mechanism for producing many different simulated routes.

---

# Haversine Distance Calculation

Flight distance is calculated using the **Haversine formula**.

Given the latitude and longitude of two airports:

```text
Airport A
(lat₁, lon₁)

Airport B
(lat₂, lon₂)
```

the program estimates their great-circle distance across the Earth.

The implementation is encapsulated in:

```cpp
class haversine
```

with:

```cpp
static double calculate(
    double lat1,
    double lon1,
    double lat2,
    double lon2
);
```

and an Earth radius of approximately:

```text
6,371 km
```

The resulting distance can then be used by the flight-processing logic.

---

# Round-Trip Flights

Flights can optionally be identified as round trips.

When a route is marked as round trip, the calculated travel distance is doubled:

```cpp
if (roundTrip)
    distance *= 2;
```

This distance can subsequently feed into passenger mileage and flight-cost calculations.

---

# STL Usage

The project makes extensive use of the C++ Standard Library.

### `std::vector`

Used for:

* Passengers
* Flights
* Aircraft
* Airports
* Cabin rows
* Seats

### `std::map`

Used for keyed collections such as:

```cpp
std::map<std::string, passenger>
std::map<std::string, airport>
```

### `std::array`

Used for the fixed-size name dataset.

### `std::optional`

Used when an operation may or may not return a valid object.

Examples include:

```text
Passenger occupying a seat
Airport lookup
Passenger lookup
```

### `std::tuple`

Used when an operation needs to return multiple related values.

### `std::shared_ptr`

Used for polymorphic seat ownership and shared passenger collections.

### `std::unique_ptr`

Used where exclusive ownership of dynamically returned collections is appropriate.

---

# Modern C++ Features

The project provides practical experience with several modern C++ concepts, including:

```text
Strongly typed enum classes
Smart pointers
std::optional
Structured bindings
Range-based loops
std::ranges
Template functions
Defaulted move/copy operations
RAII file handling
Operator overloading
Polymorphism
```

The code also contains a template utility for converting map keys into vectors:

```cpp
template <typename T, typename R, typename S>
std::vector<T> mapKeysToVector(const std::map<R,S>& m)
```

and a generic vector concatenation operator.

---

# Operator Overloading

Output operators are overloaded for domain objects such as passengers and flight collections.

For example:

```cpp
std::ostream& operator<<(
    std::ostream& os,
    const passenger& obj
);
```

This allows domain objects to participate naturally in normal C++ output streams:

```cpp
std::cout << passenger;
```

---

# Error Handling

The project defines a custom exception:

```cpp
class fixedFileException :
    public std::runtime_error
```

which is used when required input files cannot be opened.

The code also handles failed map lookups through standard exceptions such as:

```cpp
std::out_of_range
```

This provided practice with both:

* Standard exception handling
* Custom exception types

---

# Large-Scale Simulation

The main simulation driver is capable of constructing a substantial synthetic object graph.

The current configuration requests:

```text
197,247 source names
10,000 aircraft
10,000 generated flight objects
Multiple cabin configurations
Airport routes selected from CSV data
```

This makes the project useful for exploring how object-oriented structures and STL containers behave when managing larger collections of interconnected objects.

---

# Project Structure

```text
airline-passenger-system/
│
├── airram.cpp
│
├── airplane.h
├── airplane.cpp
│
├── airport.h
├── airport.cpp
│
├── passenger.h
├── passenger.cpp
│
├── flights.h
├── flights.cpp
│
├── cabin.h
├── cabin.cpp
│
├── iSeat.h
├── seat.h
├── seat.cpp
│
├── deluxeSeat.h
├── deluxeSeat.cpp
│
├── baseSeatCabinContainer.h
├── baseSeatCabinContainer.cpp
│
├── firstSeatCabinContainer.h
├── firstSeatCabinContainer.cpp
│
├── businessSeatCabinContainer.h
├── businessSeatCabinContainer.cpp
│
├── premiumEconomySeatCabinContainer.h
├── premiumEconomySeatCabinContainer.cpp
│
├── economySeatCabinContainer.h
├── economySeatCabinContainer.cpp
│
├── basicEconomySeatCabinContainer.h
├── basicEconomySeatCabinContainer.cpp
│
├── haversine.h
├── haversine.cpp
│
├── readUtil.h
├── readUtil.cpp
│
├── helper.h
├── airRamException.h
├── ct301Tester.h
├── ct301Tester.cpp
│
├── airports.csv
├── nameofpeople.txt
└── makefile
```

---

# Design Concepts Demonstrated

## Object-Oriented Design

The application separates different airline concepts into domain-specific classes rather than storing the complete simulation in a single structure.

## Encapsulation

Objects expose operations through public interfaces while maintaining their own internal state.

## Inheritance

Shared cabin and seat behaviour can be defined in base classes or interfaces and specialised by derived types.

## Polymorphism

Different seat implementations can be accessed through the common `iSeat` interface.

## Composition

Higher-level objects are constructed from lower-level domain objects:

```text
Seats
  ↓
Cabin
  ↓
Airplane
  ↓
Flight
```

## STL Data Structures

Different containers are selected according to the access patterns required by different parts of the system.

## Resource Management

Smart pointers provide explicit ownership semantics for dynamically managed objects.

---

# Educational Context

This repository originated as coursework designed to explore increasingly sophisticated C++ programming concepts through a single evolving airline domain.

The project should therefore be understood as an **educational software-engineering exercise rather than a production airline reservation platform**.

It contains both implemented functionality and instructional scaffolding intended for extension and experimentation.

The exercise provided experience working within an **existing codebase**, understanding an instructor-provided architecture, extending domain models, and integrating changes across multiple interconnected classes—skills that are directly relevant to maintaining and extending real-world software systems.

---

# Attribution

The underlying educational program and starter architecture were originally developed by:

**Dr. Francisco Ortega**
**Colorado State University**

This repository is a **modified version of that instructional program**, created as part of my coursework at Colorado State University.

Dr. Ortega's original material provided the foundation and instructional scaffolding for the project. I am including this attribution explicitly to distinguish the provided educational framework from my own modifications and coursework contributions.

---

# What This Project Demonstrates

### C++ Programming

* Multi-file C++ development
* Header/source separation
* Classes and objects
* Strongly typed enumerations
* Templates
* Operator overloading
* Exception handling

### Object-Oriented Programming

* Encapsulation
* Inheritance
* Polymorphism
* Abstract interfaces
* Composition

### Memory Management

* `std::shared_ptr`
* `std::unique_ptr`
* RAII principles
* Polymorphic ownership

### Data Structures

* `std::vector`
* `std::map`
* `std::array`
* `std::optional`
* `std::tuple`

### Data Processing

* Text-file parsing
* CSV parsing
* External datasets
* Synthetic data generation
* Randomization

### Software Design

* Existing-codebase modification
* Modular architecture
* Domain modelling
* Separation of responsibilities
* Reusable utility functions

### Applied Algorithms

* Haversine great-circle distance
* Randomized route generation
* Collection searching and transformation

---

# Potential Future Improvements

As an educational project, several areas could be expanded or modernised further:

* Complete remaining assignment scaffolding
* Add comprehensive unit tests
* Add deterministic random seeds for testing
* Improve CSV parsing robustness
* Add unique passenger identifiers
* Complete criteria-based seat assignment
* Expand specialised seat implementations
* Add complete frequent-flyer reward calculations
* Add early validation for invalid passenger data
* Implement more sophisticated route planning
* Add persistent passenger and flight storage
* Add command-line configuration
* Add benchmarking for large simulations
* Introduce CI-based automated builds and tests
* Refactor duplicated cabin-class implementations

---

# Author

**Alexia Riner**

Modified and extended as part of computer science coursework at **Colorado State University**.

---

## Acknowledgement

Special thanks to **Dr. Francisco Ortega of Colorado State University**, who developed the original instructional program on which this modified project is based.
