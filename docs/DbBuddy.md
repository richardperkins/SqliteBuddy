# DbBuddy

DbBuddy is the base class used to communicate to SQLite.

## Properties

**connectionPath** - *string*


## Methods

### *System.Data.DataTable* **Execute** (*string* sql, *Dictionary<string, object>* args)

Executes SQL query, returning any result as a *System.Data.DataTable*.

**Arguments**

 `sql` - SQL text to be executed
 
 `args` - Key-value *Dictionary* of arguments to bind
  
**Return**

*System.Data.DataTable* - Resulting data from query

## Example

In this example, we will create a database as well as a 

sql_data.sql
```sql
CREATE TABLE items
(
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT NOT NULL,
    description TEXT
);

INSERT INTO items
(name, description)
VALUES
("Suspicious Mushroom", "I wouldn't eat it if I were you"),
("Cubic Zirconium Ring", "Sounds expensive. Is not"),
("Saltpeter", "Saltiest of the Dynamite Three"),
("Sulfur", "Smelliest of the Dynamite Three"),
("Charcoal", "Dirtiest of the Dynamite Three"),
("Gunpowder", "Make things go BOOM."),
("Stave", "Probably belongs to a king or a wizard.");

```

ItemLoader.cs
```cs
using System.Data;
using DbBuddy;

// Class extending DbBuddy
public class ItemLoader : DbBuddy
{
    /* Constructor
     *
     * Params:
     * string path - filepath to SQLite database
     *
     */
    public ItemLoader (string path) : base (path)
    {
        // Initialize here
    }

    /* Loads item from table "items" by "id
     *
     * Params:
     * int id - Id of item to load
     *
     * Returns:
     * DataTable - SQL query result
     */
    public DataTable LoadItemById (int id)
    {
        // The SQL query to execute
        string sql = "SELECT * FROM items WHERE id=?";
        // The key-value dictionary to bind
        Dictionary<string, object> args = new Dictionary<string, object>();
        // Bind "id"
        args["id"] = id;

        // Execute and return the query
        return Execute(sql, args);
    }

    /* Add row to "items" table
     *
     * Params:
     * string name - Item name to add
     * string description - Item description to add
     *
     * Returns:
     * int - number of affected rows
     */
    public int AddItem(string name, string description)
    {
        // SQL statement to execute
        string sql = "INSERT INTO items (name, description) VALUES (?,?);";
        // Binding arguments to SQL
        Dictionary<string, object> args = new Dictionary<string, object>();
        args["name"] = name;
        args["description"] = description;

        // Execute SQL write
        // Return number of affected rows
        return ExecuteWrite(sql, args);
    }

    /* Updates item information by id
     *
     * Params:
     * int id - Id of item to update
     * string name - name of item
     * string description - description of item
     *
     * Returns:
     * int - Number of affected rows
     */
    public int UpdateItem(int id, string name, string description)
    {
        // SQL statement to execute
        string sql = "UPDATE items SET name=?, description=? WHERE id=?;";
        // Bind arguments to statement
        Dictionary<string, object> args = new Dictionary<string, object>();
        args["id"] = id;
        args["name"] = name;
        args["description"] = description;

        // Execute write
        // Return number of affected rows
        return ExecuteWrite(sql, args);
    }

    /* Removes item by id
     *
     * Params:
     * int id - Id of item to remove
     *
     * Returns:
     * int - Number of rows affected
     */
    public int RemoveItem(int id)
    {
        // SQL statement to execute
        string sql = "DELETE FROM items WHERE id=?"
        // Bind arguments to statement
        Dictionary<string, object> args = new Dictionary<string, object>();
        args["id"] = id;

        //Execute write statement
        // Return number of affected rows
        return ExecuteWrite(sql, args);
    }
}
```

Demo.cs
```cs
using System;
using System.Data;
using DbBuddy;

// Demo class to test our ItemLoader
public class Demo
{
    // Constructor
    public Demo()
    {
        // Filepath to data.db
        string connectionPath = "data.db";

        // Create an ItemLoader
        ItemLoader itemLoader = new ItemLoader(connectionPath);

        // Get the second item from "items" table
        DataTable secondItem = itemLoader.GetItemById(2);

        // Store the name from our results
        string secondItemName = secondItem.Rows[0]["name"];

        Console.WriteLine("Second item is: %s", secondItemName);
    }
}
```