COURSE 1 - ADO.NET – CONNECTED MODEL
1.1 Objective
To know use of views and how to create
1.2 ADO.Net
ADO.NET is the managed data access API. .Net data provider provides the facility to interact with
database. It has the classes Reader, Adapter to fetch data from database. It uses connection
class to connect to the database and it uses command class to execute SQL commands
ADO.Net provides managed types for data access.
Generic types (irrespective of data provider) available in System. Data namespace
SQL Server types in System.Data.SqlClient
SqlConnection – Used to specify the connection string. Following statement is used to specify
connection string and create an object of SqlConnection
string ConnectionString = "Data Source=ServerName;" +
"Initial Catalog=DataBaseName;" +
"User id=UserName;" +
"Password=Secret;";
V2.0
Stor
e
.NET Data Provider
Command
Reader Connection
Adapter

Internal 2
ILP Content
SqlConnection connection = new SqlConnection(ConnectionString);
//Connection is opened using Open method
 connection.Open();
 //Connection should be closed after use. Its done using close method
 connection.Close();
SqlCommand:Used to execute SQL Command. Command object is created . Command type
can be text or stored procedure or Table Direct. Command Text is sql query if command type is
text. Its stored procedure name if command type is stored procedure and its table name if
command type is stored procedure.
 //create command object
 SqlCommand command = new SqlCommand();
 //Set command type as stored procedure
 command.CommandType = CommandType.StoredProcedure;
 //Command text is stored procedure name if command type is stored procedure
 command.CommandText = "procedureName";
 //Used to relate command object to connection
 command.Connection = connection;
SQL Parameter: Used to required add parameters to stored procedure
 Following code is used if the procedure has input parameter. Parameter name is specified
within “” and value is passed to the procedure.
 command.Parameters.AddWithValue("@id", id);
Executing command:
Command is executed using following methods
1) ExecuteNonQuery:
Used for insert/update/delete statements. Execute NonQuery returns the number
of rows affected
eg.int rowsAffected = command.ExecuteNonQuery();
2) ExecuteReader – Used for select query. Execute Reader returns reader object. We use Read
method to read from reader and we can fetch details using reader object. Column name is
specified in reader object to fetch the details of the required column eg.reader["id"]. Alternatively
we can provide index we can use reader[0],reader[1] and so on.
eg
// Execute procedure with select query
 SqlDataReader reader= command.ExecuteReader();
 List<Customer> customerList = new List<Customer>();
 //Read row by row from reader object
 while(reader.Read())
 {
 int id = Convert.ToInt32(reader["id"]);
 string name = reader["name"].ToString();
 Customer customer = new Customer(id, name);
 customerList.Add(customer);
 }
3) ExecuteScalar – Used when select query returns a single value
eg int maxUser = Convert.ToInt32(command.ExecuteScalar());

Internal 3
ILP Content
1.3 Sample
Create a customer table with id, name, contact number and location. And do the following
operations
1. Add customer and return the autogenerated customer id
2. View all the customers
3. Retrieve contact number for a customer
4.
Following is the sql code for the given scenario
CREATE TABLE tblCustomer(
[customerid] [int] IDENTITY(1,1) primary key,
[name] [varchar](30),
[contactNo] [bigint],
[Location] [varchar](30)
)
create proc sp_viewCustomer
as
select * from tblCustomer
create proc sp_viewCustomerbyId
(@id int)
as
begin
select contactNo from tblCustomer
where customerid=@id
end
create proc sp_insertCustomer
(@name varchar(30),
@contactNo bigint,
@location varchar(30),
@id int out)
as
begin
insert into tblCustomer
values(@name,@contactNo,@location)
set @id=@@identity
end
Following is the customer Class
//Customer Class
 public class Customer
 {
 //Attributes
 int _customerId;
 string _name;
 int _contactNo;
 string _location;
 //properties
 public int CustomerId { get { return _customerId; } set { _customerId =
value; } }
 public string Name { get { return _name; } set { _name = value; } }

Internal 4
ILP Content
 public int ContactNo { get { return _contactNo; } set { _contactNo =
value; } }
 public string Location { get { return _location; } set { _location =
value; } }
 //constructor
 public Customer(int id, string name, int contactNo, string location)
 {
 _customerId = id;
 _name = name;
 _contactNo = contactNo;
 _location = location;
 }
 public Customer(string name, int contactNo, string location)
 {

 _name = name;
 _contactNo = contactNo;
 _location = location;
 }
Following is the customer DB class which contains seperate methods for each operation
public class CustomerDB
 {
 public List<Customer> getCustomers()
 {
 string ConnectionString = "Data Source=ServerName;" +
 "Initial Catalog=DataBaseName;" +
"User id=UserName;" +
"Password=Secret;";

 SqlConnection connection = new SqlConnection(ConnectionString);
 //Connection is opened using Open method
 connection.Open();
 //create command object
 SqlCommand command = new SqlCommand();
 //Set command type as stored procedure
 command.CommandType = CommandType.StoredProcedure;
 //Command text is stored procedure name if command type is stored
procedure
 command.CommandText = "sp_viewCustomer";
 //Used to relate command object to connection
 command.Connection = connection;
 // Execute procedure with select query
 SqlDataReader reader= command.ExecuteReader();
 List<Customer> customerList = new List<Customer>();
 //Read row by row from reader object
 while(reader.Read())
 {
 int id = Convert.ToInt32(reader["customerid"]);
 string name = reader["name"].ToString();
 Customer customer = new Customer(id,
name,Convert.ToInt32(reader["contactNo"]),reader["Location"].ToString());
 customerList.Add(customer);
 }

 //Connection should be closed after use. Its done using close method
 connection.Close();

Internal 5
ILP Content
 return (customerList);
 }
 public int geCustomersContactNo(int id)
 {
 string ConnectionString = "Data Source=ServerName;" +
 "Initial Catalog=DataBaseName;" +
"User id=UserName;" +
"Password=Secret;";

 SqlConnection connection = new SqlConnection(ConnectionString);
 //Connection is opened using Open method
 connection.Open();
 //create command object
 SqlCommand command = new SqlCommand();
 //Set command type as stored procedure
 command.CommandType = CommandType.StoredProcedure;
 //Command text is stored procedure name if command type is stored
procedure
 command.CommandText = "sp_viewCustomerbyId";
 //Used to relate command object to connection
 command.Connection = connection;
 //Used to pass parameter to procedure
 command.Parameters.AddWithValue("@id", id);
 // Execute procedure with select query
 int number = Convert.ToInt32(command.ExecuteScalar());
 //Connection should be closed after use. Its done using close method
 connection.Close();
 return number;

 }
 public int addCustomers(Customer custObj)
 {
 string ConnectionString = "Data Source=ServerName;" +
 "Initial Catalog=DataBaseName;" +
"User id=UserName;" +
 "Password=Secret;";

 SqlConnection connection = new SqlConnection(ConnectionString);
 //Connection is opened using Open method
 connection.Open();
 //create command object
 SqlCommand command = new SqlCommand();
 //Set command type as stored procedure
 command.CommandType = CommandType.StoredProcedure;
 //Command text is stored procedure name if command type is stored
procedure
 command.CommandText = "sp_insertCustomer";
 //Used to relate command object to connection
 command.Connection = connection;
 //Used to pass parameter to procedure
 command.Parameters.AddWithValue("@name", custObj.Name);
 command.Parameters.AddWithValue("@contactNo", custObj.ContactNo);
 command.Parameters.AddWithValue("@location", custObj.Location);
 command.Parameters.AddWithValue("@id", 0);

Internal 6
ILP Content
 //Denotes that id is a output parameter
 command.Parameters["@id"].Direction=ParameterDirection.Output;
 //Used to execute command
 int rowAffected = command.ExecuteNonQuery();
 //Connection should be closed after use. Its done using close method
 connection.Close();
 // Used to return the value of output parameter
 if (rowAffected > 0)
 return Convert.ToInt32(command.Parameters["@id"].Value);
 else
 return (rowAffected);
 }
Following is the code executed to be written in main method
 CustomerDB db=new CustomerDB();
 //Get all customer from getCustomer method in customerDB
 List<Customer> customerList = db.getCustomers();
 foreach (Customer c in customerList)
 {
 Console.WriteLine("Customer Id: "+c.CustomerId);
 Console.WriteLine("Customer Name: "+c.Name);
 Console.WriteLine("Contact Number: "+c.ContactNo);
 Console.WriteLine("Location: "+c.Location);
 }
 Console.ReadKey();
 //code to insert a data. You can get data from user
 Customer ins=new Customer("Priya",990909090,"Delhi");
 int result=db.addCustomers(ins);
 Console.WriteLine("Customer Added and id is " + result);
 Console.ReadKey();
 //code to fetch contact number of customer inserted now
 int contactNo=db.geCustomersContactNo(result);
 Console.WriteLine(contactNo);
 Console.ReadKey();
