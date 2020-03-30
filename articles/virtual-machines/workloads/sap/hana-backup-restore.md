---
title: Hana kopii zapasowej i przywracania na SAP HANA na platformie Azure (duże wystąpienia) | Dokumenty firmy Microsoft
description: Jak wykonać kopię zapasową hana i przywrócić na SAP HANA na platformie Azure (duże wystąpienia)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/16/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4384d29811d29f06422802abba5d3eb1ea5737e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72430079"
---
# <a name="backup-and-restore"></a>Tworzenie kopii zapasowej i przywracanie

>[!IMPORTANT]
>Ten artykuł nie zastępuje dokumentacji administracyjnej SAP HANA ani notatek SAP. Oczekujemy, że masz solidne zrozumienie i wiedzę w zakresie administracji i operacji SAP HANA, szczególnie w zakresie tworzenia kopii zapasowych, przywracania, wysokiej dostępności i odzyskiwania po awarii. W tym artykule wyświetlane są zrzuty ekranu z SAP HANA Studio. Zawartość, struktura i charakter ekranów narzędzi administracyjnych SAP i samych narzędzi może ulec zmianie z wersji SAP HANA do wydania.

Ważne jest, aby wykonywać kroki i procesy podejmowane w środowisku oraz z wersjami i wersjami HANA. Niektóre procesy opisane w tym artykule są uproszczone w celu lepszego ogólnego zrozumienia. Nie są one przeznaczone do użycia jako szczegółowe kroki dla podręczników ewentualnych operacji. Jeśli chcesz utworzyć podręczniki operacji dla konfiguracji, przetestuj i ćwicz swoje procesy oraz dokumentuj procesy związane z określonymi konfiguracjami. 

Jednym z najważniejszych aspektów operacyjnych baz danych jest ochrona ich przed katastrofami. Przyczyną tych zdarzeń może być wszystko, od klęsk żywiołowych do prostych błędów użytkownika.

Tworzenie kopii zapasowej bazy danych, z możliwością przywrócenia go do dowolnego punktu w czasie, na przykład przed ktoś usunięte dane krytyczne, umożliwia przywrócenie do stanu, który jest jak najbliżej sposobu, w jaki było przed zakłóceniem.

Aby uzyskać możliwość przywracania, należy wykonać dwa typy kopii zapasowych:

- Kopie zapasowe bazy danych: pełne, przyrostowe lub różnicowe kopie zapasowe
- Kopie zapasowe dziennika transakcji

Oprócz kopii zapasowych pełnej bazy danych wykonywanych na poziomie aplikacji można wykonywać kopie zapasowe z migawkami magazynu. Migawki magazynu nie zastępują kopii zapasowych dziennika transakcji. Kopie zapasowe dziennika transakcji pozostają ważne, aby przywrócić bazę danych do pewnego punktu w czasie lub opróżnić dzienniki z już zatwierdzonych transakcji. Migawki magazynu można przyspieszyć odzyskiwanie, szybko zapewniając roll-forward obrazu bazy danych. 

SAP HANA na platformie Azure (duże wystąpienia) oferuje dwie opcje tworzenia kopii zapasowych i przywracania:

- **Zrób to sam (DIY).** Po upewnieniu się, że jest wystarczająco dużo miejsca na dysku, wykonaj pełną bazę danych i dziennika kopii zapasowych przy użyciu jednej z następujących metod tworzenia kopii zapasowych dysku. Można wywrzeć zapasy zapasowe bezpośrednio do woluminów dołączonych do jednostek dużych wystąpień HANA lub do udziałów NFS, które są skonfigurowane na maszynie wirtualnej platformy Azure (VM). W tym ostatnim przypadku klienci skonfigurować maszynę wirtualną systemu Linux na platformie Azure, dołączyć usługę Azure Storage do maszyny Wirtualnej i udostępnić magazyn za pośrednictwem skonfigurowanego serwera NFS w tej maszynie wirtualnej. Jeśli wykonasz kopię zapasową względem woluminów, które bezpośrednio dołączyć do jednostek dużych wystąpień HANA, skopiuj kopie zapasowe do konta magazynu platformy Azure. Należy to zrobić po skonfigurowaniu maszyny Wirtualnej platformy Azure, która eksportuje udziały NFS, które są oparte na usłudze Azure Storage. Można również użyć magazynu usługi Azure Backup lub magazynu w chłodniach platformy Azure. 

   Inną opcją jest użycie narzędzia do ochrony danych innej firmy do przechowywania kopii zapasowych po ich skopiowaniu na konto magazynu platformy Azure. Opcja tworzenia kopii zapasowej diy może być również konieczne dla danych, które należy przechowywać przez dłuższy czas do celów zgodności i inspekcji. We wszystkich przypadkach kopie zapasowe są kopiowane do udziałów NFS reprezentowanych za pośrednictwem maszyny Wirtualnej i usługi Azure Storage.

- **Funkcje tworzenia kopii zapasowych i przywracania infrastruktury.** Można również użyć funkcji tworzenia kopii zapasowych i przywracania, które zapewnia podstawowa infrastruktura SAP HANA na platformie Azure (duże wystąpienia). Ta opcja spełnia potrzebę tworzenia kopii zapasowych i szybkich przywracania. Pozostała część tej sekcji dotyczy funkcji tworzenia kopii zapasowych i przywracania, które są oferowane z dużymi wystąpieniami HANA. W tej sekcji opisano również relacje, które mają kopii zapasowej i przywracania do funkcji odzyskiwania po awarii oferowanych przez hana dużych wystąpień.

> [!NOTE]
>   Technologia migawki, która jest używana przez podstawową infrastrukturę dużych wystąpień HANA ma zależność od migawek SAP HANA. W tym momencie migawki SAP HANA nie działają w połączeniu z wieloma dzierżawami kontenerów wielodostępnych bazy danych SAP HANA. Jeśli wdrożono tylko jedną dzierżawę, migawki SAP HANA działają i można użyć tej metody.

## <a name="use-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Użyj migawek magazynu SAP HANA na platformie Azure (duże wystąpienia)

Infrastruktura magazynu leżąca u podstaw platformy SAP HANA na platformie Azure (duże wystąpienia) obsługuje migawki magazynu woluminów. Obsługiwane są zarówno tworzenie kopii zapasowych, jak i przywracanie woluminów, z następującymi zagadnieniami:

- Zamiast pełnych kopii zapasowych bazy danych migawki woluminów magazynu są często pobierane.
- Gdy migawka jest wyzwalana przez /hana/data i /hana/shared, która zawiera /usr/sap, woluminy, technologia migawki inicjuje migawkę SAP HANA przed uruchomieniem migawki magazynu. Ta migawka SAP HANA jest punktem instalacyjnym dla ostatecznych uzupełnień dziennika po odzyskaniu migawki magazynu. Aby migawka HANA zakończyła się pomyślnie, potrzebujesz aktywnego wystąpienia HANA. W scenariuszu HSR migawka magazynu nie jest obsługiwana w bieżącym węźle pomocniczym, w którym nie można wykonać migawki HANA.
- Po pomyślnym uruchomieniu migawki magazynu migawka SAP HANA jest usuwana.
- Kopie zapasowe dziennika transakcji są często podejmowane i przechowywane w woluminie /hana/logbackups lub na platformie Azure. Można wyzwolić wolumin /hana/logbackups, który zawiera kopie zapasowe dziennika transakcji, aby wykonać migawkę oddzielnie. W takim przypadku nie trzeba uruchamiać migawki HANA.
- Jeśli musisz przywrócić bazę danych do pewnego punktu w czasie, w przypadku awarii produkcji, zażądaj, aby pomoc techniczna platformy Microsoft Azure lub SAP HANA na platformie Azure zostały przywrócone do określonej migawki magazynu. Przykładem jest planowane przywrócenie systemu piaskownicy do stanu pierwotnego.
- Migawka SAP HANA, która jest uwzględniona w migawce magazynu, jest punktem przesunięcia do stosowania kopii zapasowych dziennika transakcji, które zostały uruchomiony i były przechowywane po wykonaniu migawki magazynu.
- Te kopie zapasowe dziennika transakcji są podejmowane w celu przywrócenia bazy danych z powrotem do pewnego punktu w czasie.

Można wykonywać migawki magazynu, które są przeznaczone dla trzech klas woluminów:

- Połączona migawka za /hana/data i /hana/shared, która zawiera /usr/sap. Ta migawka wymaga utworzenia migawki SAP HANA jako przygotowania do migawki magazynu. Migawka SAP HANA zapewnia, że baza danych jest w stanie spójnym z punktu widzenia magazynu. W przypadku procesu przywracania jest to punkt do skonfigurowania.
- Oddzielna migawka za pomocą /hana/logbackups.
- Partycja systemu operacyjnego.

Aby uzyskać najnowsze skrypty migawki i dokumentację, zobacz [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). Po pobraniu pakietu skryptu migawki z [GitHub,](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md)otrzymasz trzy pliki. Jeden z plików jest udokumentowany w pliku PDF dla podanych funkcji. Po pobraniu zestawu narzędzi postępuj zgodnie z instrukcjami w "Pobierz narzędzia migawki".

## <a name="storage-snapshot-considerations"></a>Zagadnienia dotyczące migawki magazynu

>[!NOTE]
>Migawki magazynu zużywają miejsce do magazynowania, które jest przydzielone do jednostek dużych wystąpień HANA. Należy wziąć pod uwagę następujące aspekty planowania migawek magazynu i ile migawek magazynu do zachowania. 

Specyficzne mechaniki migawek magazynu dla SAP HANA na platformie Azure (duże wystąpienia) obejmują:

- Migawka określonego magazynu w momencie, gdy jest pobierana zużywa mało miejsca.
- W miarę jak zawartość danych zmienia się, a zawartość plików danych SAP HANA zmienia się na woluminie magazynu, migawka musi przechowywać oryginalną zawartość bloku i zmiany danych.
- W rezultacie migawka magazynu zwiększa rozmiar. Im dłużej istnieje migawka, tym większa staje się migawka magazynu.
- Im więcej zmian, które są wprowadzane do woluminu bazy danych SAP HANA w okresie istnienia migawki magazynu, tym większe zużycie miejsca migawki magazynu.

SAP HANA na platformie Azure (Duże wystąpienia) zawiera stałe rozmiary woluminów dla woluminów danych i dzienników SAP HANA. Wykonywanie migawek tych woluminów zjada w przestrzeni woluminu. Musisz:

- Określ, kiedy zaplanować migawki magazynu.
- Monitorowanie zużycia miejsca w woluminach pamięci masowej. 
- Zarządzanie liczbą migawek, które przechowujesz. 

Migawki magazynu można wyłączyć podczas importowania mas danych lub wykonywania innych istotnych zmian w bazie danych HANA. 


Poniższe sekcje zawierają informacje dotyczące wykonywania tych migawek i zawierają ogólne zalecenia:

- Mimo że sprzęt może wytrzymać 255 migawek na wolumin, chcesz pozostać znacznie poniżej tej liczby. Zalecenie to 250 lub mniej.
- Przed wykonaniem migawek magazynu należy monitorować i śledzić wolne miejsce.
- Zmniejsz liczbę migawek magazynu na podstawie wolnego miejsca. Można zmniejszyć liczbę zachowanych migawek lub rozszerzyć woluminy. Dodatkową pamięć można zamówić w jednostkach 1-terabajtowych.
- Podczas działań, takich jak przenoszenie danych do SAP HANA za pomocą narzędzi do migracji platformy SAP (R3load) lub przywracanie baz danych SAP HANA z kopii zapasowych, wyłącz migawki magazynu na woluminie /hana/data. 
- Podczas większych reorganizacji tabel SAP HANA, należy unikać migawek magazynu, jeśli to możliwe.
- Migawki magazynu są warunkiem wstępnym do korzystania z możliwości odzyskiwania po awarii sap HANA na platformie Azure (duże wystąpienia).

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>Wymagania wstępne dotyczące korzystania z samoobsługowych migawek magazynu

Aby upewnić się, że skrypt migawki działa pomyślnie, upewnij się, że Perl jest zainstalowany w systemie operacyjnym Linux na serwerze dużych wystąpień HANA. Perl jest preinstalowany w jednostce hana dużych instancji. Aby sprawdzić wersję perla, użyj następującego polecenia:

`perl -v`

![Klucz publiczny jest kopiowany przez uruchomienie tego polecenia](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


## <a name="set-up-storage-snapshots"></a>Konfigurowanie migawek magazynu

Aby skonfigurować migawki magazynu za pomocą dużych wystąpień HANA, wykonaj następujące kroki.
1. Upewnij się, że Perl jest zainstalowany w systemie operacyjnym Linux na serwerze dużych wystąpień HANA.
1. Zmodyfikuj konfigur\_/etc/ssh/ssh, aby dodać linię _MACs hmac-sha1_.
1. Utwórz konto użytkownika kopii zapasowej SAP HANA w węźle głównym dla każdego uruchomionego wystąpienia SAP HANA, jeśli ma to zastosowanie.
1. Zainstaluj klienta SAP HANA HDB na wszystkich serwerach SAP HANA Dużych Wystąpień.
1. Na pierwszym serwerze sap HANA dużych wystąpień każdego regionu utwórz klucz publiczny, aby uzyskać dostęp do podstawowej infrastruktury magazynu, która kontroluje tworzenie migawek.
1. Skopiuj skrypty i plik konfiguracyjny z [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md) do lokalizacji **hdbsql** w instalacji SAP HANA.
1. Zmodyfikuj plik *HANABackupDetails.txt* w razie potrzeby dla odpowiednich specyfikacji klienta.

Pobierz najnowsze skrypty migawki i dokumentację z [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md). Aby zapoznać się z wymienionymi wcześniej krokami, zobacz [Narzędzia migawek firmy Microsoft dla systemu SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

### <a name="consideration-for-mcod-scenarios"></a>Uwzględnienie scenariuszy MCOD
Jeśli zostanie uruchomiony [scenariusz MCOD](https://launchpad.support.sap.com/#/notes/1681092) z wieloma wystąpieniami SAP HANA na jednej jednostce dużych wystąpień HANA, masz oddzielne woluminy magazynu aprowizowana dla każdego wystąpienia SAP HANA. Aby uzyskać więcej informacji na temat mdc i innych zagadnień, zobacz "Ważne rzeczy do zapamiętania" w [narzędziach migawek firmy Microsoft dla sap HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>Krok 1: Zainstaluj klienta SAP HANA HDB

System operacyjny Linux zainstalowany na platformie SAP HANA na platformie Azure (Duże wystąpienia) zawiera foldery i skrypty niezbędne do uruchamiania migawek magazynu SAP HANA do celów tworzenia kopii zapasowych i odzyskiwania po awarii. Sprawdź, czy w [usłudze GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md)nie ma nowszych wersji. 

Twoim obowiązkiem jest zainstalowanie klienta SAP HANA HDB na jednostkach dużych wystąpień HANA podczas instalowania sap hana.

### <a name="step-2-change-the-etcsshssh_config"></a>Krok 2: Zmień konfigur /etc/ssh/ssh\_

Ten krok jest opisany w "Włącz komunikację z magazynem" w [narzędziach migawek firmy Microsoft dla sap HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).


### <a name="step-3-create-a-public-key"></a>Krok 3: Tworzenie klucza publicznego

Aby włączyć dostęp do interfejsów migawki magazynu dzierżawy dużych wystąpień HANA, należy ustanowić procedurę logowania za pomocą klucza publicznego. 

Na pierwszym serwerze SAP HANA na platformie Azure (duże wystąpienia) w dzierżawie utwórz klucz publiczny, aby uzyskać dostęp do infrastruktury magazynu. Przy kluczu publicznym hasło nie jest wymagane do logowania się do interfejsów migawki magazynu. Nie trzeba również obsługiwać poświadczeń hasła za pomocą klucza publicznego. 

Aby wygenerować klucz publiczny, zobacz "Włączanie komunikacji z magazynem" w [narzędziach migawek firmy Microsoft dla systemu SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).


### <a name="step-4-create-an-sap-hana-user-account"></a>Krok 4: Tworzenie konta użytkownika SAP HANA

Aby rozpocząć tworzenie migawek SAP HANA, utwórz konto użytkownika w sap HANA, którego mogą używać skrypty migawki magazynu. W tym celu utwórz konto użytkownika SAP HANA w programie SAP HANA Studio. Użytkownik musi być utworzony w bazie danych SYSTEMDB, a *nie* w bazie danych SID dla MDC. W środowisku pojedynczego kontenera użytkownik jest tworzony w bazie danych dzierżawy. To konto musi mieć uprawnienia **Administrator kopii zapasowej** i Odczyt **katalogu.** 

Aby skonfigurować i używać konta użytkownika, zobacz "Włączanie komunikacji z SAP HANA" w [usłudze GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).


### <a name="step-5-authorize-the-sap-hana-user-account"></a>Krok 5: Autoryzowanie konta użytkownika SAP HANA

W tym kroku autoryzujesz utworzone konto użytkownika SAP HANA, aby skrypty nie musiały przesyłać haseł w czasie wykonywania. Polecenie `hdbuserstore` SAP HANA umożliwia utworzenie klucza użytkownika SAP HANA. Klucz jest przechowywany w co najmniej jednym węzłach SAP HANA. Klucz użytkownika umożliwia użytkownikowi dostęp do sap HANA bez konieczności zarządzania hasłami z poziomu procesu skryptów. Proces skryptów jest omówiony w dalszej części tego artykułu.

>[!IMPORTANT]
>Uruchom te polecenia konfiguracji w tym samym kontekście użytkownika, w których są uruchamiane polecenia migawki. W przeciwnym razie polecenia migawki nie będą działać poprawnie.


### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>Krok 6: Pobierz skrypty migawki, skonfiguruj migawki i przetestuj konfigurację i łączność

Pobierz najnowszą wersję skryptów z [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.1). Sposób instalowania skryptów zmienił się wraz z wersją 4.1 skryptów. Aby uzyskać więcej informacji, zobacz "Włączanie komunikacji z SAP HANA" w [narzędziach migawek firmy Microsoft dla systemu SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Aby uzyskać dokładną sekwencję poleceń, zobacz "Łatwa instalacja narzędzi migawek (domyślnie)" w [narzędziach migawek firmy Microsoft dla sap HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). Zaleca się użycie instalacji domyślnej. 

Aby uaktualnić wersję z wersji 3.x do 4.1, zobacz "Uaktualnianie istniejącej [instalacji" w narzędziach migawek firmy Microsoft dla systemu SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). Aby odinstalować zestaw narzędzi 4.1, zobacz "Dezinstalacja narzędzi migawek" w [narzędziach migawek firmy Microsoft dla systemu SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Nie zapomnij wykonać czynności opisanych w części "Pełna konfiguracja narzędzi migawek" w [narzędziach migawek firmy Microsoft dla systemu SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Cel różnych skryptów i plików po ich zainstalowaniu jest opisany w "Jakie są te narzędzia migawki?" w [narzędziach migawek firmy Microsoft dla systemu SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Przed skonfigurowaniem narzędzi migawek upewnij się, że poprawnie skonfigurowano również lokalizacje i ustawienia kopii zapasowej HANA. Aby uzyskać więcej informacji, zobacz "Konfiguracja SAP HANA" w [narzędziach migawek firmy Microsoft dla sap HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Konfiguracja zestawu narzędzi migawki jest opisana w "Config file - HANABackupCustomerDetails.txt" w [narzędziach migawki firmy Microsoft dla sap HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

#### <a name="test-connectivity-with-sap-hana"></a>Przetestuj łączność z SAP HANA

Po umieszczeniu wszystkich danych konfiguracyjnych w pliku *HANABackupCustomerDetails.txt* sprawdź, czy konfiguracje są poprawne dla danych wystąpienia HANA. Użyj skryptu `testHANAConnection`, który jest niezależny od konfiguracji skalowania w górę lub skalowanej w poziomie SAP HANA.

Aby uzyskać więcej informacji, zobacz "Sprawdź łączność z SAP HANA - testHANAConnection" w [narzędziach migawek firmy Microsoft dla sap HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

#### <a name="test-storage-connectivity"></a>Testowanie łączności pamięci masowej

Następnym krokiem testu jest sprawdzenie łączności z magazynem na podstawie danych umieszczonych w pliku konfiguracyjnym *HANABackupCustomerDetails.txt.* Następnie uruchom migawkę testu. Przed uruchomieniem `azure_hana_backup` polecenia należy uruchomić ten test. Sekwencja poleceń dla tego testu, zobacz "Sprawdź łączność z magazynem - testStorageSnapshotConnection"" w [narzędziach migawek firmy Microsoft dla sap HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Po pomyślnym zalogowaniu się do interfejsów maszyny wirtualnej magazynu skrypt kontynuuje fazę 2 i tworzy migawkę testową. Dane wyjściowe są wyświetlane w tym miejscu dla konfiguracji skalowania w poziomie trzech węzłów SAP HANA.

Jeśli migawka testu zostanie pomyślnie uruchomiona ze skryptem, można zaplanować rzeczywiste migawki magazynu. Jeśli to się nie powiedzie, zbadaj problemy przed przejściem do przodu. Migawka testu powinna pozostać wokół, dopóki nie zostaną wykonane pierwsze prawdziwe migawki.


### <a name="step-7-perform-snapshots"></a>Krok 7: Wykonywanie migawek

Po zakończeniu etapów przygotowania można rozpocząć konfigurowanie i planowanie rzeczywistych migawek magazynu. Skrypt, który ma być zaplanowany, współpracuje z konfiguracjami skalowania w górę i skalowano w poziomie SAP HANA. W przypadku okresowego i regularnego wykonywania skryptu kopii zapasowej należy zaplanować skrypt przy użyciu narzędzia cron. 

Aby uzyskać dokładną składnię i funkcjonalność polecenia, zobacz "Wykonywanie kopii zapasowej migawki — azure_hana_backup" w [narzędziach migawek firmy Microsoft dla systemu SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 

Po uruchomieniu `azure_hana_backup` skryptu tworzy migawkę magazynu w następujących trzech fazach:

1. Uruchamia migawkę SAP HANA.
1. Uruchamia migawkę magazynu.
1. Usuwa migawkę SAP HANA, która została utworzona przed uruchomiono migawkę magazynu.

Aby uruchomić skrypt, należy go wywołać z folderu wykonywalnego HDB, do którego został skopiowany. 

Okres przechowywania jest administrowany z liczbą migawek, które są przesyłane jako parametr po uruchomieniu skryptu. Czas, który jest objęty migawek magazynu jest funkcją okresu wykonywania i liczby migawek przesłanych jako parametr po uruchomieniu skryptu. 

Jeśli liczba zachowanych migawek przekracza liczbę, która jest nazywana jako parametr w wywołaniu skryptu, najstarsza migawka magazynu tej samej etykiety jest usuwana przed uruchomieniu nowej migawki. Numer, który podajesz jako ostatni parametr połączenia, jest numerem, którego można użyć do kontrolowania liczby migawek, które są przechowywane. Za pomocą tej liczby można również kontrolować pośrednio miejsce na dysku, które jest używane dla migawek. 


## <a name="snapshot-strategies"></a>Strategie migawek
Częstotliwość migawek dla różnych typów zależy od tego, czy używasz funkcji odzyskiwania po awarii dużych wystąpień HANA. Ta funkcja opiera się na migawek magazynu, które mogą wymagać specjalnych zaleceń dla częstotliwości i okresów wykonywania migawek magazynu. 

W rozważaniach i zaleceniach, które należy wykonać, założenie jest, że *nie* używasz funkcji odzyskiwania po awarii, które hana dużych wystąpień oferuje. Zamiast tego należy użyć migawek magazynu, aby mieć kopie zapasowe i być w stanie zapewnić odzyskiwanie punktu w czasie w ciągu ostatnich 30 dni. Biorąc pod uwagę ograniczenia liczby migawek i miejsca, należy wziąć pod uwagę następujące wymagania:

- Czas odzyskiwania dla odzyskiwania punktu w czasie.
- Wykorzystana przestrzeń.
- Punkt odzyskiwania i czas odzyskiwania cele dla potencjalnego odzyskania po awarii.
- Ostateczne wykonanie kopii zapasowych pełnej bazy danych HANA na dyskach. Za każdym razem, gdy wykonywana jest kopia zapasowa pełnej bazy danych na dyskach lub **interfejsie backint,** wykonanie migawek magazynu kończy się niepowodzeniem. Jeśli planujesz uruchomić kopie zapasowe pełnej bazy danych na podstawie migawek magazynu, upewnij się, że wykonywanie migawek magazynu jest wyłączona w tym czasie.
- Liczba migawek na wolumin, który jest ograniczony do 250.

<!-- backint is term for a SAP HANA interface and not a spelling error not spelling errors -->

Jeśli nie używasz funkcji odzyskiwania po awarii hana dużych wystąpień, okres migawki jest rzadszy. W takich przypadkach należy wykonać połączone migawki na /hana/data i /hana/shared, która obejmuje /usr/sap, w okresach 12-godzinnym lub 24-godzinnym. Zachowaj migawki przez miesiąc. To samo dotyczy migawek woluminu kopii zapasowej dziennika. Wykonywanie kopii zapasowych dziennika transakcji SAP HANA względem woluminu kopii zapasowej dziennika odbywa się w okresach od 5 minut do 15 minut.

Zaplanowane migawki magazynu są najlepiej wykonywane przy użyciu cron. Użyj tego samego skryptu dla wszystkich kopii zapasowych i potrzeb odzyskiwania po awarii. Zmodyfikuj dane wejściowe skryptu, aby dopasować różne żądane czasy tworzenia kopii zapasowej. Te migawki są zaplanowane inaczej w cron w zależności od czasu ich wykonania. Może to być co godzinę, co 12 godzin, codziennie lub co tydzień. 

W poniższym przykładzie pokazano harmonogram cron w /etc/crontab:
```
00 1-23 * * * ./azure_hana_backup --type=hana --prefix=hourlyhana --frequency=15min --retention=46
10 00 * * *  ./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
22 12 * * *  ./azure_hana_backup --type=logs --prefix=dailylogback --frequncy=3min --retention=28
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```
W poprzednim przykładzie cogodzinna łączna migawka obejmuje woluminy zawierające /hana/data i /hana/shared/SID, które obejmują /usr/sap, lokalizacje. Użyj tego typu migawki dla szybszego odzyskiwania w czasie w ciągu ostatnich dwóch dni. Istnieje również codzienna migawka na tych woluminach. Tak, masz dwa dni pokrycia przez co godzinę migawki plus cztery tygodnie pokrycia przez codzienne migawki. Wolumin kopii zapasowej dziennika transakcji również jest archiwizowy codziennie. Te kopie zapasowe są przechowywane przez cztery tygodnie. 

Jak widać w trzecim wierszu crontab, kopia zapasowa dziennika transakcji HANA jest zaplanowane do uruchomienia co 5 minut. Godziny rozpoczęcia różnych zadań cron, które uruchamiają migawki magazynu są rozłożone. W ten sposób migawki nie działają wszystkie na raz w określonym momencie w czasie. 

W poniższym przykładzie należy wykonać połączone migawki, która obejmuje woluminy, które zawierają /hana/data i /hana/shared/SID, który zawiera /usr/sap, lokalizacje na podstawie godzinowej. Te migawki są przechowywane przez dwa dni. Migawki woluminów kopii zapasowej dziennika transakcji są uruchamiane na zasadzie 5 minut i są przechowywane przez cztery godziny. Podobnie jak poprzednio, kopia zapasowa pliku dziennika transakcji HANA jest zaplanowana do uruchomienia co 5 minut. 

Migawka woluminu kopii zapasowej dziennika transakcji jest wykonywana z 2-minutowym opóźnieniem po uruchomieniu kopii zapasowej dziennika transakcji. W normalnych warunkach kopia zapasowa dziennika transakcji SAP HANA kończy się w ciągu tych 2 minut. Tak jak poprzednio, wolumin zawierający jednostkę LUN rozruchu jest archiwiza się raz dziennie przez migawkę magazynu i jest przechowywany przez cztery tygodnie.

```
10 0-23 * * * ./azure_hana_backup --type=hana ==prefix=hourlyhana --frequency=15min --retention=48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup --type=logs --prefix=logback --frequency=3min --retention=48
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

Poniższa grafika ilustruje sekwencje poprzedniego przykładu. Jednostka LUN rozruchu jest wykluczona.

![Relacja między kopiami zapasowymi a migawkami](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA wykonuje regularne zapisy względem woluminu /hana/log, aby udokumentować zatwierdzone zmiany w bazie danych. Regularnie SAP HANA zapisuje punkt zapisu do woluminu /hana/data. Jak określono w crontab, kopia zapasowa dziennika transakcji SAP HANA jest uruchamiana co 5 minut. 

Widać również, że migawka SAP HANA jest uruchamiana co godzinę w wyniku wyzwalania połączonej migawki magazynu za połączoną zaoszerwnienie/danych/danych i woluminów /hana/shared/SID. Po pomyślnym zakończeniu migawki HANA uruchomi się migawka magazynu. Zgodnie z instrukcją w crontab migawka magazynu na woluminie /hana/logbackup jest uruchamiana co 5 minut, około 2 minut po kopii zapasowej dziennika transakcji HANA.

> 

>[!IMPORTANT]
> Użycie migawek magazynu dla kopii zapasowych SAP HANA jest cenne tylko wtedy, gdy migawki są wykonywane w połączeniu z kopiami zapasowymi dziennika transakcji SAP HANA. Te kopie zapasowe dziennika transakcji muszą obejmować okresy między migawkami magazynu. 

Jeśli ustawisz zobowiązanie dla użytkowników odzyskiwania punktu w czasie 30 dni, musisz:

- W skrajnych przypadkach dostęp do połączonej migawki magazynu za film /hana/data i /hana/shared/SID ma 30 dni. 
- Mają ciągłe kopie zapasowe dziennika transakcji, które obejmują czas między dowolnymi migawkami magazynu połączone. Tak więc najstarsza migawka woluminu kopii zapasowej dziennika transakcji musi mieć 30 dni. Nie jest to przypadek, jeśli kopiujesz kopie zapasowe dziennika transakcji do innego udziału NFS, który znajduje się w usłudze Azure Storage. W takim przypadku można wyciągnąć stare kopie zapasowe dziennika transakcji z tego udziału NFS.

Aby korzystać z migawek magazynu i ewentualnej replikacji magazynu kopii zapasowych dziennika transakcji, zmień lokalizację, do której SAP HANA zapisuje kopie zapasowe dziennika transakcji. Możesz wprowadzić tę zmianę w HANA Studio. 

Mimo że SAP HANA automatycznie kopiuje kopie zapasowe pełnych segmentów dziennika, należy określić interwał tworzenia kopii zapasowych dziennika jako deterministyczny. Jest to szczególnie ważne, gdy używasz opcji odzyskiwania po awarii, ponieważ zwykle chcesz uruchomić kopie zapasowe dziennika z okresem deterministycznym. W poniższym przypadku 15 minut jest ustawiona jako interwał tworzenia kopii zapasowej dziennika.

![Planowanie dzienników kopii zapasowych SAP HANA w SAP HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

Można również wybrać kopie zapasowe, które są częstsze niż co 15 minut. Częstsze ustawienie jest często używane w połączeniu z funkcją odzyskiwania po awarii wystąpień dużych hana. Niektórzy klienci wykonują kopie zapasowe dziennika transakcji co 5 minut.

Jeśli baza danych nigdy nie została utworzona, ostatnim krokiem jest wykonanie kopii zapasowej bazy danych opartej na plikach w celu utworzenia pojedynczego wpisu kopii zapasowej, który musi istnieć w katalogu kopii zapasowej. W przeciwnym razie sap HANA nie może zainicjować określonych kopii zapasowych dziennika.

![Tworzenie kopii zapasowej opartej na plikach w celu utworzenia pojedynczego wpisu kopii zapasowej](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


Po uruchomieniu pierwszych pomyślnych migawek magazynu usuń migawkę testu, która została uruchomiony w kroku 6. Aby uzyskać więcej informacji, zobacz "Usuwanie migawek testowych — removeTestStorageSnapshot" w [narzędziach migawek firmy Microsoft dla systemu SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 


### <a name="monitor-the-number-and-size-of-snapshots-on-the-disk-volume"></a>Monitorowanie liczby i rozmiaru migawek na woluminie dysku

Na określonym woluminie magazynu można monitorować liczbę migawek i zużycie magazynu tych migawek. Polecenie `ls` nie pokazuje katalogu migawki ani plików. Polecenie `du` systemu operacyjnego Linux pokazuje szczegóły dotyczące tych migawek magazynu, ponieważ są one przechowywane na tych samych woluminach. Użyj polecenia z następującymi opcjami:

- `du –sh .snapshot`: Ta opcja zawiera łącznie wszystkie migawki w katalogu migawki.
- `du –sh --max-depth=1`: Ta opcja zawiera listę wszystkich migawek zapisanych w folderze **.snapshot** oraz rozmiar każdej migawki.
- `du –hc`: Ta opcja zapewnia całkowity rozmiar używany przez wszystkie migawki.

Użyj tych poleceń, aby upewnić się, że migawki, które są pobierane i przechowywane nie zużywają całego magazynu na woluminach.

>[!NOTE]
>Migawki jednostki LUN rozruchu nie są widoczne w poprzednich poleceniach.

### <a name="get-details-of-snapshots"></a>Uzyskaj szczegółowe informacje o migawkach
Aby uzyskać więcej informacji na temat `azure_hana_snapshot_details`migawek, użyj skryptu . Ten skrypt można uruchomić w obu lokalizacjach, jeśli w lokalizacji odzyskiwania po awarii znajduje się aktywny serwer. Skrypt zawiera następujące dane wyjściowe, w podziale na każdy wolumin zawierający migawki: 
   * Rozmiar całkowitych migawek w woluminie
   * Następujące szczegóły w każdej migawce w tym woluminie: 
      - Nazwa migawki 
      - Tworzenie czasu 
      - Rozmiar migawki
      - Częstotliwość migawki
      - Identyfikator kopii zapasowej HANA skojarzony z tą migawką, w stosownych przypadkach

Aby uzyskać składnię polecenia i wyjść, zobacz "Lista migawek - azure_hana_snapshot_details" w [narzędziach migawek firmy Microsoft dla sap HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 



### <a name="reduce-the-number-of-snapshots-on-a-server"></a>Zmniejszanie liczby migawek na serwerze

Jak wcześniej wyjaśniono, można zmniejszyć liczbę niektórych etykiet migawek, które przechowujesz. Ostatnie dwa parametry polecenia do zainicjowania migawki są etykiety i liczbę migawek, które mają zostać zachowane.

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
```

W poprzednim przykładzie etykieta migawki to **dailyhana**. Liczba migawek z tą etykietą do zachowania wynosi **28**. W odpowiedzi na zużycie miejsca na dysku, można zmniejszyć liczbę przechowywanych migawek. Łatwym sposobem na zmniejszenie liczby migawek do 15, na przykład, jest uruchomienie skryptu z ostatnim parametrem ustawionym na **15:**

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=15
```

Jeśli skrypt zostanie uruchomiony z tym ustawieniem, liczba migawek, która zawiera nową migawkę magazynu, wynosi 15. 15 najnowszych migawek są przechowywane, a 15 starszych migawek są usuwane.

 >[!NOTE]
 > Ten skrypt zmniejsza liczbę migawek tylko wtedy, gdy istnieją migawki więcej niż jedną godzinę. Skrypt nie usuwa migawek, które mają mniej niż jedną godzinę. Ograniczenia te są związane z opcjonalną funkcją odzyskiwania po awarii.

Jeśli nie chcesz już utrzymywać zestaw migawek z prefiksem kopii zapasowej **dailyhana** w przykładach składni, uruchom skrypt z **0** jako numer przechowywania. Wszystkie migawki, które pasują do tej etykiety są następnie usuwane. Usunięcie wszystkich migawek może mieć wpływ na możliwości funkcji odzyskiwania po awarii dużych wystąpień HANA.

Drugą opcją usuwania określonych migawek `azure_hana_snapshot_delete`jest użycie skryptu . Ten skrypt jest przeznaczony do usuwania migawki lub zestaw migawek przy użyciu identyfikatora kopii zapasowej HANA, jak można znaleźć w HANA Studio lub za pośrednictwem samej nazwy migawki. Obecnie identyfikator kopii zapasowej jest powiązany tylko z migawkami utworzonymi dla typu migawki **hana.** Migawka kopii zapasowych **dzienników** typów i **rozruchu** nie wykonują migawki SAP HANA, więc nie można znaleźć identyfikatora kopii zapasowej dla tych migawek. Jeśli zostanie wprowadzona nazwa migawki, wyszukuje wszystkie migawki na różnych woluminach, które pasują do wprowadzonej nazwy migawki. 

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->

Aby uzyskać więcej informacji na temat skryptu, zobacz "Usuwanie migawki — azure_hana_snapshot_delete" w [narzędziach migawek firmy Microsoft dla systemu SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Uruchom skrypt jako **główny**użytkownik .

>[!IMPORTANT]
>Jeśli istnieją dane, które istnieją tylko na migawki, które zamierzasz usunąć, po usunięciu migawki, że dane zostaną utracone na zawsze.


## <a name="file-level-restore-from-a-storage-snapshot"></a>Przywracanie na poziomie pliku z migawki magazynu

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->
Dla typów migawek **hana** i **dzienniki**, można uzyskać dostęp do migawek bezpośrednio na woluminach w katalogu **.snapshot.** Istnieje podkatalog dla każdej migawki. Skopiuj każdy plik w stanie, w jaki znajdował się w punkcie migawki z tego podkatalogu do rzeczywistej struktury katalogów. 

W bieżącej wersji skryptu *nie ma skryptu* przywracania przewidzianego dla przywracania migawki jako samoobsługi. Przywracanie migawki można wykonać jako część samoobsługowych skryptów odzyskiwania po awarii w lokacji odzyskiwania po awarii podczas pracy awaryjnej. Aby przywrócić żądaną migawkę z istniejących dostępnych migawek, należy skontaktować się z zespołem operacyjnym firmy Microsoft, otwierając żądanie usługi.

>[!NOTE]
>Przywracanie pojedynczego pliku nie działa w przypadku migawek jednostki LUN rozruchu niezależnie od typu jednostek dużych wystąpień HANA. Katalog **.snapshot** nie jest widoczny w lun rozruchu. 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>Odzyskiwanie do najnowszej migawki HANA

W scenariuszu produkcji w dół proces odzyskiwania z migawki magazynu można uruchomić jako zdarzenia klienta z pomocy technicznej platformy Microsoft Azure. Jest to sprawa o wysokiej pilności, jeśli dane zostały usunięte w systemie produkcyjnym i jedynym sposobem, aby je pobrać jest przywrócenie produkcyjnej bazy danych.

W innej sytuacji odzyskiwanie punktu w czasie może być mało pilne i planowane z kilkudniowym wyprzedzeniem. Możesz zaplanować to odzyskiwanie za pomocą sap HANA na platformie Azure zamiast wywoływania flagi o wysokim priorytecie. Na przykład można zaplanować uaktualnienie oprogramowania SAP, stosując nowy pakiet ulepszeń. Następnie należy przywrócić migawkę, która reprezentuje stan przed uaktualnieniem pakietu ulepszeń.

Przed wysłaniem żądania należy się przygotować. Sap HANA w zespole platformy Azure można następnie obsłużyć żądanie i zapewnić przywrócone woluminy. Następnie można przywrócić bazę danych HANA na podstawie migawek.

Aby uzyskać możliwość przywrócenia migawki za pomocą nowego zestawu narzędzi, zobacz "Jak przywrócić migawkę" w [przewodniku Ręczne odzyskiwanie dla sap HANA na platformie Azure z migawki magazynu](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Aby przygotować się do żądania, wykonaj następujące kroki.

1. Zdecyduj, którą migawkę przywrócić. Przywracany jest tylko wolumin hana/data, chyba że zalecisz inaczej. 

1. Zamknij wystąpienie HANA.

   ![Zamykanie wystąpienia HANA](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. Odinstaluj woluminy danych w każdym węźle bazy danych HANA. Jeśli woluminy danych są nadal zainstalowane w systemie operacyjnym, przywrócenie migawki nie powiedzie się.

   ![Odinstalowywaj woluminy danych w każdym węźle bazy danych HANA](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. Otwórz żądanie pomocy technicznej platformy Azure i dołącz instrukcje dotyczące przywracania określonej migawki:

   - Podczas przywracania: SAP HANA w usłudze Azure może poprosić o udział w połączeniu konferencyjnym w celu koordynowania, weryfikowania i potwierdzania, że przywracana jest poprawna migawka magazynu. 

   - Po przywróceniu: SAP HANA w usłudze Azure powiadamia użytkownika po przywróceniu migawki magazynu.

1. Po zakończeniu procesu przywracania ponownie należy ponownie zmontować wszystkie woluminy danych.

   ![Ponowne ponowne zamontowanie wszystkich woluminów danych](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)



Inną możliwością uzyskania, na przykład, SAP HANA plików danych odzyskanych z migawki magazynu, jest udokumentowana w kroku 7 w [przewodniku Ręczne odzyskiwanie dla SAP HANA na platformie Azure z migawki magazynu](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Aby przywrócić z kopii zapasowej migawki, zobacz [Przewodnik ręcznego odzyskiwania dla sap HANA na platformie Azure z migawki magazynu](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 

>[!Note]
>Jeśli migawka została przywrócona przez operacje firmy Microsoft, nie trzeba wykonać kroku 7.


### <a name="recover-to-another-point-in-time"></a>Powrót do innego punktu w czasie
Aby przywrócić do pewnego momentu w czasie, zobacz "Odzyskiwanie bazy danych do następnego punktu w czasie" w [Przewodniku ręcznego odzyskiwania dla sap HANA na platformie Azure z migawki magazynu](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 


## <a name="next-steps"></a>Następne kroki
- Zobacz [Zasady odzyskiwania po awarii i przygotowanie](hana-concept-preparation.md).
