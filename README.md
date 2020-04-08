# Get-Weather
Get-Weather function that will give you the weather forecast for the next 7 days, anywhere. 


#This is the Gigabit's 'Get-Weather' Script. 

function get-Weather {
                     [CmdletBinding()]
                      Param(
                            [Parameter(Mandatory=$true)]
                            [string] $City,
                            [Parameter(Mandatory=$true)]
                            [string] $State,
                            [Parameter(Mandatory=$true)]
                            [string] $Days
                            )
                  
#Request Latitude/Longitude Location data for DarkSky to understand:
$LatLng = "http://www.mapquestapi.com/geocoding/v1/address?key=GsSJFOXD9QBQYbIAFjx19GXxov9nyfJQ&location=$City,$State"
$GeoObject = Invoke-RestMethod -URI "$LatLng"
$GeoObjectTable = $GeoObject.results
$Lat = $GeoObjectTable.locations.latLng.lat
$Lng = $GeoObjectTable.locations.latLng.lng

#Pass Latitude/Longitude to DarkSky to output a hash of forecast data into the user's PowerShell instance:
$DarkSky = "https://api.darksky.net/forecast/1432e1bfe7a2a7707b46193fae3d4373/$Lat,$Lng"
$DarkSkyPSObject = Invoke-RestMethod -URI $DarkSky 

#Establish human-readable time format converting from Unix time:
$DaysRange = (0..($Days-1))
$UTCTime = Get-Date -Date "1970-01-01 00:00:00Z"
Foreach($item in $DaysRange){ 
    $DarkSkyPSObject.daily.data[$item].time = $UTCTime.AddSeconds($DarkSkyPSObject.daily.data[$item].time)
    }
Return $DarkSkyPSObject.daily.data[$DaysRange]

<#Friendly filter:
Select-Object time,temperaturehigh,temperaturelow#>



}

#get-Weather -City olympia -State wa -days 5
