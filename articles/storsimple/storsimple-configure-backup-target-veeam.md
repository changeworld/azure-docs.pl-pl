---
title: StorSimple serii 8000 jako miejsce docelowe tworzenia kopii zapasowych z veeam | Dokumenty firmy Microsoft
description: W tym artykule opisano docelową konfigurację kopii zapasowej StorSimple z usługą Veeam.
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
ms.date: 12/06/2016
ms.author: matd
ms.openlocfilehash: 3ebf464fed1480e7452f246f04f3906faf0dd219
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67875299"
---
# <a name="storsimple-as-a-backup-target-with-veeam"></a>StorSimple jako miejsce docelowe tworzenia kopii zapasowych z veeam

## <a name="overview"></a>Omówienie

Azure StorSimple to rozwiązanie magazynu w chmurze hybrydowej firmy Microsoft. StorSimple rozwiązuje złożoność wykładniczego wzrostu danych przy użyciu konta usługi Azure Storage jako rozszerzenia rozwiązania lokalnego i automatycznie warstwowania danych w magazynie lokalnym i magazynie w chmurze.

W tym artykule omówimy integrację StorSimple z veeam i najlepsze rozwiązania dotyczące integracji obu rozwiązań. Poczyniliśmy również zalecenia dotyczące konfigurowania firmy Veeam w celu jak najlepszej integracji z StorSimple. Odraczamy najlepsze praktyki firmy Veeam, architektów kopii zapasowych i administratorów, aby uzyskać najlepszy sposób skonfigurowania rozwiązania Veeam w celu spełnienia indywidualnych wymagań dotyczących tworzenia kopii zapasowych i umów dotyczących poziomu usług(SLA).

Chociaż zilustrujemy kroki konfiguracji i kluczowe pojęcia, w tym artykule nie jest w żadnym wypadku instrukcja konfiguracji krok po kroku lub instalacji. Zakładamy, że podstawowe składniki i infrastruktura są sprawne i gotowe do obsługi koncepcji, które opisujemy.

### <a name="who-should-read-this"></a>Kto powinien to przeczytać?

Informacje zawarte w tym artykule będą najbardziej przydatne dla administratorów kopii zapasowych, administratorów pamięci masowej i architektów pamięci masowej posiadających wiedzę na temat pamięci masowej, systemu Windows Server 2012 R2, Ethernet, usług w chmurze i rozwiązania Veeam.

### <a name="supported-versions"></a>Obsługiwane wersje

-   Veeam 9 i nowsze wersje
-   [StorSimple Aktualizacja 3 i nowsze wersje](storsimple-overview.md#storsimple-workload-summary)


## <a name="why-storsimple-as-a-backup-target"></a>Dlaczego StorSimple jako miejsce docelowe kopii zapasowej?

StorSimple jest dobrym wyborem dla celu tworzenia kopii zapasowych, ponieważ:

-   Zapewnia standardową, lokalną pamięć masową dla aplikacji do tworzenia kopii zapasowych do użycia jako miejsce docelowe szybkiej kopii zapasowej, bez żadnych zmian. StorSimple służy również do szybkiego przywracania ostatnich kopii zapasowych.
-   Jego warstwa w chmurze jest bezproblemowo zintegrowana z kontem usługi Azure w chmurze, aby korzystać z efektywnego kosztowo usługi Azure Storage.
-   Automatycznie zapewnia magazyn pozalokalem do odzyskiwania po awarii.


## <a name="key-concepts"></a>Kluczowe pojęcia

Podobnie jak w przypadku każdego rozwiązania pamięci masowej, dokładna ocena wydajności pamięci masowej rozwiązania, SLA, szybkości zmian i potrzeb w zakresie wzrostu pojemności ma kluczowe znaczenie dla powodzenia. Główną ideą jest to, że wprowadzając warstwę chmury, czasy dostępu i przepływności do chmury odgrywają podstawową rolę w zdolności StorSimple do wykonywania swojej pracy.

StorSimple został zaprojektowany w celu zapewnienia magazynu dla aplikacji, które działają na dobrze zdefiniowanym zestawie roboczym danych (dane na gorąco). W tym modelu zestaw roboczy danych jest przechowywany w warstwach lokalnych, a pozostałe niedziałające/cold/archived zestaw danych jest warstwowy do chmury. Ten model jest reprezentowany na poniższej ilustracji. Prawie płaska zielona linia reprezentuje dane przechowywane w warstwach lokalnych urządzenia StorSimple. Czerwona linia reprezentuje całkowitą ilość danych przechowywanych w storsimple rozwiązanie we wszystkich warstwach. Odstęp między płaską zieloną linią a wykładniczą czerwoną krzywą reprezentuje całkowitą ilość danych przechowywanych w chmurze.

**StorSimple warstwowy**
![diagram warstwowy StorSimple](./media/storsimple-configure-backup-target-using-veeam/image1.jpg)

Mając na uwadze tę architekturę, przekonasz się, że StorSimple idealnie nadaje się do działania jako miejsce docelowe tworzenia kopii zapasowych. StorSimple służy do:

-   Wykonaj najczęściej przywracane dane z lokalnego zestawu roboczego danych.
-   Użyj chmury do odzyskiwania po awarii poza obiektem i starszych danych, gdzie przywracanie jest rzadsze.

## <a name="storsimple-benefits"></a>Korzyści StorSimple

StorSimple zapewnia rozwiązanie lokalne, które jest bezproblemowo zintegrowane z platformą Microsoft Azure, korzystając z bezproblemowego dostępu do magazynu lokalnego i w chmurze.

StorSimple używa automatycznego warstwowania między urządzeniem lokalnym, które ma urządzenie półprzewodnikowe (SSD) i magazyn SCSI (SAS) podłączony szeregowo, a usługą Azure Storage. Automatyczne warstwowanie utrzymuje często dostępne dane lokalne, w warstwach SSD i SAS. Przenosi rzadko dostępne dane do usługi Azure Storage.

StorSimple oferuje następujące korzyści:

-   Unikalne algorytmy deduplikacji i kompresji, które wykorzystują chmurę do osiągnięcia bezprecedensowych poziomów deduplikacji
-   Wysoka dostępność
-   Replikacja geograficzna przy użyciu replikacji geograficznej platformy Azure
-   Integracja z Azure
-   Szyfrowanie danych w chmurze
-   Ulepszone odzyskiwanie po awarii i zgodność z przepisami

Chociaż StorSimple przedstawia dwa główne scenariusze wdrażania (podstawowy obiekt docelowy kopii zapasowej i pomocniczy obiekt docelowy kopii zapasowej), zasadniczo jest to zwykły, blok urządzenia magazynującego. StorSimple wykonuje wszystkie kompresji i deduplikacji. Bezproblemowo wysyła i pobiera dane między chmurą a aplikacją i systemem plików.

Aby uzyskać więcej informacji na temat storsimple, zobacz [StorSimple serii 8000: Rozwiązanie do przechowywania w chmurze hybrydowej](storsimple-overview.md). Ponadto, można przejrzeć [techniczne StorSimple 8000 specyfikacji serii](storsimple-technical-specifications-and-compliance.md).

> [!IMPORTANT]
> Używanie urządzenia StorSimple jako docelowego tworzenia kopii zapasowych jest obsługiwane tylko dla storsimple 8000 Update 3 i nowszych wersji.

## <a name="architecture-overview"></a>Omówienie architektury

W poniższych tabelach przedstawiono początkowe wskazówki dotyczące modelu urządzenia do architektury.

**Pojemności StorSimple dla pamięci masowej lokalnej i w chmurze**

| Pojemność magazynu | 8100 | 8600 |
|---|---|---|
| Pojemność magazynu lokalnego | &lt;10 TiB\*  | &lt;20 tib\*  |
| Pojemność pamięci masowej w chmurze | &gt;200 tib\* | &gt;500 TiB\* |

\*Rozmiar magazynu zakłada, że nie deduplikacji lub kompresji.

**Pojemności StorSimple dla podstawowych i pomocniczych kopii zapasowych**

| Scenariusz tworzenia kopii zapasowych  | Pojemność magazynu lokalnego  | Pojemność pamięci masowej w chmurze  |
|---|---|---|
| Podstawowa kopia zapasowa  | Ostatnie kopie zapasowe przechowywane w lokalnej pamięci masowej w celu szybkiego odzyskiwania w celu osiągnięcia celu punktu odzyskiwania (RPO) | Historia kopii zapasowych (RPO) mieści się w pojemności chmury |
| Pomocnicza kopia zapasowa | Dodatkowa kopia danych kopii zapasowej może być przechowywana w pojemności chmury  | Nie dotyczy  |

## <a name="storsimple-as-a-primary-backup-target"></a>StorSimple jako podstawowy obiekt docelowy kopii zapasowej

W tym scenariuszu StorSimple woluminy są prezentowane do aplikacji kopii zapasowej jako jedyne repozytorium kopii zapasowych. Na poniższej ilustracji przedstawiono architekturę rozwiązania, w której wszystkie kopie zapasowe używają woluminów warstwowych StorSimple do tworzenia kopii zapasowych i przywracania.

![StorSimple jako podstawowy diagram logiczny docelowy kopii zapasowej](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Podstawowe docelowe kroki logiczne kopii zapasowej

1.  Serwer kopii zapasowej kontaktuje się z docelowym agentem kopii zapasowej, a agent kopii zapasowej przesyła dane do serwera kopii zapasowej.
2.  Serwer kopii zapasowej zapisuje dane na woluminach warstwowych StorSimple.
3.  Serwer kopii zapasowej aktualizuje bazę danych katalogu, a następnie kończy zadanie tworzenia kopii zapasowej.
4.  Skrypt migawki wyzwala Menedżera migawek chmury StorSimple (uruchamianie lub usuwanie).
5.  Serwer kopii zapasowych usuwa wygasłe kopie zapasowe na podstawie zasad przechowywania.

### <a name="primary-target-restore-logical-steps"></a>Podstawowe kroki logiczne przywracania obiektu docelowego

1.  Serwer kopii zapasowej rozpoczyna przywracanie odpowiednich danych z repozytorium magazynu.
2.  Agent kopii zapasowej odbiera dane z serwera kopii zapasowej.
3.  Serwer kopii zapasowej kończy zadanie przywracania.

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple jako dodatkowy obiekt docelowy kopii zapasowej

W tym scenariuszu StorSimple woluminów głównie są używane do długoterminowego przechowywania lub archiwizacji.

Na poniższej ilustracji przedstawiono architekturę, w której początkowe kopie zapasowe i przywraca docelowe wolumin o wysokiej wydajności. Te kopie zapasowe są kopiowane i archiwizowane do storsimple wolumin warstwowy zgodnie z ustalonym harmonogramem.

Ważne jest, aby rozmiar woluminu o wysokiej wydajności, tak aby mógł obsługiwać możliwości przechowywania zasad i wymagań dotyczących wydajności.

![StorSimple jako pomocniczy diagram logiczny docelowego kopii zapasowej](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>Kroki logiczne pomocniczej kopii zapasowej docelowej

1.  Serwer kopii zapasowej kontaktuje się z docelowym agentem kopii zapasowej, a agent kopii zapasowej przesyła dane do serwera kopii zapasowej.
2.  Serwer kopii zapasowej zapisuje dane w magazynie o wysokiej wydajności.
3.  Serwer kopii zapasowej aktualizuje bazę danych katalogu, a następnie kończy zadanie tworzenia kopii zapasowej.
4.  Serwer kopii zapasowych kopiuje kopie zapasowe do StorSimple na podstawie zasad przechowywania.
5.  Skrypt migawki wyzwala Menedżera migawek chmury StorSimple (uruchamianie lub usuwanie).
6.  Serwer kopii zapasowych usuwa wygasłe kopie zapasowe na podstawie zasad przechowywania.

### <a name="secondary-target-restore-logical-steps"></a>Kroki logiczne przywracania obiektu docelowego pomocniczego

1.  Serwer kopii zapasowej rozpoczyna przywracanie odpowiednich danych z repozytorium magazynu.
2.  Agent kopii zapasowej odbiera dane z serwera kopii zapasowej.
3.  Serwer kopii zapasowej kończy zadanie przywracania.

## <a name="deploy-the-solution"></a>Wdrażanie rozwiązania

Wdrożenie rozwiązania wymaga trzech kroków:

1. Przygotuj infrastrukturę sieciową.
2. Wdrażanie urządzenia StorSimple jako miejsce docelowe kopii zapasowej.
3. Wdrażanie rozwiązania Veeam.

Każdy krok jest szczegółowo omówiony w poniższych sekcjach.

### <a name="set-up-the-network"></a>Konfigurowanie sieci

Ponieważ StorSimple jest rozwiązaniem, które jest zintegrowane z chmurą platformy Azure, StorSimple wymaga aktywnego i działającego połączenia z chmurą platformy Azure. To połączenie jest używane do operacji, takich jak migawki w chmurze, zarządzanie danymi i transfer metadanych, a także do warstwy starszych, mniej dostępnych danych do magazynu w chmurze platformy Azure.

Aby rozwiązanie działało optymalnie, zaleca się przestrzeganie tych najlepszych rozwiązań dotyczących sieci:

-   Łącze łączące warstwę StorSimple z platformą Azure musi spełniać wymagania dotyczące przepustowości. Osiągnij to, stosując wymagany poziom jakości usług (QoS) do przełączników infrastruktury, aby dopasować swoje umowy SLA celu punktu odzyskiwania i czasu odzyskiwania (RTO).
-   Maksymalne opóźnienia dostępu do magazynu obiektów Blob platformy Azure powinny wynosić około 80 ms.

### <a name="deploy-storsimple"></a>Wdrażanie StorSimple

Aby uzyskać wskazówki dotyczące wdrażania storsimple krok po kroku, zobacz [Wdrażanie lokalnego urządzenia StorSimple](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-veeam"></a>Wdrażanie rozwiązania Veeam

Aby zapoznać się z najlepszymi rozwiązaniami w zakresie instalacji veeam, zobacz [Najważniejsze wskazówki dotyczące tworzenia kopii zapasowych & replikacji veeam](https://bp.veeam.expert/)i przeczytaj podręcznik użytkownika w [Centrum pomocy veeam (Dokumentacja techniczna).](https://www.veeam.com/documentation-guides-datasheets.html)

## <a name="set-up-the-solution"></a>Konfigurowanie rozwiązania

W tej sekcji zademonstrujemy niektóre przykłady konfiguracji. Poniższe przykłady i zalecenia ilustrują najbardziej podstawowe i podstawowe wdrożenie. Ta implementacja może nie dotyczyć bezpośrednio określonych wymagań dotyczących tworzenia kopii zapasowych.

### <a name="set-up-storsimple"></a>Konfigurowanie StorSimple

| Zadania wdrażania StorSimple  | Dodatkowe komentarze |
|---|---|
| Wdrażanie lokalnego urządzenia StorSimple. | Obsługiwane wersje: Aktualizacja 3 i nowsze wersje. |
| Włącz miejsce docelowe kopii zapasowej. | Użyj tych poleceń, aby włączyć lub wyłączyć tryb docelowy kopii zapasowej i uzyskać status. Aby uzyskać więcej informacji, zobacz [Łączenie zdalnie z urządzeniem StorSimple](storsimple-remote-connect.md).</br> Aby włączyć tryb `Set-HCSBackupApplianceMode -enable`tworzenia kopii zapasowej: . </br> Aby wyłączyć tryb `Set-HCSBackupApplianceMode -disable`tworzenia kopii zapasowej: . </br> Aby uzyskać bieżący stan ustawień `Get-HCSBackupApplianceMode`trybu tworzenia kopii zapasowej: . |
| Utwórz wspólny kontener woluminu dla woluminu, który przechowuje dane kopii zapasowej. Wszystkie dane w kontenerze woluminów są deduplikowane. | StorSimple kontenery woluminów zdefiniować domeny deduplikacji.  |
| Utwórz woluminy StorSimple. | Tworzenie woluminów o rozmiarach zbliżonych do przewidywanego użycia, ponieważ rozmiar woluminu wpływa na czas trwania migawki w chmurze. Aby uzyskać informacje dotyczące rozmiaru woluminu, przeczytaj artykuł o [zasadach przechowywania](#retention-policies).</br> </br> Użyj storsimple woluminów warstwowych i zaznacz pole wyboru **Użyj tego woluminu dla rzadziej używanych danych archiwalnych.** </br> Używanie tylko woluminów przypiętych lokalnie nie jest obsługiwane. |
| Utwórz unikatową zasadę tworzenia kopii zapasowych StorSimple dla wszystkich woluminów docelowych kopii zapasowej. | Zasady tworzenia kopii zapasowych StorSimple definiują grupę spójności woluminów. |
| Wyłącz harmonogram w miarę wygasania migawek. | Migawki są wyzwalane jako operacja przetwarzania końcowego. |

### <a name="set-up-the-host-backup-server-storage"></a>Konfigurowanie magazynu serwera kopii zapasowych hosta

Skonfiguruj magazyn serwera kopii zapasowych hosta zgodnie z tymi wytycznymi:  

- Nie używaj woluminów łączonych (utworzonych przez zarządzanie dyskami systemu Windows). Woluminy łączone nie są obsługiwane.
- Sformatować woluminy przy użyciu systemu plików NTFS o rozmiarze jednostki alokacji 64 KB.
- Mapuj woluminy StorSimple bezpośrednio na serwerZe Veeam.
    - Użyj interfejsu iSCSI dla serwerów fizycznych.


## <a name="best-practices-for-storsimple-and-veeam"></a>Najważniejsze wskazówki dotyczące StorSimple i Veeam

Skonfiguruj rozwiązanie zgodnie z wytycznymi zawartymi w poniższych kilku sekcjach.

### <a name="operating-system-best-practices"></a>Najważniejsze wskazówki dotyczące systemu operacyjnego

- Wyłącz szyfrowanie i deduplikację systemu Windows Server dla systemu plików NTFS.
- Wyłącz defragmentację systemu Windows Server na woluminach StorSimple.
- Wyłącz indeksowanie systemu Windows Server na woluminach StorSimple.
- Uruchom skanowanie antywirusowe na hoście źródłowym (nie względem woluminów StorSimple).
- Wyłącz domyślną [konserwację systemu Windows Server](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx) w Menedżerze zadań. Zrób to w jeden z następujących sposobów:
  - Wyłącz konfigurator konserwacji w Harmonogramie zadań systemu Windows.
  - Pobierz [PsExec](https://technet.microsoft.com/sysinternals/bb897553.aspx) z Windows Sysinternals. Po pobraniu programu PsExec uruchom program Windows PowerShell jako administrator i wpisz:
    ```powershell
    psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
    ```

### <a name="storsimple-best-practices"></a>StorSimple najlepsze rozwiązania

-   Upewnij się, że urządzenie StorSimple jest zaktualizowane do [aktualizacji 3 lub nowszej](storsimple-install-update-3.md).
-   Izoluj iSCSI i ruch w chmurze. Użyj dedykowanych połączeń iSCSI dla ruchu między StorSimple a serwerem kopii zapasowej.
-   Upewnij się, że urządzenie StorSimple jest dedykowanym celem tworzenia kopii zapasowych. Obciążenia mieszane nie są obsługiwane, ponieważ wpływają na rto i rpo.

### <a name="veeam-best-practices"></a>Najważniejsze wskazówki dotyczące rozwiązania veeam

-   Baza danych Veeam powinna być lokalna dla serwera i nie znajdować się na woluminie StorSimple.
-   Aby uzyskać odzyskiwanie po awarii, generuj zapas bazy danych Veeam na woluminie StorSimple.
-   Obsługujemy pełne i przyrostowe kopie zapasowe veeam dla tego rozwiązania. Zaleca się, aby nie używać syntetycznych i różnicowych kopii zapasowych.
-   Pliki danych kopii zapasowej powinny zawierać tylko dane dla określonego zadania. Na przykład żadne dołączanie multimediów w różnych zadaniach nie jest dozwolone.
-   Wyłącz weryfikację zadania. W razie potrzeby weryfikacja powinna być zaplanowana po wykonaniu ostatniego zadania tworzenia kopii zapasowej. Ważne jest, aby zrozumieć, że to zadanie wpływa na okno kopii zapasowej.
-   Włącz wstępną alokację nośnika.
-   Upewnij się, że przetwarzanie równoległe jest włączone.
-   Wyłącz kompresję.
-   Wyłącz deduplikację w zadaniu tworzenia kopii zapasowej.
-   Ustaw optymalizację na **LAN Target**.
-   Włącz **tworzenie aktywnej pełnej kopii zapasowej** (co 2 tygodnie).
-   W repozytorium kopii zapasowych skonfiguruj **opcję Użyj plików kopii zapasowej na maszynę wirtualną**.
-   Ustaw **użyj wielu strumieni przekazywania na zadanie** do **8** (dozwolone jest maksymalnie 16). Dostosuj tę liczbę w górę lub w dół na podstawie wykorzystania procesora CPU na urządzeniu StorSimple.

## <a name="retention-policies"></a>Zasady przechowywania

Jednym z najpopularniejszych typów zasad przechowywania kopii zapasowych jest zasady dziadek, ojciec i syn (GFS). W zasadach GFS przyrostowa kopia zapasowa jest wykonywana codziennie, a pełne kopie zapasowe są wykonywane co tydzień i co miesiąc. Ta zasada powoduje sześć storsimple woluminów warstwowych: jeden wolumin zawiera tygodniowe, miesięczne i roczne pełne kopie zapasowe; pozostałe pięć woluminów przechowuje dzienne przyrostowe kopie zapasowe.

W poniższym przykładzie używamy rotacji GFS. W przykładzie przyjęto następujące informacje:

-   Używane są dane nieduszczone lub skompresowane.
-   Pełne kopie zapasowe są 1 TiB każdy.
-   Codzienne przyrostowe kopie zapasowe to 500 GiB każdy.
-   Cztery cotygodniowe kopie zapasowe są przechowywane przez miesiąc.
-   Dwanaście miesięcznych kopii zapasowych jest przechowywanych przez rok.
-   Roczna kopia zapasowa jest przechowywana przez 10 lat.

Na podstawie powyższych założeń utwórz wolumin warstwowy 26-TiB StorSimple dla miesięcznych i 5 wyższych pełnych kopii zapasowych. Utwórz wolumin warstwowy StorSimple 5-TiB dla każdego przyrostowego dziennego tworzenia kopii zapasowych.

| Przechowywanie typu kopii zapasowej | Rozmiar (TiB) | Mnożnik GFS\* | Całkowita pojemność (TiB)  |
|---|---|---|---|
| Tygodniowo pełna | 1 | 4  | 4 |
| Przyrostowe dzienne | 0,5 | 20 (cykle równej liczbie tygodni w miesiącu) | 12 (2 dla dodatkowego kontyngentu) |
| Miesięczna pełna | 1 | 12 | 12 |
| Roczna pełna | 1  | 10 | 10 |
| Wymóg GFS |   | 38 |   |
| Dodatkowy przydział  | 4  |   | 42 całkowite zapotrzebowanie GFS  |

\*Mnożnik GFS to liczba kopii, które należy chronić i zachować, aby spełnić wymagania zasad tworzenia kopii zapasowych.

## <a name="set-up-veeam-storage"></a>Konfigurowanie pamięci masowej Veeam

### <a name="to-set-up-veeam-storage"></a>Aby skonfigurować pamięć masową Veeam

1.  W konsoli Veeam Backup and Replication w **narzędziach repozytorium**przejdź do pozycji **Infrastruktura kopii zapasowych**. Kliknij prawym przyciskiem myszy pozycję **Repozytoria kopii zapasowych**, a następnie wybierz polecenie **Dodaj repozytorium kopii zapasowych**.

    ![Konsola zarządzania veeam, strona repozytorium kopii zapasowych](./media/storsimple-configure-backup-target-using-veeam/veeamimage1.png)

2.  W oknie dialogowym **Nowe repozytorium kopii zapasowych** wprowadź nazwę i opis repozytorium. Wybierz **pozycję Dalej**.

    ![Konsola zarządzania veeam, strona z nazwą i opisem](./media/storsimple-configure-backup-target-using-veeam/veeamimage2.png)

3.  Dla tego typu wybierz pozycję **Microsoft Windows Server**. Wybierz serwer Veeam. Wybierz **pozycję Dalej**.

    ![Konsola zarządzania veeam, wybierz typ repozytorium kopii zapasowych](./media/storsimple-configure-backup-target-using-veeam/veeamimage3.png)

4.  Aby określić **lokalizację,** przejrzyj i wybierz wolumin. Zaznacz pole wyboru **Ogranicz maksymalną liczbę równoczesnych zadań na:** i ustaw wartość **4**. Gwarantuje to, że tylko cztery dyski wirtualne są przetwarzane jednocześnie podczas przetwarzania każdej maszyny wirtualnej. Wybierz przycisk **Zaawansowane.**

    ![Konsola zarządzania veeam, wybierz wolumin](./media/storsimple-configure-backup-target-using-veeam/veeamimage4.png)


5.  W oknie dialogowym **Ustawienia zgodności magazynu** zaznacz pole wyboru Użyj plików kopii zapasowej na **maszynę wirtualną.**

    ![Konsola zarządzania veeam, ustawienia zgodności pamięci masowej](./media/storsimple-configure-backup-target-using-veeam/veeamimage5.png)

6.  W oknie dialogowym **Nowe repozytorium kopii zapasowych** zaznacz pole wyboru **Włącz usługę NFS vPower na serwerze instalacji (zalecane).** Wybierz **pozycję Dalej**.

    ![Konsola zarządzania veeam, strona repozytorium kopii zapasowych](./media/storsimple-configure-backup-target-using-veeam/veeamimage6.png)

7.  Przejrzyj ustawienia, a następnie wybierz przycisk **Dalej**.

    ![Konsola zarządzania veeam, strona repozytorium kopii zapasowych](./media/storsimple-configure-backup-target-using-veeam/veeamimage7.png)

    Do serwera Veeam zostanie dodane repozytorium.

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Konfigurowanie StorSimple jako podstawowego obiektu docelowego kopii zapasowej

> [!IMPORTANT]
> Przywracanie danych z kopii zapasowej, która została warstwowa do chmury występuje z szybkością chmury.

Na poniższej ilustracji przedstawiono mapowanie typowego woluminu do zadania tworzenia kopii zapasowej. W takim przypadku wszystkie cotygodniowe kopie zapasowe są mapowane na pełny dysk w sobotę, a przyrostowe kopie zapasowe są mapowane na dyski przyrostowe od poniedziałku do piątku. Wszystkie kopie zapasowe i przywraca są z StorSimple woluminu warstwowego.

![Diagram logiczny konfiguracji docelowej kopii zapasowej](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>StorSimple jako przykład podstawowego planu docelowego kopii zapasowej GFS

Oto przykład harmonogramu rotacji GFS przez cztery tygodnie, co miesiąc i co roku:

| Częstotliwość/typ kopii zapasowej | Pełne | Przyrostowe (dni 1-5)  |   
|---|---|---|
| Co tydzień (tygodnie 1-4) | Sobota | Od poniedziałku do piątku |
| Co miesiąc  | Sobota  |   |
| Roczne | Sobota  |   |


### <a name="assign-storsimple-volumes-to-a-veeam-backup-job"></a>Przypisywanie woluminów StorSimple do zadania tworzenia kopii zapasowej veeam

W przypadku podstawowego scenariusza docelowego kopii zapasowej utwórz codzienne zadanie z podstawowym woluminem Veeam StorSimple. W przypadku dodatkowego scenariusza docelowego kopii zapasowej utwórz codzienne zadanie przy użyciu pamięci masowej podłączonej do sieci (DAS), sieciowej pamięci masowej (NAS) lub magazynu JBOD (Just a Bunch of Disks).

#### <a name="to-assign-storsimple-volumes-to-a-veeam-backup-job"></a>Aby przypisać woluminy StorSimple do zadania tworzenia kopii zapasowej veeam

1.  W konsoli Kopia zapasowa i replikacja veeam wybierz pozycję **Kopia zapasowa & replikacja**. Kliknij prawym przyciskiem myszy **pozycję Kopia zapasowa**, a następnie wybierz polecenie **VMware** lub **Hyper-V**, w zależności od środowiska.

    ![Konsola zarządzania Veeam, nowe zadanie tworzenia kopii zapasowych](./media/storsimple-configure-backup-target-using-veeam/veeamimage8.png)

2.  W oknie dialogowym **Nowe zadanie kopii zapasowej** wprowadź nazwę i opis zadania dziennego wykonywania kopii zapasowej.

    ![Konsola zarządzania veeam, nowa strona zadania tworzenia kopii zapasowych](./media/storsimple-configure-backup-target-using-veeam/veeamimage9.png)

3.  Wybierz maszynę wirtualną, do na które chcesz przejść do kopii zapasowej.

    ![Konsola zarządzania veeam, nowa strona zadania tworzenia kopii zapasowych](./media/storsimple-configure-backup-target-using-veeam/veeamimage10.png)

4.  Wybierz wartości, które chcesz wybrać dla **serwera proxy kopii zapasowej** i **repozytorium kopii zapasowych**. Select a value for **Restore points to keep on disk** according to the RPO and RTO definitions for your environment on locally attached storage. Wybierz **pozycję Zaawansowane**.

    ![Konsola zarządzania veeam, nowa strona zadania tworzenia kopii zapasowych](./media/storsimple-configure-backup-target-using-veeam/veeamimage11.png)

5. W oknie dialogowym **Ustawienia zaawansowane** na karcie **Kopia zapasowa** wybierz pozycję **Przyrostowe**. Upewnij się, że pole wyboru **Utwórz syntetyczne pełne kopie zapasowe jest wyczyszczone.** Zaznacz okresowe pole wyboru **Utwórz aktywne pełne kopie zapasowe.** W obszarze **Aktywne pełne tworzenie kopii zapasowych**zaznacz pole wyboru Tydzień w wybranych **dniach** na sobotę.

    ![Konsola zarządzania Veeam, nowa strona ustawień ustawień zadania tworzenia kopii zapasowych](./media/storsimple-configure-backup-target-using-veeam/veeamimage12.png)

6. Na karcie **Magazyn** upewnij się, że pole wyboru **Włącz deduplikację danych wbudowanych** jest wyczyszczone. Zaznacz pole wyboru **Wyklucz bloki plików wymiany** i zaznacz pole wyboru Wyklucz usunięte bloki **plików.** Ustaw **poziom kompresji** na **Brak**. Aby uzyskać zrównoważoną wydajność i deduplikację, ustaw **optymalizację pamięci masowej** na **docelową sieć LAN.** Kliknij przycisk **OK**.

    ![Konsola zarządzania Veeam, nowa strona ustawień ustawień zadania tworzenia kopii zapasowych](./media/storsimple-configure-backup-target-using-veeam/veeamimage13.png)

    Aby uzyskać informacje na temat ustawień deduplikacji i kompresji veeam, zobacz [Kompresja danych i deduplikacja](https://helpcenter.veeam.com/backup/vsphere/compression_deduplication.html).

7.  W oknie dialogowym **Edytowanie zadania kopii zapasowej** można **zaznaczyć** pole wyboru Włącz przetwarzanie z uwzględnieniem aplikacji (opcjonalnie).

    ![Konsola zarządzania Veeam, nowa strona przetwarzania zadań kopii zapasowej](./media/storsimple-configure-backup-target-using-veeam/veeamimage14.png)

8.  Ustaw harmonogram, aby uruchamiał się raz dziennie, o godzinie, którą można określić.

    ![Konsola zarządzania veeam, nowa strona harmonogramu zadań tworzenia kopii zapasowych](./media/storsimple-configure-backup-target-using-veeam/veeamimage15.png)

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>Konfigurowanie storprosza jako pomocniczego obiektu docelowego kopii zapasowej

> [!NOTE]
> Przywracanie danych z kopii zapasowej, która została warstwowa do chmury występują z szybkością chmury.

W tym modelu musi mieć nośnik magazynu (inne niż StorSimple), aby służyć jako tymczasowa pamięć podręczna. Na przykład można użyć nadmiarowej macierzy woluminu niezależnych dysków (RAID), aby pomieścić miejsce, wejście/wyjście (We/Wy) i przepustowość. Zalecamy użycie macierzy RAID 5, 50 i 10.

Na poniższym rysunku przedstawiono typowe woluminy lokalne przechowywania krótkoterminowego (na serwerze) i woluminy archiwum długoterminowego przechowywania. W tym scenariuszu wszystkie kopie zapasowe są uruchamiane na lokalnym woluminie RAID (na serwerze). Te kopie zapasowe są okresowo duplikowane i archiwizowane na woluminie archiwum. Ważne jest, aby rozmiar woluminu raid lokalnego (do serwera), tak aby mógł obsługiwać krótkoterminowe wymagania dotyczące pojemności przechowywania i wydajności.

![StorSimple jako pomocniczy diagram logiczny docelowego kopii zapasowej](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetdiagram.png)

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>StorSimple jako przykład pomocniczego obiektu docelowego kopii zapasowej GFS

W poniższej tabeli przedstawiono sposób konfigurowania kopii zapasowych do uruchamiania na dyskach lokalnych i StorSimple. Obejmuje indywidualne i całkowite wymagania dotyczące zdolności przepustowej.

| Typ kopii zapasowej i przechowywanie | Skonfigurowana pamięć masowa | Rozmiar (TiB) | Mnożnik GFS | Całkowita\* pojemność (TiB) |
|---|---|---|---|---|
| Tydzień 1 (pełny i przyrostowy) |Dysk lokalny (krótkoterminowy)| 1 | 1 | 1 |
| StorProste tygodnie 2-4 |Dysk StorSimple (długoterminowy) | 1 | 4 | 4 |
| Miesięczna pełna |Dysk StorSimple (długoterminowy) | 1 | 12 | 12 |
| Roczna pełna |Dysk StorSimple (długoterminowy) | 1 | 1 | 1 |
|Wymagania dotyczące wielkości GFS |  |  |  | 18*|

\*Całkowita pojemność obejmuje 17 TiB dysków StorSimple i 1 TiB lokalnego woluminu RAID.


### <a name="gfs-example-schedule"></a>Przykładowy harmonogram GFS

Rotacja GFS tygodniowo, miesięcznie i co roku

| Tydzień | Pełne | Przyrostowy dzień 1 | Przyrostowy dzień 2 | Przyrostowy dzień 3 | Przyrostowy dzień 4 | Przyrostowy dzień 5 |
|---|---|---|---|---|---|---|
| Tydzień 1 | Lokalny wolumin RAID  | Lokalny wolumin RAID | Lokalny wolumin RAID | Lokalny wolumin RAID | Lokalny wolumin RAID | Lokalny wolumin RAID |
| Tydzień 2 | StorProste tygodnie 2-4 |   |   |   |   |   |
| Tydzień 3 | StorProste tygodnie 2-4 |   |   |   |   |   |
| Tydzień 4 | StorProste tygodnie 2-4 |   |   |   |   |   |
| Co miesiąc | StorSimple co miesiąc |   |   |   |   |   |
| Roczne | StorSimple rocznie  |   |   |   |   |   |

### <a name="assign-storsimple-volumes-to-a-veeam-copy-job"></a>Przypisywanie woluminów StorSimple do zadania kopiowania veeam

#### <a name="to-assign-storsimple-volumes-to-a-veeam-copy-job"></a>Aby przypisać woluminy StorSimple do zadania kopiowania veeam

1.  W konsoli Kopia zapasowa i replikacja veeam wybierz pozycję **Kopia zapasowa & replikacja**. Kliknij prawym przyciskiem myszy **pozycję Kopia zapasowa**, a następnie wybierz polecenie **VMware** lub **Hyper-V**, w zależności od środowiska.

    ![Konsola zarządzania Veeam, nowa strona zadania kopiowania kopii zapasowej](./media/storsimple-configure-backup-target-using-veeam/veeamimage16.png)

2.  W oknie dialogowym **Nowe zadanie kopiowania kopii zapasowej** wprowadź nazwę i opis zadania.

    ![Konsola zarządzania Veeam, nowa strona zadania kopiowania kopii zapasowej](./media/storsimple-configure-backup-target-using-veeam/veeamimage17.png)

3.  Wybierz maszyny wirtualne, które chcesz przetworzyć. Wybierz z kopii zapasowych, a następnie wybierz dzienną kopię zapasową, która została utworzona wcześniej.

    ![Konsola zarządzania Veeam, nowa strona zadania kopiowania kopii zapasowej](./media/storsimple-configure-backup-target-using-veeam/veeamimage18.png)

4.  W razie potrzeby wyklucz obiekty z zadania kopiowania kopii zapasowej.

5.  Wybierz repozytorium kopii zapasowych i ustaw wartość **punktów przywracania do zachowania**. Pamiętaj, aby zaznaczyć pole wyboru **Zachowaj następujące punkty przywracania do celów archiwalnych.** Zdefiniuj częstotliwość tworzenia kopii zapasowych, a następnie wybierz pozycję **Zaawansowane**.

    ![Konsola zarządzania Veeam, nowa strona zadania kopiowania kopii zapasowej](./media/storsimple-configure-backup-target-using-veeam/veeamimage19.png)

6.  Określ następujące ustawienia zaawansowane:

    * Na karcie **Konserwacja** wyłącz ochronę przed uszkodzeniem poziomu magazynu.

    ![Konsola zarządzania Veeam, nowa strona ustawień ustawień zadania kopiowania kopii zapasowej](./media/storsimple-configure-backup-target-using-veeam/veeamimage20.png)

    * Na karcie **Magazyn** upewnij się, że deduplikacja i kompresja są wyłączone.

    ![Konsola zarządzania Veeam, nowa strona ustawień ustawień zadania kopiowania kopii zapasowej](./media/storsimple-configure-backup-target-using-veeam/veeamimage21.png)

7.  Określ, że transfer danych jest bezpośredni.

8.  Zdefiniuj harmonogram okna kopii zapasowej zgodnie z potrzebami, a następnie zakończ kreatora.

Aby uzyskać więcej informacji, zobacz [Tworzenie zadań kopiowania kopii zapasowych](https://helpcenter.veeam.com/backup/hyperv/backup_copy_create.html).

## <a name="storsimple-cloud-snapshots"></a>StorSimple migawki w chmurze

StorSimple migawki w chmurze chronią dane, które znajdują się w urządzeniu StorSimple. Tworzenie migawki w chmurze jest równoznaczne z wysyłką lokalnych taśm kopii zapasowych do obiektu poza obiektem. Jeśli używasz magazynu geograficznego nadmiarowego platformy Azure, tworzenie migawki w chmurze jest równoważne wysyłania taśm kopii zapasowej do wielu lokacji. Jeśli musisz przywrócić urządzenie po awarii, możesz przewieźć inne urządzenie StorSimple do trybu online i wykonać tryb failover. Po przewijaniu awaryjnego będzie można uzyskać dostęp do danych (z szybkością chmury) z najnowszej migawki chmury.

W poniższej sekcji opisano sposób tworzenia krótkiego skryptu, aby uruchomić i usunąć migawki chmury StorSimple podczas wykonywania kopii zapasowej.

> [!NOTE]
> Migawki, które są tworzone ręcznie lub programowo nie są zgodne storproste zasady wygasania migawki. Te migawki muszą być ręcznie lub programowo usunięte.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Uruchamianie i usuwanie migawek w chmurze przy użyciu skryptu

> [!NOTE]
> Dokładnie ocenić zgodność i przechowywania danych reperkusje przed usunięciem StorSimple migawki. Więcej informacji na temat uruchamiania skryptu po wykonaniu kopii zapasowej można znaleźć w dokumentacji firmy Veeam.


### <a name="backup-lifecycle"></a>Cykl życia kopii zapasowej

![Diagram cyklu życia kopii zapasowej](./media/storsimple-configure-backup-target-using-veeam/backuplifecycle.png)

### <a name="requirements"></a>Wymagania

-   Serwer, który uruchamia skrypt musi mieć dostęp do zasobów chmury platformy Azure.
-   Konto użytkownika musi mieć niezbędne uprawnienia.
-   Zasady tworzenia kopii zapasowych StorSimple ze skojarzonymi woluminami StorSimple muszą być skonfigurowane, ale nie włączone.
-   Będziesz potrzebować nazwy zasobu StorSimple, klucza rejestracji, nazwy urządzenia i identyfikatora zasad kopii zapasowej.

### <a name="to-start-or-delete-a-cloud-snapshot"></a>Aby uruchomić lub usunąć migawkę w chmurze

1. [Zainstaluj program Azure PowerShell](/powershell/azure/overview).
2. Pobierz i skonfiguruj skrypt Programu PowerShell [Manage-CloudSnapshots.ps1.](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Manage-CloudSnapshots.ps1)
3. Na serwerze, na który jest uruchamiany skrypt, uruchom program PowerShell jako administrator. Upewnij się, że `-WhatIf $true` skrypt zostanie uruchomiony, aby zobaczyć, jakie zmiany spowoduje skrypt. Po zakończeniu sprawdzania poprawności `-WhatIf $false`należy przekazać . Uruchom polecenie poniżej:
   ```powershell
   .\Manage-CloudSnapshots.ps1 -SubscriptionId [Subscription Id] -TenantId [Tenant ID] -ResourceGroupName [Resource Group Name] -ManagerName [StorSimple Device Manager Name] -DeviceName [device name] -BackupPolicyName [backup policyname] -RetentionInDays [Retention days] -WhatIf [$true or $false]
   ```
4. Aby dodać skrypt do zadania tworzenia kopii zapasowej, edytuj zaawansowane opcje zadania Veeam.

    ![Karta Skrypty ustawień zaawansowanych kopii zapasowych veeam](./media/storsimple-configure-backup-target-using-veeam/veeamimage22.png)

Zaleca się uruchomienie zasad tworzenia kopii zapasowych migawki w chmurze StorSimple jako skryptu przetwarzania końcowego na końcu codziennego zadania tworzenia kopii zapasowej. Aby uzyskać więcej informacji na temat tworzenia kopii zapasowych i przywracania środowiska aplikacji kopii zapasowej, aby pomóc w spełnieniu rpo i obiektu RTO, skontaktuj się z architektem kopii zapasowych.

## <a name="storsimple-as-a-restore-source"></a>StorSimple jako źródło przywracania

Przywraca z urządzenia StorSimple działa jak przywraca z dowolnego urządzenia magazynu bloku. Przywraca dane, które są warstwowe do chmury występuje z prędkością chmury. W przypadku danych lokalnych przywracane są z szybkością dysku lokalnego urządzenia.

Dzięki veeam możesz szybko, szczegółową, szczegółową, generyzyjną, generyzyjną, generyzać za pośrednictwem StorSimple za pomocą wbudowanych widoków eksploratora w konsoli Veeam. Explorery Veeam korzystają z odzyskiwania pojedynczych elementów, takich jak wiadomości e-mail, obiekty usługi Active Directory i elementy programu SharePoint z kopii zapasowych. Odzyskiwanie można wykonać bez zakłóceń lokalnych maszyn wirtualnych. Można również wykonać odzyskiwanie punktu w czasie dla bazy danych SQL Azure i bazy danych Oracle. Veeam i StorSimple sprawiają, że proces odzyskiwania na poziomie elementu z platformy Azure jest szybki i łatwy. Aby uzyskać informacje dotyczące sposobu przywracania, zobacz dokumentację firmy Veeam:

- Dla [programu Exchange Server](https://www.veeam.com/microsoft-exchange-recovery.html)
- Dla [usługi Active Directory](https://www.veeam.com/microsoft-active-directory-explorer.html)
- Dla [programu SQL Server](https://www.veeam.com/microsoft-sql-server-explorer.html)
- Dla [programu SharePoint](https://www.veeam.com/microsoft-sharepoint-recovery-explorer.html)
- Dla [Oracle](https://www.veeam.com/oracle-backup-recovery-explorer.html)


## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple pracy awaryjnej i odzyskiwania po awarii

> [!NOTE]
> W scenariuszach docelowych kopii zapasowych storsimple cloud appliance nie jest obsługiwany jako miejsce docelowe przywracania.

Katastrofa może być spowodowana przez wiele czynników. W poniższej tabeli wymieniono typowe scenariusze odzyskiwania po awarii.

| Scenariusz | Wpływ | Jak odzyskać | Uwagi |
|---|---|---|---|
| StorSimple awarii urządzenia | Operacje tworzenia kopii zapasowych i przywracania są przerywane. | Wymień urządzenie, które uległo awarii i wykonaj [storsimple pracy awaryjnej i odzyskiwania po awarii](storsimple-device-failover-disaster-recovery.md). | Jeśli konieczne jest wykonanie przywracania po odzyskaniu urządzenia, pełne zestawy robocze danych są pobierane z chmury do nowego urządzenia. Wszystkie operacje są na prędkości chmury. Proces ponownego skanowania indeksu i katalogu może spowodować, że wszystkie zestawy kopii zapasowych zostaną zeskanowane i pobrane z warstwy chmury do warstwy urządzeń lokalnych, co może być procesem czasochłonnym. |
| Awaria serwera Veeam | Operacje tworzenia kopii zapasowych i przywracania są przerywane. | Odbuduj serwer kopii zapasowych i wykonaj przywracanie bazy danych zgodnie z opisem w [Centrum pomocy Veeam (Dokumentacja techniczna).](https://www.veeam.com/documentation-guides-datasheets.html)  | Serwer Veeam należy odbudować lub przywrócić w lokacji odzyskiwania po awarii. Przywróć bazę danych do najnowszego punktu. Jeśli przywrócona baza danych Veeam nie jest zsynchronizowana z najnowszymi zadaniami tworzenia kopii zapasowych, wymagane jest indeksowanie i katalogowanie. Ten proces ponownego skanowania indeksu i katalogu może spowodować, że wszystkie zestawy kopii zapasowych zostaną zeskanowane i pobrane z warstwy chmury do warstwy urządzeń lokalnych. To sprawia, że jest to dodatkowo czasochłonne. |
| Awaria lokacji, która powoduje utratę zarówno serwera kopii zapasowej, jak i storproszta | Operacje tworzenia kopii zapasowych i przywracania są przerywane. | Najpierw przywróć StorSimple, a następnie przywróć veeam. | Najpierw przywróć StorSimple, a następnie przywróć veeam. Jeśli trzeba wykonać przywracanie po odzyskaniu urządzenia, pełne zestawy robocze danych są pobierane z chmury do nowego urządzenia. Wszystkie operacje są na prędkości chmury. |


## <a name="references"></a>Dokumentacja

Do tego artykułu przywołyno na następujące dokumenty:

- [Konfiguracja wielościeżkowych we/wy storSimple](storsimple-configure-mpio-windows-server.md)
- [Scenariusze magazynu: elastyczne inicjowanie obsługi administracyjnej](https://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [Korzystanie z dysków GPT](https://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [Konfigurowanie kopii w tle dla folderów udostępnionych](https://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o tym, jak [przywrócić z zestawu kopii zapasowych](storsimple-restore-from-backup-set-u2.md).
- Dowiedz się więcej o tym, jak wykonać [funkcję pracy awaryjnej urządzenia i odzyskiwanie po awarii.](storsimple-device-failover-disaster-recovery.md)
