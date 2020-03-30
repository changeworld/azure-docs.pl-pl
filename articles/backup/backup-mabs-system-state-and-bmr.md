---
title: Stan systemu i ochrona odzysku bez metalu
description: Użyj usługi Azure Backup Server, aby wykonać kopię zapasową stanu systemu i zapewnić ochronę odzyskiwania bez systemu operacyjnego (BMR).
ms.topic: conceptual
ms.date: 05/15/2017
ms.openlocfilehash: 358a1c96d598788170993fc48e60daae2b6b036c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505881"
---
# <a name="back-up-system-state-and-restore-to-bare-metal-by-using-azure-backup-server"></a>Tworzenie kopii zapasowych stanu systemu i przywracanie gołego systemu za pomocą serwera kopii zapasowych platformy Azure

Usługa Azure Backup Server zapewnia kopię zapasową stanu systemu i zapewnia ochronę odzyskiwania bez systemu operacyjnego (BMR).

* **Kopia zapasowa stanu systemu:** Kopie zapasowe plików systemu operacyjnego. Ta kopia zapasowa umożliwia odzyskanie po uruchomieniu komputera, ale pliki systemowe i rejestr zostaną utracone. Kopia zapasowa stanu systemu zawiera następujące elementy:
  * Element członkowski domeny: pliki rozruchowe, baza danych rejestracji klasy COM+, rejestr
  * Kontroler domeny: usługa Active Directory systemu Windows Server (NTDS), pliki rozruchowe, baza danych rejestracji klas COM+, rejestr, wolumin systemowy (SYSVOL)
  * Komputer z uruchomień usług klastrowania: metadane serwera klastra
  * Komputer uruchamiany usługi certyfikatów: dane certyfikatu
* **Kopia zapasowa typu bare-metal:** Kopie zapasowe plików systemu operacyjnego i wszystkich danych na woluminach krytycznych, z wyjątkiem danych użytkownika. Z definicji kopia zapasowa BMR zawiera kopię zapasową stanu systemu. Zapewnia ochronę, gdy komputer nie uruchamia się i trzeba odzyskać wszystko.

W poniższej tabeli podsumowano, co można zrobić z kopii zapasowej i odzyskać. Aby uzyskać informacje o wersjach aplikacji, które mogą chronić stan systemu i bmr, zobacz [Co wykonuje kopia zapasowa usługi Azure Backup Server?](backup-mabs-protection-matrix.md).

|Tworzenie kopii zapasowych|Problem|Odzyskiwanie z kopii zapasowej serwera kopii zapasowej platformy Azure|Odzyskiwane z kopii zapasowej stanu systemu|BMR|
|----------|---------|---------------------------|------------------------------------|-------|
|**Dane pliku**<br /><br />Regularne tworzenie kopii zapasowej danych<br /><br />Kopia zapasowa stanu systemu/kompletnego stanu systemu|Utracone dane pliku|Tak|Nie|Nie|
|**Dane pliku**<br /><br />Kopia zapasowa danych plików serwera kopii zapasowych platformy Azure<br /><br />Kopia zapasowa stanu systemu/kompletnego stanu systemu|Uszkodzony lub utracony system operacyjny|Nie|Tak|Tak|
|**Dane pliku**<br /><br />Kopia zapasowa danych plików serwera kopii zapasowych platformy Azure<br /><br />Kopia zapasowa stanu systemu/kompletnego stanu systemu|Utracony serwer (woluminy danych nienaruszone)|Nie|Nie|Tak|
|**Dane pliku**<br /><br />Kopia zapasowa danych plików serwera kopii zapasowych platformy Azure<br /><br />Kopia zapasowa stanu systemu/kompletnego stanu systemu|Utracony serwer (utracone woluminy danych)|Tak|Nie|Tak<br /><br />BMR, a następnie regularne odzyskiwanie kopii zapasowych danych plików|
|**Dane programu SharePoint**<br /><br />Tworzenie kopii zapasowej danych farmy przez serwer kopii zapasowych platformy Azure<br /><br />Kopia zapasowa stanu systemu/kompletnego stanu systemu|Utracona witryna, listy, elementy listy, dokumenty|Tak|Nie|Nie|
|**Dane programu SharePoint**<br /><br />Tworzenie kopii zapasowej danych farmy przez serwer kopii zapasowych platformy Azure<br /><br />Kopia zapasowa stanu systemu/kompletnego stanu systemu|Uszkodzony lub utracony system operacyjny|Nie|Tak|Tak|
|**Dane programu SharePoint**<br /><br />Tworzenie kopii zapasowej danych farmy przez serwer kopii zapasowych platformy Azure<br /><br />Kopia zapasowa stanu systemu/kompletnego stanu systemu|Odzyskiwanie po awarii|Nie|Nie|Nie|
|Windows Server 2012 R2 Hyper-V<br /><br />Kopia zapasowa serwera kopii zapasowej usługi Azure Server hosta lub gościa funkcji Hyper-V<br /><br />Kopia zapasowa stanu systemu/kompletnego stanu systemu hosta|Utracona maszyna wirtualna|Tak|Nie|Nie|
|Funkcja Hyper-V<br /><br />Kopia zapasowa serwera kopii zapasowej usługi Azure Server hosta lub gościa funkcji Hyper-V<br /><br />Kopia zapasowa stanu systemu/kompletnego stanu systemu hosta|Uszkodzony lub utracony system operacyjny|Nie|Tak|Tak|
|Funkcja Hyper-V<br /><br />Kopia zapasowa serwera kopii zapasowej usługi Azure Server hosta lub gościa funkcji Hyper-V<br /><br />Kopia zapasowa stanu systemu/kompletnego stanu systemu hosta|Utracony host funkcji Hyper-V (nieuszkodzone maszyny wirtualne)|Nie|Nie|Tak|
|Funkcja Hyper-V<br /><br />Kopia zapasowa serwera kopii zapasowej usługi Azure Server hosta lub gościa funkcji Hyper-V<br /><br />Kopia zapasowa stanu systemu/kompletnego stanu systemu hosta|Utracony host funkcji Hyper-V (utracone maszyny wirtualne)|Nie|Nie|Tak<br /><br />BMR, po którym następuje regularne odzyskiwanie usługi Azure Backup Server|
|Program SQL Server/Exchange<br /><br />Kopia zapasowa aplikacji usługi Azure Backup Server<br /><br />Kopia zapasowa stanu systemu/kompletnego stanu systemu|Utracone dane aplikacji|Tak|Nie|Nie|
|Program SQL Server/Exchange<br /><br />Kopia zapasowa aplikacji usługi Azure Backup Server<br /><br />Kopia zapasowa stanu systemu/kompletnego stanu systemu|Uszkodzony lub utracony system operacyjny|Nie|Tak|Tak|
|Program SQL Server/Exchange<br /><br />Kopia zapasowa aplikacji usługi Azure Backup Server<br /><br />Kopia zapasowa stanu systemu/kompletnego stanu systemu|Utracony serwer (nieuszkodzone dzienniki transakcji i baz danych)|Nie|Nie|Tak|
|Program SQL Server/Exchange<br /><br />Kopia zapasowa aplikacji usługi Azure Backup Server<br /><br />Kopia zapasowa stanu systemu/kompletnego stanu systemu|Utracony serwer (utracone dzienniki transakcji i baz danych)|Nie|Nie|Tak<br /><br />Odzyskiwanie BMR, a następnie regularne odzyskiwanie usługi Azure Backup Server|

## <a name="how-system-state-backup-works"></a>Jak działa tworzenie kopii zapasowej stanu systemu

Po uruchomieniu kopii zapasowej stanu systemu serwer kopii zapasowej komunikuje się z programem Kopia zapasowa systemu Windows Server w celu zażądania kopii zapasowej stanu systemu serwera. Domyślnie serwer kopii zapasowych i kopia zapasowa systemu Windows Server korzystają z dysku, na który ma najwięcej wolnego miejsca. Informacje o tym dysku są zapisywane w pliku *PSDataSourceConfig.xml.* 

Można dostosować dysk używany przez serwer kopii zapasowej do tworzenia kopii zapasowej stanu systemu: 

1. Na chronionym serwerze przejdź do *folderu C:\Program Files\Microsoft Data Protection Manager\MABS\Datasources*. 
1. Otwórz plik *PSDataSourceConfig.xml* do edycji. 
1. Zmień \<wartość FilesToProtect\> dla litery dysku. 
1. Zapisz i zamknij plik. 

Jeśli grupa ochrony jest ustawiona w celu ochrony stanu systemu komputera, uruchom sprawdzanie spójności. Jeśli alert jest generowany, wybierz pozycję **Modyfikuj grupę ochrony** w alertie, a następnie wypełnij strony kreatora. Następnie uruchom kolejną kontrolę spójności.

Jeśli serwer ochrony znajduje się w klastrze, dysk klastrowy może zostać wybrany jako dysk, na który ma najwięcej wolnego miejsca. Jeśli własność tego dysku zostanie przełączona do innego węzła i uruchomi się kopia zapasowa stanu systemu, dysk jest niedostępny, a kopia zapasowa nie powiedzie się. W tym scenariuszu zmodyfikuj *plik PSDataSourceConfig.xml,* aby wskazać dysk lokalny.

Następnie program Kopia zapasowa systemu Windows Server tworzy folder o nazwie *WindowsImageBackup* w katalogu głównym folderu przywracania. Gdy kopia zapasowa systemu Windows Server tworzy kopię zapasową, wszystkie dane są umieszczane w tym folderze. Po zakończeniu tworzenia kopii zapasowej plik jest przesyłany do komputera serwera kopii zapasowych. Zanotuj następujące informacje:

* Ten folder i jego zawartość nie są czyszczone po zakończeniu tworzenia kopii zapasowej lub transferu. Najlepszym sposobem, aby myśleć o tym jest to, że miejsce jest zarezerwowane dla następnego czasu zakończenia kopii zapasowej.
* Folder jest tworzony dla każdej kopii zapasowej. Sygnatura czasu i daty odzwierciedla godzinę ostatniej kopii zapasowej stanu systemu.

## <a name="how-bmr-backup-works"></a>Jak działa kopia zapasowa BMR

W przypadku usługi BMR (w tym kopii zapasowej stanu systemu) zadanie tworzenia kopii zapasowej jest zapisywane bezpośrednio w udziale na komputerze z serwerem kopii zapasowych. Nie jest zapisywany w folderze na chronionym serwerze.

Serwer kopii zapasowych wywołuje program Kopia zapasowa systemu Windows Server i udostępnia wolumin repliki dla tej kopii zapasowej BMR. W takim przypadku nie wymaga programu Kopia zapasowa systemu Windows Server do korzystania z dysku, który ma najwięcej wolnego miejsca. Zamiast tego używa udziału, który został utworzony dla zadania.

Po zakończeniu tworzenia kopii zapasowej plik jest przesyłany do komputera serwera kopii zapasowych. Dzienniki są przechowywane w *języku C:\Windows\Logs\WindowsServerBackup*.

## <a name="prerequisites-and-limitations"></a>Wymagania wstępne i ograniczenia

* Bmr nie jest obsługiwany dla komputerów z systemem Windows Server 2003 ani dla komputerów z klienckim systemem operacyjnym.

* Nie można używać jednocześnie ochrony BMR i stanu systemu dla komputera znajdującego się w różnych grupach ochrony.

* Komputer z serwerem kopii zapasowych nie może chronić się dla bmr.

* Krótkoterminowa ochrona przed taśmą (dysk na taśmę lub D2T) nie jest obsługiwana dla BMR. Obsługiwane jest długoterminowe przechowywanie na taśmie (dysk na dysk na taśmę lub D2D2T).

* Aby uzyskać ochronę BMR, kopia zapasowa systemu Windows Server musi być zainstalowana na chronionym komputerze.

* W przypadku ochrony BMR, w przeciwieństwie do ochrony stanu systemu, serwer kopii zapasowych nie ma wymagań dotyczących miejsca na chronionym komputerze. Kopia zapasowa systemu Windows Server bezpośrednio przesyła kopie zapasowe na komputer serwera kopii zapasowych. Zadanie transferu kopii zapasowej nie jest wyświetlane w widoku **Zadania** serwera kopii zapasowych.

* Serwer kopii zapasowych rezerwuje 30 GB miejsca na woluminie repliki dla bmr. Przydział miejsca można zmienić na stronie **Alokacja dysku** w Kreatorze modyfikowania grupy ochrony. Można też użyć polecenia cmdlet programu Get-DatasourceDiskAllocation i Set-DatasourceDiskAllocation PowerShell. Na woluminie punktu odzyskiwania ochrony BMR wymaga około 6 GB dla przechowywania pięciu dni.
  * Nie można zmniejszyć rozmiaru woluminu repliki do mniej niż 15 GB.
  * Serwer kopii zapasowej nie oblicza rozmiaru źródła danych BMR. Zakłada 30 GB dla wszystkich serwerów. Zmień wartość na podstawie rozmiaru kopii zapasowych BMR, których oczekujesz w swoim środowisku. Można z grubsza obliczyć rozmiar kopii zapasowej BMR jako sumę zużytego miejsca na wszystkich woluminach krytycznych. Woluminy krytyczne = wolumin rozruchowy + wolumin systemowy + dane o stanie systemu hostingu woluminów, takie jak usługa Active Directory.

* Jeśli zmienisz ochronę stanu systemu na ochronę BMR, ochrona BMR wymaga mniej miejsca na *woluminie punktu odzyskiwania*. Jednak dodatkowe miejsce na woluminie nie jest odzyskiwał. Rozmiar woluminu można zmniejszyć ręcznie na stronie **Modyfikowanie alokacji dysku** Kreatora modyfikowania grupy ochrony. Można też użyć polecenia cmdlet programu Get-DatasourceDiskAllocation i Set-DatasourceDiskAllocation PowerShell.

    Jeśli zmienisz ochronę stanu systemu na ochronę BMR, ochrona BMR wymaga więcej miejsca na *woluminie repliki*. Głośność zostanie automatycznie rozszerzona. Jeśli chcesz zmienić domyślne alokacje miejsca, użyj polecenia cmdlet programu PowerShell Modify-DiskAllocation.

* Jeśli zmienisz ochronę BMR na ochronę stanu systemu, potrzebujesz więcej miejsca na woluminie punktu odzyskiwania. Serwer kopii zapasowej może próbować automatycznie zwiększyć wolumin. Jeśli pula magazynu nie ma wystarczającej ilości miejsca, występuje błąd.

    Jeśli zmienisz ochronę BMR na ochronę stanu systemu, potrzebujesz miejsca na chronionym komputerze. Potrzebne jest miejsce, ponieważ ochrona stanu systemu najpierw zapisuje replikę na komputerze lokalnym, a następnie przenosi replikę na komputer serwera kopii zapasowej.

## <a name="before-you-begin"></a>Przed rozpoczęciem

1. **Wdrażanie serwera kopii zapasowych platformy Azure**. Sprawdź, czy serwer kopii zapasowej jest poprawnie wdrożony. Aby uzyskać więcej informacji, zobacz:
    * [Wymagania systemowe dla usługi Azure Backup Server](https://docs.microsoft.com/system-center/dpm/install-dpm#setup-prerequisites)
    * [Macierz ochrony serwera kopii zapasowych](backup-mabs-protection-matrix.md)

1. **Skonfiguruj magazyn**. Dane kopii zapasowej można przechowywać na dysku, na taśmie i w chmurze za pomocą platformy Azure. Aby uzyskać więcej informacji, zobacz [Przygotowywanie przechowywania danych](https://docs.microsoft.com/system-center/dpm/plan-long-and-short-term-data-storage).

1. **Skonfiguruj agenta ochrony**. Zainstaluj agenta ochrony na komputerze, którego chcesz zrobić, aby uzyskać jego utworzenie. Aby uzyskać więcej informacji, zobacz [Wdrażanie agenta ochrony programu DPM](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent).

## <a name="back-up-system-state-and-bare-metal"></a>Tworzenie kopii zapasowej stanu systemu i kompletnego stanu systemu

Aby przywrócić zapas stanu systemu i gołego metalu:

1. Aby otworzyć Kreatora tworzenia nowej grupy ochrony, w konsoli Administratora serwera kopii zapasowych wybierz pozycję**Akcje** >  **ochrony** > **Utwórz grupę ochrony**.

1. Na stronie **Wybierz typ grupy ochrony** wybierz pozycję **Serwery**, a następnie wybierz pozycję **Dalej**.

1. Na stronie **Wybieranie członków grupy** rozwiń komputer, a następnie wybierz **bmr** lub **stan systemu**.

    Pamiętaj, że nie można chronić zarówno BMR, jak i stanu systemu dla tego samego komputera w różnych grupach. Ponadto po wybraniu bmr stan systemu jest automatycznie włączony. Aby uzyskać więcej informacji, zobacz [Wdrażanie grup ochrony](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups).

1. Na stronie **Wybierz metodę ochrony danych** wybierz sposób obsługi krótkoterminowej kopii zapasowej i długoterminowej kopii zapasowej. 

    Tworzenie kopii zapasowej krótkoterminowej jest zawsze na dysku pierwszy, z opcją tworzenia kopii zapasowej z dysku na platformie Azure przy użyciu usługi Azure Backup (krótkoterminowe lub długoterminowe). Alternatywą dla długoterminowej kopii zapasowej w chmurze jest skonfigurowanie długoterminowej kopii zapasowej na autonomicznym urządzeniu taśmowym lub bibliotece taśm połączonych z serwerem kopii zapasowych.

1. Na stronie **Wybierz cele krótkoterminowe** wybierz sposób utworzenia kopii zapasowej do magazynu krótkoterminowego na dysku:
    * W polu **Retencja zakres**wybierz, jak długo dane mają być utrzymywane na dysku.
    * W przypadku **częstotliwości synchronizacji**wybierz częstotliwość uruchamiania przyrostowej kopii zapasowej na dysku. Jeśli nie chcesz ustawiać interwału tworzenia kopii zapasowych, możesz wybrać **opcję Tuż przed punktem odzyskiwania**. Serwer kopii zapasowej uruchomi ekspresową pełną kopię zapasową tuż przed zaplanowaniem każdego punktu odzyskiwania.

1. Jeśli chcesz przechowywać dane na taśmie do długoterminowego przechowywania, a następnie na stronie **Określ cele długoterminowe,** wybierz, jak długo przechowywać dane taśmy (od 1 do 99 lat).
    1. W przypadku **opcji Częstotliwość tworzenia kopii zapasowych**wybierz częstotliwość uruchamiania kopii zapasowej na taśmie. Częstotliwość jest oparta na wybranym zakresie przechowywania:
        * Gdy zakres retencji wynosi od 1 do 99 lat, można uzupełnić zapasy zapasowe codziennie, co tydzień, co dwa tygodnie, co miesiąc, co kwartał, pół roku lub rocznie.
        * Gdy zakres retencji wynosi od 1 do 11 miesięcy, można uzupełnić zapasy zapasowe codziennie, co tydzień, co dwa tygodnie lub co miesiąc.
        * Gdy zakres retencji wynosi od 1 do 4 tygodni, można uzupełnić zapasy zapasowe codziennie lub co tydzień.

    1. Na stronie **Wybieranie szczegółów taśmy i biblioteki** wybierz taśmę i bibliotekę do użycia. Wybierz również, czy dane mają być kompresowane i szyfrowane.

1. Na stronie **Przejrzyj alokację dysku** przejrzyj miejsce na dysku puli magazynów, które jest dostępne dla grupy ochrony.

    * **Całkowity rozmiar danych** to rozmiar danych, których chcesz poprzeć.
    * **Miejsce na dysku, które ma być aprowizowane na serwerze kopii zapasowej platformy Azure,** to miejsce zalecane przez serwer kopii zapasowej dla grupy ochrony. Serwer kopii zapasowych używa tych ustawień, aby wybrać idealny wolumin kopii zapasowej. Opcje woluminu kopii zapasowej można edytować w **szczegółach alokacji dysku**.
    * W przypadku obciążeń w menu rozwijanym wybierz preferowaną pamięć masową. Edycja zmienia wartości pozycji **Całkowita ilość miejsca dla magazynu** i **Wolne miejsce w magazynie** w okienku **Dostępny magazyn dyskowy**. Niedoprowizowana przestrzeń to ilość miejsca, którą serwer kopii zapasowej sugeruje dodać do woluminu, aby zapewnić płynne tworzenie kopii zapasowych.

1. Na stronie **Wybierz metodę tworzenia repliki** wybierz sposób obsługi początkowej replikacji pełnych danych. 

    Jeśli zdecydujesz się na replikację za pośrednictwem sieci, zaleca się wybranie godziny poza szczytem. W przypadku dużych ilości danych lub warunków sieciowych, które są mniej niż optymalne, należy rozważyć replikację danych w trybie offline przy użyciu nośników wymiennych.

1. Na stronie **Wybieranie opcji sprawdzania spójności** wybierz sposób automatyzacji kontroli spójności. 

    Sprawdzanie można uruchomić tylko wtedy, gdy dane repliki staną się niespójne lub zgodnie z harmonogramem. Jeśli nie chcesz konfigurować automatycznego sprawdzania spójności, możesz w dowolnym momencie uruchomić ręczne sprawdzanie. Aby uruchomić ręczne sprawdzanie, w obszarze **Ochrona** konsoli administratora serwera kopii zapasowych kliknij prawym przyciskiem myszy grupę ochrony, a następnie wybierz polecenie **Sprawdź spójność**.

1. Jeśli zdecydujesz się wykonać kopię zapasową w chmurze przy użyciu usługi Azure Backup, na stronie **Określ dane ochrony online** wybierz obciążenia, których kopię zapasową chcesz utworzyć na platformie Azure.

1. Na stronie **Określ harmonogram tworzenia kopii zapasowych** w trybie online wybierz, jak często przyrostowo utworzyć kopię zapasową na platformie Azure. 

    Można zaplanować wykonywanie kopii zapasowych codziennie, w tygodniu, miesiącu i roku. Można również wybrać godzinę i datę, z którą mają być uruchamiane kopie zapasowe. Kopie zapasowe mogą być tworzone maksymalnie dwa razy dziennie. Za każdym razem, gdy uruchamia się kopia zapasowa, punkt odzyskiwania danych jest tworzony na platformie Azure na podstawie kopii danych kopii zapasowej przechowywanych na dysku serwera kopii zapasowej.

1. Na stronie **Określ zasady przechowywania w trybie online** wybierz sposób przechowywania punktów odzyskiwania utworzonych na podstawie codziennych, tygodniowych, miesięcznych i 5 000 kopii zapasowych na platformie Azure.

1. Na stronie **Wybierz replikację online** wybierz sposób początkowej pełnej replikacji danych. 

    Można replikować za pośrednictwem sieci lub kopii zapasowej w trybie offline (rozmieszczanie w trybie offline). Kopia zapasowa w trybie offline korzysta z funkcji importu platformy Azure. Aby uzyskać więcej informacji, zobacz [Przepływ pracy tworzenia kopii zapasowych w trybie offline w usłudze Azure Backup](offline-backup-azure-data-box.md).

1. Na stronie **Podsumowanie** przejrzyj ustawienia. Po wybraniu opcji **Utwórz grupę**nastąpi początkowa replikacja danych. Po zakończeniu replikacji danych na stronie **Stan** stan grupy ochrony jest **ok**. Następnie kopie zapasowe są nachyli zgodnie z ustawieniami grupy ochrony.

## <a name="recover-system-state-or-bmr"></a>Odzyskiwanie stanu systemu lub odzyskiwanie systemu od zera (BMR)

Możliwe jest odzyskanie systemu od zera (BMR) lub odzyskanie stanu systemu w lokalizacji sieciowej. Jeśli wykonasz kopię zapasową bmr, użyj środowiska odzyskiwania systemu Windows (WinRE), aby uruchomić system i podłączyć go do sieci. Użyj narzędzia Kopia zapasowa systemu Windows Server, aby odzyskać informacje z lokalizacji sieciowej. Jeśli wykonasz kopię zapasową stanu systemu, po prostu użyj programu Kopia zapasowa systemu Windows Server, aby odzyskać dane z lokalizacji sieciowej.

### <a name="restore-bmr"></a>Przywracanie systemu od zera (BMR)

Aby uruchomić odzyskiwanie na komputerze z serwerem kopii zapasowych:

1. W okienku **Odzyskiwanie** znajdź komputer, który chcesz odzyskać. Następnie wybierz **opcję Odzyskiwanie bezgłowego**.

1. Dostępne punkty odzyskiwania są pogrubione w kalendarzu. Wybierz datę i godzinę punktu odzyskiwania, którego chcesz użyć.

1. Na stronie **Wybierz typ odzyskiwania** wybierz pozycję **Kopiuj do folderu sieciowego**.

1. Na stronie **Określanie miejsca docelowego** wybierz miejsce docelowe dla skopiowanych danych. 

    Pamiętaj, że miejsce docelowe musi mieć wystarczająco dużo miejsca na dane. Zaleca się utworzenie nowego folderu dla miejsca docelowego.

1. Na stronie **Określanie opcji odzyskiwania** wybierz ustawienia zabezpieczeń. Następnie wybierz, czy migawki sprzętowe oparte na sieci magazynowej (SAN) mają być używane do szybszego odzyskiwania. Ta opcja jest dostępna tylko wtedy, gdy:
    * Masz san, który zapewnia tę funkcję. 
    * Klon można utworzyć i podzielić, aby można go było zapisywać.
    * Komputer chroniony i komputer z serwerem kopii zapasowych są podłączone do tej samej sieci.

1. Konfigurowanie opcji powiadomień. 
1. Na stronie **Potwierdzenie** wybierz pozycję **Odzyskaj**.

Aby skonfigurować lokalizację udziału:

1. W lokalizacji przywracania przejdź do folderu, w którym znajduje się kopia zapasowa.

1. Udostępnij folder, który jest jeden poziom powyżej *WindowsImageBackup* tak, aby katalog główny folderu udostępnionego jest *WindowsImageBackup* folderu. 

    Jeśli nie udostępnisz tego folderu, funkcja przywracania nie znajdzie kopii zapasowej. Aby połączyć się za pomocą WinRE, potrzebujesz udziału, do którego można uzyskać dostęp w WinRE z poprawnym adresem IP i poświadczeniami.

Aby przywrócić system:

1. Uruchom komputer, na którym chcesz przywrócić obraz, używając dysku DVD systemu Windows dla przywracania systemu.

1. Na pierwszej stronie sprawdź ustawienia języka i ustawień regionalnych. Na stronie **Instalacja** wybierz pozycję **Napraw komputer**.

1. Na stronie **Opcje odzyskiwania systemu** wybierz pozycję **Przywróć komputer przy użyciu obrazu systemu utworzonego wcześniej**.

1. Na stronie **Wybieranie kopii zapasowej obrazu systemu** wybierz pozycję Wybierz obraz >  **systemowy****Zaawansowane** > **wyszukiwanie obrazu systemowego w sieci**. Jeśli pojawi się ostrzeżenie, wybierz przycisk **Tak**. Przejdź do ścieżki udziału, wprowadź poświadczenia, a następnie wybierz punkt odzyskiwania. System skanuje w poszukiwaniu określonych kopii zapasowych, które są dostępne w tym punkcie odzyskiwania. Wybierz punkt odzyskiwania, którego chcesz użyć.

1. Na stronie **Wybieranie sposobu przywracania kopii zapasowej** wybierz pozycję **Formatuj i ponownie losuj dyski**. Na następnej stronie sprawdź ustawienia.

1. Aby rozpocząć przywracanie, wybierz pozycję **Zakończ**. Wymagane jest ponowne uruchomienie.

### <a name="restore-system-state"></a>Przywracanie stanu systemu

Aby uruchomić odzyskiwanie na serwerze kopii zapasowej:

1. W okienku **Odzyskiwanie** znajdź komputer, który chcesz odzyskać, a następnie wybierz pozycję **Odzyskiwanie systemu operacyjnego .**

1. Dostępne punkty odzyskiwania są pogrubione w kalendarzu. Wybierz datę i godzinę punktu odzyskiwania, którego chcesz użyć.

1. Na stronie **Wybierz typ odzyskiwania** wybierz pozycję **Kopiuj do folderu sieciowego**.

1. Na stronie **Określanie miejsca docelowego** wybierz miejsce kopiowania danych. 

    Pamiętaj, że wybrane miejsce docelowe musi mieć wystarczająco dużo miejsca na dane. Zaleca się utworzenie nowego folderu dla miejsca docelowego.

1. Na stronie **Określanie opcji odzyskiwania** wybierz ustawienia zabezpieczeń. Następnie wybierz, czy mają być używane migawki sprzętowe oparte na sieci SAN w celu szybszego odzyskiwania. Ta opcja jest dostępna tylko wtedy, gdy: 
    * Masz san, który zapewnia tę funkcję.
    * Klon można utworzyć i podzielić, aby można go było zapisywać. 
    * Chroniony komputer i serwer serwera kopii zapasowych są połączone z tą samą siecią.

1. Konfigurowanie opcji powiadomień. 
1. Na stronie **Potwierdzenie** wybierz pozycję **Odzyskaj**.

Aby uruchomić kopię zapasową systemu Windows Server:

1. Wybierz **akcje** > **Odzyskaj** > **ten serwer** > **dalej**.

1. Wybierz **pozycję Inny serwer**, wybierz stronę **Określ typ lokalizacji,** a następnie wybierz pozycję **Zdalny folder udostępniony**. Wprowadź ścieżkę do folderu zawierającego punkt odzyskiwania.

1. Na stronie **Wybierz typ odzyskiwania** wybierz pozycję Stan **systemu**.

1. Na stronie **Wybierz lokalizację dla odzyskiwania stanu systemu** wybierz pozycję Lokalizacja **oryginalna**.

1. Na stronie **Potwierdzenie** wybierz pozycję **Odzyskaj**. 

1. Po przywróceniu uruchom ponownie serwer.

Można również uruchomić przywracanie stanu systemu w wierszu polecenia: 

1. Uruchom program Kopia zapasowa systemu Windows Server na komputerze, który chcesz odzyskać. 

1. Aby uzyskać identyfikator wersji, w wierszu polecenia wprowadź:

   ```wbadmin get versions -backuptarget \<servername\sharename\>```

1. Użyj identyfikatora wersji, aby uruchomić przywracanie stanu systemu. W wierszu polecenia wprowadź: 

    ```wbadmin start systemstaterecovery -version:<versionidentified> -backuptarget:<servername\sharename>```

1. Potwierdź, że chcesz rozpocząć odzyskiwanie. Proces można zobaczyć w oknie wiersza polecenia. Tworzony jest dziennik przywracania. 

1. Po przywróceniu uruchom ponownie serwer.
