---
title: Punkt końcowy ochrony rozwiązania odnajdywania i kondycji oceny w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: Jak rozwiązań ochrony punktów końcowych są odnajdywane i zidentyfikowane jako w dobrej kondycji.
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
ms.date: 05/23/2019
ms.author: v-mohabe
ms.openlocfilehash: b17e5f16b988bfa562b00bc6f5b9dfd34be4ca43
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66247967"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Ocena ochrony punktu końcowego i zalecenia w usłudze Azure Security Center

Ocena ochrony punktu końcowego i zalecenia w usłudze Azure Security Center wykrywa i umożliwia ocenę kondycji [obsługiwane](https://docs.microsoft.com/azure/security-center/security-center-os-coverage#supported-platforms-for-windows-computers-and-vms) wersje rozwiązań ochrony punktów końcowych. W tym temacie opisano scenariusze, które generują następujące dwa zaleceń dotyczących rozwiązań ochrony punktów końcowych przez usługę Azure Security Center.

* **Zainstalować rozwiązanie do ochrony punktu końcowego na maszynie wirtualnej.**
* **Rozwiązywanie problemów dotyczących kondycji ochrony punktu końcowego na komputerach**

## <a name="windows-defender"></a>Windows Defender

* **"Instalowanie rozwiązań ochrony punktów końcowych na maszynie wirtualnej"** zalecenie jest generowany podczas [Get MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) przebiegi i wynik jest **AMServiceEnabled: False**

* **"Rozwiązywanie problemów dotyczących kondycji ochrony punktu końcowego na maszynach"** zalecenie jest generowany podczas [Get MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) występuje przebiegi i jednego lub obu z następujących czynności:

  * Co najmniej jeden z następujących właściwości ma wartość false:

     **AMServiceEnabled**

     **AntispywareEnabled**

     **RealTimeProtectionEnabled**

     **BehaviorMonitorEnabled**

     **IoavProtectionEnabled**

     **OnAccessProtectionEnabled**

  * Jeśli co najmniej jeden z następujących właściwości jest większy lub równy 7.

     **AntispywareSignatureAge**

     **AntivirusSignatureAge**

## <a name="microsoft-system-center-endpoint-protection"></a>Programu Microsoft System Center endpoint protection

* **"Instalowanie rozwiązań ochrony punktów końcowych na maszynie wirtualnej"** zalecenie jest generowany podczas importowania **SCEPMpModule ("$env: Client\MpProvider\MpProvider.psd1 zabezpieczeń folderze ProgramFiles\Microsoft")** i uruchamianie **Get MProtComputerStatus** wyników z **AMServiceEnabled = false**

* **"Rozwiązywanie problemów dotyczących kondycji ochrony punktu końcowego na maszynach"** zalecenie jest generowany podczas **Get MprotComputerStatus** występuje przebiegi i jednego lub obu z następujących czynności:

    * Co najmniej jeden z następujących właściwości ma wartość false:

       **AMServiceEnabled**
    
       **AntispywareEnabled**
    
       **RealTimeProtectionEnabled**
    
       **BehaviorMonitorEnabled**
    
       **IoavProtectionEnabled**
    
       **OnAccessProtectionEnabled**
          
    * Jeśli jeden lub oba następujące aktualizacje podpisu jest większy lub równy 7. 

       **AntispywareSignatureAge**
    
       **AntivirusSignatureAge**

## <a name="trend-micro"></a>Trend Micro

* **"Instalowanie rozwiązań ochrony punktów końcowych na maszynie wirtualnej"** zalecenie jest generowany, jeśli jeden lub więcej następujące testy nie są spełnione:
    * **Agent zabezpieczeń HKLM:\SOFTWARE\TrendMicro\Deep** istnieje
    * **Agent\InstallationFolder zabezpieczeń HKLM:\SOFTWARE\TrendMicro\Deep** istnieje
    * **Dsq_query.cmd** plik znajduje się w folderze instalacyjnym
    * Uruchamianie **dsa_query.cmd** wyników z **Component.AM.mode: na — Trend Micro Deep Security Agent wykrył**

## <a name="symantec-endpoint-protection"></a>Rozwiązanie Symantec endpoint protection
**"Instalowanie rozwiązań ochrony punktów końcowych na maszynie wirtualnej"** zalecenie jest generowany, gdy dowolne następujące testy nie są spełnione:

* **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **Punkt końcowy HKLM:\Software\Symantec\Symantec Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

Lub

* **Protection\CurrentVersion\PRODUCTNAME punktu końcowego HKLM:\Software\Wow6432Node\Symantec\Symantec = "Aplikacji Symantec Endpoint Protection"**

* **Punkt końcowy HKLM:\Software\Wow6432Node\Symantec\Symantec Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

**"Rozwiązywanie problemów dotyczących kondycji ochrony punktu końcowego na maszynach"** zalecenie jest generowany, gdy dowolne następujące testy nie są spełnione:  

* Zapoznaj się z wersją programu Symantec > = 12:  Lokalizacja w rejestrze: **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion" -Value "PRODUCTVERSION"**

* Sprawdź stan ochrony w czasie rzeczywistym: **Protection\AV\Storages\Filesystem\RealTimeScan\OnOff punktu końcowego HKLM:\Software\Wow6432Node\Symantec\Symantec == 1**

* Sprawdź stan sygnatury aktualizacji: **Punkt końcowy HKLM\Software\Symantec\Symantec Protection\CurrentVersion\public-opstate\LatestVirusDefsDate < = 7 dni**

* Sprawdź stan pełne skanowanie: **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LastSuccessfulScanDateTime <= 7 days**

* Znaleźć numer wersji podpisu ścieżki do sygnatury wersji 12 firmy Symantec: **Ścieżki rejestru + "CurrentVersion\SharedDefs"-wartość "SRTSP"** 

* Ścieżka do sygnatury wersji dla firmy Symantec 14: **Registry Paths+ "CurrentVersion\SharedDefs\SDSDefs" -Value "SRTSP"**

Ścieżki rejestru:

**"HKLM:\Software\Symantec\Symantec Endpoint Protection" + $Path;** 
 **"HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection" + $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>McAfee programu endpoint protection for Windows

**"Instalowanie rozwiązań ochrony punktów końcowych na maszynie wirtualnej"** zalecenie jest generowany, gdy nie są spełnione następujące testy:

* **HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion** istnieje

* **HKLM:\SOFTWARE\McAfee\AVSolution\MCSHIELDGLOBAL\GLOBAL\enableoas = 1**

**"Rozwiązywanie problemów dotyczących kondycji ochrony punktu końcowego na maszynach"** zalecenie jest generowany, gdy nie są spełnione następujące testy:

* Wersja McAfee: **HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion > = 10**

* Znajdź wersję podpisu: **HKLM:\Software\McAfee\AVSolution\DS\DS-wartość "dwContentMajorVersion"**

* Znajdź Data podpisu: **HKLM:\Software\McAfee\AVSolution\DS\DS-wartość "szContentCreationDate" > = 7 dni**

* Znajdź Data skanowania: **HKLM:\Software\McAfee\Endpoint\AV\ODS-wartość "LastFullScanOdsRunTime" > = 7 dni**

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomocy technicznej

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Microsoft Antimalware rozszerzenia dzienniki są dostępne pod adresem:  
**%SYSTEMDRIVE%\WindowsAzure\Logs\Plugins\Microsoft.Azure.Security.IaaSAntimalware (\CommandExecution.log lub PaaSAntimalware)\1.5.5.x (wersja #)**

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz dodatkowej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ze ekspertów platformy Azure na [forów platformy Azure z subskrypcją MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Lub mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj [pomocy technicznej Microsoft Azure — często zadawane pytania](https://azure.microsoft.com/support/faq/).
