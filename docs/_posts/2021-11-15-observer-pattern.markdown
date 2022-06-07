---
layout: post
title:  "Observer Pattern"
date:   2021-11-15 7:52:05 -0400
categories: patterns, apprenticeship
---

How do you get many things to stay consistent with one another without 
coupling them together?

For example: I want three different departments to have a realtime inventory report, 
and each department needs the data displayed differently. Each time an item is 
sold or received, all reports should be updated immediately.

The Observer pattern is one way we can achieve this separation.

### How It Works

Basically, this pattern states that many clients (observers) can "subscribe" to a 
single object (the subject). Any time the Subject changes, the Observers are 
notified and can update themselves based on the subject's new state.

The Subject must defines an interface for the Observers to subscribe or unsubscribe to.
The Subject must also have a mechanism to notify its Observers of changes to its state,
though this doesn't always need to be defined in a public interface.

Observers are simply clients to the Subject. They only need some sort of update
method, which the Subject will use in its notify method.

### An Email Subscription

This pattern works a lot like an email subscription. The newsletter (subject) only 
knows to send itself to a list of subscribers (observers). Subscribers don't know 
anything about other subscribers, who they are, or even how many there are.

Any time a newsletter is sent out, anyone who is subscribed to that newsletter 
receives it in their inbox. It is then up to the subscriber to decide what to do
with it: read, delete, archive, print it, or just leave it in the mass of unread emails.

### JavaScript Event Handling

You may have been using the Observer pattern this whole time without even knowing it!
In JavaScript, any time you want to add functionality to a button, you would just 
add an event listener to it like so:

````javascript
var button = document.getElementById("party-button");
button.addEventListener("click", () => {
    playMusic();
    shootConfetti();
    playLightShow();
});
````

In this case, the party button is the subject and `addEventListener` would be the 
Subscribe method (or `removeEventListener` to Unsubscribe).

The Observers here would just be function references, a lambda in this case, which know
nothing about other Observers.

Any time the button is clicked, it notifies (or executes) all the functions that 
subscribe to it.

### Implementation

In strongly typed languages, we would first need to define interfaces for the
Observer and Subject, though this isn't necessary in dynamically typed languages.

In the Subject, the `notify` method is optional. Subscribers may force the Subject to 
trigger a notification, or this method can be removed to only allow the Subject
to manage notifications.

````java
public interface Observer {
    void update();
}

public interface Subject {
    void subscribe(Observer observer);
    void unsubscribe(Observer observer);
    void notify();
}
````

Next, we would need to implement the Subject. The `subscribe`, `unsubscribe`, and `notify`
methods should look very similar among different implementations.

What's important in the `sell` and `receive` methods is that `notify` is called at the
end of the method, after all changes have been made.

````java
public class Inventory implements Subject {
    Collection<Observer> observers;

    public void subscribe(Observer o) {
        observers.add(o);
    }
    
    public void unsubscribe(Observer o) {
        observers.remove(o);
    }
    
    public void notify() {
        for (Observer o : observers)
            o.update();
    }
    
    public void receive(int itemCode, int count) {
        // ... receive logic ...
        notify();
    }
    
    public void sell(int itemCode, int count) {
        // ... sale logic ...
        notify();
    }
}
````

Now that we have our concrete Subject, `Inventory`, we can create many observers
that subscribe to it. 

````java
public class ManagerInventoryReport implements Observer {
    private Inventory inventory;
    
    public ManagerInventoryReport(Inventory inventory) {
        this.inventory = inventory;
        this.inventory.subscribe(this);
    }
    
    public void update() {
        // Update the live report
    }
    
    public void close() {
        inventory.unsubscribe(this);
    }
}

public class EmployeeInventoryReport implements Observer {
    // Effectively the same thing as ManagerInventoryReport
}

public class AccountantInventoryReport implements Observer { /* ... */ }
````

Depending on your implementation, `subscribe` and `unsubscribe` may be called in one 
place or another. In any case however, your Subject knows nothing about its Observers,
and the Observers know nothing about each other.
