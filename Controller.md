# ASP-Learning-note: Controller

## Session
```C#
Session["Name"].ToString();
Session.Add("Name", value);
Session.Remove("Name");
```

## Read through Model
```C#
DataProvider objDP = new DataProvider();
objDP.OpenConnection();
DroplistName.DataTextField = "ColumnText";
DroplistName.DataValueField = "ColumnValue";
DroplistName.DataSource = objDP.ModelFunction();
DroplistName.DataBind();
objDP.CloseConnection();
```

## GridView
```C#
protected void gvResult_OnRowDataBound(object sender, GridViewRowEventArgs e)
{
  if (e.Row.RowType == DataControlRowType.DataRow && e.Row.RowType != DataControlRowType.EmptyDataRow)
  {
    ((HyperLink)e.Row.Cells[((GridView)sender).Columns.Count - 1].Controls[0]).NavigateUrl = "Detail.aspx?c=" + ((DataRowView)e.Row.DataItem)["ID"].ToString().Trim() + "&act=manage";
    HtmlImage img = new HtmlImage();
    img.Src = "~/Images/edit.png";
    img.Height = 20;
    ((HyperLink)e.Row.Cells[((GridView)sender).Columns.Count - 1].Controls[0]).Controls.Add(img);

    if (((DataRowView)e.Row.DataItem)["Name"] != null && ((DataRowView)e.Row.DataItem)["Name"].ToString() != "")
    {
        e.Row.Cells[8].ToolTip = e.Row.Cells[9].Text;
    }

/// view different pages
protected void gvResult_PageIndexChanging(object sender, GridViewPageEventArgs e)
{
try
{
gvResult.PageIndex = e.NewPageIndex;
Session.Add("PageIndex", gvResult.PageIndex);
gvResult.DataBind();
resultSwitch.Visible = true;
}
catch (Exception ex)
{ 
if (ex.Message == "Object reference not set to an instance of an object.")
showError("The current session is timeout, Please search agian.");
else
showError(ex.Message);
}
}
/// Sorting
protected void gvResult_OnSorting(object sender, GridViewSortEventArgs e)
{
try
{
if (Session["SortDirection"] == null)
{
Session.Add("SortDirection", e.SortDirection == SortDirection.Ascending ? SortDirection.Descending : SortDirection.Ascending);
}
else
{

Session["SortDirection"] = (SortDirection)Session["SortDirection"] == SortDirection.Ascending ? SortDirection.Descending : SortDirection.Ascending;
}
Session.Add("SortExpression", e.SortExpression);
string strSortDirection = (SortDirection)Session["SortDirection"] == SortDirection.Ascending ? "ASC" : "DESC";
((DataView)(gvResult.DataSource)).Sort = e.SortExpression + " " + strSortDirection;
gvResult.PageIndex = 0;
gvResult.DataBind();
resultSwitch.Visible = true;
}catch(Exception ex)
{
if (ex.Message == "Object reference not set to an instance of an object.")
showError("The current session is timeout, Please search agian.");
else
showError(ex.Message);
}
}
}
```

Load paging and sorting
```C#
//sorting
if (Session["SortDirection"] != null && Session["SortExpression"] != null)
{
string strSortDirection = (SortDirection)Session["SortDirection"] == SortDirection.Ascending ? "ASC" : "DESC";
((DataView)gvResult.DataSource).Sort = Session["SortExpression"].ToString() + " " + strSortDirection;
}
gvResult.DataBind();
//paging                  
gvResult.PageIndex = (Session["PageIndex"] != null ? (int)Session["PageIndex"] : 0);
```

## Export file
Export KML for google earth
```C#
//export google earth KML
objDP.OpenConnection();
DataTable dt = objDP.getCinemasForKML(Session["SearchID"].ToString());
// create xml for export
Util ut = new Util();
string kml = ut.createXml(dt).InnerXml;

Response.ClearHeaders();
Response.ClearContent(); // neded to clear previous (if any) written content
Response.StatusCode = 200;
Response.AddHeader("Content-disposition", "attachment;filename=name-" + DateTime.Now.ToString("dd/mm/yyyy") + ".kml");
Response.ContentType = "application/vnd.google-earth.kml+xml";
Response.BufferOutput = true;

Response.Write(kml);
Response.Flush();
Response.End();

dt.Dispose();
```

Export Excel
```C#
DataSet ds = new DataSet("XLSReport");
GridView gv = new GridView();
objDP.OpenConnection();
ds.EnforceConstraints = false;
ds.Tables.Add(objDP.getForExcel(Session["SearchID"].ToString()));
ds.Tables.Add(ut.Pivot(objDP.geTS(Session["SearchID"].ToString()), "ID", "Year", "Sales", "Estimated", "GST"));
ds.Tables[1].PrimaryKey = new DataColumn[] { ds.Tables[1].Columns["ID"] };

ds.Tables[0].Merge(ds.Tables[1], false, MissingSchemaAction.Add);
ds.Tables[0].Columns.Add("counter", typeof(int));

for (int i = 0; i < ds.Tables[0].Rows.Count; i++)
{
ds.Tables[0].Rows[i]["counter"] = i + 1;
}
DataView view = ds.Tables[0].DefaultView;
view.RowFilter = "counter <= 100";

gv.DataSource = view;//ds.Tables["storedetails"];
gv.DataBind();
this.ExportToExcel(gv, "DBMater-" + DateTime.Now.ToString("dd/mm/yyyy"));
view.Dispose();


//private void ExportToExcel(GridView g, string name)
        {
            Response.Clear();
            Response.AddHeader("content-disposition",
                string.Format("attachment;filename={0}.xls", name));
            //Response.Charset = "";
            Response.ContentType = "application/vnd.ms-excel";

            StringBuilder sb = new StringBuilder();
            StringWriter stringWrite = new StringWriter(sb);
            HtmlTextWriter htmlWrite = new HtmlTextWriter(stringWrite);

            try
            {
                g.GridLines = GridLines.Both;
                g.RenderControl(htmlWrite);
            }
            catch (Exception ex)
            {
                showError(ex.Message);
            }
            Response.Write(stringWrite.ToString());
            Response.Flush();
            Response.End();

        }
```
