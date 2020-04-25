###### This is the TripService Kata:
https://github.com/sandromancuso/trip-service-kata

Trip Service Kata
Kata for legacy code hands-on session. The objective is to test and refactor the legacy TripService class.

The end result should be well-crafted code that express the domain.

You can watch the video with my solution. Although quite long, I explain my whole thought process while writting tests, how I break dependencies, the reasons for refactoring and re-desining the code (tests and production code), and why certain steps are important. I also cover how often I commit and why I do it.

The video is full of tips and tricks that can be used in any language.

https://www.youtube.com/watch?v=_NnElPO5BU0



https://www.youtube.com/watch?v=_NnElPO5BU0

Trip Service Kata
Kata for legacy code hands-on session. The objective is to test and refactor the legacy TripService class.

The end result should be well-crafted code that express the domain.

You can watch the video with my solution. Although quite long, I explain my whole thought process while writting tests, how I break dependencies, the reasons for refactoring and re-desining the code (tests and production code), and why certain steps are important. I also cover how often I commit and why I do it.

The video is full of tips and tricks that can be used in any language.

https://www.youtube.com/watch?v=_NnElPO5BU0

I have now got up to 10:55 of 1:17:07 for this trip service video . by sandro Mancuso:
https://www.youtube.com/watch?v=_NnElPO5BU0

This is Joe bew's video on Trip Service Kata:
https://www.youtube.com/watch?v=pmoLmjirmTk


These were the tests that we wrote before Ewan worked on the kata:
```
package org.craftedsw.tripservicekata.trip;

import org.craftedsw.tripservicekata.exception.UserNotLoggedInException;
import org.craftedsw.tripservicekata.user.User;
import org.junit.jupiter.api.Assertions;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.when;

@ExtendWith(MockitoExtension.class)
public class TripServiceTest {

  private TripService tripService;
  private User loggedInUser;
  @Mock
  User userToCheck;

  @Test
  void should_throw_an_exception_when_user_is_not_logged_in() {

    tripService = new TestableTripService();
    loggedInUser = null;
    assertThrows(UserNotLoggedInException.class, () -> {
      tripService.getTripsByUser(null);
    });
  }

  @Test
  void return_an_empty_tripList_for_user_with_no_friends() {

    tripService = new TestableTripService();
    loggedInUser = new User();
    ArrayList<Trip> tripList = new ArrayList<Trip>();
    assertEquals(tripList, tripService.getTripsByUser(new User()));
  }

  @Test
  void return_empty_list_if_user_has_friend_not_logged_in_user() {

    tripService = new TestableTripService();
    loggedInUser = new User();
    List<User> userList = Arrays.asList(new User());
    when(userToCheck.getFriends()).thenReturn(userList);

    ArrayList<Trip> tripList = new ArrayList<Trip>();
    assertEquals(tripList, tripService.getTripsByUser(userToCheck));
  }

  @Test
  void return_empty_trip_list_if_friend_but_no_trip() {

    tripService = new TestableTripService();
    loggedInUser = new User();
    List<User> userList = Arrays.asList(loggedInUser);
    when(userToCheck.getFriends()).thenReturn(userList);
    ArrayList<Trip> tripList = new ArrayList<Trip>();

    when(userToCheck.trips()).thenReturn(tripList);

    assertEquals(tripList, tripService.getTripsByUser(userToCheck));
  }

  private class TestableTripService extends TripService {
    @Override
    protected User getLoggedInUser() {
      return loggedInUser;
    }

    @Override
    protected List<Trip> getTrips(User user){
      return userToCheck.trips();
    }
  }
}
```
For this first part of the tests we added a TestableTripService class to override the getLoggedInUser() method and the getTrips(User user) methods.

These two methods allows us to mock the:
```
UserSession.getInstance().getLoggedUser()
```
and:
```
TripDAO.findTripsByUser(user);
```
through our test methods. This is what our code for this looked like (before refactoring):
```

public class TripService {

	public List<Trip> getTripsByUser(User user) throws UserNotLoggedInException {
		List<Trip> tripList = new ArrayList<Trip>();
    User loggedUser = getLoggedInUser();
    boolean isFriend = false;
		if (loggedUser != null) {
			for (User friend : user.getFriends()) {
				if (friend.equals(loggedUser)) {
					isFriend = true;
					break;
				}
			}
			if (isFriend) {
        tripList = getTrips(user);
      }
			return tripList;
		} else {
			throw new UserNotLoggedInException();
		}
	}

  protected List<Trip> getTrips(User user) {
    return TripDAO.findTripsByUser(user);
  }

  protected User getLoggedInUser() {
    return UserSession.getInstance().getLoggedUser();
  }

}
```
Here the getTripsByUser(User user) gets an ArrayList<Trip>() and then reassigns the tripList if a friend in the user.getFriends is the same as the loggedUser. We went on to refactor this in a few ways. We reduced the getTripsByUser method to a tenary statement and extracted the protected methods to Interfaces of the TripDAO and UserSession respectively. This enabled us to use @Mock and inject these dependencies into the TripService class.

This is what we had before lunch:
```
package org.craftedsw.tripservicekata.trip;

import org.craftedsw.tripservicekata.exception.UserNotLoggedInException;
import org.craftedsw.tripservicekata.user.SessionInterface;
import org.craftedsw.tripservicekata.user.User;

import java.util.Collections;
import java.util.List;

public class TripService {

	private TripDAO tripDAOInterface;

	public TripService(TripDAO tripDAOInterface) {
		this.tripDAOInterface = tripDAOInterface;
	}

	public List<Trip> getTripsByUser(User user, User loggedUser) throws UserNotLoggedInException {
		if (loggedUser == null) {
			throw new UserNotLoggedInException();
		}

        return areFriends(user, loggedUser) ?
				getTrips(user) :
				Collections.emptyList();
    }

	private boolean areFriends(User user, User loggedUser) {
		return user.getFriends().contains(loggedUser);
	}

	private List<Trip> getTrips(User user) {
        return tripDAOInterface.instanceFindTripsByUser(user);
    }

}

```
This involved using an arFriends method to check if the User user was friends with the User loggedUser. This enabled us to delete the getFriends() loop. Next we extracted the friendsWith method to the User:
```
package org.craftedsw.tripservicekata.trip;

import org.craftedsw.tripservicekata.exception.UserNotLoggedInException;
import org.craftedsw.tripservicekata.user.User;

import java.util.Collections;
import java.util.List;

public class TripService {

	private TripDAO tripDAOInterface;

	public TripService(TripDAO tripDAOInterface) {
		this.tripDAOInterface = tripDAOInterface;
	}

	public List<Trip> getTripsByUser(User user, User loggedUser) throws UserNotLoggedInException {
		if (loggedUser == null) {
			throw new UserNotLoggedInException();
		}

        return user.isFriendsWith(loggedUser) ?
				getTrips(user) :
				Collections.emptyList();
    }

	private List<Trip> getTrips(User user) {
        return tripDAOInterface.instanceFindTripsByUser(user);
    }

}
```
This simplified the ternary even further.

The other code which Sandro included in the test was:
```
UserBuilder.aUser()
   .friendsWith(ANOTHER_USER, loggedInUser)
   .withTrips(TO_BRAZIL, TO_LONDON)
   .build()
```
The start of this builder was to create UserBuilder:
```
public static class UserBuilder{
 public static UserBuilder aUser() {
  return null;
 }
}
```

This is what the final builder looks like:
```
public static class UserBuilder {
 private User[] friends = new User[] {};
 private Trip[] trips = new Trip[]{};

 public static UserBuilder aUser(){
  return new UserBuilder();
 }

 public UserBuilder withTrips(Trip... trips) {
  this.trips = trips;
  return this;
 }

 public UserBuilder friendsWith(User... friends) {
  this.friends = friends;
  return this;
 }

 public User build() {
  User user = new User();
  addTripsTo(user);
  addFriendsTo(user);
  return user;
 }
}
```

This is Sandro Mancuso's article on the TripService class:
https://codurance.com/2011/07/16/testing-legacy-hard-wired-dependencies/

This is Sandro Mancuso's video on TripService kata:
https://www.youtube.com/watch?v=_NnElPO5BU0