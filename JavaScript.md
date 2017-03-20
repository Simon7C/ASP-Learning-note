# ASP-Learning-note: JavaScript
Using javascipt is dangorous, Please make sure the javascript won't cause a bug.

* Run automatically while page load
```Javascript
function pageLoad(sender, args) {
  // get current url
  url = window.location.pathname;
  
  //open js tooltip
  $('.subtooltip').tooltip();
  
  //set key press property
  $("form").bind("keypress", function (e) {
        if (e.keyCode == 13) {
            if (id == "Index") {
                document.getElementById("Button").click();
            }
            //add more buttons here
            return false;
        }
    });
}
```
* Run automaticall before unload
```Javascript
window.onbeforeunload = function () {
  // ask if it is ok to leave
  return "You will lost all changes without save.";
}
```

## Read from Database
You need to prepare the data connection in .asmx fil, see [Web Method](https://github.com/Simon7C/ASP-Learning-note/blob/master/Controller.md)
```Javascript
function NeedData() {
  ID = "11111111"
  var op = "aaaa";
  if (op != -1 && op != 0 && op != null) { // check parameter
    CinemaDB.CinemaDBServices.findExist(op, ID, SuccessEx, OnfailureCallback); // two callbacks, success and failare
  }
}
function SuccessEx(AddResult) {
    //displaying output on alertbox   
    var placeHolder = document.getElementById("placeHolder");
    if (AddResult != "") { // validate result
        var obj = JSON.parse(AddResult);
        obj.Data.forEach(function (element) {
            //create link
            var hypLink = document.createElement("A");
            hypLink.appendChild(t1);
            hypLink.href = "Detail.aspx?c=" + element.ID + "&act=manage";// Append the text to <p>
            hypLink.target = "_blank";
            placeHolder.appendChild(hypLink);
            var para = document.createElement("P");
            var t2 = document.createTextNode(element.Add);
            para.appendChild(t2);
            placeHolder.appendChild(para);
            var br = document.createElement("BR");
            placeHolder.appendChild(br);
        });
        exDiv.removeChild(exDiv.lastChild);
        $find('existWindow').show();
    } else if (AddResult.charAt(0)!="{") {
        var para = document.createElement("P");
        var t2 = document.createTextNode(AddResult);
        para.appendChild(t2);
        placeHolder.appendChild(para);
        $find('existWindow').show();
    }

}
function OnfailureCallback(error) {

}
```
