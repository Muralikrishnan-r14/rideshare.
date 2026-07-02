# RideShare — Complete UI Flow Diagram

## 1. Application Entry Point & Main Navigation

```mermaid
flowchart TD
    START(["🚗 RideShare Application"]) --> MAIN_MENU

    MAIN_MENU["===== RIDE SHARE APPLICATION =====\n1. Login\n2. Register Passenger\n3. Register Driver\n4. Exit"]

    MAIN_MENU -->|1| LOGIN_MENU
    MAIN_MENU -->|2| REG_PASS["Passenger Registration Flow"]
    MAIN_MENU -->|3| REG_DRV["Driver Registration Flow"]
    MAIN_MENU -->|4| EXIT(["Thank You! 👋"])

    LOGIN_MENU["===== LOGIN =====\n1. Admin Login\n2. Driver Login\n3. Passenger Login"]

    LOGIN_MENU -->|1| ADMIN_AUTH["Admin Authentication"]
    LOGIN_MENU -->|2| DRIVER_AUTH["Driver Authentication"]
    LOGIN_MENU -->|3| PASSENGER_AUTH["Passenger Authentication"]

    ADMIN_AUTH -->|"username: admin\npassword: admin123"| ADMIN_MENU["Admin Menu"]
    ADMIN_AUTH -->|"Invalid"| ERR_A["❌ Invalid Credentials"] --> MAIN_MENU

    DRIVER_AUTH -->|"email + password match"| DRIVER_MENU["Driver Menu"]
    DRIVER_AUTH -->|"Invalid"| ERR_D["❌ Invalid Credentials"] --> MAIN_MENU

    PASSENGER_AUTH -->|"email + password match"| CHK_STATUS{"Account\nActive?"}
    CHK_STATUS -->|"ACTIVE"| PASSENGER_MENU["Passenger Menu"]
    CHK_STATUS -->|"INACTIVE"| ERR_DEACT["❌ Account is deactivated!"] --> MAIN_MENU
    PASSENGER_AUTH -->|"Invalid"| ERR_P["❌ Invalid Credentials"] --> MAIN_MENU
```

---

## 2. Passenger Registration Flow

```mermaid
flowchart TD
    PR_START["===== PASSENGER REGISTRATION ====="] --> PR_NAME["Enter Name"]
    PR_NAME --> V_NAME{"✅ Valid?\nLetters & spaces\nMin 2 chars"}
    V_NAME -->|No| ERR1["❌ Error message"] --> BACK1(["↩ Return to Main Menu"])
    V_NAME -->|Yes| PR_EMAIL["Enter Email"]

    PR_EMAIL --> V_EMAIL{"✅ Valid format?\nuser@domain.com"}
    V_EMAIL -->|No| ERR2["❌ Invalid email format!"] --> BACK1
    V_EMAIL -->|Yes| V_DUP{"🔍 Duplicate\nemail check?"}

    V_DUP -->|"Exists"| ERR3["❌ A passenger with this email already exists!"] --> BACK1
    V_DUP -->|"Unique"| PR_PASS["Enter Password"]

    PR_PASS --> V_PASS{"✅ Valid?\n≥8 chars, 1 upper,\n1 lower, 1 digit,\n1 special char"}
    V_PASS -->|No| ERR4["❌ Password error"] --> BACK1
    V_PASS -->|Yes| PR_MOB["Enter Mobile"]

    PR_MOB --> V_MOB{"✅ Valid?\nExactly 10 digits"}
    V_MOB -->|No| ERR5["❌ Mobile error"] --> BACK1
    V_MOB -->|Yes| PR_CITY["Enter City"]

    PR_CITY --> V_CITY{"✅ Valid?\nLetters & spaces\nMin 2 chars"}
    V_CITY -->|No| ERR6["❌ City error"] --> BACK1
    V_CITY -->|Yes| PR_SUCCESS["✅ Passenger Registered Successfully!\nPassenger ID: XXXXXXX"] --> BACK1
```

---

## 3. Driver Registration Flow

```mermaid
flowchart TD
    DR_START["===== DRIVER REGISTRATION ====="] --> DR_NAME["Enter Name"]
    DR_NAME --> V1{"✅ Name valid?"}
    V1 -->|No| E1["❌ Error"] --> BACK(["↩ Return to Main Menu"])
    V1 -->|Yes| DR_EMAIL["Enter Email"]

    DR_EMAIL --> V2{"✅ Email valid?"}
    V2 -->|No| E2["❌ Error"] --> BACK
    V2 -->|Yes| V2D{"🔍 Duplicate?"}
    V2D -->|Exists| E2D["❌ Email exists!"] --> BACK
    V2D -->|Unique| DR_PASS["Enter Password"]

    DR_PASS --> V3{"✅ Password valid?\n≥8 chars, mixed"}
    V3 -->|No| E3["❌ Error"] --> BACK
    V3 -->|Yes| DR_MOB["Enter Mobile Number"]

    DR_MOB --> V4{"✅ 10 digits?"}
    V4 -->|No| E4["❌ Error"] --> BACK
    V4 -->|Yes| DR_LIC["Enter License Number"]

    DR_LIC --> V5{"✅ Format: li + 7 digits?\ne.g., li1234567"}
    V5 -->|No| E5["❌ License must start with\n'li' + 7 digits!"] --> BACK
    V5 -->|Yes| DR_MAKE["Enter Vehicle Make"]

    DR_MAKE --> V6{"✅ Letters & spaces?\nMin 2 chars"}
    V6 -->|No| E6["❌ Error"] --> BACK
    V6 -->|Yes| DR_MODEL["Enter Vehicle Model"]

    DR_MODEL --> V7{"✅ Alphanumeric?"}
    V7 -->|No| E7["❌ Error"] --> BACK
    V7 -->|Yes| DR_SEATS["Enter Available Seats"]

    DR_SEATS --> V8{"✅ 1-10?"}
    V8 -->|No| E8["❌ Error"] --> BACK
    V8 -->|Yes| DR_OK["✅ Driver Registered Successfully!\nDriver ID: XXXXXXX\n📝 Audit Log Created"] --> BACK
```

---

## 4. Admin Menu — Full Operations

```mermaid
flowchart TD
    AM["===== ADMIN MENU =====\n1. Approve Rides\n2. View Drivers\n3. View Passengers\n4. Terminate Driver\n5. View Audit Logs\n6. Logout"]

    AM -->|1| AR["===== PENDING RIDES ====="]
    AR --> AR_CHK{"Pending rides\nexist?"}
    AR_CHK -->|No| AR_NONE["No Pending Rides Found"] --> AM
    AR_CHK -->|Yes| AR_LIST["Display all PENDING rides"]
    AR_LIST --> AR_ID["Enter Ride ID to Approve"]
    AR_ID --> AR_FIND{"Ride found?"}
    AR_FIND -->|Yes| AR_OK["✅ Ride Approved Successfully!\nStatus → APPROVED\n📝 Audit Log"] --> AM
    AR_FIND -->|No| AR_NF["❌ Ride Not Found"] --> AM

    AM -->|2| VD["===== DRIVER DETAILS ====="]
    VD --> VD_CHK{"Drivers exist?"}
    VD_CHK -->|No| VD_NONE["No Drivers Found!"] --> AM
    VD_CHK -->|Yes| VD_LIST["Display each driver +\nAverage Rating"] --> AM

    AM -->|3| VP["===== PASSENGER DETAILS ====="]
    VP --> VP_CHK{"Passengers exist?"}
    VP_CHK -->|No| VP_NONE["No Passengers Found!"] --> AM
    VP_CHK -->|Yes| VP_LIST["Display all passengers"] --> AM

    AM -->|4| TD["===== TERMINATE DRIVER ====="]
    TD --> TD_VIEW["View all drivers"]
    TD_VIEW --> TD_ID["Enter Driver ID to Terminate"]
    TD_ID --> TD_FIND{"Driver found?"}
    TD_FIND -->|No| TD_NF["❌ Driver not found!"] --> AM
    TD_FIND -->|Yes| TD_RATE{"Avg rating ≥ 3.0?"}
    TD_RATE -->|Yes| TD_NO["❌ Cannot terminate!\nRating above 3.0"] --> AM
    TD_RATE -->|No| TD_OK["✅ Driver terminated!\nStatus → INACTIVE"] --> AM

    AM -->|5| AL["===== AUDIT LOGS ====="]
    AL --> AL_CHK{"Logs exist?"}
    AL_CHK -->|No| AL_NONE["No Audit Logs Found!"] --> AM
    AL_CHK -->|Yes| AL_LIST["Display all audit logs\nLog ID, Action, Entity,\nDescription, Timestamp"] --> AM

    AM -->|6| LOGOUT(["↩ Logout → Main Menu"])
```

---

## 5. Driver Menu — Full Operations

```mermaid
flowchart TD
    DM["===== DRIVER MENU =====\n1. Create Ride\n2. View My Rides\n3. Edit Ride\n4. Cancel Ride\n5. Update Profile\n6. Logout"]

    DM -->|1| CR["===== CREATE RIDE ====="]
    CR --> CR_SRC["Enter Source"]
    CR_SRC --> CR_DEST["Enter Destination"]
    CR_DEST --> CR_DIST["Enter Distance (KM)"]
    CR_DIST --> CR_FARE["Enter Fare"]
    CR_FARE --> CR_OK["✅ Ride Created Successfully!\nRide ID: XXXX\nStatus: PENDING\n📝 Audit Log\n⏳ Waiting for Admin Approval"] --> DM

    DM -->|2| VMR["===== MY RIDES ====="]
    VMR --> VMR_CHK{"Driver has rides?"}
    VMR_CHK -->|No| VMR_NONE["No Rides Found!"] --> DM
    VMR_CHK -->|Yes| VMR_LIST["Display all driver's rides"] --> DM

    DM -->|3| ER["View My Rides → Select"]
    ER --> ER_ID["Enter Ride ID to Edit"]
    ER_ID --> ER_FIND{"Valid Ride ID &\nbelongs to driver?"}
    ER_FIND -->|No| ER_NF["❌ Invalid Ride ID!"] --> DM
    ER_FIND -->|Yes| ER_EDIT["Enter:\n• New Source\n• New Destination\n• New Distance\n• New Fare"]
    ER_EDIT --> ER_OK["✅ Ride Updated Successfully!"] --> DM

    DM -->|4| CXR["View My Rides → Select"]
    CXR --> CXR_ID["Enter Ride ID to Cancel"]
    CXR_ID --> CXR_FIND{"Valid Ride ID?"}
    CXR_FIND -->|No| CXR_NF["❌ Invalid Ride ID!"] --> DM
    CXR_FIND -->|Yes| CXR_STAT{"Already\ncancelled?"}
    CXR_STAT -->|Yes| CXR_ALR["❌ Ride already cancelled!"] --> DM
    CXR_STAT -->|No| CXR_OK["✅ Ride Cancelled!\nStatus → CANCELLED"] --> DM

    DM -->|5| UP["===== UPDATE PROFILE ====="]
    UP --> UP_FIELDS["Enter:\n• New Name\n• New Mobile\n• New Vehicle Make\n• New Vehicle Model\n• New Available Seats"]
    UP_FIELDS --> UP_OK["✅ Profile Updated Successfully!"] --> DM

    DM -->|6| DLOGOUT(["↩ Logout → Main Menu"])
```

---

## 6. Passenger Menu — Full Operations

```mermaid
flowchart TD
    PM["===== PASSENGER MENU =====\n1. Browse Rides\n2. Book Ride\n3. Booking History\n4. Rate Driver\n5. Update Profile\n6. Deactivate Account\n7. Logout"]

    PM -->|1| BR["===== AVAILABLE RIDES ====="]
    BR --> BR_CHK{"APPROVED &\nACTIVE rides?"}
    BR_CHK -->|No| BR_NONE["No Rides Available"] --> PM
    BR_CHK -->|Yes| BR_LIST["Display available rides"] --> PM

    PM -->|2| BK["Browse Rides → Select"]
    BK --> BK_ID["Enter Ride ID to Book"]
    BK_ID --> BK_SEATS["Enter Number of Seats"]
    BK_SEATS --> BK_FIND{"Valid APPROVED\n& ACTIVE ride?"}
    BK_FIND -->|No| BK_NF["❌ Invalid Ride ID!"] --> PM
    BK_FIND -->|Yes| BK_OK["✅ Ride Booked Successfully!\nBooking ID: XXXXX"] --> PM

    PM -->|3| BH["===== BOOKING HISTORY ====="]
    BH --> BH_CHK{"Has bookings?"}
    BH_CHK -->|No| BH_NONE["No Bookings Found!"] --> PM
    BH_CHK -->|Yes| BH_LIST["Display all bookings:\nBooking ID, Ride ID,\nSeats, Status"] --> PM

    PM -->|4| RD["View Booking History → Select"]
    RD --> RD_ID["Enter Ride ID to Rate"]
    RD_ID --> RD_FIND{"Valid\nRide ID?"}
    RD_FIND -->|No| RD_NF["❌ Invalid Ride ID!"] --> PM
    RD_FIND -->|Yes| RD_VAL["Enter Rating (1 to 5)"]
    RD_VAL --> RD_CHK{"1 ≤ rating ≤ 5?"}
    RD_CHK -->|No| RD_INV["❌ Invalid Rating!"] --> PM
    RD_CHK -->|Yes| RD_OK["✅ Driver Rated Successfully!"] --> PM

    PM -->|5| UP_P["===== UPDATE PASSENGER PROFILE ====="]
    UP_P --> UP_FIELDS["Enter with validation:\n• New Name ✅\n• New Email ✅ + duplicate check\n• New Password ✅\n• New Mobile ✅\n• New City ✅"]
    UP_FIELDS -->|"All valid"| UP_OK["✅ Profile Updated Successfully!\n🕐 modifiedOn timestamp updated"] --> PM
    UP_FIELDS -->|"Any invalid"| UP_ERR["❌ Validation error message"] --> PM

    PM -->|6| DA["Are you sure? (Y/N)"]
    DA -->|Y| DA_OK["✅ Account Deactivated!\nStatus → INACTIVE\n↩ Logout"] --> MAIN(["↩ Main Menu"])
    DA -->|N| DA_CANCEL["Operation Cancelled!"] --> PM

    PM -->|7| PLOGOUT(["↩ Logout → Main Menu"])
```

---

## 7. Complete Role-Based Access Summary

```mermaid
flowchart LR
    subgraph ROLES["🔐 Role-Based Access Control"]
        direction TB
        subgraph ADMIN["👑 Admin"]
            A1["Approve Rides"]
            A2["View Drivers + Ratings"]
            A3["View Passengers"]
            A4["Terminate Driver\n(only if rating < 3.0)"]
            A5["View Audit Logs"]
        end
        subgraph DRIVER["🚗 Driver"]
            D1["Create Ride → PENDING"]
            D2["View My Rides"]
            D3["Edit Ride"]
            D4["Cancel Ride"]
            D5["Update Profile"]
        end
        subgraph PASSENGER["👤 Passenger"]
            P1["Browse Available Rides"]
            P2["Book Ride"]
            P3["View Booking History"]
            P4["Rate Driver (1-5)"]
            P5["Update Profile\n(all fields except ID)"]
            P6["Deactivate Account"]
        end
    end
```

---

## 8. Data Model Relationships

```mermaid
erDiagram
    PASSENGER {
        int passengerId PK
        string passengerName
        string email UK
        string password
        long mobile
        string city
        string registrationStatus
        datetime createdOn
        datetime modifiedOn
    }

    DRIVER {
        int driverId PK
        string driverName
        string email UK
        string password
        long mobile
        string licenseNumber
        string vehicleMake
        string vehicleModel
        int availableSeats
        string registrationStatus
        datetime createdOn
        datetime modifiedOn
    }

    RIDE {
        int rideId PK
        int driverId FK
        string source
        string destination
        datetime departureTime
        int distance
        double fare
        string rideStatus
        string registrationStatus
        datetime createdOn
        datetime modifiedOn
    }

    BOOKING {
        int bookingId PK
        int rideId FK
        int passengerId FK
        int seatsBooked
        string bookingStatus
        datetime createdOn
        datetime modifiedOn
    }

    RATING {
        int ratingId PK
        int driverId FK
        int passengerId FK
        double ratingValue
        datetime createdOn
        datetime modifiedOn
    }

    AUDIT_LOG {
        int logId PK
        string action
        string entityType
        string description
        datetime createdOn
    }

    DRIVER ||--o{ RIDE : "creates"
    RIDE ||--o{ BOOKING : "has"
    PASSENGER ||--o{ BOOKING : "makes"
    PASSENGER ||--o{ RATING : "gives"
    DRIVER ||--o{ RATING : "receives"
```

---

## 9. Validation Rules Summary

| Field | Rules |
|---|---|
| **Name** | Letters & spaces only, min 2 chars |
| **Email** | Valid format `user@domain.com`, must be unique per role |
| **Password** | Min 8 chars, 1 uppercase, 1 lowercase, 1 digit, 1 special char |
| **Mobile** | Exactly 10 digits |
| **City** | Letters & spaces only, min 2 chars |
| **License Number** | Prefix `li` + exactly 7 digits (e.g., `li1234567`) |
| **Vehicle Make** | Letters & spaces only, min 2 chars |
| **Vehicle Model** | Letters, digits & spaces only |
| **Available Seats** | Integer between 1 and 10 |
| **Rating** | Double between 1.0 and 5.0 |
