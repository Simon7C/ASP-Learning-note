# ASP-Learning-note: Controller

## Set up connection
In Web.config
```C#
<connectionStrings>
   <add name="CinemasDBConnectionString" //string name
   connectionString="MultipleActiveResultSets=True; 
   Data Source= Database1; //DatabaseName
   Initial Catalog= Table; //database table name
   Persist Security Info=False; 
   User ID= people1; //database user name
   Password= password1" //database password
   providerName="System.Data.SqlClient"/>
</connectionStrings>
```


In Controller
```C#
private string connStr = ConfigurationManager.ConnectionStrings["Data"].ToString();
private SqlConnection dbConn;

// new data provider
public DataProvider()
{
  dbConn = new SqlConnection(connStr);
}
public void CloseConnection()
{
  dbConn.Close();
}
public void OpenConnection()
{
  dbConn.Open();
}
```

## Read data
1. get data reader (used for object datasource)
```C#
public SqlDataReader getDataReader(string parameter....)
{
  SqlCommand command = new SqlCommand("commandName", dbConn); //stored procedure name
  command.CommandType = CommandType.StoredProcedure;
  command.Parameters.Add("@Parameter", SqlDbType.VarChar, 50).Value = "Parameter"; // send parameter to SQL
  return command.ExecuteReader();
}
```

2. get data directly (return data as table/string/int, can be read directly)
```C#
public DataTable getDataTable(string parameter....)
{
  SqlDataAdapter sda = new SqlDataAdapter();
  SqlCommand command = new SqlCommand("commandName", this.dbConn); //stored procedure name
  command.CommandType = CommandType.StoredProcedure;
  command.Parameters.Add("@Parameter", SqlDbType.Bit).Value = Parameter; // send parameter to SQL
  sda.SelectCommand = command;
  DataTable dtresult = new DataTable();
  sda.Fill(dtresult);
  sda.Dispose();
  return dtresult;
  //return ((DateTime)dtresult.Rows[0][0]).ToString("dd MMM yyyy hh:mm");
}
```

3. return indicator (to see if the procedure run successful or not)
```C#
public int insertXXX(XmlDocument queryxml, string fullname) // usually for SQL without return value
{
  SqlCommand oCMD = new SqlCommand("commandName", this.dbConn); //stored procedure name
  oCMD.CommandType = CommandType.StoredProcedure;
  oCMD.Parameters.Add("@name1", SqlDbType.Text).Value = queryxml.InnerXml;
  oCMD.Parameters.Add("@name3", SqlDbType.DateTime).Value = DateTime.Now;
  return oCMD.ExecuteNonQuery();     // return the number of rows affected
}
```


