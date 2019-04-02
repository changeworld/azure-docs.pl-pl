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
ms.date: 04/01/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 69417551c1c8d410f75e74a8164c8b8a223ab835
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58805333"
---
# <a name="backup-and-restore"></a>Tworzenie kopii zapasowej i przywracanie

>[!IMPORTANT]
>Ta dokumentacja jest nie można zastąpić dokumentacji administracyjnej platformy SAP HANA lub SAP Notes. Oczekuje się, że czytelnik ma stałych zrozumienie i doświadczeń w administracyjnej platformy SAP HANA oraz operacji, szczególnie w przypadku tematów, tworzenie kopii zapasowych, przywracania, wysokiej dostępności i odzyskiwania po awarii. W tej dokumentacji przedstawiono zrzuty ekranu z SAP HANA Studio. Zawartości, struktury i charakter ekrany narzędzia do administrowania SAP i narzędzi, sami mogą ulec zmianie z platformy SAP HANA wersji.

Należy pamiętać, że wykonujesz kroki i procesy wykonywane w danym środowisku i z platformy HANA wersji i wydań. Niektóre procesy, opisane w tej dokumentacji są uproszczone, aby lepiej zrozumieć ogólny i nie są przeznaczone do służyć jako szczegółowy opis kroków podręczników ostatecznej operacji. Jeśli chcesz utworzyć podręczników operacja konfiguracji usługi, należy do testowania i wykonywania procesów i dokumentowanie procesów związanych z określonej konfiguracji. 

Jednym z najważniejszych aspektów, które operacyjnej bazy danych jest aby chronić je przed katastrofami. Przyczyny te zdarzenia może być dowolna przed klęskami żywiołowymi błędy proste użytkownika.

Tworzenie kopii zapasowej bazy danych, z możliwością przywrócenia go do dowolnego punktu w czasie (takie jak przed usunięciem ktoś krytycznych danych), umożliwia przywracanie do stanu, który jest możliwie najbliżej sposób ich zakłócenie.

Dwa typy kopii zapasowych należy wykonać w celu uzyskania najlepszych wyników:

- Kopie zapasowe bazy danych: Pełne, przyrostowych lub różnicowej kopii zapasowych
- Kopie zapasowe dziennika transakcji

Oprócz bazy danych na pełne kopie zapasowe wykonywane na poziomie aplikacji można wykonywać kopie zapasowe przy użyciu migawek magazynu. Migawek magazynu, nie zastępuj kopie zapasowe dziennika transakcji. Kopie zapasowe dziennika transakcji nadal ważne, aby przywrócić bazę danych do pewnego momentu w czasie lub pustą dzienniki transakcji już zatwierdzone. Jednakże migawek magazynu można co pozwala przyspieszyć odzyskiwanie szybko udostępniając obrazem przodu bazy danych. 

Oprogramowanie SAP HANA na platformie Azure (duże wystąpienia) oferuje dwie opcje tworzenia kopii zapasowych i przywracania:

- Zrób to sam (DIY). Po obliczeniu upewnij się, że istnieje wystarczająca ilość miejsca na dysku, należy wykonać pełnej bazy danych i kopie zapasowe dziennika przy użyciu jednej z następujących metod tworzenia kopii zapasowej dysku. Kopię zapasową można wykonywać bezpośrednio w woluminach dołączony do jednostek dużych wystąpień HANA lub do sieciowych udziałów plików (NFS), skonfigurowanej maszyny wirtualnej (VM) platformy Azure. W tym ostatnim przypadku klientom Konfigurowanie maszyny Wirtualnej z systemem Linux na platformie Azure, Połącz z maszyną Wirtualną usługi Azure Storage i współużytkują magazyn za pośrednictwem serwera systemu plików NFS skonfigurowanych na tej maszynie Wirtualnej. Jeśli wykonanie kopii zapasowej dla woluminów, które bezpośrednio dołączania do dużych wystąpień HANA jednostek, należy skopiować kopie zapasowe do konta usługi Azure storage (po skonfigurowaniu maszyny Wirtualnej platformy Azure, które eksportuje udziałów NFS, które są oparte na usłudze Azure Storage). Można również użyć magazynu usługi Azure backup lub Azure zimnego magazynu. 

   Innym rozwiązaniem jest na potrzeby przechowywania kopii zapasowych, po ich skopiowaniu konta usługi Azure storage to narzędzie ochrony danych innych firm. Możesz opcji tworzenia kopii zapasowej może być również niezbędnych dla danych, które będą przechowywane przez dłuższy czas do celów inspekcji i zgodności. We wszystkich przypadkach kopie zapasowe są kopiowane do udziałów NFS, reprezentowane przez maszynę Wirtualną i usługi Azure Storage.

- Infrastruktura kopii zapasowej i przywracania oferowane. Można również użyć kopii zapasowej i przywrócenia jej funkcjonalności, który zapewnia podstawową infrastrukturą platformy SAP HANA na platformie Azure (duże wystąpienia). Ta opcja spełnia potrzeby kopii zapasowych oraz szybkie ich przywrócenie. Pozostałej części tej sekcji eliminuje funkcje i przywracania kopii zapasowych, które jest oferowana z dużymi wystąpieniami platformy HANA. W tej sekcji omówiono również relacji kopii zapasowej i przywracania musi po awarii odzyskiwania funkcjonalność oferowana przez dużych wystąpień HANA.

> [!NOTE]
>   Technologia migawki, która jest używana przez podstawową infrastrukturą dużych wystąpień HANA zależny od oprogramowania SAP HANA migawki. W tym momencie migawek platformy SAP HANA nie działają w połączeniu z wieloma dzierżawami kontenery wielodostępne bazy danych SAP HANA. Jeśli tylko jednej dzierżawy zostanie wdrożona, migawek platformy SAP HANA będą działać, a ta metoda może być używana.

## <a name="using-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Przy użyciu migawek magazynu oprogramowania SAP Hana na platformie Azure (duże wystąpienia)

Infrastruktura magazynu, podstawowe oprogramowanie SAP HANA na platformie Azure (duże wystąpienia) obsługuje Magazyn migawek woluminów. Kopia zapasowa i Przywracanie woluminów jest obsługiwane przez następujące kwestie:

- Zamiast tworzenia pełnych kopii zapasowych migawek woluminu magazynu są pobierane częste.
- Podczas wyzwalania migawki /hana/data i /hana/shared (w tym /usr/sap) woluminów, technologia migawki inicjuje migawki przed rozpoczęciem wykonywania migawek magazynu oprogramowania SAP HANA. Ta migawka platformy SAP HANA jest punktem instalacji dla przywracana ostateczna dziennika po odzyskaniu migawki magazynu. Migawka HANA zakończy się powodzeniem wymaga aktywnego wystąpienia platformy HANA.  W scenariuszu HSR migawki magazynu nie jest obsługiwana w bieżącym węźle pomocniczego, których nie można wykonać migawki platformy HANA.
- Po migawki magazynu została wykonana pomyślnie, migawka platformy SAP HANA jest usuwana.
- Kopie zapasowe dziennika transakcji są często wykonywane i są przechowywane w woluminie /hana/logbackups lub na platformie Azure. Możesz wyzwolić woluminu /hana/logbackups, który zawiera kopie zapasowe dziennika transakcji, aby zrobić migawkę oddzielnie. W takiej sytuacji nie trzeba wykonać migawki platformy HANA.
- Jeśli musisz przywrócić bazę danych do pewnego momentu w czasie, zażądać tej obsługi platformy Microsoft Azure (w przypadku awarii produkcji) lub SAP HANA podczas przywracania systemu Azure do migawki magazynu. Przykładem jest planowane Przywracanie systemu piaskownicy do stanu pierwotnego.
- Migawka platformy SAP HANA, który znajduje się w tle magazynu jest punkt przesunięcia stosowania kopie zapasowe dziennika transakcji, które zostały wykonane i przechowywane po migawki magazynu.
- Te kopie zapasowe dziennika transakcji przejście do przywrócenia bazy danych do pewnego momentu w czasie.

Można wykonywać migawek magazynu, przeznaczone dla trzech klas woluminów:

- Połączone migawkę/hana/dane oraz /hana/shared (obejmuje/usr/sap). Ta migawka wymaga utworzenia migawki platformy SAP HANA jako przygotowania do migawki magazynu. Migawki platformy SAP HANA sprawdza, czy baza danych jest w stanie spójnym z punktu widzenia magazynu. I że do przywrócenia przetwarzania oznacza to punkt, aby ustawić działa na.
- Oddzielne migawkę za pośrednictwem/hana/logbackups.
- Partycja systemu operacyjnego.

Pobierz najnowsze skrypty migawki i dokumentacji [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts). Po pobraniu pakietu skryptu migawki z [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts), możesz także uzyskać w dokumentacji PDF skrypty jako część pakietu skryptu. Każdy pakiet skrypt ma własną dokumentację PDF.

## <a name="storage-snapshot-considerations"></a>Zagadnienia dotyczące migawek magazynu

>[!NOTE]
>Migawek magazynu używać miejsca do magazynowania, która została przydzielona do jednostek dużych wystąpień HANA. Należy wziąć pod uwagę następujące aspekty planowania migawek magazynu i jak wiele migawek magazynu, aby zachować. 

Określone mechanika migawek magazynu oprogramowania SAP HANA na platformie Azure (duże wystąpienia) obejmują:

- Migawki określonego magazynu (w tym punkcie czasu, gdy jest ona traktowana) wykorzystuje nieco magazynu.
- Dane zmiany zawartości i zawartości danych SAP HANA, pliki dopasowane na woluminie magazynu migawki wymaga przechowywania pierwotną wersją zawartości w bloku, a także zmiany danych.
- W rezultacie migawki magazynu zwiększa rozmiar. Już istnieje migawki, im większy staje się migawki magazynu.
- Więcej zmian wprowadzonych na woluminie bazy danych SAP HANA w okresie istnienia magazynu migawek, większy wykorzystanie miejsca w pamięci masowej migawki.

Oprogramowanie SAP HANA na platformie Azure (duże wystąpienia) zawiera stałą woluminy o maksymalnym rozmiarze dla woluminów danych i dziennika platformy SAP HANA. Wykonywanie migawek tych woluminów eats do ilość miejsca na woluminie. Należy określić, kiedy należy zaplanować migawek magazynu. Należy również monitorować wykorzystanie miejsca woluminów magazynu, a także zarządzać liczby migawek, które są przechowywane. Można wyłączyć migawek magazynu, zarówno przy zaimportować mas dane i wykonywać inne ważne zmiany w bazie danych HANA. 


Poniższe sekcje zawierają informacje dotyczące wykonywania migawek, w tym ogólne zalecenia:

- Chociaż sprzętu może wytrzymać 255 migawek na woluminie, mają pozostać znacznie poniżej tej liczby. Zalecane jest 250 lub mniej.
- Przed wykonaniem migawki magazynu, monitorować i śledzić ilość wolnego miejsca.
- Zmniejsz liczbę migawek magazynu, w oparciu o ilość wolnego miejsca. Można zmniejszyć liczbę migawek, które są stale lub można rozszerzyć woluminów. Może zamówić łączność obejmującą dodatkowego miejsca do magazynowania w jednostkach 1 terabajt.
- Podczas działania, takie jak przenoszenie danych do platformy SAP HANA przy użyciu narzędzi migracji platformy SAP (R3load) lub przywracania baz danych SAP HANA z kopii zapasowych należy wyłączyć migawek magazynu na woluminie /hana/data. 
- Podczas większych reorganizacji tabel platformy SAP HANA migawek magazynu należy unikać, jeśli jest to możliwe.
- Migawki magazynu to warunek wstępny do wykorzystując po awarii możliwości odzyskiwania systemu SAP HANA na platformie Azure (duże wystąpienia).

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>Wymagania wstępne dotyczące korzystania z migawek magazynu samoobsługi

Aby upewnić się, czy skrypt migawka została wykonana pomyślnie, upewnij się, że Perl jest zainstalowany w systemie operacyjnym Linux na serwerze dużych wystąpień HANA. Perl jest wstępnie zainstalowany w Twojej jednostce dużych wystąpień HANA. Aby sprawdzić wersję środowiska Perl, użyj następującego polecenia:

`perl -v`

![Klucz publiczny jest kopiowany, uruchamiając następujące polecenie](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


## <a name="set-up-storage-snapshots"></a>Konfigurowanie migawek magazynu

Aby skonfigurować migawek magazynu przy użyciu dużych wystąpień HANA, wykonaj następujące kroki:
1. Upewnij się, że Perl jest zainstalowany w systemie operacyjnym Linux na serwerze dużych wystąpień HANA.
1. Modyfikowanie/etc/ssh/ssh\_konfiguracji, aby dodać wiersz _Mac hmac-sha1_.
1. W węźle głównym dla każdego wystąpienia platformy SAP HANA, którą pracujesz, należy utworzyć konto kopii zapasowej platformy SAP HANA, jeśli ma to zastosowanie.
1. Zainstaluj klienta SAP HANA HDB na wszystkich serwerach duże wystąpienia SAP HANA.
1. Na pierwszym serwerze duże wystąpienia SAP HANA każdego regionu należy utworzyć klucz publiczny do dostępu do podstawowej infrastruktury magazynu, który kontroluje tworzenia migawki.
1. Skopiuj skrypty i pliku konfiguracji z [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts) do lokalizacji **hdbsql** w instalacji oprogramowania SAP HANA.
1. Modyfikowanie *HANABackupDetails.txt* plików, gdy jest to konieczne do specyfikacji odpowiedniego klienta.

Pobierz najnowsze skrypty migawki i dokumentacji [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts). Po pobraniu pakietu skryptu migawki z [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts), możesz także uzyskać w dokumentacji PDF skrypty jako część pakietu skryptu. Każdy pakiet skrypt ma własną dokumentację PDF.

### <a name="consideration-for-mcod-scenarios"></a>Ważną kwestią dotyczącą MCOD scenariuszy
Jeśli korzystasz z [scenariusza MCOD](https://launchpad.support.sap.com/#/notes/1681092) z wieloma wystąpieniami platformy SAP HANA w jednej jednostce dużych wystąpień HANA mieć osobne woluminy magazynu zainicjowana obsługa administracyjna dla wszystkich wystąpień oprogramowania SAP HANA. W bieżącej wersji automatyzacji samoobsługi migawki nie można zainicjować oddzielne migawek w systemie wystąpienia każdej platformy HANA identyfikator. Funkcje zapewnia sprawdza, czy zarejestrowanych wystąpień oprogramowania SAP HANA serwera w pliku konfiguracji (zobacz w dalszej części tego artykułu) i wykonuje migawek równocześnie woluminów wszystkich wystąpień, które są zarejestrowane w jednostce.
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>Krok 1: Zainstaluj klienta SAP HANA HDB

System operacyjny Linux, które są zainstalowane na platformie SAP HANA na platformie Azure (duże wystąpienia) zawiera foldery i skrypty wymagane do wykonania migawek magazynu oprogramowania SAP HANA do celów odzyskiwania kopii zapasowych i odzyskiwanie po awarii. Wyszukaj więcej najnowsze wersje w [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts). Najbardziej aktualną wersję wydania skryptów jest 3.x. Różne skrypty mogą mieć różne wersje pomocnicze w ramach tej samej wersji głównej.

>[!IMPORTANT]
>Podczas przenoszenia w wersji 2.1 w wersji 3.x skryptów, należy pamiętać, że struktura pliku konfiguracji i niektóre przypadki składni uległa zmianie. Zobacz objaśnienia w sekcjach określone. 

Jest odpowiedzialny za do zainstalowania klienta SAP HANA HDB w jednostkach dużych wystąpień HANA, podczas instalowania platformy SAP HANA.

### <a name="step-2-change-the-etcsshsshconfig"></a>Krok 2: Zmienianie/etc/ssh/ssh\_konfiguracji

Zmiana `/etc/ssh/ssh_config` , dodając _Mac hmac-sha1_ wiersz, jak pokazano poniżej:
```
#   RhostsRSAAuthentication no
#   RSAAuthentication yes
#   PasswordAuthentication yes
#   HostbasedAuthentication no
#   GSSAPIAuthentication no
#   GSSAPIDelegateCredentials no
#   GSSAPIKeyExchange no
#   GSSAPITrustDNS no
#   BatchMode no
#   CheckHostIP yes
#   AddressFamily any
#   ConnectTimeout 0
#   StrictHostKeyChecking ask
#   IdentityFile ~/.ssh/identity
#   IdentityFile ~/.ssh/id_rsa
#   IdentityFile ~/.ssh/id_dsa
#   Port 22
Protocol 2
#   Cipher 3des
#   Ciphers aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-cbc,3des-cbc
#   MACs hmac-md5,hmac-sha1,umac-64@openssh.com,hmac-ripemd160
MACs hmac-sha1
#   EscapeChar ~
#   Tunnel no
#   TunnelDevice any:any
#   PermitLocalCommand no
#   VisualHostKey no
#   ProxyCommand ssh -q -W %h:%p gateway.example.com
```

### <a name="step-3-create-a-public-key"></a>Krok 3: Tworzenie klucza publicznego

Aby włączyć dostęp do interfejsów migawki magazynu Twojej dzierżawy dużych wystąpień HANA, należy ustanowić procedura logowania za pomocą klucza publicznego. Na pierwszy SAP HANA na serwerze Azure (duże wystąpienia) w dzierżawie Utwórz klucz publiczny ma być używany do dostępu do infrastruktury magazynu. Klucz publiczny gwarantuje, że do logowania się na interfejsy migawki magazynu nie jest wymagane hasło. Tworzenie klucza publicznego również oznacza to, nie należy do obsługi poświadczeń haseł. W systemie Linux na serwerze SAP HANA, duże wystąpienia Wydaj następujące polecenie, aby wygenerować klucz publiczny:
```
  ssh-keygen -t rsa –b 5120 -C ""
```

Nowa lokalizacja jest **_/root/.ssh/id\_rsa.pub**. Nie należy wprowadzać hasło; w przeciwnym razie należy wprowadzać hasła za każdym razem, logowania. Zamiast tego należy wybrać **Enter** dwa razy, aby usunąć wymaganie "Wprowadź hasło" do logowania.

Upewnij się, że klucz publiczny został rozwiązany, zgodnie z oczekiwaniami, zmieniając folderów **/root/.ssh/** , a następnie wykonywanie `ls` polecenia. Jeśli klucz jest obecny, skopiuj go, uruchamiając następujące polecenie:

![Klucz publiczny jest kopiowany, uruchamiając następujące polecenie](./media/hana-overview-high-availability-disaster-recovery/image2-public-key.png)

W tym momencie skontaktować się z platformą SAP HANA na platformie Azure i je podać przy użyciu klucza publicznego. Przedstawiciela biura obsługi używa klucza publicznego, aby zarejestrować go w podstawowej infrastruktury magazynu, który jest używać dla Twojej dzierżawy dużych wystąpień HANA.

### <a name="step-4-create-an-sap-hana-user-account"></a>Krok 4: Tworzenie konta użytkownika platformy SAP HANA

Aby zainicjować tworzenie migawek platformy SAP HANA, należy utworzyć konto użytkownika w, można użyć skryptów migawek magazynu oprogramowania SAP HANA. Tworzenie konta użytkownika platformy SAP HANA w obrębie SAP HANA Studio, w tym celu. Musi zostać utworzona użytkownika, w obszarze SYSTEMDB, a nie w bazie danych identyfikatora SID dla MDC. W środowisku jednego kontenera użytkownika jest skonfigurowana, w ramach bazy danych dzierżaw. To konto musi mieć następujące uprawnienia: **Wykonaj kopię zapasową administratora** i **odczytu z katalogu**. W tym przykładzie nazwa użytkownika to **SCADMIN**. Nazwa konta użytkownika, które są utworzone w programie Studio HANA jest uwzględniana wielkość liter. Upewnij się, że wybrano **nie** wymagająca użytkownikowi na zmianę hasła dla ich następnym zalogowaniu.

![Tworzenie użytkownika w programie Studio HANA](./media/hana-overview-high-availability-disaster-recovery/image3-creating-user.png)

Użycie MCOD wdrożeń z wieloma wystąpieniami platformy SAP HANA w jednej jednostce, należy powtórzyć ten krok dla każdego wystąpienia platformy SAP HANA.

### <a name="step-5-authorize-the-sap-hana-user-account"></a>Krok 5. Autoryzacja konta użytkownika platformy SAP HANA

W tym kroku autoryzować konta użytkownika platformy SAP HANA, który został utworzony, dzięki czemu nie ma potrzeby skrypty do przesyłania haseł w czasie wykonywania. Polecenie platformy SAP HANA `hdbuserstore` umożliwia tworzenie klucza użytkownika platformy SAP HANA, który jest przechowywany na co najmniej jeden węzeł oprogramowania SAP HANA. Klucz użytkownika umożliwia dostęp użytkowników oprogramowania SAP HANA, bez konieczności zarządzania hasłami z w ramach procesu wykonywania skryptów. Proces wykonywania skryptów jest omówione w dalszej części tego artykułu.

>[!IMPORTANT]
>Uruchom następujące polecenie, w obszarze użytkownika, że skrypty są zaplanowane do wykonania. W przeciwnym razie skrypt nie może działać prawidłowo.

Wprowadź `hdbuserstore` polecenia w następujący sposób:

**HANA niż MDC Instalatora**
```
hdbuserstore set <key> <host>:<3[instance]15> <user> <password>
```

**MDC HANA Instalatora**
```
hdbuserstore set <key> <host>:<3[instance]13> <user> <password>
```

W poniższym przykładzie użytkownik jest **SCADMIN01**, nazwa hosta jest **lhanad01**, a liczby wystąpień jest **01**:
```
hdbuserstore set SCADMIN01 lhanad01:30115 <backup username> <password>
```
Jeśli używasz HANA MCOD wdrożenia z wieloma wystąpieniami platformy SAP HANA w jednej jednostce, musisz Powtórz krok dla każdego wystąpienia platformy SAP HANA oraz skojarzonego użytkownika kopii zapasowej w jednostce.

W przypadku oprogramowania SAP HANA skalowalnego w poziomie konfiguracji musisz zarządzać, wszystkie skrypty z jednego serwera. W tym przykładzie klucz platformy SAP HANA **SCADMIN01** podanego dla każdego hosta w sposób, który pokazuje, które hostów jest powiązany z klucza. Zmiana konta kopii zapasowych oprogramowania SAP HANA z liczbą wystąpień bazy danych HANA. Klucz musi mieć uprawnienia administracyjne na hoście, do którego jest przypisany, a użytkownik kopii zapasowej dla konfiguracji skalowania w poziomie musi mieć prawa dostępu do wszystkich wystąpień oprogramowania SAP HANA. Zakładając, że trzy węzły skalowalnego w poziomie mają nazwy **lhanad01**, **lhanad02**, i **lhanad03**, sekwencja poleceń wygląda następująco:

```
hdbuserstore set SCADMIN01 lhanad01:30115 SCADMIN <password>
hdbuserstore set SCADMIN01 lhanad02:30115 SCADMIN <password>
hdbuserstore set SCADMIN01 lhanad03:30115 SCADMIN <password>
```

### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>Krok 6: Pobieranie skryptów migawki, skonfiguruj migawki i testowania konfiguracji i łączność

Pobierz najnowszą wersję skryptów z [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts). Skopiuj pobierane skrypty i plik tekstowy do katalogu roboczego **hdbsql**. W przypadku bieżącej instalacji oprogramowania HANA ten katalog jest w następującym formacie: /hana/shared/D01/exe/linuxx86\_64/hdb. 
``` 
azure_hana_backup.pl 
azure_hana_replication_status.pl 
azure_hana_snapshot_details.pl 
azure_hana_snapshot_delete.pl 
testHANAConnection.pl 
testStorageSnapshotConnection.pl 
removeTestStorageSnapshot.pl
azure_hana_dr_failover.pl
azure_hana_test_dr_failover.pl 
HANABackupCustomerDetails.txt 
``` 

Podczas pracy ze skryptami języka Perl: 

- Nigdy nie modyfikują skryptów, o ile nie zdecyduje się przez Microsoft Operations.
- Po wyświetleniu monitu, aby zmodyfikować skrypt lub plik parametrów, należy zawsze używać edytora tekstów systemu Linux, takiego jak "vi", a nie edytorze Windows, takim jak Notatnik. Za pomocą edytora Windows może spowodować uszkodzenie formatu pliku.
- Zawsze używaj najnowszych skryptów. Najnowszą wersję można pobrać z witryny GitHub.
- Użyj tej samej wersji skryptów na krajobrazu.
- Przetestuj skrypty i przyzwyczaić wymagane parametry i dane wyjściowe skryptu przed użyciem ich bezpośrednio w systemie produkcyjnym.
- Nie zmieniaj nazwę punktu instalacji serwera aprowizowanego przez Microsoft Operations. Skrypty te zależą od tych punktów instalacji standard, ma być dostępna dla pomyślne wykonanie.


Celem różnych skryptów i plików jest następująca:

- **azure\_hana\_backup.pl**: Ten skrypt jest zaplanowane przy użyciu narzędzia do planowania Cron w systemie Linux wykonywania migawek magazynu danych HANA i udostępnione woluminy, wolumin /hana/logbackups lub systemu operacyjnego.
- **azure\_hana\_replication\_status.pl**: Ten skrypt zawiera podstawowe informacje dotyczące stanu replikacji z lokacji produkcyjnej do lokacji odzyskiwania po awarii. Monitory skryptu, aby upewnić się, że replikacja odbywa się i pokazuje, że rozmiar elementów są replikowane. Zawiera również wskazówki, jeśli replikacji trwa zbyt długo lub łącze jest wyłączona.
- **azure\_hana\_snapshot\_details.pl**: Ten skrypt zawiera listę z podstawowymi informacjami dotyczącymi wszystkie migawki, na każdym woluminie, które istnieją w Twoim środowisku. Ten skrypt można uruchomić na serwerze podstawowym lub w jednostce server w lokalizacji odzyskiwania danych po awarii. Skrypt zawiera następujące informacje, w rozbiciu na każdy wolumin, który zawiera migawki:
   * Rozmiar całkowitej migawek woluminu
   * Następujące informacje w każdej migawki w danym woluminie: 
      - Nazwa migawki 
      - Czas utworzenia 
      - Rozmiar migawki
      - Częstotliwość migawek
      - Identyfikator kopii zapasowej HANA skojarzone z tej migawki, jeśli jest to odpowiednie
- **azure\_hana\_snapshot\_delete.pl**: Ten skrypt usuwa migawkę pamięci masowej lub zestawu migawek. Można użyć identyfikator kopii zapasowej platformy SAP HANA, tak jak w programie HANA Studio lub nazwy migawki magazynu. Obecnie identyfikator kopii zapasowej jest tylko powiązane migawek utworzonych dla platformy HANA danych/log/udostępnione woluminy. W przeciwnym razie jeśli zostanie wprowadzony identyfikator migawki zmierza wszystkie migawki, które odpowiadają identyfikator migawki wprowadzony.  
- **testHANAConnection.pl**: Ten skrypt testuje połączenie z wystąpieniem platformy SAP HANA i jest wymagane do skonfigurowania migawek magazynu.
- **testStorageSnapshotConnection.pl**: Ten skrypt ma dwa cele. Najpierw gwarantuje, że jednostki dużych wystąpień HANA, która uruchamia skrypty ma dostęp do przypisanych magazynu maszyny wirtualnej i interfejs migawki magazynu dużych wystąpień HANA. Drugi cel to można utworzyć tymczasowego migawki wystąpienia HANA, które testujesz. Ten skrypt powinien być uruchamiany dla każdego wystąpienia oprogramowania HANA na serwerze, aby upewnić się, że skrypty kopii zapasowej działać zgodnie z oczekiwaniami.
- **removeTestStorageSnapshot.pl**: Ten skrypt usuwa migawkę testu, utworzone za pomocą skryptu **testStorageSnapshotConnection.pl**.
- **azure\_hana\_dr\_failover.pl**: Ten skrypt przełączenia w tryb failover odzyskiwania po awarii w innym regionie. Skrypt musi być wykonywane na jednostce dużych wystąpień HANA w regionie odzyskiwania po awarii, lub w jednostce ma do trybu failover. Ten skrypt zatrzymanie replikacji magazynu ze strony głównej stronie dodatkowej, przywrócenie najnowszej migawki w woluminach odzyskiwania po awarii i zapewnia punkty instalacji do odzyskiwania po awarii woluminów.
- **azure\_hana\_test\_dr\_failover.pl**: Ten skrypt wykonuje test trybu failover do lokacji odzyskiwania po awarii. W przeciwieństwie do skryptu azure_hana_dr_failover.pl to wykonanie nie przerywa działania replikacji magazynu z podstawowych do pomocniczych. Zamiast tego klony woluminów replikowanego magazynu są tworzone po stronie odzyskiwania po awarii i znajdują się punkty instalacji woluminów sklonowane. 
- **HANABackupCustomerDetails.txt**: Ten plik jest plik konfiguracji można modyfikować, który chcesz zmodyfikować, aby dostosować je do konfiguracji oprogramowania SAP HANA. *HANABackupCustomerDetails.txt* plik jest plikiem formantu i konfiguracja dla skryptu, który uruchamia migawek magazynu. Dostosuj plik dla instalacji i celów. Pojawi się **Nazwa kopii zapasowej magazynu** i **adres IP magazynu** z platformy SAP HANA na platformie Azure podczas wdrażania wystąpień. Nie można zmodyfikować sekwencji, zamawiania lub odstępy tych zmiennych w tym pliku. Jeśli to zrobisz, skryptów, nie działają poprawnie. Ponadto zostanie wyświetlony adres IP węzła skalowanie w górę lub węzła głównego (jeśli skalowalnego w poziomie) z platformy SAP HANA na platformie Azure. Znasz także liczby wystąpień HANA, której można korzystać podczas instalacji oprogramowania SAP HANA. Teraz należy dodać nazwę kopii zapasowej do pliku konfiguracji.

Skalowanie w pionie lub poziomie wdrożenia plik konfiguracji będzie wyglądać podobnie jak w poniższym przykładzie, po wypełnieniu nazwę serwera, jednostka dużych wystąpień HANA i adres IP serwera. Wypełnij wszystkie wymagane pola dla każdego identyfikatora SID HANA SAP, aby tworzenie kopii zapasowej lub odzyskiwania.

Możesz również skomentować wierszy wystąpień, które nie chcesz, aby utworzyć kopię zapasową w okresie czasu, dodając "#" przed polem wymaganym. Ponadto nie trzeba wprowadzić wszystkie wystąpienia SAP HANA, które znajdują się na serwerze, jeśli nie ma potrzeby tworzenia kopii lub odzyskiwania tego konkretnego wystąpienia. Format muszą być przechowywane dla wszystkich pól, lub wszystkie skrypty zgłaszać komunikat o błędzie i kończy działanie skryptu. Możesz usunąć dodatkowe wiersze wymagane jakieś informacje informacji SID, które nie jest używany po ostatniego wystąpienia platformy SAP HANA w użyciu. Wszystkie wiersze musisz być wypełnione, oznaczone jako komentarz lub usunięty.

>[!IMPORTANT]
>Struktura pliku zmienione wraz z przejściem z wersji 2.1 w wersji 3.x. Jeśli chcesz korzystać ze skryptów w wersji 3.x, konieczne jest dostosowanie struktura pliku konfiguracji. 


```
HANA Server Name: testing01
HANA Server IP Address: 172.18.18.50
```

Dla każdego wystąpienia, skonfigurowanego w jednostce dużych wystąpień HANA lub dla konfiguracji skalowania w poziomie musisz zdefiniować dane w następujący sposób:

    
```
######***SID #1 Information***#####
SID1: h01
###Provided by Microsoft Operations###
SID1 Storage Backup Name: clt1h01backup
SID1 Storage IP Address: 172.18.18.11
######     Customer Provided    ######
SID1 HANA instance number: 00
SID1 HANA HDBuserstore Name: SCADMINH01
```
Powtórz tę konfigurację w każdym z węzłów skalowalnego w poziomie i konfiguracji replikacji systemu HANA. Ta miara gwarantuje, że w przypadku awarii kopie zapasowe i ostateczną magazynu replikacji będą nadal działać.   

Po umieszczeniu wszystkich danych konfiguracji do *HANABackupCustomerDetails.txt* plików, należy sprawdzić, czy konfiguracje są poprawne dane wystąpienie oprogramowania HANA. Użyj skryptu `testHANAConnection.pl`, który jest niezależne skalowanie w pionie lub poziomie konfiguracji SAP HANA.

```
testHANAConnection.pl
```

W przypadku oprogramowania SAP HANA skalowalnego w poziomie konfiguracji upewnij się, że główny wystąpienie oprogramowania HANA ma dostęp do wszystkich wymaganych serwerów HANA oraz wystąpień. Nie ma żadnych parametrów do skryptu testowego, ale należy dodać dane do *HANABackupCustomerDetails.txt* pliku konfiguracji dla skryptu działać poprawnie. Zwracane są tylko kody błędów polecenia powłoki, więc nie jest możliwe dla skryptu, aby wskazywał błąd, sprawdź każde wystąpienie. Mimo tego skryptu zapewniają niektóre przydatne komentarze należy dokładnie sprawdzić.

Aby uruchomić skrypt, wprowadź następujące polecenie:
```
 ./testHANAConnection.pl
```
Jeśli skrypt pomyślnie uzyska stan wystąpienie oprogramowania HANA, wyświetla komunikat, że połączenia platformy HANA zakończyło się pomyślnie.


Następnym krokiem testu jest aby sprawdzić połączenie z magazynu, w oparciu o dane umieszczane w *HANABackupCustomerDetails.txt* konfiguracji pliku, a następnie wykonaj migawkę testu. Przed wykonaniem `azure_hana_backup.pl` skryptu możesz uruchomić ten test. Jeśli wolumin nie zawiera żadnych migawek, jest możliwe ustalenie, czy wolumin jest pusty lub jeśli wystąpił błąd protokołu SSH w celu uzyskania szczegółowych informacji migawki. Z tego powodu skrypt wykonuje dwie czynności:

- Sprawdza się, że magazyn maszyny wirtualnej dzierżawcy i interfejsy są dostępne dla skryptów do wykonywania migawki.
- Tworzy migawkę testu lub dummy, dla każdego woluminu przez wystąpienie oprogramowania HANA.

Z tego powodu wystąpienie oprogramowania HANA jest dołączana jako argument. Jeśli wykonanie nie powiedzie się, nie jest możliwe podanie sprawdzanie połączenia z magazynem. Nawet jeśli nie ma błędów sprawdzania, skrypt zawiera wskazówki pomocne.

1. Wykonaj sekwencję poleceń, aby wykonać ten test:

   ```
   ssh <StorageUserName>@<StorageIP>
   ```

   Magazyn nazwę użytkownika i adres IP magazynu zostały nadane następującym użytkownikom można na przekazanie jednostki dużych wystąpień HANA.

1. Uruchom skrypt testu:
   ```
    ./testStorageSnapshotConnection.pl
   ```

Skrypt próbuje zalogować się do magazynu przy użyciu klucza publicznego dostarczane w ramach poprzednich kroków konfiguracji, a także przy użyciu danych skonfigurowanych w *HANABackupCustomerDetails.txt* pliku. W przypadku logowania zakończy się pomyślnie, jest wyświetlana następująca zawartość:

```
**********************Checking access to Storage**********************
Storage Access successful!!!!!!!!!!!!!!
```

Jeśli wystąpią problemy podczas łączenia się z konsoli magazynu, dane wyjściowe wyglądają następująco:

```
**********************Checking access to Storage**********************
WARNING: Storage check status command 'volume show -type RW -fields volume' failed: 65280
WARNING: Please check the following:
WARNING: Was publickey sent to Microsoft Service Team?
WARNING: If passphrase entered while using tool, publickey must be re-created and passphrase must be left blank for both entries
WARNING: Ensure correct IP address was entered in HANABackupCustomerDetails.txt
WARNING: Ensure correct Storage backup name was entered in HANABackupCustomerDetails.txt
WARNING: Ensure that no modification in format HANABackupCustomerDetails.txt like additional lines, line numbers or spacing
WARNING: ******************Exiting Script*******************************
```

Po pomyślnym zalogowaniu interfejsom magazynu maszyny wirtualnej skrypt będzie kontynuowane z użyciem fazy 2 i tworzących migawkę testu. Poniżej pokazano dane wyjściowe trzema węzłami skalowalnego w poziomie konfiguracji SAP Hana:

```
**********************Creating Storage snapshot**********************
Taking snapshot testStorage.recent for hana_data_hm3_mnt00001_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00001_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00002_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00002_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00003_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00003_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_backups_hm3_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_backups_hm3_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_hm3_mnt00001_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_hm3_mnt00002_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_hm3_mnt00003_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_shared_hm3_t020_vol ...
Snapshot created successfully.
```

Migawki testu została wykonana pomyślnie za pomocą skryptu, można kontynuować konfigurowanie migawek rzeczywisty magazyn. Jeśli się nie powiedzie, należy zbadać problemy przed skierowaniem ich do przodu. Migawki testu powinno pozostać wokół, dopóki pierwszych rzeczywistych migawek są wykonywane.


### <a name="step-7-perform-snapshots"></a>Krok 7: Wykonaj migawki

Po zakończeniu kroków przygotowania, można uruchomić konfigurację migawki rzeczywisty magazyn. Skrypt do zaplanowania współpracuje z platformy SAP HANA skalowanie w górę i skalowania w poziomie konfiguracji. Okresowe i regularnego wykonywania skryptu kopii zapasowej należy zaplanować skryptu za pomocą narzędzia cron. 

Można utworzyć trzy typy kopii zapasowych migawki:
- **HANA**: Kopii zapasowej migawki połączone, w którym woluminów, które zawierają/hana/data i/hana/udostępnione (który zawiera także /usr/sap) są objęte skoordynowanego migawki. Przywracanie pojedynczego pliku jest możliwe z tą migawką.
- **Dzienniki**: Kopii zapasowej migawki woluminu logbackups/hana /. Nie migawki HANA zostanie wywołany do wykonywania tej migawki magazynu. Oznacza, że ten wolumin magazynu zawiera kopie zapasowe dziennika transakcji platformy SAP HANA. Są one wykonywane częściej ograniczyć wzrostu dzienników i zapobiegania utracie danych. Przywracanie pojedynczego pliku jest możliwe z tą migawką. Nie zmniejszyć częstotliwość mniej niż 3 minut.
- **Rozruch**: Migawki woluminu, który zawiera numer jednostki logicznej (LUN) rozruchu dużych wystąpień HANA. Ta kopia zapasowa migawki jest możliwe tylko w przypadku typu I jednostki SKU z dużymi wystąpieniami platformy HANA. Nie można wykonać pojedynczy plik Przywracanie z migawki woluminu, który zawiera rozruchu jednostki LUN.


>[!NOTE]
> Składnia wywołania te trzy typy migawek zmienione wraz z przejściem do skryptów 3.x wersji, które obsługują MCOD wdrożeń. Nie ma potrzeby już określić identyfikator SID HANA wystąpienia. Należy upewnić się, że wystąpieniami platformy SAP HANA jednostki są skonfigurowane w pliku konfiguracyjnym *HANABackupCustomerDetails.txt*.

>[!NOTE]
> Podczas wykonywania skryptu po raz pierwszy, może wyświetlać w środowisku z wieloma identyfikatorami SID nieoczekiwane błędy. Ponowne uruchomienie skryptu rozwiązuje ten problem.



Nowa Składnia wywołania do wykonywania migawek magazynu za pomocą skryptu *azure_hana_backup.pl* wygląda podobnie do następującego:

```
HANA backup covering /hana/data and /hana/shared (includes/usr/sap)
./azure_hana_backup.pl hana <snapshot_prefix> <snapshot_frequency> <number of snapshots retained>

For /hana/logbackups snapshot
./azure_hana_backup.pl logs <snapshot_prefix> <snapshot_frequency> <number of snapshots retained>

For snapshot of the volume storing the boot LUN
./azure_hana_backup.pl boot <HANA Large Instance Type> <snapshot_prefix> <snapshot_frequency> <number of snapshots retained>

```

Szczegóły parametrów są następujące: 

- Pierwszy parametr charakteryzuje typ kopii zapasowej migawki. Wartości dozwolone są **hana**, **dzienniki**, i **rozruchu**. 
- Parametr  **\<HANA dużego wystąpienia typu >** wymagane do rozruchu kopie zapasowe woluminów tylko. Dwóch prawidłowych wartości "TypeI" lub "TypeII" są zależne od HANA duże wystąpienia jednostki. Aby dowiedzieć się, jaki typ urządzenia, zobacz [platformy SAP HANA (duże wystąpienia) — omówienie i architektura na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).  
- Parametr  **\<snapshot_prefix >** migawki lub etykieta kopii zapasowej dla typu migawka. Ma dwa cele: jeden jest nadaj jej nazwę, dzięki czemu będzie wiadomo, o ile te migawki. Drugi cel to skrypt *azure\_hana\_backup.pl* do określenia liczby migawek magazynu, które są przechowywane w ramach tej określonej etykiety. Jeśli zaplanować dwie kopie zapasowe migawek magazynu tego samego typu (takich jak **hana**), za pomocą dwóch różnych etykiet i zdefiniuj, czy powinny być przechowywane 30 migawek dla każdego, na końcu 60 migawek magazynu woluminów, których to dotyczy. Alfanumeryczne ("A – Z, a-z, 0 – 9"), podkreślenia ("_") i myślnik ("-") może zawierać znaków. 
- Parametr  **\<snapshot_frequency >** jest zarezerwowane dla przyszłego rozwoju i nie ma żadnego wpływu. Ustaw ją na "3min" podczas wykonywania kopii zapasowych typu **dziennika**i "15 min" podczas wykonywania kopii zapasowej innych typów.
- Parametr  **\<liczby migawek przechowywane >** definiuje przechowywania migawek pośrednio przez zdefiniowanie liczby migawek z tym samym prefiksem migawki (etykieta). Ten parametr jest ważne w przypadku zaplanowanego wykonania za pomocą usługi cron. Jeśli liczba migawki za pomocą tego samego snapshot_prefix przekracza wartość podana w tym parametrze, najstarsza migawka jest usuwana przed wykonaniem nową migawkę pamięci masowej.

W przypadku skalowania w poziomie skrypt wykonuje dodatkowego sprawdzenia, czy można uzyskać dostępu do wszystkich serwerów HANA. Skrypt sprawdza również, że wszystkich wystąpień HANA zwraca odpowiedni stan wystąpienia, zanim utworzy migawkę platformy SAP HANA. Migawka platformy SAP HANA następuje migawki magazynu.

Wykonywanie skryptu `azure_hana_backup.pl` tworzy Magazyn migawek na następujące trzy etapy:

1. Wykonuje migawkę platformy SAP HANA
1. Wykonuje migawkę pamięci masowej
1. Usuwa migawkę platformy SAP HANA, który został utworzony przed wykonaniem migawki magazynu

Aby wykonać skrypt, należy wywołać go z folderu pliku wykonywalnego HDB, do którego została skopiowana. 

Okres przechowywania jest podawana przy użyciu liczby migawek, które są przesyłane jako parametr podczas wykonywania skryptu. Ilość czasu, który pasuje do żadnego migawek magazynu jest funkcją czasu wykonania i liczby migawek przesyłany w postaci parametru podczas wykonywania skryptu. W przypadku liczby migawek, które są utrzymywane przekracza liczbę, które są nazywane jako parametr w wywołaniu skryptu, najstarsze migawki magazynu w tej samej etykiety zostaną usunięte, zanim zostanie wykonany nową migawkę. Liczba zapewniają jako ostatni parametr wywołanie jest liczba służących do kontrolowania liczby migawek, które są zachowane. Z tym numerem można także kontrolować, pośrednio, miejsca na dysku używanego dla migawki. 

> [!NOTE]
>Zaraz po zmianie etykiety liczenie zostanie ponownie uruchomiona. Trzeba strict metodach etykietowania, dzięki czemu Twoje migawek nie zostaną przypadkowo usunięte.

## <a name="snapshot-strategies"></a>Strategie migawki
Częstotliwość migawek dla różnych typów, zależy od tego, czy korzystasz z funkcji odzyskiwania po awarii dużych wystąpień HANA. Ta funkcja opiera się na migawek magazynu, w których może wymagać specjalnego zalecenia dla okresów częstotliwość i wykonywanie migawek magazynu. 

Zagadnienia i zalecenia, które należy wykonać, zakłada się, że czy *nie* korzystać z funkcji odzyskiwania po awarii, która oferuje dużych wystąpień HANA. Zamiast tego należy użyć migawek magazynu kopii zapasowych i być w stanie zapewnić odzyskiwanie w momencie w ciągu ostatnich 30 dni. Biorąc pod uwagę ograniczenia liczby migawek i miejsce, klienci mają za następujące wymagania:

- Czas odzyskiwania w momencie odzyskiwania.
- Miejsce.
- Punkt odzyskiwania i cele czasu odzyskiwania do potencjalnego odzyskiwania po awarii.
- Ostateczna wykonanie względem dysków, kopie zapasowe bazy danych pełnej platformy HANA. Zawsze, gdy kopii zapasowej pełnej obsługi bazy danych względem dysków lub **backint** interfejsu jest wykonywane, wykonywanie migawek magazynu kończy się niepowodzeniem. Jeśli planujesz wykonać kopie zapasowe bazy danych na pełne, na podstawie migawek magazynu, upewnij się, że wykonywanie migawek magazynu jest wyłączone w tym czasie.
- Liczba migawek na woluminie (ograniczone do 250).


W przypadku klientów, którzy nie używają funkcji odzyskiwania po awarii w dużych wystąpień HANA okres migawki jest dłuższe interwały. W takich przypadkach klienci wykonywania migawek połączone na /hana/data i /hana/shared (w tym /usr/sap) w okresach 12-godzinny lub 24-godzinnego i prowadzą migawki przez jeden miesiąc. Dotyczy to także przy użyciu migawek woluminu kopii zapasowej dziennika. Jednak wykonanie kopii zapasowej dziennika transakcji platformy SAP HANA na woluminie kopii zapasowej dziennika występuje w 5-minutowych do 15-minutowy okresów.

Zaplanowany magazynu migawek najlepiej są wykonywane za pomocą usługi cron. Użyj tego samego skryptu dla wszystkich kopii zapasowych i awarii i że zmodyfikujesz skrypt danych wejściowych do dopasowania do różnych żądanie godziny tworzenia kopii zapasowej. Te migawki są wszystkie zaplanowane inaczej w cron w zależności od ich czas wykonywania: co godzinę, 12-godzinnego, codziennie lub co tydzień. 

Oto przykład harmonogramu wyrażenia cron w/etc/crontab:
```
00 1-23 * * * ./azure_hana_backup.pl hana hourlyhana 15min 46
10 00 * * *  ./azure_hana_backup.pl hana dailyhana 15min 28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup.pl logs regularlogback 3min 28
22 12 * * *  ./azure_hana_backup.pl logs dailylogback 3min 28
30 00 * * *  ./azure_hana_backup.pl boot TypeI dailyboot 15min 28
```
W poprzednim przykładzie, jest co godzinę migawki połączone, która obejmuje /hana/shared (obejmuje/usr/sap) i woluminów, które zawierają dane/hana/lokalizacji. Na użytek tego typu migawka szybsze odzyskiwanie w momencie w ciągu ostatnich dwóch dni. Ponadto istnieje dzienną migawkę codziennie na tych woluminach. Dlatego masz dwa dni obowiązywania przez godzinę migawki, a także cztery tygodnie pokrycia, dzienne migawki. Ponadto wolumin kopii zapasowej dziennika transakcji kopia zapasowa jest tworzona codziennie. Te kopie zapasowe są przechowywane także czterech tygodni. Jak widać w trzecim wierszu crontab kopii zapasowej dziennika transakcji HANA jest zaplanowane do wykonania co 5 minut. Godziny rozpoczęcia zadań różnych cron, które są wykonywane migawek magazynu są zróżnicowane, tak, aby te migawki nie są wykonywane w całości w pewnym momencie w czasie. 

W poniższym przykładzie możesz wykonać połączone migawkę, która obejmuje woluminów, które zawierają/hana/data i/hana/udostępnione (takie jak/usr/sap) lokalizacje w systemie godzinowym. Te migawki są przechowywane przez dwa dni. Migawki woluminów kopii zapasowej dziennika transakcji są wykonywane na podstawie 5-minutowych i są przechowywane przez 4 godziny. Jak wcześniej, kopii zapasowej pliku dziennika transakcji HANA jest zaplanowane do wykonania co 5 minut. Migawki woluminu kopii zapasowej dziennika transakcji jest wykonywane z opóźnieniem 2-minutowy po rozpoczęciu kopii zapasowej dziennika transakcji. W ramach tych 2 minuty kopia zapasowa dziennika transakcji platformy SAP HANA powinno zostać zakończone w normalnych warunkach. Jako przed, woluminu rozruchowego jednostki LUN kopia zapasowa jest tworzona raz dziennie migawki magazynu i jest przechowywany przez cztery tygodnie.

```
10 0-23 * * * ./azure_hana_backup.pl hana hourlyhana 15min 48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup.pl logs regularlogback 3min 28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup.pl logs logback 3min 48
30 00 * * *  ./azure_hana_backup.pl boot TypeII dailyboot 15min 28
```

Poniższa ilustracja przedstawia sekwencji z poprzedniego przykładu, z wyłączeniem rozruchu jednostki LUN:

![Relacja między migawki i kopii zapasowych](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA przeprowadza regularne zapisu woluminu /hana/log dokumentowanie zatwierdzone zmiany w bazie danych. Na bieżąco SAP HANA zapisuje punktu zapisu do woluminu /hana/data. Określone w crontab kopia zapasowa dziennika transakcji platformy SAP HANA jest wykonywana co 5 minut. Zobaczysz również, czy migawki platformy SAP HANA jest wykonywane co godzinę w wyniku wyzwalania migawki magazynu połączone za pośrednictwem woluminów /hana/data i /hana/shared. Po pomyślnym migawki HANA migawki połączonego magazynu, jest wykonywany. Zgodnie z instrukcją w crontab, migawek magazynu na woluminie /hana/logbackup jest wykonywana co 5 minut, po kopii zapasowej dziennika transakcji HANA około 2 minuty.

> 

>[!IMPORTANT]
> Korzystanie z migawek magazynu kopii zapasowych oprogramowania SAP HANA jest przydatna tylko wtedy, gdy migawki są wykonywane w połączeniu z kopie zapasowe dziennika transakcji platformy SAP HANA. Te kopie zapasowe dziennika transakcji muszą obejmować okresach czasu między migawek magazynu. 

Jeśli wybierzesz zobowiązanie do użytkowników w momencie odzyskiwania przez 30 dni, należy:

- W skrajnych przypadkach dostępu połączonego magazynu, migawki /hana/data i /hana/shared, który jest 30 dni.
- Mieć kopie zapasowe dziennika transakcji ciągłych, które obejmują czas między dowolnymi migawek magazynu połączone. Tak najstarsze migawki woluminu kopii zapasowej dziennika transakcji musi być 30 dni. To nie jest tak, jeśli kopiujesz kopie zapasowe dziennika transakcji do innego udziału NFS, który znajduje się w usłudze Azure storage. W takim przypadku może pobierać stary kopie zapasowe dziennika transakcji z tego folderu wspólnego systemu plików NFS.

Aby korzystać z magazynu migawek i replikacji magazynu ostatecznej kopie zapasowe dziennika transakcji, musisz zmienić lokalizację, do którego platformy SAP HANA zapisuje kopie zapasowe dziennika transakcji. Można wprowadzić tę zmianę w Studio platformy HANA. Chociaż platformy SAP HANA tworzy kopie zapasowe segmentów pełny dziennik automatycznie, należy określić interwałem wykonywania kopii zapasowej dziennika, aby być deterministyczna. Jest to szczególnie istotne podczas używania opcji odzyskiwania po awarii, ponieważ ma wykonać kopie zapasowe dziennika z okresem deterministyczna. W poniższym przypadku 15 minut są ustawiane jako interwałem wykonywania kopii zapasowej dziennika.

![Zaplanuj dzienniki kopii zapasowych oprogramowania SAP HANA w systemie SAP HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

Możesz również tworzenie kopii zapasowych, które są częściej niż co 15 minut. Częstsze ustawienie jest często używane w połączeniu z dużymi wystąpieniami platformy HANA działania funkcji odzyskiwania po awarii. Niektórzy klienci wykonywać kopie zapasowe dziennika transakcji co 5 minut.  

Jeśli baza danych nigdy nie wykonano kopię zapasową, ostatnim krokiem jest wykonanie kopii zapasowej na podstawie pliku bazy danych do utworzenia pojedynczy wpis kopii zapasowej, który musi istnieć w wykaz kopii zapasowych. W przeciwnym razie platformy SAP HANA nie można zainicjować kopie zapasowe określonego dziennika.

![Tworzenie kopii zapasowych opartych na plikach do utworzenia pojedynczego wpisu tworzenia kopii zapasowej](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


Po swojej pierwszej migawek magazynu pomyślne zostały wykonane, można usunąć migawki testu, który został wykonany w kroku 6. Aby to zrobić, uruchom skrypt `removeTestStorageSnapshot.pl`:
```
./removeTestStorageSnapshot.pl
```

Oto przykład danych wyjściowych skryptu:
```
Checking Snapshot Status for h80
**********************Checking access to Storage**********************
Storage Snapshot Access successful.
**********************Getting list of volumes that match HANA instance specified**********************
Collecting set of volumes hosting HANA matching pattern *h80* ...
Volume show completed successfully.
Adding volume hana_data_h80_mnt00001_t020_vol to the snapshot list.
Adding volume hana_log_backups_h80_t020_vol to the snapshot list.
Adding volume hana_shared_h80_t020_vol to the snapshot list.
**********************Adding list of snapshots to volume list**********************
Collecting set of snapshots for each volume hosting HANA matching pattern *h80* ...
**********************Displaying Snapshots by Volume**********************
hana_data_h80_mnt00001_t020_vol
Test_HANA_Snapshot.2018-02-06_1753.3
Test_HANA_Snapshot.2018-02-06_1815.2
….
Command completed successfully.
Exiting with return code: 0
Command completed successfully.
```


### <a name="monitoring-the-number-and-size-of-snapshots-on-the-disk-volume"></a>Monitorowanie liczby i rozmiaru migawki na woluminie dysku

Na woluminie określonego magazynu można monitorować liczbę migawek i użyciu przestrzeni dyskowej, tych migawek. `ls` Polecenie nie wyświetla katalog migawki lub plików. Jednak polecenia systemu operacyjnego Linux `du` przedstawia szczegółowe informacje dotyczące tych migawek magazynu, ponieważ są one przechowywane na tej samej wielkości. Polecenie może służyć za pomocą następujących opcji:

- `du –sh .snapshot`: Ta opcja zapewnia daje w sumie wszystkich migawek Directory migawki.
- `du –sh --max-depth=1`: Ta opcja wyświetla wszystkie migawki, które są zapisywane w **.snapshot** folder i rozmiar każdej migawki.
- `du –hc`: Ta opcja zapewnia całkowity rozmiar używany przez wszystkie migawki.

Użyj tych poleceń, aby upewnić się, migawki, które są wykonywane i przechowywane nie zużywają cały magazyn na woluminach.

>[!NOTE]
>Migawki rozruchu jednostki LUN nie są widoczne przy użyciu poprzednich poleceń.

### <a name="getting-details-of-snapshots"></a>Trwa pobieranie szczegółów migawki
Aby uzyskać więcej informacji na temat migawek, można także użyć skryptu `azure_hana_snapshot_details.pl`. Ten skrypt można uruchomić w dowolnej lokalizacji w przypadku aktywnego serwera w lokalizacji odzyskiwania danych po awarii. Skrypt zawiera następujące dane wyjściowe z podziałem na każdy wolumin, który zawiera migawki: 
   * Rozmiar całkowitej migawek woluminu
   * Następujące informacje w każdej migawki w danym woluminie: 
      - Nazwa migawki 
      - Czas utworzenia 
      - Rozmiar migawki
      - Częstotliwość migawek
      - Identyfikator kopii zapasowej HANA skojarzone z tej migawki, jeśli jest to odpowiednie

Oto przykład składni wykonywania skryptu:

```
./azure_hana_snapshot_details.pl 
```

Ponieważ skrypt próbuje pobrać identyfikator kopii zapasowej HANA, trzeba połączyć się z wystąpieniem platformy SAP HANA. To połączenie wymaga pliku konfiguracji *HANABackupCustomerDetails.txt* należy poprawnie ustawić. Dane wyjściowe z dwiema migawkami na woluminie może wyglądać następująco:

```
**********************************************************
****Volume: hana_shared_SAPTSTHDB100_t020_vol       ***********
**********************************************************
Total Snapshot Size:  411.8MB
----------------------------------------------------------
Snapshot:   customer.2016-09-20_1404.0
Create Time:   "Tue Sep 20 18:08:35 2016"
Size:   2.10MB
Frequency:   customer 
HANA Backup ID:   
----------------------------------------------------------
Snapshot:   customer2.2016-09-20_1532.0
Create Time:   "Tue Sep 20 19:36:21 2016"
Size:   2.37MB
Frequency:   customer2
HANA Backup ID:   
```



### <a name="reducing-the-number-of-snapshots-on-a-server"></a>Zmniejszenie liczby migawek na serwerze

Jak wyjaśniono wcześniej, można zmniejszyć liczbę niektórych etykiet migawek, które są przechowywane. Ostatnie dwa parametry polecenia, aby zainicjować migawki są etykiety i liczby migawek, które chcesz zachować.

```
./azure_hana_backup.pl hana dailyhana 15min 28
```

W poprzednim przykładzie, etykieta migawki jest **dailyhana** i liczby migawek przy użyciu tej etykiety, które ma zostać zachowana **28**. Jak odpowiedzieć użycie miejsca na dysku, można zmniejszyć liczbę migawek przechowywanych. Prosty sposób, aby zmniejszyć liczbę migawek do 15, na przykład, jest uruchomienie skryptu z ostatniego parametru równa **15**:

```
./azure_hana_backup.pl hana dailyhana 15min 15
```

Po uruchomieniu skryptu z tym ustawieniem liczby migawek, w tym nową migawkę magazynu to 15. 15 ostatnich migawki są przechowywane i 15 migawek starsze są usuwane.

 >[!NOTE]
 > Ten skrypt powoduje zmniejszenie liczby migawek, tylko wtedy, gdy istnieje więcej niż 1 godzinę stare migawki. Skrypt nie powoduje usunięcia migawek, które są mniej niż 1 godzinę. Ograniczenia te są powiązane funkcje odzyskiwania po awarii opcjonalne, które są oferowane.

Jeśli nie chcesz już przechowywać zestawu migawek przy użyciu kopii zapasowej etykiety **hanadaily** w przykładach składni można wykonać skryptu za pomocą **0** jako liczba przechowywania. Następnie zostaną usunięte wszystkie migawki dopasowania tej etykiety. Jednak usunięcie wszystkich migawek może mieć wpływ na możliwości dużych wystąpień HANA działania funkcji odzyskiwania po awarii.

Drugą opcję, aby usunąć migawki określonej jest użycie skryptu `azure_hana_snapshot_delete.pl`. Ten skrypt jest przeznaczony do usunięcia migawki lub zestawu migawek, albo za pomocą Identyfikatora kopii zapasowej w HANA jak ustalono, HANA Studio lub za pomocą sama nazwa migawki. Obecnie identyfikator kopii zapasowej jest powiązany tylko z migawek utworzonych dla **hana** typ migawki. Tworzenie migawki kopii zapasowych typu **dzienniki** i **rozruchu** nie wykonuj migawki platformy SAP HANA i dlatego nie ma żadnych identyfikator kopii zapasowej do znalezienia dla tych migawek. Jeśli wprowadzona nazwa migawki, wyszukuje wszystkie migawki na różnych woluminach, które pasuje do nazwy wprowadzone migawki. 

Wywołanie skryptu należy określić identyfikator SID wystąpienie oprogramowania HANA przy użyciu składni wywołanie skryptu:

```
./azure_hana_snapshot_delete.pl <SID>

```

Uruchom skrypt jako użytkownik **głównego**.

Jeśli wybierzesz migawki, możesz usunąć każdej migawki indywidualnie. Najpierw podaj wolumin, który zawiera migawki, a następnie podaj nazwę migawki. Jeśli istnieje w tym woluminie migawki jest więcej niż 1 godzinę, został usunięty. Nazwy woluminów i nazwy migawki można znaleźć, wykonując `azure_hana_snapshot_details` skryptu. 

>[!IMPORTANT]
>W przypadku danych, który istnieje tylko w migawce usuwasz, po usunięciu migawki, że dane są trwale utracone.

  
## <a name="file-level-restore-from-a-storage-snapshot"></a>Poziom pliku przywracania z migawki magazynu
Dla typów migawki **hana** i **dzienniki**, są dostępne migawki bezpośrednio na woluminach **.snapshot** katalogu. Brak podkatalogu dla każdego z migawki. Możesz skopiować każdego pliku w stanie, w jakim były one na punkcie migawki z tym podkatalogu w strukturze katalogu. W bieżącej wersji skryptu jest **nie** przywrócić skryptu do przywrócenia migawki jako samoobsługi (chociaż może można wykonać przywracanie z migawki, ponieważ element odzyskiwania po awarii samoobsługi skrypty po stronie odzyskiwania po awarii podczas trybu failover). Musisz skontaktować się z zespołem firmy Microsoft operations, otwierając żądanie obsługi, można przywrócić żądanej migawki z istniejących migawek dostępne.

>[!NOTE]
>Pojedynczy plik przywracania nie działa w przypadku migawki rozruchu niezależna od typu jednostki dużych wystąpień HANA jednostki LUN. **.Snapshot** katalogu nie jest widoczny w rozruchu jednostki LUN. 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>Odzyskaj do najnowszej migawki HANA

W scenariuszu produkcji w dół proces odzyskiwania z migawki magazynu może być inicjowane jako zdarzenia klienta, pomocy technicznej firmy Microsoft Azure. Jeśli dane zostały usunięte w systemie produkcyjnym i jest jedynym sposobem, aby go pobrać, można przywrócić produkcyjną bazę danych, jest kwestią wysoka pilność.

W innej sytuacji w momencie odzyskiwania może być niski pilność i planowane-dniowym wyprzedzeniem. Możesz zaplanować to odzyskiwanie z platformą SAP HANA na platformie Azure, zamiast wywoływania flagi o wysokim priorytecie. Na przykład może być planowane jest uaktualnienie oprogramowania SAP, stosując nowego pakietu rozszerzenia. Następnie należy przywrócić do migawki, który reprezentuje stan przed wykonaniem uaktualnienia pakietu rozszerzenia.

Przed wysłaniem żądania, które trzeba przygotować. Oprogramowanie SAP HANA na zespół platformy Azure można obsłużyć żądania i podaj przywracanych woluminów. Potem możesz przywrócić bazy danych HANA, oparte na migawki. 

Poniżej pokazano, jak przygotować się do żądania:

>[!NOTE]
>Interfejs użytkownika może się różnić od poniższych zrzutach ekranu, w zależności od używanej wersji oprogramowania SAP HANA.

1. Zdecyduj, które migawek do przywrócenia. Tylko wolumin hana/danych zostanie przywrócony, chyba że poinstruować w inny sposób. 

1. Zamknij wystąpienie oprogramowania HANA.

   ![Zamknij wystąpienie oprogramowania HANA](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. Odinstaluj woluminy danych w każdym węźle bazy danych HANA. Woluminy danych nadal są zainstalowane w systemie operacyjnym, przywracanie migawki nie powiedzie się.
   ![Odinstaluj woluminy danych w każdym węźle bazy danych HANA](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. Otwórz żądanie pomocy technicznej platformy Azure i zawierają instrukcje informujące o przywrócenie określoną migawkę.

   - Podczas przywracania: SAP HANA na platformie Azure może poprosić Cię o weź udział w konferencji, do zapewnienia koordynacji, weryfikacji i potwierdzenie, że migawki magazynu poprawną został przywrócony. 

   - Po przywróceniu: SAP HANA w usłudze Azure Service powiadamia o przywróceniu z migawki magazynu.

1. Po ukończeniu procesu przywracania, należy ponownie zainstalować wszystkie woluminy danych.

   ![Zainstaluj wszystkie woluminy danych](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)

1. Wybierz opcje odzyskiwania w systemie SAP HANA Studio, jeśli nie automatycznie pojawiania się po ponownym nawiązaniu połączenia bazy danych HANA za pośrednictwem programu SAP HANA Studio. Przywracanie do ostatniego migawki HANA można znaleźć w poniższym przykładzie. Migawki magazynu osadza jedną migawkę platformy HANA. W przypadku przywracania do najnowszej migawki magazynu powinno być najnowszej migawki platformy HANA. (Jeśli można przywrócić starsze migawek magazynu, musisz zlokalizować migawki HANA na podstawie czasu, który migawki magazynu.)

   ![Wybierz opcje odzyskiwania w obrębie SAP HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image10-recover-options-a.png)

1. Wybierz **odzyskać bazę danych do określonych danych migawki kopii zapasowej lub magazynu**.

   ![W oknie Określanie typu odzyskiwania](./media/hana-overview-high-availability-disaster-recovery/image11-recover-options-b.png)

1. Wybierz **Określ kopia zapasowa, bez katalogu**.

   ![W oknie określ lokalizację kopii zapasowej](./media/hana-overview-high-availability-disaster-recovery/image12-recover-options-c.png)

1. W **typ docelowy** listy wybierz **migawki**.

   ![Określ kopii zapasowej do okna odzyskiwanie](./media/hana-overview-high-availability-disaster-recovery/image13-recover-options-d.png)

1. Wybierz **Zakończ** można uruchomić procesu odzyskiwania.

    ![Wybierz opcję "Zakończ", aby rozpocząć proces odzyskiwania](./media/hana-overview-high-availability-disaster-recovery/image14-recover-options-e.png)

1. Baza danych HANA jest przywrócić i odzyskać z migawką HANA, który znajduje się w tle magazynu.

    ![Baza danych HANA jest przywrócić i odzyskać z migawką HANA](./media/hana-overview-high-availability-disaster-recovery/image15-recover-options-f.png)

### <a name="recover-to-the-most-recent-state"></a>Odzyskaj do najnowszych stanu

Następujący proces przywraca migawki HANA, który znajduje się w tle magazynu. Go następnie przywraca kopie zapasowe dziennika transakcji najnowszych stanu bazy danych przed przywróceniem z migawki magazynu.

>[!IMPORTANT]
>Przed kontynuowaniem upewnij się, że mają pełny i ciągłym łańcuch kopie zapasowe dziennika transakcji. Bez tych kopii zapasowych nie można przywrócić bieżącego stanu bazy danych.

1. Wykonaj kroki 1 – 6 w odzyskiwanie do najnowszej migawki platformy HANA.

1. Wybierz **odzyskać bazę danych do stanu najnowszych**.

   ![Wybierz pozycję "Odzyskiwanie bazy danych do stanu najnowszych"](./media/hana-overview-high-availability-disaster-recovery/image16-recover-database-a.png)

1. Określ lokalizację najnowsze kopie zapasowe dziennika platformy HANA. Lokalizacja musi zawierać wszystkie HANA kopie zapasowe dziennika transakcji z migawki HANA do najnowszych stanu.

   ![Określ lokalizację najnowsze kopie zapasowe dziennika HANA](./media/hana-overview-high-availability-disaster-recovery/image17-recover-database-b.png)

1. Wybierz kopię zapasową jako podstawowy, z którego można odzyskać bazy danych. W tym przykładzie migawki HANA na zrzucie ekranu jest migawką HANA, która została uwzględniona w migawce magazynu. 

   ![Wybierz kopię zapasową jako podstawowy, z którego można odzyskać bazy danych](./media/hana-overview-high-availability-disaster-recovery/image18-recover-database-c.png)

1. Wyczyść **tworzenia kopii zapasowych Użyj Delta** pole wyboru, jeśli nie istnieją różnice między czasem migawki HANA i najnowszych stanu.

   ![Usuń zaznaczenie pola wyboru "Użyj różnicowych kopii zapasowych", jeśli istnieje nie różnic](./media/hana-overview-high-availability-disaster-recovery/image19-recover-database-d.png)

1. Na ekranie Podsumowanie należy wybrać **Zakończ** rozpocząć wykonywanie procedury przywracania.

   ![Kliknij przycisk "Zakończ", na ekranie Podsumowanie](./media/hana-overview-high-availability-disaster-recovery/image20-recover-database-e.png)

### <a name="recover-to-another-point-in-time"></a>Odzyskaj do innego punktu w czasie
Aby odzyskać do punktu w czasie między migawki HANA (zawarte w tle magazynu) oraz jedną, która jest nowsza niż HANA migawkę punktu w czasie odzyskiwania, należy wykonać następujące czynności:

1. Upewnij się, że wszystkie kopie zapasowe dziennika transakcji z migawki HANA czas, który chcesz odzyskać do.
1. Rozpocznij procedurę opisaną w odzyskiwanie, najbardziej aktualną stanu.
1. W kroku 2 procedury w **Określanie typu odzyskiwania** wybierz **odzyskać bazę danych do następujących punktu w czasie**, a następnie określ punkt w czasie. 
1. Wykonaj kroki od 3 do 6.

## <a name="monitor-the-execution-of-snapshots"></a>Monitorowanie wykonania migawki

Jak korzystasz z migawek magazynu dużych wystąpień HANA, należy monitorować wykonanie te migawki. Skrypt, który wykonuje migawkę pamięci masowej zapisuje dane wyjściowe do pliku i zapisuje go do tej samej lokalizacji co skryptów języka Perl. Osobny plik jest przeznaczony dla każdej migawki magazynu. Dane wyjściowe każdego pliku pokazuje różnych faz, wykonuje skrypt migawki:

1. Umożliwia znalezienie woluminów, które należy utworzyć migawkę.
1. Umożliwia znalezienie migawek pobrane z tych woluminów.
1. Usuwa ostatecznej istniejącej migawki do dopasowania liczby migawek, wskazana.
1. Tworzy migawkę platformy SAP HANA.
1. Tworzy migawki magazynu dla woluminów.
1. Usuwa migawkę platformy SAP HANA.
1. Zmienia nazwę najnowszej migawki na **.0**.

Najważniejszy element cab skryptu identyfikowane jest ta część:
```
**********************Creating HANA snapshot**********************
Creating the HANA snapshot with command: "./hdbsql -n localhost -i 01 -U SCADMIN01 "backup data create snapshot"" ...
HANA snapshot created successfully.
**********************Creating Storage snapshot**********************
Taking snapshot hourly.recent for hana_data_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_log_backup_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_log_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_shared_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for sapmnt_lhanad01_t020_vol ...
Snapshot created successfully.
**********************Deleting HANA snapshot**********************
Deleting the HANA snapshot with command: "./hdbsql -n localhost -i 01 -U SCADMIN01 "backup data drop snapshot"" ...
HANA snapshot deletion successfully.
```
W tym przykładzie widać, jak skrypt rekordy utworzenie migawki platformy HANA. W przypadku skalowania w poziomie ten proces jest inicjowany w węźle głównym. Węzeł główny inicjuje synchroniczne tworzenie migawek platformy SAP HANA na każdym z węzłów procesu roboczego. Następnie wykonywana jest migawka magazynu. Po pomyślnym wykonaniu migawek magazynu oprogramowania HANA migawki jest usuwany. Usuwanie migawki HANA jest inicjowane z węzła głównego.


## <a name="next-steps"></a>Kolejne kroki
- Zobacz [zasady odzyskiwania po awarii i przygotowania](hana-concept-preparation.md).
