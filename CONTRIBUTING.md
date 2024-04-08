# Intoduction
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

