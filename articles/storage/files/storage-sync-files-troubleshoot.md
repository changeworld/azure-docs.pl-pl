---
title: Rozwiązywanie problemów z synchronizacją plików platformy Azure | Dokumenty firmy Microsoft
description: Rozwiązywanie typowych problemów z synchronizacją plików platformy Azure.
author: jeffpatt24
ms.service: storage
ms.topic: conceptual
ms.date: 1/22/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: d46f513fccf9921d4cf47835bc9d5be4c6ffe241
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80607488"
---
# <a name="troubleshoot-azure-file-sync"></a>Rozwiązywanie problemów z usługą Azure File Sync
Użyj usługi Azure File Sync, aby scentralizować udziały plików organizacji w usłudze Azure Files, zachowując elastyczność, wydajność i zgodność lokalnego serwera plików. Funkcja Azure File Sync przekształca system Windows Server w szybką pamięć podręczną udziału plików platformy Azure. Można użyć dowolnego protokołu dostępnego w systemie Windows Server, aby uzyskać dostęp do danych lokalnie, w tym SMB, NFS i FTPS. Możesz mieć tyle pamięci podręcznych, ile potrzebujesz na całym świecie.

Ten artykuł został zaprojektowany, aby ułatwić rozwiązywanie problemów, które mogą wystąpić podczas wdrażania usługi Azure File Sync. Opisano również, jak zbierać ważne dzienniki z systemu, jeśli wymagane jest głębsze badanie problemu. Jeśli nie widzisz odpowiedzi na swoje pytanie, możesz skontaktować się z nami za pośrednictwem następujących kanałów (w kolejności eskalacji):

1. [Forum usługi Azure Storage](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).
2. [Głos użytkownika plików platformy Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files).
3. Pomoc techniczna firmy Microsoft. Aby utworzyć nowe żądanie pomocy technicznej, w witrynie Azure portal na karcie **Pomoc** wybierz przycisk **Pomoc + pomoc,** a następnie wybierz pozycję Nowe żądanie pomocy **technicznej**.

## <a name="im-having-an-issue-with-azure-file-sync-on-my-server-sync-cloud-tiering-etc-should-i-remove-and-recreate-my-server-endpoint"></a>Mam problem z synchronizacją plików platformy Azure na moim serwerze (synchronizacja, warstwa w chmurze itp.). Czy należy usunąć i ponownie utworzyć punkt końcowy serwera?
[!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]

## <a name="agent-installation-and-server-registration"></a>Instalacja agenta i rejestracja serwera
<a id="agent-installation-failures"></a>**Rozwiązywanie problemów z awariami instalacji agenta**  
Jeśli instalacja agenta usługi Azure File Sync nie powiedzie się, w wierszu polecenia z podwyższonym poziomem uprawnień uruchom następujące polecenie, aby włączyć rejestrowanie podczas instalacji agenta:

```
StorageSyncAgent.msi /l*v AFSInstaller.log
```

Przejrzyj plik installer.log, aby ustalić przyczynę błędu instalacji.

<a id="agent-installation-on-DC"></a>**Instalacja agenta kończy się niepowodzeniem na kontrolerze domeny usługi Active Directory**  
Jeśli spróbujesz zainstalować agenta synchronizacji na kontrolerze domeny usługi Active Directory, na którym właściciel roli kontrolera PDC znajduje się w wersji systemu Windows Server 2008 R2 lub niższej niż system operacyjny, może wystąpić problem polegający na tym, że agent synchronizacji nie zostanie zainstalowany.

Aby rozwiązać ten problem, przenieś rolę kontrolera PDC na inny kontroler domeny z systemem Windows Server 2012 R2 lub nowszym, a następnie zainstaluj synchronizację.

<a id="parameter-is-incorrect"></a>**Uzyskiwanie dostępu do woluminu w systemie Windows Server 2012 R2 kończy się niepowodzeniem z powodu błędu: parametr jest niepoprawny**  
Po utworzeniu punktu końcowego serwera w systemie Windows Server 2012 R2 podczas uzyskiwania dostępu do woluminu występuje następujący błąd:

list napędowy:\ nie jest dostępna.  
Parametr jest nieprawidłowy.

Aby rozwiązać ten problem, zainstaluj najnowsze aktualizacje systemu Windows Server 2012 R2 i uruchom ponownie serwer.

<a id="server-registration-missing-subscriptions"></a>**Rejestracja serwera nie zawiera wszystkich subskrypcji platformy Azure**  
Podczas rejestrowania serwera przy użyciu serverregistration.exe, subskrypcje brakuje po kliknięciu listy rozwijanej subskrypcji platformy Azure.

Ten problem występuje, ponieważ ServerRegistration.exe obecnie nie obsługuje środowisk wielodostępnych. Ten problem zostanie rozwiązany w przyszłej aktualizacji agenta usługi Azure File Sync.

Aby rozwiązać ten problem, użyj następujących poleceń programu PowerShell, aby zarejestrować serwer:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
Login-AzureRmStorageSync -SubscriptionID "<guid>" -TenantID "<guid>"
Register-AzureRmStorageSyncServer -SubscriptionId "<guid>" -ResourceGroupName "<string>" -StorageSyncServiceName "<string>"
```

<a id="server-registration-prerequisites"></a>**Rejestracja serwera wyświetla następujący komunikat: "Brakuje wymagań wstępnych"**  
Ten komunikat pojawia się, jeśli moduł programu Az lub AzureRM PowerShell nie jest zainstalowany w programie PowerShell 5.1. 

> [!Note]  
> Program ServerRegistration.exe nie obsługuje programu PowerShell 6.x. Do zarejestrowania serwera można użyć polecenia cmdlet Register-AzStorageSyncServer w programie PowerShell 6.x.

Aby zainstalować moduł Az lub AzureRM w programie PowerShell 5.1, wykonaj następujące kroki:

1. Wpisz **powershell** z wiersza polecenia z podwyższonym poziomem uprawnień i naciśnij enter.
2. Zainstaluj najnowszy moduł Az lub AzureRM, wykonując dokumentację:
    - [Moduł Az (wymaga .NET 4.7.2)](https://go.microsoft.com/fwlink/?linkid=2062890)
    - [Moduł AzureRM]( https://go.microsoft.com/fwlink/?linkid=856959)
3. Uruchom program ServerRegistration.exe i ukończ kreatora, aby zarejestrować serwer w usłudze synchronizacji magazynu.

<a id="server-already-registered"></a>**Rejestracja serwera wyświetla następujący komunikat: "Ten serwer jest już zarejestrowany"** 

![Zrzut ekranu okna dialogowego Rejestracja serwera z komunikatem o błędzie "serwer jest już zarejestrowany"](media/storage-sync-files-troubleshoot/server-registration-1.png)

Ten komunikat pojawia się, jeśli serwer był wcześniej zarejestrowany w usłudze synchronizacji magazynu. Aby wyrejestrować serwer z bieżącej usługi synchronizacji magazynu, a następnie zarejestrować się w nowej usłudze synchronizacji magazynu, wykonaj kroki opisane w polu [Wyrejestruj serwer za pomocą usługi Azure File Sync](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

Jeśli serwer nie znajduje się na liście w obszarze **Zarejestrowane serwery** w usłudze synchronizacji magazynu, na serwerze, który chcesz wyrejestrować, uruchom następujące polecenia programu PowerShell:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> Jeśli serwer jest częścią klastra, można użyć opcjonalnego *parametru Reset-StorageSyncServer -CleanClusterRegistration,* aby również usunąć rejestrację klastra.

<a id="web-site-not-trusted"></a>**Kiedy rejestruję serwer, widzę wiele odpowiedzi "strona internetowa nie ufa" odpowiedzi. Dlaczego?**  
Ten problem występuje, gdy zasada **rozszerzone zabezpieczenia programu Internet Explorer** jest włączona podczas rejestracji serwera. Aby uzyskać więcej informacji dotyczących prawidłowego wyłączania zasad **zabezpieczeń rozszerzonego programu Internet Explorer,** zobacz [Przygotowywanie systemu Windows Server do użycia z synchronizacją plików platformy Azure](storage-sync-files-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync) i jak [wdrożyć usługę Azure File Sync](storage-sync-files-deployment-guide.md).

<a id="server-registration-missing"></a>**Serwer nie jest wymieniony pod zarejestrowanymi serwerami w witrynie Azure portal**  
Jeśli serwer nie znajduje się na liście w obszarze **Zarejestrowane serwery** dla usługi synchronizacji magazynu:
1. Zaloguj się na serwerze, który chcesz zarejestrować.
2. Otwórz Eksploratora plików, a następnie przejdź do katalogu instalacji agenta synchronizacji magazynu (domyślną lokalizacją jest C:\Program Files\Azure\StorageSyncAgent). 
3. Uruchom program ServerRegistration.exe i ukończ kreatora, aby zarejestrować serwer w usłudze synchronizacji magazynu.

## <a name="sync-group-management"></a>Zarządzanie grupami synchronizacji
<a id="cloud-endpoint-using-share"></a>**Tworzenie punktu końcowego w chmurze kończy się niepowodzeniem, z tym błędem: "Określony program Azure FileShare jest już używany przez inny cloudendpoint"**  
Ten błąd występuje, jeśli udział plików platformy Azure jest już używany przez inny punkt końcowy w chmurze. 

Jeśli widzisz ten komunikat i udział plików platformy Azure obecnie nie jest używany przez punkt końcowy w chmurze, wykonaj następujące kroki, aby wyczyścić metadane usługi Azure File Sync w udziale plików platformy Azure:

> [!Warning]  
> Usunięcie metadanych w udziale plików platformy Azure, który jest obecnie używany przez punkt końcowy w chmurze powoduje niepowodzenie operacji synchronizacji plików platformy Azure. 

1. W witrynie Azure portal przejdź do udziału plików platformy Azure.  
2. Kliknij prawym przyciskiem myszy udział plików platformy Azure, a następnie wybierz polecenie **Edytuj metadane**.
3. Kliknij prawym przyciskiem myszy **pozycję SyncService**, a następnie wybierz polecenie **Usuń**.

<a id="cloud-endpoint-authfailed"></a>**Tworzenie punktu końcowego w chmurze kończy się niepowodzeniem, z tym błędem: "AuthorizationFailed"**  
Ten błąd występuje, jeśli konto użytkownika nie ma wystarczających praw do utworzenia punktu końcowego chmury. 

Aby utworzyć punkt końcowy w chmurze, konto użytkownika musi mieć następujące uprawnienia autoryzacji firmy Microsoft:  
* Przeczytaj: Uzyskaj definicję roli
* Zapis: Tworzenie lub aktualizowanie niestandardowej definicji roli
* Odczyt: Pobierz przypisanie roli
* Zapis: Tworzenie przypisania roli

Następujące wbudowane role mają wymagane uprawnienia autoryzacji firmy Microsoft:  
* Właściciel
* Administrator dostępu użytkowników

Aby ustalić, czy rola konta użytkownika ma wymagane uprawnienia:  
1. W witrynie Azure portal wybierz pozycję **Grupy zasobów**.
2. Wybierz grupę zasobów, w której znajduje się konto magazynu, a następnie wybierz **pozycję Kontrola dostępu (IAM)**.
3. Wybierz kartę **Przypisania ról.**
4. Wybierz **rolę** (na przykład Właściciela lub Współautora) dla swojego konta użytkownika.
5. Na liście **Dostawca zasobów** wybierz pozycję **Autoryzacja firmy Microsoft**. 
    * **Przypisanie roli** powinno mieć uprawnienia **do odczytu** i **zapisu.**
    * **Definicja roli** powinna mieć uprawnienia **do odczytu** i **zapisu.**

<a id="-2134375898"></a>**Tworzenie punktu końcowego serwera kończy się niepowodzeniem, z tym błędem: "MgmtServerJobFailed" (kod błędu: -2134375898 lub 0x80c80226)**  
Ten błąd występuje, gdy ścieżka punktu końcowego serwera znajduje się na woluminie systemowym i obsługa warstw w chmurze jest włączona. Obsługa warstw w chmurze nie jest dostępna na woluminie systemowym. Aby utworzyć punkt końcowy serwera na woluminie systemowym, wyłącz obsługę warstw w chmurze podczas tworzenia punktu końcowego serwera.

<a id="-2147024894"></a>**Tworzenie punktu końcowego serwera kończy się niepowodzeniem, z tym błędem: "MgmtServerJobFailed" (kod błędu: -2147024894 lub 0x80070002)**  
Ten błąd występuje, jeśli określona ścieżka punktu końcowego serwera jest nieprawidłowa. Upewnij się, że określona ścieżka punktu końcowego serwera jest lokalnie dołączonym woluminem NTFS. Uwaga: Usługa Azure File Sync nie obsługuje mapowanych dysków jako ścieżki punktu końcowego serwera.

<a id="-2134375640"></a>**Tworzenie punktu końcowego serwera kończy się niepowodzeniem, z tym błędem: "MgmtServerJobFailed" (kod błędu: -2134375640 lub 0x80c80328)**  
Ten błąd występuje, jeśli określona ścieżka punktu końcowego serwera nie jest woluminem NTFS. Upewnij się, że określona ścieżka punktu końcowego serwera jest lokalnie dołączonym woluminem NTFS. Uwaga: Usługa Azure File Sync nie obsługuje mapowanych dysków jako ścieżki punktu końcowego serwera.

<a id="-2134347507"></a>**Tworzenie punktu końcowego serwera kończy się niepowodzeniem, z tym błędem: "MgmtServerJobFailed" (kod błędu: -2134347507 lub 0x80c8710d)**  
Ten błąd występuje, ponieważ usługa Azure File Sync nie obsługuje punktów końcowych serwera na woluminach, które mają skompresowany katalog informacji o woluminie systemowym. Aby rozwiązać ten problem, zdekompresuj katalog informacji o woluminie systemowym. Jeśli katalog informacji o woluminie systemowym jest jedynym skompresowanym katalogiem na woluminie, wykonaj następujące czynności:

1. Pobierz narzędzie [PsExec.](https://docs.microsoft.com/sysinternals/downloads/psexec)
2. Uruchom następujące polecenie z wiersza polecenia z podwyższonym poziomem uprawnień, aby uruchomić wiersz polecenia działający pod kontem systemowym: **PsExec.exe -i -s -d cmd**
3. W wierszu polecenia uruchomionym w ramach konta systemowego wpisz następujące polecenia i naciśnij klawisz Enter:   
    **cd /d "litera dysku:\Informacje o woluminie systemowym"**  
    **kompaktowy /u /s**

<a id="-2134376345"></a>**Tworzenie punktu końcowego serwera kończy się niepowodzeniem, z tym błędem: "MgmtServerJobFailed" (kod błędu: -2134376345 lub 0x80C80067)**  
Ten błąd występuje, gdy zostanie osiągnięty limit liczby punktów końcowych serwera. Usługa Azure File Sync obsługuje obecnie do 30 punktów końcowych serwera na serwer. Aby uzyskać więcej informacji, zobacz [Cele skalowania synchronizacji plików platformy Azure](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#azure-file-sync-scale-targets).

<a id="-2134376427"></a>**Tworzenie punktu końcowego serwera kończy się niepowodzeniem, z tym błędem: "MgmtServerJobFailed" (kod błędu: -2134376427 lub 0x80c80015)**  
Ten błąd występuje, gdy inny punkt końcowy serwera synchronizuje określoną ścieżkę punktu końcowego serwera. Usługa Azure File Sync nie obsługuje synchronizacji tego samego katalogu lub woluminu w wielu punktach końcowych serwera.

<a id="-2160590967"></a>**Tworzenie punktu końcowego serwera kończy się niepowodzeniem, z tym błędem: "MgmtServerJobFailed" (kod błędu: -2160590967 lub 0x80c80077)**  
Ten błąd występuje, jeśli ścieżka punktu końcowego serwera zawiera oddzielone pliki warstwowe. Jeśli punkt końcowy serwera został niedawno usunięty, poczekaj, aż oczyszczanie oddzielonych plików warstwowych zostanie zakończone. Identyfikator zdarzenia 6662 jest rejestrowany w dzienniku zdarzeń Telemetria po rozpoczęciu oczyszczania osieroconych plików warstwowych. Identyfikator zdarzenia 6661 jest rejestrowany po zakończeniu oczyszczania osieroconych plików warstwowych i odtworzeniu punktu końcowego serwera za pomocą ścieżki. Jeśli tworzenie punktu końcowego serwera nie powiedzie się po zarejestrowaniu identyfikatora zdarzenia 6661, usuń oddzielone pliki warstwowe, wykonując kroki opisane w [plikach warstwowych, które nie są dostępne na serwerze po usunięciu sekcji punktu końcowego serwera.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)

<a id="-2134347757"></a>**Usunięcie punktu końcowego serwera kończy się niepowodzeniem, z tym błędem: "MgmtServerJobExpired" (kod błędu: -2134347757 lub 0x80c87013)**  
Ten błąd występuje wtedy, gdy serwer jest w trybie offline lub nie ma łączności sieciowej. Jeśli serwer nie jest już dostępny, wyrejestruj serwer w portalu, co spowoduje usunięcie punktów końcowych serwera. Aby usunąć punkty końcowe serwera, wykonaj kroki opisane w obszarze [Wyrejestruj serwer z synchronizacją plików azure](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

<a id="server-endpoint-provisioningfailed"></a>**Nie można otworzyć strony właściwości punktu końcowego serwera lub zaktualizować zasad warstwowych w chmurze**  
Ten problem może wystąpić, jeśli operacja zarządzania w punkcie końcowym serwera nie powiedzie się. Jeśli strona właściwości punktu końcowego serwera nie jest otwarta w portalu Azure portal, aktualizowanie punktu końcowego serwera przy użyciu poleceń programu PowerShell z serwera może rozwiązać ten problem. 

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
<a id="server-endpoint-noactivity"></a>**Punkt końcowy serwera ma stan kondycji "Brak działania" lub "Oczekujące", a stan serwera w bloku zarejestrowanych serwerów to "Pojawia się w trybie offline"**  

Ten problem może wystąpić, jeśli proces Monitora synchronizacji magazynu (AzureStorageSyncMonitor.exe) nie jest uruchomiony lub serwer nie może uzyskać dostępu do usługi Azure File Sync.

Na serwerze, który jest wyświetlany jako "Pojawia się w trybie offline" w portalu, spójrz na identyfikator zdarzenia 9301 w dzienniku zdarzeń telemetrii (znajduje się w obszarze Aplikacje i usługi\Microsoft\FileSync\Agent w Podglądzie zdarzeń), aby ustalić, dlaczego serwer nie może uzyskać dostępu do usługi Azure File Sync. 

- Jeśli **GetNextJob zakończone ze stanem: 0** jest zalogowany, serwer może komunikować się z usługą Azure File Sync. 
    - Otwórz Menedżera zadań na serwerze i upewnij się, że proces programu Storage Sync Monitor (AzureStorageSyncMonitor.exe) jest uruchomiony. Jeśli proces nie jest uruchomiony, najpierw spróbuj uruchomić ponownie serwer. Jeśli ponowne uruchomienie serwera nie rozwiąże problemu, uaktualnij agenta usługi Azure File Sync do najnowszej [wersji](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes). 

- Jeśli **GetNextJob zakończone ze stanem: -2134347756** jest zalogowany, serwer nie może komunikować się z usługą Azure File Sync z powodu zapory lub serwera proxy. 
    - Jeśli serwer znajduje się za zaporą, sprawdź, czy ruch wychodzący na port 443 jest dozwolony. Jeśli zapora ogranicza ruch do określonych domen, upewnij się, że domeny wymienione w [dokumentacji](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#firewall) zapory są dostępne.
    - Jeśli serwer znajduje się za serwerem proxy, skonfiguruj ustawienia serwera proxy dla całego komputera lub aplikacji, wykonując kroki opisane w [dokumentacji](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#proxy)serwera proxy .
    - Polecenie cmdlet Test-StorageSyncNetworkConnectivity służy do sprawdzania łączności sieciowej z punktami końcowymi usługi. Aby dowiedzieć się więcej, zobacz [Testowanie łączności sieciowej z punktami końcowymi usługi](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#test-network-connectivity-to-service-endpoints).

- Jeśli **GetNextJob zakończone o stanie: -2134347764** jest zalogowany, serwer nie może komunikować się z usługą Azure File Sync z powodu wygasłego lub usuniętego certyfikatu.  
    - Uruchom następujące polecenie programu PowerShell na serwerze, aby zresetować certyfikat używany do uwierzytelniania:
    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```
<a id="endpoint-noactivity-sync"></a>**Punkt końcowy serwera ma stan kondycji "Brak działania", a stan serwera w bloku zarejestrowanych serwerów to "Online"**  

Stan kondycji punktu końcowego serwera "Brak działania" oznacza, że punkt końcowy serwera nie zarejestrował aktywności synchronizacji w ciągu ostatnich dwóch godzin.

Aby sprawdzić bieżącą aktywność synchronizacji na serwerze, zobacz [Jak monitorować postęp bieżącej sesji synchronizacji?](#how-do-i-monitor-the-progress-of-a-current-sync-session).

Punkt końcowy serwera może nie rejestrować aktywności synchronizacji przez kilka godzin z powodu błędu lub niewystarczających zasobów systemowych. Sprawdź, czy zainstalowana jest najnowsza [wersja agenta](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes) usługi Azure File Sync. Jeśli problem będzie się powtarzał, otwórz żądanie pomocy technicznej.

> [!Note]  
> Jeśli stan serwera w bloku zarejestrowanych serwerów to "Pojawia się w trybie offline", wykonaj kroki opisane w [punkcie końcowym serwera ma stan kondycji "Brak działania" lub "Oczekujące", a stan serwera w bloku zarejestrowanych serwerów to sekcja "Pojawia się w trybie offline".](#server-endpoint-noactivity)

## <a name="sync"></a>Sync
<a id="afs-change-detection"></a>**Jeśli plik został utworzony bezpośrednio w moim udziale plików platformy Azure za pośrednictwem SMB lub portalu, jak długo trwa synchronizacja pliku z serwerami w grupie synchronizacji?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="serverendpoint-pending"></a>**Kondycja punktu końcowego serwera jest w stanie oczekiwania przez kilka godzin**  
Ten problem jest oczekiwany, jeśli utworzysz punkt końcowy chmury i użyjesz udziału plików platformy Azure, który zawiera dane. Zadanie wyliczenia zmiany, które skanuje w poszukiwaniu zmian w udziale plików platformy Azure musi zostać ukończone, zanim pliki będą mogły synchronizować między punktami końcowymi chmury i serwera. Czas ukończenia zadania zależy od rozmiaru obszaru nazw w udziale plików platformy Azure. Kondycja punktu końcowego serwera powinna zostać zaktualizowana po zakończeniu zadania wyliczenia zmiany.

### <a name="how-do-i-monitor-sync-health"></a><a id="broken-sync"></a>Jak monitorować kondycję synchronizacji?
# <a name="portal"></a>[Portal](#tab/portal1)
W ramach każdej grupy synchronizacji można przejść do szczegółów poszczególnych punktów końcowych serwera, aby wyświetlić stan ostatnich zakończonych sesji synchronizacji. Zielona kolumna Kondycja i wartość niesynchronizowania plików 0 wskazują, że synchronizacja działa zgodnie z oczekiwaniami. Jeśli tak nie jest, zobacz poniżej listę typowych błędów synchronizacji i jak obsługiwać pliki, które nie są synchronizowane. 

![Zrzut ekranu przedstawiający witrynę Azure portal](media/storage-sync-files-troubleshoot/portal-sync-health.png)

# <a name="server"></a>[Serwer](#tab/server)
Przejdź do dzienników telemetrycznych serwera, które można znaleźć `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`w Podglądzie zdarzeń pod adresem . Zdarzenie 9102 odpowiada zakończonej sesji synchronizacji; aby uzyskać najnowszy stan synchronizacji, poszukaj najnowszego zdarzenia o identyfikatorze 9102. SyncDirection informuje, czy ta sesja była upload lub download. Jeśli HResult jest 0, a następnie sesji synchronizacji zakończył się pomyślnie. Non-zero HResult oznacza, że wystąpił błąd podczas synchronizacji; poniżej znajduje się lista typowych błędów. Jeśli PerItemErrorCount jest większa niż 0, oznacza to, że niektóre pliki lub foldery nie zostały poprawnie zsynchronizowane. Jest możliwe, aby HResult 0, ale PerItemErrorCount, który jest większy niż 0.

Poniżej znajduje się przykład pomyślnego przesłania. Ze względu na zwięzłość, tylko niektóre wartości zawarte w każdym zdarzeniu 9102 są wymienione poniżej. 

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: 0, 
SyncFileCount: 2, SyncDirectoryCount: 0,
AppliedFileCount: 2, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0,
TransferredFiles: 2, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Z drugiej strony nieudane przesyłanie może wyglądać następująco:

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: -2134364065,
SyncFileCount: 0, SyncDirectoryCount: 0, 
AppliedFileCount: 0, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0, 
TransferredFiles: 0, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Czasami sesje synchronizacji nie powiedzie się ogólnie lub mają non-zero PerItemErrorCount, ale nadal do przodu postępu, z niektórych plików synchronizacji pomyślnie. Można to zobaczyć w polach Applied* (AppliedFileCount, AppliedDirCount, AppliedTombstoneCount i AppliedSizeBytes), które informują, jaka część sesji jest pomyślna. Jeśli widzisz wiele sesji synchronizacji z rzędu, które nie popełniają się, ale mają rosnącą liczbę Applied*, należy dać czas synchronizacji, aby spróbować ponownie przed otwarciem biletu pomocy technicznej.

---

### <a name="how-do-i-monitor-the-progress-of-a-current-sync-session"></a>Jak monitorować postęp bieżącej sesji synchronizacji?
# <a name="portal"></a>[Portal](#tab/portal1)
W grupie synchronizacji przejdź do danego punktu końcowego serwera i spójrz na sekcję Działanie synchronizacji, aby zobaczyć liczbę plików przekazanych lub pobranych w bieżącej sesji synchronizacji. Należy zauważyć, że ten stan będzie opóźniony o około 5 minut, a jeśli sesja synchronizacji jest wystarczająco mała, aby można ją było ukończyć w tym okresie, może nie zostać zgłoszona w portalu. 

# <a name="server"></a>[Serwer](#tab/server)
Spójrz na najnowsze zdarzenie 9302 w dzienniku telemetrii na serwerze (w Podglądzie zdarzeń przejdź do dzienników aplikacji i usług\Microsoft\FileSync\Agent\Telemetria). To zdarzenie wskazuje stan bieżącej sesji synchronizacji. TotalItemCount oznacza, ile plików mają być synchronizowane, AppliedItemCount liczbę plików, które zostały zsynchronizowane do tej pory i PerItemErrorCount liczbę plików, które nie są synchronizowane (patrz poniżej, jak radzić sobie z tym).

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

### <a name="how-do-i-know-if-my-servers-are-in-sync-with-each-other"></a>Jak sprawdzić, czy serwery są zsynchronizowane ze sobą?
# <a name="portal"></a>[Portal](#tab/portal1)
Dla każdego serwera w danej grupie synchronizacji upewnij się, że:
- Sygnatury czasowe ostatniej próby synchronizacji zarówno dla przekazywania, jak i pobierania są najnowsze.
- Stan jest zielony zarówno dla wysyłania, jak i pobierania.
- Pole Działanie synchronizacji zawiera bardzo niewiele plików do synchronizacji lub nie ma ich do zsynchronizowania.
- Pole Niesynchronizowane plików jest 0 dla przekazywania i pobierania.

# <a name="server"></a>[Serwer](#tab/server)
Spójrz na ukończone sesje synchronizacji, które są oznaczone zdarzeniami 9102 w dzienniku zdarzeń `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`telemetrycznych dla każdego serwera (w Podglądzie zdarzeń przejdź do ). 

1. Na dowolnym danym serwerze chcesz upewnić się, że najnowsze sesje przekazywania i pobierania zostały pomyślnie zakończone. Aby to zrobić, sprawdź, czy HResult i PerItemErrorCount są 0 zarówno przekazywania i pobierania (SyncDirection pole wskazuje, czy dana sesja jest upload lub download sesji). Należy zauważyć, że jeśli nie widzisz niedawno ukończonej sesji synchronizacji, prawdopodobnie sesja synchronizacji jest obecnie w toku, czego należy się spodziewać, jeśli właśnie dodano lub zmodyfikowano dużą ilość danych.
2. Gdy serwer jest w pełni aktualny z chmurą i nie ma żadnych zmian do synchronizacji w obu kierunkach, zobaczysz puste sesje synchronizacji. Są one wskazywane przez zdarzenia przekazywania i pobierania, w których wszystkie pola Sync* (SyncFileCount, SyncDirCount, SyncTombstoneCount i SyncSizeBytes) są zerowe, co oznacza, że nie było nic do synchronizacji. Należy zauważyć, że te puste sesje synchronizacji mogą nie występować na serwerach o wysokiej rezygnacji, ponieważ zawsze jest coś nowego do synchronizacji. Jeśli nie ma żadnych działań synchronizacji, powinny one występować co 30 minut. 
3. Jeśli wszystkie serwery są aktualne w chmurze, co oznacza, że ich ostatnie sesje przesyłania i pobierania są pustymi sesjami synchronizacji, możesz z dużą pewnością powiedzieć, że system jako całość jest zsynchronizowany. 
    
Należy zauważyć, że jeśli wprowadzono zmiany bezpośrednio w udziale plików platformy Azure, usługa Azure File Sync nie wykryje tej zmiany, dopóki nie zostanie wprowadzone wyliczenie zmian, co zdarza się raz na 24 godziny. Jest możliwe, że serwer powie, że jest na bieżąco z chmury, gdy w rzeczywistości brakuje ostatnich zmian wprowadzonych bezpośrednio w udziale plików platformy Azure. 

---

### <a name="how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing"></a>Jak mogę sprawdzić, czy istnieją określone pliki lub foldery, które nie są synchronizowane?
Jeśli PerItemErrorCount na serwerze lub liczba plików nie synchronizacji w portalu są większe niż 0 dla danej sesji synchronizacji, oznacza to, że niektóre elementy nie są synchronizowane. Pliki i foldery mogą mieć właściwości, które uniemożliwiają ich synchronizację. Te cechy mogą być trwałe i wymagają jawnych działań, aby wznowić synchronizację, na przykład usunięcie nieobsługiconych znaków z nazwy pliku lub folderu. Mogą być również przejściowe, co oznacza, że plik lub folder zostanie automatycznie wznowiony synchronizacji; na przykład pliki z otwartymi uchwytami zostaną automatycznie wznowione po zamknięciu pliku. Gdy aparat synchronizacji plików platformy Azure wykryje taki problem, jest produkowany dziennik błędów, który można przeanalizować, aby wyświetlić listę elementów, które obecnie nie są poprawnie synchronizowane.

Aby wyświetlić te błędy, uruchom skrypt **Programu PowerShell FileSyncErrorsReport.ps1** (znajdujący się w katalogu instalacji agenta usługi Azure File Sync) w celu zidentyfikowania plików, których nie udało się zsynchronizować z powodu otwartych uchwytów, nieobsługiconych znaków lub innych problemów. Pole ItemPath informuje o lokalizacji pliku w odniesieniu do katalogu synchronizacji głównej. Zobacz listę typowych błędów synchronizacji poniżej, aby uzyskać kroki korygowania.

> [!Note]  
> Jeśli skrypt FileSyncErrorsReport.ps1 zwraca komunikat "Nie znaleziono żadnych błędów plików" lub nie zawiera listy błędów na element dla grupy synchronizacji, przyczyną jest:
>
>- Przyczyna 1: Ostatnia ukończona sesja synchronizacji nie miała błędów na element. Portal powinien zostać wkrótce zaktualizowany, aby wyświetlić 0 Pliki nie są synchronizowane. 
>   - Sprawdź [identyfikator zdarzenia 9102](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) w dzienniku zdarzeń telemetrii, aby potwierdzić, że licznik PerItemErrorCount wynosi 0. 
>
>- Przyczyna 2: ItemResults dziennika zdarzeń na serwerze opakowane z powodu zbyt wielu błędów na element i dziennik zdarzeń nie zawiera już błędów dla tej grupy synchronizacji.
>   - Aby zapobiec temu problemowi, zwiększ rozmiar dziennika zdarzeń ItemResults. Dziennik zdarzeń ItemResults można znaleźć w obszarze "Dzienniki aplikacji i usług\Microsoft\FileSync\Agent" w Podglądzie zdarzeń. 

#### <a name="troubleshooting-per-filedirectory-sync-errors"></a>Rozwiązywanie problemów z błędami synchronizacji plików/katalogów
**ItemResults log - błędy synchronizacji dla elementu**  

| HRESULT | HRESULT (dziesiętny) | Ciąg błędu | Problem | Korekty |
|---------|-------------------|--------------|-------|-------------|
| 0x80070043 | -2147942467 | ERROR_BAD_NET_NAME | Plik warstwowy na serwerze jest niedostępny. Ten problem występuje, gdy plik warstwowy nie został odwołany przed usunięciem punktu końcowego serwera. | Aby rozwiązać ten problem, zobacz [Pliki warstwowe nie są dostępne na serwerze po usunięciu punktu końcowego serwera](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint). |
| 0x80c80207 | -2134375929 | ECS_E_SYNC_CONSTRAINT_CONFLICT | Nie można jeszcze zsynchronizować zmiany pliku lub katalogu, ponieważ folder zależny nie został jeszcze zsynchronizowany. Ten element zostanie zsynchronizowany po zsynchronizowanie zmian zależnych. | Żadna akcja nie jest wymagana. Jeśli błąd będzie się powtarzał przez kilka dni, użyj skryptu Programu PowerShell FileSyncErrorsReport.ps1, aby ustalić, dlaczego folder zależny nie jest jeszcze zsynchronizowany. |
| 0x80c80284 | -2134375804 | ECS_E_SYNC_CONSTRAINT_CONFLICT_SESSION_FAILED | Nie można jeszcze zsynchronizować zmiany pliku lub katalogu, ponieważ folder zależny nie został jeszcze zsynchronizowany, a sesja synchronizacji nie powiodła się. Ten element zostanie zsynchronizowany po zsynchronizowanie zmian zależnych. | Żadna akcja nie jest wymagana. Jeśli błąd będzie się powtarzał, zbadaj błąd sesji synchronizacji. |
| 0x8007007b | -2147024773 | ERROR_INVALID_NAME | Nazwa pliku lub katalogu jest nieprawidłowa. | Zmień nazwę danego pliku lub katalogu. Aby uzyskać więcej informacji, zobacz [Obsługa nieobsługiwałych znaków.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters) |
| 0x80c80255 | -2134375851 | ECS_E_XSMB_REST_INCOMPATIBILITY | Nazwa pliku lub katalogu jest nieprawidłowa. | Zmień nazwę danego pliku lub katalogu. Aby uzyskać więcej informacji, zobacz [Obsługa nieobsługiwałych znaków.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters) |
| 0x80c80018 | -2134376424 | ECS_E_SYNC_FILE_IN_USE | Nie można zsynchronizować pliku, ponieważ jest on używany. Plik zostanie zsynchronizowany, gdy nie będzie już używany. | Żadna akcja nie jest wymagana. Usługa Azure File Sync tworzy tymczasową migawkę vss raz dziennie na serwerze, aby zsynchronizować pliki, które mają otwarte uchwyty. |
| 0x80c8031d | -2134375651 | ECS_E_CONCURRENCY_CHECK_FAILED | Plik został zmieniony, ale zmiana nie została jeszcze wykryta przez synchronizację. Synchronizacja zostanie odzyskana po wykryciu tej zmiany. | Żadna akcja nie jest wymagana. |
| 0x80070002 | -2147024894 | Error_file_not_found | Plik został usunięty, a synchronizacja nie jest świadoma zmiany. | Żadna akcja nie jest wymagana. Synchronizacja zatrzyma rejestrowanie tego błędu po wykryciu zmiany plik został usunięty. |
| 0x80070003 | -2147942403 | ERROR_PATH_NOT_FOUND | Nie można zsynchronizować pliku lub katalogu, ponieważ element został już usunięty w miejscu docelowym, a synchronizacja nie jest znana ze zmiany. | Żadna akcja nie jest wymagana. Synchronizacja zatrzyma rejestrowanie tego błędu po uruchomieniu wykrywania zmian w miejscu docelowym i wykryje, że element został usunięty. |
| 0x80c80205 | -2134375931 | ECS_E_SYNC_ITEM_SKIP | Plik lub katalog został pominięty, ale zostanie zsynchronizowany podczas następnej sesji synchronizacji. Jeśli ten błąd jest zgłaszany podczas pobierania elementu, nazwa pliku lub katalogu jest bardziej niż prawdopodobne nieprawidłowe. | Nie jest wymagana żadna akcja, jeśli ten błąd jest zgłaszany podczas przesyłania pliku. Jeśli błąd zostanie zgłoszony podczas pobierania pliku, zmień nazwę danego pliku lub katalogu. Aby uzyskać więcej informacji, zobacz [Obsługa nieobsługiwałych znaków.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters) |
| 0x800700B7 | -2147024713 | ERROR_ALREADY_EXISTS | Nie można zsynchronizować pliku lub katalogu, ponieważ element już istnieje w miejscu docelowym, a synchronizacja nie jest znana ze zmiany. | Żadna akcja nie jest wymagana. Synchronizacja zatrzyma rejestrowanie tego błędu po uruchomieniu wykrywania zmian w miejscu docelowym i synchronizacja jest świadoma tego nowego elementu. |
| 0x80c8603e | -2134351810 | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED | Nie można zsynchronizować pliku, ponieważ osiągnięto limit udziału plików platformy Azure. | Aby rozwiązać ten problem, zobacz [Osiągnięto sekcję limit magazynu udziału plików platformy Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#-2134351810) w przewodniku rozwiązywania problemów. |
| 0x80c8027C | -2134375812 | ECS_E_ACCESS_DENIED_EFS | Plik jest szyfrowany przez nieobsługiwanie rozwiązania (np. ntfs EFS). | Odszyfruj plik i użyj obsługiwanego rozwiązania szyfrującego. Listę obsługiwanych rozwiązań można znaleźć w sekcji [Rozwiązania do szyfrowania](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#encryption) w przewodniku planowania. |
| 0x80c80283 | -2160591491 | ECS_E_ACCESS_DENIED_DFSRRO | Plik znajduje się w folderze replikacji tylko do odczytu systemu DFS-R. | Plik znajduje się w folderze replikacji tylko do odczytu systemu DFS-R. Usługa Azure File Sync nie obsługuje punktów końcowych serwera w folderach replikacji tylko do odczytu usługi DFS-R. Więcej informacji można znaleźć [w przewodniku po planowaniu.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#distributed-file-system-dfs) |
| 0x80070005 | -2147024891 | Error_access_denied | Plik ma stan oczekiwania na usunięcie. | Żadna akcja nie jest wymagana. Plik zostanie usunięty po zamknięciu wszystkich otwartych uchwytów plików. |
| 0x80c86044 | -2134351804 | ECS_E_AZURE_AUTHORIZATION_FAILED | Nie można zsynchronizować pliku, ponieważ ustawienia zapory i sieci wirtualnej na koncie magazynu są włączone, a serwer nie ma dostępu do konta magazynu. | Dodaj adres IP serwera lub sieć wirtualną, wykonując kroki opisane w sekcji [Konfigurowanie zapory i ustawień sieci wirtualnej](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings) w przewodniku wdrażania. |
| 0x80c80243 | -2134375869 | ECS_E_SECURITY_DESCRIPTOR_SIZE_TOO_LARGE | Nie można zsynchronizować pliku, ponieważ rozmiar deskryptora zabezpieczeń przekracza limit 64 KiB. | Aby rozwiązać ten problem, usuń wpisy kontroli dostępu (ACE) w pliku w celu zmniejszenia rozmiaru deskryptora zabezpieczeń. |
| 0x8000ffff | -2147418113 | E_unexpected | Nie można zsynchronizować pliku z powodu nieoczekiwanego błędu. | Jeśli błąd będzie się powtarzał przez kilka dni, otwórz sprawę pomocy technicznej. |
| 0x80070020 | -2147024864 | ERROR_SHARING_VIOLATION | Nie można zsynchronizować pliku, ponieważ jest on używany. Plik zostanie zsynchronizowany, gdy nie będzie już używany. | Żadna akcja nie jest wymagana. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | Plik został zmieniony podczas synchronizacji, więc musi zostać ponownie zsynchronizowany. | Żadna akcja nie jest wymagana. |
| 0x80070017 | -2147024873 | ERROR_CRC | Nie można zsynchronizować pliku z powodu błędu CRC. Ten błąd może wystąpić, jeśli plik warstwowy nie został odwołany przed usunięciem punktu końcowego serwera lub jeśli plik jest uszkodzony. | Aby rozwiązać ten problem, zobacz [Pliki warstwowe nie są dostępne na serwerze po usunięciu punktu końcowego serwera,](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) aby usunąć pliki warstwowe, które są oddzielone. Jeśli błąd nadal występuje po usunięciu oprhaned warstwowych plików, uruchom [chkdsk](https://docs.microsoft.com/windows-server/administration/windows-commands/chkdsk) na woluminie. |
| 0x80c80200 | -2134375936 | ECS_E_SYNC_CONFLICT_NAME_EXISTS | Nie można zsynchronizować pliku, ponieważ osiągnięto maksymalną liczbę plików konfliktów. Usługa Azure File Sync obsługuje 100 plików konfliktów na plik. Aby dowiedzieć się więcej o konfliktach plików, zobacz Często [zadawane pytania](https://docs.microsoft.com/azure/storage/files/storage-files-faq#afs-conflict-resolution)dotyczące synchronizacji plików platformy Azure . | Aby rozwiązać ten problem, zmniejsz liczbę plików konfliktów. Plik zostanie zsynchronizowany, gdy liczba plików powodujących konflikt jest mniejsza niż 100. |

#### <a name="handling-unsupported-characters"></a>Obsługa nieobsługiwała znaków
Jeśli skrypt **programu PowerShell FileSyncErrorsReport.ps1** zawiera błędy spowodowane nieobsługiwione znaki (kod błędu 0x8007007b lub 0x80c80255), należy usunąć lub zmienić nazwę znaków z winy z odpowiednich nazw plików. Program PowerShell prawdopodobnie wydrukuje te znaki jako znaki zapytania lub puste prostokąty, ponieważ większość z tych znaków nie ma standardowego kodowania wizualnego. [Narzędzie ewaluacyjne](storage-sync-files-planning.md#evaluation-cmdlet) może służyć do identyfikowania znaków, które nie są obsługiwane.

Poniższa tabela zawiera wszystkie znaki unicode Usługa Azure File Sync nie obsługuje jeszcze.

| Zestaw znaków | Liczba znaków |
|---------------|-----------------|
| <ul><li>0x0000009D (polecenie systemu operacyjnego OSC)</li><li>0x00000090 (ciąg sterujący urządzenia dcs)</li><li>0x0000008F (ss3 pojedyncza zmiana trzy)</li><li>0x00000081 (ustawienie wysokiego oktetu)</li><li>0x0000007F (del delete)</li><li>0x0000008D (posuw linii odwrotnej ri)</li></ul> | 6 |
| 0x0000FDD0 - 0x0000FDEF (formularze prezentacji arabskiej-a) | 32 |
| 0x0000FFF0 - 0x0000FF (promocje) | 16 |
| <ul><li>0x0001FFFE - 0x0001FFFF = 2 (nonszarakter)</li><li>0x0002FFFE - 0x0002FFFF = 2 (nonszarakter)</li><li>0x0003FFFE - 0x0003FFFF = 2 (nonszarakter)</li><li>0x0004FFFE - 0x0004FFFF = 2 (nonszarakter)</li><li>0x0005FFFE - 0x0005FFFF = 2 (nonszarakter)</li><li>0x0006FFFE - 0x0006FFFF = 2 (nonszarakter)</li><li>0x0007FFFE - 0x0007FFFF = 2 (nonszarakter)</li><li>0x0008FFFE - 0x0008FFFF = 2 (nonszarakter)</li><li>0x0009FFFE - 0x0009FFFF = 2 (nonszarakter)</li><li>0x000AFFFE - 0x000AFFFF = 2 (nonszarakter)</li><li>0x000BFFFE - 0x000BFFFF = 2 (nonszarakter)</li><li>0x000CFFFE - 0x000CFFFF = 2 (nonszarakter)</li><li>0x000DFFFE - 0x000DFFFF = 2 (nonszarakter)</li><li>0x000EFFFE - 0x000EFFFF = 2 (niezdefiniowane)</li><li>0x000FFFFE - 0x000FFFFF = 2 (dodatkowy obszar prywatnego użytku)</li></ul> | 30 |
| 0x0010FFFE, 0x0010FF | 2 |

### <a name="common-sync-errors"></a>Typowe błędy synchronizacji
<a id="-2147023673"></a>**Sesja synchronizacji została anulowana.**  

| | |
|-|-|
| **HRESULT** | 0x800704c7 |
| **HRESULT (dziesiętny)** | -2147023673 | 
| **Ciąg błędu** | ERROR_CANCELLED |
| **Wymagane korygowanie** | Nie |

Sesje synchronizacji mogą zakończyć się niepowodzeniem z różnych powodów, w tym serwera jest ponownie uruchomiony lub zaktualizowany, migawki VSS, itp. Mimo że ten błąd wygląda na to, że wymaga obserwacji, można bezpiecznie zignorować ten błąd, chyba że będzie on powtarzał się przez kilka godzin.

<a id="-2147012889"></a>**Nie można ustanowić połączenia z usługą.**    

| | |
|-|-|
| **HRESULT** | 0x80072ee7 |
| **HRESULT (dziesiętny)** | -2147012889 | 
| **Ciąg błędu** | WININET_E_NAME_NOT_RESOLVED |
| **Wymagane korygowanie** | Tak |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134376372"></a>**Żądanie użytkownika zostało ograniczone przez usługę.**  

| | |
|-|-|
| **HRESULT** | 0x80c8004c |
| **HRESULT (dziesiętny)** | -2134376372 |
| **Ciąg błędu** | ECS_E_USER_REQUEST_THROTTLED |
| **Wymagane korygowanie** | Nie |

Nie jest wymagane żadne działanie; serwer spróbuje ponownie. Jeśli ten błąd będzie występował przez kilka godzin, należy utworzyć wniosek o pomoc techniczną.

<a id="-2134364043"></a>**Synchronizacja jest blokowana do momentu zakończenia wykrywania zmian po przywróceniu**  

| | |
|-|-|
| **HRESULT** | 0x80c83075 |
| **HRESULT (dziesiętny)** | -2134364043 |
| **Ciąg błędu** | ECS_E_SYNC_BLOCKED_ON_CHANGE_DETECTION_POST_RESTORE |
| **Wymagane korygowanie** | Nie |

Nie jest wymagana żadna akcja. Po przywróceniu udziału plików lub plików (punkt końcowy w chmurze) przy użyciu usługi Azure Backup synchronizacja jest blokowana do momentu zakończenia wykrywania zmian w udziale plików platformy Azure. Wykrywanie zmian jest uruchamiane natychmiast po zakończeniu przywracania, a czas trwania zależy od liczby plików w udziale plików.

<a id="-2147216747"></a>**Synchronizacja nie powiodła się, ponieważ baza danych synchronizacji została zwolniona.**  

| | |
|-|-|
| **HRESULT** | 0x80041295 |
| **HRESULT (dziesiętny)** | -2147216747 |
| **Ciąg błędu** | SYNC_E_METADATA_INVALID_OPERATION |
| **Wymagane korygowanie** | Nie |

Ten błąd występuje zazwyczaj wtedy, gdy aplikacja kopii zapasowej tworzy migawkę usługi VSS, a baza danych synchronizacji jest zwalniana. Jeśli ten błąd będzie występował przez kilka godzin, należy utworzyć wniosek o pomoc techniczną.

<a id="-2134364065"></a>**Synchronizacja nie może uzyskać dostępu do udziału plików platformy Azure określonego w punkcie końcowym chmury.**  

| | |
|-|-|
| **HRESULT** | 0x80c8305f |
| **HRESULT (dziesiętny)** | -2134364065 |
| **Ciąg błędu** | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED |
| **Wymagane korygowanie** | Tak |

Ten błąd występuje, ponieważ agent usługi Azure File Sync nie może uzyskać dostępu do udziału plików platformy Azure, co może być spowodowane tym, że udział plików platformy Azure lub konto magazynu, na którym udział się znajduje, już nie istnieje. Ten błąd można rozwiązać, wykonując następujące czynności:

1. [Sprawdź, czy konto magazynu istnieje.](#troubleshoot-storage-account)
2. [Upewnij się, że istnieje udział plików platformy Azure.](#troubleshoot-azure-file-share)
3. [Upewnij się, że usługa Azure File Sync ma dostęp do konta magazynu.](#troubleshoot-rbac)
4. [Sprawdź, czy ustawienia zapory i sieci wirtualnej na koncie magazynu są prawidłowo skonfigurowane (jeśli włączono)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134351804"></a>**Synchronizacja nie powiodła się, ponieważ żądanie nie jest autoryzowane do wykonania tej operacji.**  

| | |
|-|-|
| **HRESULT** | 0x80c86044 |
| **HRESULT (dziesiętny)** | -2134351804 |
| **Ciąg błędu** | ECS_E_AZURE_AUTHORIZATION_FAILED |
| **Wymagane korygowanie** | Tak |

Ten błąd występuje, ponieważ agent usługi Azure File Sync nie jest autoryzowany do uzyskiwania dostępu do udziału plików platformy Azure. Ten błąd można rozwiązać, wykonując następujące czynności:

1. [Sprawdź, czy konto magazynu istnieje.](#troubleshoot-storage-account)
2. [Upewnij się, że istnieje udział plików platformy Azure.](#troubleshoot-azure-file-share)
3. [Sprawdź, czy ustawienia zapory i sieci wirtualnej na koncie magazynu są prawidłowo skonfigurowane (jeśli włączono)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)
4. [Upewnij się, że usługa Azure File Sync ma dostęp do konta magazynu.](#troubleshoot-rbac)

<a id="-2134364064"></a><a id="cannot-resolve-storage"></a>**Nie można rozpoznać nazwy używanego konta magazynu.**  

| | |
|-|-|
| **HRESULT** | 0x80C83060 |
| **HRESULT (dziesiętny)** | -2134364064 |
| **Ciąg błędu** | ECS_E_STORAGE_ACCOUNT_NAME_UNRESOLVED |
| **Wymagane korygowanie** | Tak |

1. Sprawdź, czy można rozpoznać nazwę DNS magazynu z serwera.

    ```powershell
    Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 443
    ```
2. [Sprawdź, czy konto magazynu istnieje.](#troubleshoot-storage-account)
3. [Sprawdź, czy ustawienia zapory i sieci wirtualnej na koncie magazynu są prawidłowo skonfigurowane (jeśli włączono)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364022"></a><a id="storage-unknown-error"></a>**Wystąpił nieznany błąd podczas uzyskiwania dostępu do konta magazynu.**  

| | |
|-|-|
| **HRESULT** | 0x80c8308a |
| **HRESULT (dziesiętny)** | -2134364022 |
| **Ciąg błędu** | ECS_E_STORAGE_ACCOUNT_UNKNOWN_ERROR |
| **Wymagane korygowanie** | Tak |

1. [Sprawdź, czy konto magazynu istnieje.](#troubleshoot-storage-account)
2. [Sprawdź, czy ustawienia zapory i sieci wirtualnej na koncie magazynu są prawidłowo skonfigurowane (jeśli włączono)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364014"></a>**Synchronizacja nie powiodła się z powodu zablokowanego konta magazynu.**  

| | |
|-|-|
| **HRESULT** | 0x80c83092 |
| **HRESULT (dziesiętny)** | -2134364014 |
| **Ciąg błędu** | ECS_E_STORAGE_ACCOUNT_LOCKED |
| **Wymagane korygowanie** | Tak |

Ten błąd występuje, ponieważ konto magazynu ma [blokadę zasobu](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources)tylko do odczytu . Aby rozwiązać ten problem, usuń blokadę zasobu tylko do odczytu na koncie magazynu. 

<a id="-1906441138"></a>**Synchronizacja nie powiodła się z powodu problemu z bazą danych synchronizacji.**  

| | |
|-|-|
| **HRESULT** | 0x8e5e044e |
| **HRESULT (dziesiętny)** | -1906441138 |
| **Ciąg błędu** | JET_errWriteConflict |
| **Wymagane korygowanie** | Tak |

Ten błąd występuje, gdy występuje problem z wewnętrzną bazą danych używaną przez usługę Azure File Sync. W przypadku wystąpienia tego problemu utwórz żądanie pomocy technicznej, a my skontaktujemy się z Tobą, aby pomóc Ci rozwiązać ten problem.

<a id="-2134364053"></a>**Wersja agenta usługi Azure File Sync zainstalowana na serwerze nie jest obsługiwana.**  

| | |
|-|-|
| **HRESULT** | 0x80C8306B |
| **HRESULT (dziesiętny)** | -2134364053 |
| **Ciąg błędu** | ECS_E_AGENT_VERSION_BLOCKED |
| **Wymagane korygowanie** | Tak |

Ten błąd występuje, gdy wersja agenta usługi Azure File Sync zainstalowana na serwerze nie jest obsługiwana. Aby rozwiązać ten problem, [uaktualnij]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#upgrade-paths) do [obsługiwanej wersji agenta]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#supported-versions).

<a id="-2134351810"></a>**Osiągnięto limit magazynu udziału plików platformy Azure.**  

| | |
|-|-|
| **HRESULT** | 0x80c8603e |
| **HRESULT (dziesiętny)** | -2134351810 |
| **Ciąg błędu** | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED |
| **Wymagane korygowanie** | Tak |

Ten błąd występuje po przekroczeniu limitu magazynowania udziałów plików platformy Azure, co może wystąpić w przypadku zastosowania limitu przydziału dla udziału plików platformy Azure lub przekroczenia limitów użycia dla udziału plików platformy Azure. Aby uzyskać więcej informacji, zobacz [bieżące limity udziału plików platformy Azure](storage-files-scale-targets.md).

1. Przejdź do grupy synchronizacji w usłudze synchronizacji magazynu.
2. Wybierz punkt końcowy chmury w grupie synchronizacji.
3. Zanotuj nazwę udziału plików platformy Azure w otwartym okienku.
4. Wybierz połączone konto magazynu. Jeśli to łącze nie powiedzie się, konto magazynu, do którego istnieje odwołanie, zostało usunięte.

    ![Zrzut ekranu przedstawiający okienko szczegółów punktu końcowego chmury z łączem do konta magazynu.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

5. Wybierz **pozycję Pliki,** aby wyświetlić listę udziałów plików.
6. Kliknij trzy kropki na końcu wiersza dla udziału plików platformy Azure, do którego odwołuje się punkt końcowy chmury.
7. Sprawdź, czy pozycja **Użycie** znajduje się poniżej pozycji **Limit przydziału**. Uwaga, chyba że określono alternatywny przydział, przydział będzie zgodny [z maksymalnym rozmiarem udziału plików platformy Azure](storage-files-scale-targets.md).

    ![Zrzut ekranu właściwości udziału plików platformy Azure.](media/storage-sync-files-troubleshoot/file-share-limit-reached-1.png)

Jeśli udział jest pełny i nie ustawiono limitu przydziału, jednym z możliwych rozwiązań tego problemu jest przekształcenie każdego podfolderu bieżącego punktu końcowego serwera we własny punkt końcowy serwera w oddzielnych grupach synchronizacji. W ten sposób każdy podfolder będzie synchronizowany z poszczególnymi udziałami plików platformy Azure.

<a id="-2134351824"></a>**Nie można odnaleźć udziału plików platformy Azure.**  

| | |
|-|-|
| **HRESULT** | 0x80c86030 |
| **HRESULT (dziesiętny)** | -2134351824 |
| **Ciąg błędu** | ECS_E_AZURE_FILE_SHARE_NOT_FOUND |
| **Wymagane korygowanie** | Tak |

Ten błąd występuje, gdy udział plików platformy Azure jest niedostępny. Aby rozwiązać problem:

1. [Sprawdź, czy konto magazynu istnieje.](#troubleshoot-storage-account)
2. [Upewnij się, że istnieje udział plików platformy Azure.](#troubleshoot-azure-file-share)

Jeśli udział plików platformy Azure został usunięty, należy utworzyć nowy udział plików, a następnie ponownie utworzyć grupę synchronizacji. 

<a id="-2134364042"></a>**Synchronizacja jest wstrzymana, gdy ta subskrypcja platformy Azure jest zawieszona.**  

| | |
|-|-|
| **HRESULT** | 0x80C83076 |
| **HRESULT (dziesiętny)** | -2134364042 |
| **Ciąg błędu** | ECS_E_SYNC_BLOCKED_ON_SUSPENDED_SUBSCRIPTION |
| **Wymagane korygowanie** | Tak |

Ten błąd występuje, gdy subskrypcja platformy Azure jest zawieszona. Synchronizacja zostanie ponownie włączona po przywróceniu subskrypcji platformy Azure. Zobacz [Dlaczego moja subskrypcja platformy Azure jest wyłączona i jak ją ponownie aktywować?](../../cost-management-billing/manage/subscription-disabled.md)

<a id="-2134364052"></a>**Konto magazynu ma skonfigurowaną zaporę lub sieci wirtualne.**  

| | |
|-|-|
| **HRESULT** | 0x80c8306c |
| **HRESULT (dziesiętny)** | -2134364052 |
| **Ciąg błędu** | ECS_E_MGMT_STORAGEACLSNOTSUPPORTED |
| **Wymagane korygowanie** | Tak |

Ten błąd występuje, gdy udział plików platformy Azure jest niedostępny z powodu zapory konta magazynu lub gdy konto magazynu należy do sieci wirtualnej. Sprawdź, czy ustawienia zapory i sieci wirtualnej na koncie magazynu są poprawnie skonfigurowane. Aby uzyskać więcej informacji, zobacz [Konfigurowanie ustawień zapory i sieci wirtualnej](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings). 

<a id="-2134375911"></a>**Synchronizacja nie powiodła się z powodu problemu z bazą danych synchronizacji.**  

| | |
|-|-|
| **HRESULT** | 0x80c80219 |
| **HRESULT (dziesiętny)** | -2134375911 |
| **Ciąg błędu** | ECS_E_SYNC_METADATA_WRITE_LOCK_TIMEOUT |
| **Wymagane korygowanie** | Nie |

Ten błąd zwykle rozwiązuje się sam i może wystąpić w następujących przypadkach:

* Duża liczba zmian plików na serwerach w grupie synchronizacji.
* Duża liczba błędów w poszczególnych plikach i katalogach.

Jeśli ten błąd będzie się powtarzał dłużej niż kilka godzin, utwórz żądanie pomocy technicznej, a my skontaktujemy się z Tobą, aby pomóc Ci rozwiązać ten problem.

<a id="-2146762487"></a>**Serwer nie może ustanowić bezpiecznego połączenia. Usługa w chmurze otrzymała nieoczekiwany certyfikat.**  

| | |
|-|-|
| **HRESULT** | 0x800b0109 |
| **HRESULT (dziesiętny)** | -2146762487 |
| **Ciąg błędu** | CERT_E_UNTRUSTEDROOT |
| **Wymagane korygowanie** | Tak |

Ten błąd może się zdarzyć, jeśli organizacja używa serwera proxy zakończenia protokołu TLS lub jeśli złośliwa jednostka przechwytuje ruch między serwerem a usługą Azure File Sync. Jeśli masz pewność, że jest to oczekiwane (ponieważ organizacja używa serwera proxy zakończenia protokołu TLS), pomijasz weryfikację certyfikatu z zastąpieniem rejestru.

1. Utwórz wartość rejestru SkipVerifyingPinnedRootCertificate.

    ```powershell
    New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Azure\StorageSync -Name SkipVerifyingPinnedRootCertificate -PropertyType DWORD -Value 1
    ```

2. Uruchom ponownie usługę synchronizacji na zarejestrowanym serwerze.

    ```powershell
    Restart-Service -Name FileSyncSvc -Force
    ```

Ustawiając tę wartość rejestru, agent usługi Azure File Sync zaakceptuje dowolny lokalnie zaufany certyfikat TLS/SSL podczas przesyłania danych między serwerem a usługą w chmurze.

<a id="-2147012894"></a>**Nie można ustanowić połączenia z usługą.**  

| | |
|-|-|
| **HRESULT** | 0x80072ee2 |
| **HRESULT (dziesiętny)** | -2147012894 |
| **Ciąg błędu** | WININET_E_TIMEOUT |
| **Wymagane korygowanie** | Tak |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134375680"></a>**Synchronizacja nie powiodła się z powodu problemu z uwierzytelnianiem.**  

| | |
|-|-|
| **HRESULT** | 0x80c80300 |
| **HRESULT (dziesiętny)** | -2134375680 |
| **Ciąg błędu** | ECS_E_SERVER_CREDENTIAL_NEEDED |
| **Wymagane korygowanie** | Tak |

Ten błąd zazwyczaj występuje, ponieważ czas serwera jest niepoprawny. Jeśli serwer jest uruchomiony na maszynie wirtualnej, sprawdź, czy czas na hoście jest poprawny.

<a id="-2134364040"></a>**Synchronizacja nie powiodła się z powodu wygaśnięcia certyfikatu.**  

| | |
|-|-|
| **HRESULT** | 0x80c83078 |
| **HRESULT (dziesiętny)** | -2134364040 |
| **Ciąg błędu** | ECS_E_AUTH_SRV_CERT_EXPIRED |
| **Wymagane korygowanie** | Tak |

Ten błąd występuje, ponieważ wygasł certyfikat używany do uwierzytelniania.

Aby sprawdzić, czy certyfikat wygasł, wykonaj następujące czynności:  
1. Otwórz przystawkę Mmc certyfikaty, wybierz pozycję Konto komputera i przejdź do pozycji Certyfikaty (komputer lokalny)\Osobiste\Certyfikaty.
2. Sprawdź, czy certyfikat uwierzytelniania klienta wygasł.

Jeśli certyfikat uwierzytelniania klienta wygasł, wykonaj następujące czynności, aby rozwiązać ten problem:

1. Sprawdź, czy agent usługi Azure File Sync jest zainstalowany w wersji 4.0.1.0 lub nowszej.
2. Uruchom na serwerze następujące polecenie programu PowerShell: 

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134375896"></a>**Synchronizacja nie powiodła się z powodu nieodnalenie certyfikatu uwierzytelniania.**  

| | |
|-|-|
| **HRESULT** | 0x80c80228 |
| **HRESULT (dziesiętny)** | -2134375896 |
| **Ciąg błędu** | ECS_E_AUTH_SRV_CERT_NOT_FOUND |
| **Wymagane korygowanie** | Tak |

Ten błąd występuje, ponieważ nie znaleziono certyfikatu używanego do uwierzytelniania.

Aby rozwiązać ten problem, wykonaj następujące kroki:

1. Sprawdź, czy agent usługi Azure File Sync jest zainstalowany w wersji 4.0.1.0 lub nowszej.
2. Uruchom na serwerze następujące polecenie programu PowerShell: 

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134364039"></a>**Synchronizacja nie powiodła się z powodu nieodnalenie tożsamości uwierzytelniania.**  

| | |
|-|-|
| **HRESULT** | 0x80c83079 |
| **HRESULT (dziesiętny)** | -2134364039 |
| **Ciąg błędu** | ECS_E_AUTH_IDENTITY_NOT_FOUND |
| **Wymagane korygowanie** | Tak |

Ten błąd występuje, ponieważ usuwanie punktu końcowego serwera nie powiodło się, a punkt końcowy jest teraz w stanie częściowo usuniętym. Aby rozwiązać ten problem, spróbuj ponownie usunąć punkt końcowy serwera.

<a id="-1906441711"></a><a id="-2134375654"></a><a id="doesnt-have-enough-free-space"></a>**Wolumin, na którym znajduje się punkt końcowy serwera, ma mało miejsca na dysku.**  

| | |
|-|-|
| **HRESULT** | 0x8e5e0211 |
| **HRESULT (dziesiętny)** | -1906441711 |
| **Ciąg błędu** | JET_errLogDiskFull |
| **Wymagane korygowanie** | Tak |
| | |
| **HRESULT** | 0x80c8031a |
| **HRESULT (dziesiętny)** | -2134375654 |
| **Ciąg błędu** | ECS_E_NOT_ENOUGH_LOCAL_STORAGE |
| **Wymagane korygowanie** | Tak |

Ten błąd występuje, ponieważ wolumin jest pełny. Ten błąd często występuje, gdy pliki spoza punktu końcowego serwera zużywają przestrzeń na woluminie. Zwolnić miejsce na woluminie, dodając dodatkowe punkty końcowe serwera, przenosząc pliki na inny wolumin lub zwiększając rozmiar woluminu, na który znajduje się punkt końcowy serwera.

<a id="-2134364145"></a><a id="replica-not-ready"></a>**Usługa nie jest jeszcze gotowa do synchronizacji z tym punktem końcowym serwera.**  

| | |
|-|-|
| **HRESULT** | 0x80c8300f |
| **HRESULT (dziesiętny)** | -2134364145 |
| **Ciąg błędu** | ECS_E_REPLICA_NOT_READY |
| **Wymagane korygowanie** | Nie |

Ten błąd występuje, ponieważ punkt końcowy chmury został utworzony z zawartością już istniejącą w udziale plików platformy Azure. Usługa Azure File Sync musi skanować udział plików platformy Azure w poszukiwaniu całej zawartości przed zezwoleniem punktowi końcowemu serwera na kontynuowanie jego początkowej synchronizacji.

<a id="-2134375877"></a><a id="-2134375908"></a><a id="-2134375853"></a>**Synchronizacja nie powiodła się z powodu problemów z wieloma pojedynczymi plikami.**  

| | |
|-|-|
| **HRESULT** | 0x80c8023b |
| **HRESULT (dziesiętny)** | -2134375877 |
| **Ciąg błędu** | ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED |
| **Wymagane korygowanie** | Tak |
| | |
| **HRESULT** | 0x80c8021c |
| **HRESULT (dziesiętny)** | -2134375908 |
| **Ciąg błędu** | ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED |
| **Wymagane korygowanie** | Tak |
| | |
| **HRESULT** | 0x80c80253 |
| **HRESULT (dziesiętny)** | -2134375853 |
| **Ciąg błędu** | ECS_E_TOO_MANY_PER_ITEM_ERRORS |
| **Wymagane korygowanie** | Tak |

W przypadkach, gdy istnieje wiele błędów synchronizacji plików na, sesje synchronizacji mogą zacząć zakończyć się niepowodzeniem. <!-- To troubleshoot this state, see [Troubleshooting per file/directory sync errors]().-->

> [!NOTE]
> Usługa Azure File Sync tworzy tymczasową migawkę vss raz dziennie na serwerze, aby zsynchronizować pliki, które mają otwarte uchwyty.

<a id="-2134376423"></a>**Synchronizacja nie powiodła się z powodu problemu ze ścieżką punktu końcowego serwera.**  

| | |
|-|-|
| **HRESULT** | 0x80c80019 |
| **HRESULT (dziesiętny)** | -2134376423 |
| **Ciąg błędu** | ECS_E_SYNC_INVALID_PATH |
| **Wymagane korygowanie** | Tak |

Upewnij się, że ścieżka istnieje, znajduje się na lokalnym woluminie NTFS i nie jest punktem ponownejparwy ani istniejącym punktem końcowym serwera.

<a id="-2134375817"></a>**Synchronizacja nie powiodła się, ponieważ wersja sterownika filtru nie jest zgodna z wersją agenta**  

| | |
|-|-|
| **HRESULT** | 0x80C80277 |
| **HRESULT (dziesiętny)** | -2134375817 |
| **Ciąg błędu** | ECS_E_INCOMPATIBLE_FILTER_VERSION |
| **Wymagane korygowanie** | Tak |

Ten błąd występuje, ponieważ załadowana wersja sterownika filtru obsługi warstw w chmurze (StorageSync.sys) jest niezgodna z usługą agenta synchronizacji usługi Storage (FileSyncSvc). Jeśli agent usługi Azure File Sync został uaktualniony, ponownie uruchom serwer, aby zakończyć instalację. Jeśli błąd będzie nadal występował, odinstaluj agenta, uruchom ponownie serwer i ponownie zainstaluj agenta usługi Azure File Sync.

<a id="-2134376373"></a>**Usługa jest obecnie niedostępna.**  

| | |
|-|-|
| **HRESULT** | 0x80c8004b |
| **HRESULT (dziesiętny)** | -2134376373 |
| **Ciąg błędu** | ECS_E_SERVICE_UNAVAILABLE |
| **Wymagane korygowanie** | Nie |

Ten błąd występuje, ponieważ usługa Azure File Sync jest niedostępna. Ten błąd zostanie automatycznie rozwiązany, gdy usługa Azure File Sync stanie się ponownie dostępna.

<a id="-2146233088"></a>**Synchronizacja nie powiodła się z powodu wyjątku.**  

| | |
|-|-|
| **HRESULT** | 0x80131500 |
| **HRESULT (dziesiętny)** | -2146233088 |
| **Ciąg błędu** | Cor_e_exception |
| **Wymagane korygowanie** | Nie |

Ten błąd występuje, ponieważ synchronizacja nie powiodła się z powodu wyjątku. Jeśli błąd będzie się powtarzał przez kilka godzin, utwórz żądanie pomocy technicznej.

<a id="-2134364045"></a>**Synchronizacja nie powiodła się, ponieważ konto magazynu zostało przejęte awaryjnie w innym regionie.**  

| | |
|-|-|
| **HRESULT** | 0x80c83073 |
| **HRESULT (dziesiętny)** | -2134364045 |
| **Ciąg błędu** | ECS_E_STORAGE_ACCOUNT_FAILED_OVER |
| **Wymagane korygowanie** | Tak |

Ten błąd występuje, ponieważ konto magazynu zostało przełączone w tryb failover do innego regionu. Usługa Azure File Sync nie obsługuje funkcji przełączania konta magazynu w tryb failover. Kont magazynu zawierających udziały plików platformy Azure używane jako punkty końcowe w chmurze w usłudze Azure File Sync nie należy przełączać w tryb failover. Wykonanie tej operacji spowoduje, że synchronizacja przestanie działać, a także może spowodować nieoczekiwaną utratę danych w przypadku nowych plików warstwowych. Aby rozwiązać ten problem, przenieś konto magazynu do regionu podstawowego.

<a id="-2134375922"></a>**Synchronizacja nie powiodła się z powodu przejściowego problemu z bazą danych synchronizacji.**  

| | |
|-|-|
| **HRESULT** | 0x80c8020e |
| **HRESULT (dziesiętny)** | -2134375922 |
| **Ciąg błędu** | ECS_E_SYNC_METADATA_WRITE_LEASE_LOST |
| **Wymagane korygowanie** | Nie |

Ten błąd występuje z powodu wewnętrznego problemu z bazą danych synchronizacji. Ten błąd zostanie automatycznie rozwiązany przy ponownych próbach synchronizacji. Jeśli ten błąd będzie trwał przez dłuższy czas, utwórz żądanie pomocy technicznej, a my skontaktujemy się z Tobą, aby pomóc Ci rozwiązać ten problem.

<a id="-2134364024"></a>**Synchronizacja nie powiodła się z powodu zmiany w dzierżawie usługi Azure Active Directory**  

| | |
|-|-|
| **HRESULT** | 0x80c83088 |
| **HRESULT (dziesiętny)** | -2134364024 | 
| **Ciąg błędu** | ECS_E_INVALID_AAD_TENANT |
| **Wymagane korygowanie** | Tak |

Ten błąd występuje, ponieważ obecnie usługa Azure File Sync nie obsługuje przenoszenia subskrypcji do innej dzierżawy usługi Azure Active Directory.
 
Aby rozwiązać ten problem, wykonaj jedną z następujących czynności:

- **Opcja 1 (zalecane)**: Przeniesienie subskrypcji z powrotem do pierwotnej dzierżawy usługi Azure Active Directory
- **Opcja 2**: Usuwanie i ponownetworzenie bieżącej grupy synchronizacji. Jeśli włączono obsługę warstw w chmurze w punkcie końcowym serwera, usuń grupę synchronizacji, a następnie wykonaj czynności opisane w [sekcji obsługi warstw w chmurze]( https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint), aby usunąć oddzielone pliki warstwowe przed ponownym utworzeniem grup synchronizacji. 

<a id="-2134364010"></a>**Synchronizacja nie powiodła się z powodu nieskonfigurowania wyjątku zapory i sieci wirtualnej**  

| | |
|-|-|
| **HRESULT** | 0x80c83096 |
| **HRESULT (dziesiętny)** | -2134364010 | 
| **Ciąg błędu** | ECS_E_MGMT_STORAGEACLSBYPASSNOTSET |
| **Wymagane korygowanie** | Tak |

Ten błąd występuje, jeśli ustawienia zapory i sieci wirtualnej są włączone na koncie magazynu i wyjątek "Zezwalaj zaufanym usługom firmy Microsoft na dostęp do tego konta magazynu" nie jest zaznaczony. Aby rozwiązać ten problem, wykonaj czynności opisane w sekcji [Konfigurowanie ustawień zapory i sieci wirtualnej](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings) w podręczniku wdrażania.

<a id="-2147024891"></a>**Synchronizacja nie powiodła się, ponieważ uprawnienia do folderu Informacje o woluminie systemowym są niepoprawne.**  

| | |
|-|-|
| **HRESULT** | 0x80070005 |
| **HRESULT (dziesiętny)** | -2147024891 |
| **Ciąg błędu** | Error_access_denied |
| **Wymagane korygowanie** | Tak |

Ten błąd może wystąpić, jeśli konto NT AUTHORITY\SYSTEM nie ma uprawnień do folderu System Volume Information na woluminie, na którym znajduje się punkt końcowy serwera. Należy zauważyć, że jeśli poszczególne pliki nie są synchronizowane z ERROR_ACCESS_DENIED, wykonaj kroki opisane w sekcji [Rozwiązywanie problemów z błędami synchronizacji plików/katalogów.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#troubleshooting-per-filedirectory-sync-errors)

Aby rozwiązać ten problem, wykonaj następujące kroki:

1. Pobierz narzędzie [Psexec](https://docs.microsoft.com/sysinternals/downloads/psexec).
2. Uruchom następujące polecenie z wiersza polecenia z podwyższonym poziomem uprawnień, aby uruchomić wiersz polecenia przy użyciu konta systemowego: **PsExec.exe -i -s -d cmd** 
3. Z poziomu wiersza polecenia uruchomionego w ramach konta systemowego uruchom następujące polecenie, aby potwierdzić, że konto NT AUTHORITY\SYSTEM nie ma dostępu do folderu System Volume Information: **cacls "litera dysku:\system volume information" /T /C**
4. Jeśli konto NT AUTHORITY\SYSTEM nie ma dostępu do folderu System Volume Information, uruchom następujące polecenie: **cacls "litera dysku:\system volume information" /T /E /G "NT AUTHORITY\SYSTEM:F"**
    - Jeśli krok 4 zakończy się niepowodzeniem z powodu odmowy dostępu, uruchom następujące polecenie, aby przejąć na własność folder System Volume Information, a następnie powtórz krok 4: **takeown /A /R /F "litera dysku:\System Volume Information"**

<a id="-2134375810"></a>**Synchronizacja nie powiodła się, ponieważ udział plików platformy Azure został usunięty i odtworzony.**  

| | |
|-|-|
| **HRESULT** | 0x80c8027e |
| **HRESULT (dziesiętny)** | -2134375810 |
| **Ciąg błędu** | ECS_E_SYNC_REPLICA_ROOT_CHANGED |
| **Wymagane korygowanie** | Tak |

Ten błąd występuje, ponieważ usługa Azure File Sync nie obsługuje usuwania i ponownego tworzenia udziału plików platformy Azure w tej samej grupie synchronizacji. 

Aby rozwiązać ten problem, usuń i utwórz ponownie grupę synchronizacji, wykonując następujące czynności:

1. Usuń wszystkie punkty końcowe serwera w grupie synchronizacji.
2. Usuń punkt końcowy chmury. 
3. Usuń grupę synchronizacji.
4. Jeśli warstwa w chmurze została włączona w punkcie końcowym serwera, usuń oddzielone pliki warstwowe na serwerze, wykonując kroki opisane w [plikach warstwowych, które nie są dostępne na serwerze po usunięciu sekcji punktu końcowego serwera.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)
5. Ponownie utworzyć grupę synchronizacji.

<a id="-2145844941"></a>**Synchronizacja nie powiodła się, ponieważ żądanie HTTP zostało przekierowane**  

| | |
|-|-|
| **HRESULT** | 0x80190133 |
| **HRESULT (dziesiętny)** | -2145844941 |
| **Ciąg błędu** | HTTP_E_STATUS_REDIRECT_KEEP_VERB |
| **Wymagane korygowanie** | Tak |

Ten błąd występuje, ponieważ usługa Azure File Sync nie obsługuje przekierowania HTTP (kod stanu 3xx). Aby rozwiązać ten problem, należy wyłączyć przekierowanie HTTP na serwerze proxy lub urządzeniu sieciowym.

<a id="-2134364027"></a>**Wystąpił limit czasu podczas przesyłania danych w trybie offline, ale jest nadal w toku.**  

| | |
|-|-|
| **HRESULT** | 0x80c83085 |
| **HRESULT (dziesiętny)** | -2134364027 |
| **Ciąg błędu** | ECS_E_DATA_INGESTION_WAIT_TIMEOUT |
| **Wymagane korygowanie** | Nie |

Ten błąd występuje, gdy operacja pozyskiwania danych przekracza limit czasu. Ten błąd można zignorować, jeśli synchronizacja robi postęp (AppliedItemCount jest większa niż 0). Zobacz [Jak monitorować postęp bieżącej sesji synchronizacji?](#how-do-i-monitor-the-progress-of-a-current-sync-session).

### <a name="common-troubleshooting-steps"></a>Typowe kroki rozwiązywania problemów
<a id="troubleshoot-storage-account"></a>**Sprawdź, czy konto magazynu istnieje.**  
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Przejdź do grupy synchronizacji w usłudze synchronizacji magazynu.
2. Wybierz punkt końcowy chmury w grupie synchronizacji.
3. Zanotuj nazwę udziału plików platformy Azure w otwartym okienku.
4. Wybierz połączone konto magazynu. Jeśli to łącze nie powiedzie się, konto magazynu, do którego istnieje odwołanie, zostało usunięte.
    ![Zrzut ekranu przedstawiający okienko szczegółów punktu końcowego chmury z łączem do konta magazynu.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
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

<a id="troubleshoot-azure-file-share"></a>**Upewnij się, że istnieje udział plików platformy Azure.**  
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Kliknij **pozycję Przegląd** w lewym spisie treści, aby powrócić do głównej strony konta magazynu.
2. Wybierz **pozycję Pliki,** aby wyświetlić listę udziałów plików.
3. Sprawdź, czy udział plików, do którego odwołuje się punkt końcowy chmury, pojawia się na liście udziałów plików (należy to zauważyć w kroku 1 powyżej).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
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

<a id="troubleshoot-rbac"></a>**Upewnij się, że usługa Azure File Sync ma dostęp do konta magazynu.**  
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Kliknij **pozycję Kontrola dostępu (IAM)** w lewym spisie treści.
1. Kliknij kartę **Przypisania ról** na liście użytkowników i aplikacji (*podmiotów usługi),* które mają dostęp do konta magazynu.
1. Sprawdź, czy **program Microsoft.StorageSync** lub **hybrydowa usługa synchronizacji plików** (stara nazwa aplikacji) są wyświetlane na liście z rolą **Czytnik i dostęp do danych.** 

    ![Zrzut ekranu przedstawiający jednostkę usługi hybrydowej usługi synchronizacji plików na karcie kontrola dostępu na koncie magazynu](media/storage-sync-files-troubleshoot/file-share-inaccessible-3.png)

    Jeśli **microsoft.StorageSync** lub **hybrydowa usługa synchronizacji plików** nie jest wyświetlana na liście, wykonaj następujące czynności:

    - Kliknij przycisk **Dodaj**.
    - W polu **Rola** wybierz **pozycję Czytnik i dostęp do danych**.
    - W polu **Wybierz** wpisz **microsoft.StorageSync**, wybierz rolę i kliknij przycisk **Zapisz**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell    
$role = Get-AzRoleAssignment -Scope $storageAccount.Id | Where-Object { $_.DisplayName -eq "Microsoft.StorageSync" }

if ($role -eq $null) {
    throw [System.Exception]::new("The storage account does not have the Azure File Sync " + `
                "service principal authorized to access the data within the " + ` 
                "referenced Azure file share.")
}
```
---

### <a name="how-do-i-prevent-users-from-creating-files-containing-unsupported-characters-on-the-server"></a>Jak uniemożliwić użytkownikom tworzenie plików zawierających nieobsługiwały znaki na serwerze?
Za pomocą [osłony plików Menedżera zasobów serwera plików (FSRM)](https://docs.microsoft.com/windows-server/storage/fsrm/file-screening-management) można blokować tworzenie plików z nieobsługiwanymi znakami w ich nazwach na serwerze. Może być konieczne to za pomocą programu PowerShell, ponieważ większość nieobsługiwanych znaków nie można wydrukować, dlatego należy najpierw oddać ich szesnastkowe reprezentacje jako znaki.

Najpierw utwórz grupę plików FSRM przy użyciu [polecenia cmdlet New-FsrmFileGroup](https://docs.microsoft.com/powershell/module/fileserverresourcemanager/new-fsrmfilegroup). W tym przykładzie zdefiniowano grupę zawierającą tylko dwa nieobsługiwały znaki, ale w grupie plików można dołączyć dowolną liczbę znaków.

```powershell
New-FsrmFileGroup -Name "Unsupported characters" -IncludePattern @(("*"+[char]0x00000090+"*"),("*"+[char]0x0000008F+"*"))
```

Po zdefiniowaniu grupy plików FSRM można utworzyć ekran pliku FSRM przy użyciu polecenia cmdlet New-FsrmFileScreen.

```powershell
New-FsrmFileScreen -Path "E:\AFSdataset" -Description "Filter unsupported characters" -IncludeGroup "Unsupported characters"
```

> [!Important]  
> Należy zauważyć, że osłony plików powinny być używane tylko do blokowania tworzenia znaków nie obsługiwanych przez usługę Azure File Sync. Jeśli osłony plików są używane w innych scenariuszach, synchronizacja będzie stale próbować pobrać pliki z udziału plików platformy Azure na serwer i zostaną zablokowane ze względu na ekran plików, co powoduje wysoki ruch wychodzący danych. 

## <a name="cloud-tiering"></a>Obsługa warstw w chmurze 
Istnieją dwie ścieżki błędów w warstwie chmury:

- Pliki mogą zakończyć się niepowodzeniem, co oznacza, że usługa Azure File Sync bezskutecznie próbuje warstwy pliku do usługi Azure Files.
- Pliki mogą nie zostać odwołane, co oznacza, że filtr systemu plików usługi Azure File Sync (StorageSync.sys) nie może pobrać danych, gdy użytkownik próbuje uzyskać dostęp do pliku, który został warstwowy.

Istnieją dwie główne klasy awarii, które mogą się zdarzyć za pomocą jednej ścieżki awarii:

- Awarie magazynu w chmurze
    - *Problemy z dostępnością usługi magazynu przejściowego*. Aby uzyskać więcej informacji, zobacz [umowę dotyczącą poziomu usług (SLA) dla usługi Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).
    - *Niedostępny udział plików platformy Azure*. Ten błąd zazwyczaj występuje po usunięciu udziału plików platformy Azure, gdy nadal jest punktem końcowym chmury w grupie synchronizacji.
    - *Niedostępne konto magazynu*. Ten błąd zazwyczaj występuje po usunięciu konta magazynu, gdy nadal ma udział plików platformy Azure, który jest punktem końcowym chmury w grupie synchronizacji. 
- Awarie serwera 
  - *Filtr systemu plików usługi Azure File Sync (StorageSync.sys) nie jest ładowany*. Aby odpowiadać na żądania warstw/odwołania, należy załadować filtr systemu plików synchronizacji plików azure. Nieuładowywany filtr może się zdarzyć z kilku powodów, ale najczęstszą przyczyną jest to, że administrator wyładował go ręcznie. Filtr systemu plików synchronizacji plików azure musi być ładowany przez cały czas, aby usługa Azure File Sync działała poprawnie.
  - *Brak, uszkodzony lub w inny sposób uszkodzony punkt ponownej zapaśniczej*. Punkt ponownejpary jest specjalną strukturą danych w pliku, która składa się z dwóch części:
    1. Tag ponownejparacji, który wskazuje systemowi operacyjnemu, że filtr systemu plików synchronizacji plików azure (StorageSync.sys) może być konieczne wykonać pewne działania na we/wy do pliku. 
    2. Ponowne przetwarzanie danych, co wskazuje na filtr systemu plików identyfikator URI pliku w skojarzonym punkcie końcowym chmury (udział plików platformy Azure). 
        
       Najczęstszym sposobem, w jaki punkt ponownejparwy może ulec uszkodzeniu, jest próba zmodyfikowania tagu lub jego danych przez administratora. 
  - *Problemy z łącznością sieciową*. Aby zrównać lub odwołać plik, serwer musi mieć łączność z Internetem.

W poniższych sekcjach wskazano sposób rozwiązywania problemów z warstwami w chmurze i określanie, czy problem jest problemem z magazynem w chmurze, czy z serwerem.

### <a name="how-to-monitor-tiering-activity-on-a-server"></a>Jak monitorować aktywność warstw na serwerze  
Aby monitorować aktywność warstwową na serwerze, użyj identyfikatora zdarzenia 9003, 9016 i 9029 w dzienniku zdarzeń Telemetria (znajdującym się w obszarze Aplikacje i usługi\Microsoft\FileSync\Agent w Podglądzie zdarzeń).

- Identyfikator zdarzenia 9003 zapewnia dystrybucję błędów dla punktu końcowego serwera. Na przykład całkowita liczba błędów, kod błędów itp. Uwaga: jedno zdarzenie jest rejestrowane na kod błędu.
- Identyfikator zdarzenia 9016 zapewnia wyniki ghosting dla woluminu. Na przykład, Procent wolnego miejsca jest, Liczba plików ghosted w sesji, Liczba plików nie ghost, itp.
- Identyfikator zdarzenia 9029 zawiera informacje o sesji ghosting dla punktu końcowego serwera. Na przykład liczba plików, które próbowano w sesji, Liczba plików warstwowych w sesji, Liczba plików już warstwowych, itp.

### <a name="how-to-monitor-recall-activity-on-a-server"></a>Jak monitorować aktywność odwołań na serwerze
Aby monitorować działanie odwołania na serwerze, użyj identyfikatora zdarzenia 9005, 9006, 9009 i 9059 w dzienniku zdarzeń Telemetria (znajdującym się w obszarze Aplikacje i usługi\Microsoft\FileSync\Agent w Podglądzie zdarzeń).

- Identyfikator zdarzenia 9005 zapewnia niezawodność odwołania dla punktu końcowego serwera. Na przykład całkowita liczba unikatowych plików, całkowita liczba unikatowych plików z nieudanym dostępem itp.
- Identyfikator zdarzenia 9006 zapewnia dystrybucję błędów odwołania dla punktu końcowego serwera. Na przykład całkowita liczba nieudanych żądań, kod błędu itp. Uwaga: jedno zdarzenie jest rejestrowane na kod błędu.
- Identyfikator zdarzenia 9009 zawiera informacje o sesji odwołania dla punktu końcowego serwera. Na przykład durationSeconds, CountFilesRecallSucceeded, CountFilesRecallFailed itp.
- Identyfikator zdarzenia 9059 zapewnia dystrybucję odwołania aplikacji dla punktu końcowego serwera. Na przykład ShareId, Nazwa aplikacji i TotalEgressNetworkBytes.

### <a name="how-to-troubleshoot-files-that-fail-to-tier"></a>Jak rozwiązywać problemy z plikami, które nie są w warstwie
Jeśli pliki nie powiodą się z warstwą do usługi Azure Files:

1. W Podglądzie zdarzeń przejrzyj dzienniki zdarzeń danych telemetrycznych, operacyjnych i diagnostycznych w obszarze Aplikacje i usługi\Microsoft\FileSync\Agent. 
   1. Sprawdź, czy pliki istnieją w udziale plików platformy Azure.

      > [!NOTE]
      > Plik musi być zsynchronizowany z udziałem plików platformy Azure, zanim będzie można go zsynchronizować.

   2. Sprawdź, czy serwer ma połączenie z Internetem. 
   3. Sprawdź, czy są uruchomione sterowniki filtrów synchronizacji plików platformy Azure (StorageSync.sys i StorageSyncGuard.sys):
       - W wierszu polecenia z `fltmc`podwyższonym poziomem uprawnień uruchom polecenie . Sprawdź, czy są wyświetlane sterowniki filtrów systemu plików StorageSync.sys i StorageSyncGuard.sys.

> [!NOTE]
> Identyfikator zdarzenia 9003 jest rejestrowany raz na godzinę w dzienniku zdarzeń telemetrii, jeśli plik nie powiedzie się warstwy (jedno zdarzenie jest rejestrowane na kod błędu). Sprawdź [warstwowe błędy i korygowania sekcji,](#tiering-errors-and-remediation) aby sprawdzić, czy kroki korygowania są wymienione dla kodu błędu.

### <a name="tiering-errors-and-remediation"></a>Błędy warstwowe i korygowanie

| HRESULT | HRESULT (dziesiętny) | Ciąg błędu | Problem | Korekty |
|---------|-------------------|--------------|-------|-------------|
| 0x80c86043 | -2134351805 | ECS_E_GHOSTING_FILE_IN_USE | Plik nie może się zlinąć, ponieważ jest używany. | Żadna akcja nie jest wymagana. Plik będzie warstwowy, gdy nie jest już używany. |
| 0x80c80241 | -2134375871 | ECS_E_GHOSTING_EXCLUDED_BY_SYNC | Nie można zniego zniego, ponieważ jest wykluczony przez synchronizację. | Żadna akcja nie jest wymagana. Pliki na liście wykluczeń synchronizacji nie mogą być warstwowe. |
| 0x80c86042 | -2134351806 | ECS_E_GHOSTING_FILE_NOT_FOUND | Nie można zniego warstwy, ponieważ nie został znaleziony na serwerze. | Żadna akcja nie jest wymagana. Jeśli błąd będzie się powtarzał, sprawdź, czy plik istnieje na serwerze. |
| 0x80c83053 | -2134364077 | ECS_E_CREATE_SV_FILE_DELETED | Nie można zniego warstwy, ponieważ został usunięty w udziale plików platformy Azure. | Żadna akcja nie jest wymagana. Plik powinien zostać usunięty na serwerze po uruchomieniu następnej sesji synchronizacji pobierania. |
| 0x80c8600e | -2134351858 | ECS_E_AZURE_SERVER_BUSY | Plik nie uległ warstwie z powodu problemu z siecią. | Żadna akcja nie jest wymagana. Jeśli błąd będzie się powtarzał, sprawdź łączność sieciową z udziałem plików platformy Azure. |
| 0x80072ee7 | -2147012889 | WININET_E_NAME_NOT_RESOLVED | Plik nie uległ warstwie z powodu problemu z siecią. | Żadna akcja nie jest wymagana. Jeśli błąd będzie się powtarzał, sprawdź łączność sieciową z udziałem plików platformy Azure. |
| 0x80070005 | -2147024891 | Error_access_denied | Plik nie może warstwy z powodu błędu odmowy dostępu. Ten błąd może wystąpić, jeśli plik znajduje się w folderze replikacji tylko do odczytu systemu DFS-R. | Usługa Azure File Sync nie obsługuje punktów końcowych serwera w folderach replikacji tylko do odczytu usługi DFS-R. Więcej informacji można znaleźć [w przewodniku po planowaniu.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#distributed-file-system-dfs) |
| 0x80072efe | -2147012866 | WININET_E_CONNECTION_ABORTED | Plik nie uległ warstwie z powodu problemu z siecią. | Żadna akcja nie jest wymagana. Jeśli błąd będzie się powtarzał, sprawdź łączność sieciową z udziałem plików platformy Azure. |
| 0x80c80261 | -2134375839 | ECS_E_GHOSTING_MIN_FILE_SIZE | Nie można poprzek warstwy pliku, ponieważ rozmiar pliku jest mniejszy niż obsługiwany rozmiar. | Jeśli wersja agenta jest mniejsza niż 9.0, minimalny obsługiwany rozmiar pliku wynosi 64 kb. Jeśli wersja agenta jest 9.0 i nowsza, minimalny obsługiwany rozmiar pliku jest oparty na rozmiarze klastra systemu plików (rozmiar klastra systemu plików o podwójnym rozmiarze pliku). Na przykład jeśli rozmiar klastra systemu plików wynosi 4 kb, minimalny rozmiar pliku wynosi 8 kb. |
| 0x80c83007 | -2134364153 | ECS_E_STORAGE_ERROR | Plik nie może się z tym powiększyć z powodu problemu z magazynem platformy Azure. | Jeśli błąd będzie się powtarzał, otwórz żądanie pomocy technicznej. |
| 0x800703e3 | -2147023901 | ERROR_OPERATION_ABORTED | Plik nie został warstwowy, ponieważ został odwołany w tym samym czasie. | Żadna akcja nie jest wymagana. Plik będzie warstwowy po zakończeniu odwołania i plik nie jest już używany. |
| 0x80c80264 | -2134375836 | ECS_E_GHOSTING_FILE_NOT_SYNCED | Nie można zniwą warstwy, ponieważ nie został zsynchronizowany z udziałem plików platformy Azure. | Żadna akcja nie jest wymagana. Plik będzie warstwy po jego zsynchronizowanie z udziałem plików platformy Azure. |
| 0x80070001 | -2147942401 | ERROR_INVALID_FUNCTION | Nie można pozycjować pliku, ponieważ sterownik filtru warstwowego chmury (storagesync.sys) nie jest uruchomiony. | Aby rozwiązać ten problem, otwórz wiersz polecenia z podwyższonym poziomem uprawnień i uruchom następujące polecenie:`fltmc load storagesync`<br>Jeśli sterownik filtru storagesync nie powiedzie się podczas uruchamiania polecenia fltmc, odinstaluj agenta usługi Azure File Sync, uruchom ponownie serwer i zainstaluj ponownie agenta usługi Azure File Sync. |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | Plik nie uległ warstwie z powodu niewystarczającego miejsca na dysku, na którym znajduje się punkt końcowy serwera. | Aby rozwiązać ten problem, zwolnić co najmniej 100 MB miejsca na dysku na woluminie, na którym znajduje się punkt końcowy serwera. |
| 0x80070490 | -2147023728 | ERROR_NOT_FOUND | Nie można zniwą warstwy, ponieważ nie został zsynchronizowany z udziałem plików platformy Azure. | Żadna akcja nie jest wymagana. Plik będzie warstwy po jego zsynchronizowanie z udziałem plików platformy Azure. |
| 0x80c80262 | -2134375838 | ECS_E_GHOSTING_UNSUPPORTED_RP | Plik nie może się ziścić, ponieważ jest to nieobsługicony punkt ponownejparacji. | Jeśli plik jest punktem ponownej ponajdywki deduplikacji danych, wykonaj kroki opisane w [przewodniku planowania,](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#data-deduplication) aby włączyć obsługę deduplikacji danych. Pliki z punktami ponownejparacji innych niż Deduplikacja danych nie są obsługiwane i nie będą warstwowe.  |
| 0x80c83052 | -2134364078 | ECS_E_CREATE_SV_STREAM_ID_MISMATCH | Plik nie może się z warstwą, ponieważ został zmodyfikowany. | Żadna akcja nie jest wymagana. Plik będzie warstwy po zmodyfikowany plik został zsynchronizowany z udziału plików platformy Azure. |
| 0x80c80269 | -2134375831 | ECS_E_GHOSTING_REPLICA_NOT_FOUND | Nie można zniwą warstwy, ponieważ nie został zsynchronizowany z udziałem plików platformy Azure. | Żadna akcja nie jest wymagana. Plik będzie warstwy po jego zsynchronizowanie z udziałem plików platformy Azure. |
| 0x80072ee2 | -2147012894 | WININET_E_TIMEOUT | Plik nie uległ warstwie z powodu problemu z siecią. | Żadna akcja nie jest wymagana. Jeśli błąd będzie się powtarzał, sprawdź łączność sieciową z udziałem plików platformy Azure. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | Plik nie może się z warstwą, ponieważ został zmodyfikowany. | Żadna akcja nie jest wymagana. Plik będzie warstwy po zmodyfikowany plik został zsynchronizowany z udziału plików platformy Azure. |
| 0x800705aa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | Plik nie uległ warstwie z powodu niewystarczających zasobów systemowych. | Jeśli błąd będzie się powtarzał, należy zbadać, który sterownik aplikacji lub trybu jądra wyczerpuje zasoby systemowe. |



### <a name="how-to-troubleshoot-files-that-fail-to-be-recalled"></a>Jak rozwiązywać problemy z plikami, których nie można odwołać  
Jeśli pliki nie zostaną odwołane:
1. W Podglądzie zdarzeń przejrzyj dzienniki zdarzeń danych telemetrycznych, operacyjnych i diagnostycznych w obszarze Aplikacje i usługi\Microsoft\FileSync\Agent.
    1. Sprawdź, czy pliki istnieją w udziale plików platformy Azure.
    2. Sprawdź, czy serwer ma połączenie z Internetem. 
    3. Otwórz przystawkę Programu MMC usług i sprawdź, czy usługa Agent synchronizacji magazynu (FileSyncSvc) jest uruchomiona.
    4. Sprawdź, czy są uruchomione sterowniki filtrów synchronizacji plików platformy Azure (StorageSync.sys i StorageSyncGuard.sys):
        - W wierszu polecenia z `fltmc`podwyższonym poziomem uprawnień uruchom polecenie . Sprawdź, czy są wyświetlane sterowniki filtrów systemu plików StorageSync.sys i StorageSyncGuard.sys.

> [!NOTE]
> Identyfikator zdarzenia 9006 jest rejestrowany raz na godzinę w dzienniku zdarzeń telemetrii, jeśli plik nie może się odwołać (jedno zdarzenie jest rejestrowane na kod błędu). Sprawdź [wycofanie błędów i korygowania sekcji,](#recall-errors-and-remediation) aby sprawdzić, czy kroki korygowania są wymienione dla kodu błędu.

### <a name="recall-errors-and-remediation"></a>Przywoływanie błędów i korygowanie

| HRESULT | HRESULT (dziesiętny) | Ciąg błędu | Problem | Korekty |
|---------|-------------------|--------------|-------|-------------|
| 0x80070079 | -2147942521 | ERROR_SEM_TIMEOUT | Nie można odwołać pliku z powodu limitu czasu we/wy. Ten problem może wystąpić z kilku powodów: ograniczenia zasobów serwera, słaba łączność sieciowa lub problem z magazynem platformy Azure (na przykład ograniczanie przepustowości). | Żadna akcja nie jest wymagana. Jeśli błąd będzie się powtarzać przez kilka godzin, otwórz zgłoszenie do pomocy technicznej. |
| 0x80070036 | -2147024842 | ERROR_NETWORK_BUSY | Nie można odwołać pliku z powodu problemu z siecią.  | Jeśli błąd będzie się powtarzał, sprawdź łączność sieciową z udziałem plików platformy Azure. |
| 0x80c80037 | -2134376393 | ECS_E_SYNC_SHARE_NOT_FOUND | Odwołanie pliku nie powiodło się, ponieważ punkt końcowy serwera został usunięty. | Aby rozwiązać ten problem, zobacz [Pliki warstwowe nie są dostępne na serwerze po usunięciu punktu końcowego serwera](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint). |
| 0x80070005 | -2147024891 | Error_access_denied | Nie można odwołać pliku z powodu błędu odmowy dostępu. Ten problem może wystąpić, jeśli ustawienia zapory i sieci wirtualnej na koncie magazynu są włączone, a serwer nie ma dostępu do konta magazynu. | Aby rozwiązać ten problem, dodaj adres IP serwera lub sieć wirtualną, wykonując kroki opisane w sekcji [Konfigurowanie zapory i ustawień sieci wirtualnej](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings) w przewodniku wdrażania. |
| 0x80c86002 | -2134351870 | ECS_E_AZURE_RESOURCE_NOT_FOUND | Nie można odwołać pliku, ponieważ nie jest dostępny w udziale plików platformy Azure. | Aby rozwiązać ten problem, sprawdź, czy plik istnieje w udziale plików platformy Azure. Jeśli plik istnieje w udziale plików platformy Azure, uaktualnij do najnowszej [wersji agenta](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#supported-versions)usługi Azure File Sync . |
| 0x80c8305f | -2134364065 | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED | Nie można odwołać pliku z powodu niepowodzenia autoryzacji konta magazynu. | Aby rozwiązać ten problem, sprawdź, czy [usługa Azure File Sync ma dostęp do konta magazynu](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#troubleshoot-rbac). |
| 0x80c86030 | -2134351824 | ECS_E_AZURE_FILE_SHARE_NOT_FOUND | Nie można odwołać pliku, ponieważ udział plików platformy Azure jest niedostępny. | Sprawdź, czy udział plików istnieje i jest dostępny. Jeśli udział plików został usunięty i odtworzony, wykonaj kroki opisane w [synchronizacji nie powiodło się, ponieważ udział plików platformy Azure został usunięty i ponownie utworzyć](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#-2134375810) sekcję, aby usunąć i ponownie utworzyć grupę synchronizacji. |
| 0x800705aa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | Nie można go odwołać z powodu niewystarczających zasobów systemowych. | Jeśli błąd będzie się powtarzał, należy zbadać, który sterownik aplikacji lub trybu jądra wyczerpuje zasoby systemowe. |
| 0x8007000e | -2147024882 | ERROR_OUTOFMEMORY | Plik nie został odwołany z powodu nieudolności pamięci. | Jeśli błąd będzie się powtarzał, należy zbadać, który sterownik aplikacji lub trybu jądra powoduje stan małej ilości pamięci. |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | Nie można go odwołać z powodu niewystarczającego miejsca na dysku. | Aby rozwiązać ten problem, zwolnić miejsce na woluminie, przenosząc pliki na inny wolumin, zwiększ rozmiar woluminu lub wymuś pliki do warstwy przy użyciu polecenia cmdlet Invoke-StorageSyncCloudTiering. |

### <a name="tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint"></a>Pliki wielowarstwowe są niedostępne na serwerze po usunięciu punktu końcowego serwera
Pliki warstwowe na serwerze staną się niedostępne, jeśli pliki nie zostaną odwołane przed usunięciem punktu końcowego serwera.

Błędy rejestrowane, jeśli pliki warstwowe nie są dostępne
- Podczas synchronizowania pliku w dzienniku zdarzeń ItemResults jest rejestrowany kod błędu -2147942467 (0x80070043 - ERROR_BAD_NET_NAME)
- Podczas odwoływania pliku, kod błędu -2134376393 (0x80c80037 - ECS_E_SYNC_SHARE_NOT_FOUND) jest zalogowany w dzienniku zdarzeń RecallResults

Przywrócenie dostępu do plików warstwowych jest możliwe w przypadku spełnienia następujących warunków:
- Punkt końcowy serwera został usunięty w ciągu ostatnich 30 dni
- Punkt końcowy w chmurze nie został usunięty 
- Udział plików nie został usunięty
- Grupa synchronizacji nie została usunięta

Jeśli powyższe warunki są spełnione, można przywrócić dostęp do plików na serwerze przez ponowne utworzenie punktu końcowego serwera w tej samej ścieżce na serwerze w ramach tej samej grupy synchronizacji w ciągu 30 dni. 

Jeśli powyższe warunki nie są spełnione, przywrócenie dostępu nie jest możliwe, ponieważ te pliki warstwowe na serwerze są teraz oddzielone. Postępuj zgodnie z poniższymi instrukcjami, aby usunąć osierocone pliki warstwowe.

**Uwagi**
- Gdy pliki warstwowe nie są dostępne na serwerze, pełny plik powinien być nadal dostępny, jeśli masz bezpośredni dostęp do udziału plików platformy Azure.
- Aby zapobiec oddzielonych plików warstwowych w przyszłości, wykonaj kroki opisane w [Usuń punkt końcowy serwera](https://docs.microsoft.com/azure/storage/files/storage-sync-files-server-endpoint#remove-a-server-endpoint) podczas usuwania punktu końcowego serwera.

<a id="get-orphaned"></a>**Jak uzyskać listę osieroconych plików warstwowych** 

1. Sprawdź, czy jest zainstalowana wersja agenta usługi Azure File Sync w wersji 5.1 lub nowszej.
2. Uruchom następujące polecenia programu PowerShell, aby wyświetlić listę osieroconych plików warstwowych:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. Zapisz plik wyjściowy OrphanTieredFiles.txt w przypadku, gdy pliki muszą zostać przywrócone z kopii zapasowej po ich usunięciu.

<a id="remove-orphaned"></a>**Jak usunąć osierocone pliki warstwowe** 

*Opcja 1: Usuwanie osieroconych plików warstwowych*

Ta opcja powoduje usunięcie osieroconych plików warstwowych w systemie Windows Server, ale wymaga usunięcia punktu końcowego serwera, jeśli istnieje z powodu rekreacji po 30 dniach lub jest połączony z inną grupą synchronizacji. Konflikty plików wystąpią, jeśli pliki zostaną zaktualizowane w udziale plików systemu Windows Server lub Azure przed odtworzeniem punktu końcowego serwera.

1. Sprawdź, czy jest zainstalowana wersja agenta usługi Azure File Sync w wersji 5.1 lub nowszej.
2. Tworzenie kopii zapasowych udziału plików platformy Azure i lokalizacji punktu końcowego serwera.
3. Usuń punkt końcowy serwera w grupie synchronizacji (jeśli istnieje), wykonując kroki opisane w [polu Usuń punkt końcowy serwera](https://docs.microsoft.com/azure/storage/files/storage-sync-files-server-endpoint#remove-a-server-endpoint).

> [!Warning]  
> Jeśli punkt końcowy serwera nie zostanie usunięty przed użyciem polecenia cmdlet Remove-StorageSyncOrphanedTieredFiles, usunięcie osieroconego pliku warstwowego na serwerze spowoduje usunięcie pełnego pliku w udziale plików platformy Azure. 

4. Uruchom następujące polecenia programu PowerShell, aby wyświetlić listę osieroconych plików warstwowych:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
5. Zapisz plik wyjściowy OrphanTieredFiles.txt w przypadku, gdy pliki muszą zostać przywrócone z kopii zapasowej po ich usunięciu.
6. Uruchom następujące polecenia programu PowerShell, aby usunąć oddzielone pliki warstwowe:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFilesRemoved = Remove-StorageSyncOrphanedTieredFiles -Path <folder path containing orphaned tiered files> -Verbose
$orphanFilesRemoved.OrphanedTieredFiles > DeletedOrphanFiles.txt
```
**Uwagi** 
- Pliki warstwowe zmodyfikowane na serwerze, które nie są synchronizowane z udziałem plików platformy Azure, zostaną usunięte.
- Pliki warstwowe, które są dostępne (nie sierotą) nie zostaną usunięte.
- Pliki niewarstwowe pozostaną na serwerze.

7. Opcjonalnie: Ponownie utworzyć punkt końcowy serwera, jeśli zostanie usunięty w kroku 3.

*Opcja 2: Zainstaluj udział plików platformy Azure i skopiuj pliki lokalnie, które są oddzielone na serwerze*

Ta opcja nie wymaga usunięcia punktu końcowego serwera, ale wymaga wystarczającej ilości miejsca na dysku, aby skopiować pełne pliki lokalnie.

1. [Zainstaluj](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) udział plików platformy Azure w systemie Windows Server, który jest oddzielony plików warstwowych.
2. Uruchom następujące polecenia programu PowerShell, aby wyświetlić listę osieroconych plików warstwowych:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. Plik wyjściowy OrphanTieredFiles.txt służy do identyfikowania osieroconych plików warstwowych na serwerze.
4. Zastąp oddzielone pliki warstwowe, kopiując pełny plik z udziału plików platformy Azure do systemu Windows Server.

### <a name="how-to-troubleshoot-files-unexpectedly-recalled-on-a-server"></a>Jak rozwiązywać problemy z plikami nieoczekiwanie przywoływani na serwerze  
Programy antywirusowe, tworzenie kopii zapasowych i inne aplikacje, które odczytują dużą liczbę plików, powodują niezamierzone odwołania, chyba że respektują atrybut skip offline i pomijają czytanie zawartości tych plików. Pomijanie plików offline w przypadku produktów, które obsługują tę opcję, umożliwia uniknięcie niezamierzonych odwołań podczas operacji takich jak skanowanie antywirusowe lub zadania kopii zapasowej.

Skonsultuj się z dostawcą oprogramowania, aby dowiedzieć się, w jaki sposób skonfigurować rozwiązanie tak, aby odczytywanie plików offline było pomijane.

Niezamierzone odwołania mogą również wystąpić w innych scenariuszach, na przykład podczas przeglądania plików w Eksploratorze plików. Otwarcie folderu, w którym znajdują się pliki w warstwach chmury, w Eksploratorze plików na serwerze może spowodować niezamierzone odwołania. Prawdopodobieństwo wystąpienia takiej sytuacji jest wyższe, jeśli na serwerze włączono rozwiązanie antywirusowe.

> [!NOTE]
>Użyj identyfikatora zdarzenia 9059 w dzienniku zdarzeń Telemetria, aby określić, które aplikacje są przyczyną odwołań. To zdarzenie zapewnia dystrybucję odwołania aplikacji dla punktu końcowego serwera i jest rejestrowana raz na godzinę.

## <a name="general-troubleshooting"></a>Ogólne rozwiązywanie problemów
Jeśli wystąpią problemy z synchronizacją plików platformy Azure na serwerze, zacznij od wykonania następujących kroków:
1. W Podglądzie zdarzeń przejrzyj dzienniki zdarzeń telemetrycznych, operacyjnych i diagnostycznych.
    - Problemy z synchronizacją, warstwą i przywoływaniem są rejestrowane w dziennikach zdarzeń telemetrycznych, diagnostycznych i operacyjnych w obszarze Aplikacje i usługi\Microsoft\FileSync\Agent.
    - Problemy związane z zarządzaniem serwerem (na przykład ustawienia konfiguracji) są rejestrowane w dziennikach zdarzeń operacyjnych i diagnostycznych w obszarze Aplikacje i usługi\Microsoft\FileSync\Management.
2. Sprawdź, czy usługa Azure File Sync jest uruchomiona na serwerze:
    - Otwórz przystawkę Programu MMC usług i sprawdź, czy usługa Agent synchronizacji magazynu (FileSyncSvc) jest uruchomiona.
3. Sprawdź, czy są uruchomione sterowniki filtrów synchronizacji plików platformy Azure (StorageSync.sys i StorageSyncGuard.sys):
    - W wierszu polecenia z `fltmc`podwyższonym poziomem uprawnień uruchom polecenie . Sprawdź, czy są wyświetlane sterowniki filtrów systemu plików StorageSync.sys i StorageSyncGuard.sys.

Jeśli problem nie został rozwiązany, uruchom narzędzie AFSDiag:
1. Utwórz katalog, w którym zostaną zapisane dane wyjściowe AFSDiag (na przykład C:\Output).
    > [!NOTE]
    >AFSDiag usunie całą zawartość w katalogu wyjściowym przed zbieraniem dzienników. Określ lokalizację wyjściową, która nie zawiera danych.
2. Otwórz okno programu PowerShell z podwyższonym poziomem uprawnień, a następnie uruchom następujące polecenia (naciśnij klawisz Enter po każdym poleceniu):

    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. W przypadku poziomu śledzenia trybu jądra synchronizacji plików azure wprowadź **1** (o ile nie określono inaczej, aby utworzyć więcej pełnych śladów), a następnie naciśnij klawisz Enter.
4. W przypadku poziomu śledzenia trybu użytkownika synchronizacji plików azure wprowadź **1** (o ile nie określono inaczej, aby utworzyć więcej pełnych śladów), a następnie naciśnij klawisz Enter.
5. Odtwórz problem. Po zakończeniu wprowadź **klawisz D**.
6. Plik zip zawierający dzienniki i pliki śledzenia jest zapisywany w określonym katalogu wyjściowym.

## <a name="see-also"></a>Zobacz też
- [Monitorowanie usługi Azure File Sync](storage-sync-files-monitoring.md)
- [Często zadawane pytania dotyczące usług Azure Files](storage-files-faq.md)
- [Rozwiązywanie problemów z usługą Azure Files w systemie Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Rozwiązywanie problemów z plikami platformy Azure w systemie Linux](storage-troubleshoot-linux-file-connection-problems.md)
