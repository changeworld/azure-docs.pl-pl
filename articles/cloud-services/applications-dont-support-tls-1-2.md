---
title: Rozwiązywanie problemów spowodowanych przez aplikacje nieobsługujące protokołu TLS 1,2 | Microsoft Docs
description: Rozwiązywanie problemów spowodowanych przez aplikacje nieobsługujące protokołu TLS 1,2
services: cloud-services
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: top-support-issue
ms.assetid: ''
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 01/17/2020
ms.author: tagore
ms.openlocfilehash: 161c24b4e751eea9477e9d338722629daa97e193
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78250437"
---
# <a name="troubleshooting-applications-that-dont-support-tls-12"></a>Rozwiązywanie problemów z aplikacjami, które nie obsługują protokołu TLS 1,2
W tym artykule opisano sposób włączania starszych protokołów TLS (TLS 1,0 i 1,1) oraz stosowania starszych mechanizmów szyfrowania w celu obsługi dodatkowych protokołów w systemie Windows Server 2019 w sieci Web i roli procesu roboczego usługi w chmurze. 

Firma Microsoft zdaje sobie sprawę, że podczas wykonywania kroków w celu wycofania protokołu TLS 1,0 i TLS 1,1 klienci mogą potrzebować obsługi starszych protokołów i mechanizmów szyfrowania do momentu, gdy będą mogli zaplanować ich wycofanie.  Mimo że nie zalecamy ponownego włączenia tych starszych wartości, udostępniamy wskazówki ułatwiające klientom. Zachęcamy klientów do ocenienia ryzyka związanego z regresją przed wdrożeniem zmian opisanych w tym artykule. 

> [!NOTE]
> System operacyjny gościa 6 wydania wymusza protokół TLS 1,2 przez wyłączenie szyfrów 1.0/1.1. 

  
## <a name="dropping-support-for-tls-10-tls-11-and-older-cipher-suites"></a>Porzucanie pomocy technicznej dla protokołów TLS 1,0, TLS 1,1 i starszych mechanizmów szyfrowania 
Zgodnie z naszymi zobowiązaniami do korzystania z najlepszej klasy szyfrowania firma Microsoft ogłosiła plany rozpoczęcia migracji od protokołu TLS 1,0 i 1,1 w czerwcu z 2017.   Od tego początkowego anonsu firma Microsoft ogłosiła nasze zamiar w celu wyłączenia Transport Layer Security (TLS) 1,0 i 1,1 domyślnie w obsługiwanych wersjach programu Microsoft Edge i Internet Explorer 11 w pierwszej połowie 2020.  Podobne anonse od firmy Apple, Google i Mozilla wskazują kierunek, w którym jest on używany.   

## <a name="tls-configuration"></a>Konfiguracja protokołu TLS  
Obraz serwera z systemem Windows Server 2019 w chmurze jest skonfigurowany przy użyciu protokołu TLS 1,0 i TLS 1,1 na poziomie rejestru. Oznacza to, że aplikacje wdrożone w tej wersji systemu Windows i przy użyciu stosu systemu Windows na potrzeby negocjacji TLS nie będą zezwalały na komunikację TLS 1,0 i TLS 1,1.   

Serwer zawiera również ograniczony zestaw mechanizmów szyfrowania: 

```Powershell
    TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 
    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 
    TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
    TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 
    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256 
    TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384 
    TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 
    TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 
```
 
## <a name="step-1-create-the-powershell-script-to-enable-tls-10-and-tls-11"></a>Krok 1. utworzenie skryptu programu PowerShell w celu włączenia protokołu TLS 1,0 i TLS 1,1 

Użyj poniższego kodu jako przykładu, aby utworzyć skrypt, który umożliwia używanie starszych protokołów i mechanizmów szyfrowania.   Na potrzeby tej dokumentacji ten skrypt będzie miał nazwę: TLSsettings. ps1. 
  
```Powershell
#******************* FUNCTION THAT ACTUALLY UPDATES KEYS; WILL RETURN REBOOT FLAG IF CHANGES *********************** 

Function Set-CryptoSetting {  
    param (  
        $regKeyName,  
        $value,  
        $valuedata,  
        $valuetype       
    )  
    
    $restart = $false 
  
    # Check for existence of registry key, and create if it does not exist  
    If (!(Test-Path -Path $regKeyName)) {  
        New-Item $regKeyName | Out-Null  
    }  

    # Get data of registry value, or null if it does not exist  
    $val = (Get-ItemProperty -Path $regKeyName -Name $value -ErrorAction SilentlyContinue).$value  

    If ($val -eq $null) {  
        # Value does not exist - create and set to desired value  
        New-ItemProperty -Path $regKeyName -Name $value -Value $valuedata -PropertyType $valuetype | Out-Null  
        $restart = $true 
    } 

    Else {  
        # Value does exist - if not equal to desired value, change it  
        If ($val -ne $valuedata) {  
            Set-ItemProperty -Path $regKeyName -Name $value -Value $valuedata  
            $restart = $true  
        }  
    }  

    $restart  
}  

#*************************************************************************************************************** 

#****************************** CIPHERSUITES FOR OS VERSIONS WINDOWS 10 AND ABOVE ****************************** 

function Get-BaseCipherSuitesWin10Above() 
{ 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256" 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384" 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256" 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384" 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256" 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384" 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256" 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384" 

# Legacy cipher suites 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA_P256" 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA_P256" 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256" 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256" 
        $cipherorder += "TLS_RSA_WITH_AES_256_GCM_SHA384"  
        $cipherorder += "TLS_RSA_WITH_AES_128_GCM_SHA256"  
        $cipherorder += "TLS_RSA_WITH_AES_256_CBC_SHA256"  
        $cipherorder += "TLS_RSA_WITH_AES_128_CBC_SHA256"  
        $cipherorder += "TLS_RSA_WITH_AES_256_CBC_SHA" 
        $cipherorder += "TLS_RSA_WITH_AES_128_CBC_SHA" 

  return $cipherorder 
} 

  
#*************************************************************************************************************** 


#********************************************** REGISTRY KEYS **************************************************** 

  
function Get-RegKeyPathToEnable() 
{ 
    $regKeyPath = @( 
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2",         
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client",  
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" , 
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1",  
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Client", 
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" , 
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0",  
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Client",  
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" 
    ) 
    return $regKeyPath 
} 

#*************************************************************************************************************** 

$localRegistryPath = @() 

# Enable TLS 1.2, TLS 1.1 and TLS 1.0 
$localRegistryPath += Get-RegKeyPathToEnable 

#******************* CREATE THE REGISTRY KEYS IF THEY DON'T EXIST******************************** 

# Check for existence of the registry keys, and create if they do not exist  
For ($i = 0; $i -lt $localRegistryPath .Length; $i = $i + 1) {  
   Write-Log -Message "Checking for existing of key: $($localRegistryPath [$i]) " -Logfile $logLocation  -Severity Information 
   If (!(Test-Path -Path $localRegistryPath [$i])) {  
        New-Item $localRegistryPath [$i] | Out-Null 
               Write-Log -Message "Creating key: $($localRegistryPath [$i]) "  -Logfile $logLocation -Severity Information 
           } 
}  

#********************************* EXPLICITLY Enable TLS12,  TLS11 and TLS10********************************* 

For ($i = 0; $i -lt $localRegistryPath .Length; $i = $i + 1) { 
    if ($localRegistryPath [$i].Contains("Client") -Or $localRegistryPath [$i].Contains("Server")) { 
        Write-Log -Message "Enabling this key: $($localRegistryPath [$i]) "  -Logfile $logLocation -Severity Information  
        $result = Set-CryptoSetting $localRegistryPath [$i].ToString() Enabled 1 DWord   
        $result = Set-CryptoSetting $localRegistryPath [$i].ToString() DisabledByDefault 0 DWord  
        $reboot = $reboot -or $result 
    } 
} 
 
#**************************************** SET THE CIPHER SUITE ORDER******************************** 

$cipherlist = @() 

# Set cipher suite order 
$cipherlist += Get-BaseCipherSuitesWin10Above 
$cipherorder = [System.String]::Join(",", $cipherlist) 
$CipherSuiteRegKey = "HKLM:\SOFTWARE\Policies\Microsoft\Cryptography\Configuration\SSL\00010002"  

if (!(Test-Path -Path $CipherSuiteRegKey))  
{  
    New-Item $CipherSuiteRegKey | Out-Null  
    $reboot = $True  
    Write-Log -Message "Creating key: $($CipherSuiteRegKey) "  -Logfile $logLocation -Severity Information 
}  

Set-ItemProperty -Path $CipherSuiteRegKey -Name Functions -Value $cipherorder  

#********************************************* REBOOT ******************************************* 

Write-Host "A reboot is required in order for changes to effect"  
Write-Host "Rebooting now..."  
shutdown.exe /r /t 5 /c "Crypto settings changed" /f /d p:2:4  
```
  
## <a name="step-2-create-a-command-file"></a>Krok 2. Tworzenie pliku poleceń 

Utwórz plik CMD z poniższymi informacjami. 

```
IF "%ComputeEmulatorRunning%" == "true" ( 
   ECHO Not launching the script, since is DEV Machine >> "Log.txt" 2>&1 
) ELSE ( 

PowerShell .\TLSsettings.ps1   
  
) 

REM This line is required to ensure the startup tasks does not block the role from starting in case of error.  DO NOT REMOVE!!!! 

EXIT /B 0   
```

## <a name="step-3-add-the-startup-task-to-the-roles-service-definition-csdef"></a>Krok 3. Dodawanie zadania uruchamiania do definicji usługi roli (csdef) 

Oto przykład, który pokazuje rolę procesu roboczego i rolę sieci Web. 
  
```
<?xml version="1.0" encoding="utf-8"?> 
<ServiceDefinition name="NugetExampleCloudServices" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6"> 
  <WebRole name="WebRole1" vmsize="Standard_D1_v2"> 
    <Sites> 
      <Site name="Web"> 
        <Bindings> 
          <Binding name="Endpoint1" endpointName="Endpoint1" /> 
        </Bindings> 
      </Site> 
    </Sites> 
    <Startup> 
      <Task executionContext="elevated" taskType="simple" commandLine="RunTLSSettings.cmd"> 
      </Task> 
    </Startup> 
    <Endpoints> 
      <InputEndpoint name="Endpoint1" protocol="http" port="80" /> 
    </Endpoints> 
  </WebRole> 
  <WorkerRole name="WorkerRole1" vmsize="Standard_D1_v2"> 
    <Startup> 
      <Task executionContext="elevated" taskType="simple" commandLine="RunTLSSettings.cmd"> 
      </Task> 
    </Startup> 
  </WorkerRole> 
</ServiceDefinition> 
```

## <a name="step-4-validation"></a>Krok 4. Sprawdzanie poprawności 

Możesz użyć [SSLLabs](https://www.ssllabs.com/) , aby sprawdzić stan protokołu TLS punktów końcowych 

 
