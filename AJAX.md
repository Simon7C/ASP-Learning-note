# ASP-Learning-note: AJAX

Include ajax service

```C#
<ajaxToolkit:ToolkitScriptManager ID="ScriptManager" runat="server" ScriptMode="Release" LoadScriptsBeforeUI="true" EnablePartialRendering="true">
<Services>
   <asp:ServiceReference Path="Services.asmx" />
</Services>
</ajaxToolkit:ToolkitScriptManager>
```

Useful tools:
```C#
<asp:UpdatePanel>

<ajaxToolkit:UpdatePanelAnimationExtender ID="upAnimation" runat="server" TargetControlID="upPanel">

<ajaxToolkit:CollapsiblePanelExtender ID="adv_searchEx" runat="Server"
                        TargetControlID="adv_SearchPanel"
                        TextLabelID ="adv_SearchButton"
                        CollapsedSize="0"
                        Collapsed="False"
                        ExpandControlID="adv_SearchButton"
                        CollapseControlID="adv_SearchButton"
                        AutoCollapse="False"
                        AutoExpand="False"
                        ScrollContents="False"
                        CollapsedText="Advanced Search"
                        ExpandedText="Text Search"
                        ExpandDirection="Vertical" 
                        ValidateRequestMode="Disabled"/>
                        
<ajaxToolkit:ModalPopupExtender ID="indexError" runat="server" TargetControlID="indexPop" PopupControlID="indexPopUp">
```
