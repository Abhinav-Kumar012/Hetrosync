# NoSQL Systems Project: HetroSync

## Problem Statement
This project aims to synchronize data redundantly loaded onto different systems (PostgreSQL, Hive, and MongoDB) by performing operations like SET, GET, and MERGE. The Full problem statement is mentioned in [PS.pdf](./PS.pdf)

## Design of Logs
The oplogs are stored in the following format:
- **Get query**: `get,<student-id>,<course-id>,<timestamp>`
- **Set query**: `set,<student-id>,<course-id>,<grade>,<timestamp>`


## Design
- **GET Operation**: Fetches the grade corresponding to the student-ID and course-ID and records it in the log with the current timestamp.
- **SET Operation**: Modifies the grade and 'last_modified' attribute to the current time of operation and records it in the log with that timestamp.
- **MERGE Operation**: Reads the log of another system from the offset and updates the grade if the timestamp in the log is greater than the 'last_modified' attribute in the datastore.

## Merge Logic
1. Read the log file of the system to merge from, starting from the offset, and consider only the set queries.
2. For every set query, get the 'last_modified' attribute from the datastore.
3. If the data is stale (i.e., the 'last_modified' attribute is earlier than the timestamp in the log file), update the grade and 'last_modified' attributes.
4. Update the log file of the current system with a set query.
5. Update the offset of the other system internally after reading each line of the log.

## ACI Properties
- **Associativity**: `A ∪ (B ∪ C) = (A ∪ B) ∪ C`
- **Commutativity**: The system is not commutative in nature.
- **Idempotency**: `A ∪ A = A`

## Testcase Execution
### Associativity
The testcase demonstrates the associativity property of the system. The output shows that `HIVE.GET(SID1033,CSE016)` returns the same value even after doing the merge in two different ways.

### Commutativity
The testcase demonstrates the non-commutativity property of the system. The output shows that `HIVE.GET(SID1071,CSE011)` and `MONGO.GET(SID1071,CSE011)` do not return the same value.

## Steps to Run
1. Download the project folder on the system.
2. Open the terminal in the root of the project and run `mvn clean install` to build the project.
3. For running the jar file, use:
   - `java -jar target/project-1.0-SNAPSHOT.jar` for interpreter mode.
   - `java -jar target/project-1.0-SNAPSHOT.jar <testcase file>` for testcase mode.

**Note**: We assume the data is redundantly loaded in all the datastores and all of them are running on their default ports.

## Report
The report for this project is mentioned in [Report.pdf](./Report.pdf)