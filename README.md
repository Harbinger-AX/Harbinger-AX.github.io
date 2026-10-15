# ePortfolio of items from my CS program at SNHU

Code review video is [here](https://youtu.be/_mPQfI58nvY)

# Professional Self-Assessment

## Introduction
I am a computer science student at Southern New Hampshire University, and what I do best is take a program that works on the surface and find where it is broken. I read the code, trace each defect to its cause, fix it, and then run the result to prove it.
My coursework built that habit in three settings. In CS-465 (Full-Stack Development I) I built and documented a full-stack web application, in CS-405 (Secure Coding) I wrote a secure development policy and presented it, and in the capstone I enhanced a Java application in three separate ways.
Building the ePortfolio showed me which of those habits an employer can check for themselves. Each artifact comes with evidence I produced by running the code: a defect traced across files, a query plan showing an index in use, and a SQL injection attempt stored as harmless text. That evidence lets me describe my skills in specific ways.
It also shaped what I want from a job. I am aiming for software engineering work where backend, data, and security responsibilities overlap, because those were the parts of the program where careful verification mattered most. The sections below cover communication, data structures and algorithms, software engineering and databases, and security, drawing mostly on work outside the capstone artifacts.

## Communicating with stakeholders
I adjust the format and level of detail to the person who will read or hear the work. In CS-465 I wrote a full Software Design Document for the Travlr Getaways booking app and a final reflection, covering architecture, functionality, testing, and my own growth. The design document was written for people who need to understand how the system is built, and the reflection for an evaluator who needs to understand what I learned.
In CS-405 I turned my Green Pace Secure Development Policy into a 14-slide presentation with a narrated script. A slide deck cannot carry the detail of a written policy, so I had to decide which parts of the threat matrix and coding standards mattered most when spoken aloud. I also wrote a case study of the 2023 Real Estate Wealth Network data breach, which involved explaining a real incident's causes in plain terms.
In the capstone, each milestone came with a narrative for an evaluator who was not present for the work. I wrote them to explain why a decision was made, including the ones I reversed, such as moving a planned fix to a different layer once the original target no longer existed.

## Data structures and algorithms
I treat an algorithm as something to be checked against its edge cases, not only its typical input. In CS-405 I wrote C++ code that detects numeric overflow and underflow before an arithmetic operation runs, rather than after the wrong value has already been used. Later, I filtered SQL injection tautologies with std::regex, which required deciding exactly which input patterns to reject without blocking legitimate text.
Those exercises are small, but each one forced the same question: what is the cost of this check, and what does it miss? I carried that question into the capstone, where I weighed fetching every row and filtering in Java against filtering in SQL, and chose the second after confirming with a query plan that the database used an index.
What I take from this is a habit of choosing a data structure or algorithm because it fits the problem, then testing it with input designed to break it.
Software engineering and databases
My clearest software engineering experience outside the capstone is CS-465, where I built Travlr Getaways, a travel booking app on the MEAN stack. I worked through how an Angular single-page application and an Express back end divide responsibility, and I documented that architecture in the design document.
Most of what I learned came from debugging. I fixed Angular bugs caused by stray double curly braces, a CORS configuration problem, and mismatched Object IDs. I also traced a MongoDB and Mongoose connectivity failure to a version incompatibility between MongoDB 8.0 and newer and my Linux kernel, which taught me to check the environment before rewriting working code.
That project used a document database, and the capstone used a relational one, so I have designed for both. MongoDB let me store records without a fixed schema, while the capstone's normalized SQL tables enforced one. Knowing why I would pick each is the database skill I most want to bring to a job.

## Security
Security is the area where I have the most coursework, and I treat it as a design requirement rather than a final check. CS-405 took me from individual defects to a full policy. I prevented buffer overflows with std::setw, then wrote a SEI CERT C++ security policy with ten principles and ten coding standards, adding three of my own choosing: MEM52-CPP (memory allocation errors), ERR58-CPP (exceptions thrown before main), and CON50-CPP (mutex lifetime).
For the Green Pace Developer Security Policy I built a threat matrix, set encryption policies, and organized access control around the Triple-A framework of authentication, authorization, and accounting. I also ran Cppcheck and GCC static analysis on a deliberately flawed file, QuestionableCode.cpp, and wrote journals on defense in depth and secure coding practice.
The habit I value most is testing the mitigation against the attack. In the capstone I did not stop at writing parameterized queries; I fed in '; DROP TABLE animals; -- as an animal's name and confirmed it was stored as plain text.

## How my artifacts fit together
The three artifacts are one program, the Grazioso Salvare Rescue Animal System, improved at three layers: its objects, its algorithms, and its storage. The original was a Java console app of four classes with no persistence and code that would not compile, so each layer fixed a different weakness and built on the one before.

### Category: Software design and engineering

##### What I changed:
Made RescueAnimal's fields private, corrected the Monkey constructor, removed dead code, added an IRescueAnimal interface, and validated user input.

##### How I checked it:
Compiled and ran the program, including a bad entry that used to crash it.

### Category: Algorithms and Data Structures

##### What I changed:
Moved the in-service, unreserved filter from Java into one SQL query and added a composite index.

##### How I checked it:
A query plan showed an index search, the output was unchanged, and reservations skipped animals already reserved.

### Category: Databases

##### What I changed:
Designed a normalized SQLite schema of three tables and a data access layer using parameterized queries.

##### How I checked it:
Data persisted across two separate program runs, and an injection attempt was stored as plain text.


# Enhancements Narrative

## Artifact Description
The artifact is the same Grazioso Salvare Rescue Animal System used in the software design and engineering milestone: a Java console application built around a RescueAnimal base class, Dog and Monkey subclasses, and a Driver class that runs the menu-driven interface. It was originally created earlier in the CS program as a basic inheritance exercise. In its original form every animal record lived only in two in-memory ArrayLists inside Driver, meaning any data entered during a session was lost the moment the program exited.

## Justification for Inclusion
I selected this artifact for the databases category because its complete lack of persistence was one of the clearest gaps identified in the original code review. That gap gave me room to design and build an actual persistence layer, rather than making a smaller adjustment to something that already worked.
Several specific components showcase my database design and development skills. First, the schema itself: rather than one flat table with every possible field from both Dog and Monkey, I normalized the design into a base animals table holding the fields every rescue animal shares, with separate dogs and monkeys child tables holding only their type-specific columns, linked back by a foreign key. That mirrors the inheritance relationship already present in the Java object model, translated into purely relational terms. Second, the DAO layer: RescueAnimalDao is the only class in the entire application permitted to contain SQL, and Driver now calls save, findAllDogs, findAllMonkeys, existsByName, and reserveByTypeAndCountry instead of manipulating a list directly. Third, every one of those methods uses a parameterized PreparedStatement instead of concatenating user input into a query string, which is what actually closes off SQL injection as a risk rather than just avoiding it by coincidence.
The artifact was improved in a way I could verify. I ran the enhanced program, added a new dog, and exited; then, in a completely separate process, I started the program again and confirmed the new dog was still there and could be reserved. I also deliberately entered ‘; DROP TABLE animals; --’ as an animal's name during intake to confirm the parameterized queries actually neutralize an injection attempt rather than merely looking correct on paper; the payload was stored as a harmless literal string and the table was untouched.

## Course Outcomes
In my enhancement plan, I identified course outcome four (using well-founded, industry-standard techniques and tools to deliver real value) and course outcome five (a security mindset that anticipates and mitigates vulnerabilities) as the outcomes this enhancement would demonstrate. Working through the schema and the DAO layer, I found this enhancement also demonstrates course outcome three (designing and evaluating a computing solution using algorithmic principles and managing the trade-offs of design choices), which I had not originally scoped this category toward.
I believe I met all three. Outcome four is demonstrated by the DAO pattern and the normalized schema themselves. Outcome five is demonstrated less by the presence of parameterized queries and more by the fact that I tested the specific vulnerability they're meant to prevent, rather than assuming the mitigation worked because I'd read that it should.
Outcome three shows up in three specific design decisions. First, normalizing the schema into a base animals table with dogs and monkeys child tables, rather than one flat table, is a data-structure trade-off in the same sense outcome three describes for a Java collection: it avoids null-heavy columns and duplicated fields at the cost of needing a join on every read. Second, I added indexes on name, animal_type, and acquisition_country, which trades a small amount of extra storage and slightly slower writes for a much faster lookup than a full table scan would give as the table grows. Third, findInServiceAndUnreserved fetches every dog and every monkey through findAllDogs and findAllMonkeys and then filters down to in-service, unreserved animals in Java with removeIf, rather than pushing that filter into the SQL WHERE clause. I chose the simpler, more reusable version over the more efficient one.
One update to my original plan: I had initially expected to also update reserveAnimal's matching logic to check whether an animal was already reserved before reserving it again. I chose to keep reserveByTypeAndCountry's behavior equivalent to the original (reserve the first unreserved match) for this milestone, since that specific logic gap belongs to the algorithms and data structures enhancement rather than the database one, and folding it in here would have mixed which category was demonstrating which fix. 

## Reflection on the Process
The most valuable thing I learned in this process was the gap between code that looks correct and code that has been run. I wrote the DAO's save method using Statement.RETURN_GENERATED_KEYS (which is the standard way to retrieve an auto-incremented id after an insert) and it compiled without any warning. It only failed once I ran it because SQLite's JDBC driver doesn't support that feature the way most other databases do; I had to switch to SQLite's own SELECT last_insert_rowid() instead. That's not a mistake a code review would have caught, because the code was syntactically and logically reasonable. It only surfaces by exercising it against the database engine you're targeting, which is a habit I want to keep going forward rather than trusting that code which compiles cleanly will also run correctly.
A related challenge was getting the JDBC driver working at all in my environment: the driver jar depended on an SLF4J logging library that wasn't installed by default, and failed with a fairly generic ‘no suitable driver found’ error that didn't point at the real cause until I traced it back to a missing class. Diagnosing that — recognizing that a driver registration failure can look identical to a missing driver entirely — was its own small lesson in not trusting the first plausible explanation for an error message.
