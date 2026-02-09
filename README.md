# 📊 Database Design – BookMyShow Backend

This project uses a **normalized relational database** to manage movies, theatres, shows, seat availability, bookings, and payments.  
Seat availability is tracked **per show** to avoid double booking and handle concurrency.

---

## 🧱 Tables Overview

### 👤 users
Stores registered users of the platform.

- Handles authentication and booking ownership
- One user can create multiple bookings

**Relationship**
- `users → bookings (1:N)`

---

### 🌆 cities
Represents cities where theatres operate.

- Used to filter theatres and movies by location

**Relationship**
- `cities → theatres (1:N)`

---

### 🎭 theatres
Represents physical theatres in a city.

- Each theatre belongs to one city

**Relationship**
- `theatres → cities (N:1)`
- `theatres → screens (1:N)`

---

### 🖥 screens
Represents individual screens inside a theatre.

- Defines physical layout for shows

**Relationship**
- `screens → theatres (N:1)`
- `screens → seats (1:N)`
- `screens → shows (1:N)`

---

### 💺 seats
Defines the static seat layout (A1, A2, etc.).

- Does **not** store availability
- Seat availability depends on the show

**Relationship**
- `seats → show_seats (1:N)`
- `seats → booking_seats (1:N)`

---

### 🎬 movies
Stores movie metadata.

- Same movie can run in multiple theatres and cities

**Relationship**
- `movies → shows (1:N)`

---

### 🎥 shows
Represents a movie running on a specific screen at a specific time.

- Central entity of the booking system

**Relationship**
- `shows → movies (N:1)`
- `shows → screens (N:1)`
- `shows → show_seats (1:N)`
- `shows → bookings (1:N)`

---

### 🪑 show_seats
Tracks seat availability **per show**.

- Supports seat locking and concurrency control
- Status: `AVAILABLE`, `LOCKED`, `BOOKED`

**Relationship**
- `show_seats → shows (N:1)`
- `show_seats → seats (N:1)`

---

### 📦 bookings
Represents a user’s booking attempt.

- Created before payment
- Status: `PENDING`, `CONFIRMED`, `CANCELLED`

**Relationship**
- `bookings → users (N:1)`
- `bookings → shows (N:1)`
- `bookings → booking_seats (1:N)`
- `bookings → payments (1:1)`

---

### 🎟 booking_seats
Maps seats to a booking.

- Supports multi-seat bookings

**Relationship**
- `booking_seats → bookings (N:1)`
- `booking_seats → seats (N:1)`

---

### 💳 payments
Stores payment transaction details.

- Linked only to bookings (not directly to users)

**Relationship**
- `payments → bookings (1:1)`

---

## 🔁 Booking Flow Summary

```text
City → Theatre → Screen → Seat
                 ↓
               Show
                 ↓
          Show_Seats (availability)
                 ↓
             Booking
                 ↓
          Booking_Seats
                 ↓
              Payment
