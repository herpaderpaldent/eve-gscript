/*
Copy this Example Sheet and paste the code below in the script or make a copy via menu->file->make copy 
https://docs.google.com/spreadsheets/d/1N1Pjn16lM2IZP9SEWCSDHpOW5ycAgXcYDsMLv774J3M/edit#gid=895522078
*/


function cleanArray(actual) {
  var newArray = new Array();
  for (var i = 0; i < actual.length; i++) {
    if (actual[i]) {
      newArray.push(actual[i]);
    }
  }
  return newArray;
}



function FindClosestHSStaion (){
  
  var ui = SpreadsheetApp.getUi();
  var ss = SpreadsheetApp.getActiveSpreadsheet();
  var name = "Hauling"//ss.getSheetName();
  
  var sheet = ss.getSheetByName(name), lastRow = sheet.getLastRow()
  
  var destoHelper = [];
  for(i=0; i<lastRow; i++){
    destoHelper.push(sheet.getRange(19+i, 11).getValue());
  }
  
  var desto = cleanArray(destoHelper);
  
  //Logger.log(desto);
  //ui.alert(desto);
  
  var origin = sheet.getRange(3, 3).getValue();
  
  //ui.alert(desto[0].toString());
  
  var helperDesto = desto[0].toString();
  var Jumps = [];
  var JumpsHelper = NumbersOfJump(origin,desto[0].toString());
  var Position;
  
  //ui.alert(desto.length);
  
  for(i=0; i<desto.length; i++){
    if(NumbersOfJump(origin,desto[i].toString())<JumpsHelper){
      JumpsHelper = NumbersOfJump(origin,desto[i].toString());
      Position = i;
      //Logger.log(JumpsHelper);
    }
    Jumps.push(NumbersOfJump(origin,desto[i].toString()));
 }
  
  ui.alert(Jumps);
  ui.alert(JumpsHelper);
  ui.alert(desto[Position]);
  
  sheet.getRange(4, 3).setValue(desto[Position]);
  sheet.getRange(5, 3).setValue(JumpsHelper);
  
  
}

function NumbersOfJump(from,to) {
  
  // GET /route/{origin}/{destination}/
  // https://esi.tech.ccp.is/latest/route/30000142/30002771/
  
  var origin = SystemNameToSystemID(from);
  var destination = SystemNameToSystemID(to);

  
  var url = "https://esi.tech.ccp.is/latest/route/"+origin+"/"+destination+"/";
  var response = UrlFetchApp.fetch(url).getContentText(); // 35 Jumps
  var data = JSON.parse(response)
  
  //Logger.log(data.length);
  return data.length-1;
}

function SystemNameToSystemID(systemName){
  
  // GET SYSTEMID: https://esi.tech.ccp.is/latest/search/?categories=solar_system&datasource=tranquility&language=en-us&search=Jita&strict=false
  url = "https://esi.tech.ccp.is/latest/search/?categories=solar_system&datasource=tranquility&language=en-us&search="+systemName+"&strict=true";
  var response = UrlFetchApp.fetch(url).getContentText(); // 35 Jumps
  var data = JSON.parse(response)
  
  return data['solar_system'];
  
}
  
  