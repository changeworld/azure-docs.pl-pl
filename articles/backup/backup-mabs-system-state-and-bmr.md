---
title: Azure serwera kopii zapasowej stanu systemu chroni i przywraca od zera
description: Serwer kopii zapasowej Azure umożliwia tworzenie kopii zapasowej stanu systemu i zapewnić ochronę odzyskiwania kompletnego stanu systemu od zera (BMR).
services: backup
author: markgalioto
manager: carmonm
keywords: ''
ms.service: backup
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: markgal
ms.openlocfilehash: d35f8667cb1ca9a0b3abd08450ebc647d6d12276
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34607212"
---
# <a name="back-up-system-state-and-restore-to-bare-metal-with-azure-backup-server"></a>Wykonaj kopię zapasową stanu systemu i przywrócić serwer kopii zapasowej Azure od zera

Serwer kopii zapasowej systemu Azure wykonuje kopię zapasową stanu systemu i zapewnia ochronę odzyskiwania zera (BMR).

*   **Kopia zapasowa stanu systemu**: tworzy kopie zapasowe plików systemu operacyjnego, więc można odzyskać, gdy komputer jest uruchamiany, ale system plików i rejestru zostaną utracone. Kopię zapasową stanu systemu zawiera:
    * Element członkowski domeny: rozruch plików, COM + baza danych rejestracji klasy, rejestru
    * Kontroler domeny: rozruchu systemu Windows Server Active Directory (NTDS), plików, COM + baza danych rejestracji klasy, rejestru, wolumin systemowy (SYSVOL)
    * Komputer z uruchomioną usług klastrowania: metadanych serwera klastra
    * Komputer z uruchomioną usług certyfikatów: certyfikat danych
* **Kopia zapasowa bez systemu operacyjnego**: tworzy kopie zapasowe plików systemu operacyjnego i wszystkich danych na woluminach krytycznych (z wyjątkiem danych użytkownika). Zgodnie z definicją kopia zapasowa BMR obejmuje kopię zapasową stanu systemu. Jeśli komputer nie uruchamia się i konieczne jest odzyskanie wszystkich, zapewnia ochronę.

Poniższa tabela zawiera podsumowanie, jakie można utworzyć kopię zapasową i odzyskiwanie. Aby uzyskać szczegółowe informacje o wersji aplikacji, które mogą być chronione przy użyciu stanu systemu i BMR, zobacz [czego serwer kopii zapasowej Azure Utwórz kopię zapasową?](backup-mabs-protection-matrix.md).

|Backup|Problem|Odzyskiwanie z kopii zapasowej serwera kopii zapasowej Azure|Odzyskiwanie z kopii zapasowej stanu systemu|ODZYSKIWANIA SYSTEMU OD ZERA|
|----------|---------|---------------------------|------------------------------------|-------|
|**Plik danych**<br /><br />Regularnego tworzenia kopii zapasowych<br /><br />Kopii zapasowej BMR/system stanu|Utraconych danych|Tak|Nie|Nie|
|**Plik danych**<br /><br />Kopia zapasowa Azure serwera kopii zapasowej danych plików<br /><br />Kopii zapasowej BMR/system stanu|Utraconego lub uszkodzonego systemu operacyjnego|Nie|Tak|Tak|
|**Plik danych**<br /><br />Kopia zapasowa Azure serwera kopii zapasowej danych plików<br /><br />Kopii zapasowej BMR/system stanu|Utracony serwer (nieuszkodzone woluminy danych)|Nie|Nie|Tak|
|**Plik danych**<br /><br />Kopia zapasowa Azure serwera kopii zapasowej danych plików<br /><br />Kopii zapasowej BMR/system stanu|Utracony serwer (utracone woluminy danych)|Tak|Nie|Tak (BMR, następuje zwykłe odzyskiwanie kopii zapasowej pliku danych)|
|**Dane programu SharePoint**:<br /><br />Utwórz kopię zapasową serwera kopii zapasowej systemu Azure danych farmy<br /><br />Kopii zapasowej BMR/system stanu|Utracone witryny, listy, elementy listy, dokumentów|Tak|Nie|Nie|
|**Dane programu SharePoint**:<br /><br />Utwórz kopię zapasową serwera kopii zapasowej systemu Azure danych farmy<br /><br />Kopii zapasowej BMR/system stanu|Utraconego lub uszkodzonego systemu operacyjnego|Nie|Tak|Tak|
|**Dane programu SharePoint**:<br /><br />Utwórz kopię zapasową serwera kopii zapasowej systemu Azure danych farmy<br /><br />Kopii zapasowej BMR/system stanu|Odzyskiwanie po awarii|Nie|Nie|Nie|
|Windows Server 2012 R2 Hyper-V<br /><br />Utwórz kopię zapasową serwera kopii zapasowej systemu Azure hosta funkcji Hyper-V lub gościa<br /><br />Kopia zapasowa stanu BMR/systemu hosta|Utracone maszyny Wirtualnej|Tak|Nie|Nie|
|Funkcja Hyper-V<br /><br />Utwórz kopię zapasową serwera kopii zapasowej systemu Azure hosta funkcji Hyper-V lub gościa<br /><br />Kopia zapasowa stanu BMR/systemu hosta|Utraconego lub uszkodzonego systemu operacyjnego|Nie|Tak|Tak|
|Funkcja Hyper-V<br /><br />Utwórz kopię zapasową serwera kopii zapasowej systemu Azure hosta funkcji Hyper-V lub gościa<br /><br />Kopia zapasowa stanu BMR/systemu hosta|Utracone hosta funkcji Hyper-V (nieuszkodzone VMs)|Nie|Nie|Tak|
|Funkcja Hyper-V<br /><br />Utwórz kopię zapasową serwera kopii zapasowej systemu Azure hosta funkcji Hyper-V lub gościa<br /><br />Kopia zapasowa stanu BMR/systemu hosta|Utracone hosta funkcji Hyper-V (utracone maszyny wirtualne)|Nie|Nie|Tak<br /><br />Odzyskiwania systemu od ZERA, następuje zwykłe odzyskiwanie serwera usługi Kopia zapasowa Azure|
|Wymiana serwer SQL<br /><br />Utwórz kopię zapasową serwera aplikacji usługa Kopia zapasowa Azure<br /><br />Kopii zapasowej BMR/system stanu|Dane aplikacji utracone|Tak|Nie|Nie|
|Wymiana serwer SQL<br /><br />Utwórz kopię zapasową serwera aplikacji usługa Kopia zapasowa Azure<br /><br />Kopii zapasowej BMR/system stanu|Utraconego lub uszkodzonego systemu operacyjnego|Nie|Y|Tak|
|Wymiana serwer SQL<br /><br />Utwórz kopię zapasową serwera aplikacji usługa Kopia zapasowa Azure<br /><br />Kopii zapasowej BMR/system stanu|Utracony serwer (nieuszkodzone dzienniki transakcji/bazy danych)|Nie|Nie|Tak|
|Wymiana serwer SQL<br /><br />Utwórz kopię zapasową serwera aplikacji usługa Kopia zapasowa Azure<br /><br />Kopii zapasowej BMR/system stanu|Utracony serwer (utracone dzienniki transakcji/bazy danych)|Nie|Nie|Tak<br /><br />Odzyskiwania systemu od ZERA, następuje zwykłe odzyskiwanie serwera usługi Kopia zapasowa Azure|

## <a name="how-system-state-backup-works"></a>Jak działa kopii zapasowej stanu systemu

Po uruchomieniu kopii zapasowej stanu systemu, Utwórz kopię zapasową serwera komunikuje się z kopia zapasowa systemu Windows Server na żądanie kopii zapasowej stanu systemu serwera. Utwórz kopię zapasową serwera i kopia zapasowa systemu Windows Server domyślnie używają dysku, który ma najwięcej wolnego miejsca. Informacje o dysku są zapisywane w plik PSDataSourceConfig.xml. Jest to dysk, którego kopia zapasowa systemu Windows Server używa kopii zapasowych.

Można wybrać dysk, który korzysta z tworzenia kopii zapasowej serwera kopii zapasowej stanu systemu. Na serwerze chronionym przejdź do C:\Program Files\Microsoft danych ochrony Manager\MABS\Datasources. Otwórz plik PSDataSourceConfig.xml do edycji. Zmień \<FilesToProtect\> wartość na literę dysku. Zapisz i zamknij plik. W przypadku zestawu grup ochrony, aby chronić stan systemu komputera, uruchom sprawdzanie spójności. Jeśli alert jest generowany, wybierz **Modyfikuj grupę ochrony** w alercie, a następnie Zakończ pracę kreatora. Następnie uruchom kolejną kontrolę spójności.

Należy pamiętać, że jeśli serwer ochrony znajduje się w klastrze, możliwe, że dysk klastra zostanie wybrany jako dysk z największą ilością wolnego miejsca. Jeśli tego własności dysku została przełączona na inny węzeł i kopii zapasowej stanu systemu, dysku nie jest dostępna i tworzenia kopii zapasowej nie powiedzie się. W tym scenariuszu należy zmodyfikować PSDataSourceConfig.xml, aby wskazywał dysk lokalny.

Następnie kopia zapasowa systemu Windows Server tworzy folder o nazwie WindowsImageBackup w katalogu głównym folderu przywracania. Jako kopia zapasowa systemu Windows Server tworzy kopię zapasową wszystkich danych znajduje się w tym folderze. Po zakończeniu operacji tworzenia kopii zapasowej plik jest przekazywany do tworzenia kopii zapasowej serwera. Zanotuj następujące informacje:

* Ten folder i jego zawartość nie wyczyszczono po zakończeniu operacji transferu lub kopii zapasowej. Najlepszym sposobem jest to, że miejsce pozostaje zarezerwowane dla następnej kopii zapasowej zostało zakończone.
* Folder jest tworzony za każdym razem, gdy kopia zapasowa ma zostać. Datą i godziną wskazuje godzinę wykonania ostatniej kopii zapasowej stanu systemu.

## <a name="bmr-backup"></a>Kopii zapasowej BMR

W przypadku odzyskiwania systemu od ZERA (łącznie z kopii zapasowej stanu systemu) zadanie tworzenia kopii zapasowej jest zapisywany bezpośrednio do udziału na komputerze serwera kopii zapasowej. Nie są zapisywane w folderze w na serwerze chronionym.

Utwórz kopię zapasową serwera wywołuje kopia zapasowa systemu Windows Server i udostępnia wolumin repliki dla kopii zapasowej BMR. W takim przypadku program nie nakazuje narzędzia Kopia zapasowa systemu Windows Server do użycia z największą ilością wolnego miejsca na dysku. Zamiast tego używa udziału, który został utworzony dla zadania.

Po zakończeniu operacji tworzenia kopii zapasowej plik jest przekazywany do tworzenia kopii zapasowej serwera. Dzienniki są przechowywane w C:\Windows\Logs\WindowsServerBackup.

## <a name="prerequisites-and-limitations"></a>Wymagania wstępne i ograniczenia

-   Odzyskiwania systemu od ZERA nie jest obsługiwana dla komputerów z systemem Windows Server 2003 lub na komputerach z systemem operacyjnym klienta.

-   Nie można włączyć ochrony BMR i stanu systemu dla komputera znajdującego się w różnych grupach ochrony.

-   Komputer Utwórz kopię zapasową serwera nie może chronić siebie samego przy odzyskiwania systemu od ZERA.

-   Krótkoterminowa ochrona na taśmie (dysk taśma lub D2T) nie jest obsługiwana dla odzyskiwania systemu od ZERA. Magazynowania długoterminowego na taśmie (dysk do dysk taśma lub D2D2T) jest obsługiwana.

-   W przypadku ochrony BMR kopia zapasowa systemu Windows Server musi być zainstalowany na komputerze chronionym.

-   W przypadku ochrony BMR w przeciwieństwie do ochrony stanu systemu, Utwórz kopię zapasową serwera nie ma żadnych wymagań dotyczących miejsca na chronionym komputerze. Kopia zapasowa systemu Windows Server bezpośrednio przesyła kopie zapasowe z komputerem serwera kopii zapasowej. Zadanie tworzenia kopii zapasowej transferu nie jest wyświetlane w kopii zapasowej serwera **zadania** widoku.

-   Utwórz kopię zapasową serwera rezerwuje 30 GB miejsca na woluminie repliki odzyskiwania systemu od zera. Można to zmienić na **przydział dysku** stronie kreatora modyfikacji grupy ochrony lub przy użyciu polecenia cmdlet Get-DatasourceDiskAllocation i Set-DatasourceDiskAllocation w programie PowerShell. Na woluminie punktu odzyskiwania ochrona BMR wymaga około 6 GB do przechowywania danych przez pięć dni.
    * Należy pamiętać, że nie można zmniejszyć rozmiar woluminu repliki do mniej niż 15 GB.
    * Utwórz kopię zapasową serwera nie oblicza rozmiaru źródła danych BMR. Zakłada się 30 GB dla wszystkich serwerów. Zmień wartość na podstawie rozmiaru kopii zapasowych BMR, które mogą pojawić się w danym środowisku. Rozmiar kopii zapasowej BMR może być około obliczona jako suma miejsca używanego przez wszystkie woluminy krytyczne. Woluminy krytyczne = wolumin rozruchowy + wolumin systemowy + wolumin obsługujący dane o stanie systemu, takie jak Active Directory.

-   W przypadku zmiany z ochrony stanu systemu na ochronę BMR ochrona BMR wymaga mniej miejsca na *woluminu punktu odzyskiwania*. Nie jest jednak odzyskać dodatkowe miejsce na woluminie. Można ręcznie zmniejszyć rozmiar woluminu na **modyfikowanie przydziału dysku** strony kreatora modyfikacji grupy ochrony lub przy użyciu polecenia cmdlet Get-DatasourceDiskAllocation i Set-DatasourceDiskAllocation w programie PowerShell.

    W przypadku zmiany z ochrony stanu systemu na ochronę BMR ochrona BMR wymaga więcej miejsca na *woluminu repliki*. Wolumin zostanie rozszerzony automatycznie. Jeśli chcesz zmienić domyślne alokacje miejsca, użyj polecenia cmdlet programu PowerShell Modify-DiskAllocation.

-   Jeśli zmienisz z ochrony BMR na ochronę stanu systemu należy więcej miejsca na woluminie punktu odzyskiwania. Utwórz kopię zapasową serwera może podjąć próbę automatycznego zwiększenia woluminu. Jeśli w puli magazynów jest za mało miejsca, występuje błąd.

    Jeśli zmienisz z ochrony BMR na ochronę stanu systemu należy miejsca na chronionym komputerze. Jest tak, ponieważ Ochrona stanu systemu najpierw zapisuje replikę na komputerze lokalnym, a następnie przekazuje ją do serwera kopii zapasowej.

## <a name="before-you-begin"></a>Przed rozpoczęciem

1.  **Wdrażanie serwera kopii zapasowej Azure**. Upewnij się, że Utwórz kopię zapasową serwera jest prawidłowo wdrożony. Aby uzyskać więcej informacji, zobacz:
    * [Wymagania systemowe dla serwera usługi Kopia zapasowa Azure](http://docs.microsoft.com/system-center/dpm/install-dpm#setup-prerequisites)
    * [Kopii zapasowej macierzy ochrony serwera](backup-mabs-protection-matrix.md)

2.  **Konfigurowanie magazynu**. Można przechowywać dane kopii zapasowej na dysku, na taśmie i w chmurze platformy Azure. Aby uzyskać więcej informacji, zobacz [Przygotuj magazyn danych](https://docs.microsoft.com/system-center/dpm/plan-long-and-short-term-data-storage).

3.  **Skonfiguruj agenta ochrony**. Zainstaluj agenta ochrony na komputerze, na którym chcesz utworzyć kopię zapasową. Aby uzyskać więcej informacji, zobacz [wdrożyć agenta ochrony programu DPM](http://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent).

## <a name="back-up-system-state-and-bare-metal"></a>Wykonaj kopię zapasową stanu systemu i od zera
Konfigurowanie grupy ochrony zgodnie z opisem w [wdrażanie grup ochrony](http://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups). Należy pamiętać, że nie można włączyć ochrony BMR i stanu systemu dla komputera znajdującego się w różnych grupach. Po wybraniu funkcji BMR, stan systemu jest automatycznie włączone.


1.  Aby otworzyć Kreatora tworzenia nowej grupy ochrony w konsoli administratora serwera kopii zapasowej, wybierz **ochrony** > **akcje** > **tworzenia grupy ochrony** .

2.  Na **wybierz typ grupy ochrony** wybierz **serwerów**, a następnie wybierz **dalej**.

3.  Na **Wybierz członków grupy** strony, rozwiń węzeł komputera, a następnie wybierz opcję **BMR** lub **stanu systemu**.

    Należy pamiętać, że nie można chronić zarówno BMR i stanu systemu dla komputera znajdującego się w różnych grupach. Po wybraniu funkcji BMR, stan systemu jest automatycznie włączone. Aby uzyskać więcej informacji, zobacz [wdrażanie grup ochrony](http://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups).

4.  Na **wybierz metodę ochrony danych** wybierz jak chcesz obsługiwać krótko- i długoterminowej kopii zapasowej. Tworzenie krótkoterminowej kopii zapasowej jest zawsze najpierw na dysku, z możliwością tworzenia kopii zapasowej z dysku platformy Azure w chmurze za pomocą usługi Kopia zapasowa Azure (krótkoterminowej lub długoterminowej). Zamiast długoterminowej kopii zapasowej w chmurze jest ustawienie długoterminowej kopii zapasowej do autonomicznej taśmy lub urządzenia biblioteki taśm podłączoną do tworzenia kopii zapasowej serwera.

5.  Na **Wybierz cele krótkoterminowe** wybierz jak chcesz utworzyć kopię zapasową do przechowywania krótkoterminowego na dysku:
    1. Aby uzyskać **zakres przechowywania**, wybierz, jak długo mają być przechowywanie danych na dysku. 
    2. Aby uzyskać **częstotliwość synchronizacji**, wybierz, jak często chcesz uruchomić przyrostowej kopii zapasowej na dysku. Jeśli nie chcesz ustawić interwał wykonywania kopii zapasowej, można sprawdzić **zaraz przed punktem odzyskiwania** opcji. Utwórz kopię zapasową serwera zostanie uruchomiony ekspresowej pełnej kopii zapasowej, tuż przed każdym punktu odzyskiwania zaplanowano.

6.  Jeśli chcesz przechowywać dane na taśmę do długoterminowego przechowywania w **Określ cele długoterminowe** wybierz, jak długo mają być zachowane dane taśmy (1 – 99 lat). 
    1. Aby uzyskać **częstotliwość wykonywania kopii zapasowych**, wybierz częstotliwość tworzenia kopii zapasowej na taśmie powinno być ono uruchomione. Częstotliwość opiera się na zakres przechowywania, wybrane:
        * Gdy zakres przechowywania wynosi 1 – 99 lat, można wybrać tworzenie kopii zapasowej codziennie, co tydzień, co dwa tygodnie, co miesiąc, co kwartał, co pół roku lub co rok.
        * Gdy zakres przechowywania wynosi 1-11 miesięcy, można wybrać tworzenie kopii zapasowej codziennie, co tydzień, co dwa tygodnie lub co miesiąc.
        * Gdy zakres przechowywania wynosi 1 – 4 tygodnie, można wybrać tworzenie kopii zapasowej codziennie lub co tydzień.

    2. Na **szczegółów zaznacz taśmy i biblioteki** strony, wybierz taśmę i bibliotekę do użycia, i czy skompresowane i zaszyfrowanych danych.

7.  Na **Przejrzyj przydział dysku** Przejrzyj miejsce dysku w puli magazynu przypisany do grupy ochrony.

    1. **Całkowity rozmiar danych** rozmiar danych, aby utworzyć kopię zapasową.
    2. **Miejsca na dysku do udostępniane na serwerze kopii zapasowej Azure** miejsca, Utwórz kopię zapasową serwera zalecane dla grupy ochrony. Utwórz kopię zapasową serwera wybiera idealne wolumin kopii zapasowej na podstawie ustawień. Jednak możesz edytować opcje tworzenia kopii zapasowej woluminu w **szczegóły alokacji dysku**. 
    3. W przypadku obciążeń w menu rozwijanego wybierz preferowany magazynu. Zmiany wprowadzone zmiany wartości **całkowita ilość miejsca** i **wolnego miejsca w magazynie** w **dostępny Magazyn dyskowy** okienka. Ilość miejsca w magazynie sugerujące, Utwórz kopię zapasową serwera można dodać do woluminu w celu zapewnienia sprawnego przebiegu tworzenia kopii zapasowych jest underprovisioned miejsca.

8.  Na **wybierz metodę tworzenia repliki** wybierz sposób obsługi replikacji początkowej danych. Jeśli chcesz replikować za pośrednictwem sieci, zaleca się wybranie poza godzinami szczytu. W przypadku dużych ilości danych lub warunków sieciowych, które są optymalne należy wziąć pod uwagę replikację danych w trybie offline przy użyciu nośnika wymiennego.

9. Na **wybierz opcje sprawdzania spójności** wybierz sposób automatyzacji sprawdzania spójności. Można sprawdzić tylko wtedy, gdy dane repliki staje się niespójna, lub zgodnie z harmonogramem. Jeśli nie chcesz konfigurować automatycznego sprawdzania spójności, można uruchomić sprawdzanie ręczne w dowolnym momencie. Aby uruchomić ręczne sprawdzanie, w **ochrony** konsoli administratora serwera kopii zapasowej, kliknij prawym przyciskiem myszy grupę ochrony, a następnie wybierz **Przeprowadź Sprawdzanie spójności**.

10. Jeśli wybrano, aby utworzyć kopię zapasową do chmury przy użyciu usługi Kopia zapasowa Azure, na **Określ dane chronione Online** strony, upewnij się, że wybrana obciążeń, aby utworzyć kopię zapasową na platformie Azure.

11. Na **Określanie harmonogramu tworzenia kopii zapasowej Online** strony, nastąpi wybierz częstotliwość przyrostowych kopii zapasowych na platformie Azure. Można zaplanować tworzenie kopii zapasowych do uruchamiania każdego dnia, tygodnia, miesiąca i roku i wybierz godzinę i datę, po których powinno być ono uruchomione. Tworzenie kopii zapasowych może wystąpić maksymalnie dwa razy dziennie. Każdym uruchomieniu kopii zapasowej danych punkt odzyskiwania jest tworzony na platformie Azure z kopii kopii zapasowej danych przechowywanych na dysku serwera kopii zapasowej.

12. Na **Określanie zasad przechowywania Online** wybierz, jak punkty odzyskiwania, które są tworzone z kopii zapasowej codziennie, co tydzień, miesięcznych i rocznych są przechowywane na platformie Azure.

13. Na **wybierz replikacji Online** wybierz sposób następuje pełna Replikacja początkowa danych. Można replikować za pośrednictwem sieci lub wykonaj w trybie offline kopii zapasowej (obsługi w trybie offline). Kopia zapasowa offline korzysta z funkcji importu platformy Azure. Aby uzyskać więcej informacji, zobacz [w trybie Offline kopii zapasowej przepływu pracy w usłudze Kopia zapasowa Azure](backup-azure-backup-import-export.md).

14. Na **Podsumowanie** Przejrzyj ustawienia. Po wybraniu **Utwórz grupę**, następuje Replikacja początkowa danych. Podczas replikacji danych zakończeniu na **stan** strona, stan grupy ochrony jest **OK**. Kopia zapasowa następnie odbywa się na ochronę grupy ustawień.

## <a name="recover-system-state-or-bmr"></a>Odzyskiwanie stanu systemu lub odzyskiwania systemu od ZERA
Może odzyskać stanu systemu lub odzyskiwania systemu od ZERA do lokalizacji sieciowej. Jeśli utworzono kopię zapasową BMR, użyj środowiska odzyskiwania systemu Windows (WinRE) do uruchamiania systemu i podłącz go do sieci. Następnie za pomocą narzędzia Kopia zapasowa systemu Windows Server do odzyskania z lokalizacji sieciowej. Jeśli utworzono kopię zapasową stanu systemu, po prostu użyć narzędzia Kopia zapasowa systemu Windows Server do odzyskania z lokalizacji sieciowej.

### <a name="restore-bmr"></a>Przywróć odzyskiwania systemu od ZERA
Wykonaj operację odzyskiwania na komputerze serwera kopii zapasowej:

1.  W **odzyskiwania** okienka, Znajdź komputer ma zostać odzyskana, a następnie wybierz **odzyskiwaniu bez systemu operacyjnego**.

2.  Dostępne punkty odzyskiwania są pogrubione w kalendarzu. Wybierz datę i godzinę dla punktu odzyskiwania, którego chcesz używać.

3.  Na **Wybieranie typu odzyskiwania** wybierz **Kopiuj do folderu sieciowego.**

4.  Na **określ miejsce docelowe** wybierz, gdzie chcesz skopiować dane. Należy pamiętać, że wybrane miejsce docelowe musi mieć wystarczająco dużo miejsca. Firma Microsoft zaleca, aby utworzyć nowy folder.

5.  Na **Określanie opcji odzyskiwania** wybierz ustawienia zabezpieczeń, które mają być stosowane. Następnie wybierz, czy chcesz użyć sieci magazynowania (SAN) — na podstawie migawki sprzętowe szybciej odzyskiwać dane. (Jest to opcja tylko wtedy, gdy sieć SAN z tej funkcji, które są dostępne i możliwość tworzenia i podziału klon do zapisu. Ponadto na chronionym komputerze i na komputerze utwórz kopię zapasową serwera musi być podłączony do tej samej sieci.)

6.  Skonfiguruj opcje powiadamiania. Na **potwierdzenie** wybierz pozycję **odzyskać**.

Skonfiguruj lokalizację udziału:

1.  W lokalizacji przywracania przejdź do folderu zawierającego kopię zapasową.

2.  Udostępnij folder o jeden poziom wyżej WindowsImageBackup, tak aby głównym folderu udostępnionego jest WindowsImageBackup folder. Jeśli nie zrobisz, przywracania nie odnajdzie kopii zapasowej. Aby połączyć przy użyciu środowiska odzyskiwania systemu Windows (WinRE), należy udziału, w której będziesz mieć dostęp ze środowiska WinRE poprawny adres IP i poświadczeń.

Przywracanie systemu:

1.  Uruchom komputer, na którym chcesz przywrócić obrazu przy użyciu dysku DVD systemu Windows dla systemu, w którym jest przywracana.

2.  Na pierwszej stronie sprawdź ustawienia języka i ustawienia regionalne. Na **zainstalować** wybierz pozycję **Napraw komputer**.

3.  Na **opcje odzyskiwania systemu** wybierz pozycję **Przywróć komputer przy użyciu utworzonego wcześniej obrazu systemu**.

4.  Na **Wybierz kopię zapasową obrazu systemu** wybierz **wybierz obraz systemu** > **zaawansowane** > **Wyszukaj obraz systemu w sieci**. Jeśli zostanie wyświetlone ostrzeżenie, wybierz **tak**. Przejdź do ścieżki udziału, wprowadź poświadczenia, a następnie wybierz punkt odzyskiwania. To skanowanie w poszukiwaniu określonych kopii zapasowych, które są dostępne w tym punkcie odzyskiwania. Wybierz punkt odzyskiwania, którego chcesz używać.

5.  Na **wybierz sposób przywracania kopii zapasowej** wybierz pozycję **Format i ponownie podziel na partycje dysków**. Sprawdź ustawienia na następnej stronie. 

6.  Aby rozpocząć przywracanie, wybierz **Zakończ**. Wymagane jest ponowne uruchomienie.

### <a name="restore-system-state"></a>Przywracanie stanu systemu

Uruchom odzyskiwanie z kopii zapasowej serwera:

1.  W **odzyskiwania** okienka, Znajdź komputer, który chcesz odzyskać, a następnie wybierz **odzyskiwaniu bez systemu operacyjnego**.

2.  Dostępne punkty odzyskiwania są pogrubione w kalendarzu. Wybierz datę i godzinę dla punktu odzyskiwania, którego chcesz używać.

3.  Na **Wybieranie typu odzyskiwania** wybierz **Kopiuj do folderu sieciowego**.

4.  Na **określ miejsce docelowe** wybierz, które chcesz skopiować dane. Należy pamiętać, że zaznaczone miejsce docelowe musi wystarczająco dużo miejsca. Firma Microsoft zaleca, aby utworzyć nowy folder.

5.  Na **Określanie opcji odzyskiwania** wybierz ustawienia zabezpieczeń, które mają być stosowane. Następnie wybierz opcję szybciej odzyskiwać dane przy użyciu migawek sprzętowych opartych na sieci SAN. (Jest to opcja tylko wtedy, gdy sieć SAN z tej funkcji i możliwości tworzenia i podziału jako możliwego do zapisu. Ponadto komputera chronionego i serwera kopii zapasowej serwera muszą być połączone z tą samą siecią.)

6.  Skonfiguruj opcje powiadamiania. Na **potwierdzenie** wybierz pozycję **odzyskać**.

Uruchom narzędzie Kopia zapasowa systemu Windows Server:

1.  Wybierz **akcje** > **odzyskać** > **tego serwera** > **dalej**.

2.  Wybierz **innego serwera**, wybierz pozycję **Określ typ lokalizacji** , a następnie wybierz **zdalnego folderu udostępnionego**. Wprowadź ścieżkę do folderu, który zawiera punkt odzyskiwania.

3.  Na **Wybieranie typu odzyskiwania** wybierz **stanu systemu**. 

4. Na **Wybieranie lokalizacji dla odzyskiwania stanu systemu** wybierz **oryginalnej lokalizacji**.

5.  Na **potwierdzenie** wybierz pozycję **odzyskać**. Po przywróceniu Uruchom ponownie serwer.

6.  Przywracanie stanu systemu można również uruchomić polecenie w wierszu polecenia. Aby to zrobić, należy uruchomić narzędzie Kopia zapasowa systemu Windows Server na komputerze, który chcesz odzyskać. Aby uzyskać identyfikator wersji, w wierszu polecenia, wpisz: ```wbadmin get versions -backuptarget \<servername\sharename\>```

    Użyj identyfikatora wersji, aby uruchomić Przywracanie stanu systemu. W wierszu polecenia wpisz: ```wbadmin start systemstaterecovery -version:<versionidentified> -backuptarget:<servername\sharename>```

    Upewnij się, że chcesz rozpocząć odzyskiwanie. Możesz śledzić proces w oknie wiersza polecenia. Tworzony jest dziennik przywracania. Po przywróceniu Uruchom ponownie serwer.

