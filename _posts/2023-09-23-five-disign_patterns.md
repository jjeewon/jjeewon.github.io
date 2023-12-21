---
layout: post
title:  "Top 5 Design Patterns for Efficient App Development"
author: ella
categories: [ Android ]
image: assets/images/android_diffutil.png
---
🤖  
#  Design Patterns for Easy Maintenance, Flexibility, and Reusability in Apps

## 1. Singleton Pattern

The Singleton Pattern ensures that a class has only one instance and provides a global point of access to it. This pattern is useful for:

- **Analytics**: Ideal for analytics where only one instance is needed to maintain consistency.
- **Database, Use Cases, APIs**: Singleton can be used for database connections, API clients, or specific use cases where a single shared resource is preferable.
- **Simplicity Over Singleton**: For simple utilities, instead of creating a singleton class, top-level functions can be used for a cleaner approach.

## 2. Mapper Pattern

The Mapper Pattern allows different interfaces or classes to work together, adapting one to another's interface. It's useful for:

- **RecyclerView.Adapter**: Adapts a data set for display in a RecyclerView, transforming the data to fit the UI requirements.
- **API Wrapping**: Simplifies complex APIs by wrapping them into a more straightforward interface.

    ```kotlin
    // Example: Converting exceptions to a common error response model
    val Throwable.errorResponse: ErrorResponseModel
        get() = when (this) {
            is UnknownHostException -> NetworkFailException
            else -> ErrorResponseModel(error = this.message, message = this.localizedMessage)
        }
    ```

- **Layer-to-Layer Model Mapping**: Transforms models from one layer to another in the app architecture, ensuring each layer communicates effectively without direct dependency.

    ```kotlin
    // Example: Mapping domain model to presentation model
    class AutoCompleteDomainToPresentationMapper {
        fun toPresentation(input: LocationAutoCompleteDomainModel) = AutoCompletePresentationModel(
            list = input.locationAutoCompleteList.map {
                val location = "${it.name}, ${it.country}"
                LocationPresentationModel(
                    location = location,
                    lat = it.lat,
                    lng = it.lng,
                )
            }
        )
    }
    ```

## 3. Strategy Pattern

The Strategy Pattern enables changing the execution of an algorithm dynamically during runtime. It separates the algorithm from the objects that use it, allowing for flexibility and easy changes without modifying existing code.

- **Example Use Case**: Adapting data retrieval strategy dynamically.

    ```kotlin
    // Example: Selecting data retrieval strategy at runtime
    enum class Strategy {
        OFFLINE_FIRST,
        ONLINE_FIRST,
        OFFLINE_ONLY,
        ONLINE_ONLY,
    }

    class WeatherDataSourceImpl(
        private val weatherApi: WeatherApi,
        private val weatherInMemoryDataBase: InMemoryDatabase,
        private val weatherToDataMapper: WeatherToDataMapper,
        private val futureWeatherToDataMapper: FutureWeatherToDataMapper,
        private val locationAutoCompleteToDataMapper: LocationAutoCompleteToDataMapper
    ): WeatherDataSource {
        override suspend fun getWeather(q: String): WeatherDataModel {
            val data = when (strategy) {
                Strategy.OFFLINE_FIRST -> {
                    weatherInMemoryDataBase.getWeather(q)
                    weatherApi.getWeather(q)
                }
                Strategy.ONLINE_FIRST -> {
                    weatherApi.getWeather(q)
                    weatherInMemoryDataBase.getWeather(q)                  
                }
                Strategy.OFFLINE_ONLY -> {              
                    weatherInMemoryDataBase.getWeather(q)                  
                }
                Strategy.ONLINE_ONLY -> {
                    weatherApi.getWeather(q)                       
                }
            }
            return weatherToDataMapper.toData(data)
        }
    }
    ```

## 4. Observer Pattern

The Observer Pattern is a design pattern where an object, known as the subject, maintains a list of its dependents, called observers, and notifies them automatically of any state changes, usually by calling one of their methods. It is primarily used to implement distributed event handling systems.

### Core Components of the Observer Pattern:

1. **Subject**: The entity that holds the state and notifies observers about changes. It maintains a list of observers and provides mechanisms to add or remove them.
2. **Observer**: An interface or abstract class defining the `update` method that gets called when the Subject's state changes.
3. **Concrete Observer**: An implementation of the Observer, which gets notified by the Subject on changes.
4. **Client**: The part of code that creates Subjects and Observers and registers Observers with Subjects.

### Key Features:

- **Loose Coupling**: The subject doesn't need to know anything about the observers, promoting loose coupling.
- **Broadcast Communication**: Subject sends broadcast notifications to all registered observers without concerning itself with their specifics.
- **Dynamic Relationships**: Observers can be added or removed dynamically at runtime.

### Kotlin Implementation Example:

Below is a simple implementation of the Observer Pattern in Kotlin, demonstrating a weather update system:

```kotlin
// Observer interface with an update method
interface Observer {
    fun update(weather: String)
}

// Subject class
class WeatherRepository {
    private val observers = mutableListOf<Observer>()

    // Method to add an observer
    fun addObserver(observer: Observer) {
        observers.add(observer)
    }

    // Method to remove an observer
    fun removeObserver(observer: Observer) {
        observers.remove(observer)
    }

    // Notify all observers about the weather change
    fun notifyObservers(weather: String) {
        observers.forEach { it.update(weather) }
    }

    // Example method to simulate weather change
    fun changeWeather(weather: String) {
        notifyObservers(weather)
    }
}

// Concrete Observer
class WeatherObserver(private val name: String) : Observer {
    override fun update(weather: String) {
        println("$name received weather update: $weather")
    }
}

// Main function to demonstrate usage
fun main() {
    val weatherRepository = WeatherRepository()  // Create the subject
    val observer1 = WeatherObserver("Observer 1")  // Create observers
    val observer2 = WeatherObserver("Observer 2")

    weatherRepository.addObserver(observer1)  // Register observers
    weatherRepository.addObserver(observer2)

    weatherRepository.changeWeather("Sunny")  // Change weather and notify observers
    weatherRepository.changeWeather("Rainy")
}
```

This pattern is widely used in GUI applications, event handling systems, and for implementing broadcast communication. It allows for a scalable and flexible way to handle state changes in one object affecting other dependent objects.

## 5. Command Pattern

The Command Pattern is a design pattern that encapsulates a request as an object, thereby allowing for parameterization of clients with queues, requests, and operations. It enables the separation of concerns between the sender of a request (the invoker) and the receiver of the request (the object executing the command). This pattern is particularly useful for implementing features like undo/redo operations and for managing transactions and queue-based tasks.

### Core Components of the Command Pattern:

1. **Command Interface**: Defines a standard interface for commands, typically with an `execute` method.
2. **Concrete Command**: Implements the Command interface and specifies the action to perform.
3. **Receiver**: The object that knows how to perform the operations associated with carrying out a request.
4. **Invoker**: The object that knows how to execute a command but not how the command has been implemented. It can queue, log, and undo commands.
5. **Client**: Creates Concrete Commands and sets their receiver.

### Basic Implementation in Kotlin:

Here's a basic implementation of the Command Pattern:

```kotlin
// Command interface
interface Command {
    fun execute()
}

// Concrete Commands that encapsulate a request
class ConcreteCommandA(private val receiver: Receiver) : Command {
    override fun execute() {
        receiver.actionA()  // Delegates the action to the receiver
    }
}

class ConcreteCommandB(private val receiver: Receiver) : Command {
    override fun execute() {
        receiver.actionB()  // Delegates the action to the receiver
    }
}

// Receiver class
class Receiver {
    fun actionA() {
        // Implementation of operation A
    }

    fun actionB() {
        // Implementation of operation B
    }
}

// Invoker class
class Invoker {
    private val commandQueue = ArrayDeque<Command>()

    // Adds commands to the queue
    fun addCommand(command: Command) {
        commandQueue.offer(command)
    }

    // Executes commands in the queue
    fun executeCommands() {
        while (commandQueue.isNotEmpty()) {
            commandQueue.poll().execute()
        }
    }
}

// Main function to demonstrate usage
fun main() {
    val receiver = Receiver()  // Create the receiver
    val commandA = ConcreteCommandA(receiver)  // Create commands
    val commandB = ConcreteCommandB(receiver)
    val invoker = Invoker()  // Create the invoker
    invoker.addCommand(commandA)  // Add commands to the invoker
    invoker.addCommand(commandB)
    invoker.executeCommands()  // Execute commands
}
```

### Advanced Usage:
Undo/Redo Functionality:

The Command Pattern is ideal for implementing undo/redo features, as commands can encapsulate state required for undo operations.

```kotlin
// Extended Command interface with undo functionality
interface Command {
    fun execute()
    fun undo()
}

// Concrete Command with an undo action
class ActionCommand(private val receiver: Receiver, private val action: () -> Unit) : Command {
    override fun execute() {
        action()
    }

    override fun undo() {
        // Implement undo logic here
        receiver.undoAction()
    }
}

// Receiver to maintain the state and history of commands
class Receiver {
    private val history = mutableListOf<Command>()

    fun execute(command: Command) {
        command.execute()
        history.add(command)
    }

    fun undoAction() {
        if (history.isNotEmpty()) {
            val lastCommand = history.removeAt(history.size - 1)
            lastCommand.undo()
        }
    }
}

// Client code to use Undo/Redo functionality
val receiver = Receiver()
val invoker = Invoker(receiver)
val actionCommand = ActionCommand(receiver) {
    // Perform an action
}
invoker.execute(actionCommand)
invoker.undo()

```

### Background Tasks and Queue Management:

For managing background tasks and queues, the Command Pattern can be adapted to handle asynchronous operations.

```kotlin
import kotlinx.coroutines.Dispatchers
import kotlinx.coroutines.GlobalScope
import kotlinx.coroutines.launch

// Command interface for background tasks
interface BackgroundCommand {
    suspend fun execute()
}

// Concrete Command for a background task
class BackgroundTaskCommand(private val task: suspend () -> Unit) : BackgroundCommand {
    override suspend fun execute() {
        task()
    }
}

// CommandInvoker for background tasks
class BackgroundTaskInvoker {
    fun execute(command: BackgroundCommand) {
        GlobalScope.launch(Dispatchers.IO) {
            command.execute()
        }
    }
}

// Client code to perform a background task
val backgroundTaskInvoker = BackgroundTaskInvoker()
val backgroundCommand = BackgroundTaskCommand {
    // Perform a background task
}
backgroundTaskInvoker.execute(backgroundCommand)
```
