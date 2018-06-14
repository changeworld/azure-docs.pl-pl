---
title: Aktualizacja Azure stosu 1710 (kompilacja 20171020.1) | Dokumentacja firmy Microsoft
description: Więcej informacji na temat nowości w aktualizacji 1710 stosu Azure zintegrowanych systemów, znane problemy i pobierania aktualizacji.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 135314fd-7add-4c8c-b02a-b03de93ee196
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: mabrigg
ms.openlocfilehash: 8c7c39ecdc332c994e5c00f8415462f208e7d20b
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
ms.locfileid: "30311927"
---
# <a name="azure-stack-1710-update-build-201710201"></a>Aktualizacja Azure stosu 1710 (kompilacja 20171020.1)

*Dotyczy: Azure stosu zintegrowane systemy*

W tym artykule opisano ulepszenia i poprawki w tym pakiecie aktualizacji znane problemy dotyczące tej wersji i pobierania aktualizacji. Znane problemy są podzielone na znane problemy bezpośrednio związane z procesem aktualizacji i znane problemy związane z kompilacją (po instalacji).

> [!IMPORTANT]
> Ten pakiet aktualizacji jest tylko dla stosu Azure zintegrowanych systemów. Nie dotyczą tego pakietu aktualizacji Azure stosu Development Kit.

## <a name="improvements-and-fixes"></a>Ulepszeń i poprawek

Ta aktualizacja obejmuje następujące ulepszenia jakości i poprawki dla stosu Azure.
 
### <a name="windows-server-2016-improvements-and-fixes"></a>Ulepszenia w systemie Windows Server 2016 i poprawki

- Aktualizacje systemu Windows Server 2016: 10 października 2017 — KB4041691 (kompilacja 14393.1770 systemu operacyjnego. Zobacz [ https://support.microsoft.com/help/4041691 ](https://support.microsoft.com/help/4041691) Aby uzyskać więcej informacji.

### <a name="additional-quality-improvements-and-fixes"></a>Dodatkową jakością ulepszeń i poprawek

- Dodane polecenia cmdlet programu PowerShell uprzywilejowanych punktu końcowego, ułatwiające rozwiązywanie problemów z serwerem i zaktualizować protokołu NTP (Network Time).
- Dodano obsługę aktualizacja modułów punktu końcowego tylko tyle administracyjnej (JEA) uprzywilejowany punktu końcowego i polecenia cmdlet listy dozwolonych adresów IP. 
- Błędy stałym język lokalny uprzywilejowanych punktu końcowego.
- Dodano możliwość obracania poświadczeń bramy.
- Usunięte CBLocalAdmin konta administratora lokalnego. 
- Stałe zawartość szablonu alertów pulsu aby upewnij się, że Puls alerty pracy prawidłowo po aktualizacji.
- Rozwiązany przez dostawcę zasobów sieci szkieletowej postępowania w przypadku przekroczenia limitu czasu podczas operacji FRU. 
- Dodano możliwość deweloperom chmury profile usługi Azure Resource Manager interfejsu API Azure stosu.
- Wyłączyć usługę Windows Update na maszynie wirtualnej wdrożenia (Menedżer DVM). 
- Usunięte zasilania węzła lub wyłącza akcje w interfejsie użytkownika.
- Różne inne wydajności i stabilności poprawki. 
 
## <a name="known-issues-with-the-update-process"></a>Znane problemy związane z procesem aktualizacji

Ta sekcja zawiera znane problemy, które mogą wystąpić podczas instalacji aktualizacji 1710.

> [!IMPORTANT]
> W przypadku niepowodzenia aktualizacji później podczas Wznów aktualizację, należy użyć `Resume-AzureStackUpdate` polecenia cmdlet z uprzywilejowanego punktu końcowego. Wznawia aktualizacji za pomocą portalu administratora. (Jest to znany problem w tej wersji). Aby uzyskać więcej informacji, zobacz [monitorowania aktualizacji w stosie Azure przy użyciu punktu końcowego uprzywilejowanych](azure-stack-monitor-update.md).

| Objaw  | Przyczyna  | Rozwiązanie |
|---------|---------|---------|
|Po wykonaniu aktualizacji wystąpił błąd podobny do następującego<br>mogą wystąpić podczas wykonywania kroku "Magazynu hostów ponownie uruchomić węzeł magazynu"<br> Aktualizacja planu działań.<br><br>**{"name": "Uruchom ponownie magazynu hostów", "opis": "Uruchom ponownie<br> magazynu hostów. komunikat o błędzie","": "wpisz"Restart"roli<br> "BareMetal"zgłoszony wyjątek: \n\n komputera<br> HostName-05 zostanie pominięta. Nie można pobrać jej LastBootUpTime<br> za pomocą usługi WMI z powodu następującego błędu:<br> serwer RPC jest niedostępny.<br> (Wyjątek od HRESULT: 0x800706BA). \nat ponownego uruchomienia komputera hosta** | Przyczyną tego problemu jest potencjalnych uszkodzony sterownik istnieje w niektórych konfiguracjach. | 1. Zaloguj się do interfejsu sieci web uzyskiwania informacji na temat kontrolera zarządzania płytą główną i uruchom ponownie hosta, który określono w komunikacie o błędzie.<br><br>2. Wznów aktualizacji przy użyciu uprzywilejowanych punktu końcowego. |
| Podczas wykonywania aktualizacji proces aktualizacji pojawi się do staluj<br> i nie wprowadzać postępu po wykonaniu kroku "krok: uruchamiania kroku 2.4 — Instalacja<br> Aktualizuj"planu działania aktualizacji.<br><br>Ten krok jest po niej serii procesu kopiowania .nupkg<br> pliki do wewnętrznej infrastruktury udziałów plików. Na przykład:<br><br>**Kopiowanie plików 1 z content\PerfCollector\VirtualMachines do <br> \VirtualMachineName-ERCS03\C$\TraceCollectorUpdate\ <br>PerfCounterConfiguration**<br><br>Lub zostanie wyświetlony komunikat:<br><br>**WarningMessage:Task: Wywołanie interfejsu "LiveUpdate"<br> roli "Cloud\Fabric\VirtualMachines" nie powiodło się:<br> wpisz "LiveUpdate" roli "VirtualMachines" wywoływane<br> wyjątek: nie ma wystarczającej ilości miejsca na dysku .**  | Przyczyną problemu jest pliki dziennika zapełnia się dyski na maszynie wirtualnej infrastruktury i problemu w systemu Windows Server skalowalnego w poziomie serwerem plików (SOFS) które zostaną dostarczone w kolejnych aktualizacji. | Skontaktuj się z Microsoft dział obsługi klienta i pomocy technicznej (CSS), aby uzyskać pomoc. | 
| Po wykonaniu aktualizacji wystąpił błąd podobny do następującego<br> mogą wystąpić podczas wykonywania kroku "krok: uruchamiania kroku 2.13.2 — aktualizacja<br> *VM_Name*"planu aktualizacji działania. (Maszyny wirtualnej<br> Nazwa może być inna.)<br><br>**ActionPlanInstanceWarning NZ/MachineName:<br> WarningMessage:Task: wywołanie interfejsu "LiveUpdate" z<br> roli "Cloud\Fabric\WAS" nie powiodło się: typ "LiveUpdate" roli<br> '' zgłoszono wyjątek: Wystąpił błąd podczas Magazyn<br> inicjowania: Wystąpił błąd podczas próby nawiązania interfejsu API<br> wywołań do usługi Microsoft Storage: {"Komunikat": "limit czasu<br> podczas komunikacji z sieci szkieletowej usług.<br> Typ wyjątku: TimeoutException.<br> Komunikat o wyjątku: Upłynął limit czasu operacji. "}**  | Przyczyną problemu jest limit czasu operacji We/Wy w systemie Windows Server, który zostanie rozwiązany w kolejnych aktualizacji. | Aby uzyskać pomoc, skontaktuj się z Microsoft CSS.
| Po wykonaniu aktualizacji wystąpił błąd podobny do następującego<br> mogą wystąpić podczas wykonywania kroku "krok maszyn wirtualnych 21 ponowne uruchomienie programu SQL server".<br><br>**Wpisz "LiveUpdateRestart" roli "VirtualMachines" wywoływane<br> wyjątek: VerboseMessage: [VirtualMachines:LiveUpdateRestart]<br> wykonywania zapytania dotyczącego MachineName maszyny Wirtualnej — Sql01. - 10/13/2017 5:23:50: 00 VerboseMessage: [maszyn wirtualnych: LiveUpdateRestart]<br> maszyny Wirtualnej jest oznaczony jako dużej dostępności. — 10/13/2017 5:23:50: 00<br> VerboseMessage: [VirtualMachines:LiveUpdateRestart] w<br>MS. Internal.ServerClusters.ExceptionHelp.Build na<br>MS. Internal.ServerClusters.ClusterResource.BeginTakeOffline<br>(wartość logiczna force) w Microsoft.FailoverClusters.PowerShell.<br> StopClusterResourceCommand.BeginTimedOperation() na <br>Microsoft.FailoverClusters.PowerShell.TimedCmdlet.Wrapped<br>ProcessRecord() na Microsoft.FailoverClusters.PowerShell.<br> FCCmdlet.ProcessRecord() - 10/13/2017 5:23:50: 00 ostrzeżenie<br>wiadomości: zadanie: wywołanie interfejsu "LiveUpdateRestart" z<br> roli "Cloud\Fabric\VirtualMachines" nie powiodło się:** | Ten problem może wystąpić, jeśli maszyna wirtualna nie może uruchomić ponownie. | Aby uzyskać pomoc, skontaktuj się z Microsoft CSS.
| Podczas wykonywania aktualizacji może wystąpić błąd podobny do następującego:<br><br>**2017-10-22T01:37:37.5369944Z typu "Zamknij" rola "SQL"<br> zgłosił wyjątek: wstrzymywanie węzła wystąpił błąd<br> klientem "s45r1004 Sql01" Stop-SQL, C:\ProgramData\SF\ErcsClusterNode2 <br>\Fabric\work\ Applications\ EnterpriseCloud <br>EngineApplicationType&#95;App1\ <br>EnterpriseCloudEngineServicePkg.Code.1.0.597.18\ <br> CloudDeployment\Roles\SQL\SQL.psm1:line 542 na<br> Zamknięcie, C:\ProgramData\SF\ErcsClusterNode2\Fabric\work\ <br>\EnterpriseCloudEngineApplicationType aplikacji&#95;App1\ <br>EnterpriseCloudEngineServicePkg.Code.1.0.597.18\Cloud<br> Deployment\Classes\SQL\SQL.psm1: wiersz 50 w < ScriptBlock&#62;,<br> <No file>: 18 na wiersz < ScriptBlock&#62;, < plik nie&#62;: wiersz 16** | Ten problem może wystąpić, jeśli nie można umieścić maszyny wirtualnej w stanie wstrzymanym, aby opróżnić dziennik ról. | Aby uzyskać pomoc, skontaktuj się z Microsoft CSS.
| Po wykonaniu aktualizacji może wystąpić jeden z następujących błędów:<br><br>**Typ "Weryfikuj" roli "Usług AD FS" zgłosił wyjątek: weryfikacji<br> dla roli usług AD FS/grafu nie powiodła się z powodu błędu: błąd podczas sprawdzania usług AD FS<br> sondowania punktu końcowego *endpoint_URI*: wywołaniu wyjątek<br> " GetResponse"z"0"argumentów:" serwer zdalny<br> zwróciło błąd: (503) serwer jest niedostępny. "w Invoke -<br>ADFSGraphValidation**<br><br>**Typ "Weryfikuj" roli "Usług AD FS" zgłosił wyjątek: weryfikacji<br> dla roli usług AD FS/grafu nie powiodła się z powodu błędu: błąd podczas uzyskiwania<br> właściwości usługi AD FS: nie może połączyć się <br>NET.TCP://localhost: 1500/zasady. Próba nawiązania połączenia trwała<br> dla czasu trwania 00:00:02.0498923. Kod błędu TCP<br> 10061: połączenie nie może zostać ustanowione, ponieważ element docelowy<br> maszyny aktywnie go odmawia 127.0.0.1:1500.<br> na Invoke ADFSGraphValidation** | Plan akcji update nie może sprawdzić poprawności kondycji usługi Active Directory Federation Services (AD FS). | Aby uzyskać pomoc, skontaktuj się z Microsoft CSS.

## <a name="known-issues-post-installation"></a>Znane problemy (po instalacji)

Ta sekcja zawiera po instalacji, znane problemy związane z 20171020.1 kompilacji.

### <a name="portal"></a>Portal

- Może nie być możliwe do wyświetlania zasobów obliczeniowych i magazynu w portalu administratora. Oznacza to, że wystąpił błąd podczas instalowania aktualizacji i aktualizacji niepoprawnie Zgłoszono następujący powiodło się. Jeśli ten problem występuje, skontaktuj się z CSS firmy Microsoft, aby uzyskać pomoc.
- Może pojawić się pustego pulpitu nawigacyjnego w portalu. Aby odzyskać pulpitu nawigacyjnego, wybierz koło zębate ikonę w prawym górnym rogu portalu, a następnie wybierz **przywrócić ustawienia domyślne**.
- Po wyświetleniu właściwości grupy zasobów, **Przenieś** przycisk jest niedostępny. To zachowanie jest oczekiwane. Przeniesienie grup zasobów między subskrypcjami nie jest obecnie obsługiwane.
- Na każdy przepływ pracy, gdzie wybierz subskrypcji, grupy zasobów lub lokalizacji w listy rozwijanej może wystąpić jeden lub więcej z następujących problemów:

   - Może pojawić się pusty wiersz na początku listy. Nadal można wybrać elementu zgodnie z oczekiwaniami.
   - Jeśli na liście elementów na liście rozwijanej jest krótki, nie można przeglądać nazwy elementu.
   - Jeśli masz wiele subskrypcji użytkownika listy rozwijanej grupy zasobów może być pusta. 

   Aby obejść problemy z ostatnich dwóch, możesz wpisać nazwę subskrypcji lub grupy zasobów (jeśli ją znasz), lub można użyć programu PowerShell.
- Usunięcie użytkownika powoduje subskrypcje zasoby oddzielone. Jako obejście najpierw usuń zasoby użytkownika lub grupy zasobów całej, a następnie usuń subskrypcji użytkownika.
- Nie jest możliwe wyświetlić uprawnienia do subskrypcji przy użyciu portali stosu Azure. Jako rozwiązanie alternatywne można sprawdzić uprawnień za pomocą programu PowerShell.
- **Kondycja usługi** bloku nie udało się załadować. Po otwarciu bloku usługi kondycji w portalu administratora lub użytkownika, stosu Azure jest wyświetlany błąd i nie zostanie załadowany informacji. Jest to oczekiwane zachowanie. Chociaż można wybrać i otworzyć usługi kondycji, ta funkcja nie jest jeszcze dostępny, ale będzie wykonywany w przyszłych wersjach programu Azure stosu.
 

### <a name="backup"></a>Backup

- Nie należy włączać infrastruktura kopii zapasowej na **infrastruktura kopii zapasowej** bloku.

### <a name="health-and-monitoring"></a>Monitorowania kondycji i

- Jeśli ponowne uruchomienie wystąpienia roli infrastruktury, może zostać wyświetlony komunikat informujący, że ponowne uruchomienie nie powiodło się. Jednak ponownego uruchamiania faktycznie zakończyło się pomyślnie.

### <a name="marketplace"></a>Portal Marketplace
- Podczas dodawania elementów do stosu Azure marketplace przy użyciu **Dodaj z platformy Azure** opcji, nie wszystkie elementy mogą być widoczne do pobrania.
- Użytkownicy można przeglądać pełnego portalu marketplace bez subskrypcji i widoczne elementy administracyjne, takie jak plany i oferty. Te elementy są niefunkcjonalne dla użytkowników.

### <a name="compute"></a>Wystąpienia obliczeniowe
- Użytkownicy skorzystać z opcji, aby utworzyć maszynę wirtualną z magazynu geograficznie nadmiarowego. Ta konfiguracja powoduje niepowodzenie tworzenia maszyny wirtualnej.
- Można skonfigurować dostępność maszyn wirtualnych, ustaw tylko z jednej domeny błędów i jedną domenę aktualizacji.
- Brak nie doświadczenie marketplace, aby utworzyć zestawy skalowania maszyny wirtualnej. Można utworzyć skali ustawić za pomocą szablonu.
- Ustawienia skalowania dla zestawy skalowania maszyny wirtualnej nie są dostępne w portalu. Jako rozwiązanie alternatywne można zastosować [programu Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Z powodu różnic wersji programu PowerShell, należy użyć `-Name` parametru zamiast `-VMScaleSetName`.
 
### <a name="networking"></a>Networking
- Nie można utworzyć modułu równoważenia obciążenia z publicznym adresem IP, za pomocą portalu. Jako rozwiązanie alternatywne można utworzyć modułu równoważenia obciążenia za pomocą programu PowerShell.
- Podczas tworzenia modułu równoważenia obciążenia sieciowego, należy utworzyć regułę translatora adresów sieciowych adres. Jeśli nie, otrzymasz wystąpił błąd podczas próby dodania reguły NAT po utworzeniu usługi równoważenia obciążenia.
- Nie można usunąć skojarzenie publicznego adresu IP z maszyną wirtualną (VM), po utworzeniu maszyny Wirtualnej i skojarzonych z tym adresem IP. Usuwanie skojarzeń pojawi się do pracy, ale poprzednio przypisanych publiczny adres IP pozostają skojarzone z oryginalna maszyna wirtualna. Dzieje się tak nawet w przypadku ponownego przypisywania adresów IP do nowej maszyny Wirtualnej (nazywane *wymiany wirtualnych adresów IP*). Wszystkie przyszłe próbuje nawiązać połączenie za pośrednictwem tego wyniku adresów IP w przypadku połączenia pierwotnie skojarzonego VM, a nie nowy. Obecnie tylko musi używać nowego publiczne adresy IP do tworzenia nowej maszyny Wirtualnej.
 
### <a name="sqlmysql"></a>SQL/MySQL
- Może potrwać do godziny dzierżawców można utworzyć bazy danych w nowym SQL lub MySQL jednostki SKU. 
- Tworzenie elementów bezpośrednio na SQL i MySQL serwerów, które nie są wykonywane przez dostawcę zasobów hosta nie jest obsługiwana i może spowodować niezgodne stanu.
 
### <a name="app-service"></a>App Service
- Użytkownik musi zarejestrować dostawcy zasobów magazynu, przed utworzeniem ich pierwszej funkcji platformy Azure w ramach subskrypcji.
 
### <a name="field-replaceable-unit-fru-procedures"></a>Pole procedury zamiennych (FRU)

- Podczas przebiegu aktualizacji nie są poprawianie obrazów w trybie offline. Jeśli chcesz zamienić węzła jednostki skalowania, współpracować z dostawcą sprzętu przez producenta OEM w celu upewnij się, że węzeł zastąpionego zawiera najnowsze poziomu poprawki.

## <a name="download-the-update"></a>Pobierz aktualizację

Możesz pobrać pakiet aktualizacji 1710 [tutaj](https://aka.ms/azurestackupdatedownload).

## <a name="next-steps"></a>Kolejne kroki

- Omówienie zarządzania aktualizacjami w stosie Azure, zobacz [zarządzania aktualizacjami w omówieniu stosu Azure](azure-stack-updates.md).
- Aby uzyskać informacje dotyczące stosowania aktualizacji, zobacz [stosowanie aktualizacji w stosie Azure](azure-stack-apply-updates.md).
