# ASP-Learning-note: Web Service

Initial Class:
```C#
public class DBServices : System.Web.Services.WebService
{
}
```
  
## Web Service for JavaScript
Using JSON to pass data
```C#
[WebMethod]
public string findExist(string name, string op, string cinemaID)
{
   DataProvider dp = new DataProvider();
   try
   {
      dp.OpenConnection();
      SqlDataReader dr = dp.getExistingCinemas(name, op, cinemaID);
      string jsonAddress = "{\"Cinemas\":[";
      while (dr.Read())
      {
         jsonAddress += "{\"CinemaID\":\"" + dr["CinemaID"].ToString().Trim() + "\"";
         jsonAddress += ",\"OperatorName\":\"" + dr["OperatorName"].ToString() + "\"},";
      }
      jsonAddress = jsonAddress.TrimEnd(',');
      jsonAddress += "]}";
      dp.CloseConnection();
      return jsonAddress;
      }
   catch (Exception ex)
   {
      return ex.Message;
   }
   finally
   {
      dp.CloseConnection();
   }
}

```
