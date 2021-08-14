```ts
// The base publisher class includes subscription management
// code and notification methods.
class EventManager {
    private field listeners: hash map of event types and listeners

    method subscribe(eventType, listener) {
        listeners.add(eventType, listener)
    }

    method unsubscribe(eventType, listener) {
        listeners.remove(eventType, listener)
    }

    method notify(eventType, data) {
        foreach (listener in listeners.of(eventType)) do
            listener.update(data)
    }
}

// The concrete publisher contains real business logic that's
// interesting for some subscribers. We could derive this class
// from the base publisher, but that isn't always possible in
// real life because the concrete publisher might already be a
// subclass. In this case, you can patch the subscription logic
// in with composition, as we did here.
class Editor {
    public field events: EventManager
    private field file: File

    constructor Editor() {
        events = new EventManager()
    }

    // Methods of business logic can notify subscribers about
    // changes.
    method openFile(path) {
        this.file = new File(path)
        events.notify("open", file.name)
    }

    method saveFile() {
        file.write()
        events.notify("save", file.name)
    }

    // ...
}


// Here's the subscriber interface. If your programming language
// supports functional types, you can replace the whole
// subscriber hierarchy with a set of functions.
interface EventListener {
    method update(filename)
}

// Concrete subscribers react to updates issued by the publisher
// they are attached to.
class LoggingListener implements EventListener {
    private field log: File
    private field message

    constructor LoggingListener(log_filename, message) {
        this.log = new File(log_filename)
        this.message = message
    }

    method update(filename) {
        log.write(replace('%s',filename,message))
    }
}

class EmailAlertsListener implements EventListener {
    private field email: string

    constructor EmailAlertsListener(email, message) {
        this.email = email
        this.message = message
    }

    method update(filename) {
        system.email(email, replace('%s',filename,message))
    }
}


// An application can configure publishers and subscribers at
// runtime.
class Application {
    method config() {
        editor = new Editor()

        logger = new LoggingListener(
            "/path/to/log.txt",
            "Someone has opened the file: %s")
        editor.events.subscribe("open", logger)

        emailAlerts = new EmailAlertsListener(
            "admin@example.com",
            "Someone has changed the file: %s")
        editor.events.subscribe("save", emailAlerts)
    }
}
```