<!DOCTYPE html>
<!--
To change this license header, choose License Headers in Project Properties.
To change this template file, choose Tools | Templates
and open the template in the editor.
-->
<html lang="en">
<head>
<title>Student Enrollment Form</title>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.4.1/css/bootstrap.min.css">
<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.5.1/jquery.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.4.1/js/bootstrap.min.js"></script>
</head>

<body>
    <div class="container">
        <div class="page-header text-center"> 
            <h2>Student Enrollment Form</h2>
        </div> 
        <form id="stuform" method="get">
            <div class="form-group">
                <label>Roll No.</label>
                <input type="text" id="stuid" class="form-control" onchange="getstu()">  
            </div>
            <div class="form-group">
                <label>Full Name</label>
                <input type="text" id="stuname" class="form-control">
            </div>
            <div class="form-group">
                <label>Class</label>
                <input type="number" id="stuclass" class="form-control">
            </div>
            <div class="form-group">
                <label>Birth-Date</label>
                <input type="date" id="Bdate" class="form-control">
            </div>
            <div class="form-group">
                <label>Address</label>
                <input type="text" id="address" class="form-control">
            </div>
            <div class="form-group">
                <label>Enrollment-Date</label>
                <input type="date" id="endate" class="form-control">
            </div>
            <div class="form-group text-center">
                <button type="button" class="btn btn-lg btn-primary" id="save" onclick="saveData()" >Save</button>
                <button type="button" class="btn btn-lg btn-primary" id="change" onclick="changeData()" >Change</button>
                <button type="button" class="btn btn-lg btn-primary" id="reset" onclick="resetForm()" >Reset</button>
            </div>
        </form>
    </div>
    


    <script src="http://login2explore.com/jpdb/resources/js/0.0.3/jpdb-commons.js"></script>

    <script >
        var jpdbBaseURL="http://api.login2explore.com:5577";
var jbdbIRL="/api/irl";
var jbdbIML="/api/iml";
var stuDBName="SCHOOL-DB";
var stuRelationName="STUDENT-TABLE";
var connToken="90933077|-31949323887460454|90951507";

$("#stuid").focus();

function saveRecNo2LS(jsonObj){
    var lvData=JSON.parse(jsonObj.data);
    localStorage.setItem("recno",lvData.rec_no);
}

function createPUTRequest(connToken, jsonObj, dbName, relName) {
    var putRequest = "{\n"
    + "\"token\" : \""
    + connToken
    + "\","
    + "\"dbName\": \""
    + dbName
    + "\",\n" + "\"cmd\" : \"PUT\",\n"
    + "\"rel\" : \""
    + relName + "\","
    + "\"jsonStr\": \n"
    + jsonObj
    + "\n"
    + "}";
    return putRequest;

}

function getStuIdAsJsonObj(){
    var stuid=$("#stuid").val();
    var jsonStr={
        id:stuid
    };
    return JSON.stringify(jsonStr);
}

function fillData(jsonObj){
    saveRecNo2LS(jsonObj);
    var record= JSON.parse(jsonObj.data).record;
    $("#stuname").val(record.name);
    $("#stuclass").val(record.class);
    $("#Bdate").val(record.date);
    $("#address").val(record.address);
    $("#endate").val(record.endate);
}

function resetForm(){
    $("#stuid").val(" ");
    $("#stuname").val(" ");
    $("#stuclass").val(" ");
    $("#Bdate").val(" ");
    $("#address").val(" ");
    $("#endate").val(" ");
    $("#stuid").prop("disabled",false);
    $("#save").prop("disabled",true);
    $("#change").prop("disabled",true);
    $("#reset").prop("disabled",true);
    $("#stuid").focus();
}

function validateData(){
    var stuid, stuname, stuclass, Bdate, address, endate;
    stuid=$("#stuid").val(" ");
    stuname=$("#stuname").val(" ");
    stuclass=$("#stuclass").val(" ");
    Bdate=$("#Bdate").val(" ");
    address=$("#address").val(" ");
    endate=$("#endate").val(" ");

    if(stuid ===" "){
        alert("Student ID missing");
        $("#stuid").focus();
        return "";
    }
    if(stuname ===" "){
        alert("Student name missing");
        $("#stuname").focus();
        return "";
    }
    if(stuclass ===" "){
        alert("Student class missing");
        $("#stuclass").focus();
        return "";
    }
    if(Bdate ===" "){
        alert("Birth date missing");
        $("#Bdate").focus();
        return "";
    }
    if(address ===" "){
        alert("Address missing");
        $("#address").focus();
        return "";
    }
    if(endate ===" "){
        alert("Enrollment Date missing");
        $("#endate").focus();
        return "";
    }

    var jsonStrObj={
        id:stuid,
        name:stuname,
        class:stuclass,
        Bdate:Bdate,
        address:address,
        endate:endate
    };
    return JSON.stringify(jsonStrObj);
}

function getstu(){
    var stuIdJsonObj = getStuIdAsJsonObj();
    var getRequest = createGET_BY_KEYRequest(connToken,stuDBName,stuRelationName,stuIdJsonObj);
    jQuery.ajaxSetup({async:false});
    var resJsonObj = executeCommandAtGivenBaseUrl(getRequest,jpdbBaseURL,jpdbIRL);
    jQuery.ajaxSetup({async:true});
    if(resJsonObj.status===400){
        $("#save").prop("disabled",false);
        $("#reset").prop("disabled",false);
        $("#stuname").focus(); 
    } else if(resJsonObj.status===200){
        $("#stuid").prop("disabled",true);
        fillData(resJsonObj);
        $("#change").prop("disabled",false);
        $("#reset").prop("disabled",false);
        $("#stuname").focus();
    }
}

function saveData(){
    var jsonStrObj = validateData();
    if(jsonStrObj===""){
        return "";
    }
    var putRequest = createPUTRequest(connToken,jsonStrObj,stuDBName,stuRelationName);
    jQuery.ajaxSetup({async:false});
    var resJsonObj = executeCommandAtGivenBaseUrl(putRequest,jpdbBaseURL,jpdbIML);
    jQuery.ajaxSetup({async:true});
    resetForm();
    $("#stuid").focus();
}

function changeData(){
    $("#change").prop("disabled",true);
    jsonChg=validateData();
    var updateRequest = createUPDATERecordRequest(connToken,jsonChg,stuDBName,stuRelationName,localStorage.getItem("recno"));
    jQuery.ajaxSetup({async:false});
    var resJsonObj = executeCommandAtGivenBaseUrl(updateRequest,jpdbBaseURL,jpdbIML);
    jQuery.ajaxSetup({async:true});
    console.log(resJsonObj);
    resetForm();
    $("#stuid").focus();
}
    </script>
</body>
</html>
