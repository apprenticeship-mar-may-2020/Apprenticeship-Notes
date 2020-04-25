##### This was the Hotel booking Kata:
https://katalyst.codurance.com/corporate-hotel-booking

created by Sandro Mancuso

Build a corporate hotel booking engine. This engine has to satisfy the needs of 3 different types of actors:

Hotel Manager: Set all the different types of rooms and respective quantities for her hotel.
Company Admin: Add/delete employees and also create booking policies for her company and employees.
Employee: Book a hotel room
To achieve that, the engine needs to provide 4 main services that work in close collaboration with each other.

The four services are described below. The <?> indicates you can use whatever primitive or type you want.

https://katalyst.codurance.com/corporate-hotel-booking

This was our solution:

https://github.com/apavlidi/HotelBooking/tree/master/src/main/java

I have forked this here:
https://github.com/TomSpencerLondon/HotelBooking

This version from Stephane Meny was great:
https://github.com/TomSpencerLondon/corporate-hotel-booking-kata/blob/master/src/main/java/com/codurance/hotel/Hotel.java
I copied this.

The booking Repository code was the most complex here:
```
package com.codurance.booking;

import com.codurance.hotel.Hotel;
import com.codurance.hotel.room.Room;
import com.codurance.hotel.room.RoomTypeNotAvailableException;

import java.time.LocalDate;
import java.util.*;

public class BookingRepository {

    private final Map<LocalDate, Set<Room>> roomBookingSchedule;

    public BookingRepository() {
        this.roomBookingSchedule = new HashMap<>();
    }

    public void makeBooking(Booking booking, Hotel hotel) {
        Room availableRoom = findAvailableRoom(booking, hotel.getRooms())
                .orElseThrow(RoomTypeNotAvailableException::new);
        bookRoom(booking.getCheckIn(), booking.getCheckOut(), availableRoom);
    }

    private void bookRoom(LocalDate checkIn, LocalDate checkOut, Room availableRoom) {
        for (LocalDate currentDay = checkIn; currentDay.isBefore(checkOut); currentDay = currentDay.plusDays(1)) {
            Set<Room> bookedRooms = roomBookingSchedule.computeIfAbsent(currentDay, k -> new HashSet<>());
            bookedRooms.add(availableRoom);
        }
    }

    private Optional<Room> findAvailableRoom(Booking booking, Set<Room> hotelRooms) {
        if (hotelRooms.isEmpty()) {
            return Optional.empty();
        }

        Set<Room> availableRooms = new HashSet<>(hotelRooms);
        for (LocalDate currentDay = booking.getCheckIn(); currentDay.isBefore(booking.getCheckOut()); currentDay = currentDay.plusDays(1)) {
            Set<Room> bookedRooms = roomBookingSchedule.computeIfAbsent(currentDay, k -> new HashSet<>());
            availableRooms.removeAll(bookedRooms);
        }

        return availableRooms.stream()
                .filter(availableRoom -> availableRoom.getRoomType().equals(booking.getRoomType()))
                .findAny();
    }
}
```

This creates a HashMap for the schedule:
```

    private final Map<LocalDate, Set<Room>> roomBookingSchedule;

    public BookingRepository() {
        this.roomBookingSchedule = new HashMap<>();
    }

```
The makeBooking method and the bookRoom then use this HashMap:
```
    public void makeBooking(Booking booking, Hotel hotel) {
        Room availableRoom = findAvailableRoom(booking, hotel.getRooms())
                .orElseThrow(RoomTypeNotAvailableException::new);
        bookRoom(booking.getCheckIn(), booking.getCheckOut(), availableRoom);
    }

    private void bookRoom(LocalDate checkIn, LocalDate checkOut, Room availableRoom) {
        for (LocalDate currentDay = checkIn; currentDay.isBefore(checkOut); currentDay = currentDay.plusDays(1)) {
            Set<Room> bookedRooms = roomBookingSchedule.computeIfAbsent(currentDay, k -> new HashSet<>());
            bookedRooms.add(availableRoom);
        }
    }
```
This uses the Optional:
```

    private Optional<Room> findAvailableRoom(Booking booking, Set<Room> hotelRooms) {
        if (hotelRooms.isEmpty()) {
            return Optional.empty();
        }

        Set<Room> availableRooms = new HashSet<>(hotelRooms);
        for (LocalDate currentDay = booking.getCheckIn(); currentDay.isBefore(booking.getCheckOut()); currentDay = currentDay.plusDays(1)) {
            Set<Room> bookedRooms = roomBookingSchedule.computeIfAbsent(currentDay, k -> new HashSet<>());
            availableRooms.removeAll(bookedRooms);
        }

        return availableRooms.stream()
                .filter(availableRoom -> availableRoom.getRoomType().equals(booking.getRoomType()))
                .findAny();
    }
```
If the above method returns empty then the makeBooking() method throws an exception:
```
.orElseThrow(RoomTypeNotAvailableException::new);

```
It then calls bookRoom on this result:
```
 bookRoom(booking.getCheckIn(), booking.getCheckOut(), availableRoom);
```

I would definitely like to look over this code with Jose:
```
package com.codurance.booking;

import com.codurance.hotel.Hotel;
import com.codurance.hotel.room.Room;
import com.codurance.hotel.room.RoomTypeNotAvailableException;

import java.time.LocalDate;
import java.util.*;

public class BookingRepository {

    private final Map<LocalDate, Set<Room>> roomBookingSchedule;

    public BookingRepository() {
        this.roomBookingSchedule = new HashMap<>();
    }

    public void makeBooking(Booking booking, Hotel hotel) {
        Room availableRoom = findAvailableRoom(booking, hotel.getRooms())
                .orElseThrow(RoomTypeNotAvailableException::new);
        bookRoom(booking.getCheckIn(), booking.getCheckOut(), availableRoom);
    }

    private void bookRoom(LocalDate checkIn, LocalDate checkOut, Room availableRoom) {
        for (LocalDate currentDay = checkIn; currentDay.isBefore(checkOut); currentDay = currentDay.plusDays(1)) {
            Set<Room> bookedRooms = roomBookingSchedule.computeIfAbsent(currentDay, k -> new HashSet<>());
            bookedRooms.add(availableRoom);
        }
    }

    private Optional<Room> findAvailableRoom(Booking booking, Set<Room> hotelRooms) {
        if (hotelRooms.isEmpty()) {
            return Optional.empty();
        }

        Set<Room> availableRooms = new HashSet<>(hotelRooms);
        for (LocalDate currentDay = booking.getCheckIn(); currentDay.isBefore(booking.getCheckOut()); currentDay = currentDay.plusDays(1)) {
            Set<Room> bookedRooms = roomBookingSchedule.computeIfAbsent(currentDay, k -> new HashSet<>());
            availableRooms.removeAll(bookedRooms);
        }

        return availableRooms.stream()
                .filter(availableRoom -> availableRoom.getRoomType().equals(booking.getRoomType()))
                .findAny();
    }
}
```
This was the test for the BookingService:
https://github.com/TomSpencerLondon/corporate-hotel-booking-kata/blob/master/src/test/java/com/codurance/booking/BookingServiceMust.java