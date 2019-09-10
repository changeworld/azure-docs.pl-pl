---
title: Odnajdywanie rozwiązań programu Endpoint Protection i Ocena kondycji w Azure Security Center | Microsoft Docs
description: Sposób odnajdywania i identyfikowania rozwiązań programu Endpoint Protection w dobrej kondycji.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
ms.assetid: 2730a2f5-20bc-4027-a1c2-db9ed0539532
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2019
ms.author: v-mohabe
ms.openlocfilehash: a5cd0f88173abb65a120aa305206505af51d9f9e
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70861372"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Ocena i zalecenia dotyczące programu Endpoint Protection w Azure Security Center

Ocena i zalecenia dotyczące programu Endpoint Protection w Azure Security Center wykrywają i dostarczają oceny kondycji [obsługiwanych](https://docs.microsoft.com/azure/security-center/security-center-os-coverage) wersji rozwiązań programu Endpoint Protection. W tym temacie objaśniono scenariusze, które generują następujące dwa zalecenia dotyczące rozwiązań programu Endpoint Protection, Azure Security Center.

* **Zainstaluj rozwiązania programu Endpoint Protection na maszynie wirtualnej**
* **Rozwiązywanie problemów z kondycją programu Endpoint Protection na maszynach**

## <a name="windows-defender"></a>Windows Defender

* Zalecenie **"Zainstaluj program Endpoint Protection na maszynie wirtualnej"** jest generowane w przypadku uruchamiania [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) , a wynikiem jest **AMServiceEnabled: False**

* Zalecenie **"Rozwiąż problemy dotyczące kondycji programu Endpoint Protection na swoich maszynach"** jest generowane w przypadku uruchamiania [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) i jednego lub obu następujących wystąpień:

  * Co najmniej jedna z następujących właściwości ma wartość false:

     **AMServiceEnabled**

     **AntispywareEnabled**

     **RealTimeProtectionEnabled**

     **BehaviorMonitorEnabled**

     **IoavProtectionEnabled**

     **OnAccessProtectionEnabled**

  * Jeśli co najmniej jedna z następujących właściwości jest większa lub równa 7.

     **AntispywareSignatureAge**

     **AntivirusSignatureAge**

## <a name="microsoft-system-center-endpoint-protection"></a>Program Microsoft System Center Endpoint Protection

* Zalecenie **"Zainstaluj rozwiązania programu Endpoint Protection na maszynie wirtualnej"** jest generowane podczas importowania **SCEPMpModule ("$env:P rogramfiles\microsoft Security Client\MpProvider\MpProvider.psd1")** i uruchamiania  **Wyniki Get-MProtComputerStatus** z **AMServiceEnabled = false**

* Zalecenie **"Rozwiąż problemy dotyczące kondycji programu Endpoint Protection na swoich maszynach"** jest generowane w przypadku uruchamiania **Get-MprotComputerStatus** i jednego lub obu następujących wystąpień:

    * Co najmniej jedna z następujących właściwości ma wartość false:

       **AMServiceEnabled**
    
       **AntispywareEnabled**
    
       **RealTimeProtectionEnabled**
    
       **BehaviorMonitorEnabled**
    
       **IoavProtectionEnabled**
    
       **OnAccessProtectionEnabled**
          
    * Jeśli co najmniej jedna z poniższych aktualizacji podpisu jest większa lub równa 7. 

       **AntispywareSignatureAge**
    
       **AntivirusSignatureAge**

## <a name="trend-micro"></a>Trend Micro

* Zalecenie **"Zainstaluj rozwiązania programu Endpoint Protection na maszynie wirtualnej"** jest generowane, jeśli co najmniej jeden z następujących testów nie jest spełniony:
    * **HKLM: \ SOFTWARE\TrendMicro\Deep Security Agent** istnieje
    * **HKLM: \ SOFTWARE\TrendMicro\Deep Security Agent\InstallationFolder** istnieje
    * Plik **dsq_query. cmd** znajduje się w folderze instalacyjnym
    * Uruchamianie **dsa_query. cmd** wyniki z **składnikiem. am. Mode: wykryto agenta zabezpieczeń w usłudze Trend Micro głęboki**

## <a name="symantec-endpoint-protection"></a>Symantec Endpoint Protection
Zalecenie **"Zainstaluj rozwiązania programu Endpoint Protection na maszynie wirtualnej"** jest generowane, jeśli którykolwiek z następujących kontroli nie zostanie spełniony:

* **HKLM: \ Software\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM: \ Software\Symantec\Symantec punkt końcowy Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

Lub

* **HKLM: \ Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM: \ Software\Wow6432Node\Symantec\Symantec punkt końcowy Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

Zalecenie **"Rozwiąż problemy dotyczące kondycji programu Endpoint Protection na swoich maszynach"** jest generowane, jeśli którykolwiek z następujących kontroli nie zostanie spełniony:  

* Sprawdź wersję firmy Symantec > = 12:  Lokalizacja rejestru: **HKLM: \ Software\Symantec\Symantec Endpoint Protection\CurrentVersion "-value" PRODUCTVERSION "**

* Sprawdź stan ochrony w czasie rzeczywistym: **HKLM: \ Software\Wow6432Node\Symantec\Symantec punkt końcowy Protection\AV\Storages\Filesystem\RealTimeScan\OnOff = = 1**

* Sprawdź stan aktualizacji sygnatury: **HKLM\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LatestVirusDefsDate < = 7 dni**

* Sprawdź stan pełnego skanowania: **HKLM: \ Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LastSuccessfulScanDateTime < = 7 dni**

* Znajdź ścieżkę numeru wersji sygnatury do wersji sygnatury dla firmy Symantec 12: **Ścieżki rejestru + "CurrentVersion\SharedDefs" — wartość "SRTSP"** 

* Ścieżka do wersji sygnatury dla firmy Symantec 14: **Ścieżki rejestru + "CurrentVersion\SharedDefs\SDSDefs" — wartość "SRTSP"**

Ścieżki rejestru:

* **"HKLM: \ Software\Symantec\Symantec Endpoint Protection" + $Path;**
* **"HKLM: \ Software\Wow6432Node\Symantec\Symantec Endpoint Protection" + $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>Program McAfee Endpoint Protection dla systemu Windows

Zalecenie **"Zainstaluj rozwiązania programu Endpoint Protection na maszynie wirtualnej"** jest generowane, jeśli nie spełniono następujących kontroli:

* **HKLM: \ SOFTWARE\McAfee\Endpoint\AV\ProductVersion** istnieje

* **HKLM: \ SOFTWARE\McAfee\AVSolution\MCSHIELDGLOBAL\GLOBAL\enableoas = 1**

Zalecenie **"Rozwiąż problemy dotyczące kondycji programu Endpoint Protection na swoich maszynach"** jest generowane, jeśli nie spełniono następujących kontroli:

* Wersja programu McAfee: **HKLM: \ SOFTWARE\McAfee\Endpoint\AV\ProductVersion > = 10**

* Znajdź wersję podpisu: **HKLM: \ Software\McAfee\AVSolution\DS\DS-Value "dwContentMajorVersion"**

* Znajdź datę podpisu: **HKLM: \ Software\McAfee\AVSolution\DS\DS-Value "szContentCreationDate" > = 7 dni**

* Znajdź datę skanowania: **HKLM: \ Software\McAfee\Endpoint\AV\ODS-Value "LastFullScanOdsRunTime" > = 7 dni**

## <a name="mcafee-endpoint-security-for-linux-threat-prevention"></a>Zabezpieczenia programu McAfee Endpoint Protection dla ochrony przed zagrożeniami dla systemu Linux 

Zalecenia **dotyczące instalacji programu Endpoint Protection na maszynie wirtualnej** są generowane, jeśli co najmniej jeden z następujących testów nie jest spełniony:  

- Wyjście z **/opt/iSEC/ENS/threatprevention/bin/isecav** plików 

- **"/opt/iSEC/ENS/threatprevention/bin/isecav--Version"** dane wyjściowe: **McAfee Name = McAfee Endpoint Security dla ochrony przed zagrożeniami w systemie Linux i wersji McAfee > = 10**

Zalecenia dotyczące **rozwiązywania problemów z kondycją programu Endpoint Protection na komputerach** są generowane, jeśli co najmniej jeden z następujących testów nie zostanie spełniony:

- **"/opt/iSEC/ENS/threatprevention/bin/isecav--listtask"** zwraca **szybkie skanowanie, pełne skanowanie** i oba skanowania < = 7 dni

- **"/opt/iSEC/ENS/threatprevention/bin/isecav--listtask"** zwraca liczbę **dat i czas aktualizacji aparatu** , a oba z nich < = 7 dni

- **"/opt/iSEC/ENS/threatprevention/bin/isecav--getoasconfig--Summary"** zwraca stan **skanowania dostępu**

## <a name="sophos-antivirus-for-linux"></a>Sophos Antivirus dla systemu Linux 

Zalecenia **dotyczące instalacji programu Endpoint Protection na maszynie wirtualnej** są generowane, jeśli co najmniej jeden z następujących testów nie jest spełniony:

- **/Opt/Sophos-AV/bin/savdstatus** pliku lub wyszukiwanie dostosowanej lokalizacji **"readlink $ (savscan)"**

- **"/opt/Sophos-AV/bin/savdstatus--Version"** zwraca Sophos Name = **Sophos Anti-Virus i sophos w wersji > = 9**

Zalecenia dotyczące **rozwiązywania problemów z kondycją programu Endpoint Protection na komputerach** są generowane, jeśli co najmniej jeden z następujących testów nie zostanie spełniony:

- **"/opt/Sophos-AV/bin/savlog--maxAge = 7 | grep-i "zaplanowane skanowanie. ukończono\* "| ogon-1"** , zwraca wartość   

- **"/opt/Sophos-AV/bin/savlog--maxAge = 7 | Zakończono skanowanie grep** "| tail-1 "zwraca wartość   

- **"/opt/Sophos-AV/bin/savdstatus--LastUpdate"** zwraca LastUpdate, które powinny być < = 7 dni 

- **"/opt/Sophos-AV/bin/savdstatus-v"** jest równa **"skanowanie w trakcie dostępu jest uruchomione"** 

- **"/opt/Sophos-AV/bin/savconfig Get LiveProtection"** zwraca włączone  

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomocy technicznej

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Dzienniki rozszerzeń programu Microsoft chroniące przed złośliwym oprogramowaniem są dostępne pod adresem:  
**%Systemdrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.Security.IaaSAntimalware (lub PaaSAntimalware) \1.5.5.x (wersja #) \CommandExecution.log**

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz dodatkowej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ze ekspertów platformy Azure na [forów platformy Azure z subskrypcją MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Możesz też zaplikować zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj [pomocy technicznej Microsoft Azure — często zadawane pytania](https://azure.microsoft.com/support/faq/).
