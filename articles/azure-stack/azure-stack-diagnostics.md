---
title: Diagnostyka w usłudze Azure Stack
description: Jak zbierać pliki dziennika dotyczące diagnostyki w usłudze Azure Stack
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 11/13/2018
ms.author: jeffgilb
ms.reviewer: adshar
ms.openlocfilehash: f9a7ae76f2d52b3439bfb33f306e164bb81549eb
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51623982"
---
# <a name="azure-stack-diagnostics-tools"></a>Narzędzia diagnostyczne w usłudze Azure Stack

Usługa Azure Stack jest duży zbiór składników, współpracując i wchodzenie w interakcje ze sobą. Wszystkie te składniki generować własne unikatowe dzienniki. Dzięki temu może być trudniejszym zadaniem, szczególnie w przypadku błędów pochodzących z wielu interakcja składników usługi Azure Stack diagnozowanie problemów. 

Nasze narzędzia diagnostyczne pewność, że mechanizm zbierania dzienników jest proste i wydajne. Na poniższym diagramie przedstawiono sposób Zaloguj narzędzia kolekcji pracy usługi Azure Stack:

![Narzędzia diagnostyczne platformy Azure Stack](media/azure-stack-diagnostics/get-azslogs.png)
 
 
## <a name="trace-collector"></a>Moduł zbierający śledzenia
 
Moduł śledzenia jest domyślnie włączona i działa w sposób ciągły w tle, aby zbierać wszystkie dzienniki zdarzeń śledzenia dla Windows (ETW) z usługi składowe w usłudze Azure Stack. Dzienniki zdarzeń systemu Windows są przechowywane w typowych lokalny udział z limitem wiek pięć dni. Po osiągnięciu tego limitu najstarsze pliki są usuwane w miarę tworzenia nowych. Domyślny maksymalny dozwolony rozmiar każdego pliku jest 200 MB. Sprawdzanie rozmiaru jest wykonywane co 2 minuty, a jeśli bieżący plik jest > = 200 MB jest zapisywana i generowany jest nowy plik. Na całkowity rozmiar plików wygenerowanych zdarzeń sesji istnieje również limit 8 GB. 

## <a name="log-collection-tool"></a>Narzędzie do zbierania dzienników
 
Polecenia cmdlet programu PowerShell **Get AzureStackLog** może służyć do zbierania dzienników ze wszystkich składników w środowisku usługi Azure Stack. Zapisuje je w plikach zip w lokalizacji zdefiniowanej przez użytkownika. Jeśli zespół pomocy technicznej usługi Azure Stack wymaga swoje dzienniki, aby pomóc w rozwiązywaniu problemu, może poprosić Cię do uruchomienia tego narzędzia.

> [!CAUTION]
> Te pliki dzienników mogą zawierać identyfikowalne dane osobowe (PII). To brać pod uwagę przed opublikowaniem publicznie wszystkich plików dziennika.
 
Niektóre typy dziennika przykładu, które są zbierane są następujące:
*   **Dzienniki wdrożenia usługi Azure Stack**
*   **Dzienniki zdarzeń Windows**
*   **Dzienniki Panther**
*   **Dzienniki klastra**
*   **Dzienniki diagnostyczne magazynu**
*   **Dzienniki zdarzeń systemu Windows**

Te pliki są zbierane i zapisywane w udziale przez moduł śledzenia. **Get AzureStackLog** polecenia cmdlet programu PowerShell, następnie może służyć do zbierania je, gdy jest to konieczne.

### <a name="to-run-get-azurestacklog-on-azure-stack-integrated-systems"></a>Aby uruchomić Get AzureStackLog w usłudze Azure Stack zintegrowane systemy 
Aby uruchomić narzędzie do zbierania dzienników w zintegrowanym systemie, musisz mieć dostęp do uprzywilejowanych punktu końcowego (program ten). Poniżej przedstawiono przykładowy skrypt można uruchomić za pomocą program ten do zbierania dzienników na zintegrowanym systemie:

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
 
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
 
$shareCred = Get-Credential
 
$s = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2)  #provide the time that includes the period for your issue
 
Invoke-Command -Session $s {    Get-AzureStackLog -OutputSharePath "<EXTERNAL SHARE ADDRESS>" -OutputShareCredential $using:shareCred  -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate}

if($s)
{
    Remove-PSSession $s
}
```
- Parametry **OutputSharePath** i **OutputShareCredential** są używane do przechowywania dzienników w określonego przez użytkownika lokalizacji.
- **FromDate** i **ToDate** parametrów może służyć do zbierania dzienników dla określonego okresu. Jeśli nie określono parametrów, dzienniki są zbierane domyślnie w ciągu ostatnich czterech godzin.


### <a name="to-run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system"></a>Aby uruchomić Get AzureStackLog w systemie Azure Stack Development Kit (ASDK)
1. Zaloguj się jako **AzureStack\CloudAdmin** na hoście.
2. Otwórz okno programu PowerShell jako administrator.
3. Uruchom **Get AzureStackLog** polecenia cmdlet programu PowerShell.

**Przykłady:**

  Zbierz wszystkie dzienniki dla wszystkich ról:

  ```powershell
  Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred
  ```

  Zbieranie dzienników z ról maszyn wirtualnych i BareMetal:

  ```powershell
  Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal
  ```

  Zbieranie dzienników z ról maszyn wirtualnych i BareMetal z datą filtrowania dla plików dziennika w ciągu ostatnich 8 godzin:
    
  ```powershell
  Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8)
  ```

  Zbieranie dzienników z ról maszyn wirtualnych i BareMetal z datą filtrowania dla plików dziennika w okresie między 8 godz i 2 godz. temu:

  ```powershell
  Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)
  ```

### <a name="parameter-considerations-for-both-asdk-and-integrated-systems"></a>Parametr zagadnienia dotyczące ASDK i zintegrowanych systemów

- Jeśli **FromDate** i **ToDate** parametry nie są określone, domyślnie dzienniki są zbierane w ciągu ostatnich czterech godzin.
- Użyj **FilterByNode** parametru, aby filtrować dzienniki według nazwy komputera. Na przykład:

    ```powershell
    Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred -FilterByNode azs-xrp01
    ```
- Użyj **FilterByLogType** parametru, aby filtrować dzienniki według typu. Można filtrować według plików, udziału lub WindowsEvent. Na przykład:

    ```powershell
    Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred -FilterByLogType File
    ```
- Możesz użyć **TimeOutInMinutes** parametru, aby ustawić limit czasu zbierania dzienników. Jego jest domyślnie do 150 (2,5 godziny).
- Zbieranie dzienników plik zrzutu jest domyślnie wyłączona. Aby ją włączyć, należy użyć **IncludeDumpFile** parametr przełącznika. 
- Obecnie można używać **FilterByRole** parametru, aby zbieranie danych dziennika filtrowania przez następujące role:

 |   |   |   |    |
 | - | - | - | -  |   
 |ACS                   |CacheService                   |IBC                            |Producent OEM|
 |ACSDownloadService    |Wystąpienia obliczeniowe                        |InfraServiceController         |OnboardRP|
 |ACSFabric             |CPI                            |KeyVaultAdminResourceProvider  |ŚRODOWISKA PXE|
 |ACSFrontEnd           |CRP                            |KeyVaultControlPlane           |QueryServiceCoordinator|
 |ACSMetrics            |DeploymentMachine              |KeyVaultDataPlane              |QueryServiceWorker|
 |ACSMigrationService   |DiskRP                         |KeyVaultInternalControlPlane   |SeedRing|
 |ACSMonitoringService  |Domain                         |KeyVaultInternalDataPlane      |SeedRingServices|
 |ACSSettingsService    |ECE                            |KeyVaultNamingService          |SLB|
 |ACSTableMaster        |EventAdminRP                   |MDM                            |SQL|
 |ACSTableServer        |EventRP                        |MetricsAdminRP                 |SRP   |
 |ACSWac                |ExternalDNS                    |MetricsRP                      |Magazyn|
 |ADFS                  |FabricRing                     |MetricsServer                  |StorageController   |
 |ApplicationController |FabricRingServices             |MetricsStoreService            |URP   |
 |ASAppGateway          |FirstTierAggregationService    |MonAdminRP                     |UsageBridge|
 |AzureBridge           |FRP                            |MonRP                          |VirtualMachines   |
 |AzureMonitor          |Brama                        |KONTROLER SIECI                             |WAS|
 |BareMetal             |HealthMonitoring               |NonPrivilegedAppGateway        |WASPUBLIC|
 |BRP                   |HintingServiceV2               |DOSTAWCA NRP                            |   |
 |CA                    |HRP                            |OboService                     |   |
 |   |   |   |    |

### <a name="additional-considerations"></a>Dodatkowe zagadnienia

* Polecenie wymaga pewnego czasu do uruchomienia w oparciu o na role, które dzienniki są zbierane dane. Powiązanych czynników także czas trwania określonych dla zbieranie dzienników i liczby węzłów w środowisku usługi Azure Stack.
* Jak rejestrować uruchomień kolekcji, sprawdź nowy folder utworzony w **OutputSharePath** parametr określony w poleceniu.
* Każda rola ma jej dzienników wewnątrz zip poszczególnych plików. W zależności od rozmiaru zebranych dzienników roli może być jej dzienników podzielony na wiele plików zip. W roli Jeśli chcesz mieć wszystkich plików dziennika rozpakowano w jednym folderze użycie narzędzia, które można rozpakować zbiorczo (na przykład 7zip). Zaznacz wszystkie pliki zip dla roli, a następnie wybierz pozycję **wyodrębnić tutaj**. To unzips wszystkich plików dziennika dla tej roli w jednym folderze scalone.
* Plik o nazwie **Get AzureStackLog_Output.log** również jest tworzony w folderze, który zawiera pliki zip dziennika. Ten plik jest dziennika danych wyjściowych polecenia, który może służyć do rozwiązywania problemów podczas zbierania dzienników. Czasami plik dziennika zawiera `PS>TerminatingError` wpisów, które można bezpiecznie zignorować, chyba że pliki dziennika w oczekiwanym brakuje po dziennika uruchomień kolekcji.
* Aby zbadać określonego błędu, dzienniki mogą być potrzebne z więcej niż jednego składnika.
    -   System i dzienniki zdarzeń dla wszystkich maszyn wirtualnych infrastruktury, które są zbierane w *VirtualMachines* roli.
    -   System i dzienniki zdarzeń dla wszystkich hostów, które są zbierane w *BareMetal* roli.
    -   Dzienniki zdarzeń klastra trybu failover i funkcji Hyper-V są zbierane w *magazynu* roli.
    -   Zbierane są dzienniki usługi ACS w *magazynu* i *ACS* ról.

> [!NOTE]
> Rozmiar i wiek limity są wymuszane w dziennikach zbieranych jako istotne jest zapewnienie efektywnego wykorzystania miejsca do magazynowania, aby upewnić się, że nie nastąpi pobranie propagowane za pomocą dzienników. Podczas diagnozowania problemu czasami potrzebny, dzienników, które już nie istnieje ze względu na limity. Dlatego jest **zdecydowanie zaleca się** czy odciążania dzienniki do miejsca do magazynowania zewnętrznych (konto magazynu na platformie Azure, urządzenie magazynu lokalnego dodatkowe itp.) co 8 do 12 godzin i Zatrzymaj ich tam 1 – 3 miesięcy, w zależności od usługi wymagania. Ponadto upewnij się, że ta lokalizacja magazynu jest zaszyfrowany.

## <a name="next-steps"></a>Kolejne kroki
[Rozwiązywanie problemów z usługi Microsoft Azure Stack](azure-stack-troubleshooting.md)

