---
title: Usługa Azure Stack w wersji 1710 aktualizacji (kompilacja 20171020.1) | Dokumentacja firmy Microsoft
description: Więcej informacji na temat nowości w aktualizacji 1710 usługi Azure Stack zintegrowanych systemów, znanych problemów i umożliwiające pobranie aktualizacji.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 135314fd-7add-4c8c-b02a-b03de93ee196
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: cf870551a3dbd9b5ea0ef6f886dc6451e43b2c25
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2018
ms.locfileid: "44377194"
---
# <a name="azure-stack-1710-update-build-201710201"></a>Usługa Azure Stack w wersji 1710 aktualizacji (kompilacja 20171020.1)

*Dotyczy: zintegrowane systemy usługi Azure Stack*

W tym artykule opisano ulepszeń i poprawek dotyczących tego pakietu aktualizacji, znane problemy dotyczące tej wersji i gdzie można pobrać aktualizację. Znane problemy są podzielone na znane problemy bezpośrednio związane z procesem aktualizacji i znane problemy z kompilacją (po instalacji).

> [!IMPORTANT]
> Ten pakiet aktualizacji jest tylko dla usługi Azure Stack, zintegrowanych systemów. Nie dotyczą tego pakietu aktualizacji usługi Azure Stack Development Kit.

## <a name="improvements-and-fixes"></a>Ulepszenia i poprawki

Ta aktualizacja obejmuje następujące ulepszenia jakości i poprawki dla usługi Azure Stack.
 
### <a name="windows-server-2016-improvements-and-fixes"></a>System Windows Server 2016 ulepszenia i poprawki

- Aktualizacje dla systemu Windows Server 2016: 10 października 2017 r. — KB4041691 (14393.1770 numer kompilacji systemu operacyjnego. Zobacz [ https://support.microsoft.com/help/4041691 ](https://support.microsoft.com/help/4041691) Aby uzyskać więcej informacji.

### <a name="additional-quality-improvements-and-fixes"></a>Dodatkową jakością ulepszeń i poprawek

- Dodano polecenia cmdlet programu PowerShell uprzywilejowanych punktu końcowego ułatwiające rozwiązywanie problemów z serwerem i zaktualizować protokołu NTP (Network Time).
- Dodano obsługę aktualizowanie modułów punktu końcowego tylko tyle administracji (JEA) uprzywilejowanych punktu końcowego i polecenia cmdlet listy dozwolonych adresów. 
- Naprawiono błędy język lokalny punkt końcowy uprzywilejowanych.
- Dodano możliwość obracania poświadczeń bramy.
- Usunięte CBLocalAdmin lokalnego konta administratora. 
- Naprawiono zawartość szablonu alertów pulsu aby upewnić się, że Puls alerty pracy prawidłowo po aktualizacji.
- Naprawiono dostawcy zasobów sieci szkieletowej radzenia sobie z przekroczeń limitu czasu podczas wykonywania operacji FRU. 
- Dodano możliwość dla deweloperów rozwiązań w chmurze do użycia z profilami interfejsu API usługi Azure Resource Manager w usłudze Azure Stack.
- Wyłączyć usługę Windows Update na maszynie wirtualnej wdrożenia (Menedżer DVM). 
- Usunięty węzeł możliwości włączenia/wyłączenia akcji z poziomu interfejsu użytkownika.
- Różne inne wydajności i stabilności poprawki. 
 
## <a name="known-issues-with-the-update-process"></a>Znane problemy związane z procesem aktualizacji

Ta sekcja zawiera znane problemy, które można napotkać podczas instalowania aktualizacji 1710.

> [!IMPORTANT]
> W przypadku niepowodzenia aktualizacji później podczas do wznowienia aktualizacji, należy użyć `Resume-AzureStackUpdate` polecenia cmdlet z uprzywilejowanym punktu końcowego. Wznawia aktualizację, korzystając z portalu administratora. (Jest to znany problem w tej wersji). Aby uzyskać więcej informacji, zobacz [monitorowanie aktualizacji w usłudze Azure Stack przy użyciu uprzywilejowanych punktu końcowego](azure-stack-monitor-update.md).

| Objaw  | Przyczyna  | Rozwiązanie |
|---------|---------|---------|
|Podczas wykonywania aktualizacji, błąd podobny do następującego<br>mogą wystąpić podczas wykonywania kroku "Magazynu hostów ponowne uruchomienie węzła magazynu"<br> Aktualizacja planu działania.<br><br>**{"name": "Uruchom ponownie magazynu hostów", "description": "Uruchom ponownie<br> magazynu hostów.","komunikat o błędzie": "wpisz"Restart", roli<br> "BareMetal"zgłoszony wyjątek: \n\n komputera<br> HostName-05 zostanie pominięta. Nie można pobrać jej LastBootUpTime<br> za pośrednictwem usługi WMI, za pomocą następujący komunikat o błędzie:<br> serwer RPC jest niedostępny.<br> (Wyjątek od HRESULT: 0x800706BA). ponowne uruchomienie \nat-Host** | Ten problem jest spowodowany potencjalnych uszkodzony sterownik istnieje w niektórych konfiguracjach. | 1. Zaloguj się do interfejsu sieci web uzyskiwania informacji na temat kontrolera zarządzania płytą główną i uruchom ponownie hosta, która jest identyfikowana w komunikacie o błędzie.<br><br>2. Wznów aktualizacji za pomocą uprzywilejowanych punktu końcowego. |
| Podczas wykonywania aktualizacji proces aktualizacji, zostanie wyświetlony zostanie zatrzymane<br> i nie robić postępów po wykonaniu kroku "krok: uruchamianie kroku 2.4 — Instalacja<br> Aktualizuj"planu działania aktualizacji.<br><br>W tym kroku po niej szereg kopiowania procesy .nupkg<br> pliki do udziałów plików wewnętrznej infrastruktury. Na przykład:<br><br>**Kopiowanie plików 1 z content\PerfCollector\VirtualMachines do <br> \VirtualMachineName-ERCS03\C$\TraceCollectorUpdate\ <br>PerfCounterConfiguration**<br><br>Lub, zostanie wyświetlony komunikat:<br><br>**WarningMessage:Task: Wywołanie interfejsu "LiveUpdate"<br> roli "Cloud\Fabric\VirtualMachines" nie powiodło się:<br> wpisz "LiveUpdate" roli "VirtualMachines" zgłoszone<br> wyjątek: nie ma wystarczającej ilości miejsca na dysku .**  | Problem jest spowodowany przez pliki dziennika zapełnia się dyski na maszynie wirtualnej infrastruktury i problemów w systemu Windows Server skalowalnego w poziomie serwera plików (SOFS), które zostaną dostarczone w kolejnych aktualizacji. | Poproś o pomoc dział obsługi klienta firmy Microsoft i pomocy technicznej (CSS). | 
| Podczas wykonywania aktualizacji, błąd podobny do następującego<br> mogą wystąpić podczas wykonywania kroku "krok: uruchamianie kroku 2.13.2 - Update<br> *VM_Name*"planu aktualizacji działania. (Maszyny wirtualnej<br> Nazwa może być inna).<br><br>**ActionPlanInstanceWarning ONZ/MachineName:<br> WarningMessage:Task: wywołanie interfejsu "LiveUpdate" z<br> roli "Cloud\Fabric\WAS" nie powiodło się: "LiveUpdate" Typ roli<br> "" zgłoszono wyjątek: Wystąpił błąd podczas Magazyn<br> inicjowania: Wystąpił błąd podczas próby uzyskania interfejsu API<br> wywołań do usługi Microsoft Storage: {"Message": "Przekroczono limit czasu<br> wystąpił podczas komunikacji z usługą Service Fabric.<br> Typ wyjątku: TimeoutException.<br> Komunikat o wyjątku: Upłynął limit czasu operacji. "}**  | Problem jest spowodowany przekroczenia limitu czasu operacji We/Wy w systemie Windows Server, które zostaną rozwiązane w kolejnych aktualizacji. | Aby uzyskać pomoc, skontaktuj się z pomocą firmy Microsoft CSS.
| Podczas wykonywania aktualizacji, błąd podobny do następującego<br> mogą wystąpić podczas wykonywania kroku "krok maszyny wirtualne z 21 ponowne uruchomienie programu SQL server".<br><br>**Wpisz "LiveUpdateRestart" roli "VirtualMachines" zgłoszone<br> wyjątek: VerboseMessage: [VirtualMachines:LiveUpdateRestart]<br> wykonywaniem zapytań dotyczących maszyny Wirtualnej MachineName-Sql01. - 10/13/2017 5:23:50: 00 VerboseMessage: [maszyn wirtualnych: LiveUpdateRestart]<br> maszyna wirtualna jest oznaczana jako dużej dostępności. - 10/13/2017 5:23:50: 00<br> VerboseMessage: [VirtualMachines:LiveUpdateRestart] w<br>MS. Internal.ServerClusters.ExceptionHelp.Build na<br>MS. Internal.ServerClusters.ClusterResource.BeginTakeOffline<br>(wartość logiczna force) w Microsoft.FailoverClusters.PowerShell.<br> StopClusterResourceCommand.BeginTimedOperation() na <br>Microsoft.FailoverClusters.PowerShell.TimedCmdlet.Wrapped<br>ProcessRecord() na Microsoft.FailoverClusters.PowerShell.<br> FCCmdlet.ProcessRecord() - 10/13/2017 5:23:50: 00 ostrzeżenie<br>wiadomości: zadanie: wywołanie interfejsu "LiveUpdateRestart" z<br> roli "Cloud\Fabric\VirtualMachines" nie powiodło się:** | Ten problem może wystąpić, jeśli maszyna wirtualna nie może uruchomić ponownie. | Aby uzyskać pomoc, skontaktuj się z pomocą firmy Microsoft CSS.
| Podczas wykonywania aktualizacji może wystąpić błąd podobny do następującego:<br><br>**2017-10-22T01:37:37.5369944Z typu "Zamknij" w roli "SQL"<br> zgłosił wyjątek: Wystąpił błąd, node wstrzymywanie<br> klientem "s45r1004 Sql01" Stop-SQL, C:\ProgramData\SF\ErcsClusterNode2 <br>\Fabric\work\ Applications\ EnterpriseCloud <br>EngineApplicationType&#95;App1\ <br>EnterpriseCloudEngineServicePkg.Code.1.0.597.18\ <br> CloudDeployment\Roles\SQL\SQL.psm1:line 542 na<br> Zamknięcie, C:\ProgramData\SF\ErcsClusterNode2\Fabric\work\ <br>\EnterpriseCloudEngineApplicationType aplikacje&#95;App1\ <br>EnterpriseCloudEngineServicePkg.Code.1.0.597.18\Cloud<br> Deployment\Classes\SQL\SQL.psm1: wiersz 50 w < ScriptBlock&#62;,<br> <No file>: 18 na wiersz < ScriptBlock&#62;, < plik nie&#62;: wiersz 16** | Ten problem może wystąpić, jeśli nie można umieścić maszyny wirtualnej w stanie wstrzymania, aby opróżnić ról. | Aby uzyskać pomoc, skontaktuj się z pomocą firmy Microsoft CSS.
| Podczas wykonywania aktualizacji może wystąpić jeden z następujących błędów:<br><br>**Typ "Weryfikuj" roli "ADFS" zgłosił wyjątek: Sprawdzanie poprawności<br> dla roli usług AD FS/wykresu nie powiodło się z powodu błędu: błąd podczas sprawdzania usług AD FS<br> sondy punktu końcowego *endpoint_URI*: wywołanie wyjątku<br> " Metody GetResponse"z"0"następującej liczby argumentów::" serwer zdalny<br> zwróciło błąd: (503) serwer jest niedostępny. "w Invoke -<br>ADFSGraphValidation**<br><br>**Typ "Weryfikuj" roli "ADFS" zgłosił wyjątek: weryfikacji<br> dla roli usług AD FS/wykresu nie powiodło się z powodu błędu: błąd podczas pobierania<br> właściwości usług AD FS: nie można połączyć z <br>NET.TCP://localhost: 1500/zasady. Próba nawiązania połączenia trwała<br> dla 00:00:02.0498923 przedział czasu. Kod błędu TCP<br> 10061: nie można ustanowić połączenia ponieważ element docelowy<br> maszyny aktywnie go odmawia 127.0.0.1:1500.<br> na Invoke ADFSGraphValidation** | Ten plan działania aktualizacji nie można zweryfikować kondycji usługi Active Directory Federation Services (AD FS). | Aby uzyskać pomoc, skontaktuj się z pomocą firmy Microsoft CSS.

## <a name="known-issues-post-installation"></a>Znane problemy (po instalacji)

Ta sekcja zawiera po instalacji, znane problemy związane z kompilacji 20171020.1.

### <a name="portal"></a>Portal

- Może nie być możliwe do wyświetlania zasobów obliczeniowych i magazynu w portalu administratora. Oznacza to, że wystąpił błąd podczas instalacji aktualizacji oraz aktualizacji niepoprawnie zgłoszono pomyślnym przeprowadzeniu. Jeśli wystąpi ten problem, skontaktuj się z firmy Microsoft CSS, aby uzyskać pomoc.
- Może zostać wyświetlony pustego pulpitu nawigacyjnego w portalu. Aby odzyskać pulpitu nawigacyjnego, wybierz ikonę koła zębatego w prawym górnym rogu portalu, a następnie wybierz **przywrócić ustawienia domyślne**.
- Podczas przeglądania właściwości grupy zasobów, **przenieść** przycisk jest wyłączony. To zachowanie jest oczekiwane. Przeniesienie grup zasobów między subskrypcjami nie jest obecnie obsługiwane.
- Dla dowolnego wybierzesz subskrypcji, grupy zasobów lub lokalizację na liście rozwijanej przepływu pracy może wystąpić co najmniej jeden z następujących problemów:

   - Może zostać wyświetlony pusty wiersz, w górnej części listy. Nadal można wybrać elementu, zgodnie z oczekiwaniami.
   - Jeśli na liście elementów na liście rozwijanej jest krótki, nie można przeglądać nazwy elementu.
   - Jeśli masz wiele subskrypcji użytkownika, na liście rozwijanej grupy zasobów może być pusta. 

   Aby obejść ostatnie dwa problemy, możesz wpisać nazwę subskrypcji lub grupy zasobów (jeśli je znasz) lub zamiast tego użyj programu PowerShell.
- Usuwanie subskrypcji użytkownika powoduje zasoby oddzielone. Jako obejście najpierw usuń zasoby użytkowników lub całą grupę zasobów, a następnie usuń subskrypcji użytkownika.
- Nie jest możliwe wyświetlić uprawnienia do subskrypcji przy użyciu portali usługi Azure Stack. Obejście tego problemu można sprawdzić uprawnień przy użyciu programu PowerShell.
- **Service Health** bloku nie można załadować. Po otwarciu bloku kondycja usługi w portalu administratora lub użytkownika usługi Azure Stack wyświetla komunikat o błędzie i nie ładuje informacje. Jest to oczekiwane zachowanie. Chociaż można wybrać i otworzyć Service Health, ta funkcja nie jest jeszcze dostępna, ale będą realizowane w przyszłych wersjach programu Azure Stack.
 

### <a name="backup"></a>Backup

- Tworzenie kopii zapasowych nie jest włączona dla **tworzenie kopii zapasowych** bloku.

### <a name="health-and-monitoring"></a>Monitorowanie kondycji i

- Jeśli ponowne uruchomienie wystąpienia roli infrastruktury, otrzymasz komunikat wskazujący, że ponowne uruchomienie nie powiodło się. Jednak ponowne uruchamianie faktycznie zakończyło się pomyślnie.

### <a name="marketplace"></a>Portal Marketplace
- Podczas próby dodania elementów portalu Marketplace usługi Azure Stack przy użyciu **Dodaj na platformie Azure** opcji, nie wszystkie elementy mogą być widoczne do pobrania.
- Użytkownicy mogą przeglądać pełny rynek bez subskrypcji i można zobaczyć elementy administracyjne, takie jak plany i oferty. Te elementy są nie działa dla użytkowników.

### <a name="compute"></a>Wystąpienia obliczeniowe
- Użytkownicy otrzymają możliwość utworzenia maszyny wirtualnej z magazynu geograficznie nadmiarowego. Ta konfiguracja powoduje, że niepowodzenie tworzenia maszyny wirtualnej.
- Można skonfigurować zestaw tylko z jednej domeny błędów i domeny aktualizacji w jednej dostępności maszyny wirtualnej.
- Nie ma żadnych środowisko witryny marketplace, aby utworzyć zestawy skalowania maszyn wirtualnych. Można utworzyć za pomocą szablonu zestawu skalowania.
- Ustawienia skalowania dla zestawów skalowania maszyn wirtualnych nie są dostępne w portalu. Jako obejście tego problemu, możesz użyć [programu Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Ze względu na różnice dotyczące wersji programu PowerShell, należy użyć `-Name` parametr zamiast `-VMScaleSetName`.
 
### <a name="networking"></a>Networking
- Nie można utworzyć moduł równoważenia obciążenia z publicznym adresem IP, za pomocą portalu. Obejść ten problem można użyć programu PowerShell do tworzenia modułu równoważenia obciążenia.
- Podczas tworzenia modułu równoważenia obciążenia sieciowego, należy utworzyć regułę translatora (NAT) adres sieci. W przeciwnym wypadku otrzymasz błąd podczas próby dodania reguły translatora adresów Sieciowych, po utworzeniu modułu równoważenia obciążenia.
- Nie można usunąć skojarzenie publicznego adresu IP z maszyną wirtualną (VM), po utworzeniu maszyny Wirtualnej i skojarzonych z tym adresem IP. Usuwanie skojarzeń pojawi się do pracy, ale wcześniej przypisany publiczny adres IP pozostaje skojarzona z oryginalnej maszyny Wirtualnej. To zachowanie występuje nawet wtedy, gdy należy ponownie przypisać adres IP z nową maszynę Wirtualną (nazywaną *wymiany wirtualnych adresów IP*). Wszystkie przyszłe próbuje nawiązać połączenie za pośrednictwem tego wyniku adresów IP w przypadku połączenia pierwotnie skojarzonej maszyny Wirtualnej, a nie nowy. Obecnie tylko musi używać nowego publiczne adresy IP do utworzenia nowej maszyny Wirtualnej.
 
### <a name="sqlmysql"></a>SQL/MySQL
- Może potrwać do godziny dzierżawcy mogą utworzyć bazy danych w nowym SQL lub MySQL jednostki SKU. 
- Tworzenie elementów bezpośrednio na SQL i MySQL, serwerów, które nie są wykonywane przez dostawcę zasobów hosta nie jest obsługiwana i może spowodować niezgodnego stanu.
 
### <a name="app-service"></a>App Service
- Użytkownik musi zarejestrować dostawcę zasobów magazynowych, przed utworzeniem ich pierwszej funkcji platformy Azure w ramach subskrypcji.
 
### <a name="field-replaceable-unit-fru-procedures"></a>Pole jednostkę (FRU) replaceable unit procedur

- Podczas przebiegu aktualizacji nie są poprawić obrazów w trybie offline. Jeśli musisz zastąpić węzła jednostki skalowania, pracować z dostawcą sprzętu OEM, aby upewnić się, że zastąpione węzeł ma poziom ostatniej poprawki.

## <a name="download-the-update"></a>Pobierz aktualizację

Możesz pobrać pakiet aktualizacji 1710 [tutaj](https://aka.ms/azurestackupdatedownload).

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać omówienie zarządzania aktualizacjami w usłudze Azure Stack, zobacz [zarządzanie aktualizacjami w usłudze Azure Stack — omówienie](azure-stack-updates.md).
- Aby uzyskać informacji dotyczących sposobu stosowania aktualizacji, zobacz [stosowanie aktualizacji w usłudze Azure Stack](azure-stack-apply-updates.md).
