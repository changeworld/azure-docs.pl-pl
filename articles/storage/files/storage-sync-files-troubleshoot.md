---
title: Rozwiązywanie problemów z synchronizacji plików Azure (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Rozwiązywania typowych problemów z synchronizacją plików Azure.
services: storage
documentationcenter: ''
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: wgries
ms.openlocfilehash: 8526918630189824e26b95df7f0560c96392e55d
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="troubleshoot-azure-file-sync-preview"></a>Rozwiązywanie problemów z synchronizacji plików Azure (wersja zapoznawcza)
Umożliwia synchronizacji plików Azure (wersja zapoznawcza) scentralizowanie udziałów plików w organizacji w plikach Azure, przy zachowaniu elastyczności, wydajności i zgodności serwera plików lokalnych. Synchronizacja programu Azure pliku przy użyciu systemu Windows Server do szybkiego pamięci podręcznej udziału plików na platformę Azure. Można użyć każdego protokołu, który jest dostępny w systemie Windows Server dostępu do danych lokalnie, w tym protokołu SMB, systemu plików NFS i FTPS. Może mieć dowolną liczbę pamięci podręcznych zgodnie z potrzebami na całym świecie.

W tym artykule zaprojektowano w celu ułatwienia rozwiązywania oraz usuwania problemów, które mogą wystąpić z wdrożeniem synchronizacji plików Azure. Możemy również opisano, jak zebrać dzienniki ważne z systemu, jeśli wymagana jest głębsza analiza problemu. Jeśli nie widzisz odpowiedź na swoje pytanie, użytkownik może skontaktuj się z nami za pośrednictwem następujących kanałów (w kolejności rosnące):

1. W sekcji komentarzy w tym artykule.
2. [Forum usługi Azure Storage](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).
3. [Azure pliki UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files). 
4. pomocą techniczną firmy Microsoft. Aby utworzyć nowe żądanie pomocy technicznej, w portalu Azure na **pomocy** wybierz opcję **Pomoc i obsługa techniczna** przycisk, a następnie wybierz **nowy obsługuje żądania**.

## <a name="storage-sync-service-object-management"></a>Magazyn usługi synchronizacji obiektu zarządzania
Jeśli to zrobisz przeniesienie zasobów z jedną subskrypcję do innej subskrypcji zasobów synchronizacji (usługa synchronizacji magazynu) plików zostanie zablokowane przenoszony. 

## <a name="agent-installation-and-server-registration"></a>Agent instalacji i serwera rejestracji
### <a name="during-server-registration-get-the-error-the-term-find-azurermresource-is-not-recognized-as-the-name"></a>Podczas rejestracji serwera komunikat o błędzie "termin"Znajdź AzureRMResource"nie został rozpoznany jako nazwa,..."
Problem polega na tym, że polecenie cmdlet Znajdź AzureRMResource została zmieniona w usłudze AzureRM IPv6.  Następnej wersji agenta synchronizacji zostanie rozwiązany do obsługi AzureRM IPv6.  Do tego czasu można obejść ten problem przez:
1. Zatrzymaj bieżący ServerRegistration.exe za pomocą polecenia taskmgr
2. Wywołaj okno wiersza polecenia programu PowerShell jako Administrator
3. PS C:\> AzureRM odinstalowania modułu
4. PS C:\> install-module-name AzureRM - RequiredVersion 5.7.0
5. Uruchom C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe.

<a id="agent-installation-failures"></a>**Rozwiązywanie problemów z niepowodzeniem instalacji agenta**  
W przypadku niepowodzenia instalacji agenta synchronizacji plików Azure w wierszu polecenia z podwyższonym poziomem uprawnień uruchom następujące polecenie, aby włączyć rejestrowanie podczas instalacji agenta:

```
StorageSyncAgent.msi /l*v Installer.log
```

Przejrzyj installer.log, aby ustalić przyczynę niepowodzenia instalacji. 

> [!Note]  
> Instalacja agenta zakończy się niepowodzeniem, jeśli komputer jest skonfigurowany do użycia usługi Microsoft Update i usługa Windows Update nie jest uruchomiona.

<a id="agent-installation-on-DC"></a>**Instalacja agenta nie powiedzie się na kontrolerze domeny usługi Active Directory** Jeśli spróbuj i zainstalować agenta synchronizacji na kontrolerze domeny usługi Active Directory, gdzie właściciela roli kontrolera PDC jest w systemie Windows Server 2008 R2 lub poniżej wersji systemu operacyjnego, można napotkać problem gdzie synchronizacji Agent nie zostanie zainstalowana.

Aby rozwiązać, Przenieś rolę podstawowego kontrolera domeny do innej domeny kontrolera uruchomionego systemu Windows Server 2012 R2 lub nowszej, a następnie zainstalować synchronizacji.

<a id="agent-installation-websitename-failure"></a>**Instalacja agenta zakończy się niepowodzeniem z powodu następującego błędu: "Magazynu synchronizacji agenta przedwcześnie zakończył"**  
Ten problem może wystąpić sytuacja wersja 1.x agenta i jeśli nazwa domyślnej witryny sieci Web usług IIS zostanie zmieniona. Aby obejść ten problem, należy użyć naszych 2.0.11+ agenta.

<a id="server-registration-missing"></a>**Serwer nie jest wyszczególniony w obszarze zarejestrowane serwery w portalu Azure**  
Jeśli serwer nie jest wymieniony w obszarze **zarejestrowanych serwerów** dla usługi synchronizacji magazynu:
1. Zaloguj się do serwera, który chcesz zarejestrować.
2. Otwórz Eksploratora plików, a następnie przejdź do katalogu instalacji agenta synchronizacji magazynu (domyślna lokalizacja to C:\Program Files\Azure\StorageSyncAgent). 
3. Uruchom ServerRegistration.exe, a następnie Ukończ pracę kreatora, aby zarejestrować serwer z magazynu usługi synchronizacji.



<a id="server-already-registered"></a>**Rejestracja serwera wyświetli następujący komunikat podczas instalacji agenta synchronizacji plików Azure: "ten serwer jest już zarejestrowany"** 

![Zrzut ekranu okna dialogowego rejestrowania serwera z powodu błędu "serwer jest już zarejestrowany" wiadomości](media/storage-sync-files-troubleshoot/server-registration-1.png)

Ten komunikat zostanie wyświetlony, jeśli serwer został poprzednio zarejestrowany z usługą synchronizacji magazynu. Aby wyrejestrować serwera z bieżącej usługi synchronizacji magazynu, a następnie Zarejestruj nowe usługi synchronizacji magazynu, wykonaj kroki opisane w [Wyrejestruj serwer z funkcją synchronizacji plików Azure](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

Jeśli serwer nie jest wymieniony w obszarze **zarejestrowanych serwerów** przez usługę synchronizacji magazynu na serwerze, który chcesz wyrejestrować, uruchom następujące polecenia programu PowerShell:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> Jeśli serwer jest częścią klastra, można użyć opcjonalnego *StorageSyncServer resetowania - CleanClusterRegistration* parametr, aby usunąć również rejestracji klastra.

<a id="web-site-not-trusted"></a>**Gdy zarejestrować serwer, widzę wiele odpowiedzi "nie jest zaufane witryny sieci web". Dlaczego?**  
Ten problem występuje, gdy **zwiększonych zabezpieczeń programu Internet Explorer** jest włączona podczas rejestrowania serwera. Aby uzyskać więcej informacji na temat niepoprawnie wyłączania **zwiększonych zabezpieczeń programu Internet Explorer** zasad, zobacz [przygotowanie systemu Windows Server do użycia z synchronizacji plików Azure](storage-sync-files-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync) i [wdrażanie plików Azure Synchronizacja (wersja zapoznawcza)](storage-sync-files-deployment-guide.md).

## <a name="sync-group-management"></a>Synchronizacji grupy zarządzania
<a id="cloud-endpoint-using-share"></a>**Utworzenie punktu końcowego w chmurze nie powiedzie się, z powodu następującego błędu: "Określonym Azure udziału plików jest już używany przez inny CloudEndpoint"**  
Ten problem występuje, gdy udział plików na platformę Azure jest już używana przez inny punkt końcowy w chmurze. 

Jeśli ten komunikat zostanie wyświetlony i udziału plików na platformę Azure nie jest obecnie używany przez punkt końcowy w chmurze, wykonaj następujące kroki, aby wyczyścić metadane synchronizacji plików Azure w udziale plików na platformę Azure:

> [!Warning]  
> Usuwanie metadanych w udziale plików na platformę Azure, który jest obecnie używany przez punkt końcowy w chmurze powoduje niepowodzenie operacji synchronizacji plików Azure. 

1. W portalu Azure przejdź do udziału plików na platformę Azure.  
2. Kliknij prawym przyciskiem myszy udział plików na platformę Azure, a następnie wybierz **edycji metadanych**.
3. Kliknij prawym przyciskiem myszy **SyncService**, a następnie wybierz **usunąć**.

<a id="cloud-endpoint-authfailed"></a>**Utworzenie punktu końcowego w chmurze nie powiedzie się, z powodu następującego błędu: "AuthorizationFailed"**  
Ten problem występuje, gdy konto użytkownika nie ma wystarczających praw tworzenia punktu końcowego w chmurze. 

Aby utworzyć punktu końcowego w chmurze, Twoje konto użytkownika musi mieć następujące uprawnienia Authorization firmy Microsoft:  
* Przeczytaj: Pobrać definicji roli
* Zapis: Utwórz lub zaktualizuj niestandardową definicję roli
* Przeczytaj: Pobrać przypisania roli
* Zapis: Utworzyć przypisanie roli

Następujące role wbudowane wymaganych uprawnień Authorization firmy Microsoft:  
* Właściciel
* Administrator dostępu użytkowników, aby określić, czy rola konto użytkownika ma wymagane uprawnienia:  
1. W portalu Azure wybierz **grup zasobów**.
2. Wybierz grupę zasobów, w którym znajduje się na koncie magazynu, a następnie wybierz **(IAM) kontroli dostępu**.
3. Wybierz **roli** (na przykład właścicielem lub współautorem) dla tego konta użytkownika.
4. W **dostawcy zasobów** listy, wybierz **Authorization Microsoft**. 
    * **Przypisanie roli** powinien mieć **odczytu** i **zapisu** uprawnienia.
    * **Definicja roli** powinien mieć **odczytu** i **zapisu** uprawnienia.

<a id="server-endpoint-createjobfailed"></a>**Utworzenie punktu końcowego serwera nie powiedzie się, z powodu następującego błędu: "MgmtServerJobFailed" (kod błędu:-2134375898)**                                                                                                                    
Ten problem występuje, jeśli ścieżka punktu końcowego serwera znajduje się na woluminie systemowym i w chmurze jest włączona obsługa poziomów. Chmura obsługi nie jest obsługiwana w woluminie systemowym. Można utworzyć punktu końcowego serwera w woluminie systemowym, wyłącz chmury, dodając funkcje warstw podczas tworzenia serwera punktu końcowego.

<a id="server-endpoint-deletejobexpired"></a>**Usuwanie punktu końcowego serwera nie powiedzie się, z powodu następującego błędu: "MgmtServerJobExpired"**                
Ten problem występuje, gdy serwer działa w trybie offline lub nie ma łączności sieciowej. Jeśli serwer nie jest już dostępna, należy wyrejestrować serwera w portalu, co spowoduje usunięcie punktów końcowych serwera. Aby usunąć punkty końcowe serwera, wykonaj kroki opisane w [Wyrejestruj serwer z funkcją synchronizacji plików Azure](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

<a id="server-endpoint-provisioningfailed"></a>**Nie można otworzyć stronę właściwości punktu końcowego serwera lub zaktualizować zasadami obsługi poziomów w chmurze**

Ten problem może wystąpić, jeśli operacja zarządzania, na serwerze punktu końcowego nie powiedzie się. Jeśli strona Właściwości punktu końcowego serwera nie jest wyświetlana w portalu Azure, trwa aktualizowanie punktu końcowego serwera za pomocą poleceń programu PowerShell z serwera może rozwiązać ten problem. 

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
# Get the server endpoint id based on the server endpoint DisplayName property
Get-AzureRmStorageSyncServerEndpoint -SubscriptionId mysubguid -ResourceGroupName myrgname -StorageSyncServiceName storagesvcname -SyncGroupName mysyncgroup

# Update the free space percent policy for the server endpoint
Set-AzureRmStorageSyncServerEndpoint -Id serverendpointid -CloudTiering true -VolumeFreeSpacePercent 60
```

## <a name="sync"></a>Sync
<a id="afs-change-detection"></a>**Jeśli utworzono plik bezpośrednio w mojej udziału plików na platformę Azure przy użyciu protokołu SMB lub za pośrednictwem portalu, jak długo trwa pliku do synchronizacji serwerów w grupie synchronizacji?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="broken-sync"></a>**Synchronizacja nie powiedzie się na serwerze**  
Jeśli synchronizacja nie powiedzie się na serwerze:
1. Sprawdź, czy punkt końcowy serwera istnieje w portalu Azure do katalogu, w którym mają być synchronizowane z udziałem plików na platformę Azure:
    
    ![Zrzut ekranu grupy synchronizacji z punktu końcowego w chmurze i punktu końcowego serwera w portalu Azure](media/storage-sync-files-troubleshoot/sync-troubleshoot-1.png)

2. W Podglądzie zdarzeń przejrzyj dzienniki zdarzeń operacyjnych i diagnostycznych, znajduje się w aplikacji i Services\Microsoft\FileSync\Agent.
    1. Sprawdź, czy serwer ma połączenie z Internetem.
    2. Sprawdź, czy usługa synchronizacji Azure pliku jest uruchomiona na serwerze. Aby to zrobić, otwórz przystawkę MMC usług i sprawdź, czy jest uruchomiona usługa agenta synchronizacji magazynu (FileSyncSvc).

<a id="replica-not-ready"></a>**Synchronizacja nie powiedzie się, z powodu następującego błędu: "0x80c8300f — replika nie jest gotowa do wykonania żądanej operacji"**  
Ten problem powinien po utworzeniu punktu końcowego w chmurze i użyj udziału plików na platformę Azure, która zawiera dane. Zadanie wykrywania zmian, które skanuje w poszukiwaniu zmian w udziale plików Azure jest zaplanowane do uruchamiania raz na 24 godziny.  Czas wymagany do ukończenia jest zależna od rozmiaru przestrzeni nazw w udziale plików Azure.  Ten błąd powinien już wyświetlane po wykonaniu tych czynności.


    > [!NOTE]
    > Azure File Sync periodically takes VSS snapshots to sync files that have open handles.

Obecnie nie obsługujemy przenoszenie zasobów do innej subskrypcji lub, przeniesienie do innej usługi Azure AD dzierżawy.  Jeśli subskrypcja zostanie przeniesiony do innej dzierżawy, udział plików na platformę Azure będą niedostępne do naszej usługi, w oparciu o zmianę własności. Jeśli dzierżawca zostanie zmienione, musisz usunąć punkty końcowe serwera i punktu końcowego w chmurze (zobacz synchronizacji grupy zarządzania sekcji instrukcje jak oczyścić udział plików na platformę Azure, można ponownie użyć) i Utwórz ponownie grupę synchronizacji.

<a id="doesnt-have-enough-free-space"></a>**Ten komputer nie ma wystarczającej ilości wolnego miejsca na błąd**  
Jeśli portal Wyświetla stan "Ten komputer nie ma wystarczającej ilości wolnego miejsca" problem może być że mniej niż 1 GB wolnego miejsca jest nadal w woluminie.  Na przykład w przypadku woluminu 1,5 GB synchronizacji tylko będzie można wykorzystywać .5GB, jeśli zostanie osiągnięty ten problem, należy zwiększyć rozmiar woluminu używany przez punkt końcowy serwera.

## <a name="cloud-tiering"></a>Obsługa warstw w chmurze 
Istnieją dwie ścieżki do błędów w chmurze warstwy:

- Pliki mogą mieć problemy z warstwy, co oznacza, że synchronizacja plików Azure próba warstwy pliku do usługi pliki Azure.
- Pliki może zakończyć się niepowodzeniem do wycofania, co oznacza, że filtr synchronizacji plików Azure systemu plików (StorageSync.sys) kończy się niepowodzeniem podczas pobierania danych podczas prób użytkownika, uzyskać dostęp do pliku, które zostały warstwy.

Istnieją dwie klasy głównym błędów, które mogą wystąpić za pośrednictwem ścieżkę awarii:

- Błędów magazynu w chmurze
    - *Magazynu przejściowych problemów dotyczących dostępności usługi*. Zobacz [Umowa dotycząca poziomu usług (SLA) dla usługi Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/v1_2/) Aby uzyskać więcej informacji.
    - *Udział plików na platformę Azure niedostępny*. Ten błąd zazwyczaj odbywa się podczas usuwania udziału plików na platformę Azure, gdy jest to wciąż punktu końcowego w chmurze w grupie synchronizacji.
    - *Konto magazynu niedostępny*. Ten błąd występuje zwykle, gdy usunąć konto magazynu, gdy wciąż znajduje się udział plików na platformę Azure, który jest w grupie synchronizacji punktu końcowego w chmurze. 
- Błędy serwera 
    - *Azure synchronizacji plików filtr systemu plików (StorageSync.sys) nie został załadowany*. Odpowiadanie na żądania obsługi/odwołania, w celu synchronizacji plików Azure filtr systemu plików musi być załadowany. Nie załadowano filtr może się zdarzyć z kilku powodów, ale Najczęstszą przyczyną jest, że administrator zwalnianie go ręcznie. Filtr systemu plików Azure plik synchronizacji musi być załadowany cały czas synchronizacji usługi Azure pliku prawidłowo funkcji.
    - *Brak, uszkodzony lub w inny sposób punktu ponownej analizy przerwane*. Punkt ponownej analizy to struktura danych specjalne w pliku, który składa się z dwóch części:
        1. Tag ponownej analizy, co oznacza system operacyjny, filtr systemu plików Azure plik synchronizacji (StorageSync.sys) może być konieczne wykonaj akcję na we/wy do pliku. 
        2. Ponownej analizy danych, co oznacza filtru systemu plików, identyfikator URI pliku w punkcie końcowym skojarzonej chmury (udział plików na platformę Azure). 
        
        Najczęściej przez punkt ponownej analizy, które mogły ulec uszkodzeniu jest, jeśli administrator próbuje zmodyfikować tagu lub jego dane. 
    - *Problemy z połączeniem sieci*. Aby można było warstwę lub odwołanie pliku, serwer musi mieć połączenie z Internetem.

Poniższe sekcje wskazują sposób rozwiązywania problemów warstw w chmurze i określ, czy problem występuje problem z magazynem chmury lub błąd serwera.

<a id="files-fail-tiering"></a>**Rozwiązywanie problemów z plikami, które nie są do warstwy**  
Jeśli pliki nie warstwy do usługi pliki Azure:

1. Sprawdź, czy pliki znajdują się w udziale plików na platformę Azure.

    > [!NOTE]
    > Plik można zsynchronizować do udziału plików na platformę Azure, przed jego mogą należeć do warstwy.
2. W Podglądzie zdarzeń przejrzyj dzienniki zdarzeń operacyjnych i diagnostycznych, znajduje się w aplikacji i Services\Microsoft\FileSync\Agent.
    1. Sprawdź, czy serwer ma połączenie z Internetem. 
    2. Sprawdź, czy sterowniki filtrów synchronizacji plików Azure (StorageSync.sys i StorageSyncGuard.sys) są uruchomione:
        - W wierszu polecenia z podwyższonym poziomem uprawnień uruchom `fltmc`. Sprawdź, czy wymienione są StorageSync.sys i StorageSyncGuard.sys sterowniki filtrów systemu plików.

<a id="files-fail-recall"></a>**Rozwiązywanie problemów z plikami, które nie można wycofać**  
Jeśli pliki nie można wycofać:
1. W Podglądzie zdarzeń przejrzyj dzienniki zdarzeń operacyjnych i diagnostycznych, znajduje się w aplikacji i Services\Microsoft\FileSync\Agent.
    1. Sprawdź, czy pliki znajdują się w udziale plików na platformę Azure.
    2. Sprawdź, czy serwer ma połączenie z Internetem. 
    3. Sprawdź, czy sterowniki filtrów synchronizacji plików Azure (StorageSync.sys i StorageSyncGuard.sys) są uruchomione:
        - W wierszu polecenia z podwyższonym poziomem uprawnień uruchom `fltmc`. Sprawdź, czy wymienione są StorageSync.sys i StorageSyncGuard.sys sterowniki filtrów systemu plików.

<a id="files-unexpectedly-recalled"></a>**Rozwiązywanie problemów z nieoczekiwanie przypomnieć na serwerze plików**  
Oprogramowanie antywirusowe, kopia zapasowa i inne aplikacje, które zapoznały dużej liczby plików spowodować niezamierzone odwołań, chyba że przestrzegać Pomiń atrybutu w trybie offline i Pomiń odczytywania zawartości tych plików. Pomijanie plików trybu offline na produkty obsługują ta opcja pomaga uniknąć niezamierzone odwołań podczas operacje, takie jak skanowania antywirusowego lub zadania tworzenia kopii zapasowej.

Zapoznaj się z dostawcą oprogramowania, aby dowiedzieć się, jak skonfigurować ich rozwiązanie, aby pominąć odczytywania plików trybu offline.

Niezamierzone odwołania może również wystąpić w innych sytuacjach, takich jak podczas przeglądania plików w Eksploratorze plików. Otwieranie folderu, który ma warstwy chmury plików w Eksploratorze plików na serwerze może spowodować niezamierzone odwołań. Jest to jeszcze bardziej prawdopodobne rozwiązanie antywirusowe jest włączona na serwerze.

## <a name="general-troubleshooting"></a>Rozwiązywanie ogólnych
Jeśli wystąpią problemy z synchronizacją plików Azure na serwerze, należy uruchomić, wykonując następujące czynności:
1. W Podglądzie zdarzeń przejrzyj dzienniki zdarzeń operacyjnych i diagnostycznych.
    - Synchronizacji, dodając funkcje warstw i problemy odwołania są rejestrowane w dziennikach zdarzeń diagnostycznych i operacyjne Dzienniki aplikacji i Services\Microsoft\FileSync\Agent.
    - Problemy związane z zarządzaniem serwera (na przykład ustawienia konfiguracji) są rejestrowane w dziennikach zdarzeń operacyjnych i diagnostyki aplikacji i Services\Microsoft\FileSync\Management.
2. Sprawdź, czy usługa synchronizacji Azure pliku jest uruchomiona na serwerze:
    - Otwórz przystawkę MMC usług i sprawdź, czy jest uruchomiona usługa agenta synchronizacji magazynu (FileSyncSvc).
3. Sprawdź, czy sterowniki filtrów synchronizacji plików Azure (StorageSync.sys i StorageSyncGuard.sys) są uruchomione:
    - W wierszu polecenia z podwyższonym poziomem uprawnień uruchom `fltmc`. Sprawdź, czy wymienione są StorageSync.sys i StorageSyncGuard.sys sterowniki filtrów systemu plików.

Jeśli problem nie zniknie, uruchom narzędzie AFSDiag:
1. Utwórz katalog, w którym zostanie zapisany dane wyjściowe AFSDiag (na przykład C:\Output).
2. Otwórz okno programu PowerShell z podwyższonym poziomem uprawnień, a następnie uruchom następujące polecenia (po każdym poleceniu naciśnij klawisz Enter):

    ```PowerShell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. Poziom śledzenia trybu jądra synchronizacji plików Azure, wprowadź **1** (chyba że określono inaczej, aby utworzyć pełniejsze śladów), a następnie naciśnij klawisz Enter.
4. W przypadku synchronizacji plików Azure tryb śledzenia na poziomie użytkownika, wprowadź **1** (chyba że określono inaczej, aby utworzyć pełniejsze śladów), a następnie naciśnij klawisz Enter.
5. Odtwórz problem. Po zakończeniu, wprowadź **D**.
6. Plik zip, który zawiera dzienników i pliki śledzenia są zapisywane na katalog wyjściowy, który można określić.

## <a name="see-also"></a>Zobacz także
- [Usługa pliki Azure — często zadawane pytania](storage-files-faq.md)
- [Rozwiązywanie problemów plików Azure w systemie Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Rozwiązywanie problemów plików Azure w systemie Linux](storage-troubleshoot-linux-file-connection-problems.md)
