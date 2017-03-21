# ASP-Learning-note: ASP tools

## Repeater
```C#
 <asp:Repeater ID="RepeaterGLA" runat="server"  OnItemCommand="RepeaterGLA_ItemCommand" OnItemDataBound="RepeaterGLA_ItemDataBound">
<ItemTemplate>
<ajaxToolkit:FilteredTextBoxExtender ID="" TargetControlID="" runat="server"
FilterType="Custom, Numbers" ValidChars=",.">
</ajaxToolkit:FilteredTextBoxExtender>
<asp:Label runat="server" ID="" CssClass=""
Text="<%# ((System.Collections.Generic.KeyValuePair<string, Tuple<string, bool, bool>>)Container.DataItem).Key %>" />
<span class="twoGap"></span>
<asp:Button runat="server" ID="" CommandName="Delete" Text="Delete"  CssClass="repeaterButton inline RFloat"
CausesValidation="false"  OnClientClick="return confirmDelete()" />

<asp:HiddenField ID="hfPrevious" runat="server" />
<br />
                                               
</ItemTemplate>
<FooterTemplate>
<asp:TextBox runat="server" ID="" CssClass="twoGap inline" Height="35px" OnKeyPress="return CheckNumericKeyInfo(event.keyCode, event.which)"></asp:TextBox>

<asp:Button runat="server" ID="" CssClass="buttonFlat inline RFloat" Text="Add" ValidationGroup="TabSt2" CommandName="Add"/>

</asp:CompareValidator>
</FooterTemplate>
</asp:Repeater>
                                        
private void OnItemCommand(object source, RepeaterCommandEventArgs e, string type)
        {
            KeyValuePair<string, Tuple<string, bool, bool>> add;
            Dictionary<string, Tuple<string, bool, bool>> temp = new Dictionary<string, Tuple<string, bool, bool>>();
            foreach (RepeaterItem i in ((Repeater)source).Items)
            {
                bool gst = setStatisticgst(i, type);
                string value = setStatisticvalue(i, type);
                temp.Add(((Label)i.FindControl("Year"+type)).Text, Tuple.Create(value, ((CheckBox)i.FindControl("name")).Checked, gst));
            }
            try
            {
                bool gst = setStatisticgst(e.Item, type);
                string value = setStatisticvalue(e.Item, type);
                
                switch (e.CommandName)
                {
                    case "Delete":
                        add = new KeyValuePair<string, Tuple<string, bool, bool>>(((Label)e.Item.FindControl("Year"+type)).Text,Tuple.Create(value, ((CheckBox)e.Item.FindControl("name")).Checked, gst));
                        temp.Remove(add.Key);
                        submitdata.Value = DataXMLManager.RemoveStatisticNode(submitdata.Value.Trim(), add, type);
                        break;
                    case "Add":
                        add = new KeyValuePair<string, Tuple<string, bool, bool>>(((DropDownList)e.Item.FindControl("dlyear"+type)).SelectedValue.ToString(), Tuple.Create(value, ((CheckBox)e.Item.FindControl("name")).Checked, gst));
                        temp.Add(add.Key, add.Value);
                        submitdata.Value = DataXMLManager.InsertStatisticNode(submitdata.Value.Trim(), add, type);
                        break;
                    default: break;
                }
                ((Repeater)source).DataSource = temp;
                ((Repeater)source).DataBind();
            }
            catch (ArgumentException ex)
            {
                if(ex.Message =="An item with the same key has already been added.")
                    popErrorWindow("The value for this year already exists.");
                else
                    popErrorWindow(ex.Message);
            }
        }

        private void OnItemDataBound(RepeaterItemEventArgs e, string type)
        {
            if (e.Item.ItemType == ListItemType.AlternatingItem || e.Item.ItemType == ListItemType.Item)
            {
                if (!Page.IsPostBack)
                {
                    ((HiddenField)e.Item.FindControl("hfPrevious")).Value =
                        ((KeyValuePair<string, Tuple<string, bool, bool>>)e.Item.DataItem).Value.ToString();
                }
                else
                {
                    ((HiddenField)e.Item.FindControl("hfPrevious")).Value = ((HiddenField)e.Item.FindControl("hfPrevious")).Value;
                }
            }
            if (e.Item.ItemType == ListItemType.Footer)
            {
                ((DropDownList)e.Item.FindControl("dlyear" + type)).Items.AddRange(ut.getYearList());
                ((DropDownList)e.Item.FindControl("dlyear" + type)).SelectedValue = DateTime.Now.Year.ToString();
            }
        }
```

