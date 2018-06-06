---
title: Diagnostyka w usłudze Azure Stack
description: Jak zbierać pliki dziennika diagnostyki Azure stosu
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 06/05/2018
ms.author: jeffgilb
ms.reviewer: adshar
ms.openlocfilehash: b966ed4f1a9a8e659fbce185a807573d5321b251
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801657"
---
# <a name="azure-stack-diagnostics-tools"></a>Narzędzia diagnostyki stosu Azure

Stos Azure jest duży zbiór elementów pracy ze sobą i interakcji ze sobą. Wszystkie te składniki generowania unikatowych dzienników. To ułatwia diagnozowanie problemów lada wyzwanie, szczególnie w przypadku błędów pochodzących z wielu interakcji składniki stosu Azure. 

Upewnij się, że mechanizm kolekcji dziennika jest łatwe i skuteczne pomoc do narzędzi naszych diagnostyki. Na poniższym diagramie przedstawiono sposób dziennika narzędzia kolekcji w pracach Azure stosu:

![Azure stosu narzędzia diagnostyczne](media/azure-stack-diagnostics/get-azslogs.png)
 
 
## <a name="trace-collector"></a>Moduł zbierający śledzenia
 
Moduł zbierający śledzenia jest domyślnie włączona i działa w sposób ciągły w tle, aby zbierać wszystkie dzienniki zdarzeń śledzenia dla systemu Windows (ETW) z usługi składowe stosu Azure. Dzienniki zdarzeń systemu Windows są przechowywane w udziale lokalnym wspólnej limit wieku pięć dni. Po osiągnięciu tego limitu najstarsze pliki są usuwane, ponieważ są tworzone nowe. Domyślny maksymalny rozmiar dozwolony dla każdego pliku jest 200 MB. Sprawdzanie rozmiaru występuje co 2 minuty, a jeśli bieżący plik jest > = 200 MB jest zapisywany i utworzony nowy plik. Na rozmiar pliku generowane zdarzenie sesji istnieje również limit 8 GB. 

## <a name="log-collection-tool"></a>Narzędzie do zbierania dzienników
 
Polecenia cmdlet programu PowerShell **Get-AzureStackLog** może służyć do zbierania dzienników z wszystkich składników w środowisku Azure stosu. Zapisuje je w plikach zip w lokalizacji zdefiniowanej przez użytkownika. Jeśli zespołowi pomocy technicznej platformy Azure stos musi dzienników rozwiązywania problemu, ich może poprosić o uruchamianiu tego narzędzia.

> [!CAUTION]
> Te pliki dziennika może zawierać dane osobowe (dane osobowe). To brać pod uwagę przed publicznie post wszystkie pliki dziennika.
 
Poniżej przedstawiono kilka przykładowych typów dziennika, które są zbierane:
*   **Dzienniki wdrożenia usługi Azure stosu**
*   **Dzienniki zdarzeń systemu Windows**
*   **Dzienniki Panther**
*   **Dzienniki klastra**
*   **Dzienniki diagnostyczne magazynu**
*   **Dzienniki zdarzeń systemu Windows**

Te pliki są zbierane i zapisywane w udziale przez moduł zbierający śledzenia. **Get-AzureStackLog** polecenia cmdlet programu PowerShell można następnie używany do gromadzenia je w razie potrzeby.
 
### <a name="to-run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system"></a>Aby uruchomić Get AzureStackLog w systemie Azure stosu Development Kit (ASDK)
1. Zaloguj się jako **AzureStack\CloudAdmin** na hoście.
2. Otwórz okno programu PowerShell jako administrator.
3. Uruchom **Get-AzureStackLog** polecenia cmdlet programu PowerShell.

**Przykłady:**

  Zbieraj wszystkie dzienniki dla wszystkich ról:

  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs
  ```

  Zbierz dzienniki ról maszyn wirtualnych i BareMetal:

  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal
  ```

  Zbierz dzienniki ról maszyn wirtualnych i BareMetal, oraz datę filtrowania dla plików dziennika przez ostatnie 8 godzin:
    
  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8)
  ```

  Zbierz dzienniki ról maszyn wirtualnych i BareMetal, oraz datę filtrowania dla plików dziennika w okresie między 8 godz i 2 godz. temu:

  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)
  ```

### <a name="to-run-get-azurestacklog-on-azure-stack-integrated-systems-version-1804-and-later"></a>Do uruchomienia Get-AzureStackLog na stosie Azure zintegrowanych wersji systemów 1804 i nowsze

Aby uruchomić narzędzie do zbierania dzienników na zintegrowany system, należy mieć dostęp do uprzywilejowanych punktu końcowego (program ten). Poniżej przedstawiono przykładowy skrypt można uruchomić przy użyciu program ten zbierania dzienników na zintegrowany system:

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

- Parametry **OutputSharePath** i **OutputShareCredential** są używane do przekazywania dzienników do zewnętrznego folderu udostępnionego.
- Jak pokazano w poprzednim przykładzie **FromDate** i **ToDate** parametry może służyć do zbierania dzienników dla danego okresu. To są dostępne w przydatne w scenariuszach, takich jak zbieranie dzienników po zastosowaniu pakietu aktualizacji na zintegrowany system.


### <a name="to-run-get-azurestacklog-on-azure-stack-integrated-systems-version-1803-and-earlier"></a>Do uruchomienia Get-AzureStackLog na stosie Azure zintegrowanych wersji systemów 1803 lub starszej

Aby uruchomić narzędzie do zbierania dzienników na zintegrowany system, należy mieć dostęp do uprzywilejowanych punktu końcowego (program ten). Poniżej przedstawiono przykładowy skrypt można uruchomić przy użyciu program ten zbierania dzienników na zintegrowany system:

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
 
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
 
$shareCred = Get-Credential
 
$s = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2)  #provide the time that includes the period for your issue
 
Invoke-Command -Session $s {    Get-AzureStackLog -OutputPath "\\<HLH MACHINE ADDRESS>\c$\logs" -OutputSharePath "<EXTERNAL SHARE ADDRESS>" -OutputShareCredential $using:shareCred  -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate}

if($s)
{
    Remove-PSSession $s
}
```

- Zbieranie dzienników z program ten, określić **OutputPath** parametr do lokalizacji na komputerze hosta cyklu życia sprzętu (HLH). Również upewnić się, że lokalizacja jest zaszyfrowany.
- Parametry **OutputSharePath** i **OutputShareCredential** są opcjonalne i są używane podczas przekazywania dzienników do zewnętrznego folderu udostępnionego. Użyj tych parametrów *dodatkowo* do **OutputPath**. Jeśli **OutputPath** nie zostanie określony, narzędzie do zbierania dzienników używa dysku systemowego maszyny wirtualnej program ten magazyn. Może to spowodować skryptu, aby zakończyć się niepowodzeniem, ponieważ miejsce na dysku jest ograniczone.
- Jak pokazano w poprzednim przykładzie **FromDate** i **ToDate** parametry może służyć do zbierania dzienników dla danego okresu. To są dostępne w przydatne w scenariuszach, takich jak zbieranie dzienników po zastosowaniu pakietu aktualizacji na zintegrowany system.


### <a name="parameter-considerations-for-both-asdk-and-integrated-systems"></a>Zagadnienia dotyczące parametru dla ASDK i zintegrowanych systemów

- Jeśli **FromDate** i **ToDate** parametry nie są określone, dzienniki są domyślnie zbierane przez ostatnie cztery godziny.
- Można użyć **TimeOutInMinutes** parametr, aby ustawić limit czasu zbierania dzienników. Domyślnie jest ustawiona na 150 (2,5 godzin).
- W wersji 1805 i nowszych zbierania dzienników plik zrzutu jest domyślnie wyłączona. Aby go włączyć, należy użyć **IncludeDumpFile** przełącznik parametru. 
- Obecnie można używać **FilterByRole** parametru do kolekcji filtrów dziennika przez następujące role:

   |   |   |   |
   | - | - | - |
   | ACS                    | DeploymentMachine                | NC                         |
   | ACSBlob                | DiskRP                           | Sieć                    |
   | ACSFabric              | Domena                           | NonPrivilegedAppGateway    |
   | ACSFrontEnd            | ECE                              | DOSTAWCA NRP                        |
   | ACSMetrics             | ExternalDNS                      | Producent OEM                        |
   | ACSMigrationService    | Fabric                           | ŚRODOWISKO PXE                        |
   | ACSMonitoringService   | FabricRing                       | SeedRing                   | 
   | ACSSettingsService     | FabricRingServices               | SeedRingServices           |
   | ACSTableMaster         | FRP                              | SLB                        |   
   | ACSTableServer         | Galeria                          | SlbVips                    |
   | ACSWac                 | Brama                          | SQL                        |   
   | ADFS                   | HealthMonitoring                 | SRP                        |
   | ASAppGateway           | HRP                              | Magazyn                    |   
   | NCAzureBridge          | IBC                              | StorageAccounts            |    
   | AzurePackConnector     | IdentityProvider                 | StorageController          |  
   | AzureStackBitlocker    | międzynarodowych nazw domen.                             | Dzierżawa                     |
   | BareMetal              | InfraServiceController           | TraceCollector             |
   | BRP                    | Infrastruktura                   | URP                        |
   | Urząd certyfikacji                     | KeyVaultAdminResourceProvider    | UsageBridge                |
   | Chmura                  | KeyVaultControlPlane             | VirtualMachines            |
   | Klaster                | KeyVaultDataPlane                | WAS                        |
   | Wystąpienia obliczeniowe                | KeyVaultInternalControlPlane     | WASBootstrap               |
   | CPI                    | KeyVaultInternalDataPlane        | WASPUBLIC                  |
   | CRP                    | KeyVaultNamingService            |                            |
   | DatacenterIntegration  | MonitoringAgent                  |                            |
   |                        |                                  |                            |

### <a name="bkmk_gui"></a>Zbieranie dzienników przy użyciu graficznego interfejsu użytkownika
Zamiast dostarczanie wymaganych parametrów polecenia cmdlet Get-AzureStackLog pobrać dzienników stosu Azure, można też skorzystać narzędzia Azure stosu dostępne typu open source, znajduje się w głównym stosu Azure narzędzia narzędzia repozytorium GitHub w http://aka.ms/AzureStackTools.

**ERCS_AzureStackLogs.ps1** skrypt programu PowerShell znajduje się w repozytorium GitHub narzędzia i są aktualizowane na bieżąco. Aby upewnić się, że masz najnowszej dostępnej wersji, należy pobrać go bezpośrednio z http://aka.ms/ERCS. Uruchomiony administracyjne sesji programu PowerShell, skrypt nawiązuje połączenie z punktem końcowym uprzywilejowanych i uruchamia Get AzureStackLog z podanych parametrów. Jeśli są podane żadne parametry, skrypt domyślnie monitowania parametrów za pomocą graficznego interfejsu użytkownika.

Aby dowiedzieć się więcej na temat skryptów środowiska PowerShell ERCS_AzureStackLogs.ps1, obejrzyj [krótki film](https://www.youtube.com/watch?v=Utt7pLsXEBc) i wyświetlanie skryptu [plik readme](https://github.com/Azure/AzureStack-Tools/blob/master/Support/ERCS_Logs/ReadMe.md) znajduje się w repozytorium GitHub narzędzia Azure stosu. 

### <a name="additional-considerations"></a>Dodatkowe zagadnienia

* Polecenie wymaga pewnego czasu do uruchomienia w oparciu o role, które są zbierane w dziennikach. Czynników także czas trwania zbierania dzienników i liczby węzłów w środowisku Azure stosu.
* Jak rejestrować uruchamia kolekcji, sprawdź nowy folder utworzony w **OutputSharePath** określony w poleceniu parametru.
* Każda rola ma jej dzienników wewnątrz zip poszczególnych plików. W zależności od rozmiaru zebranych dzienniki rola może być jej dzienników podzielony na wiele plików zip. Dla roli Jeśli chcesz, aby rozpakować w celu pojedynczy folder plików dziennika, użycie narzędzia, które można rozpakować zbiorczo (na przykład 7zip). Zaznacz wszystkie pliki zip dla roli, a następnie wybierz **wyodrębnić tutaj**. To unzips wszystkich plików dziennika dla tej roli w jednym folderze scalone.
* Plik o nazwie **Get AzureStackLog_Output.log** również jest tworzony w folderze, który zawiera pliki dziennika zip. Ten plik znajduje się w dzienniku danych wyjściowych polecenia, które mogą służyć do rozwiązywania problemów podczas zbierania dzienników. Czasami plik dziennika zawiera `PS>TerminatingError` wpisów, które można bezpiecznie zignorować, chyba że brakuje plików dziennika w oczekiwanym po dziennika uruchamia kolekcji.
* Do sprawdzania, czy określony błąd, dzienniki mogą być wymagane z więcej niż jednego składnika.
    -   W dziennikach zdarzeń dla wszystkich maszyn wirtualnych infrastruktury i systemu są gromadzone w *VirtualMachines* roli.
    -   System i dzienniki zdarzeń, dla wszystkich hostów są gromadzone w *BareMetal* roli.
    -   Dzienniki zdarzeń klastra trybu failover i funkcja Hyper-V są gromadzone w *magazynu* roli.
    -   Usługi ACS dzienniki są gromadzone w *magazynu* i *ACS* ról.

> [!NOTE]
> Limity rozmiaru i wiek są wymuszane na dzienniki zebrane, ponieważ jest, aby zapewnić jego efektywne wykorzystanie przestrzeni dyskowej, aby upewnić się, że nie pobierają rozpływową z dziennikami. Podczas diagnozowania problemu czasami potrzebny, dzienniki, które już nie istnieje z powodu tych ograniczeń. W związku z tym jest **zdecydowanie zalecane** czy odciążania dzienników do obszaru magazynu zewnętrznego (konto magazynu na platformie Azure, urządzenie magazynu lokalnego dodatkowe itp.) co 8 do 12 godzin i przechowywać je tam 1 – 3 miesiące, w zależności od sieci wymagania. Ponadto upewnij się, że ta lokalizacja magazynu jest zaszyfrowany.

## <a name="next-steps"></a>Kolejne kroki
[Rozwiązywanie problemów z Microsoft Azure stosu](azure-stack-troubleshooting.md)

