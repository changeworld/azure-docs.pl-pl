---
title: Usługa Azure Backup Server stanu systemu chroni i przywraca komputerów bez systemu operacyjnego
description: Usługi Azure Backup Server umożliwia tworzenie kopii zapasowej stanu systemu, a także zapewnienia ochrony odzyskiwania systemu od zera (BMR).
services: backup
author: rayne-wiselman
manager: carmonm
keywords: ''
ms.service: backup
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: raynew
ms.openlocfilehash: 35ab150670cdc27efcedca233928e0c2184aeca6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62116179"
---
# <a name="back-up-system-state-and-restore-to-bare-metal-with-azure-backup-server"></a>Tworzenie kopii zapasowej stanu systemu i przywracanie na komputerach bez systemu operacyjnego za pomocą usługi Azure Backup Server

Usługa Azure Backup Server tworzy kopię zapasową stanu systemu i zapewnia ochronę odzyskiwania zera (BMR).

*   **Kopia zapasowa stanu systemu**: Tworzy kopie zapasowe plików systemu operacyjnego, dzięki czemu będzie można odzyskać, gdy komputer jest uruchamiany, ale pliki systemowe i rejestr zostaną utracone. Kopię zapasową stanu systemu zawiera:
    * Członek domeny: Pliki rozruchowe, database rejestracji klasy COM +, rejestr
    * Kontroler domeny: Windows Server Active Directory (NTDS), pliki rozruchowe, database rejestracji klasy COM +, rejestr, wolumin systemowy (SYSVOL)
    * Komputer z systemem usług klastrowania: Metadanych serwera klastra
    * Komputer z systemem usług certyfikatów: Dane dotyczące certyfikatu
* **Kopie zapasowe kompletnego**: Tworzy kopię zapasową plików systemu operacyjnego i wszystkich danych na woluminach krytycznych (z wyjątkiem danych użytkownika). Zgodnie z definicją kopii zapasowej BMR obejmuje kopię zapasową stanu systemu. Zapewnia ochronę, gdy komputer nie uruchamia się i trzeba wszystko odzyskać.

W poniższej tabeli podsumowano, co można utworzyć kopię zapasową, a odzyskiwanie. Aby uzyskać szczegółowe informacje o wersjach aplikacji, które mogą być chronione przy użyciu stanu systemu i BMR, zobacz [działanie usługi Azure Backup Server Utwórz kopię zapasową?](backup-mabs-protection-matrix.md).

|Backup|Problem|Odzyskiwane z kopii zapasowej serwera usługi Azure Backup|Odzyskiwanie z kopii zapasowej stanu systemu|BMR|
|----------|---------|---------------------------|------------------------------------|-------|
|**Dane pliku**<br /><br />Regularnego tworzenia kopii zapasowych<br /><br />Kopia zapasowa stanu systemu/BMR|Utracone dane pliku|Tak|Nie|Nie|
|**Dane pliku**<br /><br />Usługa Azure backup Server kopii zapasowej danych plików<br /><br />Kopia zapasowa stanu systemu/BMR|Uszkodzony lub utracony system operacyjny|Nie|Tak|Tak|
|**Dane pliku**<br /><br />Usługa Azure backup Server kopii zapasowej danych plików<br /><br />Kopia zapasowa stanu systemu/BMR|Utracony serwer (nieuszkodzone woluminy danych)|Nie|Nie|Tak|
|**Dane pliku**<br /><br />Usługa Azure backup Server kopii zapasowej danych plików<br /><br />Kopia zapasowa stanu systemu/BMR|Utracony serwer (utracone woluminy danych)|Tak|Nie|Tak (odzyskiwanie BMR, następuje zwykłe odzyskiwanie kopii zapasowej pliku danych)|
|**Dane programu SharePoint**:<br /><br />Usługa Azure backup serwer kopii zapasowych danych<br /><br />Kopia zapasowa stanu systemu/BMR|Utracona witryna, listy, elementy listy, dokumentów|Tak|Nie|Nie|
|**Dane programu SharePoint**:<br /><br />Usługa Azure backup serwer kopii zapasowych danych<br /><br />Kopia zapasowa stanu systemu/BMR|Uszkodzony lub utracony system operacyjny|Nie|Tak|Tak|
|**Dane programu SharePoint**:<br /><br />Usługa Azure backup serwer kopii zapasowych danych<br /><br />Kopia zapasowa stanu systemu/BMR|Odzyskiwanie po awarii|Nie|Nie|Nie|
|Windows Server 2012 R2 Hyper-V<br /><br />Usługa Azure backup Utwórz kopię zapasową serwera hosta funkcji Hyper-V lub gościa<br /><br />Kopii zapasowej stanu systemu/BMR hosta|Utracona maszyna wirtualna|Tak|Nie|Nie|
|Funkcja Hyper-V<br /><br />Usługa Azure backup Utwórz kopię zapasową serwera hosta funkcji Hyper-V lub gościa<br /><br />Kopii zapasowej stanu systemu/BMR hosta|Uszkodzony lub utracony system operacyjny|Nie|Tak|Tak|
|Funkcja Hyper-V<br /><br />Usługa Azure backup Utwórz kopię zapasową serwera hosta funkcji Hyper-V lub gościa<br /><br />Kopii zapasowej stanu systemu/BMR hosta|Utracony host funkcji Hyper-V (nieuszkodzone maszyny wirtualne)|Nie|Nie|Tak|
|Funkcja Hyper-V<br /><br />Usługa Azure backup Utwórz kopię zapasową serwera hosta funkcji Hyper-V lub gościa<br /><br />Kopii zapasowej stanu systemu/BMR hosta|Utracony host funkcji Hyper-V (utracone maszyny wirtualne)|Nie|Nie|Tak<br /><br />Odzyskiwania systemu od ZERA, następuje zwykłe odzyskiwanie usługi Azure Backup Server|
|SQL Server/Exchange<br /><br />Kopia zapasowa aplikacji Azure Backup Server<br /><br />Kopia zapasowa stanu systemu/BMR|Utracone dane aplikacji|Tak|Nie|Nie|
|SQL Server/Exchange<br /><br />Kopia zapasowa aplikacji Azure Backup Server<br /><br />Kopia zapasowa stanu systemu/BMR|Uszkodzony lub utracony system operacyjny|Nie|Y|Tak|
|SQL Server/Exchange<br /><br />Kopia zapasowa aplikacji Azure Backup Server<br /><br />Kopia zapasowa stanu systemu/BMR|Utracony serwer (nieuszkodzone dzienniki transakcji i baza danych)|Nie|Nie|Tak|
|SQL Server/Exchange<br /><br />Kopia zapasowa aplikacji Azure Backup Server<br /><br />Kopia zapasowa stanu systemu/BMR|Utracony serwer (utracone dzienniki transakcji i baz danych)|Nie|Nie|Tak<br /><br />Odzyskiwanie BMR, następuje zwykłe odzyskiwanie usługi Azure Backup Server|

## <a name="how-system-state-backup-works"></a>Jak działa kopii zapasowej stanu systemu

Gdy kopii zapasowej stanu systemu działa, Utwórz kopię zapasową serwera, który komunikuje się z usługą kopia zapasowa systemu Windows Server na żądanie kopii zapasowej stanu systemu serwera. Utwórz kopię zapasową serwera i kopia zapasowa systemu Windows Server domyślnie używają dysk z największą ilością wolnego miejsca. Informacje dotyczące tego dysku są zapisywane w pliku PSDataSourceConfig.xml. Jest to dysk, którego kopia zapasowa systemu Windows Server używa kopii zapasowych.

Można wybrać dysk który serwer kopii zapasowych używa kopii zapasowej stanu systemu. Na chronionym serwerze przejdź do C:\Program Files\Microsoft danych ochrony Manager\MABS\Datasources. Otwórz plik PSDataSourceConfig.xml do edycji. Zmiana \<FilesToProtect\> wartość na literę dysku. Zapisz i zamknij plik. W przypadku zestawu grup ochrony w celu ochrony stanu systemu komputera, uruchom sprawdzanie spójności. Jeśli generowany jest alert, wybierz opcję **Modyfikuj grupę ochrony** w alercie, a następnie ukończ jego pracę. Następnie uruchom kolejną kontrolę spójności.

Należy pamiętać, że jeśli serwer ochrony znajduje się w klastrze, jest to możliwe, że dysk klastra zostanie wybrany jako dysk z największą ilością wolnego miejsca. Jeśli ma zostać własności dysku do innego węzła i uruchomienia tworzenia kopii zapasowej stanu systemu, dostępnego dysku i kopii zapasowej nie powiedzie się. W tym scenariuszu należy zmodyfikować plik psdatasourceconfig.XML tak, aby wskazywał dysk lokalny.

Następnie kopia zapasowa systemu Windows Server tworzy folder o nazwie WindowsImageBackup w folderze głównym folder przywracania. Ponieważ kopia zapasowa systemu Windows Server tworzy kopię zapasową, wszystkie dane znajduje się w tym folderze. Po zakończeniu tworzenia kopii zapasowej plik jest przekazywany do serwera kopii zapasowych. Zanotuj następujące informacje:

* Ten folder i jego zawartość nie wyczyszczono po zakończeniu tworzenia kopii zapasowej lub przeniesienie. Najlepszym sposobem na myśl o tym to, że miejsce pozostaje zarezerwowane dla następnej kopii zapasowej zostało zakończone.
* Folder jest tworzony za każdym razem, gdy wykonano kopię zapasową. Datą i godziną wskazuje godzinę wykonania ostatniej kopii zapasowej stanu systemu.

## <a name="bmr-backup"></a>Kopii zapasowej BMR

W scenariuszach BMR (w tym kopii zapasowej stanu systemu) zadanie tworzenia kopii zapasowej jest zapisywany bezpośrednio do udziału na komputerze serwera kopii zapasowych. Nie są zapisywane w folderze w na chronionym serwerze.

Utwórz kopię zapasową serwera wywołuje kopia zapasowa systemu Windows Server i udostępnia wolumin repliki dla kopii zapasowej BMR. W takim przypadku program nie nakazuje zapasowa systemu Windows Server w celu skorzystania z dysku z największą ilością wolnego miejsca. Zamiast tego używa udziału, który został utworzony dla zadania.

Po zakończeniu tworzenia kopii zapasowej plik jest przekazywany do serwera kopii zapasowych. Dzienniki są przechowywane w C:\Windows\Logs\WindowsServerBackup.

## <a name="prerequisites-and-limitations"></a>Wymagania wstępne i ograniczenia

-   Odzyskiwanie BMR nie jest obsługiwana dla komputerów z systemem Windows Server 2003 lub komputerach z systemem kliencki system operacyjny.

-   Nie można chronić funkcji BMR i stanu systemu na tym samym komputerze w różnych grupach ochrony.

-   Komputerze utwórz kopię zapasową serwera nie może chronić siebie samego przy pomocy metody BMR.

-   Krótkoterminowa ochrona na taśmie (dysk taśma lub D2T) nie jest obsługiwana w scenariuszach BMR. Przechowywanie długoterminowe na taśmie (dysk do dysk taśma lub D2D2T) jest obsługiwana.

-   W przypadku ochrony BMR kopia zapasowa systemu Windows Server należy zainstalować na komputerze chronionym.

-   W przypadku ochrony BMR w przeciwieństwie do ochrony stanu systemu, Utwórz kopię zapasową serwera nie ma żadnych wymagań dotyczących miejsca na komputerze chronionym. Kopia zapasowa systemu Windows Server bezpośrednio przesyła kopie zapasowe z komputerem serwera kopii zapasowych. Zadanie tworzenia kopii zapasowej transferu nie jest wyświetlane w kopii zapasowej serwera **zadań** widoku.

-   Utwórz kopię zapasową serwera rezerwuje 30 GB miejsca na woluminie repliki na potrzeby odzyskiwania systemu od ZERA. Tę wartość można zmienić na **przydział dysku** stronie kreatora modyfikacji grupy ochrony lub za pomocą poleceń cmdlet Get-DatasourceDiskAllocation i Set-datasourcediskallocation w programie PowerShell. Na woluminie punktu odzyskiwania ochrona BMR wymaga około 6 GB do przechowywania danych przez pięć dni.
    * Należy pamiętać, że nie można zmniejszyć rozmiaru woluminu repliki do wartości mniejszej niż 15 GB.
    * Utwórz kopię zapasową serwera nie oblicza rozmiaru źródła danych BMR. Zakłada się 30 GB dla wszystkich serwerów. Zmień wartość na podstawie rozmiaru kopii zapasowych BMR, których można oczekiwać w danym środowisku. Rozmiar kopii zapasowej BMR może być mniej więcej obliczona jako suma miejsca używanego przez wszystkie woluminy krytyczne. Woluminy krytyczne = wolumin rozruchowy + wolumin systemowy + wolumin obsługujący dane o stanie systemu, takie jak Active Directory.

-   W przypadku zmiany z ochrony stanu systemu na ochronę BMR ochrona BMR wymaga mniej miejsca na *woluminu punktu odzyskiwania*. Dodatkowe miejsce na woluminie nie jest jednak odzyskane. Rozmiar woluminu można zmniejszyć ręcznie na **modyfikacja alokacji dysku** strony kreatora modyfikacji grupy ochrony lub za pomocą polecenia cmdlet Get-DatasourceDiskAllocation i Set-datasourcediskallocation w programie PowerShell.

    W przypadku zmiany z ochrony stanu systemu na ochronę BMR ochrona BMR wymaga więcej miejsca na *woluminu repliki*. Wolumin zostanie automatycznie rozszerzony. Jeśli chcesz zmienić domyślne alokacje miejsca, użyj polecenia cmdlet programu PowerShell Modify-DiskAllocation.

-   W przypadku zmiany z ochrony BMR ochrona stanu systemu, potrzebujesz więcej miejsca na woluminie punktu odzyskiwania. Utwórz kopię zapasową serwera może próbować automatycznie zwiększyć wolumin. W przypadku niewystarczającej ilości miejsca w puli magazynów, wystąpi błąd.

    W przypadku zmiany z ochrony BMR ochrona stanu systemu potrzebne miejsce na komputerze chronionym. Jest tak, ponieważ Ochrona stanu systemu najpierw zapisuje replikę na komputerze lokalnym, a następnie przekazuje ją do serwera kopii zapasowych.

## <a name="before-you-begin"></a>Przed rozpoczęciem

1.  **Wdrażanie usługi Azure Backup Server**. Sprawdź, że Utwórz kopię zapasową serwera został poprawnie wdrożony. Aby uzyskać więcej informacji, zobacz:
    * [Wymagania systemowe dla usługi Azure Backup Server](https://docs.microsoft.com/system-center/dpm/install-dpm#setup-prerequisites)
    * [Macierz ochrony usługi tworzenia kopii zapasowej serwera](backup-mabs-protection-matrix.md)

2.  **Konfigurowanie magazynu**. Dane kopii zapasowej można przechowywać na dysku, na taśmie i w chmurze dzięki platformie Azure. Aby uzyskać więcej informacji, zobacz [przygotowanie magazynu danych](https://docs.microsoft.com/system-center/dpm/plan-long-and-short-term-data-storage).

3.  **Skonfiguruj agenta ochrony**. Zainstaluj agenta ochrony na komputerze, który chcesz utworzyć kopię zapasową. Aby uzyskać więcej informacji, zobacz [wdrażanie agenta ochrony DPM](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent).

## <a name="back-up-system-state-and-bare-metal"></a>Tworzenie kopii zapasowej stanu systemu i kompletnego stanu systemu
Skonfiguruj grupę ochrony, zgodnie z opisem w [wdrażanie grup ochrony](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups). Należy pamiętać, że nie można chronić funkcji BMR i stanu systemu na tym samym komputerze w różnych grupach. Po wybraniu funkcji BMR stan systemu jest automatycznie włączany.


1.  Aby otworzyć Kreatora tworzenia nowej grupy ochrony w konsoli administratora serwera kopii zapasowych, wybierz **ochrony** > **akcje** > **tworzenia grupy ochrony** .

2.  Na **wybierz typ grupy ochrony** wybierz **serwerów**, a następnie wybierz pozycję **dalej**.

3.  Na **Wybierz członków grupy** strony, rozwiń węzeł komputera, a następnie wybierz opcję **odzyskiwania systemu od ZERA** lub **stanu systemu**.

    Należy pamiętać, że nie można chronić zarówno BMR i stanu systemu na tym samym komputerze w różnych grupach. Po wybraniu funkcji BMR stan systemu jest automatycznie włączany. Aby uzyskać więcej informacji, zobacz [wdrażanie grup ochrony](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups).

4.  Na **wybierz metodę ochrony danych** wybierz, jak chcesz obsługiwać krótkoterminowe i długoterminowe kopie zapasowe. Krótkoterminowa kopia zapasowa jest zawsze najpierw dysku, z opcją przeniesienia kopii zapasowej z dysku na platformie Azure w chmurze za pomocą usługi Kopia zapasowa Azure (krótkoterminowego lub długoterminowego). Zamiast długoterminowej kopii zapasowej w chmurze jest aby skonfigurować długoterminowe kopie zapasowe do autonomicznego urządzenia lub taśmy biblioteki taśm podłączonej do serwera kopii zapasowych.

5.  Na **Wybierz cele krótkoterminowe** wybierz sposób tworzenia kopii do krótkoterminowego przechowywania na dysku:
    1. Aby uzyskać **zakres przechowywania**, wybierz, jak długo chcesz przechowywać dane na dysku. 
    2. Aby uzyskać **częstotliwość synchronizacji**, wybierz, jak często chcesz zapisywać przyrostowe kopie zapasowe na dysku. Jeśli nie chcesz ustawić interwał wykonywania kopii zapasowej, można sprawdzić **zaraz przed punktem odzyskiwania** opcji. Utwórz kopię zapasową serwera uruchomią ekspresowej pełnej kopii zapasowej bezpośrednio przed zaplanowanym każdego punktu odzyskiwania.

6.  Jeśli chcesz przechowywać dane na taśmę do długoterminowego przechowywania na **Określ cele długoterminowe** wybierz, jak długo chcesz przechowywać dane na taśmie (1 – 99 lat). 
    1. Aby uzyskać **częstotliwość wykonywania kopii zapasowych**, wybierz częstotliwość tworzenia kopii zapasowej na taśmie powinno być ono uruchomione. Częstotliwość zależy od zakresu przechowywania, wybrane:
        * Gdy zakres przechowywania wynosi 1 – 99 lat, możesz wybrać tworzenie kopii zapasowej codziennie, co tydzień, co dwa tygodnie, co miesiąc, co kwartał, co pół roku lub co rok.
        * Gdy zakres przechowywania wynosi 1 – 11 miesięcy, możesz wybrać tworzenie kopii zapasowej codziennie, co tydzień, co dwa tygodnie lub co miesiąc.
        * Gdy zakres przechowywania wynosi 1 – 4 tygodnie, można wybrać tworzenie kopii zapasowej codziennie lub co tydzień.

    2. Na **szczegółów wybierz taśmy i biblioteki** stronie, wybierz taśmę i bibliotekę do użycia i czy kompresowane i szyfrowane dane.

7.  Na **Przejrzyj przydział dysku** Przejrzyj miejsce w puli magazynu przydzielone dla grupy ochrony.

    1. **Całkowity rozmiar danych** jest rozmiar danych, aby utworzyć kopię zapasową.
    2. **Miejsce na dysku do udostępnienia w usłudze Azure Backup Server** miejsca, Utwórz kopię zapasową serwera zaleca się dla grupy ochrony. Utwórz kopię zapasową serwera wybierze idealną wielkość kopii zapasowej na podstawie ustawień. Jednak możesz edytować opcje wielkości kopii zapasowej w **Szczegóły przydziału dysku**. 
    3. W przypadku obciążeń, w menu rozwijanym wybierz preferowany magazyn. Edycja zmienia wartości **całkowita ilość miejsca** i **ilość wolnego miejsca** w **dostępny Magazyn dyskowy** okienka. Miejsce to ilość miejsca w magazynie, który serwer zapasowy sugeruje dodać do woluminu w celu zapewnienia bezproblemowego tworzenia kopii zapasowych.

8.  Na **wybierz metodę tworzenia repliki** wybierz, jak chcesz obsługiwać początkową pełną replikację danych. Jeśli chcesz replikować za pośrednictwem sieci zaleca się wybranie poza godzinami szczytu. Dla dużych ilości danych lub warunki sieciowe, które są optymalne warto rozważyć replikowanie danych w trybie offline przy użyciu nośnika wymiennego.

9. Na **wybierz opcje sprawdzania spójności** wybierz sposób automatyzacji sprawdzania spójności. Możesz uruchomić sprawdzanie tylko wtedy, gdy replika danych staje się niespójna, lub zgodnie z harmonogramem. Jeśli nie chcesz konfigurować automatycznego sprawdzania spójności, można uruchomić sprawdzanie ręczne, w dowolnym momencie. Aby uruchomić sprawdzanie ręczne, w **ochrony** obszarów konsoli administratora serwera kopii zapasowej, kliknij prawym przyciskiem myszy grupę ochrony, a następnie wybierz **Przeprowadź Sprawdzanie spójności**.

10. Jeśli wybrano, aby utworzyć kopię zapasową w chmurze przy użyciu usługi Azure Backup na **Określ dane ochrony Online** strony, upewnij się, że wybrano obciążenia, aby utworzyć kopię zapasową na platformie Azure.

11. Na **Określ harmonogram tworzenia kopii zapasowych Online** strony, wybierz częstotliwość przyrostowe kopie zapasowe na platformie Azure zostanie przeprowadzona. Można zaplanować tworzenie kopii zapasowych do uruchamiania każdego dnia, tygodnia, miesiąc i rok i wybierz godziny i daty, w których powinno być ono uruchomione. Kopie zapasowe mogą być tworzone maksymalnie dwa razy dziennie. Każdym uruchomieniu kopii zapasowej, punkt odzyskiwania danych jest tworzony na platformie Azure z kopii kopii zapasowej danych przechowywanych na dysku kopii zapasowej serwera.

12. Na **Określanie zasad przechowywania Online** wybierz, jak punkty odzyskiwania, które są tworzone na podstawie dzienne, tygodniowe, miesięczne i roczne kopie zapasowe są przechowywane na platformie Azure.

13. Na **wybierz replikację Online** wybierz, jak występuje początkowa Pełna replikacja danych. Można replikować za pośrednictwem sieci lub wykonaj w trybie offline (rozmieszczanie offline) kopii zapasowej. Kopii zapasowych offline używana jest funkcja Azure Import. Aby uzyskać więcej informacji, zobacz [Offline kopii zapasowej przepływu pracy w usłudze Azure Backup](backup-azure-backup-import-export.md).

14. Na **Podsumowanie** Przejrzyj ustawienia. Po wybraniu **Utwórz grupę**, następuje Replikacja początkowa danych. Podczas replikacji danych zakończeniu na **stan** strona, stan grupy ochrony jest **OK**. Kopia zapasowa następnie odbywa się na ochronę grupy.

## <a name="recover-system-state-or-bmr"></a>Odzyskiwanie stanu systemu lub odzyskiwania systemu od ZERA
Można odzyskać BMR lub stanu systemu do lokalizacji sieciowej. Jeśli utworzono kopię zapasową BMR, użyj środowiska odzyskiwania Windows (WinRE) uruchom system i połączyć ją z siecią. Następnie należy użyć narzędzia Kopia zapasowa systemu Windows Server do odzyskania z lokalizacji sieciowej. Jeśli utworzono kopię zapasową stanu systemu, wystarczy użyć narzędzia Kopia zapasowa systemu Windows Server do odzyskania z lokalizacji sieciowej.

### <a name="restore-bmr"></a>Przywróć odzyskiwania systemu od ZERA
Uruchamianie odzyskiwania na komputerze serwera kopii zapasowych:

1.  W **odzyskiwania** okienka i Znajdź komputera, którą chcesz odzyskać, a następnie wybierz **odzyskiwanie systemu od zera**.

2.  Dostępne punkty odzyskiwania są pogrubione w kalendarzu. Wybierz datę i godzinę dla punktu odzyskiwania, którego chcesz używać.

3.  Na **Wybieranie typu odzyskiwania** wybierz **Kopiuj do folderu sieciowego.**

4.  Na **określ miejsce docelowe** wybierz, które chcesz skopiować dane. Należy pamiętać, że wybranego miejsca docelowego musi mieć wystarczająco dużo miejsca. Firma Microsoft zaleca, aby utworzyć nowy folder.

5.  Na **Określ opcje odzyskiwania** wybierz ustawienia zabezpieczeń, które mają być stosowane. Następnie wybierz, czy chcesz użyć sieci magazynowania (SAN) — na podstawie migawek sprzętowych, szybciej odzyskiwać. (Jest to opcja tylko wtedy, gdy masz za pośrednictwem sieci SAN przy użyciu tej funkcji, które są dostępne oraz możliwość tworzenia i dzielenia klonu w celu umożliwienia zapisu. Ponadto, chronionego komputera i komputera Utwórz kopię zapasową serwera muszą być podłączone do tej samej sieci.)

6.  Ustaw opcje powiadamiania. Na **potwierdzenie** wybierz opcję **odzyskać**.

Konfigurowanie lokalizacji udziału:

1.  W lokalizacji przywracania przejdź do folderu zawierającego kopię zapasową.

2.  Udostępnij ten folder, który jest jeden poziom wyżej WindowsImageBackup, tak aby w katalogu głównym folderu udostępnionego był WindowsImageBackup folder. Jeśli nie możesz tego zrobić, proces przywracania nie odnajdzie kopii zapasowej. Aby połączyć przy użyciu środowiska odzyskiwania Windows (WinRE), należy udziału, w której będziesz mieć dostęp ze środowiska WinRE z poprawnym adresem IP i poświadczeniami.

Przywracanie systemu:

1.  Uruchom komputer, na którym chcesz przywrócić obraz przy użyciu Windows dysku DVD odzyskiwanego systemu.

2.  Na pierwszej stronie sprawdź ustawienia języka i ustawienia regionalne. Na **zainstalować** wybierz opcję **Napraw komputer**.

3.  Na **opcje odzyskiwania systemu** wybierz opcję **Przywróć komputer przy użyciu utworzonego wcześniej obrazu systemu**.

4.  Na **Wybierz kopię zapasową obrazu systemu** wybierz **wybierz obraz systemu** > **zaawansowane** > **Wyszukaj obraz systemu w sieci**. Jeśli zostanie wyświetlone ostrzeżenie, wybierz opcję **tak**. Przejdź do ścieżki udziału, wprowadź poświadczenia, a następnie wybierz punkt odzyskiwania. To skanuje w poszukiwaniu określonych kopii zapasowych, które są dostępne w tym punkcie odzyskiwania. Wybierz punkt odzyskiwania, którego chcesz używać.

5.  Na **wybierz sposób przywracania kopii zapasowej** wybierz opcję **Formatuj dyski i**. Na następnej stronie sprawdź ustawienia. 

6.  Aby rozpocząć przywracanie, wybierz **Zakończ**. Wymagane jest ponowne uruchomienie.

### <a name="restore-system-state"></a>Przywracanie stanu systemu

Uruchamianie odzyskiwania na serwerze kopii zapasowej:

1.  W **odzyskiwania** okienka i Znajdź komputer, który chcesz odzyskać, a następnie wybierz **odzyskiwanie systemu od zera**.

2.  Dostępne punkty odzyskiwania są pogrubione w kalendarzu. Wybierz datę i godzinę dla punktu odzyskiwania, którego chcesz używać.

3.  Na **Wybieranie typu odzyskiwania** wybierz **Kopiuj do folderu sieciowego**.

4.  Na **określ miejsce docelowe** wybierz, które chcesz skopiować dane. Należy pamiętać, że wybrane miejsce docelowe musi wystarczająco dużo miejsca. Firma Microsoft zaleca, aby utworzyć nowy folder.

5.  Na **Określ opcje odzyskiwania** wybierz ustawienia zabezpieczeń, które mają być stosowane. Następnie wybierz, czy chcesz użyć migawek sprzętowych opartych na sieci SAN szybciej odzyskiwać. (Jest to opcja tylko wtedy, gdy masz sieć SAN z tej funkcji i możliwości tworzenia i dzielenia klonu w celu umożliwienia zapisu. Ponadto chroniony komputer i kopii zapasowej serwera muszą być podłączone do tej samej sieci.)

6.  Ustaw opcje powiadamiania. Na **potwierdzenie** wybierz opcję **odzyskać**.

Uruchom narzędzie Kopia zapasowa systemu Windows Server:

1.  Wybierz **akcje** > **odzyskać** > **tego serwera** > **dalej**.

2.  Wybierz **inny serwer**, wybierz opcję **Określanie typu lokalizacji** strony, a następnie wybierz pozycję **zdalny folder udostępniony**. Wprowadź ścieżkę do folderu, który zawiera punkt odzyskiwania.

3.  Na **Wybieranie typu odzyskiwania** wybierz **stanu systemu**. 

4. Na **Wybieranie lokalizacji dla odzyskiwania stanu systemu** wybierz **oryginalnej lokalizacji**.

5.  Na **potwierdzenie** wybierz opcję **odzyskać**. Po przywróceniu Uruchom ponownie serwer.

6.  Przywracanie stanu systemu można również uruchomić polecenie w wierszu polecenia. Aby to zrobić, należy uruchomić narzędzie Kopia zapasowa systemu Windows Server na komputerze, który chcesz odzyskać. Aby uzyskać identyfikator wersji, w wierszu polecenia, wpisz: ```wbadmin get versions -backuptarget \<servername\sharename\>```

    Użyj identyfikatora wersji, aby uruchomić Przywracanie stanu systemu. W wierszu polecenia wpisz polecenie: ```wbadmin start systemstaterecovery -version:<versionidentified> -backuptarget:<servername\sharename>```

    Upewnij się, że chcesz rozpocząć odzyskiwanie. Możesz zobaczyć ten proces w oknie wiersza polecenia. Tworzony jest dziennik przywracania. Po przywróceniu Uruchom ponownie serwer.

