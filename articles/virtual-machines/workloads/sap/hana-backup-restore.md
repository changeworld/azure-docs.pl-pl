---
title: HANA kopia zapasowa i przywracanie na platformie SAP HANA na platformie Azure (duże wystąpienia) | Dokumentacja firmy Microsoft
description: Jak wykonać kopię zapasową platformy HANA i przywrócić na platformie SAP HANA na platformie Azure (duże wystąpienia)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/22/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 21232e5a678d6deed920e57cd0433a3b85ca4fdc
ms.sourcegitcommit: 60606c5e9a20b2906f6b6e3a3ddbcb6c826962d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2019
ms.locfileid: "64987911"
---
# <a name="backup-and-restore"></a>Tworzenie kopii zapasowej i przywracanie

>[!IMPORTANT]
>W tym artykule nie jest zamiennikiem dokumentacji administracyjnej platformy SAP HANA lub SAP Notes. Oczekujemy, że masz pełny opis i doświadczenia w zakresie oprogramowania SAP HANA administrację i operacji, szczególnie w przypadku kopii zapasowych, przywracania, wysokiej dostępności i odzyskiwania po awarii. W tym artykule przedstawiono zrzuty ekranu z SAP HANA Studio. Zawartości, struktury i charakter ekrany narzędzia do administrowania SAP i narzędzi, sami mogą ulec zmianie z platformy SAP HANA wersji.

Należy pamiętać, że wykonujesz kroki i procesy wykonywane w danym środowisku i z platformy HANA wersji i wydań. Niektóre procesy, które opisano w tym artykule są uproszczone lepsze zrozumienie ogólnego. Nie są one przeznaczone do służyć jako szczegółowy opis kroków podręczników ostatecznej operacji. Jeśli chcesz utworzyć podręczników operacji dla Twojej konfiguracji testu i wykonywania procesów i dokumentowanie procesów związanych z określonej konfiguracji. 

Jednym z najważniejszych aspektów operacyjnej bazy danych jest, aby chronić je przed katastrofami. Przyczyny te zdarzenia może być dowolna przed klęskami żywiołowymi błędy proste użytkownika.

Tworzenie kopii zapasowej bazy danych, z możliwością przywrócenia go do dowolnego punktu w czasie, takie jak przed ktoś usuniętych danych krytycznych, umożliwia przywracanie do stanu, który jest możliwie jak go zamknąć miał miejsce przed zakłócenie.

Dwa typy kopii zapasowych należy wykonać w taki sposób, aby osiągnąć możliwość przywracania:

- Kopie zapasowe bazy danych: Pełne, przyrostowych lub różnicowej kopii zapasowych
- Kopie zapasowe dziennika transakcji

Oprócz bazy danych na pełne kopie zapasowe wykonywane na poziomie aplikacji można wykonywać kopie zapasowe przy użyciu migawek magazynu. Migawki pamięci masowej nie zastąpić kopie zapasowe dziennika transakcji. Kopie zapasowe dziennika transakcji nadal ważne, aby przywrócić bazę danych do pewnego momentu w czasie lub pustą dzienniki transakcji już zatwierdzone. Migawek magazynu można co pozwala przyspieszyć odzyskiwanie, szybko udostępniając obrazem przodu bazy danych. 

Oprogramowanie SAP HANA na platformie Azure (duże wystąpienia) oferuje dwie opcje tworzenia kopii zapasowych i przywracania:

- **Zrobić to samodzielnie (DIY).** Po możesz upewnić się, że jest wystarczająca ilość miejsca na dysku, należy wykonać pełną bazę danych i kopie zapasowe dziennika przy użyciu jednej z następujących metod tworzenia kopii zapasowej dysku. Kopię zapasową można wykonywać bezpośrednio w woluminach dołączony do jednostek dużych wystąpień HANA lub udziałów NFS, skonfigurowanych na maszynie wirtualnej (VM) platformy Azure. W tym ostatnim przypadku klientom Konfigurowanie maszyny Wirtualnej z systemem Linux na platformie Azure, Połącz z maszyną Wirtualną usługi Azure Storage i współużytkują magazyn za pośrednictwem serwera systemu plików NFS skonfigurowanych na tej maszynie Wirtualnej. Jeśli wykonanie kopii zapasowej dla woluminów, które bezpośrednio dołączania do dużych wystąpień HANA jednostek, skopiuj kopie zapasowe do konta usługi Azure storage. W tym po skonfigurowaniu maszyny Wirtualnej platformy Azure, które eksportuje udziałów NFS, które są oparte na usłudze Azure Storage. Można również użyć usługi Azure Backup vault lub Azure zimnego magazynu. 

   Innym rozwiązaniem jest na potrzeby przechowywania kopii zapasowych po zostaną skopiowane do konta usługi Azure storage to narzędzie ochrony danych innych firm. Opcja kopii zapasowej możesz również konieczne może być dla danych, które będą przechowywane przez dłuższy czas do celów inspekcji i zgodności. We wszystkich przypadkach kopie zapasowe są kopiowane do udziałów NFS, reprezentowane przez maszynę Wirtualną i usługi Azure Storage.

- **Infrastruktura kopii zapasowej i przywracania oferowane.** Możesz również użyć kopii zapasowej i przywrócenia jej funkcjonalności, który zapewnia podstawową infrastrukturą platformy SAP HANA na platformie Azure (duże wystąpienia). Ta opcja spełnia potrzeby kopii zapasowych oraz szybkie ich przywrócenie. Pozostałej części tej sekcji eliminuje funkcje i przywracania kopii zapasowych, które jest oferowana z dużymi wystąpieniami platformy HANA. W tej sekcji omówiono również relacji, których kopia zapasowa i przywracanie po awarii odzyskiwania funkcjonalność oferowana przez dużych wystąpień HANA.

> [!NOTE]
>   Technologia migawki, która jest używana przez podstawową infrastrukturą dużych wystąpień HANA zależny od oprogramowania SAP HANA migawki. W tym momencie migawek platformy SAP HANA nie działają w połączeniu z wieloma dzierżawami kontenery wielodostępne bazy danych SAP HANA. Jeśli tylko jednej dzierżawy zostanie wdrożona, migawek platformy SAP HANA będą działać, a następnie można użyć tej metody.

## <a name="use-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Używaj migawek magazynu oprogramowania SAP Hana na platformie Azure (duże wystąpienia)

Infrastruktura magazynu, podstawowe oprogramowanie SAP HANA na platformie Azure (duże wystąpienia) obsługuje Magazyn migawek woluminów. Kopia zapasowa i Przywracanie woluminów jest obsługiwane przez następujące kwestie:

- Zamiast tworzenia pełnych kopii zapasowych migawek woluminu magazynu są pobierane częste.
- Gdy migawki jest wyzwalany przez /hana/data /hana/shared, w tym /usr/sap, woluminami, technologia migawki inicjuje SAP HANA migawki przed uruchamia migawki magazynu. Ta migawka platformy SAP HANA jest punktem instalacji dla przywracana ostateczna dziennika po odzyskaniu migawki magazynu. Dla migawki HANA zakończy się powodzeniem należy aktywnego wystąpienia platformy HANA. W scenariuszu HSR migawki magazynu nie jest obsługiwany w bieżącej węzła pomocniczego, których nie można wykonać migawki platformy HANA.
- Po pomyślnym uruchomieniu migawki pamięci masowej migawki platformy SAP HANA jest usuwany.
- Kopie zapasowe dziennika transakcji są często wykonywane i przechowywanych w woluminie /hana/logbackups lub na platformie Azure. Możesz wyzwolić woluminu /hana/logbackups, który zawiera kopie zapasowe dziennika transakcji, aby zrobić migawkę oddzielnie. W takiej sytuacji nie trzeba uruchomić migawki platformy HANA.
- Jeśli musisz przywrócić bazę danych do pewnego momentu w czasie awarii produkcji, zażądać tej obsługi platformy Microsoft Azure lub SAP HANA podczas przywracania systemu Azure do migawki magazynu. Przykładem jest planowane Przywracanie systemu piaskownicy do stanu pierwotnego.
- Migawka platformy SAP HANA, który znajduje się w tle magazynu jest punkt przesunięcia stosowania kopie zapasowe dziennika transakcji, które uruchomiono które były przechowywane po migawki magazynu.
- Te kopie zapasowe dziennika transakcji przejście do przywrócenia bazy danych do pewnego momentu w czasie.

Można wykonywać migawek magazynu, przeznaczonych dla trzech klas woluminów:

- Połączone migawki/hana/dane oraz/hana/udostępniony, w tym /usr/sap. Ta migawka wymaga utworzenia migawki platformy SAP HANA jako przygotowania do migawki magazynu. Migawki platformy SAP HANA gwarantuje, że baza danych jest w stanie spójnym z punktu widzenia magazynu. Proces przywracania to punkt, aby skonfigurować na.
- Oddzielne migawkę za pośrednictwem/hana/logbackups.
- Partycja systemu operacyjnego.

Aby uzyskać najnowszą migawkę skryptów oraz dokumentacji, zobacz [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). Po pobraniu pakietu skryptu migawki z [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0), uzyskać trzy pliki. Jeden z plików jest opisane w pliku PDF do funkcje udostępniane. Po pobraniu zestawu narzędzi, postępuj zgodnie z instrukcjami w "Pobierz narzędzia snapshot".

## <a name="storage-snapshot-considerations"></a>Zagadnienia dotyczące migawek magazynu

>[!NOTE]
>Migawek magazynu wykorzystywać miejsce do magazynowania przydzielone do jednostki dużych wystąpień HANA. Należy wziąć pod uwagę następujące aspekty planowania migawek magazynu i jak wiele migawek magazynu, aby zachować. 

Określone mechanika migawek magazynu oprogramowania SAP HANA na platformie Azure (duże wystąpienia) obejmują:

- Określonego magazynu migawkę punktu w czasie, gdy jest ona traktowana zużywa niewiele pamięci masowej.
- Dane zmiany zawartości i zawartości danych SAP HANA, pliki dopasowane na woluminie magazynu migawki wymaga przechowywania oryginalną zawartość bloku i zmian danych.
- W rezultacie migawki magazynu zwiększa rozmiar. Już istnieje migawki, im większy staje się migawki magazynu.
- Więcej zmian wprowadzonych na woluminie bazy danych SAP HANA w okresie istnienia magazynu migawek, większy wykorzystanie miejsca w pamięci masowej migawki.

Oprogramowanie SAP HANA na platformie Azure (duże wystąpienia) zawiera stałą woluminy o maksymalnym rozmiarze dla woluminów danych i dziennika platformy SAP HANA. Wykonywanie migawek tych woluminów eats do ilość miejsca na woluminie. Następujące czynności:

- Ustalanie, kiedy należy zaplanować migawek magazynu.
- Monitoruj wykorzystanie miejsca woluminów magazynu. 
- Zarządzaj liczby migawek, które są przechowywane. 

Można wyłączyć migawek magazynu, zarówno przy zaimportować mas dane i wykonywać inne ważne zmiany w bazie danych HANA. 


Poniższe sekcje zawierają informacje dotyczące wykonywania migawek i obejmują ogólne zalecenia:

- Mimo że sprzętu może wytrzymać 255 migawek na woluminie, mają pozostać znacznie poniżej tej liczby. Zalecane jest 250 lub mniej.
- Przed wykonaniem migawki magazynu, monitorować i śledzić ilość wolnego miejsca.
- Zmniejsz liczbę migawek magazynu, w oparciu o ilość wolnego miejsca. Można zmniejszyć liczbę migawek, które są stale lub można rozszerzyć woluminów. Może zamówić łączność obejmującą dodatkowego miejsca do magazynowania w jednostkach 1 terabajt.
- Podczas działania, takie jak przenoszenie danych do platformy SAP HANA przy użyciu narzędzi migracji platformy SAP (R3load) lub przywracania baz danych SAP HANA z kopii zapasowych należy wyłączyć migawek magazynu na woluminie /hana/data. 
- Podczas większych reorganizacji tabel platformy SAP HANA należy unikać migawek magazynu, jeśli jest to możliwe.
- Migawki magazynu to warunek wstępny do wykorzystując po awarii możliwości odzyskiwania systemu SAP HANA na platformie Azure (duże wystąpienia).

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>Wymagania wstępne dotyczące korzystania z migawek magazynu samoobsługi

Aby pomyślnym uruchomieniu skryptu migawki, upewnij się, że Perl jest zainstalowany w systemie operacyjnym Linux na serwerze dużych wystąpień HANA. W Twojej jednostce dużych wystąpień HANA preinstalowane Perl. Aby sprawdzić wersję środowiska Perl, użyj następującego polecenia:

`perl -v`

![Klucz publiczny jest kopiowany, uruchamiając następujące polecenie](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


## <a name="set-up-storage-snapshots"></a>Konfigurowanie migawek magazynu

Aby skonfigurować migawek magazynu przy użyciu dużych wystąpień HANA, wykonaj następujące kroki.
1. Upewnij się, że Perl jest zainstalowany w systemie operacyjnym Linux na serwerze dużych wystąpień HANA.
1. Modyfikowanie/etc/ssh/ssh\_konfiguracji, aby dodać wiersz _Mac hmac-sha1_.
1. W węźle głównym dla każdego wystąpienia platformy SAP HANA, który zostanie uruchomiony, należy utworzyć konto kopii zapasowych oprogramowania SAP HANA, jeśli ma to zastosowanie.
1. Zainstaluj klienta SAP HANA HDB na wszystkich serwerach duże wystąpienia SAP HANA.
1. Na pierwszym serwerze duże wystąpienia SAP HANA każdego regionu należy utworzyć klucz publiczny do dostępu do podstawowej infrastruktury magazynu, który kontroluje tworzenia migawki.
1. Skopiuj skrypty i pliku konfiguracji z [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0) do lokalizacji **hdbsql** w instalacji oprogramowania SAP HANA.
1. Modyfikowanie *HANABackupDetails.txt* plików, gdy jest to konieczne do specyfikacji odpowiedniego klienta.

Pobierz najnowsze skrypty migawki i dokumentacji [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). Informacje dotyczące kroków wymienionych powyżej, zobacz [Microsoft migawki narzędzia dla oprogramowania SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

### <a name="consideration-for-mcod-scenarios"></a>Ważną kwestią dotyczącą MCOD scenariuszy
Jeśli uruchamiasz [scenariusza MCOD](https://launchpad.support.sap.com/#/notes/1681092) z wieloma wystąpieniami platformy SAP HANA w jednej jednostce dużych wystąpień HANA mieć osobne woluminy magazynu zainicjowana obsługa administracyjna dla wszystkich wystąpień oprogramowania SAP HANA. Aby uzyskać więcej informacji na temat MDC i inne zagadnienia, zobacz "Ważnych rzeczy do zapamiętania" w [Microsoft migawki narzędzia dla oprogramowania SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>Krok 1: Zainstaluj klienta SAP HANA HDB

System operacyjny Linux, które są zainstalowane na platformie SAP HANA na platformie Azure (duże wystąpienia) zawiera foldery i skrypty niezbędne do uruchomienia migawek magazynu oprogramowania SAP HANA dla kopii zapasowych i odzyskiwanie po awarii do celów odzyskiwania. Wyszukaj więcej najnowsze wersje w [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). Najbardziej aktualną wersję wydania skryptów jest 4.0. Różne skrypty mogą mieć różne wersje pomocnicze w ramach tej samej wersji głównej.

Jest odpowiedzialny za do zainstalowania klienta SAP HANA HDB w jednostkach dużych wystąpień HANA, podczas instalacji oprogramowania SAP HANA.

### <a name="step-2-change-the-etcsshsshconfig"></a>Krok 2: Zmienianie/etc/ssh/ssh\_konfiguracji

W tym kroku opisano w "Włączanie komunikacji z magazynem" w [Microsoft migawki narzędzia dla oprogramowania SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).


### <a name="step-3-create-a-public-key"></a>Krok 3: Tworzenie klucza publicznego

Aby włączyć dostęp do interfejsów migawki magazynu Twojej dzierżawy dużych wystąpień HANA, należy ustanowić procedura logowania za pomocą klucza publicznego. 

Na pierwszy SAP HANA na serwerze Azure (duże wystąpienia) w dzierżawie Utwórz klucz publiczny do dostępu do infrastruktury magazynu. Za pomocą klucza publicznego do logowania się na interfejsy migawki magazynu nie jest wymagane hasło. Ponadto nie trzeba utrzymywać poświadczeń haseł przy użyciu klucza publicznego. 

Aby wygenerować klucz publiczny, zobacz "Włączanie komunikacji z magazynem" w [Microsoft migawki narzędzia dla oprogramowania SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).


### <a name="step-4-create-an-sap-hana-user-account"></a>Krok 4: Tworzenie konta użytkownika platformy SAP HANA

Aby rozpocząć tworzenie migawek platformy SAP HANA, należy utworzyć konto użytkownika w, można użyć skryptów migawek magazynu oprogramowania SAP HANA. Tworzenie konta użytkownika platformy SAP HANA w obrębie SAP HANA Studio, w tym celu. Można utworzyć użytkownika w obszarze SYSTEMDB i *nie* w bazie identyfikatora SID dla MDC. W środowisku jednego kontenera użytkownik jest tworzony w bazie danych dzierżawy. To konto musi mieć **administratora kopii zapasowych** i **odczytu katalogu** uprawnień. 

Aby skonfigurować i korzystać z konta użytkownika, zobacz "Włącz komunikację z platformą SAP HANA" w [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0).


### <a name="step-5-authorize-the-sap-hana-user-account"></a>Krok 5. Autoryzacja konta użytkownika platformy SAP HANA

W tym kroku możesz autoryzować konta użytkownika platformy SAP HANA, który został utworzony tak, aby skrypty nie ma potrzeby przesyłania haseł w czasie wykonywania. Polecenie platformy SAP HANA `hdbuserstore` umożliwia tworzenie klucza interfejsu użytkownika platformy SAP HANA. Klucz jest przechowywany na co najmniej jeden węzeł oprogramowania SAP HANA. Klucz użytkownika umożliwia dostęp użytkowników oprogramowania SAP HANA, bez konieczności zarządzania hasłami z w ramach procesu wykonywania skryptów. Proces wykonywania skryptów jest omówione w dalszej części tego artykułu.

>[!IMPORTANT]
>Uruchom następujące polecenia konfiguracji, za pomocą polecenia migawki są uruchamiane w ten sam kontekst użytkownika. W przeciwnym razie poleceń migawki nie będzie działać prawidłowo.


### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>Krok 6: Pobieranie skryptów migawki, skonfiguruj migawki i testowania konfiguracji i łączność

Pobierz najnowszą wersję skryptów z [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). Zmienić sposób zainstalowania skrypty w wersji 4.0 skryptów. Aby uzyskać więcej informacji, zobacz "Włącz komunikację z platformą SAP HANA" w [Microsoft migawki narzędzia dla oprogramowania SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Aby uzyskać dokładną sekwencję wybranych poleceń, zobacz "Łatwa instalacja narzędzia snapshot (ustawienie domyślne)" w [Microsoft migawki narzędzia dla oprogramowania SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). Firma Microsoft zaleca użycie domyślnej instalacji. 

Aby przeprowadzić uaktualnienie z wersji 3.x do 4.0, zobacz "Uaktualnianie istniejącej instalacji" w [Microsoft migawki narzędzia dla oprogramowania SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). Aby odinstalować zestaw narzędzi w wersji 4.0, zobacz "Odinstalowywania narzędzia snapshot" w [Microsoft migawki narzędzia dla oprogramowania SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Nie zapomnij wykonaj kroki opisane w "Ukończenia instalacji narzędzia snapshot" w [Microsoft migawki narzędzia dla oprogramowania SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Celem różnych skryptów i plików zgodnie z ich przeprowadzona pomyślnie, opisano w "Co to są narzędzia te migawki?" w [Microsoft migawki narzędzia dla oprogramowania SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Przed rozpoczęciem konfigurowania narzędzia migawki, upewnij się, również skonfigurowanych lokalizacji kopii zapasowej platformy HANA i ustawienia poprawnie. Aby uzyskać więcej informacji, zobacz "SAP HANA konfiguracji" w [Microsoft migawki narzędzia dla oprogramowania SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Konfiguracja zestawu narzędzi migawki opisanej w konfiguracji "pliku" - HANABackupCustomerDetails.txt w [Microsoft migawki narzędzia dla oprogramowania SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

#### <a name="test-connectivity-with-sap-hana"></a>Przetestowanie łączności z platformą SAP HANA

Po umieszczeniu wszystkich danych konfiguracji do *HANABackupCustomerDetails.txt* plików, należy sprawdzić, czy konfiguracje są poprawne dane wystąpienie oprogramowania HANA. Użyj skryptu `testHANAConnection`, który jest niezależne skalowanie w pionie lub poziomie konfiguracji SAP HANA.

Aby uzyskać więcej informacji, zobacz "Sprawdzanie łączności z platformą SAP HANA — testHANAConnection" w [Microsoft migawki narzędzia dla oprogramowania SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

#### <a name="test-storage-connectivity"></a>Przetestowanie łączności z magazynu

Następnym krokiem testu jest aby sprawdzić połączenie z magazynu, w oparciu o dane umieszczane w *HANABackupCustomerDetails.txt* pliku konfiguracji. Następnie uruchom migawkę testu. Przed uruchomieniem `azure_hana_backup` polecenia, należy uruchomić ten test. Dla sekwencji poleceń dla tego testu, zobacz "Sprawdzanie łączności z usługą storage - testStorageSnapshotConnection" "w [Microsoft migawki narzędzia dla oprogramowania SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Po pomyślnym zalogowaniu interfejsom magazynu maszyny wirtualnej skrypt będzie kontynuowane z użyciem fazy 2 i tworzących migawkę testu. Dane wyjściowe są tutaj wyświetlane trzema węzłami skalowalnego w poziomie konfiguracji SAP Hana.

Jeśli migawki testu pomyślnym uruchomieniu skryptu można zaplanować migawek rzeczywisty magazyn. Jeśli nie jest pomyślnie, należy zbadać problemy przed przejściem do przodu. Migawki testu powinno pozostać wokół, dopóki pierwszych rzeczywistych migawek są wykonywane.


### <a name="step-7-perform-snapshots"></a>Krok 7: Wykonaj migawki

Po zakończeniu kroków przygotowania, można uruchomić można skonfigurować i zaplanować migawek rzeczywisty magazyn. Skrypt do zaplanowania współpracuje z platformy SAP HANA skalowanie w górę i skalowania w poziomie konfiguracji. Okresowe i regularnego wykonywania skryptu kopii zapasowej należy zaplanować skryptu za pomocą narzędzia cron. 

Aby uzyskać pełne polecenie składnię i funkcje, zobacz "Wykonywanie kopii zapasowej migawki - azure_hana_backup" w [Microsoft migawki narzędzia dla oprogramowania SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). 

Gdy skrypt `azure_hana_backup` jest uruchamiany, tworzy Magazyn migawek na następujące trzy etapy:

1. Jest ono uruchamiane migawki platformy SAP HANA.
1. Jest ono uruchamiane migawki magazynu.
1. Usuwa migawkę platformy SAP HANA, który został utworzony przed uruchomiono migawki magazynu.

Aby uruchomić skrypt, należy wywołać go z folderu pliku wykonywalnego HDB, do którego została skopiowana. 

Okres przechowywania jest podawana przy użyciu liczby migawek, które są przesyłane jako parametr, po uruchomieniu skryptu. Ilość czasu, który pasuje do żadnego migawek magazynu jest funkcją czasu wykonywania i liczby migawek przesyłane jako parametr, gdy skrypt zostanie uruchomiony. 

W przypadku liczby migawek, które są utrzymywane przekracza liczbę, które są nazywane jako parametr w wywołaniu skryptu, najstarsze migawki magazynu w tej samej etykiety zostaną usunięte, przed uruchomieniem nową migawkę. Liczba zapewniają jako ostatni parametr wywołanie jest liczba służących do kontrolowania liczby migawek, które są zachowane. Z tym numerem, można również sterować, pośrednio, ilości miejsca na dysku używanego dla migawki. 


## <a name="snapshot-strategies"></a>Strategie migawki
Częstotliwość migawek dla różnych typów, zależy od tego, czy korzystasz z funkcji odzyskiwania po awarii dużych wystąpień HANA. Ta funkcja opiera się na migawek magazynu, w których może wymagać specjalnego zalecenia dla okresów częstotliwość i wykonywanie migawek magazynu. 

Zagadnienia i zalecenia, które należy wykonać, zakłada się, że czy *nie* korzystać z funkcji odzyskiwania po awarii, która oferuje dużych wystąpień HANA. Zamiast tego należy użyć migawek magazynu kopii zapasowych i być w stanie zapewnić odzyskiwanie w momencie w ciągu ostatnich 30 dni. Biorąc pod uwagę ograniczenia liczby migawek i miejsce, należy wziąć pod uwagę następujące wymagania:

- Czas odzyskiwania w momencie odzyskiwania.
- Miejsce.
- Punkt odzyskiwania i cele czasu odzyskiwania do potencjalnego odzyskiwania po awarii.
- Ostateczna wykonanie względem dysków, kopie zapasowe bazy danych pełnej platformy HANA. Zawsze, gdy kopii zapasowej pełnej obsługi bazy danych względem dysków lub **backint** interfejsu jest wykonywane, wykonywanie migawek magazynu kończy się niepowodzeniem. Jeśli planujesz uruchamianie bazy danych pełni tworzenia kopii zapasowych na podstawie migawek magazynu, upewnij się, że wykonywanie migawek magazynu jest wyłączona w tym czasie.
- Liczba migawek na wolumin, który jest ograniczona do 250.

<!-- backint is term for a SAP HANA interface and not a spelling error not spelling errors -->

Jeśli nie używasz funkcji odzyskiwania po awarii w dużych wystąpień HANA, okres migawki jest dłuższe interwały. W takich przypadkach należy wykonać migawki połączone /hana/data i /hana/shared, który zawiera /usr/sap, 12-godzinny lub 24-godzinny okresów. Zachowaj migawki przez jeden miesiąc. To samo dotyczy migawek woluminu kopii zapasowej dziennika. Wykonywanie kopii zapasowej dziennika transakcji platformy SAP HANA na woluminie kopii zapasowej dziennika występuje w 5-minutowych do 15-minutowy okresów.

Zaplanowany magazynu migawek najlepiej są wykonywane za pomocą usługi cron. Użyj tego samego skryptu wszystkich kopii zapasowych i przywracania po awarii. Zmodyfikuj skrypt danych wejściowych do dopasowania do różnych żądane godziny tworzenia kopii zapasowej. Te migawki są wszystkie zaplanowane inaczej w cron w zależności od ich czas wykonywania. Może być co godzinę, co 12 godzin, codziennie lub co tydzień. 

Poniższy przykład przedstawia harmonogramu wyrażenia cron w /etc/crontab:
```
00 1-23 * * * ./azure_hana_backup --type=hana --prefix=hourlyhana --frequency=15min --retention=46
10 00 * * *  ./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
22 12 * * *  ./azure_hana_backup --type=logs --prefix=dailylogback --frequncy=3min --retention=28
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```
W poprzednim przykładzie co godzinę migawki połączone obejmuje woluminów, które zawierają /hana/data i /hana/shared/SID, w tym /usr/sap i lokalizacji. Na użytek tego typu migawka szybsze odzyskiwanie w momencie w ciągu ostatnich dwóch dni. Istnieje również dzienne migawki na tych woluminach. Dlatego masz dwa dni obowiązywania przez godzinę migawki, a także cztery tygodnie pokrycia, dzienne migawki. Wolumin kopii zapasowej dziennika transakcji jest również wykonywana kopia zapasowa codziennie. Te kopie zapasowe są przechowywane w czterech tygodni. 

Jak widać w trzecim wierszu crontab kopii zapasowej dziennika transakcji HANA jest zaplanowane do uruchomienia co 5 minut. Godziny rozpoczęcia zadań różnych cron, które Uruchom migawek magazynu są zróżnicowane. W ten sposób migawki nie uruchamiaj w całości w pewnym momencie w czasie. 

W poniższym przykładzie możesz wykonać połączone migawkę, która obejmuje woluminów, które zawierają /hana/data i /hana/shared/SID, który zawiera /usr/sap, lokalizacje w systemie godzinowym. Te migawki są przechowywane przez dwa dni. Migawki woluminów kopii zapasowej dziennika transakcji uruchom na podstawie 5-minutowych i są przechowywane przez kilka godzin. Jak wcześniej, kopia zapasowa pliku dziennika transakcji HANA jest zaplanowane do uruchomienia co 5 minut. 

Migawki woluminu kopii zapasowej dziennika transakcji jest wykonywane z opóźnieniem 2-minutowy po rozpoczęciu kopii zapasowej dziennika transakcji. W normalnych warunkach kopia zapasowa dziennika transakcji platformy SAP HANA zakończy się w ciągu tych dwóch minut. Jako przed, woluminu rozruchowego jednostki LUN kopia zapasowa jest tworzona raz dziennie migawki magazynu i jest przechowywany przez cztery tygodnie.

```
10 0-23 * * * ./azure_hana_backup --type=hana ==prefix=hourlyhana --frequency=15min --retention=48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup --type=logs --prefix=logback --frequency=3min --retention=48
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

Poniższa ilustracja przedstawia sekwencje poprzedniego przykładu. Rozruch jednostki LUN są wyłączone.

![Relacja między migawki i kopii zapasowych](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA przeprowadza regularne zapisu woluminu /hana/log dokumentowanie zatwierdzone zmiany w bazie danych. Na bieżąco SAP HANA zapisuje punktu zapisu do woluminu /hana/data. Określone w crontab kopia zapasowa dziennika transakcji platformy SAP HANA jest uruchamiane co 5 minut. 

Zobaczysz również, czy migawki platformy SAP HANA uruchamiany co godzinę w wyniku wyzwalania połączonego magazynu, migawki za pośrednictwem woluminów /hana/data i /hana/shared/SID. Po pomyślnym migawki HANA połączonego magazynu migawek przebiegów. Zgodnie z instrukcją w crontab, migawek magazynu na woluminie /hana/logbackup jest uruchamiane co 5 minut, około 2 minuty po kopii zapasowej dziennika transakcji HANA.

> 

>[!IMPORTANT]
> Korzystanie z migawek magazynu kopii zapasowych oprogramowania SAP HANA jest przydatna tylko wtedy, gdy migawki są wykonywane w połączeniu z kopie zapasowe dziennika transakcji platformy SAP HANA. Te kopie zapasowe dziennika transakcji muszą obejmować okresach czasu między migawek magazynu. 

Jeśli wybierzesz zobowiązanie do użytkowników w momencie odzyskiwania przez 30 dni, należy:

- Dostęp do migawek magazynu połączone za pośrednictwem /hana/data i /hana/shared/SID, który jest 30 dni, w ekstremalnych przypadkach. 
- Mieć kopie zapasowe dziennika transakcji ciągłych, które obejmują czas między dowolnymi migawek magazynu połączone. Tak najstarsze migawki woluminu kopii zapasowej dziennika transakcji musi być 30 dni. To nie jest tak, jeśli kopiujesz kopie zapasowe dziennika transakcji do innego udziału NFS, który znajduje się w usłudze Azure Storage. W takim przypadku może pobierać stary kopie zapasowe dziennika transakcji z tego folderu wspólnego systemu plików NFS.

Aby korzystać z magazynu migawek i replikacji magazynu ostatecznej kopie zapasowe dziennika transakcji, należy zmienić lokalizację, do którego platformy SAP HANA zapisuje kopie zapasowe dziennika transakcji. Można wprowadzić tę zmianę w Studio platformy HANA. 

Mimo że oprogramowanie SAP HANA tworzy kopie zapasowe segmentów pełny dziennik automatycznie, należy określić interwałem wykonywania kopii zapasowej dziennika, aby być deterministyczna. Jest to szczególnie istotne w przypadku, gdy używasz opcji odzyskiwania po awarii, ponieważ zazwyczaj chcesz wykonywać kopie zapasowe dziennika z okresem deterministyczna. W poniższym przypadku 15 minut jest ustawiony jako interwałem wykonywania kopii zapasowej dziennika.

![Zaplanuj dzienniki kopii zapasowych oprogramowania SAP HANA w systemie SAP HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

Możesz również wybrać tworzenie kopii zapasowych, które są częściej niż co 15 minut. Częstsze ustawienie jest często używane w połączeniu z dużymi wystąpieniami platformy HANA działania funkcji odzyskiwania po awarii. Niektórzy klienci wykonywać kopie zapasowe dziennika transakcji co 5 minut.

Jeśli baza danych nigdy nie wykonano kopię zapasową, ostatnim krokiem jest wykonanie kopii zapasowej na podstawie pliku bazy danych do utworzenia pojedynczy wpis kopii zapasowej, który musi istnieć w wykaz kopii zapasowych. W przeciwnym razie platformy SAP HANA nie można zainicjować kopie zapasowe określonego dziennika.

![Tworzenie kopii zapasowych opartych na plikach do utworzenia pojedynczego wpisu tworzenia kopii zapasowej](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


Po uruchomieniu usługi pierwszych migawek powodzeniem, należy usunąć migawkę testu, uruchomione w kroku 6. Aby uzyskać więcej informacji, zobacz "Usuwanie migawek test - removeTestStorageSnapshot" w [Microsoft migawki narzędzia dla oprogramowania SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). 


### <a name="monitor-the-number-and-size-of-snapshots-on-the-disk-volume"></a>Monitorowanie liczby i rozmiaru migawki na woluminie dysku

Na woluminie określonego magazynu można monitorować liczbę migawek i użyciu przestrzeni dyskowej, tych migawek. `ls` Polecenie nie wyświetla katalog migawki lub plików. Polecenia systemu operacyjnego Linux `du` przedstawia szczegółowe informacje dotyczące tych migawek magazynu, ponieważ są one przechowywane w tej samej wielkości. Użyj polecenia przy użyciu następujących opcji:

- `du –sh .snapshot`: Ta opcja zapewnia daje w sumie wszystkich migawek Directory migawki.
- `du –sh --max-depth=1`: Ta opcja wyświetla wszystkie migawki, które są zapisywane w **.snapshot** folder i rozmiar każdej migawki.
- `du –hc`: Ta opcja zapewnia całkowity rozmiar używany przez wszystkie migawki.

Użyj tych poleceń, aby upewnić się, że migawki, które są wykonywane i przechowywane nie wykorzystasz wszystkich magazynu na woluminach.

>[!NOTE]
>Migawki rozruchu jednostki LUN nie są widoczne przy użyciu poprzednich poleceń.

### <a name="get-details-of-snapshots"></a>Pobierz szczegóły migawki
Aby uzyskać więcej informacji na temat migawek, użyj skryptu `azure_hana_snapshot_details`. Ten skrypt można uruchomić w dowolnej lokalizacji, w przypadku aktywnego serwera w lokalizacji odzyskiwania danych po awarii. Skrypt zawiera następujące dane wyjściowe z podziałem na każdy wolumin, który zawiera migawki: 
   * Rozmiar całkowitej migawek woluminu
   * Następujące informacje w każdej migawki w danym woluminie: 
      - Nazwa migawki 
      - Czas utworzenia 
      - Rozmiar migawki
      - Częstotliwość migawek
      - Identyfikator kopii zapasowej HANA skojarzone z tej migawki, jeśli jest to odpowiednie

Aby uzyskać składnię polecenia i danych wyjściowych, zobacz "Wyświetlanie listy migawek — azure_hana_snapshot_details" w [Microsoft migawki narzędzia dla oprogramowania SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). 



### <a name="reduce-the-number-of-snapshots-on-a-server"></a>Zmniejsz liczbę migawek na serwerze

Jak wyjaśniono wcześniej można zmniejszyć liczbę niektóre etykiety migawek, które są przechowywane. Ostatnie dwa parametry polecenia, aby zainicjować migawki są etykiety i liczby migawek, które chcesz zachować.

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
```

W poprzednim przykładzie, etykieta migawki jest **dailyhana**. Liczba migawek oznaczone tą etykietą mają być przechowywane jest **28**. Jak odpowiedzieć użycie miejsca na dysku, można zmniejszyć liczbę migawek przechowywanych. Prosty sposób, aby zmniejszyć liczbę migawek do 15, na przykład, jest uruchomienie skryptu z ostatniego parametru równa **15**:

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=15
```

Po uruchomieniu skryptu z tym ustawieniem liczby migawek, która obejmuje nową migawkę pamięci masowej, to 15. 15 ostatnich migawki są przechowywane i 15 migawek starsze są usuwane.

 >[!NOTE]
 > Ten skrypt powoduje zmniejszenie liczby migawek, tylko wtedy, gdy istnieje więcej niż jedną godzinę migawki. Skrypt nie powoduje usunięcia migawek, które są mniej niż o jedną godzinę. Ograniczenia te są powiązane funkcje odzyskiwania po awarii opcjonalne, które są oferowane.

Jeśli nie chcesz już przechowywać zestawu migawek kopii zapasowych prefiksem **dailyhana** w przykłady składni, uruchom skrypt za pomocą **0** jako liczba przechowywania. Następnie zostaną usunięte wszystkie migawki, które odpowiadają tej etykiety. Usunięcie wszystkich migawek może mieć wpływ na możliwości dużych wystąpień HANA działania funkcji odzyskiwania po awarii.

Drugą opcję, aby usunąć migawki określonej jest użycie skryptu `azure_hana_snapshot_delete`. Ten skrypt jest przeznaczony do usunięcia migawki lub zestawu migawek, albo za pomocą Identyfikatora kopii zapasowej w HANA jak ustalono, HANA Studio lub za pomocą sama nazwa migawki. Obecnie identyfikator kopii zapasowej jest powiązany tylko z migawek utworzonych dla **hana** typ migawki. Tworzenie migawki kopii zapasowych typu **dzienniki** i **rozruchu** nie wykonują platformy SAP HANA, migawki, więc nie ma żadnych identyfikator kopii zapasowej do znalezienia dla tych migawek. Jeśli wprowadzona nazwa migawki, wyszukuje wszystkie migawki na różnych woluminach, które pasuje do nazwy wprowadzone migawki. 

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->

Aby uzyskać więcej informacji na temat skryptu, zobacz "Usuń migawki - azure_hana_snapshot_delete" w [Microsoft migawki narzędzia dla oprogramowania SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Uruchom skrypt jako użytkownik **głównego**.

>[!IMPORTANT]
>W przypadku danych, który istnieje tylko w migawce zamierzasz usunąć, po usunięciu migawki, że dane są trwale utracone.


## <a name="file-level-restore-from-a-storage-snapshot"></a>Poziom pliku przywracania z migawki magazynu

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->
Dla typów migawki **hana** i **dzienniki**, są dostępne migawki bezpośrednio na woluminach **.snapshot** katalogu. Brak podkatalogu dla każdego z migawki. Skopiuj każdy plik w stanie, w jakim były one na punkcie migawki z tym podkatalogu w strukturze katalogu. 

W bieżącej wersji skryptu Brak *nie* skryptu dostarczonego do przywrócenia migawki jako samoobsługowego przywracania. Przywracanie z migawki może zostać wykonana jako część skrypty odzyskiwania samoobsługowego po awarii w lokacji odzyskiwania po awarii, podczas trybu failover. Aby przywrócić żądanej migawki z istniejącej migawki dostępne, możesz skontaktować się zespół operacyjny firmy Microsoft, otwierając żądanie obsługi.

>[!NOTE]
>Pojedynczy plik przywracania nie działa dla migawek rozruchu niezależna od typu jednostki dużych wystąpień HANA jednostki LUN. **.Snapshot** katalogu nie jest widoczne w rozruchu jednostki LUN. 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>Odzyskaj do najnowszej migawki HANA

W scenariuszu produkcji w dół proces odzyskiwania z migawki magazynu można uruchomić jako zdarzenia klienta, pomocy technicznej firmy Microsoft Azure. Jeśli dane zostały usunięte w systemie produkcyjnym i jest jedynym sposobem, aby go pobrać, można przywrócić produkcyjną bazę danych, jest kwestią wysoka pilność.

W innej sytuacji w momencie odzyskiwania może być niski pilność i planowane-dniowym wyprzedzeniem. Możesz zaplanować to odzyskiwanie z platformą SAP HANA na platformie Azure, zamiast wywoływania flagi o wysokim priorytecie. Na przykład może zaplanować uaktualnienie oprogramowania SAP, stosując nowego pakietu rozszerzenia. Następnie należy przywrócić do migawki, który reprezentuje stan przed wykonaniem uaktualnienia pakietu rozszerzenia.

Przed wysłaniem żądania, które trzeba przygotować. Oprogramowanie SAP HANA na zespół platformy Azure można obsłużyć żądania i podaj przywracanych woluminów. Potem możesz przywrócić bazy danych HANA, oparte na migawki.

Możliwości w celu uzyskania migawki przywrócona na nowy zestaw narzędzi, zobacz "Jak przywrócić migawkę" w [przewodnik odzyskiwania ręcznego w przypadku oprogramowania SAP HANA na platformie Azure z migawki magazynu](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf).

Aby przygotować się do żądania, wykonaj następujące kroki.

1. Zdecyduj, które migawek do przywrócenia. Tylko wolumin hana/danych zostanie przywrócony, chyba że poinstruować w inny sposób. 

1. Zamknij wystąpienie oprogramowania HANA.

   ![Zamknij wystąpienie oprogramowania HANA](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. Odinstaluj woluminy danych w każdym węźle bazy danych HANA. Woluminy danych nadal są zainstalowane w systemie operacyjnym, przywracanie migawki nie powiedzie się.

   ![Odinstaluj woluminy danych w każdym węźle bazy danych HANA](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. Otwórz żądanie pomocy technicznej platformy Azure i zawierają instrukcje informujące o przywrócenie określoną migawkę:

   - Podczas przywracania: SAP HANA w usłudze Azure Service może poprosić Cię o weź udział w konferencji, do koordynowania, sprawdź i upewnij się, że migawki magazynu poprawną został przywrócony. 

   - Po przywróceniu: SAP HANA w usłudze Azure Service powiadamia użytkownika, po przywróceniu migawki magazynu.

1. Po ukończeniu procesu przywracania, należy ponownie zainstalować wszystkie woluminy danych.

   ![Zainstaluj wszystkie woluminy danych](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)



Inną możliwością pobierania, na przykład wznowiła działanie po migawki magazynu plików danych SAP HANA jest udokumentowany w kroku 7 [przewodnik odzyskiwania ręcznego w przypadku oprogramowania SAP HANA na platformie Azure z migawki magazynu](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf).

Aby przywrócić z kopii zapasowej migawki, zobacz [przewodnik odzyskiwania ręcznego w przypadku oprogramowania SAP HANA na platformie Azure z migawki magazynu](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf). 

>[!Note]
>Migawek została przywrócona przez operacje firmy Microsoft, nie trzeba krok 7.


### <a name="recover-to-another-point-in-time"></a>Odzyskaj do innego punktu w czasie
Aby przywrócić do pewnego momentu w czasie, zobacz "Odzyskiwanie bazy danych do następujących punktu w czasie" w [przewodnik odzyskiwania ręcznego w przypadku oprogramowania SAP HANA na platformie Azure z migawki magazynu](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf). 


## <a name="next-steps"></a>Kolejne kroki
- Zobacz [zasady odzyskiwania po awarii i przygotowania](hana-concept-preparation.md).
