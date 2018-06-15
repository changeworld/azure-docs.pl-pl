---
title: Z serii StorSimple 8000 jako miejsce docelowe kopii zapasowej z NetBackup | Dokumentacja firmy Microsoft
description: Zawiera opis konfiguracji miejsce docelowe kopii zapasowej StorSimple o Veritas NetBackup.
services: storsimple
documentationcenter: ''
author: harshakirank
manager: matd
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/15/2017
ms.author: hkanna
ms.openlocfilehash: b1878c181a77ac6d54654fc55228907743243c45
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
ms.locfileid: "23877608"
---
# <a name="storsimple-as-a-backup-target-with-netbackup"></a>StorSimple jako miejsce docelowe kopii zapasowej z NetBackup

## <a name="overview"></a>Przegląd

Azure StorSimple jest rozwiązania magazynu hybrydowego chmury firmy Microsoft. StorSimple adresy złożoności przyrostu wykładniczej danych za pomocą konta magazynu platformy Azure jako rozszerzenia rozwiązania lokalnego i automatycznie warstwy danych przez Magazyn lokalny i magazynu w chmurze.

W tym artykule omówiono StorSimple integracji z Veritas NetBackup i najlepsze rozwiązania dotyczące integrowania oba rozwiązania. Możemy również zaleceń w sposobie ustawiania Veritas NetBackup najlepiej jest integrowana z StorSimple. Firma Microsoft odroczenie Veritas najlepsze rozwiązania w zakresie tworzenia kopii zapasowej dla architektów i administratorów najlepszy sposób, aby skonfigurować Veritas NetBackup w celu spełnienia poszczególnych wymagań kopii zapasowej i umów dotyczących poziomu usług (SLA).

Mimo że firma Microsoft zilustrować czynności konfiguracyjne i podstawowe pojęcia, przewodnik krok po kroku konfiguracji lub instalacja jest w żadnym wypadku w tym artykule. Przyjęto założenie, czy podstawowe składniki i infrastruktury są w stanie i gotowe do obsługi opisano założenia.

### <a name="who-should-read-this"></a>Kto powinien przeczytać to?

Informacje przedstawione w tym artykule będzie najbardziej przydatne dla administratorów kopii zapasowych, magazynu administratorów i architektów magazynu, którzy korzystają z magazynu systemu Windows Server 2012 R2, Ethernet, usługi w chmurze i Veritas NetBackup.

### <a name="supported-versions"></a>Obsługiwane wersje

-   NetBackup 7.7.x i nowszymi wersjami
-   [StorSimple Update 3 i nowszych wersji](storsimple-overview.md#storsimple-workload-summary)


## <a name="why-storsimple-as-a-backup-target"></a>Dlaczego StorSimple jako miejsce docelowe kopii zapasowej?

StorSimple jest dobrym rozwiązaniem w przypadku miejsce docelowe kopii zapasowej, ponieważ:

-   Zawiera standardowe i lokalnego magazynu dla kopii zapasowej aplikacji do użycia jako protokół fast miejsce docelowe kopii zapasowej, bez wprowadzania żadnych zmian. Można też użyć StorSimple do szybkiego przywrócenia ostatnie kopii zapasowych.
-   Chmura jego obsługa poziomów jest w pełni zintegrowana z konta magazynu w chmurze Azure korzystania z ekonomicznych magazynu Azure.
-   Udostępnia automatycznie przechowywania poza siedzibą odzyskiwania po awarii.

## <a name="key-concepts"></a>Kluczowe pojęcia

Podobnie jak w przypadku dowolnego pamięci masowej, zachować ostrożność podczas oceny wydajności magazynu rozwiązania, umów SLA, szybkość zmian i wymagań wzrost wydajności jest krytyczne znaczenie dla sukcesu. Główny pomysł jest dzięki zastosowaniu warstwy chmury, godziny dostępu, a przepustowość do chmury play podstawowych roli w StorSimple możliwość jego pracy.

StorSimple zaprojektowano w celu zapewnienia magazynu dla aplikacji działających na dobrze zdefiniowany zestaw roboczy danych (gorących danych). W tym modelu zestaw roboczy danych są przechowywane w warstwach lokalnych, a pozostałe zestaw wolny/chłodni/archiwizowane danych jest warstwy do chmury. Ten model jest reprezentowany na poniższej ilustracji. Niemal płaskiej zielony wiersz reprezentuje dane przechowywane w warstwach lokalnego urządzenia StorSimple. Czerwona linia reprezentuje łączną ilość danych przechowywanych w rozwiązaniu StorSimple przez wszystkie warstwy. Odstęp między płaskim zielony wiersza i krzywej wykładniczej red przedstawia łączną ilość danych przechowywanych w chmurze.

**Obsługa poziomów StorSimple**
![StorSimple diagramów warstw](./media/storsimple-configure-backup-target-using-netbackup/image1.jpg)

Taka architektura na uwadze znajdziesz czy StorSimple idealnie nadaje się do działania jako miejsce docelowe kopii zapasowej. Możesz użyć StorSimple do:
-   Wykonaj z najczęściej wykonywanych operacji przywracania z zestawu roboczego lokalnych danych.
-   Stosować usługi w chmurze odzyskiwania po awarii poza siedzibą firmy i starszych danych, gdzie są dłuższe interwały przywracania.

## <a name="storsimple-benefits"></a>Korzyści StorSimple

StorSimple zapewnia rozwiązanie lokalne, które jest w pełni zintegrowana z Microsoft Azure, dzięki wykorzystaniu bezproblemowy dostęp do lokalnych i magazynu w chmurze.

StorSimple używa automatycznego tworzenia warstw między lokalnego urządzenia, którego urządzenia półprzewodnikowych (SSD) i magistrali serial attached SCSI (SAS) magazynu, a Magazyn Azure. Automatyczne stosowanie warstw utrzymuje często używanych danych lokalnych, na warstwy dysków SSD i dysków SAS. Rzadko używane dane przesyłane do magazynu Azure.

StorSimple oferuje następujące korzyści:

-   Unikatowy algorytmy deduplikacji i kompresji, korzystających z chmury do osiągnięcia poziomów Niespotykana deduplikacji
-   Wysoka dostępność
-   Replikacja geograficzna przy użyciu platformy Azure — replikacja geograficzna
-   Integracja Azure
-   Szyfrowanie danych w chmurze
-   Ulepszonych funkcji odzyskiwania i zgodność

Chociaż StorSimple zasadniczo przedstawia dwa scenariusze wdrażania głównego (miejsce docelowe kopii zapasowej głównej i dodatkowej miejsce docelowe kopii zapasowej), jest zwykły, urządzeniem magazynu blokowego. Wszystkie kompresji jest StorSimple i deduplikacji. Bezproblemowo wysyła i pobiera dane między chmury oraz aplikacji i systemu plików.

Aby uzyskać więcej informacji o StorSimple, zobacz [z serii StorSimple 8000: rozwiązanie magazynu w chmurze hybrydowej](storsimple-overview.md). Ponadto możesz przejrzeć [techniczne serii StorSimple 8000](storsimple-technical-specifications-and-compliance.md).

> [!IMPORTANT]
> Używanie StorSimple urządzenia jako miejsce docelowe kopii zapasowej jest obsługiwane tylko w przypadku StorSimple 8000 z aktualizacją Update 3 i nowszych wersjach.

## <a name="architecture-overview"></a>Omówienie architektury

W poniższej tabeli przedstawiono wskazówki dotyczące początkowej modelu do architektury urządzeń.

**Możliwości StorSimple dla lokalnych i magazynu w chmurze**

| Pojemność magazynu       | 8100          | 8600            |
|------------------------|---------------|-----------------|
| Pojemność magazynu lokalnego | &lt; 10 TiB\*  | &lt; 20 TiB\*  |
| Pojemność magazynu w chmurze | &gt; 200 TiB\* | &gt; 500 TiB\* |
\* Rozmiar magazynu nie przyjmuje żadnych deduplikacji ani kompresji.

**StorSimple pojemności dla głównych i dodatkowych kopii zapasowych**

| Scenariusz tworzenia kopii zapasowej  | Pojemność magazynu lokalnego  | Pojemność magazynu w chmurze  |
|---|---|---|
| Podstawowa kopia zapasowa  | Ostatnie kopie zapasowe przechowywane w magazynie lokalnym na szybkie odzyskiwanie aby spełnić cel punktu odzyskiwania (RPO) | Historia kopii zapasowych (RPO), który pasuje do pojemności chmury |
| Dodatkowej kopii zapasowej | Dodatkowej kopii kopii zapasowej danych mogą być przechowywane w chmurze pojemności  | ND  |

## <a name="storsimple-as-a-primary-backup-target"></a>StorSimple jako miejsce docelowe kopii zapasowej głównej

W tym scenariuszu przedstawiono woluminów StorSimple do tworzenia kopii zapasowej aplikacji jako jedynego repozytorium dla kopii zapasowych. Na poniższej ilustracji przedstawiono architektury rozwiązania, w którym wszystkie kopie zapasowe Użyj StorSimple warstwowej woluminów na potrzeby tworzenia kopii zapasowych i przywracania.

![StorSimple jako miejsce docelowe kopii zapasowej głównej diagram logiczny](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Etapy logiczne kopii zapasowej głównej docelowej

1.  Utwórz kopię zapasową serwera kontaktuje się docelowego agenta kopii zapasowej, a agenta kopii zapasowej przesyła dane do serwera zapasowego.
2.  Utwórz kopię zapasową serwera zapisuje dane do StorSimple woluminami warstwowymi.
3.  Utwórz kopię zapasową serwera aktualizuje bazę danych katalogu, a następnie kończy zadanie tworzenia kopii zapasowej.
4.  Skrypt migawki wyzwala StorSimple snapshot manager (start lub usunięcie).
5.  Utwórz kopię zapasową serwera Usuwa wygasłe kopii zapasowych na podstawie zasad przechowywania.

### <a name="primary-target-restore-logical-steps"></a>Etapy logiczne przywracania głównej docelowej

1.  Utwórz kopię zapasową serwera rozpoczyna przywracanie odpowiednie dane z repozytorium magazynu.
2.  Agenta kopii zapasowej odbiera dane z kopii zapasowej serwera.
3.  Serwer kopii zapasowej kończy zadanie przywracania.

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple jako dodatkowej miejsce docelowe kopii zapasowej

W tym scenariuszu woluminów StorSimple głównie służą do przechowywania długoterminowego i archiwizacji.

Na poniższej ilustracji przedstawiono architekturę, w których początkowej kopii zapasowych i przywraca wolumin docelowy wysokiej wydajności. Te kopie zapasowe są kopiowane i archiwizowane w StorSimple do warstwy woluminu zgodnie z ustalonym harmonogramem.

Jest ważne rozmiar woluminu wysokiej wydajności, dzięki czemu może obsłużyć zasad przechowywania wymagań pojemność i wydajność.

![StorSimple jako miejsce docelowe kopii zapasowej dodatkowej diagram logiczny](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>Dodatkowej docelowy kopii zapasowej etapy logiczne

1.  Utwórz kopię zapasową serwera kontaktuje się docelowego agenta kopii zapasowej, a agenta kopii zapasowej przesyła dane do serwera zapasowego.
2.  Utwórz kopię zapasową serwera zapisuje dane do magazynu o wysokiej wydajności.
3.  Utwórz kopię zapasową serwera aktualizuje bazę danych katalogu, a następnie kończy zadanie tworzenia kopii zapasowej.
4.  Utwórz kopię zapasową serwera kopiuje StorSimple na podstawie zasad przechowywania kopii zapasowych.
5.  Skrypt migawki wyzwala StorSimple snapshot manager (start lub usunięcie).
6.  Utwórz kopię zapasową serwera Usuwa wygasłe na podstawie zasad przechowywania kopii zapasowych.

### <a name="secondary-target-restore-logical-steps"></a>Etapy logiczne przywracania dodatkowej docelowego

1.  Utwórz kopię zapasową serwera rozpoczyna przywracanie odpowiednie dane z repozytorium magazynu.
2.  Agenta kopii zapasowej odbiera dane z kopii zapasowej serwera.
3.  Serwer kopii zapasowej kończy zadanie przywracania.

## <a name="deploy-the-solution"></a>Wdrażanie rozwiązania

Wdrożenie to rozwiązanie wymaga trzy kroki:
1. Przygotowanie infrastruktury sieci.
2. Wdrażanie urządzenia StorSimple jako miejsce docelowe kopii zapasowej.
3. Wdróż Veritas NetBackup.

Każdy krok została omówiona szczegółowo w poniższych sekcjach.

### <a name="set-up-the-network"></a>Konfigurowanie sieci

Ponieważ StorSimple to rozwiązanie, które jest zintegrowany z chmury Azure, StorSimple wymaga aktywnych i działające połączenie w chmurze platformy Azure. To połączenie jest używane dla operacje, takie jak migawki w chmurze, zarządzanie danymi i transfer metadanych, a do warstwy starsze, mniejsza używane dane do magazynu w chmurze Azure.

Rozwiązania w celu zapewnienia optymalnego działania zaleca się, należy wykonać następujące najlepsze rozwiązania sieci:

-   Łącza, które łączy StorSimple, dodając funkcje warstw Azure musi spełniać wymagań dotyczących przepustowości. Aby to osiągnąć, należy zastosować odpowiedniego poziomu jakości usług (QoS) w infrastrukturze przełączników do dopasowania cel punktu odzyskiwania, a odzyskiwanie czasu SLA cel (RTO).

-   Maksymalna opóźnienia dostępu do magazynu obiektów Blob platformy Azure powinna być około 80 ms.

### <a name="deploy-storsimple"></a>Wdrażanie StorSimple

Aby uzyskać instrukcje wdrożenia StorSimple, zobacz [wdrażanie lokalnego urządzenia StorSimple](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-netbackup"></a>Wdrażanie NetBackup

Aby uzyskać szczegółowe instrukcje wdrożenia 7.7.x NetBackup, zobacz [dokumentacji 7.7.x NetBackup](http://www.veritas.com/docs/000094423).

## <a name="set-up-the-solution"></a>Konfigurowanie rozwiązania

W tej sekcji przedstawiony przykłady konfiguracji. Następujące przykłady i zalecenia ilustrują implementacji najbardziej podstawowa i podstawowe. Ta implementacja nie mogą być stosowane bezpośrednio do określonych wymagań kopii zapasowej.

### <a name="set-up-storsimple"></a>Konfigurowanie StorSimple

| Zadania wdrażania StorSimple  | Dodatkowe komentarze |
|---|---|
| Wdrażanie lokalnego urządzenia StorSimple. | Obsługiwane wersje: Aktualizacja 3 i jego nowsze wersje. |
| Włącz miejsce docelowe kopii zapasowej. | Należy używać tych poleceń, aby włączyć lub wyłączyć tryb miejsce docelowe kopii zapasowej i można uzyskać stanu. Aby uzyskać więcej informacji, zobacz [połączenia zdalne z urządzeniem StorSimple](storsimple-remote-connect.md).</br> Aby włączyć tryb tworzenia kopii zapasowej: `Set-HCSBackupApplianceMode -enable`. </br> Aby wyłączyć tryb tworzenia kopii zapasowej: `Set-HCSBackupApplianceMode -disable`. </br> Aby uzyskać bieżący stan ustawienia trybu tworzenia kopii zapasowej: `Get-HCSBackupApplianceMode`. |
| Utwórz kontener woluminów typowe dla woluminu, która przechowuje dane kopii zapasowej. Wszystkie dane w kontenerze wolumin jest deduplikowany. | Kontenery woluminów StorSimple Definiowanie domen deduplikacji.  |
| Utwórz woluminy StorSimple. | Utwórz woluminy o rozmiarze jak blisko oczekiwanego użycia jak to możliwe, ponieważ chmura migawki czas trwania wpływa na rozmiar woluminu. Aby uzyskać informacje dotyczące rozmiaru woluminu, przeczytaj o [zasady przechowywania](#retention-policies).</br> </br> Użyj StorSimple woluminami warstwowymi, a następnie wybierz **Użyj tego woluminu w przypadku rzadziej używanych danych archiwalnych** pole wyboru. </br> Używanie tylko przypięty lokalnie woluminów nie jest obsługiwane. |
| Utwórz unikatowy StorSimple zasady tworzenia kopii zapasowych wszystkich woluminów, miejsce docelowe kopii zapasowej. | Zasady tworzenia kopii zapasowej StorSimple definiuje grupę spójności woluminu. |
| Wyłączyć harmonogram, zgodnie z migawki wygaśnie. | Migawki są wyzwalane jako operację przetwarzania końcowego. |

### <a name="set-up-the-host-backup-server-storage"></a>Konfigurowanie magazynu kopii zapasowej serwera hosta

Konfigurowanie magazynu kopii zapasowej serwera hosta zgodnie z tymi wytycznymi:  

- Nie używaj woluminy łączone (utworzonego przez Zarządzanie dyskami systemu Windows;) woluminy łączone nie są obsługiwane.
- Formatowanie woluminów o rozmiarze 64 KB alokacji w systemie plików NTFS.
- Mapuje woluminy StorSimple bezpośrednio na serwerze NetBackup.
    - Użyj iSCSI dla serwerów fizycznych.
    - Dyski przekazujące na użytek serwerów wirtualnych.


## <a name="best-practices-for-storsimple-and-netbackup"></a>Najlepsze rozwiązania dotyczące StorSimple i NetBackup

Konfigurowanie rozwiązania zgodnie z wytycznymi w następujących sekcjach kilku.

### <a name="operating-system-best-practices"></a>Najlepsze rozwiązania w zakresie systemu operacyjnego

-   Wyłącz szyfrowanie systemu Windows Server i deduplikacji systemu plików NTFS.
-   Wyłącz defragmentacji systemu Windows Server w przypadku woluminów StorSimple.
-   Wyłącz indeksowanie systemu Windows Server w przypadku woluminów StorSimple.
-   Uruchom skanowanie za pomocą oprogramowania antywirusowego na hoście źródłowym (nie dla woluminów StorSimple).
-   Wyłącz domyślne [konserwacji systemu Windows Server](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx) w Menedżerze zadań. Wykonaj następujące czynności w jednym z następujących sposobów:
    - Wyłącz konfiguratora konserwacji w harmonogramu zadań systemu Windows.
    - Pobierz [PsExec](https://technet.microsoft.com/sysinternals/bb897553.aspx) Windows Sysinternals. Po pobraniu narzędzia PsExec, uruchom program Windows PowerShell jako administrator i wpisz:
      ```powershell
      psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
      ```

### <a name="storsimple-best-practices"></a>Najlepsze rozwiązania StorSimple

-   Pamiętaj, że urządzenie StorSimple zostało zaktualizowane do [aktualizacji 3 lub nowszym](storsimple-install-update-3.md).
-   Odizolowanego ruchu iSCSI i chmury. Użyj iSCSI dedykowanych połączeń dla ruchu między StorSimple i Utwórz kopię zapasową serwera.
-   Pamiętaj, że urządzenie StorSimple jest dedykowany miejsce docelowe kopii zapasowej. Mieszane obciążeń nie są obsługiwane, ponieważ wpływają na Twoje RTO i cel punktu odzyskiwania.

### <a name="netbackup-best-practices"></a>Najlepsze rozwiązania w zakresie NetBackup

-   Baza danych NetBackup powinny być lokalne na serwerze i nie znajdują się na woluminie StorSimple.
-   Odzyskiwania po awarii należy wykonać kopię zapasową bazy danych NetBackup na woluminu StorSimple.
-   Obsługujemy NetBackup pełne i przyrostowe kopie zapasowe (zwaną także różnicowej przyrostowe kopie zapasowe w NetBackup) dla tego rozwiązania. Firma Microsoft zaleca, nie używaj syntetyczne i zbiorczą przyrostowych kopii zapasowych.
-   Plik kopii zapasowej danych powinien zawierać tylko dane dla danego zadania. Na przykład dołącza bez nośnika przez różne zadania są dozwolone.

Aby najnowsze ustawienia NetBackup i najlepsze rozwiązania dotyczące wdrażania tych wymagań, zobacz dokumentację NetBackup w [www.veritas.com](https://www.veritas.com).


## <a name="retention-policies"></a>Zasady przechowywania

Jednym z najbardziej popularne typy zasad przechowywania kopii zapasowych jest zasad dziadek, ojciec i syn (GFS). W zasadzie GFS przyrostowej kopii zapasowej jest wykonywane codziennie i pełne kopie zapasowe są wykonywane co tydzień i co miesiąc. Powoduje to zasady sześć StorSimple woluminami warstwowymi: jeden wolumin zawiera co tydzień, miesięcznych i rocznych pełnych kopii zapasowych; inne woluminy pięć przechowywania codziennych przyrostowych kopii zapasowych.

W poniższym przykładzie używamy obrotu GFS. W przykładzie założono, że:

-   Deduplikacją nie lub skompresowane dane są używane.
-   Pełne kopie zapasowe są 1 TiB.
-   Codzienne przyrostowe kopie zapasowe są 500 GiB.
-   Cztery cotygodniowe kopie zapasowe są przechowywane przez miesiąc.
-   Dwanaście comiesięczne kopie zapasowe są przechowywane przez rok.
-   Jeden corocznej kopii zapasowej jest przechowywana na 10 lat.

W oparciu o poprzednim założeń, Utwórz 26-TiB StorSimple do warstwy wolumin miesięcznych i rocznych pełnych kopii zapasowych. Tworzenie 5-TiB StorSimple do warstwy woluminu dla każdego przyrostowych codziennych kopii zapasowych.

| Typ kopii zapasowej przechowywania | Rozmiar (TiB) | Mnożnik GFS\* | Łączna pojemność (TiB)  |
|---|---|---|---|
| Pełne co tydzień | 1 | 4  | 4 |
| Codzienne przyrostowe | 0.5 | 20 (cykle równy liczbie tygodni miesięcznie) | 12 (2 dla dodatkowych przydziału) |
| Miesięczne pełny | 1 | 12 | 12 |
| Pełne roczne | 1  | 10 | 10 |
| Wymaganie GFS |   | 38 |   |
| Dodatkowego limitu przydziału  | 4  |   | 42 całkowita wymaganie GFS  |
\* Mnożnik GFS jest liczba kopii, które należy chronić i zachować zgodnie z wymaganiami zasad tworzenia kopii zapasowej.

## <a name="set-up-netbackup-storage"></a>Konfigurowanie magazynu NetBackup

### <a name="to-set-up-netbackup-storage"></a>Aby skonfigurować Magazyn NetBackup

1.  W konsoli administracyjnej NetBackup wybierz **nośnika i zarządzanie urządzeniami** > **urządzeń** > **pul dysków**. W Kreatorze konfiguracji puli dysków, wybierz typ serwera magazynu **AdvancedDisk**, a następnie wybierz **dalej**.

    ![Konsola administracyjna NetBackup, Kreator konfiguracji puli dysków](./media/storsimple-configure-backup-target-using-netbackup/nbimage1.png)

2.  Wybierz serwer, a następnie wybierz **dalej**.

    ![Konsola administracyjna NetBackup, wybierz serwer](./media/storsimple-configure-backup-target-using-netbackup/nbimage2.png)

3.  Wybierz woluminu StorSimple.

    ![Konsola administracyjna NetBackup, wybierz dysk woluminu StorSimple](./media/storsimple-configure-backup-target-using-netbackup/nbimage3.png)

4.  Wprowadź nazwę i miejsce docelowe kopii zapasowej, a następnie wybierz **dalej** > **dalej** aby zakończyć pracę kreatora.

5.  Przejrzyj ustawienia, a następnie wybierz **Zakończ**.

6.  Na koniec każdego przydziału woluminu, Zmień ustawienia urządzenia pamięci masowej do dopasowania tych zalecane w [najlepsze rozwiązania dotyczące StorSimple i NetBackup](#best-practices-for-storsimple-and-netbackup).

7. Powtórz kroki od 1 do 6, aż zakończysz przypisywanie woluminy StorSimple.

    ![Konsoli administracyjnej NetBackup konfiguracja dysków](./media/storsimple-configure-backup-target-using-netbackup/nbimage5.png)

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Konfigurowanie StorSimple jako miejsce docelowe kopii zapasowej głównej

> [!NOTE]
> Przywraca dane z kopii zapasowej, który ma zostać warstwy do chmury wystąpić szybkością chmury.

Na poniższej ilustracji przedstawiono mapowanie woluminie typowe zadania tworzenia kopii zapasowej. W takim przypadku wszystkie cotygodniowe kopie zapasowe mapowania sobota zapełniony dysk, a przyrostowe kopie zapasowe mapowania dysków przyrostowe od poniedziałku do piątku. Tworzenie kopii zapasowych i przywracania są z StorSimple wszystkie warstwy woluminu.

![Diagram logiczny konfiguracji podstawowej miejsce docelowe kopii zapasowej ](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>Przykład zaplanować StorSimple jako miejsce docelowe kopii zapasowej głównej GFS

Oto przykład harmonogramu obrotu GFS cztery tygodnie miesięcznych i rocznych:

| Typ częstotliwości lub tworzenia kopii zapasowej | Pełne | Przyrostowe (1-5 dni)  |   
|---|---|---|
| Co tydzień (1 – 4 tygodnie) | Sobota | Poniedziałek — piątek |
| Miesięczne  | Sobota  |   |
| Rocznie | Sobota  |   |   |

## <a name="assigning-storsimple-volumes-to-a-netbackup-backup-job"></a>Przypisywanie woluminów StorSimple do NetBackup zadanie tworzenia kopii zapasowej

Następująca sekwencja przyjęto założenie, że NetBackup i host docelowy są skonfigurowane zgodnie z wytycznymi NetBackup agenta.

### <a name="to-assign-storsimple-volumes-to-a-netbackup-backup-job"></a>Aby przypisać woluminy StorSimple NetBackup zadanie tworzenia kopii zapasowej

1.  W konsoli administracyjnej NetBackup wybierz **zarządzania NetBackup**, kliknij prawym przyciskiem myszy **zasady**, a następnie wybierz **nowe zasady**.

    ![Konsola administracyjna NetBackup, Utwórz nowe zasady](./media/storsimple-configure-backup-target-using-netbackup/nbimage6.png)

2.  W **dodać nową zasadę** okno dialogowe, wprowadź nazwę zasady, a następnie wybierz **Użyj Kreatora konfiguracji zasad** pole wyboru. Kliknij przycisk **OK**.

    ![Konsola administracyjna NetBackup, Dodaj nowe zasady okno dialogowe](./media/storsimple-configure-backup-target-using-netbackup/nbimage7.png)

3.  W Kreatorze konfiguracji zasad tworzenia kopii zapasowych wybiera typ kopii zapasowej, a następnie wybierz **dalej**.

    ![Konsola administracyjna NetBackup, wybierz typ kopii zapasowej](./media/storsimple-configure-backup-target-using-netbackup/nbimage8.png)

4.  Aby ustawić typ zasad, wybierz **standardowe**, a następnie wybierz **dalej**.

    ![Konsola administracyjna NetBackup, typ zasad wybierz opcję](./media/storsimple-configure-backup-target-using-netbackup/nbimage9.png)

5.  Wybierz hosta, wybierz pozycję **umożliwiają wykrywanie systemu operacyjnego klienta** pole wyboru, a następnie wybierz **Dodaj**. Wybierz opcję **Dalej**.

    ![Konsola administracyjna NetBackup, listę klientów w nowych zasad](./media/storsimple-configure-backup-target-using-netbackup/nbimage10.png)

6.  Wybierz dyski, które chcesz utworzyć kopię zapasową.

    ![Konsola administracyjna NetBackup, opcje tworzenia kopii zapasowej dla nowych zasad](./media/storsimple-configure-backup-target-using-netbackup/nbimage11.png)

7.  Wybierz częstotliwość i przechowywania wartości, które spełniają wymagania rotacji kopii zapasowej.

    ![Konsola administracyjna NetBackup, częstotliwość wykonywania kopii zapasowych i obrotu dla nowych zasad](./media/storsimple-configure-backup-target-using-netbackup/nbimage12.png)

8.  Wybierz **dalej** > **dalej** > **Zakończ**.  Harmonogram można zmodyfikować po utworzeniu zasad.

9.  Wybierz, aby rozwinąć zasad właśnie utworzony, a następnie wybierz **harmonogramy**.

    ![Konsola administracyjna NetBackup, harmonogramy dla nowych zasad](./media/storsimple-configure-backup-target-using-netbackup/nbimage13.png)

10.  Kliknij prawym przyciskiem myszy **Inc różnicowej**, wybierz pozycję **skopiować do nowego**, a następnie wybierz **OK**.

    ![Konsola administracyjna NetBackup, harmonogram do nowych zasad](./media/storsimple-configure-backup-target-using-netbackup/nbimage14.png)

11.  Kliknij prawym przyciskiem myszy nowo utworzony harmonogram, a następnie wybierz **zmiany**.

12.  Na **atrybuty** wybierz opcję **Zastąp wybór magazynu zasad** pole wyboru, a następnie wybierz wolumin, gdy od poniedziałku przyrostowych kopii zapasowych Przejdź.

    ![Konsola administracyjna NetBackup, Zmień harmonogram](./media/storsimple-configure-backup-target-using-netbackup/nbimage15.png)

13.  Na **Uruchom okno** , a następnie wybierz przedział czasu dla kopii zapasowych.

    ![Konsola administracyjna NetBackup, zmienianie rozpoczęcia okna](./media/storsimple-configure-backup-target-using-netbackup/nbimage16.png)

14.  Kliknij przycisk **OK**.

15.  Powtórz kroki od 10-14 dla każdej przyrostowej kopii zapasowej. Wybierz odpowiedni wolumin i harmonogram dla każdej kopii zapasowej, którą utworzysz.

16.  Kliknij prawym przyciskiem myszy **Inc różnicowej** zaplanować, a następnie usuń ją.

17.  Zmodyfikuj harmonogram pełnego dostosowane do potrzeb kopii zapasowej.

    ![Konsola administracyjna NetBackup, zmień pełnego harmonogramu](./media/storsimple-configure-backup-target-using-netbackup/nbimage17.png)

18.  Zmień okno rozpoczęcia.

    ![Konsola administracyjna NetBackup, rozpoczęcia okna zmiany](./media/storsimple-configure-backup-target-using-netbackup/nbimage18.png)

19.  Ostatecznego harmonogramu wygląda następująco:

    ![Konsola administracyjna NetBackup, ostatecznego harmonogramu](./media/storsimple-configure-backup-target-using-netbackup/nbimage19.png)

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>Konfigurowanie StorSimple jako dodatkowej miejsce docelowe kopii zapasowej

> [!NOTE]
>Przywraca dane z kopii zapasowej, który ma zostać warstwy do chmury wystąpić szybkością chmury.

W tym modelu musi mieć na nośniku magazynowania (inne niż StorSimple) jako tymczasowy pamięci podręcznej. Na przykład umożliwia dublowanej macierzy niezależnych dysków (RAID) wolumin miejsca, wejścia/wyjścia (We/Wy) i przepustowości. Firma Microsoft zaleca używanie RAID 5, 50 i 10.

Na poniższej ilustracji przedstawiono typowe przechowywania krótkoterminowego woluminów lokalnych (aby serwera) i długoterminowego przechowywania archiwa woluminów. W tym scenariuszu wszystkie kopie zapasowe, uruchom na lokalnym (serwer) woluminu RAID. Te kopie zapasowe są okresowo zduplikowany i archiwizowane do woluminu archiwa. Należy koniecznie rozmiaru lokalne (serwer) woluminu RAID, dzięki czemu może obsłużyć krótkoterminowego przechowywania pojemność i wydajność wymagań.

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>StorSimple jako przykład GFS dodatkowej miejsce docelowe kopii zapasowej

![StorSimple jako miejsce docelowe kopii zapasowej dodatkowej diagram logiczny](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetdiagram.png)

W poniższej tabeli przedstawiono, jak skonfigurować tworzenie kopii zapasowych na dyskach StorSimple i lokalne. Zawiera wymagania dotyczące poszczególnych i całkowitej pojemności.

### <a name="backup-configuration-and-capacity-requirements"></a>Wymagania dotyczące pojemności i konfiguracji kopii zapasowej

| Typ kopii zapasowej i przechowywania | Skonfigurowany magazyn | Rozmiar (TiB) | Mnożnik GFS | Łączna pojemność\* (TiB) |
|---|---|---|---|---|
| Tydzień, 1 (pełne i przyrostowe) |Dysk lokalny (krótkoterminowych)| 1 | 1 | 1 |
| Tygodnie StorSimple 2-4 |Dysk StorSimple (długoterminowej) | 1 | 4 | 4 |
| Miesięczne pełny |Dysk StorSimple (długoterminowej) | 1 | 12 | 12 |
| Pełne roczne |Dysk StorSimple (długoterminowej) | 1 | 1 | 1 |
|Wymagany rozmiar woluminów GFS |  |  |  | 18*|
\* Łączna pojemność obejmuje 17 dyski TiB StorSimple i 1 TiB woluminu RAID.


### <a name="gfs-example-schedule-gfs-rotation-weekly-monthly-and-yearly-schedule"></a>Harmonogram przykład GFS: obracanie GFS harmonogram tygodniowy, miesięcznych i rocznych

| Tydzień | Pełne | Przyrostowe dnia 1 | Dzień przyrostowe 2 | Dzień przyrostowe 3 | Dzień przyrostowe 4 | Dzień przyrostowe 5 |
|---|---|---|---|---|---|---|
| 1 tydzień | Lokalne woluminu RAID  | Lokalne woluminu RAID | Lokalne woluminu RAID | Lokalne woluminu RAID | Lokalne woluminu RAID | Lokalne woluminu RAID |
| Tydzień 2 | Tygodnie StorSimple 2-4 |   |   |   |   |   |
| Tydzień 3 | Tygodnie StorSimple 2-4 |   |   |   |   |   |
| Tydzień 4 | Tygodnie StorSimple 2-4 |   |   |   |   |   |
| Miesięczne | Co miesiąc StorSimple |   |   |   |   |   |
| Rocznie | Co rok StorSimple  |   |   |   |   |   |   |


## <a name="assign-storsimple-volumes-to-a-netbackup-archive-and-duplication-job"></a>Przypisać woluminy StorSimple do zadania archiwum i powielania NetBackup

Ponieważ NetBackup oferuje szeroką gamę opcji zarządzania magazynu i nośników, firma Microsoft zaleca, należy skontaktować się z Veritas lub Twoje architektów NetBackup prawidłowo oceny wymagań dotyczących magazynu cyklu życia zasad (SLP).

Po zdefiniowaniu pul dysków początkowej, musisz zdefiniować trzy zasady cyklu pomocy dodatkowego miejsca do magazynowania, łącznie cztery zasady:
* LocalRAIDVolume
* StorSimpleWeek2 4
* StorSimpleMonthlyFulls
* StorSimpleYearlyFulls

### <a name="to-assign-storsimple-volumes-to-a-netbackup-archive-and-duplication-job"></a>Aby przypisać woluminy StorSimple NetBackup zadania archiwum i dublowania

1.  W konsoli administracyjnej NetBackup wybierz **magazynu** > **zasady cyklu pomocy magazynu** > **nowe zasady cyklu pomocy magazynu**.

    ![Konsola administracyjna NetBackup, nowe zasady cyklu pomocy magazynu](./media/storsimple-configure-backup-target-using-netbackup/nbimage20.png)

2.  Wprowadź nazwę dla migawki, a następnie wybierz **Dodaj**.

3.  W **nowej operacji** na okna dialogowego **właściwości** karcie dla **operacji**, wybierz pozycję **kopii zapasowej**. Wybierz wartości dla **Magazyn docelowy**, **typ przechowywania**, i **okres przechowywania**. Kliknij przycisk **OK**.

    ![Konsola administracyjna NetBackup, okno dialogowe nowej operacji](./media/storsimple-configure-backup-target-using-netbackup/nbimage22.png)

    Określa pierwszą operacją tworzenia kopii zapasowej i repozytorium.

4.  Wybierz, aby wyróżnić poprzedniej operacji, a następnie wybierz **Dodaj**. W **operacji magazynu zmiany** oknie dialogowym Wybierz wartości dla **Magazyn docelowy**, **typ przechowywania**, i **okres przechowywania**.

    ![Konsola administracyjna NetBackup, okno dialogowe operacji zmiany magazynu](./media/storsimple-configure-backup-target-using-netbackup/nbimage23.png)

5.  Wybierz, aby wyróżnić poprzedniej operacji, a następnie wybierz **Dodaj**. W **nowe zasady cyklu pomocy magazynu** okno dialogowe Dodaj comiesięczne kopie zapasowe w roku.

    ![Konsola administracyjna NetBackup, okno dialogowe nowe zasady cyklu pomocy magazynu](./media/storsimple-configure-backup-target-using-netbackup/nbimage24.png)

6.  Powtórz kroki 4 – 5 po utworzeniu zasad przechowywania SLP kompleksowe, potrzebne.

    ![Konsola administracyjna NetBackup, Dodaj zasady w oknie dialogowym Nowe zasady cyklu pomocy magazynu](./media/storsimple-configure-backup-target-using-netbackup/nbimage25.png)

7.  Po zakończeniu definiowania zgodnie z zasadami przechowywania SLP **zasad**, definiowanie zasad tworzenia kopii zapasowej, wykonując kroki opisane szczegółowo w [woluminów StorSimple przypisywanie NetBackup zadanie tworzenia kopii zapasowej](#assigning-storsimple-volumes-to-a-netbackup-backup-job).

8.  W obszarze **harmonogramy**w **Zmień harmonogram** okno dialogowe kliknij prawym przyciskiem myszy **pełna**, a następnie wybierz **zmiany**.

    ![Konsola administracyjna NetBackup, okno dialogowe Zmień harmonogram](./media/storsimple-configure-backup-target-using-netbackup/nbimage26.png)

9.  Wybierz **Zastąp wybór magazynu zasad** pole wyboru, a następnie wybierz zasady przechowywania SLP utworzonego w krokach 1 – 6.

    ![Konsola administracyjna NetBackup, wybór magazynu zasad zastąpienia](./media/storsimple-configure-backup-target-using-netbackup/nbimage27.png)

10.  Wybierz **OK**, a następnie powtórz dla przyrostowych harmonogram tworzenia kopii zapasowych.

    ![Konsola administracyjna NetBackup, okno dialogowe Zmień harmonogram dla przyrostowych kopii zapasowych](./media/storsimple-configure-backup-target-using-netbackup/nbimage28.png)


| Typ kopii zapasowej przechowywania | Rozmiar (TiB) | Mnożnik GFS\* | Łączna pojemność (TiB)  |
|---|---|---|---|
| Pełne co tydzień |  1  |  4 | 4  |
| Codzienne przyrostowe  | 0.5  | 20 (cykle są równe liczbie tygodni miesięcznie) | 12 (2 dla dodatkowych przydziału) |
| Miesięczne pełny  | 1 | 12 | 12 |
| Pełne roczne | 1  | 10 | 10 |
| Wymaganie GFS  |     |     | 38 |
| Dodatkowego limitu przydziału  | 4  |    | 42 całkowita wymaganie GFS |
\* Mnożnik GFS jest liczba kopii, które należy chronić i zachować zgodnie z wymaganiami zasad tworzenia kopii zapasowej.

## <a name="storsimple-cloud-snapshots"></a>Migawki StorSimple w chmurze

Migawki StorSimple w chmurze ochrony danych, która znajduje się w urządzeniu StorSimple. Tworzenie migawek chmury jest odpowiednikiem wysyłania lokalnej kopii zapasowej taśmy, aby mechanizm poza siedzibą firmy. Użycie magazynu geograficznie nadmiarowego Azure, tworzenia migawki chmury jest odpowiednikiem wysyłania kopii zapasowych taśm z wieloma lokacjami. Jeśli potrzebujesz przywrócić urządzenia po awarii, mogą doprowadzić online innego urządzenia StorSimple i tryb failover. Po pracy awaryjnej będzie można uzyskiwać dostęp do danych (szybkością chmury) z najnowszych migawka w chmurze.

W poniższej sekcji opisano sposób tworzenia krótkich skryptu do uruchomienia i usunięcia migawki StorSimple w chmurze podczas tworzenia kopii zapasowej przetwarzania końcowego.

> [!NOTE]
> Migawki tworzone ręcznie lub programowo nie wykonuj zasady wygasania StorSimple snapshot. Te migawki, należy ręcznie lub programowo usunąć.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Uruchom i Usuń migawki w chmurze za pomocą skryptu

> [!NOTE]
> Dokładnie oceń zgodności i następstwa przechowywania danych przed usunięciem migawki StorSimple. Aby uzyskać więcej informacji o sposobie uruchamiania skryptu po wykonaniu kopii zapasowej, zobacz [dokumentacji NetBackup](http://www.veritas.com/docs/000094423).

### <a name="backup-lifecycle"></a>Cykl życia tworzenia kopii zapasowej

![Diagram cyklu życia tworzenia kopii zapasowej](./media/storsimple-configure-backup-target-using-netbackup/backuplifecycle.png)

### <a name="requirements"></a>Wymagania

-   Serwer, który uruchamia skrypt musi mieć dostęp do zasobów w chmurze Azure.
-   Konto użytkownika musi mieć odpowiednie uprawnienia.
-   Zasady tworzenia kopii zapasowej StorSimple przy użyciu skojarzonych woluminów StorSimple musi skonfigurować, ale nie jest włączona.
-   Będziesz potrzebować nazwy zasobu StorSimple, klucz rejestracji, identyfikator urządzenia zasad nazwę i tworzenia kopii zapasowej.

### <a name="to-start-or-delete-a-cloud-snapshot"></a>Aby uruchomić lub usunąć migawka w chmurze

1.  [Zainstalowanie programu Azure PowerShell](/powershell/azure/overview).
2. Pobierania i instalacji [CloudSnapshots.ps1 Zarządzaj](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Manage-CloudSnapshots.ps1) skrypt programu PowerShell.
3. Na serwerze, który uruchamia skrypt Uruchom program PowerShell jako administrator. Upewnij się, że Uruchom skrypt z `-WhatIf $true` aby zobaczyć, jakie zmiany skryptu spowoduje. Po zakończeniu weryfikacji przekazać `-WhatIf $false`. Uruchom poniżej polecenia:
```powershell
.\Manage-CloudSnapshots.ps1 -SubscriptionId [Subscription Id] -TenantId [Tenant ID] -ResourceGroupName [Resource Group Name] -ManagerName [StorSimple Device Manager Name] -DeviceName [device name] -BackupPolicyName [backup policyname] -RetentionInDays [Retention days] -WhatIf [$true or $false]
```
4.  Dodaj skrypt do zadania kopii zapasowej w NetBackup. Aby to zrobić, Edytuj NetBackup Opcje zadania Przetwarzanie wstępne i przetwarzania końcowego poleceń.

> [!NOTE]
> Zaleca się uruchomić StorSimple chmury migawki zasad tworzenia kopii zapasowej jako przetwarzanie końcowe skrypt po zakończeniu zadania kopii zapasowej codziennie. Aby uzyskać więcej informacji na temat kopii zapasowej i przywracania środowiska aplikacji kopii zapasowej, aby pomóc spełnić cel punktu odzyskiwania oraz w celu skontaktuj się z Twojego architektów kopii zapasowej.

## <a name="storsimple-as-a-restore-source"></a>StorSimple jako źródło przywracania

Przywraca z pracy urządzenia StorSimple, takich jak przywracanie z dowolnym urządzeniem magazynu blokowego. Przywraca dane, które jest do warstwy do chmury występuje szybkością chmury. Dla danych lokalnych przywraca wystąpić od szybkości dysku lokalnego urządzenia. Aby uzyskać informacje o tym, jak przeprowadzić przywracanie, zobacz [dokumentacji NetBackup](http://www.veritas.com/docs/000094423). Firma Microsoft zaleca odpowiadają NetBackup przywracania najlepszych rozwiązań.

## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple trybu failover i odzyskiwanie po awarii

> [!NOTE]
> Dla scenariuszy miejsce docelowe kopii zapasowej urządzenia chmury StorSimple nie jest obsługiwany jako miejsce docelowe przywracania.

Awarii może wpływać wiele czynników. Poniższa tabela zawiera listę typowych scenariuszy odzyskiwania po awarii.

| Scenariusz | Wpływ | Jak odzyskać | Uwagi |
|---|---|---|---|
| Niepowodzenie urządzenia StorSimple | Operacje tworzenia kopii zapasowej i przywracania są przerywane. | Zastąp urządzenia nie powiodło się i wykonywać [StorSimple trybu failover i odzyskiwania po awarii](storsimple-device-failover-disaster-recovery.md). | Jeśli musisz wykonać przywracanie po odzyskaniu urządzenia zestawów roboczych pełne dane są pobierane z chmury do nowego urządzenia. Wszystkie operacje są szybkością chmury. Indeks i ponowne skanowanie procesu katalogu może spowodować wszystkie zestawy kopii zapasowych do skanowania i pobierane z warstwy chmury do urządzenia lokalnego warstwy, która może być czasochłonne. |
| Awaria serwera NetBackup | Operacje tworzenia kopii zapasowej i przywracania są przerywane. | Odbuduj serwer kopii zapasowych i przywracania bazy danych. | Należy odbudować ani przywrócić serwer NetBackup w lokacji odzyskiwania po awarii. Najnowszy punkt przywracania bazy danych. Jeśli przywróconej bazy danych NetBackup nie jest zsynchronizowana z zadaniami najnowszej kopii zapasowej, indeksowanie i katalogowanie jest wymagane. Ten indeks i ponowne skanowanie procesu katalogu może spowodować wszystkie zestawy kopii zapasowych do skanowania i pobierane z poziomów w chmurze w warstwie lokalnej urządzenia. Dzięki temu dalsze intensywnie czasu. |
| Awaria lokacji, która powoduje utratę kopii zapasowych serwera i StorSimple | Operacje tworzenia kopii zapasowej i przywracania są przerywane. | Najpierw przywrócić StorSimple, a następnie przywróć NetBackup. | Najpierw przywrócić StorSimple, a następnie przywróć NetBackup. Jeśli musisz wykonać przywracanie po odzyskaniu urządzenia zestawów roboczych pełne dane są pobierane z chmury do nowego urządzenia. Wszystkie operacje są szybkością chmury. |

## <a name="references"></a>Dokumentacja

Poniższe dokumenty zostały odwołanie do tego artykułu:

- [StorSimple Wielościeżkowe We/Wy Instalatora](storsimple-configure-mpio-windows-server.md)
- [Scenariusze magazynu: alokowanie elastyczne](http://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [Przy użyciu GPT dysków](http://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [Konfigurowanie kopii w tle dla folderów udostępnionych](http://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o sposobie [przywrócenie z zestawu kopii zapasowych](storsimple-restore-from-backup-set-u2.md).
- Dowiedz się więcej na temat sposobu wykonywania [urządzenia trybu failover i odzyskiwania po awarii](storsimple-device-failover-disaster-recovery.md).
