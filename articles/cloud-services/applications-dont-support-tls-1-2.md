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
ms.openlocfilehash: a9d15a94421694583562f433c20413fcc84697c7
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270858"
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

```
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

Użyj poniższego kodu jako przykładu, aby utworzyć skrypt, który umożliwia używanie starszych protokołów i mechanizmów szyfrowania. Na potrzeby tej dokumentacji ten skrypt będzie miał nazwę: **TLSsettings. ps1**. Zapisz ten skrypt na pulpicie lokalnym, aby uzyskać łatwy dostęp do kolejnych kroków. 


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
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256," 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384," 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256," 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384," 
 
# Legacy cipher suites 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA_P256," 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA_P256," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256," 
        $cipherorder += "TLS_RSA_WITH_AES_256_GCM_SHA384,"  
        $cipherorder += "TLS_RSA_WITH_AES_128_GCM_SHA256,"  
        $cipherorder += "TLS_RSA_WITH_AES_256_CBC_SHA256,"  
        $cipherorder += "TLS_RSA_WITH_AES_128_CBC_SHA256,"  
        $cipherorder += "TLS_RSA_WITH_AES_256_CBC_SHA," 
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
For ($i = 0; $i -lt $localRegistryPath.Length; $i = $i + 1) {  
   Write-Host "Checking for existing of key: $($localRegistryPath[$i]) Severity Level: Information"
   If (!(Test-Path -Path $localRegistryPath[$i])) {
        New-Item $localRegistryPath [$i] | Out-Null
    Write-Host "Creating key: $($localRegistryPath[$i]) Severity Level: Information"
    }
}  
 
#********************************* EXPLICITLY Enable TLS12,  TLS11 and TLS10********************************* 
 
For ($i = 0; $i -lt $localRegistryPath.Length; $i = $i + 1) { 
    if ($localRegistryPath[$i].Contains("Client") -Or $localRegistryPath[$i].Contains("Server")) { 
      Write-Host "Enabling this key: $($localRegistryPath[$i]) Severity: Information "
        $result = Set-CryptoSetting $localRegistryPath[$i].ToString() Enabled 1 DWord   
        $result = Set-CryptoSetting $localRegistryPath[$i].ToString() DisabledByDefault 0 DWord  
        $reboot = $reboot -or $result 
    } 
} 
 
#**************************************** SET THE CIPHER SUITE ORDER******************************** 
 
$cipherlist = @() 
 
# Set cipher suite order 
$cipherlist += Get-BaseCipherSuitesWin10Above 
$CipherSuiteRegKey = "HKLM:\SOFTWARE\Policies\Microsoft\Cryptography\Configuration\SSL\00010002"  
 
if (!(Test-Path -Path $CipherSuiteRegKey))  
{  
    New-Item $CipherSuiteRegKey | Out-Null  
    $reboot = $True  
    Write-Host "Creating key: $($CipherSuiteRegKey) Severity: Information "
}  
 
#Set-ItemProperty -Path $CipherSuiteRegKey -Name Functions -Value $cipherorder  
Set-ItemProperty -Path $CipherSuiteRegKey -Name Functions -Value $cipherlist  
#********************************************* REBOOT ******************************************* 
 
Write-Host "A reboot is required in order for changes to effect"  
Write-Host "Rebooting now..."  
shutdown.exe /r /t 5 /c "Crypto settings changed" /f /d p:2:4
```

## <a name="step-2-create-a-command-file"></a>Krok 2. Tworzenie pliku poleceń 

Utwórz plik CMD o nazwie **RunTLSSettings. cmd** przy użyciu poniższego elementu. Zapisz ten skrypt na pulpicie lokalnym, aby uzyskać łatwy dostęp do kolejnych kroków. 

```cmd
PowerShell -ExecutionPolicy Unrestricted %~dp0TLSsettings.ps1
REM This line is required to ensure the startup tasks does not block the role from starting in case of error.  DO NOT REMOVE!!!! 
EXIT /B 0
```

## <a name="step-3-add-the-startup-task-to-the-roles-service-definition-csdef"></a>Krok 3. Dodawanie zadania uruchamiania do definicji usługi roli (csdef) 

Dodaj następujący fragment kodu do istniejącego pliku definicji usługi. 


```
    <Startup> 
        <Task executionContext="elevated" taskType="simple" commandLine="RunTLSSettings.cmd"> 
        </Task> 
    </Startup> 
```

Oto przykład, który pokazuje rolę procesu roboczego i rolę sieci Web. 

```
<?xmlversion="1.0"encoding="utf-8"?> 
<ServiceDefinitionname="CloudServiceName"xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition"schemaVersion="2015-04.2.6"> 
    <WebRolename="WebRole1"vmsize="Standard_D1_v2"> 
        <Sites> 
            <Sitename="Web"> 
                <Bindings> 
                    <Bindingname="Endpoint1"endpointName="Endpoint1"/> 
                </Bindings> 
            </Site> 
        </Sites> 
        <Startup> 
            <Taske xecutionContext="elevated" taskType="simple" commandLine="RunTLSSettings.cmd"> 
            </Task> 
        </Startup> 
        <Endpoints> 
            <InputEndpointname="Endpoint1"protocol="http"port="80"/> 
        </Endpoints> 
    </WebRole> 
<WorkerRolename="WorkerRole1"vmsize="Standard_D1_v2"> 
    <Startup> 
        <Task executionContext="elevated" taskType="simple" commandLine="RunTLSSettings.cmd"> 
        </Task> 
    </Startup> 
</WorkerRole> 
</ServiceDefinition> 
```

## <a name="step-5-add-the-scripts-to-your-cloud-service"></a>Krok 5. Dodawanie skryptów do usługi w chmurze 

1) W programie Visual Studio kliknij prawym przyciskiem myszy rolę webrole
2) Wybierz pozycję **Dodaj**.
3) Wybierz **istniejący element**
4) W Eksploratorze plików przejdź do pulpitu, na którym zapisano pliki **TLSsettings. ps1** i **RunTLSSettings. cmd** 
5) Wybierz dwa pliki, aby dodać je do projektu Cloud Services

## <a name="step-6-enable-copy-to-output-directory"></a>Krok 6. Włączanie kopiowania do katalogu wyjściowego

Aby zapewnić, że skrypty są przekazywane z każdą aktualizacją wypychaną z programu Visual Studio, ustawienie *Kopiuj do katalogu wyjściowego* musi być ustawione na wartość *zawsze Kopiuj*

1) W obszarze roli sieci Webkliknij prawym przyciskiem myszy pozycję RunTLSSettings. cmd
2) Wybierz **Właściwości**
3) Na karcie Właściwości zmień wartość *Kopiuj do katalogu wyjściowego* , aby była *zawsze kopiowana "*
4) Powtórz kroki dla **TLSsettings. ps1**

## <a name="step-7-publish--validate"></a>Krok 7. publikowanie & Weryfikuj

Teraz, po wykonaniu powyższych kroków, opublikuj aktualizację w istniejącej usłudze w chmurze. 

Możesz użyć [SSLLabs](https://www.ssllabs.com/) , aby sprawdzić stan protokołu TLS punktów końcowych 

 
