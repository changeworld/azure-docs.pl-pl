---
title: Ochrona stanu systemu & Przywracanie systemu od zera — Azure Backup Server
description: Użyj Azure Backup Server, aby utworzyć kopię zapasową stanu systemu i zapewnić ochronę przed odzyskiwaniem od zera (BMR).
author: dcurwin
manager: carmonm
keywords: ''
ms.service: backup
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: dacurwin
ms.openlocfilehash: 50f534a5a682cd9e4f6aeb040b897e7aae48dddd
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969031"
---
# <a name="back-up-system-state-and-restore-to-bare-metal-with-azure-backup-server"></a>Tworzenie kopii zapasowej stanu systemu i przywracanie na komputerach bez systemu operacyjnego za pomocą Azure Backup Server

Azure Backup Server Tworzenie kopii zapasowej stanu systemu i zapewnia ochronę bez systemu operacyjnego (BMR).

* **Kopia zapasowa stanu systemu**: tworzy kopie zapasowe plików systemu operacyjnego, dzięki czemu można odzyskiwać podczas uruchamiania komputera, ale pliki systemowe i rejestr zostaną utracone. Kopia zapasowa stanu systemu obejmuje:
  * Członek domeny: pliki rozruchowe, baza danych rejestracji klasy COM+, rejestr
  * Kontroler domeny: Windows Server Active Directory (NTDS), pliki rozruchowe, baza danych rejestracji klasy COM+, rejestr, wolumin systemowy (SYSVOL)
  * Komputer z uruchomionymi usługami klastra: metadane serwera klastra
  * Komputer z uruchomionymi usługami certyfikatów: dane certyfikatu
* **Kopia zapasowa bez systemu**operacyjnego: tworzy kopie zapasowe plików systemów operacyjnych i wszystkich danych na woluminach krytycznych (z wyjątkiem danych użytkownika). Zgodnie z definicją kopia zapasowa BMR obejmuje kopię zapasową stanu systemu. Zapewnia ochronę, gdy komputer nie zostanie uruchomiony i konieczne będzie odzyskanie wszystkiego.

Poniższa tabela zawiera podsumowanie informacji o tym, co można utworzyć i odzyskać. Aby uzyskać szczegółowe informacje na temat wersji aplikacji, które mogą być chronione przy użyciu stanu systemu i BMR, zobacz [co to jest Azure Backup Server kopia zapasowa?](backup-mabs-protection-matrix.md).

|Backup|Problem|Odzyskaj z kopii zapasowej Azure Backup Server|Odzyskaj z kopii zapasowej stanu systemu|BMR|
|----------|---------|---------------------------|------------------------------------|-------|
|**Dane pliku**<br /><br />Regularne kopie zapasowe danych<br /><br />BMR/kopia zapasowa stanu systemu|Utracone dane pliku|Tak|N|N|
|**Dane pliku**<br /><br />Azure Backup Server kopii zapasowej danych plików<br /><br />BMR/kopia zapasowa stanu systemu|Utracony lub uszkodzony system operacyjny|N|Tak|Tak|
|**Dane pliku**<br /><br />Azure Backup Server kopii zapasowej danych plików<br /><br />BMR/kopia zapasowa stanu systemu|Utracony serwer (nienaruszone woluminy danych)|N|N|Tak|
|**Dane pliku**<br /><br />Azure Backup Server kopii zapasowej danych plików<br /><br />BMR/kopia zapasowa stanu systemu|Utracony serwer (utracone woluminy danych)|Tak|Nie|Tak (BMR, po którym następuje regularne odzyskiwanie danych pliku kopii zapasowej)|
|**Dane programu SharePoint**:<br /><br />Azure Backup Server kopii zapasowej danych farmy<br /><br />BMR/kopia zapasowa stanu systemu|Utracona witryna, listy, elementy listy, dokumenty|Tak|N|N|
|**Dane programu SharePoint**:<br /><br />Azure Backup Server kopii zapasowej danych farmy<br /><br />BMR/kopia zapasowa stanu systemu|Utracony lub uszkodzony system operacyjny|N|Tak|Tak|
|**Dane programu SharePoint**:<br /><br />Azure Backup Server kopii zapasowej danych farmy<br /><br />BMR/kopia zapasowa stanu systemu|Odzyskiwanie po awarii|N|N|N|
|Windows Server 2012 R2 Hyper-V<br /><br />Azure Backup Server kopii zapasowej hosta lub gościa funkcji Hyper-V<br /><br />BMR/kopia zapasowa stanu systemu hosta|Utracona maszyna wirtualna|Tak|N|N|
|Funkcja Hyper-V<br /><br />Azure Backup Server kopii zapasowej hosta lub gościa funkcji Hyper-V<br /><br />BMR/kopia zapasowa stanu systemu hosta|Utracony lub uszkodzony system operacyjny|N|Tak|Tak|
|Funkcja Hyper-V<br /><br />Azure Backup Server kopii zapasowej hosta lub gościa funkcji Hyper-V<br /><br />BMR/kopia zapasowa stanu systemu hosta|Utracony host funkcji Hyper-V (nienaruszone maszyny wirtualne)|N|N|Tak|
|Funkcja Hyper-V<br /><br />Azure Backup Server kopii zapasowej hosta lub gościa funkcji Hyper-V<br /><br />BMR/kopia zapasowa stanu systemu hosta|Utracony host funkcji Hyper-V (utracone maszyny wirtualne)|N|N|Tak<br /><br />BMR, po którym następuje regularne odzyskiwanie Azure Backup Server|
|SQL Server/Exchange<br /><br />Kopia zapasowa aplikacji Azure Backup Server<br /><br />BMR/kopia zapasowa stanu systemu|Utracone dane aplikacji|Tak|N|N|
|SQL Server/Exchange<br /><br />Kopia zapasowa aplikacji Azure Backup Server<br /><br />BMR/kopia zapasowa stanu systemu|Utracony lub uszkodzony system operacyjny|N|T|Tak|
|SQL Server/Exchange<br /><br />Kopia zapasowa aplikacji Azure Backup Server<br /><br />BMR/kopia zapasowa stanu systemu|Utracony serwer (nienaruszone dzienniki bazy danych/transakcji)|N|N|Tak|
|SQL Server/Exchange<br /><br />Kopia zapasowa aplikacji Azure Backup Server<br /><br />BMR/kopia zapasowa stanu systemu|Utracony serwer (utracone dzienniki bazy danych/transakcji)|N|N|Tak<br /><br />Odzyskiwanie BMR, po którym następuje regularne odzyskiwanie Azure Backup Server|

## <a name="how-system-state-backup-works"></a>Jak działa kopia zapasowa stanu systemu

Po uruchomieniu kopii zapasowej stanu systemu serwer zapasowy komunikuje się z Kopia zapasowa systemu Windows Server, aby zażądać kopii zapasowej stanu systemu serwera. Domyślnie serwer zapasowy i Kopia zapasowa systemu Windows Server korzystają z dysku z największą ilością wolnego miejsca. Informacje o tym dysku są zapisywane w pliku plik psdatasourceconfig. XML. Jest to dysk, którego Kopia zapasowa systemu Windows Server używa do tworzenia kopii zapasowych.

Można dostosować dysk wykorzystywany przez serwer kopii zapasowej do tworzenia kopii zapasowej stanu systemu. Na serwerze chronionym przejdź do katalogu C:\Program Files\Microsoft Data Protection Manager\MABS\Datasources. Otwórz plik plik psdatasourceconfig. XML do edycji. Zmień wartość \<FilesToProtect\> na literę dysku. Zapisz i zamknij plik. Jeśli istnieje grupa ochrony ustawiona w celu ochrony stanu systemu komputera, należy uruchomić sprawdzanie spójności. W przypadku wygenerowania alertu wybierz pozycję **Modyfikuj grupę ochrony** w alercie, a następnie Ukończ pracę kreatora. Następnie uruchom inne sprawdzanie spójności.

Należy pamiętać, że jeśli serwer ochrony znajduje się w klastrze, istnieje możliwość, że dysk klastra zostanie wybrany jako dysk z największą ilością wolnego miejsca. Jeśli własność dysku została przełączona na inny węzeł i zostanie uruchomiona kopia zapasowa stanu systemu, dysk nie jest dostępny i wykonywanie kopii zapasowej nie powiedzie się. W tym scenariuszu zmodyfikuj plik psdatasourceconfig. XML, aby wskazywał na dysk lokalny.

Następnie Kopia zapasowa systemu Windows Server tworzy folder o nazwie WindowsImageBackup w katalogu głównym folderu przywracania. Ponieważ Kopia zapasowa systemu Windows Server tworzy kopię zapasową, wszystkie dane zostaną umieszczone w tym folderze. Po zakończeniu tworzenia kopii zapasowej plik jest przesyłany do komputera serwera kopii zapasowej. Zanotuj następujące informacje:

* Ten folder i jego zawartość nie są czyszczone po zakończeniu tworzenia kopii zapasowej lub transferu. Najlepszym sposobem na to, że jest to, że miejsce jest rezerwowane przy następnym zakończeniu tworzenia kopii zapasowej.
* Folder jest tworzony za każdym razem, gdy tworzona jest kopia zapasowa. Sygnatura czasowa i daty odzwierciedlają czas ostatniej kopii zapasowej stanu systemu.

## <a name="bmr-backup"></a>Kopia zapasowa BMR

W przypadku BMR (w tym kopii zapasowej stanu systemu) zadanie tworzenia kopii zapasowej jest zapisywane bezpośrednio w udziale na komputerze serwera kopii zapasowej. Nie jest on zapisywany w folderze na serwerze chronionym.

Wywołania serwera kopii zapasowej Kopia zapasowa systemu Windows Server i udostępniają wolumin repliki dla tej kopii zapasowej BMR. W takim przypadku nie informuje Kopia zapasowa systemu Windows Server o użyciu dysku z największą ilością wolnego miejsca. Zamiast tego używa udziału, który został utworzony dla tego zadania.

Po zakończeniu tworzenia kopii zapasowej plik jest przesyłany do komputera serwera kopii zapasowej. Dzienniki są przechowywane w folderze c:\windows\logs\windowsserverbackup.

## <a name="prerequisites-and-limitations"></a>Wymagania wstępne i ograniczenia

* BMR nie jest obsługiwane w przypadku komputerów z systemem Windows Server 2003 lub na komputerach z systemem operacyjnym klienta.

* Nie można włączyć ochrony BMR i stanu systemu na tym samym komputerze w różnych grupach ochrony.

* Komputer serwera kopii zapasowej nie może chronić samego siebie dla BMR.

* Krótkoterminowa ochrona na taśmie (dysk-taśma lub D2T) nie jest obsługiwana dla BMR. Obsługa magazynu długoterminowego na taśmie (dysk-dysk-taśma lub D2D2T) jest obsługiwana.

* W przypadku ochrony BMR należy zainstalować na komputerze chronionym Kopia zapasowa systemu Windows Server.

* Dla ochrony BMR, w przeciwieństwie do ochrony stanu systemu, serwer kopii zapasowych nie ma żadnych wymagań dotyczących miejsca na komputerze chronionym. Kopia zapasowa systemu Windows Server bezpośrednio przesyła kopie zapasowe do komputera serwera kopii zapasowej. Zadanie transferu kopii zapasowej nie jest wyświetlane w widoku **zadań** serwera kopii zapasowej.

* Serwer kopii zapasowych rezerwuje 30 GB miejsca na woluminie repliki dla BMR. Można to zmienić na stronie **alokacja dysku** w Kreatorze modyfikowania grupy ochrony lub za pomocą poleceń cmdlet programu PowerShell Get-DatasourceDiskAllocation i Set-DatasourceDiskAllocation. Na woluminie punktu odzyskiwania ochrona BMR wymaga około 6 GB do przechowywania przez pięć dni.
  * Należy pamiętać, że nie można zmniejszyć rozmiaru woluminu repliki do mniej niż 15 GB.
  * Serwer kopii zapasowej nie oblicza rozmiaru źródła danych BMR. Założono 30 GB dla wszystkich serwerów. Zmień wartość na podstawie rozmiaru BMR kopii zapasowych oczekiwanych w danym środowisku. Rozmiar kopii zapasowej BMR może być w przybliżeniu obliczony jako suma zajętego miejsca na wszystkich woluminach krytycznych. Woluminy krytyczne = wolumin rozruchowy + wolumin systemowy + dane stanu systemu hostingu woluminu, takie jak Active Directory.

* W przypadku zmiany ochrony stanu systemu na ochronę BMR ochrona BMR wymaga mniej miejsca na *woluminie punktu odzyskiwania*. Dodatkowe miejsce na woluminie nie jest jednak odzyskiwane. Rozmiar woluminu można zmniejszyć ręcznie na stronie **Modyfikuj przydział dysku** w Kreatorze modyfikowania grupy ochrony lub za pomocą poleceń cmdlet Get-DatasourceDiskAllocation i Set-DatasourceDiskAllocation programu PowerShell.

    W przypadku zmiany z ochrony stanu systemu na ochronę BMR ochrona BMR wymaga więcej miejsca na *woluminie repliki*. Wolumin zostanie automatycznie rozszerzony. Jeśli chcesz zmienić domyślne alokacje miejsca, użyj polecenia cmdlet Modify-DiskAllocation programu PowerShell.

* W przypadku zmiany z ochrony BMR na ochronę stanu systemu potrzeba więcej miejsca na woluminie punktu odzyskiwania. Serwer kopii zapasowej może próbować automatycznie zwiększyć wolumin. Jeśli w puli magazynów jest za mało miejsca, wystąpi błąd.

    W przypadku zmiany z ochrony BMR na ochronę stanu systemu należy mieć miejsce na komputerze chronionym. Wynika to z faktu, że ochrona stanu systemu najpierw zapisuje replikę na komputerze lokalnym, a następnie przekazuje ją na komputer serwera zapasowego.

## <a name="before-you-begin"></a>Przed rozpoczęciem

1. **Wdróż Azure Backup Server**. Sprawdź, czy serwer kopii zapasowej został poprawnie wdrożony. Aby uzyskać więcej informacji, zobacz:
    * [Wymagania systemowe Azure Backup Server](https://docs.microsoft.com/system-center/dpm/install-dpm#setup-prerequisites)
    * [Macierz ochrony serwera kopii zapasowych](backup-mabs-protection-matrix.md)

2. **Skonfiguruj magazyn**. Dane kopii zapasowej można przechowywać na dysku, na taśmie i w chmurze przy użyciu platformy Azure. Aby uzyskać więcej informacji, zobacz [Przygotowywanie magazynu danych](https://docs.microsoft.com/system-center/dpm/plan-long-and-short-term-data-storage).

3. **Skonfiguruj agenta ochrony**. Zainstaluj agenta ochrony na komputerze, którego kopię zapasową chcesz utworzyć. Aby uzyskać więcej informacji, zobacz [wdrażanie agenta ochrony programu DPM](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent).

## <a name="back-up-system-state-and-bare-metal"></a>Tworzenie kopii zapasowej stanu systemu i bez systemu operacyjnego

Skonfiguruj grupę ochrony zgodnie z opisem w artykule [wdrażanie grup ochrony](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups). Należy pamiętać, że nie można chronić BMR i stanu systemu na tym samym komputerze w różnych grupach. Ponadto w przypadku wybrania opcji BMR stan systemu jest automatycznie włączany.

1. Aby otworzyć Kreatora tworzenia nowej grupy ochrony na serwerze kopii zapasowej konsola administratora, wybierz pozycję **ochrona** > **Akcje** > **Utwórz grupę ochrony**.

2. Na stronie **Wybierz typ grupy ochrony** wybierz pozycję **serwery**, a następnie wybierz przycisk **dalej**.

3. Na stronie **Wybierz członków grupy** rozwiń komputer, a następnie wybierz pozycję **BMR** lub **stan systemu**.

    Należy pamiętać, że nie można włączyć ochrony zarówno BMR, jak i stanu systemu na tym samym komputerze w różnych grupach. Ponadto w przypadku wybrania opcji BMR stan systemu jest automatycznie włączany. Aby uzyskać więcej informacji, zobacz [wdrażanie grup ochrony](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups).

4. Na stronie **Wybierz metodę ochrony danych** wybierz sposób obsługi krótkoterminowej i długoterminowej kopii zapasowej. Krótkoterminowe kopie zapasowe zawsze są na dysku, z opcją tworzenia kopii zapasowych z dysku w chmurze platformy Azure przy użyciu Azure Backup (krótkoterminowe lub długoterminowe). Alternatywą dla długoterminowej kopii zapasowej w chmurze jest skonfigurowanie długoterminowej kopii zapasowej na autonomicznym urządzeniu taśmowym lub w bibliotece taśm połączonej z serwerem kopii zapasowych.

5. Na stronie **Wybierz cele krótkoterminowe** wybierz, jak chcesz utworzyć kopię zapasową do krótkoterminowego przechowywania na dysku:
    1. W obszarze **Zakres przechowywania**Określ, jak długo mają być przechowywane dane na dysku.
    2. W obszarze **częstotliwość synchronizacji**wybierz, jak często chcesz uruchamiać przyrostową kopię zapasową na dysku. Jeśli nie chcesz ustawiać interwału kopii zapasowych, możesz zaznaczyć opcję **tuż przed punktem odzyskiwania** . Serwer kopii zapasowej będzie uruchamiał ekspresową pełną kopię zapasową tuż przed każdym zaplanowanym punktem odzyskiwania.

6. Jeśli chcesz przechowywać dane na taśmie w celu przechowywania długoterminowego, na stronie **Określ cele długoterminowe** wybierz, jak długo chcesz przechowywać dane na taśmie (1-99 lat).
    1. W obszarze **częstotliwość wykonywania kopii zapasowych**wybierz, jak często mają być uruchamiane kopie zapasowe na taśmie. Częstotliwość zależy od wybranego zakresu przechowywania:
        * Gdy zakres przechowywania wynosi 1-99 lat, można wybrać Tworzenie kopii zapasowych codziennie, co tydzień, co dwa tygodnie, co miesiąc, co kwartał, co pół roku lub co rok.
        * Gdy zakres przechowywania wynosi 1-11 miesięcy, możesz wybrać Tworzenie kopii zapasowych codziennie, co tydzień, co dwa tygodnie lub co miesiąc.
        * Gdy zakres przechowywania wynosi 1-4 tygodnie, można wybrać Tworzenie kopii zapasowej codziennie lub co tydzień.

    2. Na stronie **Wybieranie szczegółów taśmy i biblioteki** wybierz taśmę i bibliotekę, która ma być używana, oraz czy dane mają być kompresowane i szyfrowane.

7. Na stronie **Przejrzyj przydział dysku** Przejrzyj miejsce na dysku w puli magazynów, które zostało przydzielone dla grupy ochrony.

    1. **Łączny rozmiar danych** to rozmiar danych, dla których chcesz utworzyć kopię zapasową.
    2. **Miejsce na dysku, które ma zostać zainicjowane w Azure Backup Server** jest miejscem zalecanym dla grupy ochrony przez serwer kopii zapasowych. Serwer kopii zapasowych wybiera idealny wolumin kopii zapasowej na podstawie ustawień. Można jednak edytować opcje woluminu kopii zapasowej w obszarze **szczegóły alokacji dysku**.
    3. W przypadku obciążeń, w menu rozwijanym wybierz preferowany magazyn. Twoje zmiany zmieniają wartości **łącznego magazynu** i **wolnego magazynu** w okienku **dostępne Disk Storage** . Zajęte miejsce to ilość miejsca w magazynie, którą serwer kopii zapasowej sugeruje dodać do woluminu, aby zapewnić płynne kopie zapasowe.

8. Na stronie **Wybierz metodę tworzenia repliki** wybierz, jak chcesz obsługiwać początkową pełną replikację danych. Jeśli zdecydujesz się na replikację za pośrednictwem sieci, zalecamy wybranie czasu poza godzinami szczytu. W przypadku dużych ilości danych lub warunków sieci, które są mniejsze niż optymalne, należy rozważyć replikację danych w trybie offline za pomocą nośników wymiennych.

9. Na stronie **Wybierz opcje sprawdzania spójności** wybierz sposób automatyzacji sprawdzania spójności. Można uruchomić sprawdzanie tylko wtedy, gdy dane repliki staną się niespójne lub zgodnie z harmonogramem. Jeśli nie chcesz konfigurować automatycznego sprawdzania spójności, w dowolnym momencie możesz uruchomić sprawdzanie ręczne. Aby uruchomić sprawdzanie ręczne, w obszarze **Ochrona** serwera zapasowego Konsola administratora kliknij prawym przyciskiem myszy grupę ochrony, a następnie wybierz polecenie **Przeprowadź sprawdzanie spójności**.

10. Jeśli wybrano opcję tworzenia kopii zapasowej w chmurze przy użyciu Azure Backup, na stronie **Określ dane ochrony w trybie online** upewnij się, że wybrano obciążenia, których kopia zapasowa ma zostać utworzona na platformie Azure.

11. Na stronie **Określanie harmonogramu tworzenia kopii zapasowych online** wybierz, jak często mają być wykonywane przyrostowe kopie zapasowe na platformie Azure. Można zaplanować uruchamianie kopii zapasowych codziennie, tygodnia, miesiąca i roku, a także wybrać godzinę i datę, o których powinny one zostać uruchomione. Kopie zapasowe mogą odbywać się maksymalnie dwa razy dziennie. Za każdym razem, gdy wykonywana jest kopia zapasowa, punkt odzyskiwania danych jest tworzony na platformie Azure na podstawie kopii kopii zapasowej przechowywanych na dysku serwera zapasowego.

12. Na stronie **Określanie zasad przechowywania w trybie online** wybierz, w jaki sposób punkty odzyskiwania tworzone na podstawie kopii zapasowych codziennie, co tydzień, co miesiąc i co roku mają być przechowywane na platformie Azure.

13. Na stronie **Wybierz replikację online** wybierz, jak następuje początkowa pełna replikacja danych. Można replikować za pośrednictwem sieci lub wykonywać kopie zapasowe w trybie offline (w trybie offline). Kopia zapasowa offline używa funkcji importowania platformy Azure. Aby uzyskać więcej informacji, zobacz [przepływ pracy kopii zapasowej offline w Azure Backup](backup-azure-backup-import-export.md).

14. Na stronie **Podsumowanie** przejrzyj ustawienia. Po wybraniu opcji **Utwórz grupę**następuje Replikacja początkowa danych. Po zakończeniu replikacji danych na stronie **stan** Grupa ochrony ma stan **OK**. Następnie odbywa się tworzenie kopii zapasowych zgodnie z ustawieniami grupy ochrony.

## <a name="recover-system-state-or-bmr"></a>Odzyskiwanie stanu systemu lub BMR

Możesz odzyskać BMR lub stan systemu do lokalizacji sieciowej. Jeśli utworzono kopię zapasową BMR, użyj środowiska odzyskiwania systemu Windows (WinRE) do uruchomienia systemu i nawiązania połączenia z siecią. Następnie użyj Kopia zapasowa systemu Windows Server do odzyskania z lokalizacji sieciowej. Jeśli utworzono kopię zapasową stanu systemu, po prostu Użyj Kopia zapasowa systemu Windows Server do odzyskania z lokalizacji sieciowej.

### <a name="restore-bmr"></a>Przywróć BMR

Uruchom odzyskiwanie na komputerze serwera kopii zapasowej:

1. W okienku **odzyskiwanie** Znajdź komputer, który chcesz odzyskać, a następnie wybierz pozycję **odzyskiwanie systemu od zera**.

2. Dostępne punkty odzyskiwania są pogrubione w kalendarzu. Wybierz datę i godzinę dla punktu odzyskiwania, którego chcesz użyć.

3. Na stronie **Wybierz typ odzyskiwania** wybierz opcję **Kopiuj do folderu sieciowego.**

4. Na stronie **określ miejsce docelowe** wybierz miejsce, do którego chcesz skopiować dane. Należy pamiętać, że wybrane miejsce docelowe musi mieć wystarczającą ilość miejsca. Zalecamy utworzenie nowego folderu.

5. Na stronie **Określanie opcji odzyskiwania** wybierz ustawienia zabezpieczeń, które mają zostać zastosowane. Następnie wybierz, czy chcesz korzystać z migawek sprzętowych opartych na sieci magazynowania (SAN), aby szybciej odzyskiwać dane. (Jest to opcja dostępna tylko wtedy, gdy masz dostęp do sieci SAN z tą funkcją i można utworzyć i podzielić klon, aby umożliwić jego zapisywanie. Ponadto komputer chroniony i serwer zapasowy muszą być połączone z tą samą siecią.

6. Skonfiguruj opcje powiadomień. Na stronie **potwierdzenie** wybierz pozycję **Odzyskaj**.

Skonfiguruj lokalizację udziału:

1. W lokalizacji przywracania przejdź do folderu, w którym znajduje się kopia zapasowa.

2. Udostępnij folder, który jest na poziomie powyżej WindowsImageBackup, tak aby katalogiem głównym folderu udostępnionego był folder WindowsImageBackup. Jeśli tego nie zrobisz, instrukcja RESTORE nie odnajdzie kopii zapasowej. Aby nawiązać połączenie przy użyciu środowiska odzyskiwania systemu Windows (WinRE), należy udostępnić udział, do którego można uzyskać dostęp w środowisku WinRE przy użyciu poprawnego adresu IP i poświadczeń.

Przywróć system:

1. Uruchom komputer, na którym chcesz przywrócić obraz, przy użyciu dysku DVD systemu Windows dla przywracanego systemu.

2. Na pierwszej stronie Sprawdź ustawienia języka i ustawień regionalnych. Na stronie **Instalacja** wybierz opcję **Napraw komputer**.

3. Na stronie **Opcje odzyskiwania systemu** wybierz pozycję **Przywróć komputer przy użyciu utworzonego wcześniej obrazu systemu**.

4. Na stronie **Wybierz kopię zapasową obrazu systemu** wybierz pozycję **wybierz obraz systemu** > **Advanced** > **Wyszukiwanie obrazu systemu w sieci**. Jeśli zostanie wyświetlone ostrzeżenie, wybierz pozycję **tak**. Przejdź do ścieżki udziału, wprowadź poświadczenia, a następnie wybierz punkt odzyskiwania. To skanuje konkretne kopie zapasowe, które są dostępne w tym punkcie odzyskiwania. Wybierz punkt odzyskiwania, którego chcesz użyć.

5. Na stronie **Wybierz sposób przywracania kopii zapasowej** wybierz opcję **Formatuj dyski i ponownie Podziel na partycje**. Na następnej stronie Sprawdź ustawienia.

6. Aby rozpocząć przywracanie, wybierz pozycję **Zakończ**. Wymagane jest ponowne uruchomienie.

### <a name="restore-system-state"></a>Przywróć stan systemu

Uruchom odzyskiwanie na serwerze kopii zapasowej:

1. W okienku **odzyskiwanie** Znajdź komputer, który chcesz odzyskać, a następnie wybierz pozycję **odzyskiwanie systemu od zera**.

2. Dostępne punkty odzyskiwania są pogrubione w kalendarzu. Wybierz datę i godzinę dla punktu odzyskiwania, którego chcesz użyć.

3. Na stronie **Wybierz typ odzyskiwania** wybierz opcję **Kopiuj do folderu sieciowego**.

4. Na stronie **określ miejsce docelowe** wybierz miejsce, do którego chcesz skopiować dane. Należy pamiętać, że wybrane miejsce docelowe wymaga wystarczającej ilości miejsca. Zalecamy utworzenie nowego folderu.

5. Na stronie **Określanie opcji odzyskiwania** wybierz ustawienia zabezpieczeń, które mają zostać zastosowane. Następnie wybierz, czy chcesz szybciej odzyskiwać dane przy użyciu migawek sprzętowych opartych na sieci SAN. (Jest to opcja dostępna tylko wtedy, gdy masz sieć SAN z tą funkcją i można utworzyć i podzielić klon, aby umożliwić jego zapisywanie. Ponadto komputer chroniony i serwer zapasowy muszą być połączone z tą samą siecią.

6. Skonfiguruj opcje powiadomień. Na stronie **potwierdzenie** wybierz pozycję **Odzyskaj**.

Uruchom Kopia zapasowa systemu Windows Server:

1. Wybierz **akcje** > **Odzyskaj** > **ten serwer** > **dalej**.

2. Wybierz **inny serwer**, wybierz stronę **Określ typ lokalizacji** , a następnie wybierz pozycję **zdalny folder udostępniony**. Wprowadź ścieżkę do folderu, który zawiera punkt odzyskiwania.

3. Na stronie **Wybieranie typu odzyskiwania** wybierz pozycję **stan systemu**.

4. Na stronie **Wybieranie lokalizacji dla odzyskiwania stanu systemu** wybierz opcję **Oryginalna lokalizacja**.

5. Na stronie **potwierdzenie** wybierz pozycję **Odzyskaj**. Po przywróceniu należy ponownie uruchomić serwer.

6. Można również uruchomić przywracanie stanu systemu w wierszu polecenia. W tym celu należy uruchomić Kopia zapasowa systemu Windows Server na komputerze, który ma zostać odzyskany. Aby uzyskać wersję identyfikator, w wierszu polecenia wpisz: ```wbadmin get versions -backuptarget \<servername\sharename\>```

    Użyj identyfikatora wersji, aby uruchomić przywracanie stanu systemu. W wierszu polecenia wpisz: ```wbadmin start systemstaterecovery -version:<versionidentified> -backuptarget:<servername\sharename>```

    Potwierdź, że chcesz rozpocząć odzyskiwanie. Proces można zobaczyć w oknie wiersza polecenia. Tworzony jest dziennik przywracania. Po przywróceniu należy ponownie uruchomić serwer.
