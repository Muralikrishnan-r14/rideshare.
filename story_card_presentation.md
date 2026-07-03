# Presentation Content — 3 Story Cards

---

## How Data is Stored (No Database, No Input File)

This project uses **in-memory storage** using Java `ArrayList` collections. There is **no SQL database, no file input, and no .csv file** being read. All data lives in RAM only while the program is running.

### Database.java (The In-Memory Store)

```java
public class Database {
    public static ArrayList<Driver> drivers = new ArrayList<>();
    public static ArrayList<Passenger> passengers = new ArrayList<>();
    public static ArrayList<Ride> rides = new ArrayList<>();
    public static ArrayList<Booking> bookings = new ArrayList<>();
    public static ArrayList<Rating> ratings = new ArrayList<>();
    public static ArrayList<AuditLog> auditLogs = new ArrayList<>();
}
```

**How it works:**
- Each `ArrayList` acts like a **table in a database**. For example, `Database.passengers` is the passengers table.
- `static` means there is only **one shared copy** across the entire application — every service class accesses the same data.
- When a user registers, a new object is created and **added** to the list (`Database.passengers.add(passenger)`).
- When viewing data, we **loop through** the list (`for (Passenger p : Database.passengers)`).
- When the application stops, **all data is lost** because it's stored only in memory (RAM), not on disk.

**Data Flow Example:**
```
User registers as Passenger
    → PassengerService creates a Passenger object
    → Calls Database.passengers.add(passenger)
    → The Passenger object is now inside the ArrayList

Admin clicks "View Passengers"
    → PassengerService loops through Database.passengers
    → Prints each Passenger using its toString() method
```

---

## Story Card 1: US06 — Approve Ride

> **User Story:** As an admin, I can approve the rides which are added by the driver.

### How the Admin Reaches This Feature

```
Main Menu → 1. Login → 1. Admin Login → username: admin, password: admin123
→ Admin Menu → 1. Approve Rides → calls DriverService.approveRide()
```

In `LoginService.adminMenu()`:
```java
case 1:
    DriverService.approveRide();   // Admin selects option 1, control goes to this method
    break;
```

### Complete Code — approveRide()

```java
public static void approveRide() {

    boolean found = false;
    // A flag variable to track if any pending rides exist

    System.out.println("\n===== PENDING RIDES =====");
    // Prints a header on the console

    for (Ride ride : Database.rides) {
    // Loops through every Ride object stored in the Database.rides ArrayList

        if (ride.getRegistrationStatus().equals("PENDING")) {
        // Checks if this ride's approval status is "PENDING"
        // When a driver creates a ride, it is saved with status "PENDING"

            found = true;
            // At least one pending ride exists, so set flag to true

            System.out.println(ride);
            // Calls ride.toString() which prints:
            // Ride ID, Driver ID, Source, Destination, Distance, Fare,
            // Ride Status, Approval Status
        }
    }

    if (!found) {
    // If no ride had "PENDING" status, the flag is still false

        System.out.println("No Pending Rides Found");
        // Error message displayed to admin
        return;
        // Exit the method, go back to admin menu
    }

    System.out.print("\nEnter Ride ID to Approve: ");
    // Prompt admin to enter which ride to approve

    int rideId = sc.nextInt();
    // Read the ride ID entered by admin
    sc.nextLine();
    // Clear the leftover newline character from the scanner buffer

    for (Ride ride : Database.rides) {
    // Loop through all rides again to find the matching ride

        if (ride.getRideId() == rideId) {
        // Found the ride whose ID matches what admin entered

            ride.setRegistrationStatus("APPROVED");
            // Changes the ride's approval status from "PENDING" to "APPROVED"
            // Inside setRegistrationStatus(), modifiedOn timestamp is also updated:
            //     this.registrationStatus = status;
            //     this.modifiedOn = LocalDateTime.now();

            AuditService.createLog("APPROVE", "RIDE", "Ride Approved: " + rideId);
            // Creates an audit log entry recording this action
            // This log is stored in Database.auditLogs ArrayList

            System.out.println("Ride Approved Successfully!");
            // Success message

            return;
            // Exit the method, go back to admin menu
        }
    }

    System.out.println("Ride Not Found");
    // If no ride matched the entered ID, show error message
}
```

### Ride Model — How Status & Timestamps Work

```java
// Inside Ride.java:
private String registrationStatus;   // "PENDING" → "APPROVED"
private LocalDateTime createdOn;     // Set once when ride is created
private LocalDateTime modifiedOn;    // Updated every time a setter is called

public void setRegistrationStatus(String status) {
    this.registrationStatus = status;       // Update the status
    this.modifiedOn = LocalDateTime.now();   // Record the timestamp of modification
}
```

### Execution Flow Summary

```
1. Admin logs in → adminMenu() shows options
2. Admin picks "1. Approve Rides" → calls approveRide()
3. Method loops through Database.rides, prints rides with status "PENDING"
4. If no pending rides → prints "No Pending Rides Found" and returns
5. Admin enters a Ride ID
6. Method finds matching ride → changes status to "APPROVED"
7. modifiedOn timestamp is updated automatically inside the setter
8. An audit log entry is created via AuditService.createLog()
9. "Ride Approved Successfully!" message is displayed
10. If Ride ID not found → "Ride Not Found" error message
```

---

## Story Card 2: US05 — View Registered Passengers

> **User Story:** As an admin, I can view all passenger records which are registered in the system.

### How the Admin Reaches This Feature

```
Main Menu → 1. Login → 1. Admin Login → Admin Menu → 3. View Passengers
→ calls PassengerService.viewPassengers()
```

In `LoginService.adminMenu()`:
```java
case 3:
    PassengerService.viewPassengers();   // Admin selects option 3
    break;
```

### Complete Code — viewPassengers()

```java
public static void viewPassengers() {

    if (Database.passengers.isEmpty()) {
    // Checks if the passengers ArrayList has zero elements
    // isEmpty() returns true if no passengers have registered yet

        System.out.println("\nNo Passengers Found!");
        // Error message when no data exists
        return;
        // Exit the method, go back to admin menu
    }

    System.out.println("\n===== PASSENGER DETAILS =====");
    // Prints a header

    for (Passenger passenger : Database.passengers) {
    // Enhanced for-loop: iterates through every Passenger object
    // in the Database.passengers ArrayList one by one

        System.out.println(passenger);
        // Calls passenger.toString() which prints:
        // Passenger ID, Name, Email, Mobile, City, Status

        System.out.println("--------------------------");
        // Separator line between each passenger record
    }
}
```

### Passenger Model — toString() Method

```java
// Inside Passenger.java:
@Override
public String toString() {
    return "Passenger ID : " + passengerId +    // e.g., "Passenger ID : 5529549"
            "\nName : " + passengerName +        // e.g., "Name : John Doe"
            "\nEmail : " + email +               // e.g., "Email : john@example.com"
            "\nMobile : " + mobile +             // e.g., "Mobile : 9876543210"
            "\nCity : " + city +                 // e.g., "City : Chennai"
            "\nStatus : " + registrationStatus + // e.g., "Status : ACTIVE"
            "\n";
}
```

When `System.out.println(passenger)` is called, Java automatically calls the `toString()` method of the Passenger object, which returns a formatted string with all the details.

### How Passenger Data Gets Into the ArrayList

```
1. User selects "2. Register Passenger" from main menu
2. PassengerService.registerPassenger() collects: name, email, password, mobile, city
3. All inputs are validated using UserValidator and DuplicateChecker
4. A unique ID is generated: IdGenerator.generatePassengerId()
5. A new Passenger object is created with all the data
6. Object is added to the list: Database.passengers.add(passenger)
7. Now when admin calls viewPassengers(), this passenger shows up in the loop
```

### Execution Flow Summary

```
1. Admin logs in → adminMenu() shows options
2. Admin picks "3. View Passengers" → calls viewPassengers()
3. Method checks if Database.passengers is empty
4. If empty → prints "No Passengers Found!" and returns
5. If not empty → prints "===== PASSENGER DETAILS =====" header
6. Loops through each Passenger object in the ArrayList
7. For each passenger → calls toString() which prints all details
8. Prints a separator line "----" after each passenger
9. After all passengers are printed, method ends, returns to admin menu
```

---

## Story Card 3: US07 — Admin Activity Audit Trail

> **User Story:** As an admin, I need to maintain the logs of all CRUD operations (for Driver, Passenger, Rides and Rating data), so that changes are traceable.

### How the Admin Reaches This Feature

```
Main Menu → 1. Login → 1. Admin Login → Admin Menu → 5. View Audit Logs
→ calls AuditService.viewLogs()
```

In `LoginService.adminMenu()`:
```java
case 5:
    AuditService.viewLogs();   // Admin selects option 5
    break;
```

### Complete Code — AuditService.java (Both Methods)

```java
package com.rideshare.service;

import com.rideshare.repository.Database;
// Imports the Database class to access the auditLogs ArrayList

import com.rideshare.model.AuditLog;
// Imports the AuditLog model class

import com.rideshare.util.IdGenerator;
// Imports the ID generator to create unique log IDs

public class AuditService {

    public static void createLog(
            String action,        // What was done: "REGISTER", "CREATE", "APPROVE"
            String entityType,    // On what entity: "DRIVER", "RIDE", "PASSENGER"
            String description) { // Human-readable detail: "Driver Registered: John"

        AuditLog log = new AuditLog(
                IdGenerator.generateLogId(),   // Generates a random 5-digit log ID
                action,                        // e.g., "APPROVE"
                entityType,                    // e.g., "RIDE"
                description                    // e.g., "Ride Approved: 4523"
        );
        // Creates a new AuditLog object with the above data
        // Inside the AuditLog constructor, createdOn = LocalDateTime.now()
        // is automatically set (timestamp of when this log was created)

        Database.addAuditLog(log);
        // Adds the log object to Database.auditLogs ArrayList
        // This method calls: auditLogs.add(log)
    }

    public static void viewLogs() {

        if (Database.auditLogs.isEmpty()) {
        // Checks if no audit logs exist yet

            System.out.println("\nNo Audit Logs Found!");
            // Error message
            return;
            // Exit method, go back to admin menu
        }

        System.out.println("\n===== AUDIT LOGS =====");
        // Prints header

        for (AuditLog log : Database.auditLogs) {
        // Loops through every AuditLog object in the ArrayList

            System.out.println(log);
            // Calls log.toString() which prints:
            // Log ID, Action, Entity, Description, Created On (timestamp)

            System.out.println("---------------------");
            // Separator line between each log
        }
    }
}
```

### AuditLog Model — How Each Log is Structured

```java
// Inside AuditLog.java:
private int logId;              // Unique ID, e.g., 34521
private String action;          // "REGISTER", "CREATE", "APPROVE"
private String entityType;      // "DRIVER", "RIDE", "PASSENGER"
private String description;     // "Driver Registered: John Doe"
private LocalDateTime createdOn; // Timestamp: 2026-07-03T07:30:15

// Constructor:
public AuditLog(int logId, String action, String entityType, String description) {
    this.logId = logId;
    this.action = action;
    this.entityType = entityType;
    this.description = description;
    this.createdOn = LocalDateTime.now();  // Automatically records current date-time
}

// toString():
@Override
public String toString() {
    return "Log ID : " + logId +
            "\nAction : " + action +           // e.g., "Action : APPROVE"
            "\nEntity : " + entityType +        // e.g., "Entity : RIDE"
            "\nDescription : " + description +  // e.g., "Description : Ride Approved: 4523"
            "\nCreated On : " + createdOn +     // e.g., "Created On : 2026-07-03T07:30:15"
            "\n";
}
```

### Where Audit Logs Are Created (All Trigger Points)

| Action | Entity | Where in Code | Description Example |
|---|---|---|---|
| `REGISTER` | `DRIVER` | `DriverService.registerDriver()` | "Driver Registered: John" |
| `CREATE` | `RIDE` | `DriverService.createRide()` | "Ride Created: 4523" |
| `APPROVE` | `RIDE` | `DriverService.approveRide()` | "Ride Approved: 4523" |

Every time one of these operations happens, `AuditService.createLog()` is called, which creates a new `AuditLog` object and adds it to `Database.auditLogs`.

### Execution Flow Summary

```
1. Admin logs in → adminMenu() shows options
2. Admin picks "5. View Audit Logs" → calls AuditService.viewLogs()
3. Method checks if Database.auditLogs is empty
4. If empty → prints "No Audit Logs Found!" and returns
5. If not empty → prints "===== AUDIT LOGS =====" header
6. Loops through each AuditLog object in the ArrayList
7. For each log → calls toString() which prints Log ID, Action, Entity,
   Description, and Created On timestamp
8. Prints separator "----" after each log
9. Returns to admin menu
```

### Example Console Output

```
===== AUDIT LOGS =====
Log ID : 34521
Action : REGISTER
Entity : DRIVER
Description : Driver Registered: John Doe
Created On : 2026-07-03T07:25:10

---------------------
Log ID : 45678
Action : CREATE
Entity : RIDE
Description : Ride Created: 4523
Created On : 2026-07-03T07:27:45

---------------------
Log ID : 56789
Action : APPROVE
Entity : RIDE
Description : Ride Approved: 4523
Created On : 2026-07-03T07:30:15

---------------------
```

---

## Quick Summary Table

| Story Card | Method | File | What It Does |
|---|---|---|---|
| US06: Approve Ride | `approveRide()` | `DriverService.java` | Lists PENDING rides, admin enters ID, status changes to APPROVED, audit log created |
| US05: View Passengers | `viewPassengers()` | `PassengerService.java` | Loops through `Database.passengers`, prints each passenger's details |
| US07: Audit Trail | `createLog()` + `viewLogs()` | `AuditService.java` | `createLog()` records every operation; `viewLogs()` displays all logs |

