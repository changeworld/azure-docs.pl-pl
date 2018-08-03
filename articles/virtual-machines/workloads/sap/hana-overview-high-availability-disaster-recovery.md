---
title: Wysoka dostępność i odzyskiwanie po awarii oprogramowania SAP Hana na platformie Azure (duże wystąpienia) | Dokumentacja firmy Microsoft
description: Ustanów wysokiej dostępności i plan odzyskiwania po awarii oprogramowania SAP Hana na platformie Azure (duże wystąpienia)
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
ms.date: 06/27/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 554991c7c0f11a095a11ae24dbb693a1a3ba50fd
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39430124"
---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>Duże wystąpienia SAP HANA wysoką dostępność i odzyskiwanie awaryjne na platformie Azure 

>[!IMPORTANT]
>Ta dokumentacja jest nie można zastąpić dokumentacji administracyjnej platformy SAP HANA lub SAP Notes. Oczekuje się, że czytelnik ma stałych zrozumienie i doświadczeń w administracyjnej platformy SAP HANA oraz operacji, szczególnie w przypadku tematów, tworzenie kopii zapasowych, przywracania, wysokiej dostępności i odzyskiwania po awarii. W tej dokumentacji przedstawiono zrzuty ekranu z SAP HANA Studio. Zawartości, struktury i charakter ekrany narzędzia do administrowania SAP i narzędzi, sami mogą ulec zmianie z platformy SAP HANA wersji.

Należy pamiętać, że wykonujesz kroki i procesy wykonywane w danym środowisku i z platformy HANA wersji i wydań. Niektóre procesy, opisane w tej dokumentacji są uproszczone, aby lepiej zrozumieć ogólny i nie są przeznaczone do służyć jako szczegółowy opis kroków podręczników ostatecznej operacji. Jeśli chcesz utworzyć podręczników operacja konfiguracji usługi, należy do testowania i wykonywania procesów i dokumentowanie procesów związanych z określonej konfiguracji. 


Wysoka dostępność i odzyskiwanie po awarii (DR) to kluczowe aspekty uruchomiony o kluczowym znaczeniu SAP HANA na serwerze usługi Azure (duże wystąpienia). Należy do pracy z SAP, integratora systemów lub Microsoft prawidłowo zaprojektować i zaimplementować odpowiednie wysokiej dostępności i strategie odzyskiwania po awarii. Należy również wziąć pod uwagę cel punktu odzyskiwania (RPO) i cel czasu odzyskiwania, które są specyficzne dla danego środowiska.

Firma Microsoft obsługuje niektóre funkcje wysokiej dostępności platformy SAP HANA z dużymi wystąpieniami platformy HANA. Te funkcje obejmują:

- **Replikacja magazynu**: system magazynowania możliwość replikowanie wszystkich danych do innej sygnatury dużych wystąpień HANA w innym regionie platformy Azure. SAP HANA działa niezależnie od tej metody. Ta funkcja jest domyślny mechanizm odzyskiwania po awarii, oferowana w przypadku dużych wystąpień HANA.
- **Replikacji systemu HANA**: [replikacji wszystkich danych na platformie SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html) do oddzielnego systemu SAP HANA. Cel czasu odzyskiwania jest zminimalizowany przez funkcję replikacji danych w regularnych odstępach czasu. SAP HANA obsługuje asynchroniczne, synchronicznej tryby w pamięci i synchroniczne. Synchroniczne tryb jest używany tylko w przypadku systemów SAP HANA, które znajdują się w tym samym centrum danych lub mniej niż 100 km od siebie. W bieżącym projekcie sygnatury dużych wystąpień HANA replikacji systemu HANA może służyć do wysokiej dostępności w ramach jednego regionu tylko. Replikacji systemu HANA wymaga zwrotny serwer proxy innych firm lub składnik routingu w przypadku konfiguracji odzyskiwania po awarii w innym regionie platformy Azure. 
- **Hostowanie automatyczny tryb failover**: rozwiązania lokalnego odzyskiwania po uszkodzeniu platformy SAP Hana, który stanowi alternatywę dla replikacji systemu HANA. Jeśli węzła głównego staje się niedostępny, skonfiguruj rezerwy co najmniej jeden węzeł oprogramowania SAP HANA w trybie skalowalnego w poziomie i SAP HANA automatycznie przełącza się do węzła wstrzymania.

Oprogramowanie SAP HANA na platformie Azure (duże wystąpienia) jest oferowana w dwóch regionach platformy Azure w czterech obszarów geopolitycznych (USA, Australia, Europie i Japonia). Dwoma regionami w regionie geopolitycznym, obsługujące sygnatury dużych wystąpień HANA są podłączone do oddzielnej sieci dedykowanej obwodów. Są one używane do replikacji migawek magazynu zapewnienie metody odzyskiwania po awarii. Replikacja nie zostanie nawiązane domyślnie, ale jest skonfigurowane dla klientów, którzy kolejność funkcji odzyskiwania po awarii. Replikacja magazynu jest zależny od użycia magazynu migawek dla dużych wystąpień HANA. Nie jest możliwe wybrać region platformy Azure jako regionie odzyskiwania po awarii, który znajduje się w różnych obszarze geopolitycznych. 

W poniższej tabeli przedstawiono aktualnie obsługiwany wysoką dostępność i odzyskiwanie po awarii metod odzyskiwania i kombinacji:

| Scenariusz jest obsługiwany w dużych wystąpień HANA | Opcję wysokiej dostępności | Opcję odzyskiwania po awarii | Komentarze |
| --- | --- | --- | --- |
| Jeden węzeł | Nie jest dostępna. | Dedykowany Konfiguracja odzyskiwania po awarii.<br /> Konfigurowanie odzyskiwania po awarii Multipurpose. | |
| Hostowanie automatyczny tryb failover: skalowalnego w poziomie (z lub bez w stanie wstrzymania)<br /> w tym 1 + 1 | Możliwa biorąc aktywną rolę w stanie wstrzymania.<br /> HANA kontrolki przełącznika roli. | Dedykowany Konfiguracja odzyskiwania po awarii.<br /> Konfigurowanie odzyskiwania po awarii Multipurpose.<br /> Synchronizacja odzyskiwania po awarii przy użyciu funkcji replikacji magazynu. | Zestawy platformy HANA są dołączone do wszystkich węzłów.<br /> Odzyskiwanie po awarii lokacji musi mieć taką samą liczbę węzłów. |
| Replikacji systemu HANA | Możliwa podstawowej lub dodatkowej konfiguracji.<br /> Pomocniczy przenosi podstawową rolą w przypadku pracy awaryjnej.<br /> Replikacji systemu HANA i system operacyjny sterujący trybu failover. | Dedykowany Konfiguracja odzyskiwania po awarii.<br /> Konfigurowanie odzyskiwania po awarii Multipurpose.<br /> Synchronizacja odzyskiwania po awarii przy użyciu funkcji replikacji magazynu.<br /> Odzyskiwanie po awarii za pomocą replikacji systemu HANA nie jest jeszcze możliwe bez składników innych firm. | Osobny zestaw woluminów dysków, które są dołączone do każdego węzła.<br /> Tylko dysku woluminy repliki pomocniczej w lokacji produkcyjnej są replikowane do lokalizacji odzyskiwania po awarii.<br /> Jeden zestaw woluminów jest wymagany w lokacji odzyskiwania po awarii. | 

Dedykowanej konfiguracji odzyskiwania po awarii jest, gdzie jednostka duże wystąpienie oprogramowania HANA w lokacji odzyskiwania po awarii nie jest używany do uruchamiania innych obciążeń lub system nieprodukcyjnych. Jednostka jest w stanie pasywnym i jest wdrażana tylko wtedy, gdy tryb failover po awarii jest wykonywany. Jednak ta konfiguracja nie jest preferowanych przez dla wielu klientów.

Zapoznaj się [HLI obsługiwane scenariusze](hana-supported-scenario.md) na naukę szczegółów sieci ethernet i układ pamięci masowej dla architektury.

> [!NOTE]
> [SAP HANA MCOD wdrożeń](https://launchpad.support.sap.com/#/notes/1681092) (wiele wystąpień HANA w jednej jednostce) nałożenie scenariusze pracy o wysokiej dostępności i odzyskiwania po awarii metody wymienione w tabeli. Wyjątkiem jest użycie replikacji systemu HANA z klastrem automatycznej pracy awaryjnej, w oparciu o program Pacemaker. Takiej sytuacji obsługuje tylko jedno wystąpienie oprogramowania HANA na jednostkę. Aby uzyskać [SAP HANA MDC](https://launchpad.support.sap.com/#/notes/2096000) wdrożeń, tylko systemem magazynu o wysokiej dostępności i odzyskiwania po awarii metody działają, jeśli więcej niż jednej dzierżawy zostanie wdrożona. Za pomocą jednej dzierżawy wdrożone wszystkie metody wymienione są prawidłowe.  

Multipurpose konfiguracji odzyskiwania po awarii jest, gdzie jednostka duże wystąpienie oprogramowania HANA w lokacji odzyskiwania po awarii jest uruchamiany obciążeń nieprodukcyjnych. W razie awarii zamykania systemu nieprodukcyjnych instalowanie zestawów replikowany magazyn woluminów (dodatkowe), a następnie uruchom wystąpienie oprogramowania HANA w środowisku produkcyjnym. Większość klientów, którzy korzystają z funkcji odzyskiwania po awarii dużych wystąpień HANA używać tej konfiguracji. 


Więcej informacji na temat oprogramowania SAP HANA wysokiej dostępności można znaleźć w następujących artykułach SAP: 

- [Oficjalny dokument dotyczący programu SAP HANA wysoką dostępność](http://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [Przewodnik administratora programu SAP HANA](http://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [SAP HANA Academy wideo na replikacji systemu SAP HANA](http://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [Obsługa Uwaga #1999880 — często zadawane pytania dotyczące replikacji systemu SAP HANA SAP](https://apps.support.sap.com/sap/support/knowledge/preview/en/1999880)
- [SAP Uwaga pomocy technicznej 2165547 # — SAP HANA tworzenie kopii zapasowej i przywracania w środowisku replikacji systemu HANA SAP](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [SAP Uwaga pomocy technicznej 1984882 # — za pomocą replikacji systemu SAP HANA dla wymiany sprzętu z co najmniej/żadnych przestojów](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="network-considerations-for-disaster-recovery-with-hana-large-instances"></a>Zagadnienia dotyczące sieci na potrzeby odzyskiwania po awarii przy użyciu dużych wystąpień HANA

Aby móc korzystać z funkcji odzyskiwania po awarii w dużych wystąpień HANA, należy zaprojektować połączenie sieciowe z dwóch regionach platformy Azure. Konieczne jest połączenie obwodu ExpressRoute systemu Azure ze środowiska lokalnego w główny region platformy Azure i inne połączenie obwodu ze środowiska lokalnego do regionu odzyskiwania po awarii. Ta miara dotyczy sytuacji, w którym występuje problem w regionie platformy Azure, w tym miejscu rozwiązania Microsoft Enterprise krawędzi routera (MSEE).

Jako druga Metryka używana możesz połączyć wszystkie sieci wirtualne platformy Azure, które łączą z platformą SAP HANA na platformie Azure (duże wystąpienia) w jednym regionie z obwodem usługi ExpressRoute, która łączy się z dużymi wystąpieniami platformy HANA w innym regionie. Dzięki temu *krzyżowe połączyć*, usług działających na platformie Azure sieci wirtualnej w regionie 1 podłączeniem jednostki duże wystąpienie oprogramowania HANA w regionie 2 i odwrotnie. Ta miara obsługuje przypadek, w którym tylko jeden lokalizacje MSEE, który nawiązuje połączenie z lokalizacji lokalnej z platformą Azure przejdzie do trybu offline.

Poniższa ilustracja przedstawia odporne na błędy konfiguracji w przypadku odzyskiwania po awarii:

![Optymalną konfigurację odzyskiwania po awarii](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)



## <a name="other-requirements-with-hana-large-instances-storage-replication-for-disaster-recovery"></a>Inne wymagania przy użyciu dużych wystąpień HANA replikacji magazynu do odzyskiwania po awarii

Oprócz poprzedniego wymaganiami Instalator odzyskiwania po awarii przy użyciu dużych wystąpień HANA musisz mieć:

- Kolejność platformy SAP HANA na platformie Azure (duże wystąpienia) jednostek SKU w taki sam rozmiar jak jednostki SKU w środowisku produkcyjnym i wdróż je w regionie odzyskiwania po awarii. W bieżącym wdrożeń klienta te wystąpienia są używane do uruchamiania wystąpień HANA nieprodukcyjnych. Te konfiguracje są określane jako *multipurpose konfiguracji odzyskiwania po awarii*.   
- Kolejność dodatkowy magazyn w lokacji odzyskiwania po awarii dla każdego środowiska SAP HANA na platformie Azure (duże wystąpienia) jednostek SKU, które mają zostać odzyskane w lokacji odzyskiwania po awarii. Kupowania dodatkowego miejsca do magazynowania pozwala przydzielić odpowiedniej wielkości magazynu. Można alokować woluminy, które są celem replikacji magazynu z w środowisku produkcyjnym region platformy Azure do odzyskiwania po awarii w regionie platformy Azure.
- W przypadku, gdy skonfigurowano replikacji systemu HANA na maszynie podstawowej i Konfigurowanie replikacji magazynu opartego na lokacji odzyskiwania po awarii, musi zakupić dodatkowy magazyn w lokacji odzyskiwania po awarii więc podstawowych i dodatkowych węzłów danych pobiera replikowane do lokacji odzyskiwania po awarii.

 

## <a name="backup-and-restore"></a>Tworzenie kopii zapasowej i przywracanie

Jednym z najważniejszych aspektów, które operacyjnej bazy danych jest aby chronić je przed katastrofami. Przyczyny te zdarzenia może być dowolna przed klęskami żywiołowymi błędy proste użytkownika.

Tworzenie kopii zapasowej bazy danych, z możliwością przywrócenia go do dowolnego punktu w czasie (takie jak przed usunięciem ktoś krytycznych danych), umożliwia przywracanie do stanu, który jest możliwie najbliżej sposób ich zakłócenie.

Dwa typy kopii zapasowych należy wykonać w celu uzyskania najlepszych wyników:

- Kopie zapasowe bazy danych: pełne, przyrostowych lub różnicowej kopii zapasowych
- Kopie zapasowe dziennika transakcji

Oprócz bazy danych na pełne kopie zapasowe wykonywane na poziomie aplikacji można wykonywać kopie zapasowe przy użyciu migawek magazynu. Migawek magazynu, nie zastępuj kopie zapasowe dziennika transakcji. Kopie zapasowe dziennika transakcji nadal ważne, aby przywrócić bazę danych do pewnego momentu w czasie lub pustą dzienniki transakcji już zatwierdzone. Jednakże migawek magazynu można co pozwala przyspieszyć odzyskiwanie szybko udostępniając obrazem przodu bazy danych. 

Oprogramowanie SAP HANA na platformie Azure (duże wystąpienia) oferuje dwie opcje tworzenia kopii zapasowych i przywracania:

- Zrób to sam (DIY). Po obliczeniu upewnij się, że istnieje wystarczająca ilość miejsca na dysku, należy wykonać pełnej bazy danych i kopie zapasowe dziennika przy użyciu jednej z następujących metod tworzenia kopii zapasowej dysku. Kopię zapasową można wykonywać bezpośrednio w woluminach dołączony do jednostek dużych wystąpień HANA lub do sieciowych udziałów plików (NFS), skonfigurowanej maszyny wirtualnej (VM) platformy Azure. W tym ostatnim przypadku klientom Konfigurowanie maszyny Wirtualnej z systemem Linux na platformie Azure, Połącz z maszyną Wirtualną usługi Azure Storage i współużytkują magazyn za pośrednictwem serwera systemu plików NFS skonfigurowanych na tej maszynie Wirtualnej. Jeśli wykonanie kopii zapasowej dla woluminów, które bezpośrednio dołączania do dużych wystąpień HANA jednostek, należy skopiować kopie zapasowe do konta usługi Azure storage (po skonfigurowaniu maszyny Wirtualnej platformy Azure, które eksportuje udziałów NFS, które są oparte na usłudze Azure Storage). Można również użyć magazynu usługi Azure backup lub Azure zimnego magazynu. 

   Innym rozwiązaniem jest na potrzeby przechowywania kopii zapasowych, po ich skopiowaniu konta usługi Azure storage to narzędzie ochrony danych innych firm. Możesz opcji tworzenia kopii zapasowej może być również niezbędnych dla danych, które będą przechowywane przez dłuższy czas do celów inspekcji i zgodności. We wszystkich przypadkach kopie zapasowe są kopiowane do udziałów NFS, reprezentowane przez maszynę Wirtualną i usługi Azure Storage.

- Infrastruktura kopii zapasowej i przywracania oferowane. Można również użyć kopii zapasowej i przywrócenia jej funkcjonalności, który zapewnia podstawową infrastrukturą platformy SAP HANA na platformie Azure (duże wystąpienia). Ta opcja spełnia potrzeby kopii zapasowych oraz szybkie ich przywrócenie. Pozostałej części tej sekcji eliminuje funkcje i przywracania kopii zapasowych, które jest oferowana z dużymi wystąpieniami platformy HANA. W tej sekcji omówiono również relacji kopii zapasowej i przywracania musi po awarii odzyskiwania funkcjonalność oferowana przez dużych wystąpień HANA.

>   [!NOTE]
>   Technologia migawki, która jest używana przez podstawową infrastrukturą dużych wystąpień HANA zależny od oprogramowania SAP HANA migawki. W tym momencie migawek platformy SAP HANA nie działają w połączeniu z wieloma dzierżawami kontenery wielodostępne bazy danych SAP HANA. Jeśli tylko jednej dzierżawy zostanie wdrożona, migawek platformy SAP HANA będą działać, a ta metoda może być używana.

### <a name="using-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Przy użyciu migawek magazynu oprogramowania SAP Hana na platformie Azure (duże wystąpienia)

Infrastruktura magazynu, podstawowe oprogramowanie SAP HANA na platformie Azure (duże wystąpienia) obsługuje Magazyn migawek woluminów. Kopia zapasowa i Przywracanie woluminów jest obsługiwane przez następujące kwestie:

- Zamiast tworzenia pełnych kopii zapasowych migawek woluminu magazynu są pobierane częste.
- Podczas wyzwalania migawki /hana/data i /hana/shared (w tym /usr/sap) woluminów, technologia migawki inicjuje migawki przed rozpoczęciem wykonywania migawek magazynu oprogramowania SAP HANA. Ta migawka platformy SAP HANA jest punktem instalacji dla przywracana ostateczna dziennika po odzyskaniu migawki magazynu. Migawka HANA zakończy się powodzeniem wymaga aktywnego wystąpienia platformy HANA.  W scenariuszu HSR migawki magazynu nie jest obsługiwana w bieżącym węźle pomocniczego, których nie można wykonać migawki platformy HANA.
- Po migawki magazynu została wykonana pomyślnie, migawka platformy SAP HANA jest usuwana.
- Kopie zapasowe dziennika transakcji są często wykonywane i są przechowywane w woluminie /hana/logbackups lub na platformie Azure. Możesz wyzwolić woluminu /hana/logbackups, który zawiera kopie zapasowe dziennika transakcji, aby zrobić migawkę oddzielnie. W takiej sytuacji nie trzeba wykonać migawki platformy HANA.
- Należy przywrócić bazę danych do pewnego momentu w czasie, podczas przywracania Zarządzanie usługami platformy Azure do migawki magazynu zażądać tej obsługi platformy Microsoft Azure (w przypadku awarii produkcji) lub SAP HANA. Przykładem jest planowane Przywracanie systemu piaskownicy do stanu pierwotnego.
- Migawka platformy SAP HANA, który znajduje się w tle magazynu jest punkt przesunięcia stosowania kopie zapasowe dziennika transakcji, które zostały wykonane i przechowywane po migawki magazynu.
- Te kopie zapasowe dziennika transakcji przejście do przywrócenia bazy danych do pewnego momentu w czasie.

Można wykonywać migawek magazynu, przeznaczone dla trzech klas woluminów:

- Połączone migawkę/hana/dane oraz /hana/shared (obejmuje/usr/sap). Ta migawka wymaga utworzenia migawki platformy SAP HANA jako przygotowania do migawki magazynu. Migawki platformy SAP HANA sprawdza, czy baza danych jest w stanie spójnym z punktu widzenia magazynu. I że do przywrócenia przetwarzania oznacza to punkt, aby ustawić działa na.
- Oddzielne migawkę za pośrednictwem/hana/logbackups.
- Partycja systemu operacyjnego.

Pobierz najnowsze skrypty migawki i dokumentacji [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts). 

### <a name="storage-snapshot-considerations"></a>Zagadnienia dotyczące migawek magazynu

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

### <a name="prerequisites-for-using-self-service-storage-snapshots"></a>Wymagania wstępne dotyczące korzystania z migawek magazynu samoobsługi

Aby upewnić się, czy skrypt migawka została wykonana pomyślnie, upewnij się, że Perl jest zainstalowany w systemie operacyjnym Linux na serwerze dużych wystąpień HANA. Perl jest wstępnie zainstalowany w Twojej jednostce dużych wystąpień HANA. Aby sprawdzić wersję środowiska Perl, użyj następującego polecenia:

`perl -v`

![Klucz publiczny jest kopiowany, uruchamiając następujące polecenie](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


### <a name="set-up-storage-snapshots"></a>Konfigurowanie migawek magazynu

Aby skonfigurować migawek magazynu przy użyciu dużych wystąpień HANA, wykonaj następujące kroki:
1. Upewnij się, że Perl jest zainstalowany w systemie operacyjnym Linux na serwerze dużych wystąpień HANA.
1. Modyfikowanie/etc/ssh/ssh\_konfiguracji, aby dodać wiersz _Mac hmac-sha1_.
1. W węźle głównym dla każdego wystąpienia platformy SAP HANA, którą pracujesz, należy utworzyć konto kopii zapasowej platformy SAP HANA, jeśli ma to zastosowanie.
1. Zainstaluj klienta SAP HANA HDB na wszystkich serwerach duże wystąpienia SAP HANA.
1. Na pierwszym serwerze duże wystąpienia SAP HANA każdego regionu należy utworzyć klucz publiczny do dostępu do podstawowej infrastruktury magazynu, który kontroluje tworzenia migawki.
1. Skopiuj skrypty i pliku konfiguracji z [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts) do lokalizacji **hdbsql** w instalacji oprogramowania SAP HANA.
1. Modyfikowanie *HANABackupDetails.txt* plików, gdy jest to konieczne do specyfikacji odpowiedniego klienta.

Pobierz najnowsze skrypty migawki i dokumentacji [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts). 

### <a name="consideration-for-mcod-scenarios"></a>Ważną kwestią dotyczącą MCOD scenariuszy
Jeśli korzystasz z [scenariusza MCOD](https://launchpad.support.sap.com/#/notes/1681092) z wieloma wystąpieniami platformy SAP HANA w jednej jednostce dużych wystąpień HANA mieć osobne woluminy magazynu zainicjowana obsługa administracyjna dla wszystkich wystąpień oprogramowania SAP HANA. W bieżącej wersji automatyzacji samoobsługi migawki nie można zainicjować oddzielne migawek w systemie wystąpienia każdej platformy HANA identyfikator. Funkcje zapewnia sprawdza, czy zarejestrowanych wystąpień oprogramowania SAP HANA serwera w pliku konfiguracji (zobacz w dalszej części tego artykułu) i wykonuje migawek równocześnie woluminów wszystkich wystąpień, które są zarejestrowane w jednostce.
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>Krok 1: Instalowanie klienta SAP HANA HDB

System operacyjny Linux, które są zainstalowane na platformie SAP HANA na platformie Azure (duże wystąpienia) zawiera foldery i skrypty wymagane do wykonania migawek magazynu oprogramowania SAP HANA do celów odzyskiwania kopii zapasowych i odzyskiwanie po awarii. Wyszukaj więcej najnowsze wersje w [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts). Najbardziej aktualną wersję wydania skryptów jest 3.x. Różne skrypty mogą mieć różne wersje pomocnicze w ramach tej samej wersji głównej.

>[!IMPORTANT]
>Podczas przenoszenia w wersji 2.1 w wersji 3.x skryptów, należy pamiętać, że struktura pliku konfiguracji i niektóre przypadki składni uległa zmianie. Zobacz objaśnienia w sekcjach określone. 

Jest odpowiedzialny za do zainstalowania klienta SAP HANA HDB w jednostkach dużych wystąpień HANA, podczas instalowania platformy SAP HANA.

### <a name="step-2-change-the-etcsshsshconfig"></a>Krok 2: Zmień/etc/ssh/ssh\_konfiguracji

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
  ssh-keygen –t dsa –b 1024
```
Nowa lokalizacja jest **_/root/.ssh/id\_dsa.pub**. Nie należy wprowadzać hasło; w przeciwnym razie należy wprowadzać hasła za każdym razem, logowania. Zamiast tego należy wybrać **Enter** dwa razy, aby usunąć wymaganie "Wprowadź hasło" do logowania.

Upewnij się, że klucz publiczny został rozwiązany, zgodnie z oczekiwaniami, zmieniając folderów **/root/.ssh/** , a następnie wykonywanie `ls` polecenia. Jeśli klucz jest obecny, skopiuj go, uruchamiając następujące polecenie:

![Klucz publiczny jest kopiowany, uruchamiając następujące polecenie](./media/hana-overview-high-availability-disaster-recovery/image2-public-key.png)

W tym momencie skontaktować się z oprogramowania SAP HANA na zarządzanie usługami platformy Azure i je podać przy użyciu klucza publicznego. Przedstawiciela biura obsługi używa klucza publicznego, aby zarejestrować go w podstawowej infrastruktury magazynu, który jest używać dla Twojej dzierżawy dużych wystąpień HANA.

### <a name="step-4-create-an-sap-hana-user-account"></a>Krok 4: Tworzenie konta użytkownika platformy SAP HANA

Aby zainicjować tworzenie migawek platformy SAP HANA, należy utworzyć konto użytkownika w, można użyć skryptów migawek magazynu oprogramowania SAP HANA. Tworzenie konta użytkownika platformy SAP HANA w obrębie SAP HANA Studio, w tym celu. Musi zostać utworzona użytkownika, w obszarze SYSTEMDB, a nie w bazie danych identyfikatora SID dla MDC. W środowisku jednego kontenera użytkownika jest skonfigurowana, w ramach bazy danych dzierżaw. To konto musi mieć następujące uprawnienia: **administratora kopii zapasowych** i **odczytu katalogu**. W tym przykładzie nazwa użytkownika to **SCADMIN**. Nazwa konta użytkownika, które są utworzone w programie Studio HANA jest uwzględniana wielkość liter. Upewnij się, że wybrano **nie** wymagająca użytkownikowi na zmianę hasła dla ich następnym zalogowaniu.

![Tworzenie użytkownika w programie Studio HANA](./media/hana-overview-high-availability-disaster-recovery/image3-creating-user.png)

Użycie MCOD wdrożeń z wieloma wystąpieniami platformy SAP HANA w jednej jednostce, należy powtórzyć ten krok dla każdego wystąpienia platformy SAP HANA.

### <a name="step-5-authorize-the-sap-hana-user-account"></a>Krok 5: Autoryzowania konta użytkownika platformy SAP HANA

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

- **Azure\_hana\_backup.pl**: ten skrypt jest zaplanowane przy użyciu narzędzia do planowania Cron w systemie Linux do wykonywania migawek magazynu danych HANA i udostępnione woluminy, / hana/logbackups woluminu lub systemu operacyjnego.
- **Azure\_hana\_replikacji\_status.pl**: ten skrypt zawiera podstawowe informacje dotyczące stanu replikacji z lokacji produkcyjnej do lokacji odzyskiwania po awarii. Monitory skryptu, aby upewnić się, że replikacja odbywa się i pokazuje, że rozmiar elementów są replikowane. Zawiera również wskazówki, jeśli replikacji trwa zbyt długo lub łącze jest wyłączona.
- **Azure\_hana\_migawki\_details.pl**: ten skrypt zawiera listę podstawowe szczegóły dotyczące wszystkie migawki, na każdym woluminie, które istnieją w Twoim środowisku. Ten skrypt można uruchomić na serwerze podstawowym lub w jednostce server w lokalizacji odzyskiwania danych po awarii. Skrypt zawiera następujące informacje, w rozbiciu na każdy wolumin, który zawiera migawki:
   * Rozmiar całkowitej migawek woluminu
   * Następujące informacje w każdej migawki w danym woluminie: 
      - Nazwa migawki 
      - Czas utworzenia 
      - Rozmiar migawki
      - Częstotliwość migawek
      - Identyfikator kopii zapasowej HANA skojarzone z tej migawki, jeśli jest to odpowiednie
- **Azure\_hana\_migawki\_delete.pl**: ten skrypt spowoduje usunięcie migawek magazynu lub zestawu migawek. Można użyć identyfikator kopii zapasowej platformy SAP HANA, tak jak w programie HANA Studio lub nazwy migawki magazynu. Obecnie identyfikator kopii zapasowej jest tylko powiązane migawek utworzonych dla platformy HANA danych/log/udostępnione woluminy. W przeciwnym razie jeśli zostanie wprowadzony identyfikator migawki zmierza wszystkie migawki, które odpowiadają identyfikator migawki wprowadzony.  
- **testHANAConnection.pl**: ten skrypt testuje połączenie z wystąpieniem platformy SAP HANA i jest wymagane do skonfigurowania migawek magazynu.
- **testStorageSnapshotConnection.pl**: ten skrypt ma dwa cele. Najpierw gwarantuje, że jednostki dużych wystąpień HANA, która uruchamia skrypty ma dostęp do przypisanych magazynu maszyny wirtualnej i interfejs migawki magazynu dużych wystąpień HANA. Drugi cel to można utworzyć tymczasowego migawki wystąpienia HANA, które testujesz. Ten skrypt powinien być uruchamiany dla każdego wystąpienia oprogramowania HANA na serwerze, aby upewnić się, że skrypty kopii zapasowej działać zgodnie z oczekiwaniami.
- **removeTestStorageSnapshot.pl**: ten skrypt usuwa migawkę testu, utworzone za pomocą skryptu **testStorageSnapshotConnection.pl**.
- **Azure\_hana\_odzyskiwania po awarii\_failover.pl**: ten skrypt przełączenia w tryb failover odzyskiwania po awarii w innym regionie. Skrypt musi być wykonywane na jednostce dużych wystąpień HANA w regionie odzyskiwania po awarii, lub w jednostce ma do trybu failover. Ten skrypt zatrzymanie replikacji magazynu ze strony głównej stronie dodatkowej, przywrócenie najnowszej migawki w woluminach odzyskiwania po awarii i zapewnia punkty instalacji do odzyskiwania po awarii woluminów.
- **Azure\_hana\_test\_odzyskiwania po awarii\_failover.pl**: ten skrypt wykonuje test trybu failover do lokacji odzyskiwania po awarii. W przeciwieństwie do skryptu azure_hana_dr_failover.pl to wykonanie nie przerywa działania replikacji magazynu z podstawowych do pomocniczych. Zamiast tego klony woluminów replikowanego magazynu są tworzone po stronie odzyskiwania po awarii i znajdują się punkty instalacji woluminów sklonowane. 
- **HANABackupCustomerDetails.txt**: ten plik jest plikiem można modyfikować konfiguracji, który chcesz zmodyfikować, aby dostosować je do konfiguracji oprogramowania SAP HANA. *HANABackupCustomerDetails.txt* plik jest plikiem formantu i konfiguracja dla skryptu, który uruchamia migawek magazynu. Dostosuj plik dla instalacji i celów. Pojawi się **Nazwa kopii zapasowej magazynu** i **adres IP magazynu** z platformy SAP HANA na zarządzanie usługami platformy Azure po wdrożeniu wystąpień. Nie można zmodyfikować sekwencji, zamawiania lub odstępy tych zmiennych w tym pliku. Jeśli to zrobisz, skryptów, nie działają poprawnie. Ponadto zostanie wyświetlony adres IP węzła skalowanie w górę lub węzła głównego (jeśli skalowalnego w poziomie) z platformy SAP HANA na zarządzanie usługami platformy Azure. Znasz także liczby wystąpień HANA, której można korzystać podczas instalacji oprogramowania SAP HANA. Teraz należy dodać nazwę kopii zapasowej do pliku konfiguracji.

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
- **HANA**: w którym woluminów, które zawierają/hana/data i/hana/udostępnione (który zawiera także /usr/sap) są objęte skoordynowanego migawki kopii zapasowej migawki połączone. Przywracanie pojedynczego pliku jest możliwe z tą migawką.
- **Dzienniki**: kopii zapasowej migawki woluminu logbackups/hana /. Nie migawki HANA zostanie wywołany do wykonywania tej migawki magazynu. Oznacza, że ten wolumin magazynu zawiera kopie zapasowe dziennika transakcji platformy SAP HANA. Są one wykonywane częściej ograniczyć wzrostu dzienników i zapobiegania utracie danych. Przywracanie pojedynczego pliku jest możliwe z tą migawką. Nie zmniejszyć częstotliwość mniej niż 3 minut.
- **Rozruch**: migawki woluminu, który zawiera numer jednostki logicznej (LUN) rozruchu dużych wystąpień HANA. Ta kopia zapasowa migawki jest możliwe tylko w przypadku typu I jednostki SKU z dużymi wystąpieniami platformy HANA. Nie można wykonać pojedynczy plik Przywracanie z migawki woluminu, który zawiera rozruchu jednostki LUN.


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
- Parametr **<HANA Large Instance Type>** wymagane do rozruchu kopie zapasowe woluminów tylko. Dwóch prawidłowych wartości "TypeI" lub "TypeII" są zależne od HANA duże wystąpienia jednostki. Aby dowiedzieć się, jaki typ urządzenia, zobacz [platformy SAP HANA (duże wystąpienia) — omówienie i architektura na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).  
- Parametr **< snapshot_prefix >** migawki lub etykieta kopii zapasowej dla typu migawka. Ma dwa cele: jeden jest nadaj jej nazwę, dzięki czemu będzie wiadomo, o ile te migawki. Drugi cel to skrypt *azure\_hana\_backup.pl* do określenia liczby migawek magazynu, które są przechowywane w ramach tej określonej etykiety. Jeśli zaplanować dwie kopie zapasowe migawek magazynu tego samego typu (takich jak **hana**), za pomocą dwóch różnych etykiet i zdefiniuj, czy powinny być przechowywane 30 migawek dla każdego, na końcu 60 migawek magazynu woluminów, których to dotyczy. Alfanumeryczne ("A – Z, a-z, 0 – 9"), podkreślenia ("_") i myślnik ("-") może zawierać znaków. 
- Parametr **< snapshot_frequency >** jest zarezerwowane dla przyszłego rozwoju i nie ma żadnego wpływu. Ustaw ją na "3min" podczas wykonywania kopii zapasowych typu **dziennika**i "15 min" podczas wykonywania kopii zapasowej innych typów.
- Parametr **<number of snapshots retained>** definiuje przechowywania migawek pośrednio przez zdefiniowanie liczby migawek z tym samym prefiksem migawki (etykieta). Ten parametr jest ważne w przypadku zaplanowanego wykonania za pomocą usługi cron. Jeśli liczba migawki za pomocą tego samego snapshot_prefix przekracza wartość podana w tym parametrze, najstarsza migawka jest usuwana przed wykonaniem nową migawkę pamięci masowej.

W przypadku skalowania w poziomie skrypt wykonuje dodatkowego sprawdzenia, czy można uzyskać dostępu do wszystkich serwerów HANA. Skrypt sprawdza również, że wszystkich wystąpień HANA zwraca odpowiedni stan wystąpienia, zanim utworzy migawkę platformy SAP HANA. Migawka platformy SAP HANA następuje migawki magazynu.

Wykonywanie skryptu `azure_hana_backup.pl` tworzy Magazyn migawek na następujące trzy etapy:

1. Wykonuje migawkę platformy SAP HANA
1. Wykonuje migawkę pamięci masowej
1. Usuwa migawkę platformy SAP HANA, który został utworzony przed wykonaniem migawki magazynu

Aby wykonać skrypt, należy wywołać go z folderu pliku wykonywalnego HDB, do którego została skopiowana. 

Okres przechowywania jest podawana przy użyciu liczby migawek, które są przesyłane jako parametr podczas wykonywania skryptu. Ilość czasu, który pasuje do żadnego migawek magazynu jest funkcją czasu wykonania i liczby migawek przesyłany w postaci parametru podczas wykonywania skryptu. W przypadku liczby migawek, które są utrzymywane przekracza liczbę, które są nazywane jako parametr w wywołaniu skryptu, najstarsze migawki magazynu w tej samej etykiety zostaną usunięte, zanim zostanie wykonany nową migawkę. Liczba zapewniają jako ostatni parametr wywołanie jest liczba służących do kontrolowania liczby migawek, które są zachowane. Z tym numerem można także kontrolować, pośrednio, miejsca na dysku używanego dla migawki. 

> [!NOTE]
>Zaraz po zmianie etykiety liczenie zostanie ponownie uruchomiona. Trzeba strict metodach etykietowania, dzięki czemu Twoje migawek nie zostaną przypadkowo usunięte.

### <a name="snapshot-strategies"></a>Strategie migawki
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
00,05,10,15,20,25,30,35,40,45,50,55 * * * *  Perform SAP HANA transaction log backup
22 12 * * *  ./azure_hana_backup.pl log dailylogback 3min 28
30 00 * * *  ./azure_hana_backup.pl boot TypeI dailyboot 15min 28
```
W poprzednim przykładzie, jest co godzinę migawki połączone, która obejmuje /hana/shared (obejmuje/usr/sap) i woluminów, które zawierają dane/hana/lokalizacji. Na użytek tego typu migawka szybsze odzyskiwanie w momencie w ciągu ostatnich dwóch dni. Ponadto istnieje dzienną migawkę codziennie na tych woluminach. Dlatego masz dwa dni obowiązywania przez godzinę migawki, a także cztery tygodnie pokrycia, dzienne migawki. Ponadto wolumin kopii zapasowej dziennika transakcji kopia zapasowa jest tworzona codziennie. Te kopie zapasowe są przechowywane także czterech tygodni. Jak widać w trzecim wierszu crontab kopii zapasowej dziennika transakcji HANA jest zaplanowane do wykonania co 5 minut. Godziny rozpoczęcia zadań różnych cron, które są wykonywane migawek magazynu są zróżnicowane, tak, aby te migawki nie są wykonywane w całości w pewnym momencie w czasie. 

W poniższym przykładzie możesz wykonać połączone migawkę, która obejmuje woluminów, które zawierają/hana/data i/hana/udostępnione (takie jak/usr/sap) lokalizacje w systemie godzinowym. Te migawki są przechowywane przez dwa dni. Migawki woluminów kopii zapasowej dziennika transakcji są wykonywane na podstawie 5-minutowych i są przechowywane przez 4 godziny. Jak wcześniej, kopii zapasowej pliku dziennika transakcji HANA jest zaplanowane do wykonania co 5 minut. Migawki woluminu kopii zapasowej dziennika transakcji jest wykonywane z opóźnieniem 2-minutowy po rozpoczęciu kopii zapasowej dziennika transakcji. W ramach tych 2 minuty kopia zapasowa dziennika transakcji platformy SAP HANA powinno zostać zakończone w normalnych warunkach. Jako przed, woluminu rozruchowego jednostki LUN kopia zapasowa jest tworzona raz dziennie migawki magazynu i jest przechowywany przez cztery tygodnie.

```
10 0-23 * * * ./azure_hana_backup.pl hana hourlyhana 15min 48
0,5,10,15,20,25,30,35,40,45,50,55 * * * *  Perform SAP HANA transaction log backup
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup.pl log logback 3min 48
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


### <a name="file-level-restore-from-a-storage-snapshot"></a>Poziom pliku przywracania z migawki magazynu
Dla typów migawki **hana** i **dzienniki**, są dostępne migawki bezpośrednio na woluminach **.snapshot** katalogu. Brak podkatalogu dla każdego z migawki. Możesz skopiować każdego pliku w stanie, w jakim były one na punkcie migawki z tym podkatalogu w strukturze katalogu. W bieżącej wersji skryptu jest **nie** przywrócić skryptu do przywrócenia migawki jako samoobsługi (chociaż może można wykonać przywracanie z migawki, ponieważ element odzyskiwania po awarii samoobsługi skrypty po stronie odzyskiwania po awarii podczas trybu failover). Musisz skontaktować się z zespołem firmy Microsoft operations, otwierając żądanie obsługi, można przywrócić żądanej migawki z istniejących migawek dostępne.

>[!NOTE]
>Pojedynczy plik przywracania nie działa w przypadku migawki rozruchu niezależna od typu jednostki dużych wystąpień HANA jednostki LUN. **.Snapshot** katalogu nie jest widoczny w rozruchu jednostki LUN. 


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

   

### <a name="recover-to-the-most-recent-hana-snapshot"></a>Odzyskaj do najnowszej migawki HANA

W scenariuszu produkcji w dół proces odzyskiwania z migawki magazynu może być inicjowane jako zdarzenia klienta, pomocy technicznej firmy Microsoft Azure. Jeśli dane zostały usunięte w systemie produkcyjnym i jest jedynym sposobem, aby go pobrać, można przywrócić produkcyjną bazę danych, jest kwestią wysoka pilność.

W innej sytuacji w momencie odzyskiwania może być niski pilność i planowane-dniowym wyprzedzeniem. Możesz zaplanować to odzyskiwanie z platformą SAP HANA na zarządzanie usługami platformy Azure, zamiast wywoływania flagi o wysokim priorytecie. Na przykład może być planowane jest uaktualnienie oprogramowania SAP, stosując nowego pakietu rozszerzenia. Następnie należy przywrócić do migawki, który reprezentuje stan przed wykonaniem uaktualnienia pakietu rozszerzenia.

Przed wysłaniem żądania, które trzeba przygotować. SAP HANA w zespole usługi Azure Service Management można obsłużyć żądania i podaj przywracanych woluminów. Potem możesz przywrócić bazy danych HANA, oparte na migawki. 

Poniżej pokazano, jak przygotować się do żądania:

>[!NOTE]
>Interfejs użytkownika może się różnić od poniższych zrzutach ekranu, w zależności od używanej wersji oprogramowania SAP HANA.

1. Zdecyduj, które migawek do przywrócenia. Tylko wolumin hana/danych zostanie przywrócony, chyba że poinstruować w inny sposób. 

1. Zamknij wystąpienie oprogramowania HANA.

 ![Zamknij wystąpienie oprogramowania HANA](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. Odinstaluj woluminy danych w każdym węźle bazy danych HANA. Woluminy danych nadal są zainstalowane w systemie operacyjnym, przywracanie migawki nie powiedzie się.
 ![Odinstaluj woluminy danych w każdym węźle bazy danych HANA](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. Otwórz żądanie pomocy technicznej platformy Azure i zawierają instrukcje informujące o przywrócenie określoną migawkę.

 - Podczas przywracania: oprogramowanie SAP HANA na zarządzanie usługami platformy Azure może poprosić Cię o weź udział w konferencji, do zapewnienia koordynacji, weryfikacji i potwierdzenie, że migawki magazynu poprawną został przywrócony. 

 - Po przywróceniu: oprogramowanie SAP HANA na zarządzanie usługami platformy Azure powiadamia użytkownika, po przywróceniu z migawki magazynu.

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

1. Wykonaj kroki 1 – 6 w [odzyskiwanie do najnowszej migawki HANA](#recovering-to-the-most-recent-hana-snapshot).

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
1. Rozpocznij procedurę opisaną w [odzyskać stan najnowszej](#recovering-to-the-most-recent-state).
1. W kroku 2 procedury w **Określanie typu odzyskiwania** wybierz **odzyskać bazę danych do następujących punktu w czasie**, a następnie określ punkt w czasie. 
1. Wykonaj kroki od 3 do 6.

### <a name="monitor-the-execution-of-snapshots"></a>Monitorowanie wykonania migawki

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


## <a name="disaster-recovery-principles"></a>Zasady odzyskiwania po awarii
Dużych wystąpień HANA oferuje funkcje odzyskiwania po awarii, między sygnatury duże wystąpienie oprogramowania HANA w różnych regionach platformy Azure. Na przykład w przypadku wdrożenia jednostek duże wystąpienie oprogramowania HANA w regionie zachodnie stany USA Azure służy jednostki duże wystąpienie oprogramowania HANA w regionie wschodnie stany USA jako jednostka odzyskiwania po awarii. Jak wspomniano wcześniej, odzyskiwania po awarii nie jest konfigurowana automatycznie, ponieważ wymaga ona płacisz z inną jednostką duże wystąpienie oprogramowania HANA w regionie odzyskiwania po awarii. Instalator odzyskiwania po awarii działa dla ustawień skalowania w górę, a także skalowania w poziomie. 

W scenariuszach wdrożone do tej pory klientów jednostka jest używana w regionie odzyskiwania po awarii do uruchamiania systemów nieprodukcyjnych, które używają zainstalowanego wystąpienia platformy HANA. Jednostka dużych wystąpień HANA musi mieć tej samej jednostki SKU jako jednostki SKU, używane do celów produkcyjnych. Na poniższej ilustracji przedstawiono konfigurację dysku od jednostki serwera w regionie platformy Azure środowiska produkcyjnego i regionie odzyskiwania po awarii wygląda następująco:

![W konfiguracji odzyskiwania po awarii z punktu widzenia dysku](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_setup.PNG)

Jak pokazano na poniższej ilustracji — omówienie, należy następnie kolejność drugi zestaw woluminów dysku. Woluminy dysku docelowego są dostępne w taki sam rozmiar jak wielkości produkcji dla wystąpienia produkcyjnego w jednostkach odzyskiwania po awarii. Te woluminy dysku są skojarzone z jednostki serwera duże wystąpienie oprogramowania HANA w lokacji odzyskiwania po awarii. Następujące woluminy są replikowane z regionu produkcji do lokacji odzyskiwania po awarii:

- / hana/danych
- / hana/logbackups 
- /Hana/Shared (obejmuje/usr/sap)

Wolumin /hana/log nie jest replikowana, ponieważ dziennik transakcji platformy SAP HANA nie jest potrzebna w taki sposób, który odbywa się przywracanie z tych woluminów. 

Podstawy funkcji odzyskiwania po awarii oferowana jest funkcje replikacji magazynu oferowana przez infrastrukturę dużych wystąpień HANA. Funkcje, które jest używane po stronie magazynu nie jest stały strumień zmiany, które są replikowane w sposób asynchroniczny, ponieważ wprowadzenia zmiany do woluminu magazynu. Zamiast tego jest mechanizm, który opiera się na fakcie, że migawki te woluminy są tworzone na bieżąco. Różnica między już replikowanych migawki i nową migawkę, która nie została jeszcze zreplikowana jest następnie przekazywane do lokacji odzyskiwania po awarii na woluminy dysku docelowego.  Te migawki są przechowywane w woluminach, a w przypadku trybu failover odzyskiwania po awarii trzeba przywrócić na tych woluminach.  

Pierwszy transfer pełnych danych woluminu powinien być przed ilość danych staje się mniejszy niż różnic między migawkami. Z tego powodu woluminy w lokacji odzyskiwania po awarii zawierają każdy jeden migawek woluminu wykonywane w lokacji produkcyjnej. Po pewnym czasie można użyć tego systemu odzyskiwania po awarii można pobrać stanu starszych odzyskać utracone dane, bez wycofywania w systemie produkcyjnym.

W przypadku wdrożeń MCOD wielu wystąpieniom niezależnie od oprogramowania SAP HANA w jednej jednostce dużych wystąpień HANA oczekuje się, że wszystkie wystąpienia oprogramowania SAP HANA otrzymują magazynu replikowane do odzyskiwania po awarii.

W przypadku których pełnić funkcji wysokiej dostępności replikacji systemu HANA w przypadku witryn produkcyjnych i używane replikacji na podstawie magazynu dla danej lokacji odzyskiwania po awarii ilości oba węzły w lokacji głównej do wystąpienia usługi odzyskiwania po awarii są replikowane. Należy zakupić dodatkowy magazyn (ten sam rozmiar od węzła podstawowego) w lokacji odzyskiwania po awarii, aby pomieścić replikacji z podstawowego i pomocniczego do odzyskiwania po awarii. 



>[!NOTE]
>Funkcje replikacji magazynu dużych wystąpień HANA jest dublowania i replikacji migawek magazynu. Jeśli nie wykonasz migawek magazynu, w systemie [kopii zapasowej i przywracania](#backup-and-restore) sekcji tego artykułu nie może być dowolnym replikacji do lokacji odzyskiwania po awarii. Wykonanie migawki magazynu to warunek wstępny do magazynu replikacji do lokacji odzyskiwania po awarii.



## <a name="preparation-of-the-disaster-recovery-scenario"></a>Przygotowywanie scenariusza odzyskiwania po awarii
W tym scenariuszu masz systemu produkcyjnego w dużych wystąpieniach HANA w regionie platformy Azure w środowisku produkcyjnym. Aby uzyskać instrukcje, które należy wykonać, załóżmy, że identyfikator SID tego systemu HANA jest "PRD" i że masz systemu nieprodukcyjnych w dużych wystąpieniach HANA w regionie odzyskiwania po awarii Azure. W ostatniej Załóżmy, że jego identyfikator SID jest "TST." Na poniższej ilustracji przedstawiono tę konfigurację:

![Początek konfiguracji odzyskiwania po awarii](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start1.PNG)

Jeśli wystąpienie serwera nie ma jeszcze nie zostało uporządkowane z zestawem woluminu dodatkowego miejsca do magazynowania, SAP HANA na dołącza usługi Azure Service Management dodatkowego zestawu woluminów jako element docelowy dla repliki produkcji do jednostki dużych wystąpień HANA, na którym jest uruchomiony TST Wystąpienie oprogramowania HANA. W tym celu należy podać identyfikator SID wystąpienie oprogramowania HANA produkcji. Po oprogramowanie SAP HANA na usługi Azure Service Management załącznika tych woluminów, musisz zainstalować tych woluminów do jednostki dużych wystąpień HANA.

![Następnym krokiem konfiguracji odzyskiwania po awarii](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start2.PNG)

Następnym krokiem jest dla Ciebie zainstalować drugie wystąpienie SAP HANA w jednostce duże wystąpienie oprogramowania HANA w regionie odzyskiwania po awarii platformy Azure, w którym jest uruchamiane wystąpienie oprogramowania HANA TST. Nowo zainstalowane wystąpienie SAP HANA musi mieć ten sam identyfikator SID. Użytkownicy utworzeni konieczne tych samych identyfikatorów UID i identyfikator grupy, który zawiera wystąpienie produkcyjne. Jeśli instalacja się powiodła, należy:

- Wykonaj krok 2 przygotowania migawki magazynu omówionych wcześniej w tym artykule.
- Utwórz klucz publiczny dla jednostki odzyskiwania po awarii w dużych wystąpień HANA jednostki, jeśli nie zostały jeszcze zrobione. Zobacz krok 3 Przygotowanie migawki magazynu omówionych wcześniej w tym artykule.
- Obsługa *HANABackupCustomerDetails.txt* nowe wystąpienie oprogramowania HANA i testów czy łączność w magazynie działa prawidłowo.  
- Zatrzymaj nowo zainstalowane wystąpienie SAP HANA w jednostce duże wystąpienie oprogramowania HANA w regionie odzyskiwania po awarii platformy Azure.
- Odinstaluj woluminy te PRD i skontaktuj się z oprogramowania SAP HANA na zarządzanie usługami platformy Azure. Woluminy nie może pozostać zainstalowanego do jednostki, ponieważ nie mogą być dostępne podczas działania jako cel replikacji magazynu.  

![Krok instalacji odzyskiwania po awarii przed nawiązaniem replikacji](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start3.PNG)

Zespół operacyjny ustanawia relację replikacji między woluminy PRD w regionie platformy Azure w środowisku produkcyjnym, a woluminy PRD w regionie odzyskiwania po awarii platformy Azure.

>[!IMPORTANT]
>Wolumin /hana/log nie jest replikowana, ponieważ nie jest konieczne przywrócenie zreplikowanej bazy danych SAP HANA do spójnego stanu w lokacji odzyskiwania po awarii.

Następnie można skonfigurować lub dostosować harmonogram tworzenia kopii zapasowych migawki magazynu, aby przejść do swojej RTO i RPO w przypadku awarii. Aby zminimalizować cel punktu odzyskiwania, należy ustawić następujący interwały replikacji w usłudze dużych wystąpień HANA:
- W przypadku woluminów objętych połączone migawki (typ migawki **hana**), zestawu do replikowania co 15 minut do celów woluminu magazynu równoważne w lokacji odzyskiwania po awarii.
- Na woluminie kopii zapasowej dziennika transakcji (typ migawki **dzienniki**), zestawu do replikowania co 3 minuty do celów woluminu magazynu równoważne w lokacji odzyskiwania po awarii.

Aby zminimalizować cel punktu odzyskiwania, wprowadź następujące ustawienia:
- Wykonaj **hana** typu magazynu migawek (zobacz "krok 7: Wykonaj migawki") co 30 minut do 1 godziny.
- Wykonaj kopie zapasowe dziennika transakcji platformy SAP HANA co 5 minut.
- Wykonaj **dzienniki** typu magazynu migawek co 5 – 15 minut. Za pomocą tego okresu interwał osiągasz RPO około 15 25 minut.

Dzięki tej opcji instalacji, sekwencji, kopie zapasowe dziennika transakcji, magazynu migawek i replikacji transakcji HANA Zaloguj się dane kopii zapasowej woluminu i/hana/i /hana/shared (obejmuje/usr/sap), może wyglądać dane wyświetlane na poniższej ilustracji:

 ![Relacja między migawki kopii zapasowej dziennika transakcji i dublowanie przystawki na osi czasu](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

Aby osiągnąć jeszcze lepszy cel punktu odzyskiwania w przypadku odzyskiwania po awarii, możesz skopiować kopie zapasowe dziennika transakcji HANA z platformą SAP HANA na platformie Azure (duże wystąpienia) do innego regionu platformy Azure. Aby osiągnąć ten dalsze obniżenie cel punktu odzyskiwania, należy wykonać następujące czynności:

1. Tworzenie kopii zapasowych transakcji HANA dziennika tak często, jak to tylko możliwe /hana/logbackups.
1. Aby skopiować kopie zapasowe dziennika transakcji systemu plików NFS hostowanych udziału maszyn wirtualnych platformy Azure, należy użyć polecenia rsync. Maszyny wirtualne znajdują się w sieciach wirtualnych platformy Azure w regionie platformy Azure środowiska produkcyjnego i regionu odzyskiwania po awarii. Należy połączyć obie sieci wirtualne platformy Azure z obwodem łączenia dużych wystąpień HANA w środowisku produkcyjnym na platformie Azure. Zobacz grafikę w [sieci zagadnienia dotyczące odzyskiwania po awarii przy użyciu dużych wystąpień HANA](#Network-considerations-for-disaster recovery-with-HANA-Large-Instances) sekcji. 
1. Zachowaj kopie zapasowe dziennika transakcji w regionie na maszynie wirtualnej jest dołączony do systemu plików NFS wyeksportowane magazynu.
1. W przypadku trybu failover po awarii należy uzupełnić kopie zapasowe dziennika transakcji, dostępne na woluminie /hana/logbackups więcej ostatnio wykonane kopie zapasowe dziennika transakcji na systemu plików NFS udostępniania w lokacji odzyskiwania po awarii. 
1. Uruchom kopię zapasową dziennika transakcji w celu przywrócenia najnowszej kopii zapasowej, który może zostać zapisany w regionie odzyskiwania po awarii.

Podczas operacji dużych wystąpień HANA upewnij się, Instalator relacji replikacji i Rozpocznij kopie zapasowe migawek magazynu wykonywania, rozpoczyna się replikacja danych.

![Krok instalacji odzyskiwania po awarii przed nawiązaniem replikacji](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start4.PNG)

Zgodnie z postępów replikacji migawek na woluminach PRD w regionach Azure odzyskiwania po awarii nie są przywracane. Tylko są one przechowywane. Jeśli woluminy są instalowane w taki stan, stanowią one stan, w którym możesz odinstalować tych woluminów po zainstalowaniu wystąpienie PRD SAP HANA w jednostce serwera w regionie odzyskiwania po awarii platformy Azure. Reprezentują one magazynu kopii zapasowych, które nie zostały jeszcze przywrócone.

Podczas pracy awaryjnej również można przywrócić starsze migawkę pamięci masowej zamiast najnowszą migawkę pamięci masowej.

## <a name="disaster-recovery-failover-procedure"></a>Procedurę trybu failover odzyskiwania po awarii
Istnieją dwa przypadki, które należy uwzględnić podczas przechodzenia w tryb failover do lokacji odzyskiwania po awarii:

- Potrzebujesz bazy danych SAP HANA, aby wrócić do najnowszego stanu danych. W tym przypadku jest skryptem samoobsługi za pomocą którego można wykonać przełączenie w tryb failover bez konieczności kontaktu z firmą Microsoft. Jednak do powrotu po awarii, musisz pracować z firmą Microsoft.
- Chcesz przywrócić migawkę pamięci masowej, który nie jest najnowsza wersja migawki replikowanych. W takim przypadku potrzebne do pracy z firmą Microsoft. 

>[!NOTE]
>Poniższe kroki muszą być wykonywane na jednostce dużych wystąpień HANA, która reprezentuje jednostkę odzyskiwania po awarii. 
 
Aby przywrócić najnowsze migawek replikowanego magazynu, wykonaj następujące czynności: 

1. Zamknij wystąpienie nieprodukcyjnych HANA w jednostce odzyskiwania po awarii w dużych wystąpień HANA, której używasz. Jest to spowodowane istnieje wstępnie zainstalowane wystąpienie nieaktywni produkcji HANA.
1. Upewnij się, że są uruchomione żadne procesy platformy SAP HANA. Użyj następującego polecenia dla tego sprawdzenia: `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`. Powinny zostać wyświetlone dane wyjściowe **hdbdaemon** przetwarzanie w stanie zatrzymania, a żadne inne procesy HANA w stanie uruchomiona lub jest uruchomiona.
1. W jednostce dużych wystąpień HANA lokacji odzyskiwania po awarii, uruchom skrypt *azure_hana_dr_failover.pl*. Skrypt jest zapytaniem dla identyfikatora SID SAP HANA, należy przywrócić. Gdy żądanie, wpisz w jednym lub tylko SAP HANA SID, został zreplikowany, która jest przechowywana w *HANABackupCustomerDetails.txt* plikiem w jednostce duże wystąpienie oprogramowania HANA w lokacji odzyskiwania po awarii. 

      Jeśli chcesz mieć wiele wystąpień oprogramowania SAP HANA w trybie Failover, należy uruchomić skrypt kilka razy. Gdy żądanie, wpisz identyfikator SID HANA SAP, aby tryb failover i przywrócić. Po zakończeniu skrypt pokazuje listę punktów instalacji woluminów, które są dodawane do jednostki dużych wystąpień HANA. Ta lista zawiera również przywróconych woluminów odzyskiwania po awarii.

1. Zainstalować woluminu odzyskiwania po awarii przywróconej przy użyciu poleceń systemu operacyjnego Linux do jednostki duże wystąpienie oprogramowania HANA w lokacji odzyskiwania po awarii. 
1. Rozpocznij nieaktywni wystąpienie SAP HANA w środowisku produkcyjnym.
1. Jeśli wybrano opcję Kopiuj dzienniki kopii zapasowych dziennika transakcji w celu skrócenia czasu cel punktu odzyskiwania, musisz scalić te kopie zapasowe dziennika transakcji w nowo zainstalowanego odzyskiwania po awarii/hana lub logbackups katalogu. Nie Nadpisz istniejące kopie zapasowe. Skopiuj nowszych kopii zapasowych, które nie zostały zreplikowane przy użyciu najnowszych replikacji migawki magazynu.
1. Można także przywrócić pojedynczych plików z migawek, które zostały zreplikowane do woluminu /hana/shared/PRD w regionie odzyskiwania po awarii platformy Azure. 

Możesz również testowanie trybu failover odzyskiwania po awarii, bez wywierania wpływu na relacji replikacji rzeczywiste. Aby wykonać test trybu failover, wykonaj wcześniejsze kroki 1 i 2, a następnie kontynuuj następujący krok 3.

>[!IMPORTANT]
>Czy *nie* uruchamiania dowolnej transakcji do produkcji w wystąpieniu, który został utworzony w lokacji odzyskiwania po awarii w procesie **testowania pracy w trybie failover** ze skryptem wprowadzone w kroku 3. To polecenie tworzy zestaw woluminów, które nie mają relacji z lokacją główną. W wyniku synchronizacji do lokacji głównej jest *nie* możliwe. 

Krok 3 dla testu trybu failover

W jednostce dużych wystąpień HANA lokacji odzyskiwania po awarii, uruchom skrypt **azure_hana_test_dr_failover.pl**. Ten skrypt jest *nie* zatrzymywanie relacji replikacji między lokacją podstawową a lokacją odzyskiwania po awarii. Zamiast tego należy ten skrypt jest Klonowanie woluminów magazynu odzyskiwania po awarii. Po pomyślnym procesu klonowania, sklonowany woluminy są przywrócony do stanu najnowszej migawki, a następnie instalowane do jednostki odzyskiwania po awarii. Skrypt jest zapytaniem dla identyfikatora SID SAP HANA, należy przywrócić. Wpisz jeden lub tylko SAP HANA SID, został zreplikowany, która jest przechowywana w *HANABackupCustomerDetails.txt* plikiem w jednostce duże wystąpienie oprogramowania HANA w lokacji odzyskiwania po awarii. 

Jeśli chcesz mieć wiele wystąpień oprogramowania SAP HANA do testowania, należy uruchomić skrypt kilka razy. Gdy żądanie, wpisz identyfikator SID HANA SAP wystąpienie, które chcesz przetestować tryb failover. Po ukończeniu skryptu pokazuje listę punktów instalacji woluminów, które są dodawane do jednostki dużych wystąpień HANA. Ta lista zawiera również sklonowany woluminów odzyskiwania po awarii.

Przejdź do kroku 4.

   >[!NOTE]
   >Jeśli potrzebujesz do trybu failover do lokacji odzyskiwania po awarii i ratunkowe niektórych danych została usunięta, godz. temu woluminów odzyskiwania po awarii, należy ustawić wcześniejszej migawki, ta procedura ma zastosowanie. 

1. Zamknij wystąpienie nieprodukcyjnych HANA w jednostce odzyskiwania po awarii w dużych wystąpień HANA, której używasz. Jest to spowodowane istnieje wstępnie zainstalowane wystąpienie nieaktywni produkcji HANA.
1. Upewnij się, że są uruchomione żadne procesy platformy SAP HANA. Użyj następującego polecenia dla tego sprawdzenia: `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`. Powinny zostać wyświetlone dane wyjściowe **hdbdaemon** przetwarzanie w stanie zatrzymania i żadne inne procesy HANA w stanie uruchomiona lub jest uruchomiona.
1. Określ, do którego nazwy migawki lub identyfikator kopii zapasowej platformy SAP HANA mają lokacji odzyskiwania po awarii, które są przywracane. W przypadku odzyskiwania po awarii w rzeczywistych ta migawka jest zwykle najnowszą migawkę. Jeśli potrzebujesz odzyskać utracone dane, wybierz wcześniejszej migawki.
1. Skontaktuj się z działem pomocy technicznej platformy Azure za pośrednictwem żądania pomocy technicznej o wysokim priorytecie. Poproś o przywrócenie tej migawki (przy użyciu nazwy i Data migawki) lub identyfikator kopii zapasowej platformy HANA w lokacji odzyskiwania po awarii. Wartość domyślna to, że po stronie Operacje przywraca/hana/ilość danych tylko. Jeśli chcesz mieć/hana/logbackups woluminy również należy wyraźnie określają, że. *Nie należy przywracać /hana/shared woluminu.* Zamiast tego należy wybrać konkretne pliki, takie jak global.ini poza **.snapshot** katalogu i jego podkatalogach po można ponownie zainstalować/hana/udostępniony wolumin PRD. 

   Na stronie operacje wykonane następujące kroki:

   a. Replikacji migawek woluminu odzyskiwania po awarii z woluminu produkcji jest zatrzymana. Ta przerw w działaniu może już się to zdarzyć w przypadku wystąpienia awarii w czasie produkcji Przyczyna, że musisz wykonać procedurę odzyskiwania po awarii.
   
   b. Magazyn migawek nazwy ani tworzyć migawek identyfikatorem kopii zapasowej została wybrana opcja został przywrócony na woluminu odzyskiwania po awarii.
   
   c. Po przywróceniu woluminu odzyskiwania po awarii są dostępne dla zamontowania jednostkom duże wystąpienie oprogramowania HANA w regionie odzyskiwania po awarii.
      
1. Instalowanie woluminów odzyskiwania po awarii do jednostki duże wystąpienie oprogramowania HANA w lokacji odzyskiwania po awarii. 
1. Rozpocznij nieaktywni wystąpienie SAP HANA w środowisku produkcyjnym.
1. Jeśli wybrano opcję Kopiuj dzienniki kopii zapasowych dziennika transakcji w celu skrócenia czasu cel punktu odzyskiwania, musisz scalić te kopie zapasowe dziennika transakcji w nowo zainstalowanego odzyskiwania po awarii/hana lub logbackups katalogu. Nie Nadpisz istniejące kopie zapasowe. Skopiuj nowszych kopii zapasowych, które nie zostały zreplikowane przy użyciu najnowszych replikacji migawki magazynu.
1. Można także przywrócić pojedynczych plików z migawek, które zostały zreplikowane do woluminu /hana/shared/PRD w regionie odzyskiwania po awarii platformy Azure.

Dalej sekwencji kroków obejmuje odzyskiwanie wystąpienia produkcyjnych oprogramowania SAP HANA na podstawie migawek magazynu przywrócona i kopie zapasowe dziennika transakcji, które są dostępne:

1. Zmień lokalizację kopii zapasowej na **/hana/logbackups** przy użyciu programu SAP HANA Studio.
   ![Zmiana lokalizacji kopii zapasowej do odzyskania odzyskiwania po awarii](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. SAP HANA skanuje za pośrednictwem lokalizacji kopii zapasowej plików i sugeruje najnowszej kopii zapasowej dziennika transakcji do przywrócenia. Skanowanie może potrwać kilka minut, aż do ekranu, tak jak pojawia się następujące: ![listy kopie zapasowe dziennika transakcji do odzyskania odzyskiwania po awarii](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. Dostosuj niektóre z domyślnych ustawień:

      - Wyczyść **użycie różnicowych kopii zapasowych**.
      - Wybierz **zainicjować dziennika obszaru**.

   ![Ustaw obszar zainicjować dziennika](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. Wybierz pozycję **Finish** (Zakończ).

   ![Zakończ Przywracanie odzyskiwania po awarii](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

Powinna pojawić się okno postępu, jak pokazano w tym miejscu. Należy pamiętać, że w przykładzie występuje przywracania odzyskiwania po awarii w konfiguracji platformy SAP HANA skalowalnego w poziomie trzema węzłami.

![Przywracanie w toku](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

W przypadku przywracania wydaje się zatrzymuje się na **Zakończ** ekranu i nie pokazuj ekran pokazujący postęp, upewnij się, że są uruchomione wszystkie wystąpienia oprogramowania SAP HANA na węzłach procesu roboczego. Jeśli to konieczne, należy ręcznie uruchomić wystąpieniami platformy SAP HANA.


### <a name="failback-from-a-dr-to-a-production-site"></a>Powrót po awarii z odzyskiwania po awarii do lokacji w środowisku produkcyjnym
Można powrotu po awarii z odzyskiwania po awarii do lokacji w środowisku produkcyjnym. Przyjrzyjmy się scenariusz, w którym przełączenie w tryb failover do lokacji odzyskiwania po awarii zostało spowodowane przez problemy w środowisku produkcyjnym region platformy Azure i nie trzeba odzyskać utracone dane. Uruchomiony używanych obciążeń produkcyjnych SAP od pewnego czasu w lokacji odzyskiwania po awarii. Jak są rozwiązywane problemy z witryny produkcyjnej, chcesz powrócić po awarii do witryn produkcyjnych. Ponieważ nie może utracić dane, krok do miejsca produkcji obejmuje kilka kroków i ścisłej współpracy z platformą SAP HANA w zespole operacji na platformie Azure. To Ty możesz wyzwolić zespół operacyjny, należy rozpocząć synchronizowanie wróć do witryny produkcyjnej po problemy są rozwiązywane.

Jest to sekwencja czynności do wykonania:

1. SAP HANA w zespole operacji na platformie Azure pobiera wyzwalacz, aby zsynchronizować woluminów magazynu produkcji z woluminów magazynu odzyskiwania po awarii, które teraz reprezentują stan produkcji. W tym stanie jednostkę dużych wystąpień HANA w środowisku produkcyjnym lokacji jest wyłączana.
1. SAP HANA w zespole operacji na platformie Azure monitoruje replikacji i upewnia się, że jej padł przed informujące o tym.
1. Aplikacje, które używają wystąpienie oprogramowania HANA w środowisku produkcyjnym w lokacji odzyskiwania po awarii zostanie zamknięta. Następnie należy wykonać kopii zapasowej dziennika transakcji platformy HANA. Następnie można zatrzymać wystąpienie oprogramowania HANA uruchomionych na jednostkach duże wystąpienie oprogramowania HANA w lokacji odzyskiwania po awarii.
1. Po zamknięciu wystąpienie oprogramowania HANA w jednostce duże wystąpienie oprogramowania HANA w lokacji odzyskiwania po awarii zespół operacyjny ręcznie synchronizuje woluminy dysków ponownie.
1. SAP HANA w zespole operacji na platformie Azure ponownie uruchamia jednostki duże wystąpienie oprogramowania HANA w witryny produkcyjnej i przekazuje użytkownikowi. Upewnij się, wystąpienie SAP HANA jest w czasie uruchamiania dużych wystąpień HANA jednostki w stanie zamknięcie.
1. Możesz wykonać te same kroki przywracania bazy danych, tak jak w przypadku poprzednio przejść w tryb failover do lokacji odzyskiwania po awarii.

### <a name="monitor-disaster-recovery-replication"></a>Monitorowanie replikacji odzyskiwania po awarii

Możesz monitorować stan postęp replikacji magazynu, wykonując skrypt `azure_hana_replication_status.pl`. Ten skrypt należy uruchomić z jednostki w lokalizacji odzyskiwania danych po awarii, które działają zgodnie z oczekiwaniami. Skrypt działa niezależnie od tego, czy replikacja jest aktywna. Skrypt można uruchomić dla każdej jednostki dużych wystąpień HANA dzierżawy w lokalizacji odzyskiwania danych po awarii. Nie można uzyskać szczegółowe informacje o woluminie rozruchowym.

Wywołania skryptu za pomocą następującego polecenia:
```
./replication_status.pl <HANA SID>
```

Dane wyjściowe są podzielone według woluminu na następujące sekcje:  

- Stan linku
- Bieżące działanie replikacji
- Ostatnia migawka replikowane 
- Rozmiar najnowszej migawki
- Bieżący czas opóźnienia między migawkami (od ostatniej replikacji migawki ukończone, a teraz)

Stan łącza jest wyświetlany jako **Active** chyba że łącza między lokacjami nie działa lub jest zdarzeniem aktualnie trwająca trybu failover. Działanie replikacji adresów, czy wszystkie dane, jest obecnie replikowana lub jest w stanie bezczynności, czy obecnie występują inne działania łącza. Ostatnia migawka replikowane tylko powinny się wyświetlać jako `snapmirror…`. Rozmiar ostatnia migawka zostanie wyświetlona. Na koniec jest wyświetlany czas opóźnienia. Czas opóźnienia reprezentuje czas zaplanowanej replikacji, aby po zakończeniu replikacji. Opóźnienie może być większa niż godziny replikacji danych, szczególnie w przypadku replikacji początkowej, mimo że uruchomieniu replikacji. Czas opóźnienia w dalszym ciągu zwiększyć zakończenie trwającej replikacji.

Oto przykładowe dane wyjściowe:

```
hana_data_hm3_mnt00002_t020_dp
-------------------------------------------------
Link Status: Broken-Off
Current Replication Activity: Idle
Latest Snapshot Replicated: snapmirror.c169b434-75c0-11e6-9903-00a098a13ceb_2154095454.2017-04-21_051515
Size of Latest Snapshot Replicated: 244KB
Current Lag Time between snapshots: -   ***Less than 90 minutes is acceptable***
```












