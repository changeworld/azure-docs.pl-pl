---
title: Seria StorSimple 8000, jako cel kopii zapasowej za pomocą NetBackup | Dokumentacja firmy Microsoft
description: W tym artykule opisano konfiguracji docelowej kopii zapasowej StorSimple z oprogramowaniem Veritas NetBackup.
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
ms.openlocfilehash: 17428405a0be45854a2eaaef831864f529ed145a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60725288"
---
# <a name="storsimple-as-a-backup-target-with-netbackup"></a>Usługa StorSimple jako miejsce docelowe kopii zapasowej za pomocą NetBackup

## <a name="overview"></a>Omówienie

Usługa StorSimple systemu Azure to hybrydowe rozwiązanie magazynu w chmurze firmy Microsoft. StorSimple eliminuje złożoności wykładniczy wzrost ilości danych za pomocą konta usługi Azure storage jako rozszerzenia rozwiązania lokalnego, a następnie automatycznie warstw danych przez Magazyn lokalny oraz Magazyn w chmurze.

W tym artykule omawiane jest integracja StorSimple z oprogramowaniem Veritas NetBackup i najlepsze rozwiązania dotyczące integracji obu rozwiązań. Możemy również wydają zalecenia dotyczące sposobu konfigurowania Veritas NetBackup najlepiej zintegrować z usługą StorSimple. Firma Microsoft Odrocz Veritas najlepsze rozwiązania tworzenia kopii zapasowej architektów i administratorów najlepszym sposobem skonfigurować Veritas NetBackup do spełnienia poszczególnych wymagań kopii zapasowej i umowy dotyczące poziomu usług (SLA).

Mimo że firma Microsoft pokazują, czynności konfiguracyjne i kluczowe pojęcia, przewodnik krok po kroku konfiguracji lub instalacja jest w żadnym wypadku nie w tym artykule. Przyjęto założenie, że podstawowe składniki i infrastruktury są w stanie i gotowe do obsługi pojęcia, które opisano.

### <a name="who-should-read-this"></a>Kto powinien przeczytać to?

Informacje przedstawione w tym artykule, będą najbardziej przydatne do tworzenia kopii zapasowej Administratorzy, administratorzy pamięci masowej i architektów magazynu, którzy dysponują wiedzą na temat magazynu, Windows Server 2012 R2, Ethernet, usług cloud services i oprogramowaniem Veritas NetBackup.

### <a name="supported-versions"></a>Obsługiwane wersje

-   NetBackup 7.7.x i nowsze wersje
-   [Usługa StorSimple Update 3 i nowszych wersjach](storsimple-overview.md#storsimple-workload-summary)


## <a name="why-storsimple-as-a-backup-target"></a>Dlaczego StorSimple jako miejsce docelowe kopii zapasowej?

Usługa StorSimple jest dobrym wyborem dla docelowej kopii zapasowej, ponieważ:

-   Zapewnia standardowe, lokalnego magazynu kopii zapasowych do użycia przez aplikacje jako szybko miejsce docelowe kopii zapasowej, bez wprowadzania żadnych zmian. Możesz również użyć usługi StorSimple do szybkiego przywrócenia najnowszych kopii zapasowych.
-   Chmurze warstw jest w pełni zintegrowana z konta magazynu w chmurze platformy Azure do użycia ekonomiczny magazyn usługi Azure Storage.
-   Do odzyskiwania po awarii są automatycznie zapewnia magazyn poza siedzibą firmy.

## <a name="key-concepts"></a>Kluczowe pojęcia

Podobnie jak w przypadku dowolnego pamięci masowej, dokładnej oceny wydajności magazynu rozwiązania, umowy SLA, współczynnika zmian i potrzeby związane z pojemnością wzrost jest mają kluczowe znaczenie dla sukcesu. Główne zagadnienie jest to, że dzięki zastosowaniu warstwy chmury, Twoje godziny dostępu i przepustowości grać chmury zasadniczą rolę w StorSimple możliwość wykonywania swojej pracy.

StorSimple zaprojektowano w celu zapewnienia magazynu dla aplikacji, które działają w dobrze zdefiniowany zestaw roboczy dane (gorąca). W tym modelu zestaw roboczy danych są przechowywane w warstwach lokalnych, a pozostałe wolne/zimnego/zarchiwizowane zbiór danych jest warstwy do chmury. Ten model jest reprezentowany na poniższej ilustracji. Niemal płaskie zieloną linią reprezentuje dane przechowywane w warstwach lokalnych urządzenia StorSimple. Czerwona linia reprezentuje łączna ilość danych przechowywanych w ramach rozwiązania StorSimple we wszystkich warstwach. Odstęp między płaskie zieloną linią i wykładniczą krzywa czerwony reprezentuje łączna ilość danych przechowywanych w chmurze.

**Obsługa warstw na StorSimple**
![diagramu warstw usługi StorSimple](./media/storsimple-configure-backup-target-using-netbackup/image1.jpg)

Dzięki tej architekturze pamiętać znajdziesz, Magazyn StorSimple jest idealnie nadaje się do działania jako miejsce docelowe kopii zapasowej. Możesz użyć usługi StorSimple do:
-   Wykonywać najczęstsze przywrócenie danych z lokalnych zestaw roboczy w danych.
-   Korzystaj z chmury dla odzyskiwania po awarii poza siedzibą firmy i starszych danych, w których rzadsze przywracanie.

## <a name="storsimple-benefits"></a>Korzyści z usługi StorSimple

Usługa StorSimple zapewnia rozwiązania lokalnego, który jest w pełni zintegrowana z platformą Microsoft Azure, wykorzystując bezproblemowy dostęp do serwera lokalnego i Magazyn w chmurze.

Usługa StorSimple używa automatycznego warstw między lokalnym urządzeniem mającej urządzenia półprzewodnikowe (SSD) i magistrali serial attached SCSI (SAS), magazynu i Azure Storage. Automatyczna obsługa warstw przechowuje rzadziej używanych danych lokalnych, na warstwy dysków SSD i sygnatury dostępu Współdzielonego. Przesuwa się rzadko używane dane do usługi Azure Storage.

Magazyn StorSimple oferuje następujące korzyści:

-   Unikatowe deduplikacja i kompresja algorytmów korzystaj z chmury, aby osiągnąć niezrównaną deduplikacji poziomy
-   Wysoka dostępność
-   Replikację geograficzną za pomocą platformy Azure replikacji geograficznej
-   Integracja z platformą Azure
-   Szyfrowanie danych w chmurze
-   Odzyskiwanie po awarii ulepszone i zgodności

Chociaż StorSimple przedstawia dwa scenariusze wdrażania głównego (podstawowy cel kopii zapasowej i dodatkowej docelowa kopia zapasowa), zasadniczo jest zwykły, urządzeniem magazynu blokowego. Usługa StorSimple jest podczas kompresji i deduplikacji. Bezproblemowo wysyła i pobiera dane między chmury oraz aplikacji i systemu plików.

Aby uzyskać więcej informacji na temat rozwiązania StorSimple, zobacz [serii StorSimple 8000: Rozwiązanie magazynu w chmurze hybrydowej](storsimple-overview.md). Ponadto możesz przejrzeć [techniczne serii StorSimple 8000](storsimple-technical-specifications-and-compliance.md).

> [!IMPORTANT]
> Przy użyciu usługi StorSimple urządzenia jako miejsce docelowe kopii zapasowej jest obsługiwana tylko w przypadku StorSimple 8000 Update 3 i nowszych wersjach.

## <a name="architecture-overview"></a>Omówienie architektury

W poniższej tabeli przedstawiono wskazówki dotyczące początkowej architektura modelu urządzenia.

**Możliwości usługi StorSimple dla lokalnego i Magazyn w chmurze**

| Pojemność magazynu       | 8100          | 8600            |
|------------------------|---------------|-----------------|
| Pojemność magazynu lokalnego | &lt; 10 TiB\*  | &lt; 20 TiB\*  |
| Pojemność magazynu w chmurze | &gt; 200 TiB\* | &gt; 500 TiB\* |

\* Rozmiar magazynu nie przyjmuje żadnej deduplikacji ani kompresji.

**Usługa StorSimple pojemności dla głównych i dodatkowych kopii zapasowych**

| Scenariusz tworzenia kopii zapasowej  | Pojemność magazynu lokalnego  | Pojemność magazynu w chmurze  |
|---|---|---|
| Podstawowa kopia zapasowa  | Najnowsze kopie zapasowe przechowywane w magazynie lokalnym do szybkiego odzyskiwania aby spełnić cel punktu odzyskiwania (RPO) | Historia kopii zapasowych (RPO) mieści się w pojemności chmury |
| Dodatkowe kopie zapasowej | Dodatkową kopię danych kopii zapasowej może być przechowywana w pojemności chmury  | ND  |

## <a name="storsimple-as-a-primary-backup-target"></a>Magazyn StorSimple jako podstawowy cel kopii zapasowej

W tym scenariuszu woluminów StorSimple są prezentowane w kopii zapasowej aplikacji jako jedynego repozytorium do przechowywania kopii zapasowych. Na poniższej ilustracji przedstawiono architekturę rozwiązania, w którym wszystkie przypadki użycia kopii zapasowych usługi StorSimple warstwowego woluminów na potrzeby tworzenia kopii zapasowych i przywracanie danych.

![Magazyn StorSimple jako diagram logiczny podstawowy cel kopii zapasowej](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Podstawowy cel kopii zapasowej etapy logiczne

1.  Utwórz kopię zapasową serwera skontaktuje się z docelowego agenta kopii zapasowej i agenta kopii zapasowej przesyła dane do serwera kopii zapasowych.
2.  Utwórz kopię zapasową serwera zapisuje dane do magazynu StorSimple woluminy warstwowe.
3.  Utwórz kopię zapasową serwera aktualizuje bazę danych wykazu, a następnie kończy zadanie tworzenia kopii zapasowej.
4.  Skrypt migawki wyzwala programu StorSimple snapshot manager (uruchamiania lub usuwania).
5.  Utwórz kopię zapasową serwera Usuwa wygasłe kopii zapasowych na podstawie zasad przechowywania.

### <a name="primary-target-restore-logical-steps"></a>Etapy logiczne przywracania podstawowy cel

1.  Utwórz kopię zapasową serwera rozpoczyna przywracanie odpowiednie dane z repozytorium magazynów.
2.  Agent usługi Kopia zapasowa odbiera dane z kopii zapasowej serwera.
3.  Utwórz kopię zapasową serwera zakończeniu zadania przywracania.

## <a name="storsimple-as-a-secondary-backup-target"></a>Magazyn StorSimple jako dodatkowej docelowy kopii zapasowej

W tym scenariuszu woluminów StorSimple są głównie używane do długoterminowego przechowywania i archiwizowania.

Poniższy rysunek przedstawia architekturę, w które wstępne kopie zapasowe i przywrócenie woluminu docelowego o wysokiej wydajności. Te kopie zapasowe są kopiowane i archiwizowanie StorSimple warstwowego woluminu zgodnie z ustalonym harmonogramem.

Jest ważne, aby rozmiar woluminu o wysokiej wydajności, dzięki czemu może obsługiwać zasady przechowywania wymagań pojemności i wydajności.

![Magazyn StorSimple jako diagram logiczny dodatkowej docelowy kopii zapasowej](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>Docelowy dodatkowej kopii zapasowej etapy logiczne

1.  Utwórz kopię zapasową serwera skontaktuje się z docelowego agenta kopii zapasowej i agenta kopii zapasowej przesyła dane do serwera kopii zapasowych.
2.  Utwórz kopię zapasową serwera zapisuje dane do magazynu o wysokiej wydajności.
3.  Utwórz kopię zapasową serwera aktualizuje bazę danych wykazu, a następnie kończy zadanie tworzenia kopii zapasowej.
4.  Utwórz kopię zapasową serwera kopiuje StorSimple na podstawie zasad przechowywania kopii zapasowych.
5.  Skrypt migawki wyzwala programu StorSimple snapshot manager (uruchamiania lub usuwania).
6.  Utwórz kopię zapasową serwera Usuwa wygasłe na podstawie zasad przechowywania kopii zapasowych.

### <a name="secondary-target-restore-logical-steps"></a>Etapy logiczne przywracania dodatkowej docelowej

1.  Utwórz kopię zapasową serwera rozpoczyna przywracanie odpowiednie dane z repozytorium magazynów.
2.  Agent usługi Kopia zapasowa odbiera dane z kopii zapasowej serwera.
3.  Utwórz kopię zapasową serwera zakończeniu zadania przywracania.

## <a name="deploy-the-solution"></a>Wdrażanie rozwiązania

Wdrożenie tego rozwiązania wymaga wykonania trzech kroków:
1. Przygotowanie infrastruktury sieci.
2. Wdrażanie urządzenia StorSimple jako miejsce docelowe kopii zapasowej.
3. Deploy Veritas NetBackup.

Każdy krok jest szczegółowo omówione w poniższych sekcjach.

### <a name="set-up-the-network"></a>Konfigurowanie sieci

Ponieważ StorSimple systemu Azure to rozwiązanie, które jest zintegrowane z chmury platformy Azure, StorSimple wymaga aktywne i działającego połączenia w chmurze platformy Azure. To połączenie jest używane dla operacji takich jak migawki w chmurze, zarządzanie danymi i transfer metadanych, a do warstwy starsze, mniej używane dane do magazynu w chmurze Azure.

Rozwiązanie do optymalnego wykonywania firma Microsoft zaleca, postępuj zgodnie z tych sieci najlepszych rozwiązań:

-   Łącze, które łączy StorSimple obsługi warstw na platformie Azure muszą spełniać wymagań dotyczących przepustowości. Aby to osiągnąć, zastosowanie odpowiedniego poziomu jakości usług (QoS) w infrastrukturze przełączników, aby spełnić cel punktu odzyskiwania i odzyskiwania czasu cel umowy SLA.

-   Maksymalna opóźnienia dostępu do magazynu obiektów Blob platformy Azure powinna być około 80 ms.

### <a name="deploy-storsimple"></a>Deploy StorSimple

Aby uzyskać instrukcje krok po kroku dotyczące wdrażania usługi StorSimple, zobacz [wdrażanie urządzenia StorSimple w środowisku lokalnym](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-netbackup"></a>Deploy NetBackup

Aby uzyskać instrukcje krok po kroku dotyczące wdrażania 7.7.x NetBackup, zobacz [dokumentacji 7.7.x NetBackup](http://www.veritas.com/docs/000094423).

## <a name="set-up-the-solution"></a>Konfigurowanie rozwiązania

W tej sekcji pokażemy niektóre przykłady konfiguracji. Poniższe przykłady i zalecenia dotyczące pokazują implementacji podstawowych i najbardziej podstawową. Ta implementacja nie mogą być stosowane bezpośrednio do swoich specyficznych wymagań kopii zapasowej.

### <a name="set-up-storsimple"></a>Konfigurowanie usługi StorSimple

| Zadania związane z wdrażaniem usługi StorSimple  | Dodatkowe komentarze |
|---|---|
| Wdrażanie urządzenia StorSimple w środowisku lokalnym. | Obsługiwane wersje: Update 3 i nowszych wersjach. |
| Włącz docelowy kopii zapasowej. | Aby włączyć lub wyłączyć tryb docelowy kopii zapasowej i można uzyskać stanu, należy użyć tych poleceń. Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia zdalnego na urządzeniu StorSimple](storsimple-remote-connect.md).</br> Aby włączyć tryb tworzenia kopii zapasowych: `Set-HCSBackupApplianceMode -enable`. </br> Aby wyłączyć tryb tworzenia kopii zapasowych: `Set-HCSBackupApplianceMode -disable`. </br> Aby uzyskać bieżący stan ustawienia trybu tworzenia kopii zapasowych: `Get-HCSBackupApplianceMode`. |
| Utwórz kontener woluminów typowe dla woluminu, który przechowuje dane kopii zapasowej. Wszystkie dane w kontenerze wolumin jest deduplikowany. | Kontenery woluminów StorSimple Definiowanie domen deduplikacji.  |
| Tworzenie woluminów StorSimple. | Utwórz woluminy o rozmiarze jak blisko oczekiwanego użycia jak to możliwe, ponieważ rozmiar woluminu wpływa na czas trwania migawki w chmurze. Aby uzyskać informacje o tym, jak rozmiar woluminu, przeczytaj temat [zasady przechowywania](#retention-policies).</br> </br> Użyj StorSimple woluminy warstwowe, a następnie wybierz **Użyj tego woluminu dla rzadziej używanych danych archiwalnych** pole wyboru. </br> Używanie tylko lokalnie przypięte woluminy nie jest obsługiwane. |
| Utworzone unikatowe zasady tworzenia kopii zapasowych usługi StorSimple dla wszystkich woluminów docelowy kopii zapasowej. | Zasady kopii zapasowych usługi StorSimple definiuje grupę spójności woluminu. |
| Wyłączyć harmonogram, zgodnie z migawki wygaśnie. | Migawki są wyzwalane jako operację przetwarzania końcowego. |

### <a name="set-up-the-host-backup-server-storage"></a>Konfigurowanie magazynu kopii zapasowych serwera hosta

Konfigurowanie magazynu kopii zapasowych serwera hosta zgodnie z poniższymi wskazówkami:  

- Nie używaj woluminy łączone (utworzonego przez Windows przystawki Zarządzanie dyskami); woluminy łączone są nieobsługiwane.
- Formatowanie woluminów o rozmiarze 64 KB alokacji przy użyciu systemu plików NTFS.
- Mapowanie woluminów StorSimple bezpośrednio na serwerze NetBackup.
    - Użyj iSCSI dla serwerów fizycznych.
    - Dyski przekazujące na użytek serwerów wirtualnych.


## <a name="best-practices-for-storsimple-and-netbackup"></a>Najlepsze rozwiązania dotyczące magazynu StorSimple i NetBackup

Konfigurowanie rozwiązania programu zgodnie z wytycznymi podanymi w następujących sekcjach kilka.

### <a name="operating-system-best-practices"></a>Najlepsze rozwiązania w systemie operacyjnym

- Wyłącz szyfrowanie systemu Windows Server i funkcji deduplikacji systemu plików NTFS.
- Wyłącz defragmentacji systemu Windows Server w przypadku woluminów StorSimple.
- Wyłączenie indeksowania systemu Windows Server, w przypadku woluminów StorSimple.
- Uruchomienie skanowania antywirusowego na hoście źródłowym (ale nie dla woluminów StorSimple).
- Wyłączyć domyślną [konserwacji systemu Windows Server](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx) w Menedżerze zadań. Użyć jednego z następujących sposobów:
  - Wyłącz konfiguratora konserwacji w harmonogramie zadań Windows.
  - Pobierz [PsExec](https://technet.microsoft.com/sysinternals/bb897553.aspx) Windows Sysinternals. Po pobraniu programu PsExec, uruchom program Windows PowerShell jako administrator, a następnie wpisz:
    ```powershell
    psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
    ```

### <a name="storsimple-best-practices"></a>Najlepsze rozwiązania StorSimple

-   Pamiętaj, że urządzenie StorSimple zostanie zaktualizowany i będzie [Update 3 lub nowszy](storsimple-install-update-3.md).
-   Odizolowanego ruchu iSCSI i chmury. Za pomocą połączeń iSCSI dedykowany dla ruchu między StorSimple i Utwórz kopię zapasową serwera.
-   Upewnij się że urządzenia StorSimple jest dedykowany docelowy kopii zapasowej. Mieszane obciążenia nie są obsługiwane, ponieważ wpływają na Twoje RTO i RPO.

### <a name="netbackup-best-practices"></a>Najlepsze rozwiązania NetBackup

-   Baza danych NetBackup powinien być lokalny dla serwera i nie znajdują się na woluminie StorSimple.
-   Odzyskiwania po awarii należy wykonać kopię zapasową bazy danych NetBackup woluminu StorSimple.
-   Obsługujemy NetBackup pełnych i przyrostowych kopii zapasowych (nazywane również różnicowe przyrostowe kopie zapasowe w NetBackup) dla tego rozwiązania. Firma Microsoft zaleca, nie używaj syntetyczne i zbiorcze przyrostowych kopii zapasowych.
-   Pliki kopii zapasowej danych może zawierać tylko dane dotyczące określonego zadania. Na przykład dołącza bez nośnika przez różne zadania są dozwolone.

Najnowsze ustawienia NetBackup i najlepsze rozwiązania dotyczące wdrażania tych wymagań znajdują się w dokumentacji NetBackup w [www.veritas.com](https://www.veritas.com).


## <a name="retention-policies"></a>Zasady przechowywania

Jednym z najbardziej powszechne typy zasad przechowywania kopii zapasowych jest zasad dziadek ojcem i syn (GFS). W zasadzie GFS przyrostowa kopia zapasowa jest wykonywana raz dziennie i pełne kopie zapasowe są wykonywane co tydzień i co miesiąc. Woluminy warstwowe powoduje zasad to sześć StorSimple: jeden wolumin zawiera tygodniowe, miesięczne i roczne pełne kopie zapasowe; inne woluminy pięć przechowywania codziennych przyrostowych kopii zapasowych.

W poniższym przykładzie używamy rotację GFS. W przykładzie założono, że:

-   Deduplikacją bez lub skompresowane dane są używane.
-   Pełne kopie zapasowe są 1 TiB.
-   Codzienne przyrostowe kopie zapasowe są 500 GiB.
-   Cztery cotygodniowe kopie zapasowe są przechowywane przez jeden miesiąc.
-   Dwanaście comiesięczne kopie zapasowe są przechowywane przez rok.
-   Jeden corocznej kopii zapasowej jest zachowywana przez okres 10 lat.

Na podstawie poprzedniego założeń tworzenie TiB 26 StorSimple warstwowego woluminu miesięcznych i rocznych pełnych kopii zapasowych. Tworzenie 5 TiB StorSimple warstwowego woluminu dla każdego przyrostowe codzienne kopie zapasowe.

| Typ kopii zapasowej przechowywania | Rozmiar (TiB) | Mnożnik GFS\* | Całkowita pojemność (TiB)  |
|---|---|---|---|
| Tygodniowe pełne | 1 | 4  | 4 |
| Codziennych przyrostowych | 0,5 | 20 (cykle równą liczbę tygodni na miesiąc) | 12 (2 dla dodatkowego przydziału) |
| Pełne miesięczne | 1 | 12 | 12 |
| Pełne roczne | 1  | 10 | 10 |
| Wymaganie GFS |   | 38 |   |
| Dodatkowego przydziału  | 4  |   | 42 łączna liczba wymagań GFS  |

\* Mnożnik GFS jest liczba kopii, które należy chronić i zachować zgodnie z wymaganiami zasady tworzenia kopii zapasowej.

## <a name="set-up-netbackup-storage"></a>Konfigurowanie magazynu NetBackup

### <a name="to-set-up-netbackup-storage"></a>Aby skonfigurować Magazyn NetBackup

1.  W konsoli administracyjnej NetBackup wybierz **multimediów i zarządzania urządzeniami** > **urządzeń** > **pul dysków**. W Kreatorze konfiguracji dysku w puli, wybierz typ serwera magazynu **AdvancedDisk**, a następnie wybierz pozycję **dalej**.

    ![Konsola administracyjna NetBackup, Kreator konfiguracji puli dysków](./media/storsimple-configure-backup-target-using-netbackup/nbimage1.png)

2.  Wybierz swój serwer, a następnie wybierz **dalej**.

    ![NetBackup konsoli administracyjnej, wybierz serwer](./media/storsimple-configure-backup-target-using-netbackup/nbimage2.png)

3.  Wybierz woluminu StorSimple.

    ![Konsoli administracyjnej NetBackup wybrać dysku woluminu StorSimple](./media/storsimple-configure-backup-target-using-netbackup/nbimage3.png)

4.  Wprowadź nazwę docelowej kopii zapasowej, a następnie wybierz **dalej** > **dalej** aby zakończyć działanie kreatora.

5.  Przejrzyj ustawienia, a następnie wybierz **Zakończ**.

6.  Na końcu każdego przydziału woluminu, zmiany ustawień urządzenia pamięci masowej do dopasowania tych zalecane w [najlepsze rozwiązania dotyczące magazynu StorSimple i NetBackup](#best-practices-for-storsimple-and-netbackup).

7. Powtórz kroki 1 – 6, do momentu zakończenia przypisywania woluminy StorSimple.

    ![Konsola administracyjna NetBackup, konfiguracja dysku](./media/storsimple-configure-backup-target-using-netbackup/nbimage5.png)

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Konfigurowanie usługi StorSimple jako podstawowy cel kopii zapasowej

> [!NOTE]
> Przywracanie danych z kopii zapasowej, który warstwowe chmury wystąpić szybkością chmury.

Na poniższej ilustracji pokazano mapowanie typowe woluminu do zadania tworzenia kopii zapasowej. W takim przypadku wszystkie cotygodniowe kopie zapasowe mapowania dysku pełną sobota i przyrostowe kopie zapasowe mapowania dysków przyrostowe od poniedziałku do piątku. Wszystko, czego kopii zapasowych i przywracanie danych pochodzą z magazynu StorSimple warstwowego woluminu.

![Diagram logiczny konfiguracji podstawowy cel kopii zapasowej](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>Magazyn StorSimple jako podstawowy cel kopii zapasowej GFS zaplanować przykład

Oto przykład planu obrotu GFS cztery tygodnie, miesięczne i roczne:

| Typ częstotliwości/kopia zapasowa | Pełne | Przyrostowa (1 – 5 dni)  |   
|---|---|---|
| Co tydzień (1 – 4 tygodnie) | Sobota | Od poniedziałku do piątku |
| Co miesiąc  | Sobota  |   |
| Rocznie | Sobota  |   |

## <a name="assigning-storsimple-volumes-to-a-netbackup-backup-job"></a>Przypisywanie woluminów StorSimple do zadania tworzenia kopii zapasowej NetBackup

Poniższa sekwencja przyjęto założenie, że NetBackup i host docelowy są skonfigurowane zgodnie z wytycznymi NetBackup agenta.

### <a name="to-assign-storsimple-volumes-to-a-netbackup-backup-job"></a>Aby przypisać woluminy StorSimple do zadania tworzenia kopii zapasowej NetBackup

1. W konsoli administracyjnej NetBackup wybierz **zarządzania NetBackup**, kliknij prawym przyciskiem myszy **zasady**, a następnie wybierz pozycję **nowe zasady**.

   ![Konsola administracyjna NetBackup, Utwórz nowe zasady](./media/storsimple-configure-backup-target-using-netbackup/nbimage6.png)

2. W **Add New Policy** okno dialogowe, wprowadź nazwę zasad a następnie wybierz **Użyj Kreatora konfiguracji zasad** pole wyboru. Kliknij przycisk **OK**.

   ![Konsola administracyjna NetBackup, Dodaj okno dialogowe nowych zasad](./media/storsimple-configure-backup-target-using-netbackup/nbimage7.png)

3. W Kreatorze konfiguracji zasad tworzenia kopii zapasowych wybiera typ kopii zapasowej, a następnie wybierz **dalej**.

   ![NetBackup konsoli administracyjnej, wybierz typ kopii zapasowej](./media/storsimple-configure-backup-target-using-netbackup/nbimage8.png)

4. Aby ustawić typ zasad, wybierz **standardowa**, a następnie wybierz pozycję **dalej**.

   ![Konsoli administracyjnej NetBackup typ zasad wybierz opcję](./media/storsimple-configure-backup-target-using-netbackup/nbimage9.png)

5. Wybierz hosta, wybierz pozycję **wykrywanie systemu operacyjnego klienta** pole wyboru, a następnie wybierz pozycję **Dodaj**. Wybierz opcję **Dalej**.

   ![Konsoli administracyjnej NetBackup listę klientów w nowych zasad](./media/storsimple-configure-backup-target-using-netbackup/nbimage10.png)

6. Wybierz dyski, które chcesz utworzyć kopię zapasową.

   ![Konsoli administracyjnej NetBackup opcje tworzenia kopii zapasowej dla nowych zasad](./media/storsimple-configure-backup-target-using-netbackup/nbimage11.png)

7. Wybierz częstotliwość i przechowywania wartości, które spełniają wymagania rotacji kopii zapasowej.

   ![Konsola administracyjna NetBackup, częstotliwość wykonywania kopii zapasowych i obrót dla nowych zasad](./media/storsimple-configure-backup-target-using-netbackup/nbimage12.png)

8. Wybierz **dalej** > **dalej** > **Zakończ**.  Harmonogram można zmodyfikować po utworzeniu zasad.

9. Wybierz, aby rozwinąć zasad został właśnie utworzony, a następnie wybierz **harmonogramy**.

   ![Konsoli administracyjnej NetBackup harmonogramy dla nowych zasad](./media/storsimple-configure-backup-target-using-netbackup/nbimage13.png)

10. Kliknij prawym przyciskiem myszy **Inc różnicowej**, wybierz opcję **skopiować do nowego**, a następnie wybierz pozycję **OK**.

    ![Konsoli administracyjnej NetBackup harmonogram kopiowania do nowych zasad](./media/storsimple-configure-backup-target-using-netbackup/nbimage14.png)

11. Kliknij prawym przyciskiem myszy nowo utworzony harmonogram, a następnie wybierz **zmiany**.

12. Na **atrybuty** zaznacz **Przesłoń wybór magazynu zasad** pole wyboru, a następnie wybierz wolumin, od poniedziałku przyrostowe kopie zapasowe gdzie.

    ![Konsola administracyjna NetBackup, Zmień harmonogram](./media/storsimple-configure-backup-target-using-netbackup/nbimage15.png)

13. Na **Uruchom okno** , a następnie wybierz przedział czasu dla kopii zapasowych.

    ![Konsoli administracyjnej NetBackup okno rozpoczęcia zmiany](./media/storsimple-configure-backup-target-using-netbackup/nbimage16.png)

14. Kliknij przycisk **OK**.

15. Powtórz kroki od 10 do 14 dla każdej przyrostowej kopii zapasowej. Wybierz odpowiedni wolumin i harmonogram dla każdej kopii zapasowej, który tworzysz.

16. Kliknij prawym przyciskiem myszy **Inc różnicowej** zaplanować, a następnie usuń ją.

17. Zmodyfikuj harmonogram pełnego stosownie do potrzeb kopii zapasowej.

    ![Konsoli administracyjnej NetBackup zmiany pełnego harmonogramu](./media/storsimple-configure-backup-target-using-netbackup/nbimage17.png)

18. Zmiany w oknie uruchamiania.

    ![Konsoli administracyjnej NetBackup zmiany okna uruchamiania](./media/storsimple-configure-backup-target-using-netbackup/nbimage18.png)

19. Ostatecznego harmonogramu wygląda następująco:

    ![Konsoli administracyjnej NetBackup ostatecznego harmonogramu](./media/storsimple-configure-backup-target-using-netbackup/nbimage19.png)

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>Konfigurowanie usługi StorSimple jako dodatkowej cel kopii zapasowej

> [!NOTE]
>Przywracanie danych z kopii zapasowej, który warstwowe chmury wystąpić szybkością chmury.

W tym modelu musi mieć na nośniku magazynującym (inne niż StorSimple), która będzie służyć jako tymczasowa pamięć podręczna. Na przykład można użyć dublowanej macierzy niezależnych dysków (RAID) woluminu do miejsca, wejścia/wyjścia (We/Wy) i przepustowości. Zalecamy używanie RAID 5, 50 i 10.

Na poniższej ilustracji przedstawiono typowy przechowywania krótkoterminowego na woluminach lokalnych (do serwera) i długoterminowego przechowywania danych archiwów woluminów. W tym scenariuszu wszystkie kopie zapasowe Uruchom lokalny (serwer) woluminu RAID. Te kopie zapasowe są okresowo zduplikowane i archiwizowanie woluminów archiwów. Jest ważne, aby rozmiar woluminu RAID lokalnych (do serwera), dzięki czemu może obsługiwać krótkoterminowe przechowywanie pojemność i wydajność wymagań.

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>Magazyn StorSimple jako przykład GFS dodatkowej docelowy kopii zapasowej

![Magazyn StorSimple jako diagram logiczny dodatkowej docelowy kopii zapasowej](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetdiagram.png)

W poniższej tabeli przedstawiono sposób konfigurowania kopii zapasowych do uruchamiania w lokalnych i dyski StorSimple. Zawiera wymagania dotyczące poszczególnych i całkowitej pojemności.

### <a name="backup-configuration-and-capacity-requirements"></a>Konfiguracja kopii zapasowej i wymagania dotyczące wydajności

| Typ kopii zapasowej i przechowywanie | Skonfigurowanym magazynie | Rozmiar (TiB) | Mnożnik GFS | Całkowita pojemność\* (TiB) |
|---|---|---|---|---|
| Tydzień, 1 (pełnych i przyrostowych) |Dysk lokalny (krótkoterminowej)| 1 | 1 | 1 |
| Tygodnie StorSimple 2 – 4 |Usługa StorSimple dysku (długoterminowe) | 1 | 4 | 4 |
| Pełne miesięczne |Usługa StorSimple dysku (długoterminowe) | 1 | 12 | 12 |
| Pełne roczne |Usługa StorSimple dysku (długoterminowe) | 1 | 1 | 1 |
|Wymagany rozmiar woluminów GFS |  |  |  | 18*|

\* Całkowita pojemność obejmuje 17 dysków TiB StorSimple i 1 TiB lokalnego woluminu RAID.


### <a name="gfs-example-schedule-gfs-rotation-weekly-monthly-and-yearly-schedule"></a>Harmonogram przykład GFS: Obrót GFS tygodniowe, miesięczne i roczne harmonogramu

| Tydzień | Pełne | Przyrostowe dzień 1 | Przyrostowe dnia 2 | Przyrostowe dzień 3 | Dzień przyrostowe 4 | Dzień przyrostowe 5 |
|---|---|---|---|---|---|---|
| 1 tydzień | Lokalne woluminu RAID  | Lokalne woluminu RAID | Lokalne woluminu RAID | Lokalne woluminu RAID | Lokalne woluminu RAID | Lokalne woluminu RAID |
| Tydzień 2 | Tygodnie StorSimple 2 – 4 |   |   |   |   |   |
| Tydzień 3 | Tygodnie StorSimple 2 – 4 |   |   |   |   |   |
| Tydzień 4 | Tygodnie StorSimple 2 – 4 |   |   |   |   |   |
| Co miesiąc | Co miesiąc StorSimple |   |   |   |   |   |
| Rocznie | Co rok StorSimple  |   |   |   |   |   |


## <a name="assign-storsimple-volumes-to-a-netbackup-archive-and-duplication-job"></a>Przypisać woluminy StorSimple do zadania archiwum i duplikacji NetBackup

Ponieważ NetBackup oferuje szeroką gamę opcji zarządzania magazynem i nośników, firma Microsoft zaleca, należy skontaktować się z Veritas lub swoje Architekt NetBackup prawidłowo oceny wymagań dotyczących magazynu cyklu życia zasad (SLP).

Po zdefiniowaniu pul dysków początkowej, należy zdefiniować trzy zasady cyklu życia dodatkowego miejsca do magazynowania, łącznie z czterech zasad:
* LocalRAIDVolume
* StorSimpleWeek2 4
* StorSimpleMonthlyFulls
* StorSimpleYearlyFulls

### <a name="to-assign-storsimple-volumes-to-a-netbackup-archive-and-duplication-job"></a>Aby przypisać woluminy StorSimple do zadania archiwum i duplikacji NetBackup

1. W konsoli administracyjnej NetBackup wybierz **magazynu** > **zasady cyklu życia magazynu** > **nowe zasady cyklu życia magazynu**.

   ![Konsola administracyjna NetBackup, nowe zasady cyklu życia magazynu](./media/storsimple-configure-backup-target-using-netbackup/nbimage20.png)

2. Wprowadź nazwę dla migawki, a następnie wybierz **Dodaj**.

3. W **nową operację** dialogowym **właściwości** karcie dla **operacji**, wybierz opcję **kopii zapasowej**. Wybierz wartości dla **Magazyn docelowy**, **typ przechowywania**, i **okres przechowywania**. Kliknij przycisk **OK**.

   ![Konsoli administracyjnej NetBackup nowej operacji, okno dialogowe](./media/storsimple-configure-backup-target-using-netbackup/nbimage22.png)

   To definiuje pierwszą operacją kopii zapasowej i repozytorium.

4. Wybierz, aby wyróżnić poprzedniej operacji, a następnie wybierz **Dodaj**. W **operacji magazynu zmiany** okna dialogowego Wybierz wartości, które mają dla **Magazyn docelowy**, **typ przechowywania**, i **okres przechowywania**.

   ![Konsola administracyjna NetBackup, okno dialogowe zmiany operacji magazynu](./media/storsimple-configure-backup-target-using-netbackup/nbimage23.png)

5. Wybierz, aby wyróżnić poprzedniej operacji, a następnie wybierz **Dodaj**. W **nowe zasady cyklu życia magazynu** okna dialogowego Dodaj comiesięczne kopie zapasowe na rok.

   ![Konsola administracyjna NetBackup, okno dialogowe nowej zasady cyklu życia magazynu](./media/storsimple-configure-backup-target-using-netbackup/nbimage24.png)

6. Powtórz kroki 4 – 5, dopóki nie utworzysz zasady przechowywania SLP kompleksowe, które należy.

   ![Konsola administracyjna NetBackup, Dodaj zasady w oknie dialogowym nowej zasady cyklu życia magazynu](./media/storsimple-configure-backup-target-using-netbackup/nbimage25.png)

7. Po zdefiniowaniu SLP zasady przechowywania, w obszarze **zasad**, definiowanie zasad tworzenia kopii zapasowej, wykonując kroki opisane szczegółowo w [woluminów StorSimple przypisywanie do zadania tworzenia kopii zapasowej NetBackup](#assigning-storsimple-volumes-to-a-netbackup-backup-job).

8. W obszarze **harmonogramy**w **Zmień harmonogram** okno dialogowe, kliknij prawym przyciskiem myszy **pełne**, a następnie wybierz **zmiany**.

   ![Konsola administracyjna NetBackup, okno dialogowe Zmień harmonogram](./media/storsimple-configure-backup-target-using-netbackup/nbimage26.png)

9. Wybierz **Przesłoń wybór magazynu zasad** pole wyboru, a następnie wybierz zasady przechowywania SLP, który został utworzony w krokach 1 – 6.

   ![Konsola administracyjna NetBackup, wybór magazynu zasad przesłonięcia](./media/storsimple-configure-backup-target-using-netbackup/nbimage27.png)

10. Wybierz **OK**, a następnie powtórz dla przyrostowych harmonogram tworzenia kopii zapasowych.

    ![Konsola administracyjna NetBackup, okno dialogowe Zmień harmonogram tworzenia przyrostowych kopii zapasowych](./media/storsimple-configure-backup-target-using-netbackup/nbimage28.png)


| Typ kopii zapasowej przechowywania | Rozmiar (TiB) | Mnożnik GFS\* | Całkowita pojemność (TiB)  |
|---|---|---|---|
| Tygodniowe pełne |  1  |  4 | 4  |
| Codziennych przyrostowych  | 0,5  | 20 (cykle jest równa liczbie tygodni na miesiąc) | 12 (2 dla dodatkowego przydziału) |
| Pełne miesięczne  | 1 | 12 | 12 |
| Pełne roczne | 1  | 10 | 10 |
| Wymaganie GFS  |     |     | 38 |
| Dodatkowego przydziału  | 4  |    | 42 łączna liczba wymagań GFS |

\* Mnożnik GFS jest liczba kopii, które należy chronić i zachować zgodnie z wymaganiami zasady tworzenia kopii zapasowej.

## <a name="storsimple-cloud-snapshots"></a>Migawki w chmurze StorSimple

Migawki w chmurze StorSimple chronić dane, które znajdują się w urządzeniu StorSimple. Tworzenie migawki w chmurze jest odpowiednikiem wysyłania lokalnej kopii zapasowej taśmy, które mają mechanizm poza siedzibą firmy. Jeśli używasz platformy Azure dla magazynu geograficznie nadmiarowego, tworzenia migawki w chmurze jest odpowiednikiem wysyłania taśm kopii zapasowych z wieloma lokacjami. Jeśli potrzebujesz przywrócić urządzenie po awarii, może przełączyć innego urządzenia StorSimple online i do trybu failover. Po włączeniu trybu failover będzie można uzyskiwać dostęp do danych (szybkością chmury) z najnowszej migawki w chmurze.

W poniższej sekcji opisano sposób tworzenia krótkiej skryptu do uruchamiania i usuwania migawki w chmurze StorSimple podczas tworzenia kopii zapasowej przetwarzania końcowego.

> [!NOTE]
> Migawki tworzone ręcznie lub programowo nie wykonuj zasady wygasania migawek StorSimple. Migawki te muszą zostać ręcznie lub programowo usunięte.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Uruchom i Usuń migawki w chmurze za pomocą skryptu

> [!NOTE]
> Dokładnie oceń zgodności i następstwa przechowywania danych przed usunięciem migawek StorSimple. Aby uzyskać więcej informacji o sposobie uruchamiania skryptu po utworzeniu kopii zapasowej, zobacz [dokumentacji NetBackup](http://www.veritas.com/docs/000094423).

### <a name="backup-lifecycle"></a>Cykl życia tworzenia kopii zapasowej

![Wykonaj kopię zapasową diagram cyklu życia](./media/storsimple-configure-backup-target-using-netbackup/backuplifecycle.png)

### <a name="requirements"></a>Wymagania

-   Serwer, który uruchamia skrypt musi mieć dostęp do zasobów w chmurze Azure.
-   Konto użytkownika musi mieć odpowiednie uprawnienia.
-   Zasady kopii zapasowych usługi StorSimple ze skojarzonych woluminów StorSimple należy skonfigurować, ale nie jest włączona.
-   Musisz mieć nazwę zasobu usługi StorSimple, klucz rejestracji, identyfikator urządzenia zasady dotyczące nazwy i tworzenia kopii zapasowej.

### <a name="to-start-or-delete-a-cloud-snapshot"></a>Aby uruchomić lub usunąć migawkę w chmurze

1. [Zainstalowanie programu Azure PowerShell](/powershell/azure/overview).
2. Pobierz i skonfiguruj [CloudSnapshots.ps1 Zarządzaj](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Manage-CloudSnapshots.ps1) skrypt programu PowerShell.
3. Na serwerze, który uruchamia skrypt Uruchom program PowerShell jako administrator. Upewnij się, że uruchomieniu skryptu za pomocą `-WhatIf $true` aby zobaczyć, co spowodowało skrypt spowoduje, że. Po zakończeniu sprawdzania poprawności, należy przekazać `-WhatIf $false`. Uruchom poniższe polecenie:
   ```powershell
   .\Manage-CloudSnapshots.ps1 -SubscriptionId [Subscription Id] -TenantId [Tenant ID] -ResourceGroupName [Resource Group Name] -ManagerName [StorSimple Device Manager Name] -DeviceName [device name] -BackupPolicyName [backup policyname] -RetentionInDays [Retention days] -WhatIf [$true or $false]
   ```
4. Dodaj skrypt zadania tworzenia kopii zapasowych w NetBackup. Aby to zrobić, należy edytować NetBackup Opcje zadania przetwarzania wstępnego i przetwarzanie końcowe poleceń.

> [!NOTE]
> Firma Microsoft zaleca uruchamianie usługi StorSimple cloud snapshot zasad tworzenia kopii zapasowej jako przetwarzania końcowego skryptu na końcu codzienne zadania tworzenia kopii zapasowej. Aby uzyskać więcej informacji na temat tworzenia kopii zapasowej i przywracania środowiska tworzenia kopii zapasowej aplikacji pomagają spełniać wymagania celu punktu odzyskiwania i cel czasu odzyskiwania usługi skontaktuj się z Twojego Architekt kopii zapasowej.

## <a name="storsimple-as-a-restore-source"></a>Magazyn StorSimple jako źródło przywracania

Przywraca z pracy urządzenia StorSimple, takich jak przywracanie z dowolnym urządzeniem magazynu blokowego. Przywracanie danych, które jest warstwowe w chmurze występuje szybkością chmury. Dla danych lokalnych przywraca wystąpić z szybkością dysku lokalnego urządzenia. Aby uzyskać informacje o tym, jak przeprowadzić przywracanie, zobacz [dokumentacji NetBackup](http://www.veritas.com/docs/000094423). Firma Microsoft zaleca odpowiadają NetBackup przywracania najlepszych rozwiązań.

## <a name="storsimple-failover-and-disaster-recovery"></a>Usługa StorSimple trybu failover i odzyskiwanie po awarii

> [!NOTE]
> Dla scenariuszy docelowy kopii zapasowej urządzenia StorSimple w chmurze nie jest obsługiwany jako miejsce docelowe przywracania.

Awarii może być spowodowany różnych czynników. W poniższej tabeli wymieniono typowe scenariusze odzyskiwania po awarii.

| Scenariusz | Wpływ | Jak odzyskać | Uwagi |
|---|---|---|---|
| Awaria urządzenia StorSimple | Operacje tworzenia kopii zapasowych i przywracania są przerywane. | Zamień urządzenia nie powiodło się i wykonać [StorSimple trybu failover i odzyskiwanie po awarii](storsimple-device-failover-disaster-recovery.md). | Jeśli musisz wykonać przywracanie po odzyskaniu urządzenia zestawów roboczych pełnych danych są pobierane z chmury do nowego urządzenia. Wszystkie operacje są szybkością chmury. Indeks i wykazu ponowne skanowanie proces może spowodować wszystkie zestawy kopii zapasowych do skanowania i pobierane z warstwy chmury do warstwy urządzenia lokalnego, która może być czasochłonne. |
| Błąd serwera NetBackup | Operacje tworzenia kopii zapasowych i przywracania są przerywane. | Odbuduj serwer kopii zapasowych i przywracania bazy danych. | Należy odbudować ani przywrócić serwer NetBackup w lokacji odzyskiwania po awarii. Najnowszy punkt przywracania bazy danych. Jeśli przywróconej bazy danych NetBackup nie jest zsynchronizowana z najnowszej zadań tworzenia kopii zapasowej, indeksowania i katalogowanie jest wymagane. Ten indeks i wykazu ponowne skanowanie proces może spowodować wszystkie zestawy kopii zapasowych do skanowania i pobierane z warstwy chmury w warstwie lokalnej urządzenia. Dzięki temu można dalej czasochłonnymi. |
| Awaria lokacji, która powoduje utratę Utwórz kopię zapasową serwera i usługi StorSimple | Operacje tworzenia kopii zapasowych i przywracania są przerywane. | Najpierw przywrócić StorSimple, a następnie przywróć NetBackup. | Najpierw przywrócić StorSimple, a następnie przywróć NetBackup. Jeśli musisz wykonać przywracanie po odzyskaniu urządzenia zestawów roboczych pełnych danych są pobierane z chmury do nowego urządzenia. Wszystkie operacje są szybkością chmury. |

## <a name="references"></a>Dokumentacja

W tym artykule przywoływane następujące dokumenty:

- [StorSimple Wielościeżkowe We/Wy Instalatora](storsimple-configure-mpio-windows-server.md)
- [Scenariusze usługi Storage: Alokowanie elastyczne](https://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [Za pomocą GPT dysków](https://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [Konfigurowanie kopii w tle dla folderów udostępnionych](https://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej na temat [przywrócenie z zestawu kopii zapasowych](storsimple-restore-from-backup-set-u2.md).
- Dowiedz się więcej na temat sposobu wykonywania [urządzenia trybu failover i odzyskiwanie po awarii](storsimple-device-failover-disaster-recovery.md).
