# JobBank
Job Matching Application WordPress
 

<!DOCTYPE html>
<html>  
<head><h5>Post the Open Job Location</h5>
<body>

<?php
global $StateCode;
global $JobGroup;
global $Skip;
global $isPosted;
global $intJobRoleID;
global $qryJobGroupID;
global $qryCities;
global $JobGroupSel;
global $ColonPos;
global $StateName;
global $UserID;
global $isGuest;
global $ZipCode;
global $id;
global $qapdLoc;

   $isPosted=0;

// The value of the variable Receipt is found
$Receipt = $_GET["id"];  // id = 64428
$Receipt = substr($Receipt,0,5);
// find the Job record
echo 'Job Number: ' . $Receipt;
echo "<br>";
$Skip = 1;

// open the connection
$connJobBank = mysqli_connect('localhost','i4172741_wp1','1088Delb!','i4172741_wp1')
 or die('Error connecting to MySQL server.');

?>
<form id="frmPostJobLoc" name="frmPostJobLoc" class="form" method="post" action="">
Post the Open Job Location
<br>
<style>



.form
{
background-color: #ffff66;
font-size: 16px;
font-family: Arial, Helvetica, sans-serif;
font-weight: normal;
color: black;
line-height: 1.4;
  padding: 20px;
  margin: 10px;
  border-style: solid;
    text-align: left;

}
</style>

<style>
.button {
    background-color: #4CAF50;
    border: none;
    color: white;
    padding: 8px 16px;
    text-align: center;
    text-decoration: none;
    display: inline-block;
    font-size: 12px;
    margin: 4px 2px;
    cursor: pointer;
    font-family: Arial, Helvetica, sans-serif;
}
</style>

<style> 
input[type=submit]
 {
   background-color: #4CAF50;
    border: none;
    color: white;
    padding: 8px 16px;
    text-align: center;
    text-decoration: none;
    display: inline-block;
    font-size: 12px;
    margin: 4px 2px;
    cursor: pointer;
    font-family: Arial, Helvetica, sans-serif;
    visibility: visible;
}
</style>

<br>

<?php

//Build States selection list

$qryStates=" SELECT CONCAT(StateCode,':', StateName) as StateCD_Name, StateName
FROM wp_States WHERE Status = 1 ORDER BY StateName "; 

mysqli_query($connJobBank, $qryStates) or die('Error querying $qryStates database.');
$arrStates=mysqli_query($connJobBank, $qryStates);

$Selected = 0;
$intRow=mysqli_num_rows($arrStates);

$Row00 = '00';
$Name00 = 'Select a State';

echo "<select name= JobStateList id = JobStateList value=' ' size = 4 
autofocus> States </option>";

echo '<option value="' . $Row00 . '">' . $Name00 . '</option>'; 

If ($intRow != 0)
{
while($row = mysqli_fetch_array($arrStates))
      {
         $code = $row["StateCD_Name"];
         $name = $row["StateName"]; 
                 echo '<option value="' . $code . '">' . $name . '</option>'; 
      }
}
echo "</select>";

?>
<input type="submit" name="submit" value="2. List Cities" />

<?php
//Line 76

if (isset($_POST['JobStateList']))
   {
$StateCD_Name = $_POST['JobStateList'];  //Returns AL:Alabama
$ColonPos = intval(strpos($StateCD_Name,":"));  //Returns 2
$StateName =substr($StateCD_Name, $ColonPos+1); //Display to user
$StateCode = substr($StateCD_Name,0,$ColonPos); // Save to wp_Jobs

           	echo $StateName . ' has been selected.';  
   }
else 
  {
        echo ' Please select a state, then click the List Cities button';
  }    // end if (isset($_POST['JobStateList']))

echo "<br>";
if ($StateName == "00")
{
echo ' You need to select a State ' ;
}
else
{
echo ' 3. Select a City in ' . $StateName;
}
echo "<br>";

//Build City List
$qryCities = "SELECT CONCAT(ZipCode,':', CityName) as ZipName, CityName FROM wp_USZips 
WHERE StateCode =   '$StateCode' and Population > 20000 and PrimaryRecord = 'P'  Group by CityName";

mysqli_query($connJobBank, $qryCities) or die('Error querying $qryCities database.');
$arrCities=mysqli_query($connJobBank, $qryCities);

$Selected = 0;
$intRow=mysqli_num_rows($arrCities);

$Row00 = '00';
$Name00 =  'Select the ' . $StateName. ' City';

echo "<select name= JobCityList id=JobCityList value=' ' size = 6 > City </option>";
echo '<option value="' . $Row00 . '">' . $Name00 . '</option>'; 
//line 102
if ($intRow != 0)
     {
     while($row = mysqli_fetch_array($arrCities))
        {
         $code = $row["ZipName"];
         $name =  $row["CityName"];
         echo '<option value="' . $code . '">' . $name . '</option>';
        }  
}
echo "</select>";
echo $StateCode;

$Skip = 0;
?>

<script type="text/javascript"> 
function GetJobRole()
{
}
</script>

<input type="submit" name="submit" value="4. Post This Job Location" />
<?php
if (!isset($_POST['JobCityList']))
{ 
echo ("City not Selected");
}
else
{
    	$JobCitySel = $_POST['JobCityList'];  //Returns 39532:Biloxi
    	$ColonPos = intval(strpos($JobCitySel,":"));  //Returns 5
$JobCityName =substr($JobCitySel, $ColonPos+1); //Display to user
$JobZipID = intval(substr($JobCitySel,$ColonPos+1));
   	$JobZipCode = substr($JobCitySel,0,$ColonPos); // Save to wp_Jobs
    	//echo  'City Selected: ' . $JobCitySel . ', ' . $StateCode ;


echo "<script type='text/javascript'>GetJobCity ();</script>";

//echo ' We will now post the ' . $JobCitySel . ' location. ' ;
//$ColonPos = strpos($JobCitySel,":");
             //$JobCityID = intval(substr($JobCitySel,$ColonPos+1));
            // $JobCityName =substr($JobCitySel,0,$ColonPos);
//echo "<br>";
//echo $isPosted;

//post the selected open job location
$qapdLoc = "Update wp_Jobs SET ZipCode = $JobZipCode   
WHERE JobReceipt = $Receipt ";

mysqli_query($connJobBank, $qapdLoc) or die('Error $qapdLoc database.');
echo "<br>";
echo 'Success! Your job location has been posted.';
echo "<br>";
echo ' Job is in or Near ' . $JobCityName; 
            echo "<br>";
            echo  ' Zip Code: ' . $JobZipCode;
            echo "<br>";
            echo ' Your Job Number is:  ' . $Receipt;
            echo "<br>";
            echo 'You may now enter job details';
            $isPosted = 1;
$dt = new DateTime();
echo "<br>";
    echo $dt->format('Y-m-d H:i');
//line 186
}

?>

<br>
<button type="button" class = "button" onclick="getJobDetails()">4. Go to Details Page</button>
<script>
function getJobDetails() 
{
     
    var isPosted = <?php echo $isPosted; ?>;

var myReceipt = <?php echo $Receipt ?>;
var strReceipt = myReceipt.toString();
//alert(strReceipt);
// Build this string Add ?Variable
//<a href="http://www.myjobbankmax.com/post-open-job-det?id=1788">go to job details</a>
var str1 ="<";
var str2 = "a href=";
var str3 ="href=";
var str4 = '\"http:';
var str5 = "//www.myjobbankmax.com/post-open-job-det";
var str5a = "?id=";
var str5b = strReceipt ;
var str6 = '\"';
var str7 = ">";
var str8 = "Click here to open the job details page:";
var str9 = "<";
var str10 = "/a";
var str11 = ">";

var strTot = str1 + str2 + str3 + str4 + str5 + str5a + str5b + str6 + str7 + str8 + str9 + str10 + str11;
//alert (strTot);
//alert ("demo5");

document.getElementById("GoToLoc").innerHTML = strTot;
}

</script>
<br />

<p id="GoToLoc">Click the Go To Details Page button. The link to open the details page will appear here</p>
<br>
<br>

</form>

</body>
</html>

