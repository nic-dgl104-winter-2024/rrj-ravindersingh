# Community Code Project(Part 1)
## Overview
The initial issue involved a community member utilizing the Litjson library for data deserialization while attempting to handle null values. I dedicated time to resolving this matter, successfully implementing a solution and rigorously testing it on my own PC. The outcome was a flawless execution.

In the second scenario, a fellow coder encountered difficulties updating records in SQLite using the Insert or Update query. Fortunately, I discovered a solution outlined in an article, and after validating it on an online compiler, confirmed its effectiveness in resolving the issue.

The third problem was about someone having trouble putting deserialized data into an array of objects. Even though I didn't fully understand the problem at first, I asked for clarification and got it from the person. While I didn't work on fixing this issue directly, I did get some ideas on how it could be solved.
## Issue 1
## Project Name: LitJson
The LitJSON community code project is an open-source initiative aimed at developing and maintaining a lightweight and efficient JSON library for the .NET platform. Built upon the principles of simplicity, performance, and ease of use, LitJSON provides developers with a robust toolset for working with JSON data in their .NET applications.
### Key Feaures
* Simplifies JSON data handling: LitJSON simplifies the process of parsing and serializing JSON data, allowing developers to focus on building robust applications.
* Improves performance: With its lightweight design and optimized performance, LitJSON ensures fast and efficient JSON processing, even with large datasets.
* Enhances productivity: By providing a user-friendly API and comprehensive documentation, LitJSON helps developers save time and effort during the development process.
### Link
https://github.com/Ravi-takhi/litjson/tree/Ravi-Dev-Branch
### Summary of issues examined
This [issue](https://github.com/LitJSON/litjson/issues/121), raised by corbinyo, to find a way to handle null values during deserialization. The task involved ensuring that null values from JSON data were ignored when converting it into C# objects. He is using the Litjson library for the serialization and deserialization.
### Detailed discussion of issues contributed to
I worked on a specific issue related to ignoring null values during deserialization with LitJSON. My goal was to prevent null values in JSON from being assigned to object properties during the conversion process. I explored different methods, including manually checking for null values, creating custom importers, and modifying the JSON data before deserialization. Ultimately, I found a solution by adjusting the JSON string before parsing it into objects, effectively solving the problem even though LitJSON doesn't have a built-in feature for this. I also looked into the other libraries such as Newtonsoft, they provides some in-built functionalities to ingnore or skip the null values during the serialization data.
#### Example for skip null value while deserialization
``` csharp
using LitJson;
using System;

public class Student
{
    public int Id { get; set; }
    public string Name { get; set; }
    public int? Age { get; set; } // Nullable int for handling null values
}

class Program
{
    static void Main(string[] args)
    {
        string json = "{\"Id\": 1, \"Name\": \"John\", \"Age\": null }"; // JSON string with null value for Age

        JsonData jsonData = JsonMapper.ToObject(json);

        Student std = new Student();

        if (jsonData["Id"] != null)
            std.Id = (int)jsonData["Id"];

        if (jsonData["Name"] != null)
            std.Name = (string)jsonData["Name"];

        // Handle null value for Age
        if (jsonData["Age"] != null)
        {
            if (jsonData["Age"].IsInt) // Check if the value is an integer
                std.Age = (int)jsonData["Age"];
            else
                std.Age = null;
        }

        Console.WriteLine($"Id: {std.Id}, Name: {std.Name}, Age: {(std.Age.HasValue ? std.Age.ToString() : "N/A")}");
    }
}
```
### Code review and outcomes
#### Code review
The initial version of the program effectively handles null values during JSON deserialization into a Student object. However, it could be improved for better readability and maintainability. The improved version introduces a method called RemoveNullValues to handle null values more efficiently. By encapsulating the null value removal logic, the code becomes simpler and easier to maintain. The updated version of the code is definitely a step forward. In the old version, fetching values from the JSON data required mentioning variable names explicitly. This could be troublesome if we add more variables to the Student class later on. However, the new version has a neat function called RemoveNullValues that takes care of this. Now, we don't need to worry about manually handling each variable. This makes the code simpler and more flexible, which is great for maintaining and modifying the Student class in the future. Additionally, a proposed enhancement suggests introducing a method named DeserializeNonNullValues to further streamline the deserialization process. Overall, these changes enhance the readability, maintainability, and robustness of the code, ensuring it adheres to best practices in software development.
#### Outcome
Upon receiving feedback from Katrina, I carefully considered her suggestions and realized the importance of simplifying and enhancing the code. By implementing the improvements suggested by Katrina, I was able to make the code more readable, maintainable, and flexible. This highlights the value of feedback in driving positive changes and improving overall code quality.
### Updated version
``` csharp
using LitJson;
using System;

public class Student
{
    public int Id { get; set; }
    public string Name { get; set; }
    public int? Age { get; set; } // Nullable int for handling null values

    // Constructor to initialize properties
    public Student(int id, string name, int? age)
    {
        Id = id;
        Name = name;
        Age = age;
    }
}

class Program
{
    static void Main(string[] args)
    {
        string json = "{\"Id\": 1, \"Name\": \"John\", \"Age\": null }"; // JSON string with null value for Age

        Student std = DeserializeStudent(json);

        Console.WriteLine($"Id: {std.Id}, Name: {std.Name}, Age: {(std.Age.HasValue ? std.Age.ToString() : "N/A")}");
    }

    static Student DeserializeStudent(string json)
    {
        JsonData jsonData = JsonMapper.ToObject(json);

        int id = (int)jsonData["Id"]; // Directly cast to int
        string name = (string)jsonData["Name"]; // Directly cast to string

        int? age = null; // Initialize age to null by default

        // Handle null value for Age
        if (jsonData.Keys.Contains("Age") && jsonData["Age"] != null)
        {
            if (jsonData["Age"].IsInt) // Check if the value is an integer
                age = (int)jsonData["Age"];
        }

        return new Student(id, name, age);
    }
}
```
### Prerequisites:
* Install .NET SDK: Ensure that you have the .NET SDK installed on your machine. 
* Install LitJSON: You need to have LitJSON installed in your project. You can add it via NuGet Package Manager or by running the following command in the terminal within your project directory:
``` csharp
dotnet add package LitJson
```
#### Steps to Run the Code:
* Setup Visual Studio Code.
* Create a New Project.
* Create Files.
* Restore Dependencies: Open the terminal in Visual Studio Code and navigate to your project folder. Run the following command to restore dependencies:
``` csharp
dotnet restore
```
### Reflection
Reflecting on the success of solving the issue, I did manage to fix it in the end. But it wasn't a straightforward journey. I spent a lot of time going through the LitJSON documentation, hoping to find an easy way to skip null values during data reading. Unfortunately, I couldn't find any built-in solution in LitJSON or similar libraries. I even searched online forums like Stack Overflow, but no luck there either. Eventually, I realized I could manually check the data after reading it, which turned out to be the solution. However, when testing the code in Visual Studio, I ran into some errors because I hadn't installed the LitJSON package properly. I had to watch some YouTube tutorials to figure it out. Despite the challenges, I eventually got it all working.
### Discussion of next steps
To enhance the code, I plan to create a generic function where I can pass values dynamically. My research led me to Text RegularExpressions Regex, which could be useful for creating this generic class. I'll need to learn more about Regex and how to implement it in my project. Once I've replicated the solution in my code, I'll test it with different types of JSON data and ensure proper error handling. Finally, I'll make the solution publicly available so others can benefit from it.

# Issue 2
## Project Name: queryBuilder
SqlKata Query Builder is a powerful Sql Query Builder written in C#. It's secure and framework agnostic. Inspired by the top Query Builders available, like Laravel Query Builder and Knex.SqlKata has an expressive API. it follows a clean naming convention, which is very similar to the SQL syntax. By providing a level of abstraction over the supported database engines, that allows you to work with multiple databases with the same unified API. SqlKata supports complex queries, such as nested conditions, selection from SubQuery, filtering over SubQueries, Conditional Statements and others. Currently, it has built-in compilers for SqlServer, MySql, PostgreSQL, and Firebird.

The full documentation is avaiable on https://sqlkata.com
### Link
https://github.com/Ravi-takhi/Ravi-dev-queryBuilder/tree/Ravi-dev-branch
### Summary of issues examined
This [issue](https://github.com/sqlkata/querybuilder/issues/641), raised by H0nok4 on Nov 17, 2022, The person is having trouble with doing an "insert or replace" action in SQLite. They're finding it tricky to insert new data or replace existing data in the table at the same time. They're looking for a straightforward way to solve this problem in SQLite.
### Detailed discussion of issues contributed to
The person is asking about how to handle inserting or updating data in their SQLite database. They want to update the data if a key already exists. After checking the documentation for SqlKata and finding no solution, they looked into SQLite's documentation and found some helpful information. I refer to the SQLite language documentation available at: https://www.sqlite.org/lang.html
### Solution
#### Introduction to the SQLite REPLACE statement
The REPLACE statement works by executing two steps when a UNIQUE or PRIMARY KEY constraint violation happens:
* Initially, it removes the existing row that triggers the constraint violation.
* Then, it proceeds to insert a new row in its place.
#### Syntax of the REPLACE statement
``` sql
INSERT OR REPLACE INTO table(column_list)
VALUES(value_list);
```
#### Example of INSERT OR REPLACE students data
1. First, assuming you have a table named "students" with columns "id" (primary key), "name", and "age".
``` sql
CREATE TABLE IF NOT EXISTS students (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    age INTEGER
);
```
2. Then, you can use the following SQL statement to update or insert student records
``` sql
INSERT OR REPLACE INTO students (id, name, age)
VALUES 
    (1, 'Ravi', 20),  -- Assuming student with id 1 already exists, this will update Ravi's record
    (2, 'Kuljit', 22), -- Assuming student with id 2 doesn't exist, this will insert a new record for Kuljit
    (3, 'Monika', 21); -- Assuming student with id 3 doesn't exist, this will insert a new record for Monika
```
### Code review and outcomes
#### Code review
Kuljit has reviwed my code and shared some feedback points for making a table called "students" and putting in new information or updating existing information. She thought the way I made the table was good, with rules to keep the data organized. Using "INSERT OR REPLACE INTO" was smart because it either adds new info or changes old info if it's already there. The comments you wrote next to each new piece of information helped to understand what's going on. However, She suggested adding another part to the code before updating the records. This part would put some initial information into the table right after creating it. Doing this would help people see how the table works and how the updates happen. Kuljit also thought the code was easy to understand overall.
#### Outcome
After Kuljit's review of my code for creating and updating the "students" table, it's clear that the initial structure and approach were well-received. The thoughtful organization of the table and the use of "INSERT OR REPLACE INTO" for updating records were noted as smart choices. Additionally, the inclusion of explanatory comments alongside each piece of data was appreciated for enhancing clarity.

Kuljit's suggestion to add an initial data insertion step before updating records is insightful. This addition would provide a practical demonstration of the table's functionality, aiding in understanding for future developers. Overall, the feedback highlights the code's overall clarity and effectiveness, with the proposed improvement serving to further enhance its comprehensibility and utility.
### Updated version
1. First, assuming you have a table named "students" with columns "id" (primary key), "name", and "age".
``` sql
CREATE TABLE IF NOT EXISTS students (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    age INTEGER
);
```
2. Add the sudents data in table using the insert query.
```sql
INSERT INTO students (name, age)
VALUES 
    ('Ravi', 20);

```
3. Then, you can use the following SQL statement to update or insert student records
``` sql
INSERT OR REPLACE INTO students (id, name, age)
VALUES 
    (1, 'Ravi', 20),  -- Assuming student with id 1 already exists, this will update Ravi's record
    (2, 'Kuljit', 22), -- Assuming student with id 2 doesn't exist, this will insert a new record for Kuljit
    (3, 'Monika', 21); -- Assuming student with id 3 doesn't exist, this will insert a new record for Monika
```
### Reflection
Overall, I feel like I did a good job solving the problem. Someone needed help with the "insert or replace" query in SQL, so I did some research. I found a solution by creating a table first and then updating the data using the "insert or replace" query. After getting feedback from Kuljit, I added another step to add the data before updating it. This made the code easier to understand for everyone.
### References
* https://www.sqlitetutorial.net/sqlite-replace-statement/

# Community Code Project(Part 2)

## Overview
In the communty code project part 2, I chose the c# language and decided to make the singleton pattern. The singleton pattern ensures that a class has only one instance and provides a global point of access to that instance. In C#, there are various techniques to implement the singleton pattern, and one commonly used approach is the lazy singleton pattern.

The lazy singleton pattern defers the instantiation of the singleton object until it is accessed for the first time. This means that the object is created only when it is needed, rather than eagerly instantiated at application startup. This approach is beneficial for scenarios where the creation of the singleton object is resource-intensive or needs to be deferred until it is actually required.

### Implementation of Singleton pattern with lazy technique

``` csharp
              /* ------>  Reference singleton pattern <--------  */  
// https://www.c-sharpcorner.com/UploadFile/8911c4/singleton-design-pattern-in-C-Sharp/
// https://medium.com/developer-rants/singletons-in-c-2c53a846b246
              


using System;

public sealed class Student
{
    // Properties of the student
    public string name { get; set; }
    public int age { get; set; }
    public string grade { get; set; }

    // Private static instance variable using Lazy initialization
    private static readonly Lazy<Student> instance = new Lazy<Student>(() => new Student("Default",0,"None"));
    
    // Public static property to access the singleton instance
    public static Student Instance
    {
        get
        {
            return instance.Value;
        }
    }

    // Constructor
    private Student(string name, int age, string grade)  // Making private constructor to prevent instantiation from outside
    {
        this.name = name;
        this.age = age;
        this.grade = grade;
    }

    // Method to update student details
    public void UpdateDetails(string name, int age, string grade)
    {
        this.name = name;
        this.age = age;
        this.grade = grade;
    }

    // Method to display student details
    public void DisplayDetails()
    {
        Console.WriteLine($"Name: {name}");
        Console.WriteLine($"Age: {age}");
        Console.WriteLine($"Grade: {grade}");
    }
}

class Program
{
    static void Main(string[] args)
    {
        // Accessing the singleton instance
        Student primaryStudent = Student.Instance;

        // Display original details
        Console.WriteLine("Original Details:");
        primaryStudent.DisplayDetails();

        // Update student details
        primaryStudent.UpdateDetails("Ravi", 30, "B");

        // Display updated details
        Console.WriteLine("\nUpdated Details: using primaryStudent object");
        primaryStudent.DisplayDetails();

        /// <summary>
        ///  If developer try to create the new object of student class, he will get error at the compile time
        ///  Comile time error: Student.Student(string, int, string)' is inaccessible due to its protection level
        /// </summary>
        
        // <example> 

        //Student primaryStudent = new Student("",0,""); 
        //primaryStudent.UpdateDetails("Parminder",12,"C");
        
        // </example>

        /// <summary>
        /// Developer can create another instance of Student singleton class and update the details
        /// </summary>
        
        /// <example>
        Student secondaryStudent = Student.Instance;  // creating new object referring to same class
        secondaryStudent.UpdateDetails("Shubham", 32, "A");  // updating the grade of student class using secondaryStudent object

        Console.WriteLine("\nUpdated Details: using secondaryStudent object");
        primaryStudent.DisplayDetails(); // Showing the details using the student object
        
        ///</example>

        /// <summary>
        /// Developer can compare the both instances by comparing each other 
        ///  Output: Both object are same (true)
        /// </summary>
        
        /// <example> 

        if(primaryStudent == secondaryStudent)  // comaparing the both instances
        {
            Console.WriteLine("\nComapare both instances: primaryStudent and secondaryStudent");
            Console.WriteLine("Both objects are same");   
        }
        /// </example>
       

         /// <summary>
        /// Developer can check the using the bool if the instance is exist before operating the operation 
        ///  Output if Instace exist: Is the instance created? True
        /// </summary>
        
        //  <example>

        bool isInstanceCreated = Student.Instance != null; //Checking if the instance has been created yet
        Console.WriteLine("\nCheck instance");
        Console.WriteLine($"Is the instance created? {isInstanceCreated}"); 

        // </example>
    }

}
```
### Output
![Output](https://github.com/nic-dgl104-winter-2024/rrj-ravindersingh/assets/54470199/b4c45ccf-c75b-4b11-9d6c-1b312ea48145)




