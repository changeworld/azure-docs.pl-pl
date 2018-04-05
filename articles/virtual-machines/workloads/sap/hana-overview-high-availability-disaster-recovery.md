---
title: Wysoka dostępność i odzyskiwanie po awarii programu SAP HANA na platformie Azure (wystąpienia duże) | Dokumentacja firmy Microsoft
description: Ustal wysokiej dostępności i planu odzyskiwania po awarii programu SAP HANA na platformie Azure (wystąpienia duże)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: timlt
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/01/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6c939e0fb59c7fce2c1c34aca1b77bd0b8cec0c5
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>SAP HANA dużych wystąpień wysokiej dostępności i odzyskiwania po awarii na platformie Azure 

>[!IMPORTANT]
>Ta dokumentacja nie zastępuje nie SAP HANA administracyjnej dokumentacji lub notatki SAP. Oczekuje się, że czytelnik ma pełny zrozumienia i doświadczenia w SAP HANA administracji i obsługi, szczególnie w przypadku tematów kopii zapasowych, przywracania, wysokiej dostępności i odzyskiwania po awarii. W tej dokumentacji zrzuty ekranu z SAP HANA Studio są wyświetlane. Zawartość, struktury i rodzaj ekrany SAP narzędzi administracyjnych i narzędzi, same mogą ulec zmianie od SAP HANA wersji do wersji.

Należy pamiętać, że wykonuje kroki i procesy podjęte w danym środowisku i HANA wersji i wydań. Niektóre procesy opisane w tej dokumentacji są uproszczone lepsze zrozumienie ogólnych i nie są przeznaczone do użycia dla operacji ostatecznego podręczników jako szczegółowy opis kroków. Aby utworzyć operację podręczników dla konfiguracji, należy przetestować i wykonywania procesów i zarządzania dokumentami procesów związanych z określonej konfiguracji. 


Wysokiej dostępności i odzyskiwania awaryjnego (DR) są niezwykle istotne aspekty uruchomionych na serwerze Azure (wystąpienia duże) Twojej krytycznym SAP HANA. Jest ważne pracować z SAP, system integrator lub Microsoft prawidłowo projektowania i implementacji prawo wysokiej dostępności i strategii odzyskiwania danych. Należy również wziąć pod uwagę cel punktu odzyskiwania (RPO) i celu czasu odzyskiwania, które są specyficzne dla danego środowiska.

Firma Microsoft obsługuje niektóre funkcje wysokiej dostępności SAP HANA wystąpieniami dużych HANA. Te możliwości to m.in.:

- **Replikacja magazynu**: możliwość systemu magazynu replikacji wszystkich danych do innego wystąpienia dużych HANA sygnatury w innym regionie Azure. SAP HANA działa niezależnie od tej metody. Ta funkcja jest dostępna w przypadku dużych wystąpień HANA odzyskiwania po awarii domyślnego mechanizmu.
- **Replikacji systemu HANA**: [replikacji wszystkich danych SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html) do oddzielnego systemu SAP HANA. W celu czasu odzyskiwania jest zminimalizowany do replikacji danych w regularnych odstępach czasu. SAP HANA obsługuje asynchroniczne, synchronicznej tryby w pamięci i synchroniczne. Synchroniczne tryb jest używany tylko dla systemów SAP HANA, które znajdują się w tym samym centrum danych lub mniej niż 100 km od siebie. Bieżący projekt sygnatury wystąpienia dużych HANA HANA replikacji systemu może służyć do wysokiej dostępności w ramach tylko jeden region. HANA replikacji systemu wymaga zwrotny serwer proxy innych firm lub składnik routingu dla konfiguracji odzyskiwania po awarii do innego regionu systemu Azure. 
- **Host pracy awaryjnej automatycznie**: rozwiązania lokalnego odzyskiwania błędów dla SAP HANA, który stanowi alternatywę dla replikacji systemu HANA. Jeśli węzeł główny jest niedostępny, skonfiguruj co najmniej jeden węzeł SAP HANA rezerwy w trybie skalowalnego w poziomie i SAP HANA automatycznie przełącza się do wstrzymania węzła.

SAP HANA na platformie Azure (wystąpienia duże) jest oferowana w dwóch regionach platformy Azure w trzech obszarach geograficznymi (USA, Australia i Europy). Dwóch regionach geograficznymi obszarze obsługujące sygnatury HANA dużych wystąpienia są podłączone do obwodów oddzielnych dedykowanych sieci. Są one używane do replikacji migawek magazynu do zapewnienia metody odzyskiwania po awarii. Replikacja nie zostanie nawiązane domyślnie, ale jest skonfigurowane dla klientów, którzy kolejność funkcji odzyskiwania po awarii. Replikacja magazynu jest zależna od użycia magazynu migawek dla wystąpień dużych HANA. Nie jest możliwe jako region odzyskiwania po awarii, który znajduje się w różnych obszarze geograficznymi wybrać region platformy Azure. 

W poniższej tabeli przedstawiono kombinacje i aktualnie obsługiwanych wysokiej dostępności i odzyskiwaniem po awarii metody odzyskiwania:

| Obsługiwany w wystąpieniach dużych HANA scenariusz | Opcja wysokiej dostępności | Opcje odzyskiwania po awarii | Komentarze |
| --- | --- | --- | --- |
| Jeden węzeł | Nie jest dostępna. | Dedykowany Instalator odzyskiwania po awarii.<br /> Instalator odzyskiwania po awarii Multipurpose. | |
| Host trybu failover automatycznego: N + m<br /> w tym 1 + 1 | Możliwe ze stanem gotowości, biorąc aktywnej roli.<br /> HANA steruje przełącznika roli. | Dedykowany Instalator odzyskiwania po awarii.<br /> Instalator odzyskiwania po awarii Multipurpose.<br /> Synchronizacja DR przy użyciu replikacji magazynu. | Zestawy HANA są dołączone do wszystkich węzłów (n + m).<br /> Lokacja DR musi mieć taką samą liczbę węzłów. |
| Replikacji systemu HANA | Możliwe z ustawienia podstawowy lub pomocniczy.<br /> Pomocniczy przenosi podstawową rolą w przypadku trybu failover.<br /> Replikacji systemu HANA i systemu operacyjnego kontroli trybu failover. | Dedykowany Instalator odzyskiwania po awarii.<br /> Instalator odzyskiwania po awarii Multipurpose.<br /> Synchronizacja DR przy użyciu replikacji magazynu.<br /> DR za pomocą replikacji systemu HANA nie jest jeszcze możliwe bez składników innych firm. | Osobny zestaw woluminów dysków dołączonych do każdego węzła.<br /> Replikowane tylko dysku woluminy repliki pomocniczej w lokacji produkcyjnej do lokalizacji odzyskiwania po awarii.<br /> Jeden zestaw woluminów jest wymagany w lokacji odzyskiwania po awarii. | 

Dedykowany Instalator odzyskiwania po awarii jest których HANA dużych wystąpienia jednostki w lokacji odzyskiwania po awarii nie jest używany do uruchamiania innych obciążeń lub system nieprodukcyjnych. Jednostka jest w stanie pasywnym i jest wdrożona tylko wtedy, gdy jest wykonywana po awarii trybu failover. Jednak ta konfiguracja nie jest preferowany wybór dla wielu klientów.

> [!NOTE]
> [Wdrożenia SAP HANA MCOD](https://launchpad.support.sap.com/#/notes/1681092) (wielu wystąpień HANA na jedną jednostkę) nakładanie pracy scenariuszy wysokiej dostępności i odzyskiwania po awarii metody wymienione w tabeli. Wyjątek jest użycie replikacji systemu HANA klastra automatycznej pracy awaryjnej rozrusznik w oparciu o. Wówczas obsługuje tylko jedno wystąpienie HANA na jednostkę. Aby uzyskać [SAP HANA MDC](https://launchpad.support.sap.com/#/notes/2096000) wdrożenia, tylko podstawie magazynu wysokiej dostępności i odzyskiwania po awarii metody działają w przypadku wdrażania więcej niż jednego dzierżawcy. Z jednej dzierżawy wdrożone wszystkie metody wymienione są prawidłowe.  

Uniwersalne Instalator odzyskiwania po awarii jest jednostka HANA dużych wystąpienia w lokacji odzyskiwania po awarii, w którym odbywa się obciążenie nieprodukcyjnych. W przypadku awarii, zamknij system nieprodukcyjnych zainstalować zestawy replikowany magazyn woluminów (dodatkowe), a następnie uruchom wystąpienia HANA produkcji. Większość klientów korzystających z funkcji odzyskiwania po awarii wystąpienia dużych HANA używać tej konfiguracji. 


Więcej informacji na temat SAP HANA wysokiej dostępności można znaleźć w następujących artykułach SAP: 

- [Oficjalny dokument programu SAP HANA wysokiej dostępności](http://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [Przewodnik administracji programu SAP HANA](http://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [SAP HANA Academy wideo na replikacji systemu SAP HANA](http://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [Obsługa Uwaga #1999880 — często zadawane pytania dotyczące replikacji systemu SAP HANA SAP](https://bcs.wdf.sap.corp/sap/support/notes/1999880)
- [SAP Uwaga Obsługa #2165547 — SAP HANA wstecz zapasowej i przywracania środowisku SAP HANA System replikacji](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [SAP Uwaga Obsługa #1984882 — za pomocą replikacji systemu SAP HANA wymiany sprzętu przestojów minimalna/Zero](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="network-considerations-for-disaster-recovery-with-hana-large-instances"></a>Zagadnienia dotyczące sieci dla odzyskiwania po awarii z wystąpieniami dużych HANA

Aby skorzystać z funkcji odzyskiwania po awarii HANA dużych wystąpień, musisz projektowania łączności sieciowej dwóch regionach platformy Azure. Należy połączenia obwodu Azure ExpressRoute z lokalnymi w główny region platformy Azure i inne połączenie obwodem z lokalnej do regionu odzyskiwania po awarii. To jest miara dotyczy sytuacji, w której występuje problem w regionie Azure, w tym miejscu Microsoft Enterprise krawędzi routera (MSEE).

Jako druga Metryka używana można połączyć wszystkie sieci wirtualne Azure łączący się SAP HANA na platformie Azure (duże wystąpień) w jednym regionie z obwodem usługi ExpressRoute łączącego HANA dużych wystąpień w innym regionie. Z tym *między połączyć*, usług działających na platformie Azure sieci wirtualnej w regionie 1 może nawiązać połączenie HANA dużych wystąpienia jednostek w regionie 2 i odwrotnie. To jest miara dotyczy przypadku tylko jeden z lokalizacji MSEE łączący się do lokalizacji lokalnej z platformy Azure, w którym przejdzie do trybu offline.

Poniższa ilustracja przedstawia konfigurację odporne w przypadku odzyskiwania po awarii:

![Konfiguracją optymalną dla odzyskiwania po awarii](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)



## <a name="other-requirements-with-hana-large-instances-storage-replication-for-disaster-recovery"></a>Inne wymagania z replikacji magazynu wystąpień dużych HANA odzyskiwania po awarii

Oprócz powyższych wymagań dla Instalator odzyskiwania po awarii z wystąpieniami dużych HANA należy:

- Kolejność SAP HANA na jednostki SKU Azure (duże wystąpień) w taki sam rozmiar jak produkcyjnego jednostki SKU i wdrażać je w regionie odzyskiwania po awarii. W bieżącym wdrożenia klienta te wystąpienia są używane do uruchomienia wystąpień HANA nieprodukcyjnych. Te konfiguracje są określane jako *multipurpose konfiguracje DR*.   
- Kolejność dodatkowego magazynu w lokacji odzyskiwania po awarii dla każdego z programu SAP HANA na jednostki SKU Azure (wystąpienia duże), które mają zostać odzyskane w lokacji odzyskiwania po awarii. Kupowanie dodatkowego miejsca do magazynowania pozwala przydzielić woluminów magazynu. Można alokować woluminy, które są elementem docelowym replikacji magazynu z produkcyjnego region platformy Azure do odzyskiwania po awarii region platformy Azure.

 

## <a name="backup-and-restore"></a>Tworzenie kopii zapasowej i przywracanie

Jednym z najważniejszych aspektów, które operacyjnej bazy danych jest chronić je przed zdarzeń losowych. Przyczynę tych zdarzeń może być dowolna z klęski żywiołowej błędy proste użytkownika.

Tworzenie kopii zapasowej bazy danych, z możliwością przywrócenia dowolnego punktu w czasie (takich jak przed ktoś usunął kluczowe dane), umożliwia przywrócenie do stanu, który jest możliwie najbliżej sposób ich przerw w działaniu.

Dwa typy kopii zapasowych należy wykonać w celu uzyskania najlepszych wyników:

- Kopie zapasowe bazy danych: pełne, przyrostowe lub różnicowej kopii zapasowych
- Kopie zapasowe dziennika transakcji

Oprócz bazy danych pełne kopie zapasowe wykonywane na poziomie aplikacji można wykonywać kopie zapasowe z magazynu migawek. Magazyn migawek nie zastępują kopie zapasowe dziennika transakcji. Kopie zapasowe dziennika transakcji pozostają ważne, aby przywrócić bazę danych do niektórych punktu w czasie lub aby opróżnić dziennik z już zatwierdzone transakcje. Jednak pamięci masowej migawki można przyspieszyć odzyskiwania zapewniając szybko obraz przewijaniem do bazy danych. 

SAP HANA na platformie Azure (wystąpienia duże) oferuje dwie opcje tworzenia kopii zapasowej i przywracania:

- Zrób (DIY). Po obliczeniu upewnij się, że istnieje wystarczająca ilość miejsca na dysku, należy wykonać pełnej bazy danych i tworzenie kopii zapasowych dzienników przy użyciu jednej z następujących metod tworzenia kopii zapasowej dysku. Można tworzyć kopie zapasowe albo bezpośrednio do woluminów do jednostki HANA wystąpienia dużych lub do sieci udziałów plików (NFS), które są skonfigurowane w maszynie wirtualnej platformy Azure (VM). W drugim przypadku klienci skonfigurowanie maszyny Wirtualnej systemu Linux na platformie Azure, Dołącz magazynu Azure do maszyny Wirtualnej i udostępnianie magazynu przez serwer systemu plików NFS skonfigurowanych na tej maszynie Wirtualnej. Wykonanie kopii zapasowej dla woluminów, które bezpośrednio dołączyć do wystąpienia dużych HANA jednostki, należy skopiować kopie zapasowe do konta magazynu platformy Azure (po skonfigurowaniu maszyny Wirtualnej platformy Azure, które eksportuje udziałów NFS, które są oparte na usłudze Azure Storage). Można również użyć magazynu kopii zapasowych systemu Azure albo chłodni platformy Azure. 

   Innym rozwiązaniem jest przechowywania kopii zapasowych, po skopiowaniu do konta magazynu platformy Azure za pomocą narzędzia ochrony danych innych firm. DIY opcję tworzenia kopii zapasowej może być także konieczne dla danych, które należy przechowywać przez dłuższy czas, zgodności i inspekcji. We wszystkich przypadkach kopie zapasowe są kopiowane do udziałów NFS reprezentowany przez maszynę Wirtualną i Magazyn Azure.

- Infrastruktura kopii zapasowej i przywrócenia jej funkcjonalności. Można również użyć kopii zapasowej i przywracania, który obejmuje podstawowej infrastruktury SAP HANA na platformie Azure (wystąpienia duże). Ta opcja spełnia potrzeby kopii zapasowych i przywracania szybkie. Pozostałej części tej sekcji dotyczy funkcji kopii zapasowej i przywracania jest oferowany z wystąpieniami dużych HANA. W tej sekcji omówiono również relacji kopii zapasowej i przywracania musi awarią funkcji odzyskiwania oferowane przez HANA dużych wystąpień.

>   [!NOTE]
>   Technologia migawki, która jest używana przez podstawowej infrastruktury wystąpień dużych HANA ma zależność na migawki SAP HANA. W tym momencie SAP HANA migawki nie działają w połączeniu z wieloma dzierżawcami kontenery wielodostępnym bazy danych SAP HANA. Jeśli tylko jeden dzierżawy jest wdrażana, działają SAP HANA migawki i można użyć tej metody.

### <a name="using-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Przy użyciu magazynu migawek SAP HANA na platformie Azure (wystąpienia duże)

Infrastruktury magazynu bazowy SAP HANA na platformie Azure (wystąpienia duże) obsługuje magazynu migawek woluminów. Kopii zapasowej oraz Przywracanie woluminów jest obsługiwane przez następujące kwestie:

- Zamiast pełnej bazy danych kopii zapasowych wykonywane na częste migawek woluminu magazynu.
- Gdy wyzwalania migawki przez /hana/data i /hana/shared (w tym /usr/sap) woluminów, technologii migawki inicjuje SAP HANA migawki przed rozpoczęciem wykonywania migawki magazynu. Ta migawka SAP HANA jest punkt instalacji operacje przywracania dziennika ostatecznego po odzyskaniu migawki magazynu.
- Po migawki magazynu zostało wykonane pomyślnie, jest usuwane migawki SAP HANA.
- Kopie zapasowe dziennika transakcji podjęto często i są przechowywane w woluminie /hana/logbackups lub na platformie Azure. Możesz wyzwolić woluminu /hana/logbackups, który zawiera kopie zapasowe dziennika transakcji do tworzenia migawki oddzielnie. W takim przypadku nie trzeba wykonać migawki HANA.
- Pewnym należy przywrócić bazę danych, w czasie, podczas przywracania zarządzania usługą Azure migawkę magazynu żądanie tę techniczną firmy Microsoft Azure (w przypadku awarii produkcyjnym) lub SAP HANA. Przykładem jest planowane Przywracanie systemu piaskownicy do stanu pierwotnego.
- Migawki SAP HANA, który znajduje się w pamięci masowej migawki jest punkt przesunięcia stosowania kopie zapasowe dziennika transakcji, które zostały wykonane i przechowywane po migawki magazynu.
- Te kopie zapasowe dziennika transakcji są podjąć w celu przywrócenia bazy danych do niektórych punktu w czasie.

Można wykonać migawki magazynu przeznaczonych dla trzech klas woluminów:

- Migawki połączonych za pośrednictwem/hana/danych i /hana/shared (w tym/usr/sap). Ta migawka wymaga utworzenia migawki SAP HANA jako przygotowanie do przechowywania migawki. Migawki SAP HANA upewnia się, że baza danych jest spójna z punktu widzenia magazynu. I że dla przywracania przetwarzania czyli punktu można ustawić w górę na.
- Oddzielne migawkę za pośrednictwem/hana/logbackups.
- Partycja systemu operacyjnego.


### <a name="storage-snapshot-considerations"></a>Zagadnienia dotyczące magazynu migawki

>[!NOTE]
>Magazyn migawek wykorzystywać miejsce do magazynowania przydzielone HANA dużych wystąpienia jednostki. Należy wziąć pod uwagę następujące aspekty planowania migawek magazynu i magazynu liczbę migawek zachowywanych. 

Określone mechanika magazynu migawek dla SAP HANA na platformie Azure (wystąpienia duże) obejmują:

- Migawki określonego magazynu (w punkcie w czasie, gdy jest zajęty) zużywa niewiele pamięci masowej.
- Jako dane zmiany zawartości i zawartości w danych SAP HANA zmienić plików na woluminie magazynu migawki musi przechowywać oryginalnej zawartości bloku, jak również zmian danych.
- W związku z tym migawki pamięci masowej zwiększa rozmiar. Już istnieje migawki, tym większy migawki magazynu staje się.
- Dalszych zmian dokonanych w woluminie bazy danych SAP HANA okresu istnienia pamięci masowej migawki, większy wykorzystanie miejsca w pamięci masowej migawki.

SAP HANA na platformie Azure (wystąpienia duże) jest dostarczany z stałym rozmiarów woluminów danych i dziennika SAP HANA. Wykonywanie migawek woluminy eats do ilość miejsca na woluminie. Należy określić, kiedy należy zaplanować magazynu migawek. Należy również monitorować wykorzystanie miejsca woluminów magazynu, a także zarządzać liczby migawek, które są przechowywane. Migawki magazynu można wyłączyć, gdy albo importowane masy danych lub wykonywać inne istotne zmiany w bazie danych HANA. 


Poniższe sekcje zawierają informacje dotyczące wykonywania migawek, w tym ogólne zalecenia:

- Chociaż sprzętu może wytrzymać 255 migawek dla każdego woluminu, nadal ma być również pod ten numer.
- Przed wykonaniem migawki pamięci masowej, monitorować i śledzić ilość wolnego miejsca.
- Zmniejszyć liczbę migawek magazynu oparte na wolnego miejsca. Można zmniejszyć liczbę migawek, które są stale lub można rozszerzyć woluminy. Dodatkowe miejsce do magazynowania można zamówić w jednostkach terabajt 1.
- Podczas działania, takie jak przenoszenie danych do programu SAP HANA z narzędzi migracji platform SAP (R3load) lub przywracanie z kopii zapasowej bazy danych SAP HANA Wyłącz migawek magazynu w woluminie /hana/data. 
- Podczas większych reorganizacji SAP HANA tabel Magazyn migawek należy unikać, jeśli to możliwe.
- Migawki magazynu są wstępnie wymagana do wykorzystaniu po awarii możliwości odzyskiwania systemu SAP HANA na platformie Azure (wystąpienia duże).

### <a name="prerequisites-for-using-self-service-storage-snapshots"></a>Wymagania wstępne dotyczące używania samoobsługowego magazynu migawek

Upewnij się, że skryptu migawka została wykonana pomyślnie, upewnij się, że Perl jest zainstalowana w systemie operacyjnym Linux na serwerze HANA dużych wystąpień. Perl jest wstępnie zainstalowane na urządzenia HANA dużych wystąpienia. Aby sprawdzić wersję języka Perl, użyj następującego polecenia:

`perl -v`

![Klucz publiczny jest kopiowany przez uruchomienie tego polecenia](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


### <a name="set-up-storage-snapshots"></a>Ustawienia magazynu migawek

Aby skonfigurować Magazyn migawek wystąpieniami dużych HANA, wykonaj następujące kroki:
1. Upewnij się że Perl został zainstalowany w systemie operacyjnym Linux na serwerze HANA dużych wystąpień.
2. Modyfikowanie/etc/ssh/ssh\_konfiguracji, aby dodać wiersz _Mac hmac-sha1_.
3. Tworzenie konta użytkownika kopii zapasowej SAP HANA w węźle głównym dla każdego wystąpienia SAP HANA, na którym jest uruchomiony, jeśli ma to zastosowanie.
4. Zainstaluj klienta SAP HANA HDB na wszystkich serwerach wystąpień dużych SAP HANA.
5. Na pierwszym serwerze SAP HANA dużych wystąpień poszczególnych regionach Utwórz klucz publiczny uzyskiwać dostęp do podstawowej infrastruktury magazynu, który kontroluje Tworzenie migawki.
6. Skopiuj skryptów i pliku konfiguracji z [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts) do lokalizacji **hdbsql** w instalacji programu SAP HANA.
7. Modyfikowanie *HANABackupDetails.txt* pliku niezbędne dla specyfikacji odpowiedniego klienta.

### <a name="consideration-for-mcod-scenarios"></a>Brany pod uwagę w przypadku scenariuszy MCOD
Jeśli korzystasz z [scenariusza MCOD](https://launchpad.support.sap.com/#/notes/1681092) z wielu wystąpień SAP HANA na jedną jednostkę HANA dużych wystąpienia, masz przechowywania woluminy udostępnione dla wszystkich wystąpień SAP HANA. W bieżącej wersji automatyzacji samoobsługi migawki nie można zainicjować oddzielne migawek w systemie wystąpienia każdej HANA identyfikator. Funkcje zapewnia sprawdza, czy zarejestrowanych wystąpień SAP HANA serwera w pliku konfiguracji (patrz w dalszej części tego artykułu) i wykonuje migawkę jednoczesnych woluminów wszystkich wystąpień, które są zarejestrowane w jednostce.
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>Krok 1: Instalacja klienta SAP HANA HDB

System operacyjny Linux systemie SAP HANA na platformie Azure (wystąpienia duże) zawiera foldery i skrypty wymagane do wykonania migawki magazynu SAP HANA na potrzeby odzyskiwania po awarii i. Sprawdź, czy nowsze wersje w [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts). Najnowszych wersji skryptów jest 3.x. Różne skrypty mogą mieć różne wersje pomocnicze w ramach tej samej wersji głównej.

>[!IMPORTANT]
>Podczas przenoszenia z wersji 2.1 do wersji 3.0 skryptów, należy pamiętać, zmiana struktury pliku konfiguracji, a niektóre składni. Zobacz objaśnienia w określonej sekcji. 

Jest obowiązek zainstalować klienta SAP HANA HDB w jednostkach wystąpienia dużych HANA podczas instalowania SAP HANA.

### <a name="step-2-change-the-etcsshsshconfig"></a>Krok 2: Zmień/etc/ssh/ssh\_konfiguracji

Zmień `/etc/ssh/ssh_config` przez dodanie _Mac hmac-sha1_ wiersz w sposób pokazany poniżej:
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

Aby włączyć dostęp do interfejsów migawki magazynu dzierżawy HANA dużych wystąpienia, należy ustanowić procedury logowania za pomocą klucza publicznego. Na pierwszym SAP HANA na serwerze Azure (duże wystąpień) w swojej dzierżawy Utwórz klucz publiczny ma być używany do uzyskania dostępu infrastruktury magazynu. Klucz publiczny gwarantuje, że nie jest wymagane hasło do logowania się na interfejsy migawki magazynu. Tworzenie klucza publicznego również oznacza, że nie muszą zachować hasła poświadczeń. W systemie Linux na serwerze programu SAP HANA dużych wystąpień wykonaj następujące polecenie, aby wygenerować klucz publiczny:
```
  ssh-keygen –t dsa –b 1024
```
Nowa lokalizacja jest **_/root/.ssh/id\_dsa.pub**. Nie wpisuj rzeczywiste hasło, w przeciwnym razie należy wprowadzać hasła za każdym razem, logowania. Zamiast tego należy wybrać **Enter** dwa razy, aby usunąć wymaganie "Wprowadź hasło" dla logowania.

Upewnij się, że klucz publiczny został rozwiązany, zgodnie z oczekiwaniami, zmieniając foldery do **/root/.ssh/** , a następnie wykonywanie `ls` polecenia. Jeśli klucz jest obecny, można go skopiować, uruchamiając następujące polecenie:

![Klucz publiczny jest kopiowany przez uruchomienie tego polecenia](./media/hana-overview-high-availability-disaster-recovery/image2-public-key.png)

W tym momencie skontaktuj się z SAP HANA na zarządzania usługą Azure i dostarczać klucza publicznego. Działu obsługi używa klucza publicznego, aby zarejestrować go w podstawowej infrastruktury magazynu, który jest używać dzierżawy HANA dużych wystąpienia.

### <a name="step-4-create-an-sap-hana-user-account"></a>Krok 4: Tworzenie konta użytkownika SAP HANA

Aby zainicjować tworzenie migawek SAP HANA, musisz utworzyć konto użytkownika w SAP HANA, używaną przez skrypty migawki magazynu. W tym celu należy utworzyć konto użytkownika SAP HANA poziomu SAP HANA Studio. W obszarze SYSTEMDB, a nie na bazie identyfikator SID, można utworzyć użytkownika. To konto musi mieć następujące uprawnienia: **administratora kopii zapasowych** i **odczytu katalogu**. W tym przykładzie nazwa użytkownika jest **SCADMIN**. Nazwa konta użytkownika utworzone w HANA Studio jest rozróżniana wielkość liter. Upewnij się wybrać **nr** dla użytkownik musi zmienić hasło na ich następnego logowania.

![Tworzenie użytkownika w HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image3-creating-user.png)

Jeśli używasz MCOD wdrożeń z wieloma wystąpieniami SAP HANA na jedną jednostkę, musisz Powtórz ten krok dla każdego wystąpienia SAP HANA.

### <a name="step-5-authorize-the-sap-hana-user-account"></a>Krok 5: Autoryzowanie SAP HANA konta użytkownika

W tym kroku autoryzować SAP HANA konta użytkownika, które zostały utworzone, dzięki czemu nie trzeba przesyłanie haseł w czasie wykonywania skryptów. Polecenie SAP HANA `hdbuserstore` umożliwia tworzenie SAP HANA klucza użytkownika, który jest przechowywany na co najmniej jeden węzeł SAP HANA. Klucz użytkownika umożliwia użytkownikowi dostęp SAP HANA bez konieczności zarządzania hasłami od w ramach procesu skryptów. Proces skryptów omówione w dalszej części tego artykułu.

>[!IMPORTANT]
>Uruchom następujące polecenie jako `root`. W przeciwnym razie skrypt nie może działać prawidłowo.

Wprowadź `hdbuserstore` polecenia w następujący sposób:

**Z systemem innym niż MDC HANA instalacji**
```
hdbuserstore set <key> <host>:<3[instance]15> <user> <password>
```

**MDC HANA instalacji**
```
hdbuserstore set <key> <host>:<3[instance]13> <user> <password>
```

W poniższym przykładzie użytkownik jest **SCADMIN01**, nazwa hosta jest **lhanad01**, a liczby wystąpień **01**:
```
hdbuserstore set SCADMIN01 lhanad01:30115 <backup username> <password>
```
Jeśli używasz wdrożenia HANA MCOD z wielu wystąpień SAP HANA na jedną jednostkę należy Powtórz krok dla każdego wystąpienia SAP HANA oraz skojarzonego użytkownika kopii zapasowej w jednostce.

Jeśli masz SAP HANA skalowania konfiguracji trzeba zarządzać wszystkie skrypty z jednego serwera. W tym przykładzie klucza SAP HANA **SCADMIN01** musi być zmieniony dla każdego hosta w taki sposób, który zawiera hosta, który jest powiązany z klucza. Zmiana konta kopii zapasowej SAP HANA z liczby wystąpień DB HANA. Klucz musi mieć uprawnienia administratora na hoście, do której jest przypisany, a użytkownik kopii zapasowej dla skalowalnego w poziomie konfiguracji musi mieć prawa dostępu do wszystkich wystąpień SAP HANA. Zakładając, że trzy węzły skalowalnego w poziomie mają nazwy **lhanad01**, **lhanad02**, i **lhanad03**, sekwencja poleceń wygląda następująco:

```
hdbuserstore set SCADMIN01 lhanad01:30115 SCADMIN <password>
hdbuserstore set SCADMIN01 lhanad02:30115 SCADMIN <password>
hdbuserstore set SCADMIN01 lhanad03:30115 SCADMIN <password>
```

### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>Krok 6: Pobierz skrypty migawki, skonfigurować migawki i testowania konfiguracji i łączności

Pobierz najnowszą wersję skryptów z [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts). Skopiuj pobierane skrypty i plik tekstowy do katalogu roboczego **hdbsql**. W przypadku bieżącej instalacji HANA ten katalog jest w następującym formacie: /hana/shared/D01/exe/linuxx86\_64/hdb. 
``` 
azure_hana_backup.pl 
azure_hana_replication_status.pl 
azure_hana_snapshot_details.pl 
azure_hana_snapshot_delete.pl 
testHANAConnection.pl 
testStorageSnapshotConnection.pl 
removeTestStorageSnapshot.pl
azure_hana_dr_failover.pl
azure_hana_dr_failover.pl 
HANABackupCustomerDetails.txt 
``` 

Podczas pracy nad skryptów języka Perl: 

- Nigdy nie należy modyfikować skryptów, jeśli nie zalecił Microsoft Operations.
- Po otrzymaniu monitu, aby zmodyfikować skrypt lub plik parametrów, należy zawsze używać edytora tekstów Linux, takie jak "vi", a nie Edytor Windows takim jak Notatnik. Za pomocą Edytora systemu Windows może spowodować jej uszkodzenie format pliku.
- Zawsze używaj najnowszych skryptów. Najnowszą wersję można pobrać z witryny GitHub.
- Użyj tej samej wersji skryptów w orientacji poziomej.
- Testowanie skryptów i zapoznaj się z wymaganymi parametrami i dane wyjściowe skryptu, aby można było używać ich bezpośrednio w systemie produkcji.
- Nie zmieniaj nazwę punktu instalacji serwera usługi udostępniane przez Microsoft Operations. Te skrypty korzystają z tych punktów instalacji standardowe mają być dostępne do pomyślnego wykonania.


Celem różnych skryptów i plików jest w następujący sposób:

- **Azure\_hana\_backup.pl**: ten skrypt jest zaplanowane przy użyciu narzędzia do planowania Cron Linux można wykonać migawki magazynu danych HANA i udostępnione woluminy, / hana/logbackups woluminu lub systemu operacyjnego.
- **Azure\_hana\_replikacji\_status.pl**: ten skrypt zawiera podstawowe szczegóły dotyczące stanu replikacji z lokacji produkcyjnej do lokacji odzyskiwania po awarii. Monitory skryptu, aby upewnić się, że replikacja odbywa się i zawiera rozmiar elementów które są replikowane. Zawiera również wskazówki, jeśli replikacji trwa zbyt długo lub łącze jest wyłączony.
- **Azure\_hana\_migawki\_details.pl**: ten skrypt zawiera listę podstawowe szczegóły dotyczące wszystkie migawki, na każdym woluminie, które istnieją w danym środowisku. Ten skrypt może działać na podstawowym serwerze lub na jednostkę server w lokalizacji odzyskiwania po awarii. Skrypt zawiera następujące informacje, w rozbiciu na każdy wolumin, który zawiera migawki:
   * Rozmiar całkowitą migawek woluminu
   * Następujące informacje w każdej migawki, w tym woluminie: 
      - Nazwa migawki 
      - Czas utworzenia 
      - Rozmiar migawki
      - Częstotliwość tworzenia migawki
      - Identyfikator kopii zapasowej HANA skojarzony z tej migawki, w razie potrzeby
- **Azure\_hana\_migawki\_delete.pl**: ten skrypt usuwa migawkę magazynu lub zestawu migawek. Można użyć Identyfikatora kopii zapasowej SAP HANA, tak jak w HANA Studio lub nazwy migawki magazynu. Obecnie identyfikator kopii zapasowej jest tylko powiązane migawek utworzonych dla HANA danych/dziennika/udostępnione woluminy. W przeciwnym razie jeśli wprowadzono identyfikator migawki, zmierza wszystkie migawki zgodne identyfikator wprowadzone migawki.  
- **testHANAConnection.pl**: ten skrypt testuje połączenie z wystąpieniem SAP HANA i jest wymagany do ustawienia magazynu migawek.
- **testStorageSnapshotConnection.pl**: ten skrypt ma dwa cele. Najpierw gwarantuje, że jednostki HANA dużych wystąpienia, która uruchamia skrypty ma dostęp do maszyny wirtualnej przypisanej magazynu oraz interfejsu migawki magazynu wystąpień dużych HANA. Drugi służy do utworzenia migawki tymczasowego wystąpienia HANA, które są testowania. Ten skrypt powinien być uruchamiany dla każdego wystąpienia HANA na serwerze, aby upewnić się, że skrypty kopii zapasowych działać zgodnie z oczekiwaniami.
- **removeTestStorageSnapshot.pl**: ten skrypt usuwa migawkę testu utworzone za pomocą skryptu **testStorageSnapshotConnection.pl**.
- **Azure\_hana\_dr\_failover.pl**: ten skrypt inicjuje DR trybu failover w innym regionie. Skrypt ma zostać wykonana w jednostce HANA dużych wystąpienia w regionie odzyskiwania po awarii lub na jednostkę chcesz przełączyć się. Ten skrypt zatrzymuje replikacji magazynu ze strony głównej na stronie dodatkowej, przywraca najnowszą migawkę na woluminach DR oraz zapewnia punkty instalacji DR woluminów.
- **Azure\_hana\_test\_dr\_failover.pl**: ten skrypt wykonuje test trybu failover w lokacji odzyskiwania po awarii. W przeciwieństwie do skryptu azure_hana_dr_failover.pl wykonanie tego nie przerywa replikacji magazynu z podstawowej do dodatkowej. Zamiast tego klony woluminów replikowanego magazynu są tworzone po stronie odzyskiwania po awarii, a znajdują się punkty instalacji woluminu sklonowany. 
- **HANABackupCustomerDetails.txt**: ten plik jest plikiem konfiguracji można modyfikować, który należy zmodyfikować dostosowanie konfiguracji SAP HANA. *HANABackupCustomerDetails.txt* plik jest plikiem konfiguracji i kontroli do skryptu uruchamianego migawek magazynu. Dostosuj plik dla celów i konfiguracji. Pojawi się **nazwę magazynu kopii zapasowej** i **adres IP magazynu** z SAP HANA na zarządzania usługą Azure po wdrożeniu swoich wystąpień. Sekwencja, nie można zmodyfikować kolejność lub odstępy tych zmiennych w tym pliku. Jeśli to zrobisz, skrypty nie działają poprawnie. Ponadto zostanie wyświetlony adres IP węzła skalowanie w pionie lub węzła głównego (jeśli skalowalnego w poziomie) z SAP HANA na zarządzanie usługami Azure. Znasz także liczby wystąpień HANA występujący podczas instalacji programu SAP HANA. Teraz należy dodać nazwę kopii zapasowej do pliku konfiguracji.

W przypadku wdrożenia skalowania w górę i skalowania w poziomie pliku konfiguracyjnego będzie wyglądać jak w następującym przykładzie po wypełnieniu nazwę serwera HANA dużych wystąpienie jednostki oraz adres IP serwera. Jeśli używasz programu SAP HANA System replikacji, Użyj konfiguracji replikacji systemu HANA wirtualnego adresu IP. Wypełnij wszystkie wymagane pola dla każdego identyfikatora SID HANA SAP, aby tworzenie kopii zapasowej lub odzyskiwania.

Można również komentarz wierszy wystąpień, których nie chcesz, aby utworzyć kopię zapasową w danym okresie czasu, przez dodanie "#" przed polem wymaganym. Nie należy wprowadzać SAP HANA wszystkich wystąpień, które znajdują się na serwerze, jeśli nie istnieje potrzeba tworzenie kopii zapasowej lub odzyskiwania tego konkretnego wystąpienia. Format musi znajdować się we wszystkich polach, lub wszystkie skrypty throw komunikat o błędzie i kończy skryptu. Możesz usunąć dodatkowe wiersze wymagane żadnych szczegółów informacji identyfikatora SID, który nie jest używany po ostatnim używane wystąpienie SAP HANA. Wszystkie wiersze muszą być wypełnione, oznaczone jako komentarz albo usunąć.

>[!IMPORTANT]
>Struktura pliku zmienić wraz z przejściem z wersji 2.1 do wersji 3.0 lub nowszej. Jeśli chcesz użyć skryptów w wersji 3.0, należy dostosowania struktura pliku konfiguracji. 


```
HANA Server Name: testing01
HANA Server IP Address: 172.18.18.50
```

Dla każdego wystąpienia skonfigurowanego w jednostce HANA wystąpienia dużych lub skalowania konfiguracji należy zdefiniować dane w następujący sposób:

    
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
Dla skalowalnego w poziomie i konfiguracji replikacji systemu HANA Powtórz tej konfiguracji na każdym z węzłów. To jest miara upewnia się, że w wypadku niepowodzenia tworzenia kopii zapasowych i replikacji magazynu ostatecznego kontynuować pracę.   

Po umieszczeniu wszystkie dane konfiguracyjne do *HANABackupCustomerDetails.txt* plików, sprawdź, czy konfiguracje są prawidłowe w przypadku HANA dane wystąpienia. Użyj skryptu `testHANAConnection.pl`, która jest niezależna od konfiguracji skalowania w górę i skalowania w poziomie SAP HANA.

```
testHANAConnection.pl
```

Jeśli masz SAP HANA skalowania konfiguracji upewnij się, że główny wystąpienia HANA ma dostęp do wszystkich wymaganych HANA serwerów i wystąpień. Nie ma żadnych parametrów do skryptu testu, ale należy dodać dane do *HANABackupCustomerDetails.txt* pliku konfiguracji dla skryptu do prawidłowego działania. Zwracane są tylko kody błędów polecenia powłoki, więc nie jest możliwe do skryptu, aby wskazywał błąd, sprawdź każde wystąpienie. Mimo tego skryptu Podaj niektóre przydatne uwagi, należy dokładnie sprawdzić.

Aby uruchomić skrypt, wpisz następujące polecenie:
```
 ./testHANAConnection.pl
```
Jeśli skrypt pomyślnie uzyska stan wystąpienia HANA, wyświetla komunikat o pomyślnym połączenia HANA.


Następnym krokiem testu jest Sprawdź łączność z magazynem, na podstawie danych należy umieścić w *HANABackupCustomerDetails.txt* konfiguracji pliku, a następnie wykonaj migawkę testu. Przed wykonaniem `azure_hana_backup.pl` skryptu, możesz uruchomić ten test. Jeśli wolumin nie zawiera żadnych migawek, jest niemożliwe określić, czy wolumin jest puste w przypadku niepowodzenia SSH, aby uzyskać szczegóły migawki. Z tego powodu skrypt wykonuje dwie czynności:

- Sprawdzi, że magazyn maszyny wirtualnej dzierżawcy i interfejsy są dostępne dla skryptów do wykonania migawki.
- Tworzy migawkę testu lub manekina, dla każdego woluminu przez wystąpienie HANA.

Z tego powodu wystąpienie HANA jest uwzględniona jako argument. Jeśli wykonanie nie powiedzie się, nie jest możliwe zapewnienie sprawdzanie połączenia z magazynem. Nawet jeśli nie było błędu sprawdzania, skrypt zawiera wskazówki przydatne.

1. Wykonanie sekwencji poleceń do wykonania tego testu:

   ```
   ssh <StorageUserName>@<StorageIP>
   ```

   Magazyn nazwa użytkownika i adres IP magazynu zostały dołączone do Ciebie na przekazanie HANA dużych wystąpienia jednostki.

2. Uruchom skrypt testu:
   ```
    ./testStorageSnapshotConnection.pl <HANA SID>
   ```

Skrypt próbuje zalogować się do magazynu przy użyciu klucza publicznego w poprzednich kroków konfiguracji i danych skonfigurowanych w *HANABackupCustomerDetails.txt* pliku. Jeśli logowanie zakończy się pomyślnie, jest wyświetlana następująca zawartość:

```
**********************Checking access to Storage**********************
Storage Access successful!!!!!!!!!!!!!!
```

Jeśli wystąpią problemy z połączeniem do konsoli magazynu, dane wyjściowe wygląda następująco:

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

Po pomyślnym zalogowaniu interfejsom magazynu maszyny wirtualnej skrypt będzie kontynuowane przy użyciu Faza 2 i tworzy migawkę testu. Trzy skalowania konfiguracji programu SAP HANA w tym miejscu przedstawiono dane wyjściowe:

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

Migawki testowego zostało wykonane pomyślnie ze skryptem, może kontynuować konfigurowanie przechowywania migawki. Jeśli się nie powiedzie, badania problemów, aby przejść dalej. Migawki testowego powinny pozostać wokół do momentu pierwszego rzeczywistych migawek są wykonywane.


### <a name="step-7-perform-snapshots"></a>Krok 7: Wykonaj migawki

Po zakończeniu czynności przygotowujące, można uruchomić konfigurację migawki rzeczywistego magazynu. Skrypt do zaplanowania współpracuje z konfiguracji skalowania w górę i skalowania w poziomie SAP HANA. Okresowe i regularnego wykonywania skryptu kopii zapasowej należy zaplanować skryptu za pomocą narzędzia cron. 

Można tworzyć trzy typy kopii zapasowych migawki:
- **HANA**: kopii zapasowej migawki połączonych w którym woluminów, które zawierają dane/hana/i/hana/udostępnione (zawierającej również /usr/sap) są objęte skoordynowanego migawki. Przywracanie pojedynczego pliku jest możliwe z tej migawki.
- **Dzienniki**: kopii zapasowej migawki woluminu logbackups/hana /. Nie migawki HANA zostanie wywołany do wykonania tej migawki magazynu. Oznacza, że ten wolumin magazynu zawiera kopie zapasowe dziennika transakcji SAP HANA. Są one wykonywane częściej ograniczenie dziennika wzrostu i zapobiegania utracie danych. Przywracanie pojedynczego pliku jest możliwe z tej migawki. Nie należy zmniejszyć częstotliwość w obszarze 3 minuty.
- **Rozruch**: migawki woluminu, który zawiera rozruchu numer jednostki logicznej (LUN) wystąpienia dużych HANA. Ta kopia zapasowa migawki jest możliwe tylko w przypadku typu I wersji produktu z HANA dużych wystąpienia. Nie można wykonać pojedynczy plik przywracania z migawki woluminu, który zawiera rozruchu jednostki LUN.


>[!NOTE]
> Składnia wywołania te trzy typy migawek zmienić wraz z przejściem do wersji 3.0 skrypty, które obsługują wdrożeń MCOD. Jest niepotrzebna, aby określić identyfikator SID HANA wystąpienia już. Należy się upewnić, że wystąpienia SAP HANA jednostki powinny być skonfigurowane w pliku konfiguracyjnym *HANABackupCustomerDetails.txt*.

>[!NOTE]
> Podczas wykonywania skryptu po raz pierwszy, jego nieoczekiwane błędy mogą być wyświetlane w środowisku wielu SID. Ponowne uruchomienie skryptu rozwiązuje ten problem.



Nowej składni wywołanie dla migawki magazynu ze skryptem wykonywania *azure_hana_backup.pl* wygląda podobnie do następującej:

```
HANA backup covering /hana/data and /hana/shared (includes/usr/sap)
./azure_hana_backup.pl hana <snapshot_prefix> <snapshot_frequency> <number of snapshots retained>

For /hana/logbackups snapshot
./azure_hana_backup.pl logs <snapshot_prefix> <snapshot_frequency> <number of snapshots retained>

For snapshot of the volume storing the boot LUN
./azure_hana_backup.pl boot <HANA Large Instance Type> <snapshot_prefix> <snapshot_frequency> <number of snapshots retained>

```

Szczegóły parametrów są następujące: 

- Pierwszy parametr charakteryzuje typ kopii zapasowej migawki. Dozwolone wartości to **hana**, **dzienniki**, i **rozruchu**. 
- Parametr **<HANA Large Instance Type>** jest niezbędne w przypadku rozruchu kopie zapasowe woluminów tylko. Dwa prawidłowe wartości "TypeI" lub "TypeII" są zależne od HANA dużych wystąpienia jednostki. Aby dowiedzieć się, jaki typ urządzenia, zobacz [omówienie SAP HANA (duże wystąpień) i architektury na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).  
- Parametr **< snapshot_prefix >** jest migawką lub etykieta kopii zapasowej dla typu migawka. Ma dwa cele: jedna jest można nadać mu nazwę, tak aby było wiadomo, że te migawki są informacje. Drugi cel to skrypt *azure\_hana\_backup.pl* do określenia liczby migawek magazynu, które są przechowywane w tym określoną etykietą. Przypadku zaplanowania dwóch kopii zapasowych migawki magazynu tego samego typu (takich jak **hana**), z dwie etykiety i definiować czy 30 migawki powinny być utrzymywane dla każdego, na końcu 60 magazynu migawek woluminów, których to dotyczy. 
- Parametr **< snapshot_frequency >** jest zarezerwowane dla przyszłego rozwoju i nie ma żadnego wpływu. Wartość "min 3", podczas wykonywania kopii zapasowych typu **dziennika**oraz "15 min", podczas wykonywania kopii zapasowej innych typów.
- Parametr **<number of snapshots retained>** definiuje zachowanie migawki pośrednio przez zdefiniowanie liczby migawek o identycznym prefiksie migawki (etykieta). Ten parametr jest ważne w przypadku zaplanowanego wykonaniami za pomocą usługi cron. W przypadku liczby migawek z tej samej snapshot_prefix przekracza liczbę, przez ten parametr, najstarsze migawki zostaną usunięte przed wykonaniem migawki nowego magazynu.

W przypadku skalowanie w poziomie ten skrypt wykonuje dodatkowe sprawdzenie w celu zapewnienia, że można uzyskać dostępu do wszystkich serwerów HANA. Skrypt sprawdza również, że wszystkie wystąpienia HANA zwracać odpowiedni stan wystąpienia przed tworzy migawkę SAP HANA. Migawki SAP HANA następuje migawki magazynu.

Wykonanie skryptu `azure_hana_backup.pl` tworzy magazynu migawek w następujących trzech etapów:

1. Wykonuje migawkę SAP HANA
2. Wykonuje migawkę magazynu
3. Usuwa migawkę SAP HANA, który został utworzony przed wykonaniem migawki magazynu

Można wykonać skryptu, należy wywołać ją z folderu pliku wykonywalnego HDB, do którego została skopiowana. 

Okres przechowywania jest podawana przy użyciu liczby migawek, które są przesyłane jako parametr podczas wykonywania skryptu. Ilość czasu, objętych migawek magazynu jest funkcją czasu wykonania i liczby migawek przekazany jako parametr podczas wykonywania skryptu. W przypadku liczby migawek, które są zachowane przekracza liczbę, które są nazywane jako parametr w wywołaniu skryptu, najstarsza migawka magazynu w tej samej etykiety zostaną usunięte przed wykonaniem nową migawkę. Liczba nadaj jako ostatni parametr wywołania jest liczba służy do kontrolowania liczby migawek, które są zachowane. Z tym numerem można też kontrolować, pośrednio, miejsca na dysku używanego dla migawek. 

> [!NOTE]
>Jak zmienić etykietę, liczenie zostanie uruchomiony ponownie. Należy się ograniczeniami w etykietowanie, więc nie zostaną przypadkowo usunięte z migawki.

### <a name="snapshot-strategies"></a>Strategie migawki
Częstotliwość migawek dla różnych typów zależy od tego, czy używasz funkcji odzyskiwania po awarii HANA dużych wystąpienia. Ta funkcja korzysta z magazynu migawek, które mogą wymagać specjalnego zalecenia dla okresów częstotliwości i wykonywania migawek magazynu. 

Zagadnienia i zalecenia, które należy wykonać, zakłada się, że należy wykonać *nie* korzystać z funkcji odzyskiwania po awarii, która oferuje duże wystąpień HANA. W zamian użyj magazynu migawek kopii zapasowych i mieć możliwość zapewnienia punktu w czasie odzyskiwania w ciągu ostatnich 30 dni. Podane ograniczenia liczby migawek i miejsca, klienci mają za następujące wymagania:

- Podczas odzyskiwania do punktu w czasie odzyskiwania.
- Miejsce.
- Punkt odzyskiwania i zakresie docelowego czasu odzyskiwania do potencjalnego odzyskiwania po awarii.
- Ostateczna wykonywania kopii zapasowych bazy danych pełnej HANA przed dysków. Zawsze, gdy kopii zapasowej bazy danych pełnej przed dysków lub **backint** interfejsu jest wykonywane, wykonywania migawek magazynu kończy się niepowodzeniem. Jeśli planowane jest wykonanie kopii zapasowych bazy danych pełnej u góry magazynu migawek, upewnij się, że wykonywanie migawek magazynu jest wyłączone w tym czasie.
- Liczby migawek dla każdego woluminu (maksymalnie 255).


Dla klientów, którzy nie używają funkcji odzyskiwania po awarii HANA dużych wystąpień okres migawki jest rzadziej. W takich przypadkach klientów wykonać migawki połączonych na /hana/data i /hana/shared (w tym /usr/sap) w okresach 12-godzinnym lub 24-godzinnym, a prowadzą migawki przez miesiąc. To samo dotyczy z migawki woluminu kopii zapasowej dziennika. Jednak wykonanie kopii zapasowej dziennika transakcji SAP HANA na woluminie kopii zapasowej dziennika odbywa się w 5-minutowy okresów 15 minut.

Zaplanowane magazynu migawek są wykonywane najlepiej za pomocą usługi cron. Użyj tego samego skryptu dla wszystkich kopii zapasowych i awarii i modyfikowanie danych wejściowych skryptu do dopasowania do różnych żądanie czasy tworzenia kopii zapasowych. Te migawki są wszystkie zaplanowane inaczej w cron w zależności od ich czas wykonywania: co godzinę, 12-godzinnym, codziennie lub co tydzień. 

Poniżej przedstawiono przykład harmonogramu cron w/etc/crontab:
```
00 1-23 * * * ./azure_hana_backup.pl hana hourlyhana 15min 46
10 00 * * *  ./azure_hana_backup.pl hana dailyhana 15min 28
00,05,10,15,20,25,30,35,40,45,50,55 * * * *  Perform SAP HANA transaction log backup
22 12 * * *  ./azure_hana_backup.pl log dailylogback 3min 28
30 00 * * *  ./azure_hana_backup.pl boot TypeI dailyboot 15min 28
```
W poprzednim przykładzie jest godzinowe Scalonej migawki, która obejmuje woluminów, które zawierają dane/hana/i /hana/shared (w tym/usr/sap) lokalizacji. Użyj tego typu migawka szybsze odzyskiwania w momencie w ciągu ostatnich dwóch dni. Ponadto istnieje codziennych migawek na tych woluminach. Tak masz dwa dni obowiązywania przez co godzinę migawki, a także cztery tygodnie pokrycia o codziennych migawek. Ponadto wolumin kopii zapasowej dziennika transakcji jest kopia zapasowa codziennie. Te kopie zapasowe są przechowywane również cztery tygodnie. Jak widać w trzeci wiersz crontab kopii zapasowej dziennika transakcji HANA jest zaplanowane wykonanie co 5 minut. Godziny rozpoczęcia zadań różnych cron, które wykonać migawki magazynu są rozłożone, dzięki czemu te migawki nie są wykonywane jednocześnie w określonym punkcie w czasie. 

W poniższym przykładzie możesz wykonać Scalonej migawki, która obejmuje woluminów, które zawierają/hana/danych i/hana/udostępnione (takie jak/usr/sap) lokalizacje co godzinę. Należy dysponować migawek dla dwóch dni. Migawki woluminów kopii zapasowej dziennika transakcji są wykonywane na podstawie 5-minutowy i są przechowywane przez 4 godziny. Jako przed, kopia zapasowa pliku dziennika transakcji HANA jest zaplanowane wykonanie co 5 minut. Migawki woluminu kopii zapasowej dziennika transakcji jest wykonywane z opóźnieniem 2-minutowy po rozpoczęciu kopii zapasowej dziennika transakcji. W ramach tych 2 minuty tworzenie kopii zapasowej dziennika transakcji SAP HANA powinna zakończyć się w normalnych okolicznościach. Jako przed, woluminu rozruchowego jednostka LUN jest kopii zapasowej raz dziennie przez migawkę magazynu i jest przechowywany przez cztery tygodnie.

```
10 0-23 * * * ./azure_hana_backup.pl hana hourlyhana 15min 48
0,5,10,15,20,25,30,35,40,45,50,55 * * * *  Perform SAP HANA transaction log backup
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup.pl log logback 3min 48
30 00 * * *  ./azure_hana_backup.pl boot TypeII dailyboot 15min 28
```

Poniższa ilustracja przedstawia sekwencji poprzedni przykład, z wyłączeniem rozruchu LUN:

![Relacja między migawki i kopii zapasowych](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA wykonuje regularne zapisu woluminu /hana/log dokumentów zatwierdzić zmiany w bazie danych. Regularnie SAP HANA zapisuje punktu zapisu w woluminie /hana/data. Określone w crontab SAP HANA kopii zapasowej dziennika transakcji jest wykonywana co 5 minut. Możesz również sprawdzić, czy SAP HANA migawki jest wykonywane co godzinę wyniku wyzwalania migawki magazynu połączone za pośrednictwem /hana/data i /hana/shared woluminów. Po pomyślnym zainicjowaniu migawki HANA, migawki połączonych magazynów jest wykonywana. Zgodnie z instrukcją w crontab, migawki magazynu w woluminie /hana/logbackup jest wykonywana co 5 minut, po kopii zapasowej dziennika transakcji HANA około 2 minuty.

> [!NOTE]
>Planowanie magazynu kopii zapasowych migawki na dwa węzły ustawień replikacji systemu HANA, należy najpierw upewnij się, że wykonania kopii zapasowych migawki między dwoma węzłami nie nakładają się. SAP HANA ma ograniczenie na wypadek naraz tylko jedna migawka HANA. Ponieważ migawki HANA podstawowych składników pomyślne magazynu kopii zapasowej migawki, należy upewnij się, że migawki magazynowania na węzeł podstawowych i pomocniczych i ostatecznego trzeci węzeł jest mierzony niezależnie od siebie nawzajem.


>[!IMPORTANT]
> Użycie magazynu migawek kopii zapasowych SAP HANA jest przydatna, tylko wtedy, gdy migawki są wykonywane w połączeniu z kopii zapasowej dziennika transakcji SAP HANA. Te kopie zapasowe dziennika transakcji muszą obejmować okresy między migawek magazynu. 

Jeśli ustawiono zaangażowanie użytkowników w momencie odzyskiwania 30 dni, musisz:

- W ekstremalnych przypadkach dostęp do magazynu połączone migawki przez /hana/data i /hana/shared, który jest 30 dni.
- Mieć kopie zapasowe dziennika transakcji ciągłym, które obejmuje czas od żadnego migawki połączonych magazynu. Tak najstarsze migawki woluminu kopii zapasowej dziennika transakcji musi być 30 dni. Nie jest to, czy kopie zapasowe dziennika transakcji należy skopiować do innego udziału NFS, który znajduje się w magazynie Azure. W takim przypadku może pobierać starego kopie zapasowe dziennika transakcji z tego udziału NFS.

Aby korzystać z magazynu migawek i replikacji ostatecznego magazynu kopii zapasowej dziennika transakcji, musisz zmienić lokalizację, do którego SAP HANA zapisuje kopie zapasowe dziennika transakcji. Można to zrobić w HANA Studio. Chociaż SAP HANA tworzy kopię zapasową dziennika pełne segmenty automatycznie, należy określić interwał kopii zapasowej dziennika, aby być deterministyczny. To jest szczególnie ważne podczas używania opcji odzyskiwania po awarii, ponieważ ma wykonać kopie zapasowe dziennika z okresem deterministyczna. W przypadku następujących 15 minut są ustawione jako interwał kopii zapasowej dziennika.

![Planowanie tworzenia kopii zapasowych dzienników SAP HANA w SAP HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

Możesz również kopie zapasowe, które są częściej niż co 15 minut. Częstsze ustawienie jest często używane w połączeniu z wystąpień dużych HANA działania funkcji odzyskiwania po awarii. Niektórzy klienci wykonywać kopie zapasowe dziennika transakcji, co 5 minut.  

Jeśli bazy danych nigdy nie wykonano kopię zapasową, ostatnim krokiem jest wykonanie kopii zapasowej na podstawie pliku bazy danych do utworzenia jednego wpisu kopii zapasowej, który musi istnieć w katalogu kopii zapasowej. W przeciwnym razie SAP HANA nie można zainicjować kopii zapasowych określony dziennik.

![Tworzenie kopii zapasowych opartych na plikach do utworzenia jednego wpisu kopii zapasowej](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


Po Twojego pierwszego migawek magazynu pomyślnie zostały wykonane, można usunąć migawki testowego, które zostało wykonane w kroku 6. Aby to zrobić, uruchom skrypt `removeTestStorageSnapshot.pl`:
```
./removeTestStorageSnapshot.pl <hana instance>
```

Poniżej przedstawiono przykład danych wyjściowych skryptu:
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


### <a name="monitoring-the-number-and-size-of-snapshots-on-the-disk-volume"></a>Monitorowanie liczby i rozmiaru migawek na woluminie dysku

Na woluminie określonego magazynu można monitorować liczbę migawek i użycia magazynu tych migawek. `ls` Polecenie nie wyświetla migawkę katalogu lub pliku. Jednak polecenia systemu operacyjnego Linux `du` przedstawia szczegółowe informacje dotyczące tych migawki magazynu, ponieważ są one przechowywane w woluminach tego samego. Polecenie mogą być używane z następującymi opcjami:

- `du –sh .snapshot`: Ta opcja umożliwia łączną liczbę wszystkich migawek w ramach katalogu migawki.
- `du –sh --max-depth=1`: Ta opcja wyświetla wszystkie migawki, które są zapisywane w **.snapshot** folderu, a rozmiar każdej migawki.
- `du –hc`: Ta opcja umożliwia całkowity rozmiar używane przez wszystkie migawki.

Używać tych poleceń, aby się upewnić, że zarejestrowane i przechowywane migawki nie zużywają wszystkich magazynu na woluminach.

>[!NOTE]
>Migawki rozruchu jednostki LUN nie są widoczne z powyższych poleceń.

### <a name="getting-details-of-snapshots"></a>Pobieranie szczegółów migawki
Aby uzyskać więcej informacji na temat migawek, można także użyć skryptu `azure_hana_snapshot_details.pl`. Ten skrypt można uruchomić w dowolnej lokalizacji, w przypadku aktywnego serwera w lokalizacji odzyskiwania po awarii. Skrypt zawiera następujące dane wyjściowe podziale na każdy wolumin, który zawiera migawki: 
   * Rozmiar całkowitą migawek woluminu
   * Następujące informacje w każdej migawki, w tym woluminie: 
      - Nazwa migawki 
      - Czas utworzenia 
      - Rozmiar migawki
      - Częstotliwość tworzenia migawki
      - Identyfikator kopii zapasowej HANA skojarzony z tej migawki, w razie potrzeby

Poniżej przedstawiono przykład składni wykonywania skryptu:

```
./azure_hana_snapshot_details.pl 
```

Ponieważ skrypt próbuje pobrać identyfikator HANA kopii zapasowej, należy połączyć się z wystąpieniem SAP HANA. To połączenie wymaga pliku konfiguracji *HANABackupCustomerDetails.txt* określone poprawnie. Dane wyjściowe z dwiema migawkami na woluminie może wyglądać następująco:

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


### <a name="file-level-restore-from-a-storage-snapshot"></a>Przywracania na poziomie plików z pamięci masowej migawki
Dla typów migawki **hana** i **dzienniki**, można uzyskać dostępu do migawki bezpośrednio na woluminach w **.snapshot** katalogu. Brak podkatalogu dla każdego z migawki. Możesz skopiować każdego pliku w stanie, który znajdował się w punkcie migawki z tego podkatalogu do struktury katalogu rzeczywistego.

>[!NOTE]
>Pojedynczy plik przywracania nie działa w przypadku migawki rozruchu niezależnie od typu HANA dużych wystąpienia jednostki LUN. **.Snapshot** katalogu nie jest widoczna w rozruchu jednostki LUN. 


### <a name="reducing-the-number-of-snapshots-on-a-server"></a>Zmniejszenie liczby migawek na serwerze

Jak opisano wcześniej, można zmniejszyć liczbę niektórych etykiety migawek, które są przechowywane. Ostatnie dwa parametry polecenie, aby zainicjować migawki są etykiety i liczby migawek, które chcesz zachować.

```
./azure_hana_backup.pl hana dailyhana 15min 28
```

W poprzednim przykładzie etykieta migawki jest **dailyhana** migawek oznaczone tą etykietą mają być zachowywane są **28**. Jak odpowiedzieć użycie miejsca na dysku, można zmniejszyć liczbę migawek przechowywanych. Jest łatwy sposób, aby zmniejszyć liczbę migawek do 15, na przykład do uruchomienia skryptu z ostatniego parametru ustawioną **15**:

```
./azure_hana_backup.pl hana dailyhana 15min 15
```

Jeśli to ustawienie należy uruchomić skrypt, liczby migawek, w tym nową migawkę magazynu to 15. 15 najnowszych migawki są przechowywane i 15 migawki starsze są usuwane.

 >[!NOTE]
 > Ten skrypt powoduje zmniejszenie liczby migawek tylko wtedy, gdy istnieją migawki więcej niż 1 godzinę. Skrypt nie powoduje usunięcia migawki, które są mniej niż 1 godzinę. Ograniczenia te są związane z funkcji odzyskiwania po awarii opcjonalne.

Jeśli nie chcesz przechowywać zestawu migawek z kopii zapasowej etykiety **hanadaily** w przykładach składni można wykonać skryptu za pomocą **0** jako numer przechowywania. Następnie zostaną usunięte wszystkie migawki dopasowania tej etykiecie. Jednak usunięcie wszystkich migawek może mieć wpływ na możliwości wystąpienia dużych HANA działania funkcji odzyskiwania po awarii.

Druga opcja, aby usunąć migawki określonej jest użycie skryptu `azure_hana_snapshot_delete.pl`. Ten skrypt zaprojektowano w celu usunięcia migawki lub zestawu migawek albo za pomocą Identyfikatora kopii zapasowej HANA tak jak w HANA Studio lub za pośrednictwem nazwa migawki. Obecnie identyfikator kopii zapasowej jest tylko powiązane migawek utworzonych dla **hana** migawki typu. Migawki kopii zapasowych typu **dzienniki** i **rozruchu** nie wykonuj migawki SAP HANA i dlatego nie ma Brak Identyfikatora kopii zapasowej do znalezienia tych migawek. W przypadku wprowadzenia nazwy migawki jest szuka wszystkie migawki na różnych woluminach pasujących do nazwy wprowadzone migawki. 

Wywołanie skryptu należy określić identyfikator SID wystąpienia HANA przy użyciu składni wywołanie skryptu:

```
./azure_hana_snapshot_delete.pl <SID>

```

Uruchom skrypt jako użytkownik **głównego**.

W przypadku wybrania migawkę każdej migawki można usunąć pojedynczo. Najpierw podaj wolumin zawierający migawkę, a następnie podaj nazwę migawki. Jeśli istnieje w tym woluminie migawki jest więcej niż 1 godzinę, jest usunięta. Można znaleźć migawki nazwy i nazwy woluminu, wykonując `azure_hana_snapshot_details` skryptu. 

>[!IMPORTANT]
>W przypadku danych, który istnieje tylko w migawce usuwasz, po usunięciu migawki, że dane są trwale utracone.

   

### <a name="recover-to-the-most-recent-hana-snapshot"></a>Odzyskiwanie do ostatniego migawki HANA

W scenariuszu produkcyjnym rozwijanej proces przywracania z migawki magazynu może być inicjowane jako zdarzeniem klienta z pomocy technicznej firmy Microsoft Azure. Jeśli dane zostały usunięte w systemie produkcyjnym i jest jedynym sposobem, aby go uzyskać, aby przywrócić produkcyjną bazę danych jest kwestią wysoka pilność.

W innej sytuacji w momencie odzyskiwania może być niski pilność i planowanych dni wcześniej. Można zaplanować na zarządzania usługą Azure to odzyskiwanie z programu SAP HANA zamiast wywoływanie flagę o wysokim priorytecie. Na przykład może być planowane jest uaktualnienie oprogramowania SAP, stosując nowy pakiet rozszerzenia. Następnie należy przywrócić migawki, która reprezentuje stan przed wykonaniem uaktualnienia pakietu rozszerzenia.

Przed wysłaniem żądania, które trzeba przygotować. SAP HANA zespołu zarządzania usługą Azure można obsłużyć żądania i podaj przywracanych woluminów. Później należy przywrócić bazę danych HANA oparte na migawki. 

Poniżej przedstawiono sposób przygotowania do żądania:

>[!NOTE]
>Interfejs użytkownika mogą się różnić od poniższe zrzuty ekranu, w zależności od używanej wersji programu SAP HANA.

1. Zdecyduj, które migawek do przywrócenia. Wielkość hana/danych jest przywrócić, chyba że poinstruować w inny sposób. 

2. Zamknij wystąpienie HANA.

 ![Zamknij wystąpienie HANA](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

3. Usuń woluminy danych w każdym węźle bazy danych HANA. Woluminy danych są wciąż zainstalowany system operacyjny, przywracanie migawki nie powiedzie się.
 ![Usuń woluminy danych w każdym węźle bazy danych HANA](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

4. Otwórz żądanie pomocy technicznej platformy Azure i zawierają instrukcje informujące o przywrócenie określoną migawkę.

 - Podczas przywracania: SAP HANA z zarządzania usługi Azure może poprosić o uczestniczyć konferencji do zapewnienia koordynacji, weryfikacji i potwierdzenie, że migawki poprawne magazynu został przywrócony. 

 - Po przywróceniu: SAP HANA na zarządzania usługą Azure powiadamia użytkownika po przywróceniu migawki magazynu.

5. Po ukończeniu procesu przywracania, zainstaluj wszystkie woluminy danych.

 ![Zainstaluj wszystkie woluminy danych](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)

6. Wybierz opcje odzyskiwania w ramach SAP HANA Studio, jeśli nie automatycznie pochodzą po ponownym HANA DB za pomocą programu SAP HANA Studio. W poniższym przykładzie przedstawiono przywracanie do ostatniego migawki HANA. Migawka magazynu osadza jedną migawkę HANA. W przypadku przywracania do ostatniego migawki magazynu, należy go najnowszych migawki HANA. (W przypadku przywracania starszej migawkę magazynu, należy znaleźć migawki HANA na podstawie czasu, który migawki magazynu.)

 ![Wybierz opcje odzyskiwania w ramach SAP HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image10-recover-options-a.png)

7. Wybierz **odzyskać bazę danych do migawki kopii zapasowej lub magazynu danych specyficznych dla**.

 ![Określ typ odzyskiwania okna](./media/hana-overview-high-availability-disaster-recovery/image11-recover-options-b.png)

8. Wybierz **Określ tworzenia kopii zapasowej bez katalogu**.

 ![Określ lokalizację kopii zapasowej okna](./media/hana-overview-high-availability-disaster-recovery/image12-recover-options-c.png)

9. W **typ docelowy** listy, wybierz **migawki**.

 ![Określ kopii zapasowej w celu odzyskania okna](./media/hana-overview-high-availability-disaster-recovery/image13-recover-options-d.png)

10. Wybierz **Zakończ** aby rozpocząć proces odzyskiwania.

 ![Wybierz przycisk Zakończ, aby rozpocząć proces odzyskiwania](./media/hana-overview-high-availability-disaster-recovery/image14-recover-options-e.png)

11. Bazy danych HANA jest przywracane i odzyskać migawkę HANA, który znajduje się w pamięci masowej migawki.

 ![HANA bazy danych jest przywracane i odzyskać migawkę HANA](./media/hana-overview-high-availability-disaster-recovery/image15-recover-options-f.png)

### <a name="recover-to-the-most-recent-state"></a>Odzyskiwanie do ostatniego stanu

Następujący proces przywraca migawki HANA, który znajduje się w pamięci masowej migawki. Go następnie przywraca kopie zapasowe dziennika transakcji ostatniego stanu bazy danych przed przywróceniem z migawki magazynu.

>[!IMPORTANT]
>Przed kontynuowaniem upewnij się, że masz pełne, jak i ciągłe łańcuch kopii zapasowej dziennika transakcji. Bez tych kopii zapasowych nie można przywrócić bieżący stan bazy danych.

1. Wykonać kroki od 1 do 6 w [odzyskiwanie do ostatniego migawki HANA](#recovering-to-the-most-recent-hana-snapshot).

2. Wybierz **odzyskać bazę danych do jej ostatniego stanu**.

 ![Wybierz opcję "Odzyskać bazę danych do jej ostatniego stanu"](./media/hana-overview-high-availability-disaster-recovery/image16-recover-database-a.png)

3. Określ lokalizację najnowszej kopii zapasowych dziennika HANA. Lokalizacja musi zawierać wszystkie HANA kopie zapasowe dziennika transakcji z migawki HANA do ostatniego stanu.

 ![Określ lokalizację najnowszej kopii zapasowych dziennika HANA](./media/hana-overview-high-availability-disaster-recovery/image17-recover-database-b.png)

4. Wybierz kopię zapasową jako podstawowy, z którego można odzyskać bazy danych. W tym przykładzie migawki HANA zrzut ekranu jest migawki HANA, która została uwzględniona w migawce magazynu. 

 ![Wybierz kopię zapasową jako podstawowy, z którego można odzyskać bazy danych](./media/hana-overview-high-availability-disaster-recovery/image18-recover-database-c.png)

5. Wyczyść **kopii zapasowych Delta użyj** pole wyboru, jeśli nie istnieją różnice między czasem migawki HANA i stan ostatniej.

 ![Wyczyść pole wyboru "Użyj różnicowe kopie zapasowe", jeśli nie istnieją żadne delty](./media/hana-overview-high-availability-disaster-recovery/image19-recover-database-d.png)

6. Na ekranie Podsumowanie, wybierz **Zakończ** można uruchomić procedury przywracania.

 ![Kliknij przycisk "Zakończ", na ekranie Podsumowanie](./media/hana-overview-high-availability-disaster-recovery/image20-recover-database-e.png)

### <a name="recover-to-another-point-in-time"></a>Odzyskaj do innego punktu w czasie
Aby odzyskać do punktu w czasie między migawki HANA (dołączone do migawki magazynu) i jest nowszy niż HANA migawki w momencie odzyskiwania, wykonaj następujące czynności:

1. Upewnij się, że wszystkie kopie zapasowe dziennika transakcji z migawki HANA czas, który ma zostać przeprowadzone odzyskiwanie.
2. Rozpocząć procedurę opisaną w temacie [odzyskiwanie do ostatniego stanu](#recovering-to-the-most-recent-state).
3. W kroku 2 procedury w **typu odzyskiwania określ** wybierz **odzyskać bazę danych do następujących punktu w czasie**, a następnie określ punkt w czasie. 
4. Wykonaj kroki od 3 do 6.

### <a name="monitor-the-execution-of-snapshots"></a>Monitor wykonywania migawek

Jak używasz magazynu migawek HANA dużych wystąpień, należy również monitorować wykonywania tych migawek. Skrypt, który wykonuje migawkę magazynu zapisuje dane wyjściowe do pliku, a następnie zapisuje do tej samej lokalizacji co skryptów języka Perl. Oddzielny plik jest przeznaczony dla każdej migawki magazynu. Dane wyjściowe każdego pliku przedstawia różne etapy czy wykonuje migawkę skryptu:

1. Znajduje w woluminach, które należy utworzyć migawkę.
2. Umożliwia znalezienie migawki z tych woluminów.
3. Usuwa ostatecznego istniejących migawek do dopasowania liczby migawek określona.
4. Tworzy migawkę SAP HANA.
5. Tworzy migawkę magazynu dla woluminów.
6. Usuwa migawkę SAP HANA.
7. Zmienia nazwę migawki najnowszych **.0**.

Najważniejszy element cab skryptu identyfikowane jest tej części:
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
W tym przykładzie widać, jak skrypt rekordy Tworzenie migawki HANA. W przypadku skalowania w poziomie ten proces jest inicjowana na węzła głównego. Węzeł główny inicjuje synchroniczne tworzenie migawek SAP HANA na każdym z węzłów procesu roboczego. Następnie wykonywana jest migawka magazynu. Po pomyślnym wykonaniu migawki pamięci masowej migawki HANA został usunięty. Usunięcie migawki HANA jest inicjowana z węzła głównego.


## <a name="disaster-recovery-principles"></a>Zasady odzyskiwania po awarii
Duże wystąpień HANA oferują funkcji odzyskiwania po awarii, między sygnatury HANA dużych wystąpienia w różnych regionach platformy Azure. Na przykład w przypadku wdrożenia HANA dużych wystąpienia jednostki w regionu zachodnie nam Azure, służy HANA dużych wystąpienia jednostki w regionie nam wschodnie jako jednostki odzyskiwania po awarii. Jak wspomniano wcześniej, odzyskiwania po awarii nie skonfigurowano automatycznie, ponieważ wymaga ona płatności dla innej jednostki HANA dużych wystąpienia w regionie odzyskiwania po awarii. Instalator odzyskiwania po awarii działa dla ustawień skalowania w górę, a także skalowalnego w poziomie. 

W scenariuszach wdrożone do tej pory klientów umożliwia jednostce w regionie DR systemami nieprodukcyjnych używających zainstalowane wystąpienie HANA. Jednostka wystąpienia dużych HANA musi mieć tej samej jednostki SKU jako jednostka SKU wykorzystywane do celów produkcyjnych. Na poniższej ilustracji przedstawiono jakie konfiguracji dysków od jednostki serwera w regionie Azure środowiska produkcyjnego i region odzyskiwania po awarii wygląda następująco:

![W konfiguracji odzyskiwania po awarii z punktu widzenia dysku](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_setup.PNG)

Jak pokazano na rysunku Przegląd, należy następnie kolejność drugi zestaw woluminów dysku. Woluminów dysku docelowym jest taki sam rozmiar jak wielkości produkcji dla wystąpienia produkcji w jednostkach odzyskiwania po awarii. Te woluminy dysku są skojarzone z wystąpienia dużych HANA jednostki serwera w lokacji odzyskiwania po awarii. Następujące woluminy są replikowane z obszaru produkcji do odzyskiwania po awarii lokacji:

- / hana/danych
- / hana/logbackups 
- /Hana/Shared (w tym/usr/sap)

Wolumin /hana/log nie jest replikowany, ponieważ w taki sposób, którą można wykonać przywracania z nich nie jest potrzebny dziennik transakcji SAP HANA. 

Podstawy funkcji odzyskiwania po awarii oferowana jest funkcje replikacji magazynu oferowane przez infrastrukturę HANA dużych wystąpienia. Funkcje, które jest używane po stronie magazynu nie jest stały strumień zmiany, które są replikowane w sposób asynchroniczny, ponieważ wprowadzenia zmiany, aby wolumin magazynu. Zamiast tego jest mechanizm, który polega na fakt, że migawki te woluminy są tworzone regularnie. Różnica między już replikowanych migawki i nową migawkę, która nie została jeszcze zreplikowana jest przeniesione do lokacji odzyskiwania po awarii na woluminy dysku docelowym.  Te migawki są przechowywane w woluminach, a w przypadku trybu failover odzyskiwania po awarii, konieczne jest przywrócenie na tych woluminach.  

Pierwszy transfer pełnych danych woluminu powinien być przed ilość danych staje się mniejszy niż delty między migawki. W związku z tym woluminy w lokacji odzyskiwania po awarii zawierają każdego jeden migawek woluminu w lokacji produkcyjnej. Po pewnym czasie można użyć tego systemu z odzyskiwania po awarii na uzyskanie dostępu do starszych stan odzyskać utracone dane, bez wycofywanie systemu produkcji.

W przypadku wdrożeń MCOD z wielu niezależnych wystąpień SAP HANA na jedną jednostkę HANA dużych wystąpienia oczekuje się, wszystkie wystąpienia SAP HANA uzyskiwania magazynu replikowane do strony odzyskiwania po awarii.

W przypadkach, w których HANA replikacji systemu są używane jako funkcji wysokiej dostępności w swojej witrynie produkcji są replikowane tylko woluminy wystąpienia warstwy 2 (lub replice). Ta konfiguracja może prowadzić do opóźnienia w replikacji magazynu do odzyskiwania po awarii lokacji, jeśli obsługa lub wyłączyć jednostki serwera repliki pomocniczej (poziom 2) lub wystąpieniem SAP HANA w tej jednostce. 


>[!IMPORTANT]
>Podobnie jak w przypadku replikacji systemu HANA wielowarstwowej, wyłączania jednostki warstwy 2 HANA wystąpienia lub serwer blokuje replikacji do lokacji odzyskiwania po awarii podczas korzystania z funkcji odzyskiwania po awarii HANA dużych wystąpienia.


>[!NOTE]
>Funkcje replikacji dużych wystąpienia HANA magazynu jest dublowania i replikacji migawek magazynu. Jeśli nie wykonasz magazynu migawek, wprowadzonego w [kopii zapasowej i przywracania](#backup-and-restore) sekcji tego artykułu, nie może być dowolnym replikacji do lokacji odzyskiwania po awarii. Wykonanie migawki magazynu jest wymagana do przechowywania replikacji do lokacji odzyskiwania po awarii.



## <a name="preparation-of-the-disaster-recovery-scenario"></a>Przygotowywanie scenariusza odzyskiwania po awarii
W tym scenariuszu masz system produkcji systemem HANA dużych wystąpień w środowisku produkcyjnym region platformy Azure. Aby uzyskać instrukcje, które należy wykonać Załóżmy, że identyfikator SID tego systemu HANA to "PRD", i czy masz nieprodukcyjnych systemami na HANA dużych wystąpień w regionie Azure odzyskiwania po awarii. W drugim Załóżmy, że się za pomocą jego identyfikatora SID "TST." Na poniższej ilustracji przedstawiono tę konfigurację:

![Początek Instalator odzyskiwania po awarii](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start1.PNG)

Jeśli wystąpienie serwera nie jest jeszcze uporządkowanych przy użyciu zestawu woluminu dodatkowego magazynu SAP HANA na dołącza zarządzania usługą Azure dodatkowy zestaw woluminów jako element docelowy dla repliki produkcji do jednostki HANA dużych wystąpienia, na którym jest uruchomiony TST Wystąpienie HANA. W tym celu należy podać identyfikator SID wystąpienia HANA produkcji. Po SAP HANA na zarządzania usługą Azure potwierdza załącznik tych woluminów, należy zainstalować te woluminów HANA dużych wystąpienia jednostki.

![Następnym krokiem Instalator odzyskiwania po awarii](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start2.PNG)

Następnym krokiem jest do zainstalowania w jednostce HANA dużych wystąpienia w regionie Azure odzyskiwania po awarii, gdzie uruchomić TST HANA wystąpienia drugie wystąpienie SAP HANA. Nowo zainstalowanym wystąpieniem SAP HANA musi mieć ten sam identyfikator SID. Użytkownicy utworzeni musi być taką samą UID i identyfikator grupy, który ma wystąpienie produkcji. Jeśli instalacja zakończyła się pomyślnie, należy:

- Wykonaj krok 2 przygotowanie migawki magazynu omówione w tym artykule.
- Jeśli nie ma jeszcze zrobione, tworzenia klucza publicznego dla jednostki DR HANA dużych wystąpienia jednostki. Zobacz krok 3 Przygotowanie migawki magazynu omówione w tym artykule.
- Obsługa *HANABackupCustomerDetails.txt* z nowego wystąpienia HANA i testowania czy łączność w magazynie działa prawidłowo.  
- Zatrzymaj nowo zainstalowanym wystąpieniem SAP HANA w jednostce HANA dużych wystąpienia w regionie Azure odzyskiwania po awarii.
- Odinstaluj woluminy te PRD i skontaktuj się z SAP HANA na zarządzania usługą Azure. Woluminy nie może pozostać zainstalowanego do jednostki, ponieważ nie mogą one być dostępne podczas działania jako cel replikacji magazynu.  

![Kroku konfiguracji odzyskiwania po awarii, zanim zostanie nawiązana replikacji](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start3.PNG)

Zespół operacyjny ustanawia relacji replikacji między woluminami PRD w środowisku produkcyjnym region platformy Azure oraz na woluminach PRD w regionie Azure odzyskiwania po awarii.

>[!IMPORTANT]
>Wolumin /hana/log nie jest replikowany, ponieważ nie jest konieczne przywrócenie replikowanych bazy danych SAP HANA w spójnym stanie, w lokacji odzyskiwania po awarii.

Następnie skonfiguruj lub Dostosuj harmonogram tworzenia kopii zapasowych migawki magazynu, aby uzyskać dostęp do Twoich RTO i cel punktu odzyskiwania w przypadku awarii. Aby zminimalizować cel punktu odzyskiwania, należy ustawić następujące interwały replikacji w usłudze HANA dużych wystąpienie:
- Dla woluminów objętych migawki połączonych (format migawki **hana**), zestaw replikację co 15 minut do celów wolumin magazynu odpowiednik w lokacji odzyskiwania po awarii.
- Na woluminie kopii zapasowej dziennika transakcji (format migawki **dzienniki**), zestawu do replikowania co 3 minuty do celów wolumin magazynu odpowiednik w lokacji odzyskiwania po awarii.

Aby zminimalizować cel punktu odzyskiwania, wprowadź następujące ustawienia:
- Wykonaj **hana** typu magazynu migawek (zobacz "krok 7: wykonać migawki") co 30 minut do 1 godziny.
- Wykonaj kopie zapasowe dziennika transakcji SAP HANA co 5 minut.
- Wykonaj **dzienniki** wpisz pamięci masowej migawki co 5 – 15 minut. Z tego okresu interwał uzyskania RPO około 15 25 minut.

Z tym Instalatora, sekwencja kopie zapasowe dziennika transakcji, pamięci masowej migawki i replikacji transakcji HANA dziennika kopii zapasowej danych woluminu oraz/hana/i /hana/shared (w tym/usr/sap) może wyglądać dane wyświetlane na rysunku:

 ![Relacja między migawki kopii zapasowej dziennika transakcji i dublowanie przystawki na osi czasu](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

Uzyskanie nawet lepszy cel punktu odzyskiwania w przypadku odzyskiwania po awarii, możesz skopiować kopie zapasowe dziennika transakcji HANA z SAP HANA na platformie Azure (duże wystąpień) w regionie Azure. Aby osiągnąć ten dalsze zmniejszenie cel punktu odzyskiwania, wykonaj następujące czynności:

1. Utwórz kopię zapasową transakcji HANA dziennika tak często, jak to możliwe do /hana/logbackups.
2. Użyj rsync do skopiowania kopie zapasowe dziennika transakcji do systemu plików NFS udziału hostowanych maszyn wirtualnych platformy Azure. Maszyny wirtualne znajdują się w sieci wirtualnych platformy Azure w regionie Azure środowiska produkcyjnego i regiony odzyskiwania po awarii. Należy połączyć oba sieci wirtualnych platformy Azure z obwodem łączenie produkcji HANA dużych wystąpień na platformie Azure. Zobacz grafiki w [sieci zagadnienia dotyczące odzyskiwania po awarii z wystąpieniami dużych HANA](#Network-considerations-for-disaster recovery-with-HANA-Large-Instances) sekcji. 
3. Zachowaj kopie zapasowe dziennika transakcji w regionie w maszynie Wirtualnej jest dołączony do systemu plików NFS wyeksportowane magazynu.
4. W przypadku trybu failover po awarii należy uzupełnić kopie zapasowe dziennika transakcji, które można znaleźć na woluminie /hana/logbackups o więcej ostatnio wykonane kopie zapasowe dziennika transakcji na NFS udział w lokacji odzyskiwania po awarii. 
5. Uruchom kopię zapasową dziennika transakcji można przywrócić do najnowszej kopii zapasowej mogły zostać zapisane w regionie odzyskiwania po awarii.

Podczas operacji wystąpienia dużych HANA Potwierdź ustawienia relacji replikacji i rozpocząć wykonywania migawki magazynu z kopii zapasowych, rozpoczyna się replikacja danych.

![Kroku konfiguracji odzyskiwania po awarii, zanim zostanie nawiązana replikacji](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start4.PNG)

Ponieważ w toku replikacji migawek na woluminach PRD w regionach DR Azure nie są przywracane. Tylko są przechowywane. Jeśli woluminy są instalowane w taki stan, reprezentują stanu, w którym możesz odinstalować tych woluminów po zainstalowaniu wystąpienia PRD SAP HANA w jednostce serwera w regionie Azure odzyskiwania po awarii. Reprezentują one magazynu kopii zapasowych, które nie zostały jeszcze przywrócone.

W przypadku trybu failover również można przywrócić starszą migawkę magazynu, zamiast najnowszą migawkę magazynu.

## <a name="disaster-recovery-failover-procedure"></a>Procedury awaryjnego odzyskiwania trybu failover
Istnieją dwa przypadki, które należy uwzględnić podczas przechodzenie w tryb failover do odzyskiwania po awarii lokacji:

- Należy bazy danych SAP HANA, aby powrócić do najnowszy stan danych. W takim przypadku jest samoobsługi skryptu umożliwiają wykonywanie trybu failover bez konieczności kontakt z firmą Microsoft. Jednak do powrotu po awarii, musisz współpraca z firmą Microsoft.
- Chcesz przywrócić migawkę magazynu, który nie jest najnowsza wersja replikowanych migawki. W takim przypadku należy współpraca z firmą Microsoft. 

>[!NOTE]
>Następujące kroki muszą być wykonywane na jednostkę HANA dużych wystąpienia, która reprezentuje jednostkę odzyskiwania po awarii. 
 
Aby przywrócić najnowsze migawki replikowanego magazynu, wykonaj następujące czynności: 

1. Zamknij wystąpienie nieprodukcyjnych HANA w jednostce odzyskiwania po awarii HANA wystąpień dużych, której używasz. Jest to spowodowane istnieje wstępnie zainstalowane wystąpienie nieaktywni produkcji HANA.
2. Upewnij się, że są uruchomione żadne procesy SAP HANA. Użyj następującego polecenia dla tego sprawdzenia: `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`. Dane wyjściowe powinny być widoczne możesz **hdbdaemon** przetwarzanie w stanie zatrzymania, a żadne inne procesy HANA w nim uruchomiona lub jest uruchomiona.
3. W jednostce wystąpienia dużych HANA odzyskiwania po awarii lokacji, uruchom skrypt *azure_hana_dr_failover.pl*. Skrypt jest podanie identyfikatora SID SAP HANA do przywrócenia. Gdy żądanie, wpisz w jednym lub tylko SAP HANA SID, został zreplikowany, która jest przechowywana w *HANABackupCustomerDetails.txt* pliku w jednostce HANA dużych wystąpienia w lokacji odzyskiwania po awarii. 

      Jeśli chcesz mieć wiele wystąpień SAP HANA przejścia w tryb failover, należy uruchomić skrypt kilka razy. Żądanie, wpisz identyfikator SID SAP HANA chcesz przełączyć i przywracania. Po zakończeniu skrypt pokazuje listę punktów instalacji woluminów, które są dodawane do HANA dużych wystąpienia jednostki. Ta lista zawiera również przywróconej woluminów odzyskiwania po awarii.

4. Zainstaluj woluminów odzyskiwania po awarii przywróconej za pomocą poleceń systemu operacyjnego Linux do jednostki HANA dużych wystąpienia w lokacji odzyskiwania po awarii. 
6. Uruchom nieaktywni wystąpienie SAP HANA w środowisku produkcyjnym.
7. Jeśli wybrano opcję Kopiuj dzienniki kopii zapasowej dziennika transakcji Aby skrócić czas odzyskiwania, należy scalić te kopie zapasowe dziennika transakcji do nowo zainstalowanego DR/hana/logbackups katalogu. Nie zastępuj istniejących kopii zapasowych. Skopiuj nowszych kopii zapasowych, które nie zostały zreplikowane z najnowszą replikacji migawki magazynu.
8. Można także przywrócić pojedynczych plików poza migawek, które zostały zreplikowane do woluminu /hana/shared/PRD w regionie Azure odzyskiwania po awarii. 

Możesz również testowanie trybu failover odzyskiwania po awarii, bez wywierania wpływu na moment relacji. Aby wykonać test trybu failover, wykonaj poprzednie kroki 1 i 2, a następnie kontynuuj następujący krok 3.

>[!IMPORTANT]
>Czy *nie* Uruchom wszystkie transakcje produkcji w wystąpieniu, który został utworzony w lokacji odzyskiwania po awarii w procesie **testowania trybu failover** ze skryptem wprowadzona w kroku 3. To polecenie tworzy zestaw woluminów, które nie mają relacji do lokacji głównej. W związku z tym synchronizacji do lokacji głównej jest *nie* możliwe. 

Krok 3 dla testu pracy awaryjnej.

W jednostce wystąpienia dużych HANA odzyskiwania po awarii lokacji, uruchom skrypt **azure_hana_test_dr_failover.pl**. Ten skrypt jest *nie* zatrzymywanie relacji replikacji między lokacją główną a lokacją odzyskiwania po awarii. Zamiast tego skryptu jest Klonowanie woluminów magazynu odzyskiwania po awarii. Po pomyślnym zainicjowaniu procesu klonowania, sklonowany woluminy są przywrócony do stanu migawki najnowszych, a następnie zainstalowane do jednostki odzyskiwania po awarii. Skrypt jest podanie identyfikatora SID SAP HANA do przywrócenia. Wpisz jeden lub tylko SAP HANA SID, został zreplikowany, która jest przechowywana w *HANABackupCustomerDetails.txt* pliku w jednostce HANA dużych wystąpienia w lokacji odzyskiwania po awarii. 

Jeśli chcesz mieć wiele wystąpień SAP HANA do testowania, należy uruchomić skrypt kilka razy. Gdy żądanie, wpisz w SAP HANA identyfikator SID wystąpienia, które chcesz przetestować tryb failover. Po zakończeniu skrypt pokazuje listę punktów instalacji woluminów, które są dodawane do HANA dużych wystąpienia jednostki. Ta lista zawiera również sklonowany woluminów odzyskiwania po awarii.

Przejdź do kroku 4.

   >[!NOTE]
   >Ta procedura ma zastosowanie, jeśli chcesz przełączyć się lokacja DR ratowania części danych, które usunięto godz. temu i musi woluminów odzyskiwania po awarii, należy ustawić wcześniejszej migawki. 

4. Zamknij wystąpienie nieprodukcyjnych HANA w jednostce odzyskiwania po awarii HANA wystąpień dużych, której używasz. Jest to spowodowane istnieje wstępnie zainstalowane wystąpienie nieaktywni produkcji HANA.
5. Upewnij się, że są uruchomione żadne procesy SAP HANA. Użyj następującego polecenia dla tego sprawdzenia: `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`. Dane wyjściowe powinny być widoczne możesz **hdbdaemon** przetwarzanie w stanie zatrzymania, a żadne inne procesy HANA w nim uruchomiona lub jest uruchomiona.
6. Określ, do którego migawek nazwa lub identyfikator kopii zapasowej SAP HANA chcesz, aby przywrócić lokacji odzyskiwania po awarii. W przypadku odzyskiwania awaryjnego rzeczywistych ta migawka jest zwykle najnowszą migawkę. Jeśli potrzebujesz odzyskać utracone dane pobrania żadnej wcześniejszej migawki.
7. Informacje o kontakcie Azure obsługuje żądania obsługi o wysokim priorytecie. Poproś o przywracania tej migawki (o nazwie i Data migawki) lub identyfikator kopii zapasowej HANA w lokacji odzyskiwania po awarii. Wartość domyślna to, że po stronie operacji przywraca/hana/ilość danych tylko. Jeśli chcesz mieć/hana/logbackups woluminy również należy wyraźnie określają, że. *Nie należy przywracać /hana/shared woluminu.* Zamiast tego należy wybrać określonych plików, takich jak global.ini poza **.snapshot** katalogu i jego podkatalogach po Zainstaluj/hana/udostępniony wolumin PRD. 

   Na stronie Operacje są wykonywane następujące czynności:

   a. Zatrzymania replikacji migawek od wielkości produkcji do woluminów odzyskiwania po awarii. Ta przerw w działaniu może już się to zdarzyć w przypadku wystąpienia awarii w lokacji produkcji przyczyny, należy wykonać procedurę odzyskiwania po awarii.
   
   b. Magazyn migawek nazwy lub migawki o identyfikatorze kopii zapasowej została wybrana opcja zostanie przywrócona na woluminach odzyskiwania po awarii.
   
   c. Po przywróceniu woluminów odzyskiwania po awarii są dostępne do zamontowania jednostek HANA dużych wystąpienia w regionie odzyskiwania po awarii.
      
8. Zainstaluj woluminów odzyskiwania po awarii do jednostki HANA dużych wystąpienia w lokacji odzyskiwania po awarii. 
9. Uruchom nieaktywni wystąpienie SAP HANA w środowisku produkcyjnym.
10. Jeśli wybrano opcję Kopiuj dzienniki kopii zapasowej dziennika transakcji Aby skrócić czas odzyskiwania, należy scalić te kopie zapasowe dziennika transakcji do nowo zainstalowanego DR/hana/logbackups katalogu. Nie zastępuj istniejących kopii zapasowych. Skopiuj nowszych kopii zapasowych, które nie zostały zreplikowane z najnowszą replikacji migawki magazynu.
11. Można także przywrócić pojedynczych plików poza migawek, które zostały zreplikowane do woluminu /hana/shared/PRD w regionie Azure odzyskiwania po awarii.

Następnej sekwencji kroki obejmuje odzyskiwanie wystąpienia produkcji SAP HANA na podstawie migawki przywróconej magazynu i kopie zapasowe dziennika transakcji, które są dostępne:

1. Zmień lokalizację kopii zapasowej na **/hana/logbackups** za pomocą programu SAP HANA Studio.
   ![Zmień lokalizację kopii zapasowej do odzyskiwania DR](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

2. SAP HANA skanuje do lokalizacji pliku kopii zapasowej, a także sugeruje najnowszej kopii zapasowej dziennika transakcji można przywrócić. Skanowanie może potrwać kilka minut, aż do ekranu, takich jak pojawi się następujące: ![listy kopie zapasowe dziennika transakcji odzyskiwania DR](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

3. Niektóre ustawienia domyślne ustawienia:

      - Wyczyść **Użyj różnicowe kopie zapasowe**.
      - Wybierz **zainicjować obszaru dziennika**.

   ![Ustaw obszar zainicjować dziennika](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

4. Wybierz pozycję **Finish** (Zakończ).

   ![Zakończ Przywracanie DR](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

Powinny być wyświetlane okna postępu, jak pokazano w tym miejscu. Należy pamiętać, że przykładem jest przywracania odzyskiwania po awarii trzech węzłów konfiguracji SAP HANA skalowalnego w poziomie.

![Postęp przywracania](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

Jeśli przywracania zatrzymuje się na **Zakończ** ekranu i nie pokazuj ekranu postępu, upewnij się, że są uruchomione wszystkie wystąpienia SAP HANA na węzłów procesu roboczego. Jeśli to konieczne, należy ręcznie uruchomić wystąpień SAP HANA.


### <a name="failback-from-a-dr-to-a-production-site"></a>Powrót po awarii z odzyskiwania po awarii do miejsca produkcji
Użytkownik może zakończyć się niepowodzeniem z odzyskiwania po awarii do miejsca produkcji. Przyjrzyjmy się scenariusz, w którym trybu failover w lokacji odzyskiwania po awarii został spowodowany przez problemy w środowisku produkcyjnym region platformy Azure i nie trzeba odzyskać utracone dane. Uruchomiony obciążenie produkcji SAP przez pewien czas w lokacji odzyskiwania po awarii. Jak zostaną rozwiązane problemy w lokacji produkcyjnej, chcesz powrót po awarii do lokacji produkcji. Ponieważ nie można utracić dane, krok do miejsca produkcji obejmuje kilka kroków i ścisłą współpracę z SAP HANA zespołu operacje platformy Azure. To możesz wyzwolić zespół działu operacji można uruchomić synchronizacji do miejsca produkcji po zostaną rozwiązane problemy.

Jest to sekwencja czynności do wykonania:

1. SAP HANA zespołu operacje platformy Azure pobiera trigger, aby zsynchronizować woluminów magazynu produkcyjnych z woluminów magazynu odzyskiwania po awarii, które teraz reprezentuje stan produkcji. W tym stanie jednostkę HANA dużych wystąpienia w lokacji produkcyjnej jest wyłączana.
2. SAP HANA zespołu operacje platformy Azure monitoruje replikacji i upewnia się, że jest on przechwycono przed informuje o dostępności.
3. Zamknij aplikacje, które używają produkcji HANA wystąpienia w lokacji odzyskiwania po awarii. Następnie wykonaj kopię zapasową dziennika transakcji HANA. Następnie należy zatrzymać HANA uruchomiona jednostkach HANA dużych wystąpienia w lokacji odzyskiwania po awarii.
4. Po działania w jednostce HANA dużych wystąpienia w lokacji odzyskiwania po awarii wystąpienia HANA zostanie zamknięty, zespół operacyjny ręcznie synchronizuje woluminów dysku ponownie.
5. SAP HANA zespołu operacje platformy Azure ponownie uruchamia HANA dużych wystąpienia jednostki w lokacji produkcyjnej i przekazuje użytkownikowi. Upewnij się, że wystąpienie SAP HANA jest w stanie zamknięcie w czasie uruchamiania HANA dużych wystąpienia jednostki.
6. Możesz wykonać te same kroki przywracania bazy danych, jak w przypadku wcześniej przechodzenie w tryb failover do lokacji odzyskiwania po awarii.

### <a name="monitor-disaster-recovery-replication"></a>Monitorowanie replikacji odzyskiwania po awarii

Można monitorować stan replikacji magazynu postęp za wykonywania skryptu `azure_hana_replication_status.pl`. Ten skrypt należy uruchomić z jednostki uruchomionych w lokalizacji odzyskiwania po awarii, które działają zgodnie z oczekiwaniami. Skrypt działa niezależnie od tego, czy replikacja jest aktywna. Skrypt może działać dla każdej jednostki wystąpienia dużych HANA dzierżawy w lokalizacji odzyskiwania po awarii. Nie można użyć w celu uzyskania szczegółowych informacji dotyczących woluminu rozruchowego.

Wywołanie skryptu za pomocą tego polecenia:
```
./replication_status.pl <HANA SID>
```

Dane wyjściowe jest dzielona według woluminu na następujące sekcje:  

- Stan linku
- Bieżące działanie replikacji
- Najnowszą migawkę replikowane 
- Rozmiar najnowszą migawkę
- Bieżący czas opóźnienia między migawek (od ostatniej replikacji migawki ukończone i teraz)

Stan łącza jest pokazywana jako **Active** chyba że łącza między lokacjami nie działa lub jest zdarzeniem obecnie wykonywana trybu failover. Działanie replikacji adresów żadnych danych jest obecnie replikowana lub jest w stanie bezczynności lub innych działań są aktualnie wykonywane łącza. Ostatnia migawka replikowane tylko powinny się wyświetlać jako `snapmirror…`. Następnie zostanie wyświetlony rozmiar ostatnia migawka. Na koniec jest wyświetlany czas opóźnienia. Czas opóźnienia reprezentuje czas zaplanowanej replikacji, aby po zakończeniu replikacji. Opóźnienie może być większa niż godzinę replikacji danych, szczególnie w przypadku replikacji początkowej, mimo że replikacja została uruchomiona. Czas opóźnienia w dalszym ciągu zwiększyć zakończenie trwającej replikacji.

Poniżej przedstawiono przykład danych wyjściowych:

```
hana_data_hm3_mnt00002_t020_dp
-------------------------------------------------
Link Status: Broken-Off
Current Replication Activity: Idle
Latest Snapshot Replicated: snapmirror.c169b434-75c0-11e6-9903-00a098a13ceb_2154095454.2017-04-21_051515
Size of Latest Snapshot Replicated: 244KB
Current Lag Time between snapshots: -   ***Less than 90 minutes is acceptable***
```












