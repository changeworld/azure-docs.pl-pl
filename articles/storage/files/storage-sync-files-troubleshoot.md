---
title: Rozwiązywanie problemów z usługi Azure File Sync | Dokumentacja firmy Microsoft
description: Rozwiązywanie typowych problemów z usługi Azure File Sync.
services: storage
author: jeffpatt24
ms.service: storage
ms.topic: article
ms.date: 01/31/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 26055727e308f8c05aece31746434d7e9a0a5abd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65555948"
---
# <a name="troubleshoot-azure-file-sync"></a>Rozwiązywanie problemów z usługą Azure File Sync
Usługa Azure File Sync umożliwia scentralizowanie udziałów plików Twojej organizacji w usłudze Azure Files przy jednoczesnym zachowaniu elastyczności, wydajności i zgodności lokalnego serwera plików. Usługa Azure File Sync przekształca systemu Windows Server w szybką pamięć podręczną udziału plików platformy Azure. Można użyć dowolnego protokołu, który jest dostępny w systemie Windows Server oraz dostęp do danych lokalnie, w tym protokołu SMB, systemu plików NFS i protokołu FTPS. Może mieć dowolną liczbę pamięci podręcznych potrzebnych na całym świecie.

W tym artykule jest przeznaczona ułatwiające rozwiązywania oraz usuwania problemów, które mogą wystąpić z wdrożeniem usługi Azure File Sync. Opisano również, jak zbierać dzienniki ważne z systemu, jeśli wymagana jest głębszego zbadania problemu. Jeśli nie widzisz odpowiedź na Twoje pytanie, możesz skontaktować się ze nam za pośrednictwem poniższych kanałów (w kolejności szybciej):

1. [Forum usługi Azure Storage](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).
2. [W usłudze pliki Azure UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).
3. Microsoft Support. Aby utworzyć nowe żądanie obsługi w witrynie Azure portal na **pomocy** zaznacz **Pomoc i obsługa techniczna** przycisk, a następnie wybierz **nowe żądanie obsługi**.

## <a name="im-having-an-issue-with-azure-file-sync-on-my-server-sync-cloud-tiering-etc-should-i-remove-and-recreate-my-server-endpoint"></a>Mam problem z usługi Azure File Sync na serwerze (synchronizacja, chmura warstw itp.). Należy I usunięcie i ponowne utworzenie Mój punkt końcowy serwera?
[!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]

## <a name="agent-installation-and-server-registration"></a>Rejestracja agenta instalacji i serwera
<a id="agent-installation-failures"></a>**Rozwiązywanie problemów z niepowodzeniem instalacji agenta**  
W przypadku niepowodzenia instalacji agenta usługi Azure File Sync w wierszu polecenia z podwyższonym poziomem uprawnień uruchom następujące polecenie, aby włączyć funkcję rejestrowania podczas instalacji agenta:

```
StorageSyncAgent.msi /l*v AFSInstaller.log
```

Przejrzyj installer.log, aby ustalić przyczynę niepowodzenia instalacji.

<a id="agent-installation-on-DC"></a>**Instalacja agenta nie powiedzie się na kontrolerze domeny usługi Active Directory**  
Jeśli spróbujesz zainstalować agenta synchronizacji na kontrolerze domeny usługi Active Directory, gdzie właściciela roli kontrolera PDC jest w systemie Windows Server 2008 R2 lub niższej wersji systemu operacyjnego, można napotkać problem gdzie agenta synchronizacji nie zostanie zainstalowana.

Aby rozwiązać problem, należy przenieść rolę kontrolera PDC do innego kontrolera domeny z systemem Windows Server 2012 R2 lub nowszej, zainstaluj synchronizacji.

<a id="server-registration-missing"></a>**Serwer nie znajduje się w obszarze zarejestrowane serwery w witrynie Azure portal**  
Jeśli serwer nie jest wymieniony w obszarze **zarejestrowane serwery** usługi synchronizacji magazynu:
1. Zaloguj się do serwera, który chcesz zarejestrować.
2. Otwórz Eksploratora plików, a następnie przejdź do katalogu instalacyjnego agenta synchronizacji magazynu (domyślna lokalizacja to C:\Program Files\Azure\StorageSyncAgent). 
3. Uruchom ServerRegistration.exe i Ukończ pracę kreatora, aby zarejestrować serwer przy użyciu usługi synchronizacji magazynu.

<a id="server-already-registered"></a>**Rejestracja serwera wyświetla następujący komunikat podczas instalacji agenta usługi Azure File Sync: "Ten serwer jest już zarejestrowany"** 

![Zrzut ekranu przedstawiający okno dialogowe rejestracji serwera z powodu błędu "serwer jest już zarejestrowany" wiadomości](media/storage-sync-files-troubleshoot/server-registration-1.png)

Ten komunikat pojawia się, jeśli serwer został wcześniej zarejestrowany za pomocą usługi synchronizacji magazynu. Aby wyrejestrować serwer z bieżącej usługi synchronizacji magazynu, a następnie zarejestrować przy użyciu nowej usługi synchronizacji magazynu, wykonaj czynności opisane w [Wyrejestruj serwer usługi Azure File Sync](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

Jeśli serwer nie jest wymieniony w obszarze **zarejestrowane serwery** w usłudze synchronizacji magazynu na serwerze, który chcesz wyrejestrować, uruchom następujące polecenia programu PowerShell:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> Jeśli serwer jest częścią klastra, możesz użyć opcjonalnego *Reset StorageSyncServer - CleanClusterRegistration* parametru, aby usunęła również rejestracji klastra.

<a id="web-site-not-trusted"></a>**Czy mogę zarejestrować serwer, widać jest wiele odpowiedzi "witryna sieci web nie jest zaufany". Dlaczego?**  
Ten problem występuje, gdy **zwiększonych zabezpieczeń programu Internet Explorer** zasady są włączone podczas rejestracji serwera. Aby uzyskać więcej informacji na temat sposobu wyłączania poprawnie **zwiększonych zabezpieczeń programu Internet Explorer** zasad, zobacz [przygotowanie systemu Windows Server za pomocą usługi Azure File Sync](storage-sync-files-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync) i [sposób wdrażania usługi Azure File Synchronizacja](storage-sync-files-deployment-guide.md).

## <a name="sync-group-management"></a>Zarządzanie grupami synchronizacji
<a id="cloud-endpoint-using-share"></a>**Tworzenie punktu końcowego w chmurze zakończy się niepowodzeniem z powodu następującego błędu: „Określony udział plików platformy Azure jest już używany przez inny punkt końcowy w chmurze”**  
Ten problem występuje, gdy udział plików platformy Azure jest już używany przez inny punkt końcowy w chmurze. 

Jeśli widzisz ten komunikat i udziału plików platformy Azure nie jest obecnie używany przez punkt końcowy w chmurze, wykonaj następujące kroki, aby wyczyścić metadanych usługi Azure File Sync w udziale plików platformy Azure:

> [!Warning]  
> Usuwanie metadanych w udziale plików platformy Azure, która jest aktualnie używany przez punkt końcowy w chmurze powoduje niepowodzenie działania usługi Azure File Sync. 

1. W witrynie Azure portal przejdź do udziału plików platformy Azure.  
2. Kliknij prawym przyciskiem myszy udział plików platformy Azure, a następnie wybierz **edytowanie metadanych**.
3. Kliknij prawym przyciskiem myszy **SyncService**, a następnie wybierz pozycję **Usuń**.

<a id="cloud-endpoint-authfailed"></a>**Tworzenie punktu końcowego w chmurze zakończy się niepowodzeniem z powodu następującego błędu: "AuthorizationFailed"**  
Ten problem występuje, gdy konto użytkownika nie ma wystarczających praw, aby utworzyć punkt końcowy w chmurze. 

Aby utworzyć punkt końcowy w chmurze, Twoje konto użytkownika musi mieć następujące uprawnienia Authorization firmy Microsoft:  
* Odczyt: Pobranie definicji roli
* Zapis: Utwórz lub zaktualizuj niestandardową definicję roli
* Odczyt: Pobranie przypisania roli
* Zapis: Utwórz przypisanie roli

Następujące role wbudowane mają wymagane uprawnienia Authorization Microsoft:  
* Właściciel
* Administrator dostępu użytkowników

Aby określić, czy rola konto użytkownika ma wymagane uprawnienia:  
1. W witrynie Azure portal wybierz **grup zasobów**.
2. Wybierz grupę zasobów, w którym znajduje się konto magazynu, a następnie wybierz **kontrola dostępu (IAM)** .
3. Wybierz **przypisań ról** kartę.
4. Wybierz **roli** (na przykład właściciela lub współautora) dla konta użytkownika.
5. W **dostawcy zasobów** listy wybierz **Authorization Microsoft**. 
    * **Przypisanie roli** powinny mieć **odczytu** i **zapisu** uprawnienia.
    * **Definicja roli** powinny mieć **odczytu** i **zapisu** uprawnienia.

<a id="server-endpoint-createjobfailed"></a>**Tworzenie punktu końcowego serwera nie powiodło się z powodu następującego błędu: "MgmtServerJobFailed" (kod błędu:-2134375898)**  
Ten problem występuje, gdy ścieżka punktu końcowego serwera znajduje się na woluminie systemowym i w chmurze warstw jest włączona. Chmura obsługi warstw nie jest obsługiwana na woluminie systemowym. Aby utworzyć punkt końcowy serwera na woluminie systemowym, należy wyłączyć obsługi warstw podczas tworzenia punktu końcowego serwera w chmurze.

<a id="server-endpoint-deletejobexpired"></a>**Usuwanie punktu końcowego serwera nie powiodła się z powodu następującego błędu: "MgmtServerJobExpired"**                 
Ten problem występuje, jeśli serwer jest w trybie offline lub nie ma łączności sieciowej. Jeśli serwer nie jest już dostępna, należy wyrejestrować serwera w witrynie portal, co spowoduje usunięcie punkty końcowe serwera. Aby usunąć punkty końcowe serwera, wykonaj kroki, które są opisane w [Wyrejestruj serwer usługi Azure File Sync](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

<a id="server-endpoint-provisioningfailed"></a>**Nie można otworzyć stronę właściwości punktu końcowego serwera lub zaktualizować zasady obsługi warstw w chmurze**  
Ten problem może wystąpić w przypadku niepowodzenia operacji zarządzania, w punkcie końcowym serwera. Jeśli strona Właściwości punktu końcowego serwera nie można otworzyć w witrynie Azure portal, aktualizowanie punktu końcowego serwera za pomocą poleceń programu PowerShell z serwera może rozwiązać ten problem. 

```powershell
# Get the server endpoint id based on the server endpoint DisplayName property
Get-AzStorageSyncServerEndpoint `
    -ResourceGroupName myrgname `
    -StorageSyncServiceName storagesvcname `
    -SyncGroupName mysyncgroup | `
Tee-Object -Variable serverEndpoint

# Update the free space percent policy for the server endpoint
Set-AzStorageSyncServerEndpoint `
    -InputObject $serverEndpoint
    -CloudTiering `
    -VolumeFreeSpacePercent 60
```
<a id="server-endpoint-noactivity"></a>**Punkt końcowy serwera ma stan kondycji "No Activity" lub "Pending" i stanu serwera w bloku zarejestrowane serwery "Pojawia się w trybie offline"**  

Ten problem może wystąpić, jeśli proces Monitor synchronizacji magazynu nie działa lub serwer nie może komunikować się z usługą Azure File Sync z powodu serwera proxy lub zapory.

Aby rozwiązać ten problem, wykonaj następujące kroki:

1. Otwórz Menedżera zadań na serwerze i upewnij się, że proces programu Storage Sync Monitor (AzureStorageSyncMonitor.exe) jest uruchomiony. Jeśli proces nie jest uruchomiony, najpierw spróbuj uruchomić ponownie serwer. Jeśli ponowne uruchomienie serwera nie rozwiąże problemu, uaktualnienie do najnowszej usługi Azure File Sync [wersja agenta](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes).
2. Sprawdź, czy ustawienia zapory i serwera Proxy zostały prawidłowo skonfigurowane:
    - Jeśli serwer znajduje się za zaporą, sprawdź, czy ruch wychodzący na port 443 jest dozwolony. Jeśli zapora będzie ograniczała ruch do określonych domen, upewnij się, domen, na liście w zaporze [dokumentacji](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#firewall) są dostępne.
    - Jeśli serwer znajduje się za serwerem proxy, skonfiguruj ustawienia serwera proxy dla komputera lub specyficzne dla aplikacji, wykonując kroki na serwerze proxy [dokumentacji](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#proxy).

<a id="endpoint-noactivity-sync"></a>**Punkt końcowy serwera ma stan kondycji, aktywności"No", i stanu serwera w bloku zarejestrowane serwery "Online"**  

Kondycja punktu końcowego serwera działania"nie" oznacza, że punkt końcowy serwera nie zarejestrował działanie synchronizacji w ciągu ostatnich dwóch godzin.

Punkt końcowy serwera nie mogą rejestrować działanie synchronizacji z następujących powodów:

- Wersja agenta 4.3.0.0 lub starsze jest zainstalowany, a serwer ma aktywną sesję synchronizacji usługi VSS (SnapshotSync). Podczas sesji synchronizacji usługi VSS jest aktywny dla punktu końcowego serwera, inne punkty końcowe serwera, w tym samym woluminie nie można uruchomić sesji synchronizacji start zakończenie sesji synchronizacji Usługa VSS. Aby rozwiązać ten problem, zainstaluj wersję agenta 5.0.2.0 lub nowszej obsługującą wielu punkty końcowe serwera synchronizacji na woluminie podczas VSS synchronizacji sesji jest aktywna.

    Aby sprawdzić bieżące działanie synchronizacji na serwerze, zobacz [jak monitorować postęp bieżącej sesji synchronizacji?](#how-do-i-monitor-the-progress-of-a-current-sync-session).

- Serwer osiągnął maksymalną liczbę sesji jednoczesnych synchronizacji. 
    - Wersja agenta 4.x i nowszych: Limit różni się w zależności od dostępnych zasobów systemowych.
    - Wersja agenta 3.x: 2 sesji ActiveSync na procesor lub maksymalnie 8 sesji ActiveSync na serwerze.

> [!Note]  
> Jeśli stan serwera w bloku zarejestrowanych serwerów jest "Pojawia się w trybie Offline", wykonaj kroki opisane w temacie [punkt końcowy serwera ma stan kondycji "No Activity" lub "Pending" i stanu serwera w bloku zarejestrowane serwery "Pojawia się w trybie offline" ](#server-endpoint-noactivity) sekcji.

## <a name="sync"></a>Sync
<a id="afs-change-detection"></a>**Jeśli utworzono plik bezpośrednio w mojej udziału plików platformy Azure za pośrednictwem protokołu SMB lub za pośrednictwem portalu, jak długo trwa dla pliku, aby zsynchronizować z serwerów w grupie synchronizacji?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="serverendpoint-pending"></a>**Kondycja punktu końcowego serwera jest w stanie oczekiwania przez kilka godzin**  
Ten problem powinien po utworzeniu punktu końcowego w chmurze i użyj udziału plików platformy Azure, która zawiera dane. Zadanie wyliczania zmian, które skanuje w poszukiwaniu zmian w udziale plików platformy Azure musi wykonać, zanim pliki można synchronizować między chmurą a serwerem punktami końcowymi. Czas wymagany do ukończenia zadania jest zależna od rozmiaru przestrzeni nazw w udziale plików platformy Azure. Kondycja punktu końcowego serwera powinna zostać zaktualizowana po ukończeniu zadania wyliczania zmian.

### <a id="broken-sync"></a>Jak monitorować kondycję synchronizacji?
# <a name="portaltabportal1"></a>[Portal](#tab/portal1)
W każdej grupie synchronizacji można przejść do szczegółów do jego punktów końcowych poszczególnych serwerów, aby wyświetlić stan ostatniej sesji synchronizacji ukończone. Zielony kolumny kondycji i nie synchronizuje pliki wartość 0 wskazuje, czy synchronizacja działa zgodnie z oczekiwaniami. Jeśli nie jest tak, zobacz poniżej, aby uzyskać listę typowych błędów synchronizacji i sposób obsługi plików, które nie są synchronizowane. 

![Zrzut ekranu witryny Azure Portal](media/storage-sync-files-troubleshoot/portal-sync-health.png)

# <a name="servertabserver"></a>[Serwer](#tab/server)
Przejdź do dzienników telemetrii serwera, które można znajdują się w Podglądzie zdarzeń na `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`. Zdarzenie 9102 odnosi się do sesji synchronizacji ukończone. Wyszukaj ostatnie zdarzenie o identyfikatorze 9102 uzyskać najnowszy stan synchronizacji. SyncDirection informuje, czy ta sesja było przekazywania lub pobierania. Jeśli ma wartość HResult 0, sesję synchronizacji zakończyło się pomyślnie. HResult różna od zera oznacza, że wystąpił błąd podczas synchronizacji; Poniżej zamieszczono listę najczęściej występujących błędów. Jeśli PerItemErrorCount jest większa niż 0, oznacza to, że niektóre pliki lub foldery nie zostały zsynchronizowane prawidłowo. Istnieje możliwość mają wartość HResult 0, ale PerItemErrorCount, która jest większa niż 0.

Poniżej znajduje się przykład pomyślnego wysłania. W celu skrócenia programu tylko niektóre wartości zawarte w każdym przypadku 9102 są wymienione poniżej. 

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: 0, 
SyncFileCount: 2, SyncDirectoryCount: 0,
AppliedFileCount: 2, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0,
TransferredFiles: 2, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Z drugiej strony niepowodzeniem przekazywania może wyglądać następująco:

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: -2134364065,
SyncFileCount: 0, SyncDirectoryCount: 0, 
AppliedFileCount: 0, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0, 
TransferredFiles: 0, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Czasami sesje synchronizacji ogólną się nie powieść lub mieć PerItemErrorCount różna od zera, ale nadal postęp do przodu, z niektórych plików, na synchronizowanie pomyślnie. Można to zaobserwować, zastosowano * pól (AppliedFileCount AppliedDirCount, AppliedTombstoneCount i AppliedSizeBytes), które informują, ile sesji powodzeniem. Jeśli widzisz wiele sesji synchronizacji w wierszu, które kończą się niepowodzeniem, ale zwiększa liczbę zastosowano *, powinien zapewnić synchronizacji czasu, aby spróbować ponownie, przed otwarciem biletu pomocy technicznej.

---

### <a name="how-do-i-monitor-the-progress-of-a-current-sync-session"></a>Jak monitorować postęp bieżącej sesji synchronizacji?
# <a name="portaltabportal1"></a>[Portal](#tab/portal1)
W danej grupie synchronizacji przejdź do danego punktu końcowego serwera i przyjrzyj się sekcję działanie synchronizacji, aby wyświetlić liczbę pliki, przekazać lub pobrać w bieżącej sesji synchronizacji. Należy pamiętać, że ten stan będzie opóźniony o około 5 minut, a jeśli sesja synchronizacji jest wystarczająco mała, należy wykonać w tym okresie, jego może nie być zgłaszany w portalu. 

# <a name="servertabserver"></a>[Serwer](#tab/server)
Szukaj na najnowszy: 9302; lista w dane telemetryczne dziennika zdarzeń na serwerze (w Podglądzie zdarzeń przejdź do aplikacji i usług Logs\Microsoft\FileSync\Agent\Telemetry). To zdarzenie wskazuje stan bieżącej sesji synchronizacji. TotalItemCount wskazuje, ile plików jest można synchronizować AppliedItemCount liczbę plików, które zostały zsynchronizowane do tej pory i PerItemErrorCount liczbę plików, które kończą się niepowodzeniem do synchronizacji (poniżej radzenia sobie z tym).

```
Replica Sync Progress. 
ServerEndpointName: <CI>sename</CI>, SyncGroupName: <CI>sgname</CI>, ReplicaName: <CI>rname</CI>, 
SyncDirection: Upload, CorrelationId: {AB4BA07D-5B5C-461D-AAE6-4ED724762B65}. 
AppliedItemCount: 172473, TotalItemCount: 624196. AppliedBytes: 51473711577, 
TotalBytes: 293363829906. 
AreTotalCountsFinal: true. 
PerItemErrorCount: 1006.
```
---

### <a name="how-do-i-know-if-my-servers-are-in-sync-with-each-other"></a>Jak sprawdzić, serwerów są synchronizowane ze sobą?
# <a name="portaltabportal1"></a>[Portal](#tab/portal1)
Dla każdego serwera w grupie synchronizacji danego upewnij się, że:
- Ostatnie są sygnatury czasowe dla ostatniej synchronizacji próba przekazywania i pobierania.
- Stan jest zielony przekazywania i pobierania.
- Pole działanie synchronizacji zawiera bardzo mało lub nie plików pozostałych do synchronizacji.
- Pole nie synchronizuje pliki jest 0 w przypadku przekazywania i pobierania.

# <a name="servertabserver"></a>[Serwer](#tab/server)
Obejrzyj sesje ukończone synchronizacji, które są oznaczone przez 9102 zdarzenia w dzienniku zdarzeń telemetrii dla każdego serwera (w Podglądzie zdarzeń przejdź do `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`). 

1. Na dowolnym danym serwerze, dla których chcesz upewnić się, że najnowsze przekazywania i pobierania sesji zostało ukończone pomyślnie. Aby to zrobić, należy sprawdzić, czy HResult i PerItemErrorCount są 0 w przypadku przekazywania i pobierania (pole SyncDirection wskazuje, czy sesji przekazywania lub pobierania w danej sesji). Należy pamiętać, że jeśli nie widzisz sesji synchronizacji ostatnio wykonanych, prawdopodobnie sesji synchronizacji jest obecnie w toku, której oczekuje się, jeśli są właśnie dodany lub zmodyfikowany dużej ilości danych.
2. Gdy serwer jest aktualna w chmurze i nie ma zmian do synchronizacji w dowolnym kierunku, zobaczysz sesje synchronizacji puste. Te są wskazywane przez przekazywanie i pobieranie zdarzenia, które wszystkie synchronizacji * pól (SyncFileCount, SyncDirCount, SyncTombstoneCount i SyncSizeBytes) są zero, co oznacza, że żadne do synchronizacji. Należy pamiętać, że te sesje synchronizacji pusty nie może wystąpić na serwerach z dużym postępem zmian, że jest zawsze coś nowego do synchronizacji. W przypadku działania synchronizacji, nie wystąpi co 30 minut. 
3. Jeśli wszystkie serwery są aktualne w chmurze, co oznacza ich ostatnich przekazywania i pobierania sesje są sesje synchronizacji pusty, można powiedzieć uzasadnione pewność, że system jako całość jest zsynchronizowany. 
    
Należy pamiętać, że jeśli wprowadzono zmiany bezpośrednio w udziale plików platformy Azure, usługi Azure File Sync nie wykryje tę zmianę do momentu uruchomienia wyliczenie zmiany, które odbywa się raz na 24 godziny. Istnieje możliwość, że serwer będzie Powiedzmy, że jest on aktualny z chmurą, gdy w rzeczywistości brakuje ostatnie zmiany bezpośrednio w udziale plików platformy Azure. 

---

### <a name="how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing"></a>Jak sprawdzić, czy istnieją określone pliki lub foldery, które nie są synchronizowane.
Jeśli Twoje PerItemErrorCount na serwerze lub nie synchronizuje pliki count w portalu są większe od 0 do dowolnej sesji danego synchronizacji, oznacza to, że niektóre elementy kończą się niepowodzeniem zsynchronizować. Pliki i foldery może mieć właściwości, które uniemożliwiają synchronizacji. Te cechy mogą być stałe i wymagają konkretne działanie, aby wznowić synchronizację, na przykład usuwając nieobsługiwane znaki w nazwie pliku lub folderu. Mogą również być błędem przejściowym, co oznacza plik lub folder zostanie automatycznie wznowiona synchronizacji; na przykład pliki z otwartymi dojściami zostanie automatycznie wznowiona synchronizacji, gdy plik jest zamknięty. Gdy aparatu usługi Azure File Sync wykryje taki problem, generowany jest dziennik błędów można przeanalizować, aby wyświetlić listę elementów, obecnie nie synchronizuje prawidłowo.

Aby wyświetlić te błędy, uruchom **FileSyncErrorsReport.ps1** skrypt programu PowerShell (znajdujący się w katalogu instalacyjnym programu agent agenta usługi Azure File Sync) do identyfikowania plików, których nie można zsynchronizować z powodu otwartych dojść nieobsługiwany znaki, lub inne problemy. Pole Ścieżka elementu wskazuje lokalizację pliku względem katalogu głównego synchronizacji. Zobacz listę typowych błędów synchronizacji poniżej, aby wykonać kroki korygowania.

#### <a name="troubleshooting-per-filedirectory-sync-errors"></a>Rozwiązywanie problemów na błędy synchronizacji pliku lub katalogu
**Dziennik ItemResults — błędy synchronizacji na element**  

| WARTOŚĆ HRESULT | HRESULT (dziesiętna) | Ciąg błędu | Problem | Korygowanie |
|---------|-------------------|--------------|-------|-------------|
| 0x80c80207 | -2134375929 | ECS_E_SYNC_CONSTRAINT_CONFLICT | Nie można jeszcze zsynchronizować zmiany pliku lub katalogu, ponieważ nie jest jeszcze zsynchronizowany folder zależny. Ten element zostanie zsynchronizowany po zsynchronizowaniu zmian zależnych. | Nie jest wymagana żadna akcja. |
| 0x7B | 123 | ERROR_INVALID_NAME | Nazwa pliku lub katalogu jest nieprawidłowa. | Zmień nazwę pliku lub katalogu jest zagrożona. Zobacz [obsługi nieobsługiwane znaki](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters) Aby uzyskać więcej informacji. |
| 0x8007007b | -2147024773 | STIERR_INVALID_DEVICE_NAME | Nazwa pliku lub katalogu jest nieprawidłowa. | Zmień nazwę pliku lub katalogu jest zagrożona. Zobacz [obsługi nieobsługiwane znaki](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters) Aby uzyskać więcej informacji. |
| 0x80c80018 | -2134376424 | ECS_E_SYNC_FILE_IN_USE | Nie można zsynchronizować pliku, ponieważ jest on używany. Plik zostanie zsynchronizowany, gdy nie jest już używana. | Nie jest wymagana żadna akcja. Usługa Azure File Sync tworzy migawkę usługi VSS tymczasowe raz dziennie na serwerze, aby synchronizować pliki, które mają otwarte dojścia. |
| 0x80c8031d | -2134375651 | ECS_E_CONCURRENCY_CHECK_FAILED | Plik został zmieniony, ale zmiany nie ma jeszcze wykryty przez synchronizacji. Synchronizacja zostanie przywrócona do działania po wykryciu tej zmiany. | Nie jest wymagana żadna akcja. |
| 0x80c8603e | -2134351810 | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED | Nie można zsynchronizować pliku, ponieważ osiągnięto limit udziału plików platformy Azure. | Aby rozwiązać ten problem, zobacz [osiągnięto limit magazynowania udziału plików platformy Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#-2134351810) w przewodniku rozwiązywania problemów. |
| 0x80070005 | -2147024891 | E_ACCESSDENIED | Ten błąd może wystąpić z następujących powodów: plik jest szyfrowany przez rozwiązanie nieobsługiwany (np. EFS systemu plików NTFS), plik ma blokadę usuwania w stanie oczekiwania lub plik znajduje się w folderze tylko do odczytu replikacji systemu plików DFS-R | Jeśli plik jest szyfrowany przez rozwiązanie nieobsługiwane, odszyfrowywania pliku i przy użyciu rozwiązania obsługiwanych szyfrowania. Aby uzyskać listę rozwiązań pomocy technicznej, zobacz [rozwiązań do szyfrowania](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#encryption-solutions) w przewodniku planowania. Jeśli plik znajduje się w obszarze usuwanie w stanie oczekiwania, plik zostanie usunięty, gdy są zamykane wszystkie otwarte dojścia do plików. Jeśli plik znajduje się w folderze tylko do odczytu replikacji systemu plików DFS-R, Azure Files Sync nie obsługuje punkty końcowe serwera w folderach tylko do odczytu replikacji systemu plików DFS-R. Zobacz [przewodnik planowania](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#distributed-file-system-dfs) Aby uzyskać więcej informacji.
| 0x20 | 32 | ERROR_SHARING_VIOLATION | Nie można zsynchronizować pliku, ponieważ jest on używany. Plik zostanie zsynchronizowany, gdy nie jest już używana. | Nie jest wymagana żadna akcja. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | Plik został zmieniony podczas synchronizacji, dlatego musi być synchronizowane z ponownie. | Nie jest wymagana żadna akcja. |

#### <a name="handling-unsupported-characters"></a>Obsługa nieobsługiwane znaki
Jeśli **FileSyncErrorsReport.ps1** skrypt programu PowerShell pokazuje błędy spowodowane nieobsługiwane znaki (0x7b kody błędów i 0x8007007b), należy usunąć lub zmienić znaków na pozycji błędu z nazwami odpowiednich plików. Program PowerShell prawdopodobnie zostanie wydrukowana te znaki jako znaki zapytania lub prostokąty pusty, ponieważ większość z tych znaków ma nie standardowych wizualnego kodowania. [Uruchamiania narzędzia oceny](storage-sync-files-planning.md#evaluation-tool) może służyć do identyfikowania znaków, które nie są obsługiwane.

Poniższa tabela zawiera wszystkie znaki unicode, który nie obsługuje jeszcze usługi Azure File Sync.

| Zestaw znaków | Liczba znaków |
|---------------|-----------------|
| <ul><li>0x0000009D (polecenie osc systemu operacyjnego)</li><li>0x00000090 (ciąg formantu urządzenia kontrolery domeny)</li><li>0x0000008F (zmiana jednego ss3 trzech)</li><li>0x00000081 (ustawienie wstępne wysokiej oktetu)</li><li>0x0000007F (Usuń del)</li><li>0x0000008D (wystąpień zarezerwowanych odwrotnej wiersz)</li></ul> | 6 |
| 0x0000FDD0 - 0x0000FDEF (arabski prezentacja formularzy a) | 32 |
| 0x0000FFF0 - 0x0000FFFF (promocje) | 16 |
| <ul><li>0x0001FFFE - 0x0001FFFF = 2 (nieznakowe)</li><li>0x0002FFFE - 0x0002FFFF = 2 (nieznakowe)</li><li>0x0003FFFE - 0x0003FFFF = 2 (nieznakowe)</li><li>0x0004FFFE - 0x0004FFFF = 2 (nieznakowe)</li><li>0x0005FFFE - 0x0005FFFF = 2 (nieznakowe)</li><li>0x0006FFFE - 0x0006FFFF = 2 (nieznakowe)</li><li>0x0007FFFE - 0x0007FFFF = 2 (nieznakowe)</li><li>0x0008FFFE - 0x0008FFFF = 2 (nieznakowe)</li><li>0x0009FFFE - 0x0009FFFF = 2 (nieznakowe)</li><li>0x000AFFFE - 0x000AFFFF = 2 (nieznakowe)</li><li>0x000BFFFE - 0x000BFFFF = 2 (nieznakowe)</li><li>0x000CFFFE - 0x000CFFFF = 2 (nieznakowe)</li><li>0x000DFFFE - 0x000DFFFF = 2 (nieznakowe)</li><li>0x000EFFFE - 0x000EFFFF = 2 (niezdefiniowany)</li><li>0x000FFFFE - 0x000FFFFF = 2 (dodatkowych użytku prywatnego obszaru)</li></ul> | 30 |
| 0x0010FFFE, 0x0010FFFF | 2 |

### <a name="common-sync-errors"></a>Typowe błędy synchronizacji
<a id="-2147023673"></a>**Sesja synchronizacji została anulowana.**  

| | |
|-|-|
| **HRESULT** | 0x800704c7 |
| **HRESULT (dziesiętna)** | -2147023673 | 
| **Ciąg błędu** | ERROR_CANCELLED |
| **Wymagana korekta** | Nie |

Sesje synchronizacji może się nie powieść z różnych powodów, łącznie z serwerem jest ponownie uruchomiony lub zaktualizowaniu migawki VSS itp. Chociaż ten błąd wygląda na to wymaga monitowania, jest bezpiecznie zignorować ten błąd, chyba że utrzymuje okresie do kilku godzin.

<a id="-2147012889"></a>**Nie można nawiązać połączenia z usługą.**    

| | |
|-|-|
| **HRESULT** | 0x80072ee7 |
| **HRESULT (dziesiętna)** | -2147012889 | 
| **Ciąg błędu** | WININET_E_NAME_NOT_RESOLVED |
| **Wymagana korekta** | Yes |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134376372"></a>**Żądanie użytkownika została ograniczona przez usługę.**  

| | |
|-|-|
| **HRESULT** | 0x80c8004c |
| **HRESULT (dziesiętna)** | -2134376372 |
| **Ciąg błędu** | ECS_E_USER_REQUEST_THROTTLED |
| **Wymagana korekta** | Nie |

Brak akcji jest wymagana; Serwer ponowi próbę. Jeśli ten błąd będzie się powtarzać dłużej niż kilka godzin, Utwórz żądanie pomocy technicznej.

<a id="-2134364043"></a>**Synchronizacja jest zablokowany, ukończenie przywracania wpis wykrywania zmian**  

| | |
|-|-|
| **HRESULT** | 0x80c83075 |
| **HRESULT (dziesiętna)** | -2134364043 |
| **Ciąg błędu** | ECS_E_SYNC_BLOCKED_ON_CHANGE_DETECTION_POST_RESTORE |
| **Wymagana korekta** | Nie |

Nie jest wymagana żadna akcja. W przypadku pliku lub udziału plikowego (punkt końcowy w chmurze) jest przywracany za pośrednictwem usługi Azure Backup, synchronizacji jest zablokowany do momentu ukończenia wykrywania zmian w udziale plików platformy Azure. Wykrywanie zmian jest uruchamiany natychmiast po zakończeniu przywracania i czas trwania zależy od liczby plików w udziale plików.

<a id="-2134364065"></a>**Synchronizacja nie może uzyskać dostępu z udziału plików platformy Azure, określonego przez punkt końcowy w chmurze.**  

| | |
|-|-|
| **HRESULT** | 0x80c8305f |
| **HRESULT (dziesiętna)** | -2134364065 |
| **Ciąg błędu** | ECS_E_CANNOT_ACCESS_EXTERNAL_STORAGE_ACCOUNT |
| **Wymagana korekta** | Tak |

Ten błąd występuje, ponieważ agent usługi Azure File Sync nie może uzyskać dostępu do udziału plików platformy Azure, która może być, ponieważ udział plików platformy Azure lub konto magazynu hostujące ona już nie istnieje. Ten błąd można rozwiązać, klikając pracy przez następujące kroki:

1. [Sprawdź, czy konto magazynu istnieje.](#troubleshoot-storage-account)
2. [Sprawdź, upewnij się, że konto magazynu nie zawiera żadnych reguł sieciowych.](#troubleshoot-network-rules)
3. [Upewnij się, że istnieje udział plików platformy Azure.](#troubleshoot-azure-file-share)
4. [Upewnij się, że usługi Azure File Sync ma dostęp do konta magazynu.](#troubleshoot-rbac)

<a id="-2134364064"></a><a id="cannot-resolve-storage"></a>**Nie można rozpoznać nazwy konta magazynu, które są używane.**  

| | |
|-|-|
| **HRESULT** | 0x80C83060 |
| **HRESULT (dziesiętna)** | -2134364064 |
| **Ciąg błędu** | ECS_E_STORAGE_ACCOUNT_NAME_UNRESOLVED |
| **Wymagana korekta** | Tak |

1. Sprawdź, że może rozpoznać nazwy DNS magazynu z serwera.

    ```powershell
    Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 443
    ```
2. [Sprawdź, czy konto magazynu istnieje.](#troubleshoot-storage-account)
3. [Sprawdź, upewnij się, że konto magazynu nie zawiera żadnych reguł sieciowych.](#troubleshoot-network-rules)

<a id="-1906441138"></a>**Synchronizacja nie powiodła się z powodu problemu z bazy danych usługi synchronizacji.**  

| | |
|-|-|
| **HRESULT** | 0x8e5e044e |
| **HRESULT (dziesiętna)** | -1906441138 |
| **Ciąg błędu** | JET_errWriteConflict |
| **Wymagana korekta** | Tak |

Ten błąd występuje, gdy występuje problem z wewnętrznej bazy danych używane przez usługę Azure File Sync. Jeśli wystąpi ten problem, Utwórz żądanie obsługi, a firma Microsoft z Tobą, aby pomóc rozwiązać ten problem.

<a id="-2134364053"></a>**Wersja agenta usługi Azure File Sync, które są zainstalowane na serwerze nie jest obsługiwana.**  

| | |
|-|-|
| **HRESULT** | 0x80C8306B |
| **HRESULT (dziesiętna)** | -2134364053 |
| **Ciąg błędu** | ECS_E_AGENT_VERSION_BLOCKED |
| **Wymagana korekta** | Tak |

Ten błąd występuje, jeśli nie jest obsługiwana przez wersję agenta usługi Azure File Sync, które są zainstalowane na serwerze. Aby rozwiązać ten problem, [uaktualnienia]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#upgrade-paths) do [obsługiwana wersja agenta]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#supported-versions).

<a id="-2134351810"></a>**Osiągnięto limit magazynowania udziału plików platformy Azure.**  

| | |
|-|-|
| **HRESULT** | 0x80c8603e |
| **HRESULT (dziesiętna)** | -2134351810 |
| **Ciąg błędu** | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED |
| **Wymagana korekta** | Tak |

Ten błąd występuje, gdy limit magazynu udziału plików platformy Azure zostanie osiągnięty, która może się zdarzyć, jeśli zastosowano limit przydziału dla udziału plików platformy Azure lub jeśli użycie przekroczy limity dla udziału plików platformy Azure. Aby uzyskać więcej informacji, zobacz [aktualne limity dla udziału plików platformy Azure](storage-files-scale-targets.md).

1. Przejdź do grupy synchronizacji, w ramach usługi synchronizacji magazynu.
2. Wybierz punkt końcowy w chmurze w ramach grupy synchronizacji.
3. Należy pamiętać, nazwa udziału plików platformy Azure, w okienku otwarte.
4. Wybierz na połączonym koncie magazynu. Jeśli ten link nie powiedzie się, zostało usunięte konto magazynu do którego istnieje odwołanie.

    ![Zrzut ekranu przedstawiający okienko Szczegóły punktu końcowego chmury z linkiem do konta magazynu.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

5. Wybierz **pliki** do wyświetlania listy udziałów plików.
6. Kliknij trzy kropki na końcu wiersza dla udziału plików platformy Azure, odwołuje się punkt końcowy w chmurze.
7. Upewnij się, że **użycia** znajduje się poniżej **przydziału**. Należy pamiętać o ile nie określono alternatywnego przydziału limit przydziału będzie odpowiadał [maksymalny rozmiar udziału plików platformy Azure](storage-files-scale-targets.md).

    ![Zrzut ekranu przedstawiający właściwości udziału plików platformy Azure.](media/storage-sync-files-troubleshoot/file-share-limit-reached-1.png)

Jeśli nie ustawiono limit przydziału udziału jest pełny, jeden sposób możliwe naprawienie tego problemu jest przekształcić Każdy podfolder bieżący punkt końcowy serwera swój własny punkt końcowy serwera w ich własnych grupach oddzielne synchronizacji. W ten sposób każdy podfolder zsynchronizują się z udziałami plików platformy Azure indywidualnych.

<a id="-2134351824"></a>**Nie można odnaleźć udziału plików platformy Azure.**  

| | |
|-|-|
| **HRESULT** | 0x80c86030 |
| **HRESULT (dziesiętna)** | -2134351824 |
| **Ciąg błędu** | ECS_E_AZURE_FILE_SHARE_NOT_FOUND |
| **Wymagana korekta** | Tak |

Ten błąd występuje, gdy udział plików platformy Azure nie jest dostępny. Aby rozwiązać:

1. [Sprawdź, czy konto magazynu istnieje.](#troubleshoot-storage-account)
2. [Upewnij się, że istnieje udział plików platformy Azure.](#troubleshoot-azure-file-share)

Jeśli usunięto udział plików platformy Azure, musisz utworzyć nowy udział plików, a następnie utwórz ponownie grupę synchronizacji. 

<a id="-2134364042"></a>**Synchronizacja została wstrzymana, gdy tej subskrypcji platformy Azure jest wstrzymane.**  

| | |
|-|-|
| **HRESULT** | 0x80C83076 |
| **HRESULT (dziesiętna)** | -2134364042 |
| **Ciąg błędu** | ECS_E_SYNC_BLOCKED_ON_SUSPENDED_SUBSCRIPTION |
| **Wymagana korekta** | Tak |

Ten błąd występuje zawieszenia subskrypcji platformy Azure. Synchronizacja zostanie reenabled, po przywróceniu subskrypcji platformy Azure. Zobacz [Dlaczego Moja subskrypcja platformy Azure jest wyłączona, i jak jest ponownej?](../../billing/billing-subscription-become-disable.md) Aby uzyskać więcej informacji.

<a id="-2134364052"></a>**To konto magazynu zawiera zapora lub sieci wirtualne są skonfigurowane.**  

| | |
|-|-|
| **HRESULT** | 0x80c8306c |
| **HRESULT (dziesiętna)** | -2134364052 |
| **Ciąg błędu** | ECS_E_MGMT_STORAGEACLSNOTSUPPORTED |
| **Wymagana korekta** | Yes |

Ten błąd występuje, gdy udział plików platformy Azure jest niedostępny z powodu zapory konta magazynu, lub ponieważ konto magazynu należy do sieci wirtualnej. Usługa Azure File Sync nie ma jeszcze obsługi tej funkcji. Aby rozwiązać:

1. [Sprawdź, czy konto magazynu istnieje.](#troubleshoot-storage-account)
2. [Sprawdź, upewnij się, że konto magazynu nie zawiera żadnych reguł sieciowych.](#troubleshoot-network-rules)

Usuń te reguły, aby rozwiązać ten problem. 

<a id="-2134375911"></a>**Synchronizacja nie powiodła się z powodu problemu z bazy danych usługi synchronizacji.**  

| | |
|-|-|
| **HRESULT** | 0x80c80219 |
| **HRESULT (dziesiętna)** | -2134375911 |
| **Ciąg błędu** | ECS_E_SYNC_METADATA_WRITE_LOCK_TIMEOUT |
| **Wymagana korekta** | Nie |

Ten błąd zazwyczaj rozwiązuje się i może wystąpić, jeśli istnieją:

* Dużą liczbą zmian plików na serwerach w grupie synchronizacji.
* Duża liczba błędów na poszczególnych plików i katalogów.

Jeśli ten błąd będzie się powtarzać dłużej niż kilka godzin, Utwórz żądanie obsługi, a firma Microsoft z Tobą, aby pomóc rozwiązać ten problem.

<a id="-2146762487"></a>**Serwer nie może nawiązać bezpiecznego połączenia. Usługi w chmurze Odebrano nieoczekiwany certyfikatu.**  

| | |
|-|-|
| **HRESULT** | 0x800b0109 |
| **HRESULT (dziesiętna)** | -2146762487 |
| **Ciąg błędu** | CERT_E_UNTRUSTEDROOT |
| **Wymagana korekta** | Tak |

Ten błąd może wystąpić, jeśli Twoja organizacja używa serwera proxy protokołu SSL kończący lub zamierzających przechwytują dane komunikacji między serwerem i usługa Azure File Sync. Jeśli masz pewność, że jest to oczekiwane (ponieważ jest to Twoja organizacja korzysta z protokołu SSL, zakończenie serwera proxy), możesz pominąć weryfikację certyfikatu za pomocą zastąpienia rejestru.

1. Create the SkipVerifyingPinnedRootCertificate registry value.

    ```powershell
    New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Azure\StorageSync -Name SkipVerifyingPinnedRootCertificate -PropertyType DWORD -Value 1
    ```

2. Uruchom ponownie usługi synchronizacji na zarejestrowanego serwera.

    ```powershell
    Restart-Service -Name FileSyncSvc -Force
    ```

Przez ustawienie tej wartości rejestru, agent usługi Azure File Sync będzie akceptować żadnych lokalnie zaufany certyfikat SSL, podczas przesyłania danych między serwerem a usługą w chmurze.

<a id="-2147012894"></a>**Nie można nawiązać połączenia z usługą.**  

| | |
|-|-|
| **HRESULT** | 0x80072ee2 |
| **HRESULT (dziesiętna)** | -2147012894 |
| **Ciąg błędu** | WININET_E_TIMEOUT |
| **Wymagana korekta** | Yes |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134375680"></a>**Synchronizacja nie powiodła się z powodu problemu z uwierzytelnianiem.**  

| | |
|-|-|
| **HRESULT** | 0x80c80300 |
| **HRESULT (dziesiętna)** | -2134375680 |
| **Ciąg błędu** | ECS_E_SERVER_CREDENTIAL_NEEDED |
| **Wymagana korekta** | Tak |

Ten błąd może być spowodowany przez:

- Czas serwera jest nieprawidłowa
- Usuwanie punktu końcowego serwera nie powiodło się
- Certyfikat używany do uwierzytelniania wygasł. 
    Aby sprawdzić, jeśli certyfikat wygasł, należy wykonać następujące czynności:  
    1. Otwórz przystawkę MMC Certyfikaty, wybierz konto komputera i przejdź do \Personal\Certificates certyfikaty (komputer lokalny).
    2. Sprawdź, czy certyfikat uwierzytelniania klienta jest nadal ważne.

Jeśli czas serwera jest poprawna, wykonaj następujące kroki, aby rozwiązać ten problem:

1. Sprawdź, agent usługi Azure File Sync wersja 4.0.1.0 lub nowszy jest zainstalowany.
2. Uruchom następujące polecenia programu PowerShell na serwerze:

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-1906441711"></a><a id="-2134375654"></a><a id="doesnt-have-enough-free-space"></a>**Wolumin, na którym znajduje się punkt końcowy serwera brakuje miejsca na dysku.**  

| | |
|-|-|
| **HRESULT** | 0x8e5e0211 |
| **HRESULT (dziesiętna)** | -1906441711 |
| **Ciąg błędu** | JET_errLogDiskFull |
| **Wymagana korekta** | Tak |
| | |
| **HRESULT** | 0x80c8031a |
| **HRESULT (dziesiętna)** | -2134375654 |
| **Ciąg błędu** | ECS_E_NOT_ENOUGH_LOCAL_STORAGE |
| **Wymagana korekta** | Tak |

Ten błąd występuje, ponieważ wolumin jest wypełnione. Ten błąd zazwyczaj występuje, ponieważ korzystają z plików znajdujących się poza punkt końcowy serwera miejsca na woluminie. Zwolnij miejsce na woluminie, dodając punkty końcowe dodatkowy serwer, przenoszenie plików na inny wolumin lub zwiększenie rozmiaru woluminu punktu końcowego serwera znajduje się na.

<a id="-2134364145"></a><a id="replica-not-ready"></a>**Usługa nie jest jeszcze gotowy do synchronizacji z tym punktem końcowym serwera.**  

| | |
|-|-|
| **HRESULT** | 0x80c8300f |
| **HRESULT (dziesiętna)** | -2134364145 |
| **Ciąg błędu** | ECS_E_REPLICA_NOT_READY |
| **Wymagana korekta** | Nie |

Ten błąd występuje, ponieważ nie wprowadzono zmian w udziale plików platformy Azure bezpośrednio i wykrywania zmian jest w toku. Synchronizacja rozpocznie się po zakończeniu wykrywania zmian.

[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="-2134375877"></a><a id="-2134375908"></a><a id="-2134375853"></a>**Synchronizacja nie powiodła się z powodu problemów dotyczących wielu pojedynczych plików.**  

| | |
|-|-|
| **HRESULT** | 0x80c8023b |
| **HRESULT (dziesiętna)** | -2134364145 |
| **Ciąg błędu** | ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED |
| **Wymagana korekta** | Yes |
| | |
| **HRESULT** | 0x80c8021c |
| **HRESULT (dziesiętna)** | -2134375908 |
| **Ciąg błędu** | ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED |
| **Wymagana korekta** | Tak |
| | |
| **HRESULT** | 0x80c80253 |
| **HRESULT (dziesiętna)** | -2134375853 |
| **Ciąg błędu** | ECS_E_TOO_MANY_PER_ITEM_ERRORS |
| **Wymagana korekta** | Tak |

W przypadku których istnieje wiele na błędy synchronizacji plików, sesje synchronizacji może zacząć się nie powieść. <!-- To troubleshoot this state, see [Troubleshooting per file/directory sync errors]().-->

> [!NOTE]
> Usługa Azure File Sync tworzy migawkę usługi VSS tymczasowe raz dziennie na serwerze, aby synchronizować pliki, które mają otwarte dojścia.

<a id="-2134376423"></a>**Synchronizacja nie powiodła się z powodu problemu z ścieżkę punktu końcowego serwera.**  

| | |
|-|-|
| **HRESULT** | 0x80c80019 |
| **HRESULT (dziesiętna)** | -2134376423 |
| **Ciąg błędu** | ECS_E_SYNC_INVALID_PATH |
| **Wymagana korekta** | Tak |

Upewnij się, ścieżka istnieje, znajduje się na lokalnym woluminie NTFS i nie jest punktem ponownej analizy lub istniejący punkt końcowy serwera.

<a id="-2134375817"></a>**Synchronizacja nie powiodła się, ponieważ wersja sterownika filtru nie jest zgodny z wersją agenta**  

| | |
|-|-|
| **HRESULT** | 0x80C80277 |
| **HRESULT (dziesiętna)** | -2134375817 |
| **Ciąg błędu** | ECS_E_INCOMPATIBLE_FILTER_VERSION |
| **Wymagana korekta** | Yes |

Ten błąd występuje, ponieważ załadowane wersja Obsługa poziomów w chmurze (StorageSync.sys) sterownika filtru nie jest zgodny z usługą agenta synchronizacji magazynu (FileSyncSvc). Jeśli agent usługi Azure File Sync został uaktualniony, uruchom ponownie serwer, aby zakończyć instalację. Jeśli błąd będzie nadal występować, odinstaluj agenta, uruchom ponownie serwer i ponownie zainstaluj agenta usługi Azure File Sync.

<a id="-2134376373"></a>**Usługa jest obecnie niedostępna.**  

| | |
|-|-|
| **HRESULT** | 0x80c8004b |
| **HRESULT (dziesiętna)** | -2134376373 |
| **Ciąg błędu** | ECS_E_SERVICE_UNAVAILABLE |
| **Wymagana korekta** | Nie |

Ten błąd występuje, ponieważ usługa Azure File Sync jest niedostępna. Ten błąd będzie automatyczne rozwiązanie usługi Azure File Sync ponieważ dostępne ponownie.

<a id="-2134375922"></a>**Synchronizacja nie powiodła się z powodu przejściowego problemu z bazy danych usługi synchronizacji.**  

| | |
|-|-|
| **HRESULT** | 0x80c8020e |
| **HRESULT (dziesiętna)** | -2134375922 |
| **Ciąg błędu** | ECS_E_SYNC_METADATA_WRITE_LEASE_LOST |
| **Wymagana korekta** | Nie |

Ten błąd występuje z powodu wewnętrznego problemu z bazy danych usługi synchronizacji. Ten błąd będzie automatyczne rozwiązanie kiedy usługi Azure File Sync, gdy ponowne próby synchronizacji. Jeśli ten błąd będzie nadal występował, Przedłuż okres, Utwórz żądanie obsługi, a firma Microsoft z Tobą, aby pomóc rozwiązać ten problem.

### <a name="common-troubleshooting-steps"></a>Typowe kroki rozwiązywania problemów
<a id="troubleshoot-storage-account"></a>**Sprawdź, czy konto magazynu istnieje.**  
# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
1. Przejdź do grupy synchronizacji, w ramach usługi synchronizacji magazynu.
2. Wybierz punkt końcowy w chmurze w ramach grupy synchronizacji.
3. Należy pamiętać, nazwa udziału plików platformy Azure, w okienku otwarte.
4. Wybierz na połączonym koncie magazynu. Jeśli ten link nie powiedzie się, zostało usunięte konto magazynu do którego istnieje odwołanie.
    ![Zrzut ekranu przedstawiający okienko Szczegóły punktu końcowego chmury z linkiem do konta magazynu.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

# <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)
```powershell
# Variables for you to populate based on your configuration
$region = "<Az_Region>"
$resourceGroup = "<RG_Name>"
$syncService = "<storage-sync-service>"
$syncGroup = "<sync-group>"

# Log into the Azure account
Connect-AzAccount

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = [System.String[]]@()
Get-AzLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the " + `
        " selected Azure Region or the region is mistyped.")
}

# Check to ensure resource group exists
$resourceGroups = [System.String[]]@()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    throw [System.Exception]::new("The provided resource group $resourceGroup does not exist.")
}

# Check to make sure the provided Storage Sync Service
# exists.
$syncServices = [System.String[]]@()

Get-AzStorageSyncService -ResourceGroupName $resourceGroup | ForEach-Object {
    $syncServices += $_.StorageSyncServiceName
}

if ($syncServices -notcontains $syncService) {
    throw [System.Exception]::new("The provided Storage Sync Service $syncService does not exist.")
}

# Check to make sure the provided Sync Group exists
$syncGroups = [System.String[]]@()

Get-AzStorageSyncGroup -ResourceGroupName $resourceGroup -StorageSyncServiceName $syncService | ForEach-Object {
    $syncGroups += $_.SyncGroupName
}

if ($syncGroups -notcontains $syncGroup) {
    throw [System.Exception]::new("The provided sync group $syncGroup does not exist.")
}

# Get reference to cloud endpoint
$cloudEndpoint = Get-AzStorageSyncCloudEndpoint `
    -ResourceGroupName $resourceGroup `
    -StorageSyncServiceName $syncService `
    -SyncGroupName $syncGroup

# Get reference to storage account
$storageAccount = Get-AzStorageAccount | Where-Object { 
    $_.Id -eq $cloudEndpoint.StorageAccountResourceId
}

if ($storageAccount -eq $null) {
    throw [System.Exception]::new("The storage account referenced in the cloud endpoint does not exist.")
}
```
---

<a id="troubleshoot-network-rules"></a>**Sprawdź, upewnij się, że konto magazynu nie zawiera żadnych reguł sieciowych.**  
# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
1. Raz na koncie magazynu, wybierz **zapory i sieci wirtualne** po lewej stronie konta magazynu.
2. Konto magazynu — wewnątrz **zezwolić na dostęp ze wszystkich sieci** należy wybrać przycisk radiowy.
    ![Zrzut ekranu przedstawiający reguły zapory i sieci konta magazynu wyłączone.](media/storage-sync-files-troubleshoot/file-share-inaccessible-2.png)

# <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)
```powershell
if ($storageAccount.NetworkRuleSet.DefaultAction -ne 
    [Microsoft.Azure.Commands.Management.Storage.Models.PSNetWorkRuleDefaultActionEnum]::Allow) {
    throw [System.Exception]::new("The storage account referenced contains network " + `
        "rules which are not currently supported by Azure File Sync.")
}
```
---

<a id="troubleshoot-azure-file-share"></a>**Upewnij się, że istnieje udział plików platformy Azure.**  
# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
1. Kliknij przycisk **Przegląd** w tabeli po lewej stronie spisu treści, aby wrócić do strony konto magazynu głównego.
2. Wybierz **pliki** do wyświetlania listy udziałów plików.
3. Sprawdź udział plików, które odwołuje się punkt końcowy w chmurze pojawia się na liście udziałów plików (powinien mieć zanotowaną to w kroku 1 powyżej).

# <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)
```powershell
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $cloudEndpoint.AzureFileShareName -and
    $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    throw [System.Exception]::new("The Azure file share referenced by the cloud endpoint does not exist")
}
```
---

<a id="troubleshoot-rbac"></a>**Upewnij się, że usługi Azure File Sync ma dostęp do konta magazynu.**  
# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
1. Kliknij przycisk **kontrola dostępu (IAM)** w spisie treści po lewej stronie.
1. Kliknij przycisk **przypisań ról** kartę do listy użytkowników i aplikacji (*jednostki usług*), mają dostęp do swojego konta magazynu.
1. Sprawdź **usługi hybrydowe File Sync** pojawia się na liście za pomocą **czytnik i dostęp do danych** roli. 

    ![Zrzut ekranu przedstawiający nazwy głównej usługi usługi hybrydowe File Sync na karcie kontrola dostępu do konta magazynu](media/storage-sync-files-troubleshoot/file-share-inaccessible-3.png)

    Jeśli **usługi hybrydowe File Sync** nie jest wyświetlana na liście, należy wykonać następujące czynności:

    - Kliknij pozycję **Add** (Dodaj).
    - W **roli** pól, zaznacz **czytnik i dostęp do danych**.
    - W **wybierz** wpisz **usługi hybrydowe File Sync**, wybierz rolę i kliknij przycisk **Zapisz**.

# <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)
```powershell    
$role = Get-AzRoleAssignment -Scope $storageAccount.Id | Where-Object { $_.DisplayName -eq "Hybrid File Sync Service" }

if ($role -eq $null) {
    throw [System.Exception]::new("The storage account does not have the Azure File Sync " + `
                "service principal authorized to access the data within the " + ` 
                "referenced Azure file share.")
}
```
---

### <a name="how-do-i-prevent-users-from-creating-files-containing-unsupported-characters-on-the-server"></a>Jak uniemożliwić użytkownikom tworzenie pliki zawierające znaki nieobsługiwane na serwerze?
Możesz użyć [osłon plików Menedżera zasobów serwera plików (FSRM)](https://docs.microsoft.com/windows-server/storage/fsrm/file-screening-management) plikach bloku z nieobsługiwanych znaków w nazwach z tworzona na serwerze. Trzeba to zrobić za pomocą programu PowerShell, jak najbardziej nieobsługiwane znaki nie są drukowalnych i dlatego trzeba najpierw rzutowania ich reprezentacji szesnastkowej jako znaki.

Najpierw utwórz grupę plików Menedżera zasobów serwera plików przy użyciu [polecenia cmdlet New-FsrmFileGroup](https://docs.microsoft.com/powershell/module/fileserverresourcemanager/new-fsrmfilegroup). W tym przykładzie definiuje grupę, która zawiera tylko dwa nieobsługiwane znaki, ale może zawierać tyle znaków zgodnie z potrzebami w grupie plików.

```powershell
New-FsrmFileGroup -Name "Unsupported characters" -IncludePattern @(("*"+[char]0x00000090+"*"),("*"+[char]0x0000008F+"*"))
```

Po zdefiniowaniu grupy plików Menedżera zasobów serwera plików, można utworzyć osłonę plików Menedżera zasobów serwera plików za pomocą polecenia cmdlet New-FsrmFileScreen.

```powershell
New-FsrmFileScreen -Path "E:\AFSdataset" -Description "Filter unsupported characters" -IncludeGroup "Unsupported characters"
```

> [!Important]  
> Należy pamiętać, że osłon plików powinna służyć wyłącznie do blokowania tworzenia znaki nie są obsługiwane przez usługę Azure File Sync. Jeśli osłon plików są używane w innych scenariuszach, synchronizacji stale podejmie próbę pobrania plików z udziału plików platformy Azure do serwera i zostanie zablokowana z powodu osłony plików skutkuje dużej ilości danych wychodzących. 

## <a name="cloud-tiering"></a>Obsługa warstw w chmurze 
Istnieją dwie ścieżki do awarii w chmurze warstw:

- Pliki może zakończyć się niepowodzeniem do warstwy, co oznacza, że usługi Azure File Sync niepomyślnie próbuje warstwy pliku do usługi Azure Files.
- Plików rozproszeniem do wycofywania, co oznacza, że filtru systemu plików usługi Azure File Sync (StorageSync.sys) kończy się niepowodzeniem, aby pobrać dane, gdy użytkownik próbuje uzyskać dostęp do pliku, który został warstwy.

Istnieją dwa główne klasy błędów, które mogą wystąpić przy użyciu albo ścieżka błędu:

- Błędów magazynu w chmurze
    - *Problemy z dostępnością usługi magazynu przejściowego*. Aby uzyskać więcej informacji, zobacz [Umowa dotycząca poziomu usług (SLA) dla usługi Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).
    - *Udział plików platformy Azure niedostępny*. Ten błąd zazwyczaj występuje, gdy usuniesz udział plików platformy Azure, gdy nadal jest punkt końcowy w chmurze w grupie synchronizacji.
    - *Konto magazynu niedostępny*. Ten błąd zazwyczaj występuje, gdy usunięcia konta magazynu, gdy ma ono nadal udziału plików platformy Azure, która jest punkt końcowy w chmurze w grupie synchronizacji. 
- Błędy serwera 
  - *Usługa Azure File Sync filtru systemu plików (StorageSync.sys) nie został załadowany*. Aby reagować na żądania obsługi warstw na/odwołania, muszą być ładowane filtru systemu plików usługi Azure File Sync. Filtr nie są ładowane może się zdarzyć z kilku powodów, ale najbardziej typową przyczyną jest, że administrator zwolnione go ręcznie. Filtr systemu plików usługi Azure File Sync musi być załadowany cały czas dla usługi Azure File Sync prawidłowo funkcji.
  - *Brak, uszkodzony lub w inny sposób punktu ponownej analizy w uszkodzona*. Punkt ponownej analizy to struktura danych specjalne w pliku, który składa się z dwóch części:
    1. Tag ponownej analizy, co oznacza systemowi operacyjnemu filtru systemu plików usługi Azure File Sync (StorageSync.sys) może być konieczne czynności niektóre na we/wy do pliku. 
    2. Ponownej analizy danych, co oznacza filtru systemu plików, identyfikator URI pliku na skojarzonego punktu końcowego w chmurze (udział plików platformy Azure). 
        
       Najczęstszym sposobem, które mogły ulec uszkodzeniu punkt ponownej analizy to, jeśli administrator próbuje zmodyfikować tagu lub jego danych. 
  - *Problemy z połączeniem sieci*. Aby można było warstwy lub odwołanie pliku, serwer musi mieć łączność z Internetem.

Poniższe sekcje określają, jak rozwiązywanie problemów obsługi warstw w chmurze i określ, czy problem występuje problem z magazynem chmury lub błąd serwera.

<a id="monitor-tiering-activity"></a>**Jak monitorować działania obsługi warstw na serwerze**  
Aby monitorować aktywność obsługi warstw na serwerze, należy użyć Event ID 9003 9016 i 9029 w dzienniku zdarzeń Telemetrii (znajdujący się w aplikacji i Services\Microsoft\FileSync\Agent w Podglądzie zdarzeń).

- Identyfikator zdarzenia 9003 zapewnia rozkład błędów dla punktu końcowego serwera. Np. Łączna liczba błędów, kod błędu, itd. Uwaga: jedno zdarzenie jest rejestrowane na kod błędu.
- Identyfikator zdarzenia 9016 zapewnia duplikowania wyniki dla woluminu. Na przykład wolnego miejsca, które jest procent, liczba plików jest zduplikowany w sesji, liczba plików nie powiodło się z ghost itp.
- Identyfikator zdarzenia 9029 informacje duplikowania sesji dla punktu końcowego serwera. Na przykład warstwy liczbę plików, które podjęto w danej sesji, liczba plików w danej sesji, liczby plików już warstwowe, itp.

<a id="monitor-recall-activity"></a>**Jak monitorować działania odwołania na serwerze**  
Aby monitorować aktywność odwołania na serwerze, należy użyć Event ID 9005 9006 i 9009 9059 w dzienniku zdarzeń Telemetrii (znajdujący się w aplikacji i Services\Microsoft\FileSync\Agent w Podglądzie zdarzeń).

- Identyfikator zdarzenia 9005 zapewnia niezawodność odwołania dla punktu końcowego serwera. Na przykład łączna liczba unikatowych plików uzyskiwać dostęp, a łączna liczba unikatowych plików nieudanych prób dostępu itd.
- Identyfikator zdarzenia 9006 zapewnia rozkład błędów odwołań dla punktu końcowego serwera. Np. Łączna liczba żądań nie powiodło się, kod błędu, itd. Uwaga: jedno zdarzenie jest rejestrowane na kod błędu.
- Identyfikator zdarzenia 9009 informacje wycofaniu sesji dla punktu końcowego serwera. Na przykład DurationSeconds, CountFilesRecallSucceeded, CountFilesRecallFailed, itd.
- Identyfikator zdarzenia 9059 zapewnia dystrybucję wycofaniu aplikacji dla punktu końcowego serwera. Na przykład ShareId, nazwę aplikacji i TotalEgressNetworkBytes.

<a id="files-fail-tiering"></a>**Rozwiązywanie problemów z plikami, które nie są do warstwy**  
Jeśli pliki nie powiodły się warstwy do usługi Azure Files:

1. W Podglądzie zdarzeń przejrzyj dane telemetryczne, operacyjne i diagnostycznych dzienników zdarzeń, znajdujący się w aplikacji i Services\Microsoft\FileSync\Agent. 
   1. Sprawdź, czy pliki znajdują się w udziale plików platformy Azure.

      > [!NOTE]
      > Plik musi być synchronizowane z usługą udziału plików platformy Azure przed może być warstwowego.

   2. Sprawdź, czy serwer ma połączenie z Internetem. 
   3. Sprawdź, czy sterowniki filtrów usługi Azure File Sync (StorageSync.sys i StorageSyncGuard.sys) są uruchomione:
       - W wierszu polecenia z podwyższonym poziomem uprawnień uruchom `fltmc`. Sprawdź, czy wymienione są StorageSync.sys i StorageSyncGuard.sys sterowniki filtrów systemu plików.

> [!NOTE]
> 9003 Identyfikatora zdarzenia jest rejestrowane co godzinę w dzienniku zdarzeń Telemetrii, jeśli plik nie powiedzie się warstwy (jedno zdarzenie jest rejestrowane na kod błędu). Operacyjne i dzienników zdarzeń diagnostycznych powinny być używane, jeśli potrzebne są dodatkowe informacje w celu zdiagnozowania problemu.

<a id="files-fail-recall"></a>**Rozwiązywanie problemów z plików, których nie można odwołać**  
Jeśli pliki nie powiodły się przypomnieć:
1. W Podglądzie zdarzeń przejrzyj dane telemetryczne, operacyjne i diagnostycznych dzienników zdarzeń, znajdujący się w aplikacji i Services\Microsoft\FileSync\Agent.
    1. Sprawdź, czy pliki znajdują się w udziale plików platformy Azure.
    2. Sprawdź, czy serwer ma połączenie z Internetem. 
    3. Otwórz przystawkę MMC usług i sprawdź, czy Usługa agenta synchronizacji magazynu (FileSyncSvc) jest uruchomiona.
    4. Sprawdź, czy sterowniki filtrów usługi Azure File Sync (StorageSync.sys i StorageSyncGuard.sys) są uruchomione:
        - W wierszu polecenia z podwyższonym poziomem uprawnień uruchom `fltmc`. Sprawdź, czy wymienione są StorageSync.sys i StorageSyncGuard.sys sterowniki filtrów systemu plików.

> [!NOTE]
> 9006 Identyfikatora zdarzenia jest rejestrowane raz na godzinę w dzienniku zdarzeń Telemetrii, jeśli plik nie może odwołać (jedno zdarzenie jest rejestrowane na kod błędu). Operacyjne i dzienników zdarzeń diagnostycznych powinny być używane, jeśli potrzebne są dodatkowe informacje w celu zdiagnozowania problemu.

<a id="files-unexpectedly-recalled"></a>**Rozwiązywanie problemów z nieoczekiwanym przypomnieć na serwerze plików**  
Oprogramowanie antywirusowe, kopia zapasowa i inne aplikacje, które odczytują dużą liczbę plików spowodować niezamierzone odwołania, chyba że przestrzegają Pomiń atrybut offline i pomijają odczytywanie zawartości tych plików. Pomijanie plików offline w przypadku produktów, które obsługują tę opcję, umożliwia uniknięcie niezamierzonych odwołań podczas operacji takich jak skanowanie antywirusowe lub zadania kopii zapasowej.

Skonsultuj się z dostawcą oprogramowania, aby dowiedzieć się, w jaki sposób skonfigurować rozwiązanie tak, aby odczytywanie plików offline było pomijane.

Niezamierzonym odwołania może również wystąpić w innych scenariuszach, takich jak podczas przeglądania plików w Eksploratorze plików. Otwarcie folderu, w którym znajdują się pliki w warstwach chmury, w Eksploratorze plików na serwerze może spowodować niezamierzone odwołania. Prawdopodobieństwo wystąpienia takiej sytuacji jest wyższe, jeśli na serwerze włączono rozwiązanie antywirusowe.

> [!NOTE]
>Użyj 9059 identyfikator zdarzenia w dzienniku zdarzeń Telemetrii, aby określić aplikacje, które powoduje odwołania. To zdarzenie umożliwia aplikacji odwołanie dystrybucji dla punktu końcowego serwera i jest rejestrowany raz na godzinę.

## <a name="general-troubleshooting"></a>Rozwiązywanie ogólnych
Jeśli wystąpią problemy z usługi Azure File Sync na serwerze, należy uruchomić, wykonując następujące czynności:
1. W Podglądzie zdarzeń przejrzyj dane telemetryczne, dzienniki zdarzeń operacyjnych i diagnostyki.
    - Synchronizacji, Obsługa poziomów w chmurze i wycofaniu problemów są rejestrowane w danych telemetrycznych, diagnostyki i operacyjne dzienniki zdarzeń aplikacji i Services\Microsoft\FileSync\Agent.
    - Problemy związane z zarządzaniem serwera (na przykład ustawienia konfiguracji) są rejestrowane w dziennikach zdarzeń operacyjnych i diagnostyki aplikacji i Services\Microsoft\FileSync\Management.
2. Sprawdź, czy usługa Azure File Sync jest uruchomiona na serwerze:
    - Otwórz przystawkę MMC usług i sprawdź, czy Usługa agenta synchronizacji magazynu (FileSyncSvc) jest uruchomiona.
3. Sprawdź, czy sterowniki filtrów usługi Azure File Sync (StorageSync.sys i StorageSyncGuard.sys) są uruchomione:
    - W wierszu polecenia z podwyższonym poziomem uprawnień uruchom `fltmc`. Sprawdź, czy wymienione są StorageSync.sys i StorageSyncGuard.sys sterowniki filtrów systemu plików.

Jeśli problem nie zostanie rozwiązany, należy uruchomić narzędzie AFSDiag:
1. Utwórz katalog, w którym zostaną zapisane dane wyjściowe AFSDiag (na przykład C:\Output).
2. Otwórz okno programu PowerShell z podwyższonym, a następnie uruchom następujące polecenia (po każdym poleceniu naciśnij klawisz Enter):

    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. Poziom śledzenia uzyskiwania informacji na temat trybu jądra usługi Azure File Sync wprowadź **1** (chyba że określono inaczej, aby utworzyć bardziej szczegółowy śledzenia), a następnie naciśnij klawisz Enter.
4. Poziom śledzenia uzyskiwania informacji na temat trybu użytkownika usługi Azure File Sync wprowadź **1** (chyba że określono inaczej, aby utworzyć bardziej szczegółowy śledzenia), a następnie naciśnij klawisz Enter.
5. Odtwórz problem. Po zakończeniu tego samouczka wprowadź **D**.
6. Plik .zip zawierający dzienniki oraz pliki śledzenia są zapisywane do katalogu wyjściowego, który określiłeś.

## <a name="see-also"></a>Zobacz także
- [Monitorowanie usługi Azure File Sync](storage-sync-files-monitoring.md)
- [Usługa Azure Files — często zadawane pytania](storage-files-faq.md)
- [Rozwiązywanie problemów z usługą Azure Files w systemie Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Troubleshoot Azure Files problems in Linux (Rozwiązywanie problemów z usługą Azure Files w systemie Linux)](storage-troubleshoot-linux-file-connection-problems.md).
