---
title: StorSimple serii 8000 jako miejsce docelowe kopii zapasowej z NetBackup | Dokumenty firmy Microsoft
description: W tym artykule opisano konfigurację docelową kopii zapasowej StorSimple z programem Veritas NetBackup.
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
ms.author: matd
ms.openlocfilehash: 957fff73f2406e0e057a7c978dd76a6bd9c156b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67876206"
---
# <a name="storsimple-as-a-backup-target-with-netbackup"></a>StorSimple jako miejsce docelowe kopii zapasowej z NetBackup

## <a name="overview"></a>Omówienie

Azure StorSimple to rozwiązanie magazynu w chmurze hybrydowej firmy Microsoft. StorSimple rozwiązuje złożoność wykładniczego wzrostu danych przy użyciu konta magazynu platformy Azure jako rozszerzenia rozwiązania lokalnego i automatycznie warstwowania danych w magazynie lokalnym i magazynie w chmurze.

W tym artykule omówimy integrację StorSimple z Veritas NetBackup i najlepsze rozwiązania dotyczące integracji obu rozwiązań. Możemy również zalecenia dotyczące sposobu konfigurowania Veritas NetBackup, aby jak najlepiej zintegrować się z StorSimple. Odraczamy najlepsze rozwiązania veritas, architektów kopii zapasowych i administratorów, aby uzyskać najlepszy sposób skonfigurowania programu Veritas NetBackup w celu spełnienia indywidualnych wymagań dotyczących tworzenia kopii zapasowych i umów dotyczących poziomu usług (SLA).

Chociaż zilustrujemy kroki konfiguracji i kluczowe pojęcia, w tym artykule nie jest w żadnym wypadku instrukcja konfiguracji krok po kroku lub instalacji. Zakładamy, że podstawowe składniki i infrastruktura są sprawne i gotowe do obsługi koncepcji, które opisujemy.

### <a name="who-should-read-this"></a>Kto powinien to przeczytać?

Informacje zawarte w tym artykule będą najbardziej przydatne dla administratorów kopii zapasowych, administratorów magazynu i architektów magazynu, którzy znają pamięć masową, system Windows Server 2012 R2, Ethernet, usługi w chmurze i program Veritas NetBackup.

### <a name="supported-versions"></a>Obsługiwane wersje

-   NetBackup 7.7.x i nowsze wersje
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
![diagram warstwowy StorSimple](./media/storsimple-configure-backup-target-using-netbackup/image1.jpg)

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

| Pojemność magazynu       | 8100          | 8600            |
|------------------------|---------------|-----------------|
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

![StorSimple jako podstawowy diagram logiczny docelowy kopii zapasowej](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Podstawowe docelowe kroki logiczne kopii zapasowej

1.  Serwer kopii zapasowej kontaktuje się z docelowym agentem kopii zapasowej, a agent kopii zapasowej przesyła dane do serwera kopii zapasowej.
2.  Serwer kopii zapasowej zapisuje dane na woluminach warstwowych StorSimple.
3.  Serwer kopii zapasowej aktualizuje bazę danych katalogu, a następnie kończy zadanie tworzenia kopii zapasowej.
4.  Skrypt migawki wyzwala Menedżera migawek StorSimple (uruchamianie lub usuwanie).
5.  Serwer kopii zapasowych usuwa wygasłe kopie zapasowe na podstawie zasad przechowywania.

### <a name="primary-target-restore-logical-steps"></a>Podstawowe kroki logiczne przywracania obiektu docelowego

1.  Serwer kopii zapasowej rozpoczyna przywracanie odpowiednich danych z repozytorium magazynu.
2.  Agent kopii zapasowej odbiera dane z serwera kopii zapasowej.
3.  Serwer kopii zapasowej kończy zadanie przywracania.

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple jako dodatkowy obiekt docelowy kopii zapasowej

W tym scenariuszu StorSimple woluminów głównie są używane do długoterminowego przechowywania lub archiwizacji.

Na poniższej ilustracji przedstawiono architekturę, w której początkowe kopie zapasowe i przywraca docelowe wolumin o wysokiej wydajności. Te kopie zapasowe są kopiowane i archiwizowane do storsimple wolumin warstwowy zgodnie z ustalonym harmonogramem.

Ważne jest, aby rozmiar woluminu o wysokiej wydajności, tak aby mógł obsługiwać możliwości przechowywania zasad i wymagań dotyczących wydajności.

![StorSimple jako pomocniczy diagram logiczny docelowego kopii zapasowej](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>Kroki logiczne pomocniczej kopii zapasowej docelowej

1.  Serwer kopii zapasowej kontaktuje się z docelowym agentem kopii zapasowej, a agent kopii zapasowej przesyła dane do serwera kopii zapasowej.
2.  Serwer kopii zapasowej zapisuje dane w magazynie o wysokiej wydajności.
3.  Serwer kopii zapasowej aktualizuje bazę danych katalogu, a następnie kończy zadanie tworzenia kopii zapasowej.
4.  Serwer kopii zapasowych kopiuje kopie zapasowe do StorSimple na podstawie zasad przechowywania.
5.  Skrypt migawki wyzwala Menedżera migawek StorSimple (uruchamianie lub usuwanie).
6.  Serwer kopii zapasowych usuwa wygasłe kopie zapasowe na podstawie zasad przechowywania.

### <a name="secondary-target-restore-logical-steps"></a>Kroki logiczne przywracania obiektu docelowego pomocniczego

1.  Serwer kopii zapasowej rozpoczyna przywracanie odpowiednich danych z repozytorium magazynu.
2.  Agent kopii zapasowej odbiera dane z serwera kopii zapasowej.
3.  Serwer kopii zapasowej kończy zadanie przywracania.

## <a name="deploy-the-solution"></a>Wdrażanie rozwiązania

Wdrożenie tego rozwiązania wymaga trzech kroków:
1. Przygotuj infrastrukturę sieciową.
2. Wdrażanie urządzenia StorSimple jako miejsce docelowe kopii zapasowej.
3. Wdrażanie rozwiązania Veritas NetBackup.

Każdy krok jest szczegółowo omówiony w poniższych sekcjach.

### <a name="set-up-the-network"></a>Konfigurowanie sieci

Ponieważ StorSimple to rozwiązanie zintegrowane z chmurą platformy Azure, StorSimple wymaga aktywnego i działającego połączenia z chmurą platformy Azure. To połączenie jest używane do operacji, takich jak migawki w chmurze, zarządzanie danymi i transfer metadanych, a także do warstwy starszych, mniej dostępnych danych do magazynu w chmurze platformy Azure.

Aby rozwiązanie działało optymalnie, zaleca się przestrzeganie tych najlepszych rozwiązań dotyczących sieci:

-   Łącze łączące warstwę StorSimple z platformą Azure musi spełniać wymagania dotyczące przepustowości. Aby to osiągnąć, należy zastosować odpowiedni poziom jakości usług (QoS) do przełączników infrastruktury, aby dopasować swoje umowy SLA cel czasu odzyskiwania i czasu odzyskiwania (RTO).

-   Maksymalne opóźnienia dostępu do magazynu obiektów Blob platformy Azure powinny wynosić około 80 ms.

### <a name="deploy-storsimple"></a>Wdrażanie StorSimple

Aby uzyskać wskazówki dotyczące wdrażania storsimple krok po kroku, zobacz [Wdrażanie lokalnego urządzenia StorSimple](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-netbackup"></a>Wdrażanie rozwiązania NetBackup

Aby uzyskać wskazówki dotyczące wdrażania programu NetBackup 7.7.x krok po kroku, zobacz [dokumentację programu NetBackup 7.7.x](http://www.veritas.com/docs/000094423).

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

- Nie używaj woluminów łączonych (utworzonych przez zarządzanie dyskami systemu Windows); woluminów łączonych nie są obsługiwane.
- Formatowanie woluminów przy użyciu systemu plików NTFS z rozmiarem alokacji 64 KB.
- Mapuj woluminy StorSimple bezpośrednio na serwer NetBackup.
    - Użyj interfejsu iSCSI dla serwerów fizycznych.
    - Użyj dysków przekazu dla serwerów wirtualnych.


## <a name="best-practices-for-storsimple-and-netbackup"></a>Najważniejsze wskazówki dotyczące storsimple i NetBackup

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

### <a name="netbackup-best-practices"></a>Najlepsze rozwiązania netbackup

-   Baza danych NetBackup powinna być lokalna dla serwera i nie znajduje się na woluminie StorSimple.
-   W przypadku odzyskiwania po awarii, tworzenie kopii zapasowej bazy danych NetBackup na woluminie StorSimple.
-   Obsługujemy netbackup pełne i przyrostowe kopie zapasowe (określane również jako różnicowe przyrostowe kopie zapasowe w Programie NetBackup) dla tego rozwiązania. Zaleca się, aby nie używać syntetycznych i skumulowanych przyrostowych kopii zapasowych.
-   Pliki danych kopii zapasowej powinny zawierać tylko dane dla określonego zadania. Na przykład żadne dołączanie multimediów w różnych zadaniach nie jest dozwolone.

Aby uzyskać najnowsze ustawienia programu NetBackup i najlepsze rozwiązania dotyczące wdrażania tych wymagań, zobacz dokumentację programu NetBackup w [www.veritas.com](https://www.veritas.com).


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

## <a name="set-up-netbackup-storage"></a>Konfigurowanie magazynu NetBackup

### <a name="to-set-up-netbackup-storage"></a>Aby skonfigurować magazyn NetBackup

1.  W konsoli administracyjnej NetBackup wybierz pozycję**Pule dysków** **urządzeń do zarządzania** > multimediami i**urządzeniami** > . W Kreatorze konfiguracji puli dysków wybierz typ serwera magazynu **AdvancedDisk**, a następnie wybierz pozycję **Dalej**.

    ![Konsola administracyjna netbackup, Kreator konfiguracji puli dysków](./media/storsimple-configure-backup-target-using-netbackup/nbimage1.png)

2.  Wybierz serwer, a następnie wybierz pozycję **Dalej**.

    ![Konsola administracyjna programu NetBackup, wybierz serwer](./media/storsimple-configure-backup-target-using-netbackup/nbimage2.png)

3.  Wybierz wolumin StorSimple.

    ![Konsola administracyjna programu NetBackup, wybierz dysk woluminu StorSimple](./media/storsimple-configure-backup-target-using-netbackup/nbimage3.png)

4.  Wprowadź nazwę obiektu docelowego kopii zapasowej, a następnie wybierz pozycję > **Dalej,** aby zakończyć kreatora. **Next**

5.  Przejrzyj ustawienia, a następnie wybierz pozycję **Zakończ**.

6.  Na końcu każdego przypisania woluminu zmień ustawienia urządzenia magazynującego, aby były zgodne z ustawieniami zalecanymi w [sekcji Najważniejsze wskazówki dotyczące storsimple i netbackup](#best-practices-for-storsimple-and-netbackup).

7. Powtarzaj kroki od 1 do 6, aż do zakończenia przypisywania woluminów StorSimple.

    ![Konsola administracyjna programu NetBackup, konfiguracja dysku](./media/storsimple-configure-backup-target-using-netbackup/nbimage5.png)

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Konfigurowanie StorSimple jako podstawowego obiektu docelowego kopii zapasowej

> [!NOTE]
> Przywracanie danych z kopii zapasowej, która została warstwowa do chmury występują z szybkością chmury.

Na poniższej ilustracji przedstawiono mapowanie typowego woluminu do zadania tworzenia kopii zapasowej. W takim przypadku wszystkie cotygodniowe kopie zapasowe są mapowane na pełny dysk w sobotę, a przyrostowe kopie zapasowe są mapowane na dyski przyrostowe od poniedziałku do piątku. Wszystkie kopie zapasowe i przywraca są z StorSimple woluminu warstwowego.

![Diagram logiczny konfiguracji docelowej kopii zapasowej](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>StorSimple jako przykład podstawowego planu docelowego kopii zapasowej GFS

Oto przykład harmonogramu rotacji GFS przez cztery tygodnie, co miesiąc i co roku:

| Częstotliwość/typ kopii zapasowej | Pełne | Przyrostowe (dni 1-5)  |   
|---|---|---|
| Co tydzień (tygodnie 1-4) | Sobota | Od poniedziałku do piątku |
| Co miesiąc  | Sobota  |   |
| Roczne | Sobota  |   |

## <a name="assigning-storsimple-volumes-to-a-netbackup-backup-job"></a>Przypisywanie woluminów StorSimple do zadania tworzenia kopii zapasowej programu NetBackup

Poniższa sekwencja zakłada, że NetBackup i host docelowy są skonfigurowane zgodnie z wytycznymi agenta NetBackup.

### <a name="to-assign-storsimple-volumes-to-a-netbackup-backup-job"></a>Aby przypisać woluminy StorSimple do zadania tworzenia kopii zapasowej programu NetBackup

1. W konsoli administracyjnej NetBackup wybierz pozycję **Zarządzanie programem NetBackup**, kliknij prawym przyciskiem myszy **pozycję Zasady**, a następnie wybierz pozycję **Nowa zasada**.

   ![NetBackup Administration Console, utwórz nową zasadę](./media/storsimple-configure-backup-target-using-netbackup/nbimage6.png)

2. W oknie **dialogowym Dodawanie nowej zasady** wprowadź nazwę zasady, a następnie zaznacz pole wyboru **Użyj Kreatora konfiguracji zasad.** Kliknij przycisk **OK**.

   ![Konsola administracyjna programu NetBackup, okno dialogowe Dodawanie nowych zasad](./media/storsimple-configure-backup-target-using-netbackup/nbimage7.png)

3. W Kreatorze konfiguracji zasad kopii zapasowych wybierz odpowiedni typ kopii zapasowej, a następnie wybierz pozycję **Dalej**.

   ![Konsola administracyjna programu NetBackup, wybierz typ kopii zapasowej](./media/storsimple-configure-backup-target-using-netbackup/nbimage8.png)

4. Aby ustawić typ zasad, wybierz pozycję **Standardowy**, a następnie wybierz pozycję **Dalej**.

   ![Konsola administracyjna NetBackup, wybierz typ zasad](./media/storsimple-configure-backup-target-using-netbackup/nbimage9.png)

5. Zaznacz hosta, zaznacz pole wyboru **Wykryj system operacyjny klienta,** a następnie wybierz pozycję **Dodaj**. Wybierz **pozycję Dalej**.

   ![Konsola administracyjna NetBackup, lista klientów w nowej zasadie](./media/storsimple-configure-backup-target-using-netbackup/nbimage10.png)

6. Wybierz dyski, których chcesz zrobić, aby uzyskać jego utworzenie.

   ![Konsola administracyjna NetBackup, wybór kopii zapasowych dla nowej zasady](./media/storsimple-configure-backup-target-using-netbackup/nbimage11.png)

7. Wybierz wartości częstotliwości i przechowywania, które spełniają wymagania dotyczące rotacji kopii zapasowej.

   ![Konsola administracyjna NetBackup, częstotliwość tworzenia kopii zapasowych i rotacja dla nowych zasad](./media/storsimple-configure-backup-target-using-netbackup/nbimage12.png)

8. Wybierz **następne** > **Next** > **następne zakończenie**.  Harmonogram można zmodyfikować po utworzeniu zasad.

9. Wybierz, aby rozwinąć właśnie utworzoną zasadę, a następnie wybierz pozycję **Harmonogramy**.

   ![Konsola administracyjna NetBackup, harmonogramy nowych zasad](./media/storsimple-configure-backup-target-using-netbackup/nbimage13.png)

10. Kliknij prawym przyciskiem myszy **pozycję Differential-Inc**, wybierz polecenie **Kopiuj do nowego**, a następnie wybierz przycisk **OK**.

    ![Konsola administracyjna netbackup, kopiowanie harmonogramu do nowej zasady](./media/storsimple-configure-backup-target-using-netbackup/nbimage14.png)

11. Kliknij prawym przyciskiem myszy nowo utworzony harmonogram, a następnie wybierz polecenie **Zmień**.

12. Na karcie **Atrybuty** zaznacz pole wyboru **Zastąpuj przechowywanie zasad,** a następnie wybierz wolumin, na którym trafiają przyrostowe kopie zapasowe w poniedziałek.

    ![Konsola administracyjna netbackup, zmienianie harmonogramu](./media/storsimple-configure-backup-target-using-netbackup/nbimage15.png)

13. Na karcie **Okno startowe** wybierz okno czasu dla kopii zapasowych.

    ![Konsola administracyjna netbackup, okno startowe zmiany](./media/storsimple-configure-backup-target-using-netbackup/nbimage16.png)

14. Kliknij przycisk **OK**.

15. Powtórz kroki 10-14 dla każdej przyrostowej kopii zapasowej. Wybierz odpowiedni wolumin i harmonogram dla każdej utworzonej kopii zapasowej.

16. Kliknij prawym przyciskiem myszy harmonogram **różnicowy inc,** a następnie usuń go.

17. Zmodyfikuj pełny harmonogram, aby spełnić potrzeby związane z tworzeniem kopii zapasowych.

    ![Konsola administracyjna NetBackup, zmienianie pełnego harmonogramu](./media/storsimple-configure-backup-target-using-netbackup/nbimage17.png)

18. Zmień okno startowe.

    ![NetBackup Administration Console, zmień okno startowe](./media/storsimple-configure-backup-target-using-netbackup/nbimage18.png)

19. Ostateczny harmonogram wygląda następująco:

    ![Konsola administracyjna NetBackup, ostateczny harmonogram](./media/storsimple-configure-backup-target-using-netbackup/nbimage19.png)

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>Konfigurowanie storprosza jako pomocniczego obiektu docelowego kopii zapasowej

> [!NOTE]
>Przywracanie danych z kopii zapasowej, która została warstwowa do chmury występują z szybkością chmury.

W tym modelu musi mieć nośnik magazynu (inne niż StorSimple), aby służyć jako tymczasowa pamięć podręczna. Na przykład można użyć nadmiarowej macierzy woluminu niezależnych dysków (RAID), aby pomieścić miejsce, wejście/wyjście (We/Wy) i przepustowość. Zalecamy użycie macierzy RAID 5, 50 i 10.

Na poniższym rysunku przedstawiono typowe woluminy lokalne (na serwerze) związane z przechowywaniem i woluminy archiwów długoterminowych. W tym scenariuszu wszystkie kopie zapasowe są uruchamiane na lokalnym woluminie RAID (na serwerze). Te kopie zapasowe są okresowo duplikowane i archiwizowane w woluminie archiwalnym. Ważne jest, aby rozmiar woluminu raid lokalnego (do serwera), tak aby mógł obsługiwać krótkoterminowe wymagania dotyczące pojemności przechowywania i wydajności.

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>StorSimple jako przykład pomocniczego obiektu docelowego kopii zapasowej GFS

![StorSimple jako pomocniczy diagram logiczny docelowego kopii zapasowej](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetdiagram.png)

W poniższej tabeli przedstawiono sposób konfigurowania kopii zapasowych do uruchamiania na dyskach lokalnych i StorSimple. Obejmuje indywidualne i całkowite wymagania dotyczące zdolności przepustowej.

### <a name="backup-configuration-and-capacity-requirements"></a>Wymagania dotyczące konfiguracji i pojemności kopii zapasowych

| Typ kopii zapasowej i przechowywanie | Skonfigurowana pamięć masowa | Rozmiar (TiB) | Mnożnik GFS | Całkowita\* pojemność (TiB) |
|---|---|---|---|---|
| Tydzień 1 (pełny i przyrostowy) |Dysk lokalny (krótkoterminowy)| 1 | 1 | 1 |
| StorProste tygodnie 2-4 |Dysk StorSimple (długoterminowy) | 1 | 4 | 4 |
| Miesięczna pełna |Dysk StorSimple (długoterminowy) | 1 | 12 | 12 |
| Roczna pełna |Dysk StorSimple (długoterminowy) | 1 | 1 | 1 |
|Wymagania dotyczące wielkości GFS |  |  |  | 18*|

\*Całkowita pojemność obejmuje 17 TiB dysków StorSimple i 1 TiB lokalnego woluminu RAID.


### <a name="gfs-example-schedule-gfs-rotation-weekly-monthly-and-yearly-schedule"></a>Przykładowy harmonogram GFS: harmonogram rotacji GFS co tydzień, miesięczny i roczny

| Tydzień | Pełne | Przyrostowy dzień 1 | Przyrostowy dzień 2 | Przyrostowy dzień 3 | Przyrostowy dzień 4 | Przyrostowy dzień 5 |
|---|---|---|---|---|---|---|
| Tydzień 1 | Lokalny wolumin RAID  | Lokalny wolumin RAID | Lokalny wolumin RAID | Lokalny wolumin RAID | Lokalny wolumin RAID | Lokalny wolumin RAID |
| Tydzień 2 | StorProste tygodnie 2-4 |   |   |   |   |   |
| Tydzień 3 | StorProste tygodnie 2-4 |   |   |   |   |   |
| Tydzień 4 | StorProste tygodnie 2-4 |   |   |   |   |   |
| Co miesiąc | StorSimple co miesiąc |   |   |   |   |   |
| Roczne | StorSimple rocznie  |   |   |   |   |   |


## <a name="assign-storsimple-volumes-to-a-netbackup-archive-and-duplication-job"></a>Przypisywanie woluminów StorSimple do zadania archiwum i duplikacji netbackup

Ponieważ program NetBackup oferuje szeroką gamę opcji zarządzania pamięcią masową i multimediami, zalecamy skonsultowanie się z programem Veritas lub architektem programu NetBackup w celu prawidłowej oceny wymagań zasad cyklu życia magazynu (SLP).

Po zdefiniowaniu początkowych pul dysku należy zdefiniować trzy dodatkowe zasady cyklu życia magazynu dla łącznie czterech zasad:
* LokalizacjaRAIDVolume
* StorSimpleWeek2-4
* StorSimpleMonthlyFulls
* StorSimpleYearlyFulls

### <a name="to-assign-storsimple-volumes-to-a-netbackup-archive-and-duplication-job"></a>Aby przypisać woluminy StorSimple do zadania archiwum i duplikacji netbackup

1. W konsoli administracyjnej Programu **Storage** > NetBackup wybierz pozycję**Zasady** > cyklu życia magazynu nowe zasady**cyklu magazynowania**.

   ![Konsola administracyjna NetBackup, nowe zasady cyklu życia magazynu](./media/storsimple-configure-backup-target-using-netbackup/nbimage20.png)

2. Wprowadź nazwę migawki, a następnie wybierz pozycję **Dodaj**.

3. W oknie dialogowym **Nowa operacja** na karcie **Właściwości** wybierz pozycję **Operacja**wybierz pozycję **Kopia zapasowa**. Wybierz wartości, które mają być dla **magazynu docelowego,** **typu przechowywania**i okresu **przechowywania**. Kliknij przycisk **OK**.

   ![Konsola administracyjna programu NetBackup, okno dialogowe Nowa operacja](./media/storsimple-configure-backup-target-using-netbackup/nbimage22.png)

   Definiuje pierwszą operację tworzenia kopii zapasowej i repozytorium.

4. Wybierz, aby wyróżnić poprzednią operację, a następnie wybierz pozycję **Dodaj**. W oknie dialogowym **Zmienianie operacji magazynowania** wybierz wartości, które mają być dla **magazynu docelowego,** **typu przechowywania**i okresu **przechowywania**.

   ![Konsola administracyjna programu NetBackup,Okno dialogowe Zmienianie operacji przechowywania](./media/storsimple-configure-backup-target-using-netbackup/nbimage23.png)

5. Wybierz, aby wyróżnić poprzednią operację, a następnie wybierz pozycję **Dodaj**. W oknie dialogowym **Nowa zasada cyklu życia magazynu** dodaj miesięczne kopie zapasowe na rok.

   ![Konsola administracyjna programu NetBackup, nowe zasady cyklu pracy magazynu](./media/storsimple-configure-backup-target-using-netbackup/nbimage24.png)

6. Powtarzaj kroki od 4 do 5, aż utworzono kompleksowe zasady przechowywania SLP, których potrzebujesz.

   ![Konsola administracyjna programu NetBackup, dodawanie zasad w oknie dialogowym Nowe zasady cyklu życia magazynu](./media/storsimple-configure-backup-target-using-netbackup/nbimage25.png)

7. Po zakończeniu definiowania zasad przechowywania sieci SLP w obszarze **Zasady**zdefiniuj zasady tworzenia kopii zapasowych, wykonując czynności opisane w obszarze [Przypisywanie woluminów StorSimple do zadania kopii zapasowej programu NetBackup](#assigning-storsimple-volumes-to-a-netbackup-backup-job).

8. W obszarze **Harmonogramy**w oknie dialogowym **Zmienianie harmonogramu** kliknij prawym przyciskiem myszy pozycję **Pełny**, a następnie wybierz polecenie **Zmień**.

   ![Konsola administracyjna programu NetBackup, okno dialogowe Zmienianie harmonogramu](./media/storsimple-configure-backup-target-using-netbackup/nbimage26.png)

9. Zaznacz pole wyboru **Zastąp wybór magazynu zasad,** a następnie zaznacz zasadę przechowywania SLP utworzoną w krokach 1-6.

   ![Konsola administracyjna programu NetBackup, zastępowanie wyboru magazynu zasad](./media/storsimple-configure-backup-target-using-netbackup/nbimage27.png)

10. Wybierz **przycisk OK**, a następnie powtórz dla harmonogramu przyrostowej kopii zapasowej.

    ![Konsola administracyjna programu NetBackup, okno dialogowe Zmienianie harmonogramu dla przyrostowych kopii zapasowych](./media/storsimple-configure-backup-target-using-netbackup/nbimage28.png)


| Przechowywanie typu kopii zapasowej | Rozmiar (TiB) | Mnożnik GFS\* | Całkowita pojemność (TiB)  |
|---|---|---|---|
| Tygodniowo pełna |  1  |  4 | 4  |
| Przyrostowe dzienne  | 0,5  | 20 (cykle są równe liczbie tygodni w miesiącu) | 12 (2 dla dodatkowego kontyngentu) |
| Miesięczna pełna  | 1 | 12 | 12 |
| Roczna pełna | 1  | 10 | 10 |
| Wymóg GFS  |     |     | 38 |
| Dodatkowy przydział  | 4  |    | 42 całkowite zapotrzebowanie GFS |

\*Mnożnik GFS to liczba kopii, które należy chronić i zachować, aby spełnić wymagania zasad tworzenia kopii zapasowych.

## <a name="storsimple-cloud-snapshots"></a>StorSimple migawki w chmurze

StorSimple migawki w chmurze chronią dane, które znajdują się w urządzeniu StorSimple. Tworzenie migawki w chmurze jest równoznaczne z wysyłką lokalnych taśm kopii zapasowych do obiektu poza obiektem. Jeśli używasz magazynu geograficznego nadmiarowego platformy Azure, tworzenie migawki w chmurze jest równoważne wysyłania taśm kopii zapasowej do wielu lokacji. Jeśli musisz przywrócić urządzenie po awarii, możesz przewieźć inne urządzenie StorSimple do trybu online i wykonać tryb failover. Po przewijaniu awaryjnego będzie można uzyskać dostęp do danych (z szybkością chmury) z najnowszej migawki chmury.

W poniższej sekcji opisano sposób tworzenia krótkiego skryptu, aby uruchomić i usunąć migawki chmury StorSimple podczas wykonywania kopii zapasowej.

> [!NOTE]
> Migawki, które są tworzone ręcznie lub programowo nie są zgodne storproste zasady wygasania migawki. Te migawki muszą być ręcznie lub programowo usunięte.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Uruchamianie i usuwanie migawek w chmurze przy użyciu skryptu

> [!NOTE]
> Dokładnie ocenić zgodność i przechowywania danych reperkusje przed usunięciem StorSimple migawki. Aby uzyskać więcej informacji na temat uruchamiania skryptu po wykonaniu kopii zapasowej, zobacz [dokumentację programu NetBackup](http://www.veritas.com/docs/000094423).

### <a name="backup-lifecycle"></a>Cykl życia kopii zapasowej

![Diagram cyklu życia kopii zapasowej](./media/storsimple-configure-backup-target-using-netbackup/backuplifecycle.png)

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
4. Dodaj skrypt do zadania tworzenia kopii zapasowej w programie NetBackup. Aby to zrobić, edytuj polecenia przetwarzania wstępnego i przetwarzania końcowego opcji zadań NetBackup.

> [!NOTE]
> Zaleca się uruchomienie zasad tworzenia kopii zapasowych migawki w chmurze StorSimple jako skryptu przetwarzania końcowego na końcu codziennego zadania tworzenia kopii zapasowej. Aby uzyskać więcej informacji na temat tworzenia kopii zapasowych i przywracania środowiska aplikacji kopii zapasowej, aby pomóc w spełnieniu rpo i obiektu RTO, skontaktuj się z architektem kopii zapasowych.

## <a name="storsimple-as-a-restore-source"></a>StorSimple jako źródło przywracania

Przywraca z urządzenia StorSimple działa jak przywraca z dowolnego urządzenia magazynu bloku. Przywraca dane, które są warstwowe do chmury występuje z prędkością chmury. W przypadku danych lokalnych przywracane są z szybkością dysku lokalnego urządzenia. Aby uzyskać informacje dotyczące sposobu przywracania, zobacz [dokumentację NetBackup](http://www.veritas.com/docs/000094423). Zaleca się, aby dostosować się do netbackup przywracania najlepszych praktyk.

## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple pracy awaryjnej i odzyskiwania po awarii

> [!NOTE]
> W scenariuszach docelowych kopii zapasowych storsimple cloud appliance nie jest obsługiwany jako miejsce docelowe przywracania.

Katastrofa może być spowodowana przez wiele czynników. W poniższej tabeli wymieniono typowe scenariusze odzyskiwania po awarii.

| Scenariusz | Wpływ | Jak odzyskać | Uwagi |
|---|---|---|---|
| StorSimple awarii urządzenia | Operacje tworzenia kopii zapasowych i przywracania są przerywane. | Wymień urządzenie, które uległo awarii i wykonaj [storsimple pracy awaryjnej i odzyskiwania po awarii](storsimple-device-failover-disaster-recovery.md). | Jeśli konieczne jest wykonanie przywracania po odzyskaniu urządzenia, pełne zestawy robocze danych są pobierane z chmury do nowego urządzenia. Wszystkie operacje są na prędkości chmury. Proces ponownego skanowania indeksu i katalogu może spowodować, że wszystkie zestawy kopii zapasowych zostaną zeskanowane i pobrane z warstwy chmury do warstwy urządzeń lokalnych, co może być procesem czasochłonnym. |
| Awaria serwera NetBackup | Operacje tworzenia kopii zapasowych i przywracania są przerywane. | Odbuduj serwer kopii zapasowej i wykonaj przywracanie bazy danych. | Należy odbudować lub przywrócić serwer NetBackup w lokacji odzyskiwania po awarii. Przywróć bazę danych do najnowszego punktu. Jeśli przywrócona baza danych NetBackup nie jest zsynchronizowana z najnowszymi zadaniami tworzenia kopii zapasowych, wymagane jest indeksowanie i katalogowanie. Ten proces ponownego skanowania indeksu i katalogu może spowodować, że wszystkie zestawy kopii zapasowych zostaną zeskanowane i pobrane z warstwy chmury do warstwy urządzeń lokalnych. To sprawia, że jest to dodatkowo czasochłonne. |
| Awaria lokacji, która powoduje utratę zarówno serwera kopii zapasowej, jak i storproszta | Operacje tworzenia kopii zapasowych i przywracania są przerywane. | Najpierw przywróć StorSimple, a następnie przywróć netbackup. | Najpierw przywróć StorSimple, a następnie przywróć netbackup. Jeśli trzeba wykonać przywracanie po odzyskaniu urządzenia, pełne zestawy robocze danych są pobierane z chmury do nowego urządzenia. Wszystkie operacje są na prędkości chmury. |

## <a name="references"></a>Dokumentacja

Do tego artykułu przywołyno na następujące dokumenty:

- [Konfiguracja wielościeżkowych we/wy storSimple](storsimple-configure-mpio-windows-server.md)
- [Scenariusze magazynu: elastyczne inicjowanie obsługi administracyjnej](https://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [Korzystanie z dysków GPT](https://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [Konfigurowanie kopii w tle dla folderów udostępnionych](https://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o tym, jak [przywrócić z zestawu kopii zapasowych](storsimple-restore-from-backup-set-u2.md).
- Dowiedz się więcej o tym, jak wykonać [funkcję pracy awaryjnej urządzenia i odzyskiwanie po awarii.](storsimple-device-failover-disaster-recovery.md)
