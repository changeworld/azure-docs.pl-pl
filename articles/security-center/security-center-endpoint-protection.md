---
title: Zalecenia dotyczące ochrony punktów końcowych w centrach zabezpieczeń platformy Azure
description: Jak rozwiązania ochrony punktu końcowego są odnajdowane i identyfikowane jako w dobrej kondycji.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2730a2f5-20bc-4027-a1c2-db9ed0539532
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/29/2019
ms.author: memildin
ms.openlocfilehash: dcf7df501665ea3885d00b9f7668a95cbbf02428
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78208546"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Ocena i zalecenia dotyczące ochrony punktów końcowych w usłudze Azure Security Center

Usługa Azure Security Center udostępnia oceny kondycji [obsługiwanych](security-center-services.md#endpoint-supported) wersji rozwiązań ochrony punktów końcowych. W tym artykule opisano scenariusze, które prowadzą Centrum zabezpieczeń do generowania następujących dwóch zaleceń:

* **Instalowanie rozwiązań ochrony punktów końcowych na maszynie wirtualnej**
* **Rozwiązywanie problemów ze zdrowiem związanym z ochroną punktów końcowych na komputerach**

## <a name="windows-defender"></a>Windows Defender

* Usługa Security Center zaleca **"Instalowanie rozwiązań ochrony punktów końcowych na maszynie wirtualnej"** po uruchomieniu [get-mpcomputerstatus,](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) a wynikiem jest **AMServiceEnabled: False**

* Usługa Security Center zaleca **"Rozwiązywanie problemów ze zdrowiem punktu końcowego na komputerach"** po uruchomieniu [get-mpcomputerstatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) i wystąpi dowolna z następujących czynności:

  * Każda z następujących właściwości są fałszywe:

    **AmServiceEnabled (Usługa AMService)**

    **AntispywareEnabled (Niech)**

    **RealTimeProtectionEnabled**

    **BehaviorMonitorEnabled (Nie można pojąć)**

    **IoavProtectionEnabled**

    **OnAccessProtectionEnabled**

  * Jeśli jedna lub obie z następujących właściwości są 7 lub więcej.

    **AntispywareSignatureAge**

    **AntivirusSasignage**

## <a name="microsoft-system-center-endpoint-protection"></a>Ochrona punktów końcowych programu Microsoft System Center

* Centrum zabezpieczeń zaleca **"Instalowanie rozwiązań ochrony punktów końcowych na maszynie wirtualnej"** podczas importowania **scepmpmodule ("$env:ProgramFiles\Microsoft Security Client\MpProvider\MpProvider.psd1")** i uruchamianie wyników **Get-MProtComputerStatus** z **AMServiceEnabled = false**

* Usługa Security Center zaleca **"Rozwiązywanie problemów ze zdrowiem punktu końcowego na komputerach"** po uruchomieniu **get-mprotcomputerstatus** i wystąpi dowolna z następujących czynności:

    * Co najmniej jedna z następujących właściwości jest false:

            **AMServiceEnabled**

            **AntispywareEnabled**
    
            **RealTimeProtectionEnabled**
    
            **BehaviorMonitorEnabled**
    
            **IoavProtectionEnabled**
    
            **OnAccessProtectionEnabled**
          
    * Jeśli jedna lub obie z następujących aktualizacji podpisu jest większa lub równa 7. 

            **AntispywareSignatureAge**
    
            **AntivirusSignatureAge**

## <a name="trend-micro"></a>Trend Micro

* Usługa Security Center zaleca **"Instalowanie rozwiązań ochrony punktów końcowych na maszynie wirtualnej",** gdy którykolwiek z następujących kontroli nie jest spełniony:
    * **HKLM:\SOFTWARE\TrendMicro\Deep Security Agent** istnieje
    * **HKLM:\SOFTWARE\TrendMicro\Deep Security Agent\InstallationFolder** istnieje
    * Plik **dsa_query.cmd** znajduje się w folderze instalacyjnym
    * Uruchamianie **wyników dsa_query.cmd** z **trybem Component.AM.: on - Wykryto program Trend Micro Deep Security Agent**

## <a name="symantec-endpoint-protection"></a>Ochrona punktów końcowych firmy Symantec
Usługa Security Center zaleca **"Instalowanie rozwiązań ochrony punktów końcowych na maszynie wirtualnej",** gdy którykolwiek z następujących kontroli nie jest spełniony:

* **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

Lub

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

Usługa Security Center zaleca **"Rozwiązywanie problemów ze zdrowiem punktu końcowego na komputerach",** gdy którykolwiek z następujących kontroli nie jest spełniony:

* Sprawdź wersję firmy Symantec >= 12: Lokalizacja rejestru: **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion" -Value "PRODUCTVERSION"**

* Sprawdzanie stanu ochrony w czasie rzeczywistym: **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\AV\Storages\Filesystem\RealTimeScan\OnOff == 1**

* Sprawdź stan aktualizacji podpisu: **HKLM\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LatestVirusDefsDate <= 7 days**

* Sprawdź stan pełnego skanowania: **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LastSuccessfulScanDateTime <= 7 days**

* Znajdź numer wersji podpisu Ścieżka do wersji podpisu dla programu Symantec 12: **Ścieżki rejestru+ "CurrentVersion\SharedDefs" -Value "SRTSP"** 

* Ścieżka do wersji podpisu dla programu Symantec 14: **Ścieżki rejestru+ "CurrentVersion\SharedDefs\SDSDefs" -Value "SRTSP"**

Ścieżki rejestru:

* **"HKLM:\Software\Symantec\Symantec Endpoint Protection" + $Path;**
* **"HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection" + $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>Ochrona punktów końcowych mcafee dla systemu Windows

Usługa Security Center zaleca **"Instalowanie rozwiązań ochrony punktów końcowych na maszynie wirtualnej",** gdy którykolwiek z następujących kontroli nie jest spełniony:

* **HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion** istnieje

* **HKLM:\SOFTWARE\McAfee\AVSolution\MCSHIELDGLOBAL\GLOBAL\enableoas = 1**

Usługa Security Center zaleca **"Rozwiązywanie problemów ze zdrowiem punktu końcowego na komputerach",** gdy którykolwiek z następujących kontroli nie jest spełniony:

* Wersja McAfee: **HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion >= 10**

* Znajdź wersję podpisu: **HKLM:\Software\McAfee\AVSolution\DS\DS -Value "dwContentMajorVersion"**

* Znajdź datę podpisu: **HKLM:\Software\McAfee\AVSolution\DS\DS -Value "szContentCreationDate" >= 7 dni**

* Znajdź datę skanowania: **HKLM:\Software\McAfee\Endpoint\AV\ODS -Value "LastFullScanOdsRunTime" >= 7 dni**

## <a name="mcafee-endpoint-security-for-linux-threat-prevention"></a>McAfee Endpoint Security for Linux Threat Prevention 

Usługa Security Center zaleca **"Instalowanie rozwiązań ochrony punktów końcowych na maszynie wirtualnej",** gdy którykolwiek z następujących kontroli nie jest spełniony:

- Wyjścia pliku **/opt/isec/ens/threatprevention/bin/isecav** 

- **"/opt/isec/ens/threatprevention/bin/isecav --version"** jest: **Nazwa McAfee = McAfee Endpoint Security for Linux Threat Prevention i Wersja McAfee >= 10**

Usługa Security Center zaleca **"Rozwiązywanie problemów ze zdrowiem punktu końcowego na komputerach",** gdy którykolwiek z następujących kontroli nie jest spełniony:

- **"/opt/isec/ens/threatprevention/bin/isecav --listtask"** zwraca **Szybkie skanowanie, Pełne skanowanie** i oba skany <= 7 dni

- **"/opt/isec/ens/threatprevention/bin/isecav --listtask"** zwraca **DAT i silnik Czas aktualizacji** i oba <= 7 dni

- **"/opt/isec/ens/threatprevention/bin/isecav --getoasconfig --summary"** zwraca stan **Skanowania dostępu**

## <a name="sophos-antivirus-for-linux"></a>Sophos Antivirus dla Linuksa 

Usługa Security Center zaleca **"Instalowanie rozwiązań ochrony punktów końcowych na maszynie wirtualnej",** gdy którykolwiek z następujących kontroli nie jest spełniony:

- Plik **/opt/sophos-av/bin/savdstatus** wychodzi lub wyszukaj niestandardową lokalizację **"readlink $(which savscan)"**

- **"/opt/sophos-av/bin/savdstatus --version"** zwraca nazwę Sophos = **Wersja Sophos Anti-Virus i Sophos >= 9**

Usługa Security Center zaleca **"Rozwiązywanie problemów ze zdrowiem punktu końcowego na komputerach",** gdy którykolwiek z następujących kontroli nie jest spełniony:

- **"/opt/sophos-av/bin/savlog --maxage=7 | grep -i "Zaplanowane skanowanie . \* zakończone" | tail -1"**, zwraca wartość

- **"/opt/sophos-av/bin/savlog --maxage=7 | grep "skanowanie zakończone"** | tail -1", zwraca wartość

- **"/opt/sophos-av/bin/savdstatus --lastupdate"** zwraca lastUpdate, który powinien być <= 7 dni 

- **"/opt/sophos-av/bin/savdstatus -v"** jest równe **"Skanowanie przy dostępie jest uruchomione"** 

- **"/opt/sophos-av/bin/savconfig get LiveProtection"** zwraca włączony

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomoc techniczna

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Dzienniki rozszerzeń ochrony przed złośliwym oprogramowaniem firmy Microsoft są dostępne pod adresem: **%Systemdrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.Security.IaaSAntimalware(Or PaaSAntimalware)\1.5.5.x(version#)\CommandExecution.log**

### <a name="support"></a>Pomoc techniczna

Aby uzyskać więcej pomocy, skontaktuj się z ekspertami platformy Azure na [forach MSDN Azure i Stack Overflow](https://azure.microsoft.com/support/forums/). Lub złożyć zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc techniczną. Aby uzyskać informacje na temat korzystania z pomocy technicznej platformy Azure, przeczytaj często zadawane [pytania dotyczące pomocy technicznej platformy Microsoft Azure](https://azure.microsoft.com/support/faq/).