# ASP-Learning-note: XML

## Create XML File

Basic info structure
```C#
public static string CreateTemplate()
{
  return @"<?xml version=""1.0"" encoding=""iso-8859-1"" ?><data><cataloge1></cataloge1><cataloge2></cataloge2></data>";
}
```

## Basic Add Node
```C#
public static XmlDocument Addnode(XmlDocument postdata, string elementname, string value)
{
//add element to cataloge1
postdata.SelectSingleNode("/data/cataloge1").AppendChild(
postdata.ImportNode(postdata.CreateNode(XmlNodeType.Element, elementname, postdata.NamespaceURI), true));
// set element value
postdata["data"]["cataloge1"][elementname].InnerText = value;
return postdata;
}
```

## Create complex node and operations
* Add
```C#
private static XmlNode createNode(KeyValuePair<String, Tuple<string, string>> parameter, string etype)
{
  XmlDocument temp = new XmlDocument();
  temp.AppendChild(temp.CreateElement(etype));
  temp[etype].AppendChild(temp.CreateElement("type"));
  temp[etype].AppendChild(temp.CreateElement("value1"));
  temp[etype].AppendChild(temp.CreateElement("value2"));
  temp[etype]["type"].InnerText = value.Key;
  temp[etype]["value1"].InnerText = parameter.Value.Item1;
  temp[etype]["value2"].InnerText = parameter.Value.Item2;
  return temp[etype];
}
public static string InsertStatisticNode(string XMLInputString, KeyValuePair<string, Tuple<string, string>> parameter, string type)
{
  XmlDocument document = new XmlDocument();
  document.LoadXml(XMLInputString);
  document.SelectSingleNode("/data/cataloge1").AppendChild(document.ImportNode(createStatisticNode(newEntry, type), true));
  return document.InnerXml;
}
```

* Remove Node
```C#
public static string RemoveNode(string XMLInputString, KeyValuePair<string, Tuple<string, string>> parameter, string type)
{
  XmlDocument document = new XmlDocument();
  document.LoadXml(XMLInputString);
  XmlNodeList insertedNodes = document.SelectSingleNode("/data/insert").ChildNodes;
  int i = insertedNodes.Count - 1;
  bool exist = false;
  while (i >= 0) // if it is waiting for insert, remove from insert list
  {
    if (insertedNodes[i].Name == type && insertedNodes[i]["year"].InnerXml == newEntry.Key)
    {
      exist = true;
      document.SelectSingleNode("/data/insert").RemoveChild(insertedNodes[i]);
     }
    i--;
  }
    if (!exist) //else put it in remove list
    {
      document.SelectSingleNode("/data/remove").AppendChild(document.ImportNode(createStatisticNode(newEntry, type), true));
    }
  return document.InnerXml;
  }
}
```

* Update Node
```C#
public static XmlDocument UpdateStatisticNode(XmlDocument data, string year, Tuple<string, string> parameter, string type)
{
  bool exist = false;
  foreach (XmlNode n in data.SelectNodes("/data/insert/" + type))
  {
    if (n["year"].InnerText == year)
    {
      exist = true;
      n["value1"].InnerText = parameter.Item1;
      n["value2"].InnerText = parameter.Item2;
    }
  }
  if (!exist)
  {
    data.SelectSingleNode("/data/update").AppendChild(data.ImportNode(
    createStatisticNode(new KeyValuePair<string, Tuple<string, string>>(year, parameter), type), true));
  }
  return data;
}
```
