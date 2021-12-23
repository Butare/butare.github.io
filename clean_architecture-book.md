# The clean Architecture - Book

## Chapter 2: A Tale of two Values
- Every software system provides two different values to the stakeholders: `behavior` and `structure`. 
- Software developers are responsible for ensuring that both those values remain high

### Behavior
- The first value of software is its behavior. Programmers write the code that causes the stakeholder's machines statify the requirements
- Many programmers beleive their job is to machine implement the requirements and to fix any bugs. they're sadly mistaken.


### Architecture
- The second value of software has to do with word "software". the word "ware" means "product", the word "soft" means soft (easily change the behavior of machines)

### The Greater Value
- Function or Architecture? which of these two provides the greater value? is it more important for the software system to work, or is it more important for the software system to be easy to change?
	- if you ask the business managers, they'll often say that it's more important for the software system to work.
	- but Developers should value more the software that is easy to change.
	
    
 ### EISENHOWER'S MATRIX
 1. Urgent and important
 2. Not urgent and important
 3. Urgent and not important
 4. Not urgent and not important

- The first value of software -- `behavior`-- is urgent but not always particularly important (1. & 3.)
- The second value of software -- `architecture` -- is important but never particularly urgent. (1. & 2.)
- The mistake that business managers and developers often make is to elevate items in position 3. to position 1.
- The dilemma for software developers is that business managers are not equipped to evaluate the importance of architecture

### Fight for the Architecture
- If architecture comes last, then the system will become ever more costly to develop, and eventually change will become practically impossible for part or all of the system. If that allowed to happen, it means the software development team did not fight hard enough for what they knew was necessary.


## STARTING WITH THE BRICKS: PROGRAMMING PARADAGIMS

### Computer architecture begins with the code 
- The number of revolutions in programming have occurred. 
	- One revolution with which we're all familiar is the revolution of Programming Languages.
	- Another revolution was in Programming Paradigms.
		- Paradagim are ways of programming, relatively unrelated to languages. A paradigm tells you which programming structure to use, and when to use them.

- To date, there have been 3 paradigms:
1. Structure programming
2. Object-oriented programming
3. Functional programming


## 1. Structured Programming
- Discovered in 1968 by Edsger Dijkstra
- In summary: `Structured programming imposes discipline on direct transfer of control.`

```
if/then/else
do/while/until
```

## 2. Object-oriented programming
- Discovered in 1966, by Ole Johan & Kristen Nygaard
- These two programmers noticed that functional call stack frame in the ALGOL language could be moved to a heap, thereby allowing local variables declared by a function to exist long after the function is returned. The function become a constructor for a class, the local variables became instance variables, and the nested functions became methods.
- This led inevitably to the discovery of polymorphism through the disciplined use of function pointers.

- In summary: `Object-oriented programming imposes discipline on indirect transfer of control.`

## 3. Functional programming
- Adopted recently but invented even before computer programing.
- Functional programming is direct result of the work of Alonzo Church, who in 1936 invented l-calculus
- l-calculus is the foundation of the LISP language, invented in 1958 by John McCarthy.
- The foundation notion of l-calculus is immutability -- that is, the notion that the values of symbols do not change. This effectively means that a functional language has no assignment statement.
- In Summary: `Functional programming imposes discipline upon assignment`

## Conclusion
- These paradigms are directly related to architecture.
- We use `polymorphism (OOP)` as the mechanism to cross architecture boundaries
- We use `functional programming` to impose discipline on the location of - and access to - data
- We use `structured programming` as the algorithmic foundation of our modules.
> Notice how well those three align with the three big concerns of architecture: `function`, `separation of concern`, and `data management`.


# 4. Structured Programming


## Science to the rescue
- `Mathematics` is the discipline of `proving provable statements true`. 
- `Science`, in contrast, is the discipline of `proving provable statements false`.


## Tests
- Testing shows the presence, not the absence, of bugs. In other words, a program can be proven incorrect by a test, but it cannot be proven correct.



# 5. Object-oriented programming

- The basis of a good architecture is the understanding and application of the principles of OO design.
 - What's OO? is `The combination of data and behavior/function` 
 - At least OO language must support these 3 magic words: `Encapsulation`, `Inheritance`, and `Polymorphism` 


# The Power of Polymorphism
- What's so great about polymorphism?
let's consider a copy program in the OS, what happens to that program if a new IO device is created?
	- No thing, we don't change anything.

why?
	because the source code of the copy program does not depend on the source code of the IO drivers.
    `In short, the IO devices have become plugins to the copy program`
    OO allows the plugin architecture to be used anywhere, for anything.
    
# Dependency Inversion
- Before the OOP, the typical calling tree, was: `main functions -> high-level functions -> mid-level functions -> low-level functions`
- But when OOP brought into play, something different can happen, the direction of flow of control can be opposite to the direction of source code dependecy (inheritance relationship). This is called `dependency inversion`. That's the power! That is the power that OO provides. That's what OO is really all about -- at least from the architecture's point of view.

- What can you do with that power?
You can rearrange the source code dependencies of your system so that the database and the user interface (UI) depend on the business rules, rather than the other way around.
	- This means the UI and the DB are plugins to the Business rules. It means the source code of the business rules never mentions the UI or the database. 


# Conclusion
- OO to the software architect, `is the ability, through the use of polymorphism, to gain absolute control over every source code dependency in the system.` 
- it allows architect to create a plugin architecture, in which modules that contain high-level policies are independent of modules that contain low-level details.
- The low-level details are relagated to plugin modules that can be deployed and developed independently from the modules that contain high-level policies.


# 6. FUNCTIONAL PROGRAMMING

## Immutability and Architecture
Why an architect concerned with the mutability of variables?
- All race conditions, deadlock conditions, and concurrent update problems are due to mutable variables.
- As an architect you should be very interested in issues of concurrency.

## Segregation of Mutability
- One of the most common compromises in regard to immutability is to segragate the application, or the services within the application, into mutable and immutable components.

## Event Sourcing
- The more memory we have, and the faster our machines are, the less we need mutable state.
- Even sourcing is a strategy wherin we store the transactions, but not the state. When state is required, we simply apply all the transactions from the beginning of time.
	- Eg. Imagine banking application that maintains the account balances of its customers. It mutates those balances when deposit and withdrawal transactions are executed. Now imagine instead of storing the account balances, we store only the transactions. Whenever anyone wants to know the balances of an account, we simply add up all the transactions for that account for the beginning of time.
	- Inother words, our application are not CRUD; theye're just CR. neither update nor delete operations.

- If we have enough storage and enough processor power, we can make our applications entirely immutable and therefore, entirely functional.
- This is precisely the way the source code control system works.



# III. DESIGN PRINCIPLES

- The SOLID principles tell us how to arrange our functions and data structures into classes, and how those classes should be interconnected.

The goal of the principles is the creation of mid-level software structure that:
- Tolerate change,
- Are easy to understand, and
- are the basis of components that can be used in many software systems.


## 1. SRP: The Single Responsibility Principle
- It doesn't mean that every module should do just one thing. However, make no mistake, there's a principle like that. A function should do one, and only one thing. We use that principle when we're refactoring large functions into smaller functions; we use it at lowest levels. But it is not one of the SOLID principles -- it is not the SRP.

Historical definition:
- `A module should have one, and only one, reason to change`
Software systems are changed to satisfy users and stakeholders; those users & stakeholders are the "reason to change" that the principle is talking about.

We can rephrase the definition:
- `A module should be responsible to one, and only one, user or stakeholder.`
Unfortunately, words like `user` and `stakeholder` aren't really the right words to use here. There will likely be more than one user or stakeholder who wants the system changed in the same way. Instead, we're referring to a group -- one or more people who require that change. we'll refer to that group as an actor.

The final version of definition of SRP:
- `A module should be responsible to one, and only one, actor.`

What's the module?
- `The simplest definition is just a source file`

Let's take a look at the symptoms of violating SRP

### Symptom 1: Accidental Duplication
E.g, `Employee` class from a payroll application. it has 3 methods: `calculatePay()`, `reportHours()`, and `save()`

This class violates the SRP because those three methods are responsible to three very different actors.

1. The `calculatePay()` method is specified by the accounting dept, which reports to the CFO.
2. The `reportHours()` method is specified and used by the human resources dept, which reports to the COO
3. The `save()` method is specified by the database administrators (DBAs), who report to the CTO.

SRP: Says to separate the code that different actors depend on.

### Symptom 2: Merges
- If we have to merge changes on the same source code from different teams that in most cases represent the actors that indicates the violation of SRP

### Solutions
- There are many different solutions to this problem. Each moves the functions into different classes.

- Perhaps most obvious way to solve the problem is to separate the data from the functions. Three classes share access to `EmployeeData`, which is a simple data structure with no methods.

#### Solution 1:

|EmployeeData|
|---|

|PayCalculator|
|---|
| + calculatePay|

|HourReporter|
|---|
| + reportHours|

|EmployeeSaver|
|---|
| + saveEmployee|

Downside of this solution is that developers now have three classes that they have to instantiate and track. A common solution to this dilemma is to use the `Facade pattern`

#### Solution 2
|EmployeeData|
|---|

|EmployeeFacade|
|---|
| + calculatePay|
| + reportHours |
| + save |

|PayCalculator|
|---|
| + calculatePay|

|HourReporter|
|---|
| + reportHours|

|EmployeeSaver|
|---|
| + saveEmployee|

The `EmployeeFacade` contains very little code. It is responsible for instantiating and delegating to the classes with the functions.

Some developers perfer to keep the most important business rules closer to the data. This can be done by keeping the most important method in hte original `Employee` class and then using that class as a Facade for the lesser functions.

#### Solution 3

|Employee|
|---|
| - employeeData|
| + calculatePay|
| + reportHours |
| + save |

|EmployeeSaver|
|---|
| + saveEmployee|

|HourReporter|
|---|
| + reportHours|


## OCP: THE OPEN-CLOSED PRINCIPLE
- A Software artifact should be open for extension but closed for modification.

### A thought experiment
- Imagine, for a moment, that we have a system that displays a financial summary on a web page. The data on the page is scrollable, and negative numbers are rendered in red.
- Now imagine that the stakeholders ask that this same information should be turned into a report to be printed on a black-and-white printer. The report should be properly paginated, with appropriate page headers, page footers, and column labels. Negative numbers should be sorrounded by parentheses.

Clearly, some new source code must be written. But how much old code will have to change?
> A good software architecture would reduce the amount of changed code to the barest minimum. Ideally, zero.

- How? By properly separating the things that change for different reasons (the SRP), and then organizing the dependencies between those things properly (the Dependency Inversion Principle (DIP))

```
financial data --> financial analyzer --> financial report data --> web reporter
					  --> print reporter
                      
```

- An arrow pointing from class A to class B means that the source code of class A mentions the name of class B, but class B mentions nothing about class A.
- `An arrow points to the component that we want to protect from change.` In otherwords, `If component A should be protected from changes in component B, then component B should depend on component A.`

- Protection is based on the notion of "level". The highest-level (e.g: Model) concept should be the most protected. Lowest-level (e.g: Views) should be least protected.

> This is how the OCP works at the architectural level. Architects separate functionality based on how, why, and when it changes, and then organize that separated functionality into a hierarchy of components. Higher-level components in that hierarchy are protected from the changes made to lower-level components.
> 

### Directional Control


### Information Hiding
- We put interface between two entities to protect entities from knowing too much about the internals of the other.
- If no interface, the component would form a `transitive dependencies`
- `Transitive Dependencies` are violation of the general principle that software entities should not depend on things they don't directly use.

### Conclusion
- The OCP is one of the driving forces behind the architecture of systems. 
	- The goal is to make the system easy to extend without incurring a high impact of change.
	- The goal is accomplished by partitioning the system into components, and arranging those components into a dependency hierarchy that protects higher-level components from changes in lower-level components.


# LSP: THE LISKOV SUBSTITUTION PRINCIPLE


## Guiding the use of inheritance
- Imagine that we have a class named `License`. This class has a method named calcFee(), which is called by the `Billing` application. There are two "subtypes" of License; `PersonalLicense` and `BusinessLicense` they use different algorithm to calculate the license fee.

> Billing -->  License [calcFee()]  <-- PersonalLicense, BusinessLicense

This design conforms to the LSP because the behavior of the `Billing` application does not depend, in any way, on which of two subtypes it uses. Both of the subtypes are substitutable for the `License`

## The Square/Rectangle Problem
- The canonical example of a violation of the SRP is the infamous square/rectangle problem.

## Example of LSP Violation



# ISP: The Interface Segregation


# DIP: The Dependency Inversion Principle
- Tells us that the most flexible systms are those in which code dependencies refer only to abstractions, not to concretions
- In a statically typed language, like Java, this means that the `use`, `import`, and `include` statements should refer only to abstraction source modules containing interfaces, abstract classes, or some other kind of abstract declaration. Nothing concrete should be depedended on, except some classes that are very rare and tightly controlled (e.g: `java.lang.String` string is very stable, we can tolerate since we know we can rely on them not to change)
- It is the volatile concrete elements of our system we want to avoid depending on. Those are the modules that we're actively developing, and that are undergoing frequent change.

## Stable Abstractions

- Every change to an abstract interface corresponds to a change to its concrete implementations. Conversely, changes to concrete implementation do not always, or even usually, require changes to the interfaces that they implement. therefore interfaces are less volatile than implementations
- Indeed, good software designers and architects work hard to reduce the valatility of interfaces. They try to find ways to add functionality to implementations without making changes to the interfaces.
- The stable software architectures are those that avoid depending on valatile concretions, and that favor the use of stable abstarct interfaces.
- some coding practices:
	- Don't refer to volatile concrete classes. refer to abstract interfaces instead
	- Don't derive from valatile concrete classes (use inheritance with great care)
	- Don't override concrete functionss. Concrete functions often require source code dependencies. when you override those functions, you don't eliminate those dependencies, you inherit them. To manage those dependencies you should make the function abstract and create multiple implementations.
	- Never mention the name of anything concrete and valatile. This is really just a restatement of the principle itself.

## Factories
- To comply with these rules, the creation of volatile concrete objects requires special handling. This caution is warranted because, in virtually all languages, the creation of an object requires source code dependency on the concrete definition of that object.
- In most object-oriented languages, such as Java, we would use an Abstract factory to manage this undesirable dependency.

```
High-level modules should not depend on low-level modules. Both should depend on the abstraction

i.e; - High-level module -- (uses) --> abstraction
     - Low-level module (or details) -(implements or extends) -> abstraction
     High-level ---> abstraction <- Low-level
```


# IV. COMPONENT Principles

- If SOLID principles tell us how to arrange the bricks into walls and rooms, then the component principles tell us how to arrange the rooms into buildings.
- Large software systems, like buildings, are built of of small components


## Components
- Components are the units of deployment. They're the smallest entities that can be deployed as part of a system. in Java they are `jar` files. In Ruby, they're `gem` files. in .Net, they're DLLs.
- Regardless of how they're eventually deployed, well-designed components always retain the ability to be independently deployable, and therefore, independently developable.

## Component Cohesion
- Which classes belong in which components?

### Three principles of component cohesion
- REP: The Reuse/Release Equavalence Principle
- CCP: The Common Closure Principle
- CRP: The Common Reuse Principle



#### The Reuse/Release Equivalence Principle (REP)
- The granule of reuse is the granule of release
	- The classes & modules that are grouped together into a component should be released together. 


#### The Common Closure Principle (CCP)
- Gather into components those classes that change for the same reasons and at the same times. Separate into different components those classes that change at different times and different reasons. (Thi is like Single Responsibility Principle (SRP) for component)


##### Similarity with SRP
- The SRP tells us to separate methods into different classes, if they change for different reasons. The CCP tells us to separate classes into different components, if they change for different reasons.


#### The Common Reuse Principle (CRP)
- Don't force users of a component to depend on things they don't need.

##### Relation to ISP (Interface Segregation Principle)
- The ISP advises us not to depend on classes that have methods we don't use. The CRP advises us not to depend on components that have classes we don't use. `Don't depend on things you don't need`


#### The Tension Diagram for Component Cohesion


## Component Coupling

### The Acyclic dependencies Principle
- Allow no cycles in the component dependency graph.

- Symptom of a cyclic dependency: change one component affect an other components

- Two solutions to solve this problem: 
	- 1. `The weekly build, ` and 
	- 2. `Acyclic Dependencies Principle (ADP)`

#### Eliminating dependency cycles
- Partition the development environment into releasable components. The components become units of work that can be the responsibility of a single developer, or a team of developers.

##### The effect of a cycle in the component dependency graph
- makes it difficult to isolate components. Unit testing and releaseing become very difficult and error prone.
- In addition, build issues grow geometrically with the number of modules.


#### Breaking The Cycle
- It's always possible to break a cycle of components and reinstate the dependency graph as a DAG (Acyclic Dependencies Principle). There are two primary mechanisms for doing so:
	- Apply the Dependency Inversion Principle (DIP).
	- Create a new component that both classes depend on.move class(es) that they both depend on into that new component.


### Top-Down Design
- The component structure cannot be designed from the top down. It's not one of the first things about the system that is designed, but rather evolves as the system grows and changes.


## The Stable Dependencies Principle
> Depend in the direction of stability

- Any component that we expect to be volatile should not be depended on by a component that is difficult to change. Otherwise, the volatile component will also be difficult to change.

#### Stability
- The stability is related to the amount of work required to make a change.

- The more dependent components a component has the more stability.
	- A change on a component will affect many dependent components, hence stable. 
- The more components a component depends on, the more unstability.
	- A component can be affected by the changes from multiple components, hence unstable. 


### Stability Metrics
- One way to measure the stability of a component, is to count the number of dependencies that enter and leave that component. These counts will allow us to calculate the positional stability of the component.
	- `Fan-in`: Incoming dependencies. This metric identifies the number of classes outside this component that depend on classes within this component.
	- `Fan-out`: Outgoing dependencies. This metric identifies the number of classes inside this component that depend on classes outside the component.
	- `I: Instability:` I = Fan-out/(Fan-in + Fan-out). This metric has the range [0, 1]. 
		- I = 0 indicates a maximally stable component. 
		- I = 1 indicates a maximally unstable component
	- E.g: if Fan-in = 3, Fan-out = 1. then `I = 1/4`.

	- In C++, these dependencies are typically represented by #include statements
	- In Java, the I metric can be calculated by counting the `import` statements and qualified names 
		- The less number of import statement the more stable a component is.


### Not All Components Should Be Stable
- We want to design our component structure so that some components are unstable and some are stable. After all, system should change.

An ideal configuration for a system with three components:
`instable -uses--> stable <--uses- instable`
> I = 0/2 = 0 (maximally stable)

Stability Dependency Principle(SDP) violation
`instable -uses--> stable -uses--> flexible`
- we designed `Flexible` component to be easy to change. we want it to be unstable. However, some developer, working in the component named `Stable` has hung a dependency on `Flexible`. This vilates SDP because the I metric for `stable` (i.e; I= 1/2) is much smaller than the I metric for the `Flexible` (i.e; I = 0/1). As a result, `Flexible` will no longer be easy to change. A change to `Flexible` will force us to deal with `Stable` and all its dependents.
	- To fix this problem, we somehow have to break the dependence of `Stable` on `Flexible`. 

	- Fix:
		- Lets assume that there is a class C within `Flexible` that another class U within `Stable` needs to use
		- we can fix this by employing the DIP (Dependency Inversion Principle);
			1. we create an interface class called `US` and put it in a new component named `UServer`.
			2. We make sure that this interface declares all the methods that `U` needs to use (`U --uses--> US`).
			3. We then make `C` implement this interface (`C --implements-->US`)

		This breaks the dependency of `Stable` on `Flexible`, and forces both components to depend on `UServer`. UServer is very stable (I=0), and Flexible retains its necessary instability(I=1). All the dependencies now flow in the direction of decreasing I.
        
   #### Abstract Components
   You may find it strange that we would create a component -- in the above example, `UService` -- that contains nothing but an interface. Such a component contains no executable code!
   It turns out, however, that this is very common, and necessary tactic when using statically typed languages like Java & C#. 
   These abstract components are very stable and, therefore, are ideal targets for less table components to depend on.
   
   
## The Stable Abstraction Principle
- A component should be as abstract as it is stable.

### Whe do we put the high-level Policy?

Some software in the system should not change very often. This software represents high-level architecture and policy decisions.
we don't want these business and architectural decisions to be volatile. 
- The software that encapsulates the high-level policies of the system should be placed into stable components (I=0). Unstable components (I=1) should contain only the softare that is volatile -- softare that we want to be able to quickly and easily change.
- However, if the high-level policies are placed into stable components, then the source code that represents those policies will be difficult to change. This could make the overall achitecture inflexible.
- How can a component that is maximally stable (I=0) be flexible enough to withstand change?
> The answer is found in OCP (Open-Closed Principle)
- This principle tells us that it is possible and desirable to create classes that are flexible enough to be extended without requiring modification. 
- Which kind of classes conform to this principle?
> An Abstract classes.
> 

### Introducing the Stable Abstractions Principle (SAP)

- The Stable Abstraction Principle (SAP) sets up a relationship between stability and abstractness.
	- On the one hand, it says that a stable component should also be abstract so that its stability doesn't prevent it from being extended.
	- On the other hand, it says that an unstable component should be concrete since its instability allows the concrete code within it to be easily changed.
	- Thus, if a component is to be stable, it should consist of interfaces and abstract classes so that it can be extended.

> Dependencies should run in direction of abstraction
> 

### Measuring Abstraction
- The `A` metric is a measure of the abstractness of a components.
Say;
	- Nc: The number of classes (concrete, abstract & interface) in the component
	- Na: The number of abstract classes and interfaces in the component
	- A: Abstractness. `A = Na / Nc` 

- The `A` metric ranges from 0 to 1. 
	- 0: implies that the component has no abstract classes at all
	- 1: implies that the compoent contains nothing but abstract classes.


### The Main Sequence




# 15. What is Architecture?
- The word `architecture` conjures visions of power and mystery


- what is software architecture? 
- what does a software architect do?
- when does he/she do it?


- A software architect is a programmer, and continues to be a programmer.
	- Never fall for the lie that suggests that software architects pull back from code to focus on higher-level issues. They do not!
- Software architects are the best programmers, and they continue to take programming tasks, while they also guide the rest of the team toward a design that maximizes productivity.


- The primary purpose of architecture is to support the life cycle of the system.
- Good architecture makes the system;
	- Easy to understand,
	- Easy to develop,
	- Easy to maintain, and 
	- Easy to deploy

- The ultimate goal is to minimize the lifetime cost of the system and maximize programmer productivity.


### Development



# INDEPENDENCE
As previously stated, a good software architecture must support:
- The use cases and operation of the system.
- The maintanance of the system
- The development of the system
- The deployment of the system

## Use Cases
- Use cases means that the architecture of the system must support the intent of the system.
- The most important thing a good architecture can do to support behavior is to `clarify and expose the behavior so that the intent of the system is visible at the architectural level.`

## Operation
- If the system must handle 100,000 customers per second, the architecture must support that kind of throughput and response time for each use case that demands it.
- The architecture that maintains the proper isolation of its components, and does not assume the means of communication between those components, will be much easier to transition through the spectrum of threads, processes, and services as the operational needs of the system change over time.

## Development
- Architecture plays a significant role in supporting the development environment.
- This is where Conway's law comes into play.:
> Any organization that designs a system will produce a design whose structure is a copy of the organization's communication structure.
> 

## Deployment


# DECOUPLING LAYERS
- Consider the use cases. The architect wants the structure of the system to support all the necessary use cases, but doesn't know what all those use cases are. However, the architect does know the basic intent of the system. It's a shopping cart system, or it's a bill of materials system, or it's an order processing system. So the architect can employ the Single Responsibility Principle (SRP) and the Common Closure Principle (CCP) to separate those things that change for different reasons, and to collect those things that change for the same reasons -- given the context of the system


# Decoupling USE CASES
- What else changes for different reasons? The use cases themselves!
- The use case for adding an order to an order entry system almost certainly will change at a different rate, and for different reasons, than the use case that deletes an order from the system. 
- Use case are a very natural way to divide the system.

# INDEPENDENT DEVELOP-ABILITY
- When components are strongly decoupled, the interference between teams is mitigated.
- If business rules don't know about the UI, the a team that focuses on the UI cannot much affect a team that focuses on the business rules.
- If the use cases themselves are decoupled from one another, then a team that focuses on the `addOrder` use case is not likely to interfere with a tema that focuses on the `deleteOrder` use case.

# INDEPENDENT DEPLOYABILITY
- The decoupling of the use cases and layers also affords a high degree of flexibility in deployment
- Add a new use case could be as simple as adding a new jar files or services to the system while leaving the rest alone

# DUPLICATION

- Architects often fall into a trap -- a trap that hinges on their fear of duplication.
- Duplication is generally a bad thing in software. We don't like duplicated code. 
- But there are different kinds of duplication. There is true duplication, in which every change to one instance necessitates the same change to every duplicate of that instance.
- If two apparently duplicated sections of code evolve along different paths -- if they change at different rates, and for different reasons -- then they are not true duplicates. Return to them in a few years, and you'll find that they are very different from each other.

# DECOUPLING MODES (AGAIN)
- There are many ways to decouple layers and use cases. They can be decoupled at the source code level, at the binary code (deployment) level, and at the execution unit (service) level.
- `Source level`: We can control the dependencies between source code modules so that changes to one module do not force changes or recompilation of others.
	- This decoupling mode the components all execute in the same address space, and communicate with each other using simple function calls. There is a single executable loaded into computer memory. People often call this a monolithic structure.
- `Deployment level`: We can control dependencies between deployable units such as jar files, DLLs, or shared libraries, so that changes to the source code in one module don't force others to be rebuilt and reployed.
	- Many of the components may still ive in the same address space, and communicate through function calls. Other components may live in other processes in the same processor, and communicate through interprocess communications, sockets, or shared memory
	- The important thing here is that the decoupled components are partitioned into independently deployable units such as jar files, Gem files, or DLLs

- `Service Level`: We can reduce the dependencies down to the level of data structures, and communicate solely through network packets such that every execution unit is entirely independent of source and binary changes to others (e.g; services or micro-services) 

What's the best mode?
- The answer is that it's hard to know which mode is the best during the early stage of a project. Indeed, as the proffffFject matures, the optimal mode may change
- One solution (which seems to be popular at the moment) is to simply decouple at the service level by default. 
	- A problem with this approach is that it is expensive and encourages coarse-grained decoupling.
	- An other problem with service-level decoupling is that it is expensive, both in development time and in system resources. Dealing with service boundaries where none are needed is a waste of effort, memory, and cycles. 

- Recommended way, is to push the decoupling to the point where a service could be formed. should it become necessary; but then to leave the components in the same address space as long as possible. This leaves the option for a service open.
	- With this approach, initially the components are separated at the source code level. That may be good enough for the duration of the project's lifetime. If, however, deployment or development issues arise, driving some of the decoupling to a deployment level may be sufficient -- at least for a while.
	- As the development, deployment, and operation issues increase, we can can carefully choose which deployable units to turn into services, and gradually shift the system in that direction.
	- Over time, the operation needs of the system may decline. What once requried decoupling at the service level may now require only deployment-level or even source-level decoupling
- A good architecture will allow a system to be born as a monolith, deployed in as single file, but then to grow into a set of independently deployable units, and then all the way to independent services and/or micro-services. Later, as things change, it should allow for reversing that progression and sliding all the way back down into a monolith.


# 17. BOUNDARIES: DRAWING LINES
- Software Architectue is the art of drawing lines (aka, boundaries)
- Those boundaries separate software elements from one another, and restrict those on one side from knowing about those on the other.

- Recall that the goal of an architect is to minimize the human resources required to build and maintain the required system.
- What is it that saps this kind of people-power?
	- `Coupling -- and especially coupling to premature decisions.`

- Which kinds of decisions are premature?
	- Decisions that have nothing to do with the business requirements -- the use cases -- of the system. e.g frameworks, databases, web servers, utility libraries, dependency injection,... 

- A good system architecture allows those decisions to be made at the latest possible moment, without significant impact.


## WHICH Lines do you draw, and WHEN do you draw them?
- You draw lines between things that matter and things that don't.
- The GUI doesn't matter to the business rules, so there should be a line between them.
- The database doesn't matter to the GUI, so there should be a line between them
- The database doesn't matter to the business rules, so there should be a line between them.

Some may believe that DB is inextricably connected to the business rules but that's not correct.
- Business rules don't need to know about the schema, or the query language, or any of the other details about the database.
- All the business need to know is that there's a set of functions that can be used to fetch or save data. This allows use to put the DB behind the interface.

```
Business rules -- uses --> Database Interface<I> <|----- Implements-- Database access --- uses --> Database
```
- `Business rules` uses `Database Interface`
- `Databse Access` implements `Database Interface`
- `Database Access` uses `Database`

In real application, there would be many business rule classes, many database interface classes, and many database access implementations

From the above design where is the boundary line?
- The boundary line is drawn across the inheritance relationship, just below the `Database Interface`
- Notice the two arrows leaving the Database Access class. Those two arrows point away from the DatabaseAccess class. Means, that none of these classes knows that the Database Access class exists.

At component level, 
- A component that contains many business rules
- The component that contains the DB and all its access classes
```yaml
Business rules <---- uses --- Database
```
- Note the direction of the arrow. The `Database` knows about the `Business Rules`. The `Business Rules` don't know about the `Database`
- The direction of line shows that the `Database` doesn't matter to the `Business Rules`, but the `Database` cannot exist without the `Business Rules`.
- If that seems strange, just remember this point:

	- The `Database` component contains the code that translates the calls made by the `Business Rules` into the query language of the database. It is that tranlation code that knows about the `Business Rules`

- The Database component could be replaced with many different implementations -- the `Business rules` don't care.

## WHAT ABOUT INPUT AND OUTPUT?
- Developers & Customers often get confused about what the system is. 
	- They see GUI, and think that the GUI is the system.
	- The define a system in terms of the GUI, so they believe that they should see the GUI start working immediately.
	- The fail to realize a critically important principle: `The IO is irrelevant`

- The GUI doesn't matter to the model -- the business rules
- The `GUI` and `Business Rules` are separated by a boundary line

```yaml
Business Rules <--- uses ---- GUI
```
- We can see that `GUI` cares about the `Business Rules`
- But the `Business Rules` don't care about any kind of `GUI`


## PLUGIN ARCHITECTURE
- The software development technology can be seen as how to conveniently create plugins to establish a scalable and maintainable system architecture.
- The core business rules are kept separate from, and independent of, those components that are either optional or that can be implemented in many different forms

```yaml
GUI -- uses --> Business rules <-- uses -- DB
```
- Because the GUI is a plugin, it's possible to plugi many different kinds of user interface UI. such as Web based, client/server based, SOA based, Console based, ...
- The same is true of the DB. we can replace it with SQL database, or a NOSQL database, file system-base DB, ...


## CONLUSION
- To draw boundary lines in a software architecture, you first partition the system into components.
- Some of those components are core business rules, others are plugins that contain necessary functions that are not directly related to the core business.
- The arrange the code in those components such that the arrows between them point in one direction -- toward the core business

- You should recognize this as an application of the Dependency Inversion Principle (DIP) and the Stable Abstraction Principle(SAP).
- The Dependency arrows arranged to point from lower-level details to higher-level abstractions


# 18. BOUNDARY ANATOMY
