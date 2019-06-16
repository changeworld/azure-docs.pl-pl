---
title: Microsoft Azure StorSimple Virtual Array — omówienie | Dokumentacja firmy Microsoft
description: W tym artykule opisano macierz wirtualną StorSimple, zintegrowanego rozwiązania, które zarządza zadaniami magazynowania między lokalnej macierzy wirtualnej i magazynu w chmurze Microsoft Azure.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 169c639b-1124-46a5-ae69-ba9695525b77
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/14/2019
ms.author: alkohli
ms.openlocfilehash: e5713af737a6d9d190814b4155a8e772deea06bc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60630373"
---
# <a name="introduction-to-the-storsimple-virtual-array"></a>Wprowadzenie do macierzy wirtualnej StorSimple

## <a name="overview"></a>Omówienie

Microsoft Azure StorSimple Virtual Array to zintegrowane rozwiązanie do magazynowania, który zarządza zadaniami magazynowania między lokalną macierzą wirtualną działającą w funkcji hypervisor i Magazyn w chmurze Microsoft Azure. Macierz wirtualna to wydajne, ekonomiczne i łatwe w zarządzaniu serwer_plików lub rozwiązania z serwerem iSCSI, które eliminuje wiele problemów oraz kosztów związanych z ochroną magazynu i danych przedsiębiorstwa. Macierz wirtualna jest przeznaczone szczególnie do przechowywania rzadko używanych danych archiwalnych.

Ten artykuł zawiera omówienie macierzy wirtualnej — Oto kilka innych zasobów:

* Aby uzyskać najlepsze rozwiązania, zobacz [najlepsze rozwiązania StorSimple Virtual Array](storsimple-ova-best-practices.md).
* Aby zapoznać się z omówieniem urządzeń z serii StorSimple 8000, przejdź do [serii StorSimple 8000: hybrydowe rozwiązanie w chmurze](storsimple-overview.md).
* Aby uzyskać informacje dotyczące urządzeń z serii StorSimple 5000/7000, przejdź do [pomocy Online usługi StorSimple](http://onlinehelp.storsimple.com/).

Macierz wirtualna obsługuje protokół bloku komunikatów serwera (SMB) lub iSCSI. Ona działa w istniejącej infrastrukturze funkcji hypervisor i zapewnia obsługę warstw na chmurze, kopii zapasowej w chmurze, szybkiego przywracania, odzyskiwania na poziomie elementu i funkcje odzyskiwania po awarii.

W poniższej tabeli podsumowano ważne funkcje rozwiązania StorSimple Virtual Array.

| Cecha | Macierz wirtualna usługi StorSimple |
| --- | --- |
| Wymagania dotyczące instalacji |Używa infrastruktury wirtualizacji (Hyper-V lub VMware) |
| Dostępność |Jeden węzeł |
| Całkowita pojemność (w tym chmury) |Maksymalnie 64 TB pojemności do wykorzystania na macierz wirtualną |
| Pojemność lokalna |390 GB do 6,4 TB pojemności do wykorzystania na macierz wirtualną (trzeba aprowizować 500 GB do 8 TB miejsca na dysku) |
| Protokoły natywne |iSCSI lub bloku SMB |
| Cel czasu odzyskiwania (recovery time objective, RTO) |iSCSI: mniej niż 2 minuty, bez względu na rozmiar |
| Cel punktu odzyskiwania (recovery point objective, RPO) |Codzienne wykonywanie kopii zapasowych i kopii zapasowych na żądanie |
| Obsługa warstw magazynowania |Zastosowań cieplnej mapowanie, aby ustalić, jakie dane powinny należeć do warstwy wewnątrz lub na zewnątrz |
| Pomoc techniczna |Infrastruktura wirtualizacji obsługiwane przez dostawcę |
| Wydajność |W zależności od podstawowej infrastruktury |
| Mobilność danych |Można przywrócić do tego samego urządzenia lub poziomie elementu odzyskiwać (serwer plików) |
| Warstwy magazynowania |Magazyn swoich lokalnych i w chmurze |
| Rozmiar udziału |Warstwowe: maksymalnie 20 TB przypięty lokalnie: maksymalnie 2 TB. |
| Rozmiar woluminu |Warstwy: Od 500 GB do 5 TB; przypięty lokalnie: 50 GB do 200 GB <br> Maksymalna zarezerwowane miejsce lokalne na woluminy warstwowe wynosi 200 GB. |
| Migawki |Spójne na poziomie awarii |
| Odzyskiwanie na poziomie elementu |Tak; Użytkownicy mogą przywracać z udziałów |

## <a name="why-use-storsimple"></a>Dlaczego warto używać usługi StorSimple?

StorSimple łączy użytkowników i serwerów do magazynu platformy Azure w ciągu kilku minut bez żadnych modyfikacji aplikacji.

W poniższej tabeli opisano niektóre z kluczowych korzyści zapewnianych przez rozwiązania StorSimple Virtual Array.

| Cecha | Korzyść |
| --- | --- |
| Integracja przezroczyste |Macierz wirtualna obsługuje protokół SMB lub iSCSI. Przenoszenie danych między warstwami lokalnych i chmurze jest bezproblemowe i niewidoczne dla użytkownika. |
| Mniejsze koszty |Za pomocą usługi StorSimple możesz aprowizować wystarczającej ilości miejsca lokalne, aby spełniać wymagania bieżącej dla najczęściej używanych gorących danych. Zgodnie z wzrostem wymagań magazynu, Magazyn w chmurze StorSimple warstwy zimnych danych do ekonomiczne. Dane są deduplikowane, a następnie skompresowane przed wysłaniem do chmury, aby jeszcze bardziej ograniczyć wymagania dotyczące magazynu i kosztów. |
| Uproszczone zarządzanie magazynem |Usługa StorSimple umożliwia scentralizowane zarządzanie w chmurze przy użyciu Menedżera urządzeń StorSimple do zarządzania wieloma urządzeniami. |
| Odzyskiwanie po awarii ulepszone i zgodności |Usługa StorSimple ułatwia szybsze odzyskiwanie po awarii, natychmiastowe przywracanie metadanych i przywracanie danych, zgodnie z potrzebami. Oznacza to, że przy minimalnym zakłóceniu kontynuować normalne działanie. |
| Mobilność danych |Dane warstwowe w chmurze są dostępne dla innych witryn w celach recovery i migracji. Należy pamiętać, dane można przywrócić tylko do oryginalnego macierzy wirtualnej. Jednak użyć funkcji odzyskiwania po awarii można przywrócić całą macierzy wirtualnej do innej macierzy wirtualnej. |

## <a name="storsimple-workload-summary"></a>Podsumowanie obciążenia usługi StorSimple

Podsumowanie obsługiwanych obciążeń StorSimple jest przedstawione w poniższej tabeli.

|Scenariusz     |Obciążenie     |Obsługiwane      |Ograniczenia               | Odpowiednie wersje|
|-------------|-------------|---------------|---------------------------|--------------------|
|Office zdalnych biurach/oddziałach (ROBO)  |Udostępnianie plików     |Tak      |Zobacz [maksymalny limit dla serwera plików](storsimple-ova-limits.md).<br></br>Zobacz [wymagania systemowe dotyczące obsługiwanych wersji protokołu SMB](storsimple-ova-system-requirements.md).| Wszystkie wersje     |
|Trwa archiwizowanie w chmurze  |Udostępnianie plików archiwizacji     |Tak      |Zobacz [maksymalny limit dla serwera plików](storsimple-ova-limits.md).<br></br>Zobacz [wymagania systemowe dotyczące obsługiwanych wersji protokołu SMB](storsimple-ova-system-requirements.md).| Wszystkie wersje     |

Macierz wirtualna StorSimple jest najlepszym rozwiązaniem w przypadku rzadko używanych danych. Chociaż macierzy wirtualnej ma lokalnej pamięci podręcznej w celu zwiększania wydajności, użytkowników należy przyjąć, że urządzenie usługi plików w najniższej warstwy magazynu (chmura). Każdą macierz wirtualną można zapisu i odczytu do usługi Azure storage w około 100 MB/s. Ten link jest współużytkowany przez wszystkich żądań przychodzących do urządzenia i mogą stać się wąskim gardłem, jak pokazano na poniższym diagramie.

![Trwa archiwizowanie w chmurze](./media/storsimple-ova-overview/cloud-archiving.png)

Gdy jednoczesną pracę wielu użytkowników, dostęp do macierzy wirtualnej, współużytkują one wszystkie połączenia platformy Azure, co doprowadzi do obniżenia wydajności. Istnieje nie gwarancji wydajności dla każdego użytkownika i urządzenia przetwarza poszczególnych żądań, podczas ich dostarczania.

Macierz wirtualna StorSimple nie jest odpowiednia dla obciążeń wymagających wysokiej dostępności. Macierz wirtualna to urządzenie o jednym węźle, który wystąpi przestój podczas instalowania aktualizacji oprogramowania. Administratorzy, należy zaplanować na okno obsługi, 30 minut 3 - 4 razy w roku.

## <a name="workflows"></a>Przepływy pracy

Rozwiązania StorSimple Virtual Array jest szczególnie przydatny w przypadku następujących przepływów:

* [Zarządzanie magazynami oparte na chmurze](#cloud-based-storage-management)
* [Niezależnie od lokalizacji kopii zapasowej](#location-independent-backup)
* [Dane ochrony i odzyskiwania po awarii](#data-protection-and-disaster-recovery)

### <a name="cloud-based-storage-management"></a>Zarządzanie magazynami oparte na chmurze
Usługa Menedżer urządzeń StorSimple uruchomione w witrynie Azure portal umożliwia zarządzanie danymi zapisanymi na wielu urządzeniach i w wielu lokalizacjach. Jest to szczególnie przydatne w scenariuszach oddziału rozproszonych. Należy pamiętać, że należy utworzyć oddzielne wystąpienia usługi Menedżer urządzeń StorSimple do zarządzania macierzami wirtualnymi, jak i urządzeń fizycznych StorSimple. Należy również zauważyć, że macierz wirtualna teraz używa nowej witryny Azure portal zamiast klasycznej witrynie Azure portal.

![Zarządzanie magazynami oparte na chmurze](./media/storsimple-ova-overview/cloud-based-storage-management.png)

### <a name="location-independent-backup"></a>Niezależnie od lokalizacji kopii zapasowej
Za pomocą macierzy wirtualnej migawki w chmurze zapewniają niezależnie od lokalizacji, w momencie kopia woluminu lub udziału. Migawki w chmurze są domyślnie włączone i nie może być wyłączony. Wszystkie woluminy i udziały są tworzenie kopii zapasowej w tym samym czasie za pomocą pojedynczego codzienne zasad tworzenia kopii zapasowej i może potrwać dodatkowych ad-hoc kopii zapasowych zawsze, gdy jest to konieczne.

### <a name="data-protection-and-disaster-recovery"></a>Dane ochrony i odzyskiwania po awarii
Macierz wirtualna obsługuje następujące ochrony danych i scenariuszy odzyskiwania po awarii:

* **Przywracanie woluminu lub udziału** — Użyj przywracania jako nowy przepływ pracy, aby odzyskać z woluminu lub udziału. Takie podejście umożliwia odzyskanie całego woluminu lub udziału.
* **Element odzyskiwanie na poziomie** — Zezwalaj na akcje uproszczony dostęp do najnowszych kopii zapasowych. Można łatwo odzyskać pojedynczy plik z specjalny *.backup* folderów, które są dostępne w chmurze. Ta funkcja przywracania jest oparte na użytkownika i jest wymagana żadna interwencja administracyjne.
* **Odzyskiwanie po awarii** — Użyj możliwości trybu failover, aby odzyskać wszystkie woluminy lub udziały do nowego macierzy wirtualnej. Możesz utworzyć nową tablicę wirtualnego zarejestruj je przy użyciu usługi Menedżer urządzeń StorSimple, a następnie oryginalnego macierzy wirtualnej w tryb failover. Nowe macierzy wirtualnej założy, następnie aprowizowane zasoby.

## <a name="storsimple-virtual-array-components"></a>Składniki rozwiązania StorSimple Virtual Array

Macierz wirtualna obejmuje następujące składniki:

* [Macierz wirtualna](#virtual-array) — hybrydowe urządzenie magazynujące w chmurze oparte na maszynie wirtualnej aprowizowane w Twoim środowisku wirtualnym lub funkcji hypervisor.
* [Usługa Menedżer urządzeń StorSimple](#storsimple-device-manager-service) — rozszerzenie portalu Azure, która umożliwia zarządzanie jednym lub kilkoma urządzeniami StorSimple z interfejsu jednej sieci web, które są dostępne z różnych lokalizacji geograficznych. Usługa Menedżer urządzeń StorSimple do tworzenia i zarządzania usługami, wyświetlanie i zarządzanie urządzeniami i alertami i zarządzać woluminy, udziały i istniejące migawki.
* [Interfejs użytkownika sieci web w lokalnych](#local-web-user-interface) — opartej na sieci web interfejs użytkownika, który jest używany do konfigurowania urządzenia, dzięki czemu mogą nawiązać połączenie z siecią lokalną i następnie zarejestruj urządzenie w usłudze Menedżer urządzeń StorSimple. 
* [Interfejs wiersza polecenia](#command-line-interface) — interfejsu programu Windows PowerShell, który można użyć, aby rozpocząć sesję pomocy technicznej w ramach macierzy wirtualnej.
  W poniższych sekcjach opisano każdą z tych składników, które bardziej szczegółowo i wyjaśniono, jak rozwiązanie rozmieszcza dane, przydziela pamięć i ułatwia zarządzanie magazynami i ochrony danych.

### <a name="virtual-array"></a>Macierz wirtualna

Macierz wirtualna jest rozwiązanie pojedynczego węzła magazynu, które udostępnia podstawowy magazyn, zarządza komunikacją z magazynem w chmurze i pomaga zapewnić bezpieczeństwo i poufność wszystkich danych przechowywanych na urządzeniu.

Macierz wirtualna jest dostępna w jednym modelu, który jest dostępny do pobrania. Macierz wirtualna ma maksymalną pojemność wynoszącą 6,4 TB na urządzeniu (z bazowego wymagania dotyczące magazynu o rozmiarze 8 TB) i tym 64 TB magazynu w chmurze.

Macierz wirtualna ma następujące cechy:

* To ekonomiczne. Jego sprawia, że korzystanie z istniejącej infrastruktury wirtualizacji oraz można wdrożyć w swojej istniejącej funkcji hypervisor Hyper-V lub VMware.
* Ona znajduje się w centrum danych i może być skonfigurowany jako serwer iSCSI lub serwer plików.
* Jest zintegrowany z chmurą.
* Kopie zapasowe są przechowywane w chmurze, która może ułatwić odzyskiwanie po awarii i upraszcza odzyskiwanie na poziomie elementu (ILR).
* Aktualizacje można stosować do macierzy wirtualnej, tak samo będzie je na urządzeniu fizycznym.

> [!NOTE]
> Macierz wirtualna nie może zostać rozszerzona. Dlatego ważne jest do udostępniania odpowiedniego magazynu podczas tworzenia macierzy wirtualnej.

### <a name="storsimple-device-manager-service"></a>Usługa Menedżer urządzeń StorSimple

Microsoft Azure StorSimple udostępnia interfejs użytkownika oparty na sieci web, usługi Menedżer urządzeń StorSimple, która umożliwia centralne zarządzanie magazynu StorSimple. Usługa Menedżer urządzeń StorSimple umożliwia wykonywanie następujących zadań:

* Zarządzanie wielu macierze wirtualne StorSimple z jednej usługi.
* Konfigurowanie i zarządzanie ustawieniami zabezpieczeń dla macierzy wirtualnych StorSimple. (Szyfrowania w chmurze jest zależny od interfejsów API usługi Microsoft Azure).
* Konfigurowanie poświadczeń konta magazynu i właściwości.
* Konfigurowanie i zarządzanie nimi, woluminy lub udziały.
* Tworzenie kopii zapasowej i przywracania danych na woluminy lub udziały.
* Monitorowanie wydajności.
* Przejrzyj ustawienia systemowe i identyfikowania możliwych problemów.

Usługa Menedżer urządzeń StorSimple można wykonywać codzienne zadania administracyjne macierz wirtualna.

Aby uzyskać więcej informacji, przejdź do [korzystać z usługi Menedżer urządzeń StorSimple do administrowania urządzeniem StorSimple](storsimple-virtual-array-manager-service-administration.md).

### <a name="local-web-user-interface"></a>Lokalnego internetowego interfejsu użytkownika

Macierz wirtualna zawiera internetowy interfejs użytkownika, który jest używany do jednorazowej konfiguracji i rejestracji urządzenia w usłudze Menedżer urządzeń StorSimple. Umożliwia on zamknięty i ponownie uruchomić macierzy wirtualnej, wykonaj testy diagnostyczne, aktualizacji oprogramowania, Zmień hasło administratora urządzenia, wyświetlić dzienniki systemu i skontaktuj się z Microsoft Support do pliku żądania obsługi.

Aby dowiedzieć się, jak za pomocą interfejsu użytkownika opartego na sieci web, przejdź do [administrowania rozwiązania StorSimple Virtual Array przy użyciu interfejsu użytkownika opartego na sieci web](storsimple-ova-web-ui-admin.md).

### <a name="command-line-interface"></a>Interfejs wiersza polecenia

Uwzględnione interfejsu programu Windows PowerShell można zainicjować sesję pomocy technicznej, korzystając z programu Microsoft Support, dzięki czemu mogą one pomóc rozwiązywania oraz usuwania problemów, które mogą wystąpić na macierz wirtualna.

## <a name="storage-management-technologies"></a>Technologie zarządzania magazynu

Oprócz macierz wirtualną i inne składniki rozwiązania StorSimple używa następujących technologii oprogramowania zapewniają szybki dostęp do ważnych danych, ograniczyć zużycie magazynu i ochronę danych przechowywanych na macierz wirtualna:

* [Obsługa automatycznego przechowywania warstw](#automatic-storage-tiering) 
* [Przypięty lokalnie udziały i woluminy](#locally-pinned-shares-and-volumes)
* Na potrzeby deduplikacji i kompresji danych warstwy lub kopii zapasowej w chmurze 
* [Tworzenie kopii zapasowych zaplanowanych, jak i na żądanie](#scheduled-and-on-demand-backups)

### <a name="automatic-storage-tiering"></a>Obsługa automatycznego przechowywania warstw
Macierz wirtualna używa nowego mechanizmu obsługi warstw w celu zarządzania przechowywanych danych macierz wirtualną i w chmurze. Istnieją tylko w dwóch warstwach: Magazyn w chmurze macierzy wirtualnej lokalnym i platformą Azure. Macierz wirtualną StorSimple automatycznie rozmieszcza dane warstwami, w zależności od Mapa cieplna, która śledzi bieżące użycie, wiek i relacje z innymi danymi. Dane, które są najbardziej aktywne (najbardziej aktywnych) są przechowywane lokalnie, podczas gdy mniej aktywnych i nieaktywnych danych jest automatycznie migrację do chmury. (Wszystkie kopie zapasowe są przechowywane w chmurze). Rozwiązania StorSimple, dostosowuje i Reorganizuje dane i zmienić przypisania magazynu jako wzorców użycia. Na przykład niektóre informacje mogą stać się mniej aktywne wraz z upływem czasu. Staje się stopniowo mniej aktywne, jest warstwowa się do chmury. Jeśli na tych samych danych ponownie staje się aktywny, jest warstwowe w do macierzy magazynowej.

Dane dla określonego udziału warstwowego lub wolumin jest gwarantowane własną przestrzeń warstwie lokalnej (około 10% aprowizowanego miejsca dla tego udziału lub woluminu). Zmniejsza to dostępnego magazynu w ramach macierzy wirtualnej dla tego udziału lub woluminu, gwarantuje to, czy obsługa warstw na jeden udział lub wolumin nie wpłynie warstw potrzeb inne udziały lub woluminy. Dlatego bardzo zajęty obciążenie na jeden udział lub wolumin nie może wymusić innych obciążeń do chmury.

Woluminy warstwowe utworzone dla interfejsu iSCSI ma maksymalną zarezerwowane miejsce lokalne 200 GB miejsca bez względu na rozmiar woluminu.

![Obsługa automatycznego przechowywania warstw](./media/storsimple-ova-overview/automatic-storage-tiering.png)

> [!NOTE]
> Można określić wolumin przypięty lokalnie, w którym to przypadku dane pozostają w macierzy wirtualnej i nigdy nie jest warstwowe w chmurze. Aby uzyskać więcej informacji, przejdź do [przypięty lokalnie, udziały i woluminy](#locally-pinned-shares-and-volumes).


### <a name="locally-pinned-shares-and-volumes"></a>Przypięty lokalnie udziały i woluminy

Można utworzyć odpowiednie udziały i woluminy przypięte lokalnie. Ta funkcja zapewnia, że dane wymagane przez kluczowych aplikacji pozostaje w macierzy wirtualnej i nigdy nie jest warstwowe w chmurze. Przypięty lokalnie udziały i woluminy mają następujące funkcje:

* Nie podlegają opóźnienia w chmurze lub problemy z łącznością.
* Mogą nadal korzystać z usługi StorSimple cloud kopia zapasowa i odzyskiwanie po awarii funkcji odzyskiwania.

Możesz przywrócić udziału przypiętego lokalnie lub woluminie zgodnie z zasadami warstwowej lub udziału warstwowego lub przypięty lokalnie woluminie. 

Aby uzyskać więcej informacji na temat woluminy przypięte lokalnie, przejdź do [usługi Menedżer urządzeń StorSimple umożliwia zarządzanie woluminami](storsimple-virtual-array-manage-volumes.md).

### <a name="deduplication-and-compression-for-data-tiered-or-backed-up-to-the-cloud"></a>Na potrzeby deduplikacji i kompresji danych warstwy lub kopii zapasowej w chmurze

Usługa StorSimple używa deduplikacji i kompresji danych, aby jeszcze bardziej ograniczyć wymagania dotyczące magazynu w chmurze. Funkcja deduplikacji zmniejsza ogólną ilość danych przechowywanych przez wyeliminowanie nadmiarowości przechowywanej zestawu danych. Jak zmieni się informacji StorSimple ignoruje niezmienione dane i przechwytuje tylko zmiany. Ponadto StorSimple zmniejsza ilość przechowywanych danych, identyfikowanie i usuwając duplikaty.

> [!NOTE]
> Dane przechowywane w ramach macierzy wirtualnej nie jest deduplikowany lub skompresowany. Wszystkie deduplikacji i kompresji występuje tuż przed, dane są wysyłane do chmury.

### <a name="scheduled-and-on-demand-backups"></a>Tworzenie kopii zapasowych zaplanowanych, jak i na żądanie

Funkcje ochrony danych StorSimple umożliwiają tworzenie kopii zapasowych na żądanie. Ponadto domyślny harmonogram tworzenia kopii zapasowej gwarantuje, że dane kopia zapasowa jest tworzona codziennie. Kopie zapasowe są wykonywane w postaci migawek przyrostowych, które są przechowywane w chmurze. Migawki, które należy zarejestrować tylko zmiany od ostatniej kopii zapasowej, można tworzyć i przywracane szybko. Te migawki może być niezwykle istotne w przypadku scenariuszy odzyskiwania po awarii, ponieważ Zamień systemy pomocniczego magazynu (takie jak kopii zapasowej na taśmie) i umożliwiają przywrócenie danych do centrum danych lub alternatywnej witryny, w razie potrzeby.

## <a name="managing-personal-information"></a>Zarządzanie informacji osobistych

Menedżer urządzeń StorSimple dla serii wirtualnych zbiera informacje osobiste w dwóch wystąpieniach klucza:
 - Zgłoś alert, ustawienia użytkownika, w którym skonfigurowano adresów e-mail użytkowników. Te informacje mogą zostać wyczyszczone przez administratora. 
 - Użytkownicy, którzy mają dostęp do danych znajdujących się w udziałach. Lista użytkowników, którzy mają dostęp do danych udziału jest wyświetlane i można je eksportować. Ta lista są także usuwane w momencie usunięcia akcji.

Aby uzyskać więcej informacji, zobacz [zasady Privacy firmy Microsoft w Centrum zaufania](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [przygotowywanie portalu macierzy wirtualnej](storsimple-virtual-array-deploy1-portal-prep.md).
