---
title: Omówienie tablicy wirtualne Microsoft Azure StorSimple | Dokumentacja firmy Microsoft
description: W tym artykule opisano tablicy wirtualnego StorSimple, zintegrowane pamięci masowej zarządzanego zadań magazynu między lokalnymi tablicy wirtualnych i magazynu w chmurze Microsoft Azure.
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
ms.date: 03/28/2018
ms.author: alkohli
ms.openlocfilehash: db06614ebd7dee4b0a320737ea8f575b3a3be70f
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
---
# <a name="introduction-to-the-storsimple-virtual-array"></a>Wprowadzenie do tablicy wirtualnego StorSimple

## <a name="overview"></a>Przegląd

Tablicy wirtualne Microsoft Azure StorSimple to rozwiązanie zintegrowanego magazynu zarządzanego zadań magazynu między lokalnymi tablicy wirtualny działający w funkcji hypervisor i magazynu w chmurze Microsoft Azure. Tablica wirtualnego jest serwera plików wydajne, ekonomiczne i łatwe do zarządzania lub rozwiązania z serwerem iSCSI, która eliminuje wiele problemów i koszty związane z ochroną magazyn i dane przedsiębiorstwa. Tablica wirtualnego jest szczególnie dobrze nadaje się do przechowywania rzadziej używanych danych archiwalnych.

Ten artykuł zawiera omówienie wirtualnych tablicy — poniżej przedstawiono niektóre inne zasoby:

* Aby uzyskać najlepsze rozwiązania, zobacz [najlepsze rozwiązania w zakresie tablicy wirtualnego StorSimple](storsimple-ova-best-practices.md).
* Aby uzyskać przegląd urządzeń z serii StorSimple 8000, [z serii StorSimple 8000: rozwiązanie chmury hybrydowej](storsimple-overview.md).
* Aby uzyskać informacje dotyczące urządzeń z serii StorSimple 5000/7000, przejdź do [pomocy Online StorSimple](http://onlinehelp.storsimple.com/).

Tablica wirtualny obsługuje protokołu bloku komunikatów serwera (SMB) lub iSCSI. Go działa w istniejącej infrastrukturze funkcji hypervisor i oferuje obsługę poziomów w chmurze, kopia zapasowa w chmurze, szybkiego przywracania, odzyskiwania na poziomie elementu i funkcje odzyskiwania po awarii.

W poniższej tabeli przedstawiono ważne funkcje tablicy wirtualne StorSimple.

| Cecha | Macierz wirtualna usługi StorSimple |
| --- | --- |
| Wymagania dotyczące instalacji |Używa infrastruktury wirtualizacji (funkcja Hyper-V lub programu VMware) |
| Dostępność |Jeden węzeł |
| Łączna pojemność (w tym chmury) |Maksymalnie 64 TB pojemności można używać na tablicę wirtualnego |
| Wydajność lokalnej |390 GB do 6,4 TB można używać na tablicy wirtualne (wymagana do udostępnienia 500 GB do 8 TB miejsca na dysku) |
| Protokoły natywnego |SMB lub iSCSI |
| Cel czasu odzyskiwania (recovery time objective, RTO) |iSCSI: mniej niż 2 minuty, niezależnie od rozmiaru |
| Cel punktu odzyskiwania (recovery point objective, RPO) |Codzienne wykonywanie kopii zapasowych i kopii zapasowych na żądanie |
| Warstwy magazynowania |Używa ogrzewać mapowanie w celu określenia, jakie dane powinna należeć do warstwy przychodzący lub wychodzący |
| Pomoc techniczna |Obsługiwane przez dostawcę infrastruktury wirtualizacji |
| Wydajność |Może być różna w zależności od podstawowej infrastruktury |
| Przenoszenia danych |Można przywrócić do tego samego urządzenia lub poziomie elementu recovery (serwer plików) |
| Warstwy magazynowania |Magazyn lokalnych i w chmurze |
| Rozmiar udziału |Warstwy: maksymalnie 20 TB; przypięty lokalnie: maksymalnie 2 TB |
| Rozmiar woluminu |Warstwowe: 500 GB do 5 TB; przypięty lokalnie: 50 GB do 200 GB <br> Maksymalna lokalnego rezerwacji woluminy warstwowe jest 200 GB. |
| Migawki |Awarii |
| Odzyskiwanie na poziomie elementu |Tak; Użytkownicy mogą przywracać z udziałów |

## <a name="why-use-storsimple"></a>Dlaczego warto używać StorSimple?

Użytkownicy i serwery do magazynu Azure StorSimple łączy w minutach, bez żadnych modyfikacji aplikacji.

W poniższej tabeli opisano niektóre kluczowe korzyści, które rozwiązanie StorSimple tablicy wirtualnych zapewnia.

| Cecha | Korzyść |
| --- | --- |
| Integracja przezroczyste |Tablica wirtualny obsługuje protokołu SMB lub iSCSI. Przenoszenie danych między lokalnym warstwy i warstwy chmury jest łatwego i niezauważalne dla użytkownika. |
| Magazyn zmniejszenie kosztów |StorSimple należy zapewnić obsługę administracyjną wystarczającej ilości miejsca lokalnego do zaspokojenia potrzeb bieżącego najczęściej używanych gorących danych. Potrzeb magazynu powiększania, StorSimple warstw zimnych danych do ekonomicznego chmury magazynu. Dane są deduplikowane, a skompresowane przed wysłaniem do chmury w celu dalszego ograniczenia wymagania dotyczące magazynu i kosztów. |
| Uproszczone zarządzanie magazynem |StorSimple umożliwia scentralizowane zarządzanie w chmurze za pomocą Menedżera urządzeń StorSimple do zarządzania wieloma urządzeniami. |
| Ulepszonych funkcji odzyskiwania i zgodność |StorSimple umożliwia szybsze odzyskiwanie po awarii przez Przywracanie metadanych natychmiast i przywracanie danych, zgodnie z potrzebami. Oznacza to, że minimalnego przerw w działaniu można kontynuować wykonywania normalnych operacji. |
| Przenoszenia danych |Dane do warstwy do chmury można uzyskać z innych witryn do celów odzyskiwania i migracji. Należy pamiętać, dane można przywrócić tylko do oryginalnego tablicy wirtualnego. Jednak aby przywrócić cały wirtualny tablicy do innej tablicy wirtualnego przy użyciu funkcji odzyskiwania po awarii. |

## <a name="storsimple-workload-summary"></a>Podsumowanie obciążenia StorSimple

Podsumowanie obsługiwanych obciążeniach StorSimple jest przedstawione w poniższej tabeli.

|Scenariusz     |Obciążenie     |Obsługiwane      |Ograniczenia               |
|-------------|-------------|---------------|---------------------------|
|ROBO  |Udostępnianie plików     |Yes      |Zobacz [maksymalnych dla serwera plików](storsimple-ova-limits.md).<br></br>Zobacz [wymagania systemowe dotyczące obsługiwanych wersji protokołu SMB](storsimple-ova-system-requirements.md).| Wszystkie wersje     |
|Archiwizowanie w chmurze  |Udostępnianie plików archiwizacji     |Yes      |Zobacz [maksymalnych dla serwera plików](storsimple-ova-limits.md).<br></br>Zobacz [wymagania systemowe dotyczące obsługiwanych wersji protokołu SMB](storsimple-ova-system-requirements.md).| Wszystkie wersje     |

Tablica wirtualne StorSimple jest najbardziej odpowiednie dla rzadziej używanych danych. Podczas wirtualnego tablicy ma lokalnej pamięci podręcznej w celu zwiększania wydajności, użytkowników powinny założono, że urządzenie usług plików w najniższej warstwy magazynu (w chmurze). Każdy wirtualny tablicy można zapisu i odczytu do magazynu Azure około 100 MB/s. Łącze jest współużytkowana przez wszystkie żądania do urządzenia i może stać się wąskiego gardła, jak pokazano na poniższym diagramie.

![Archiwizowanie w chmurze](./media/storsimple-ova-overview/cloud-archiving.png)

Gdy wielu użytkowników równocześnie dostęp do wirtualnego tablicy, wszystkie mają połączenie Azure prowadzące do obniżenia wydajności. Istnieje nie gwarantuje wydajności dla każdego użytkownika i urządzenia przetwarza poszczególnych żądań przychodzących.

Tablica wirtualnego StorSimple nie jest odpowiedni dla obciążeń wymagających wysokiej dostępności. Tablica wirtualnego jest urządzenia z jednego węzła, które napotyka przestoju podczas instalowania aktualizacji oprogramowania. Administratorzy należy zaplanować okna obsługi 30 minut, 3 — 4 razy w roku.

## <a name="workflows"></a>Przepływy

Tablica wirtualnego StorSimple jest szczególnie przydatny w przypadku następujących przepływy pracy:

* [Zarządzanie magazynami oparte na chmurze](#cloud-based-storage-management)
* [Niezależnym od lokalizacji kopii zapasowej](#location-independent-backup)
* [Dane ochrony i odzyskiwania po awarii](#data-protection-and-disaster-recovery)

### <a name="cloud-based-storage-management"></a>Zarządzanie magazynami oparte na chmurze
Usługę Menedżer urządzeń StorSimple działającą w portalu Azure umożliwia zarządzanie danymi przechowywanymi na wielu urządzeniach i w wielu lokalizacjach. Jest to szczególnie przydatne w scenariuszach oddziału rozproszonych. Należy pamiętać, należy utworzyć oddzielne wystąpienia usługi Menedżer StorSimple urządzenia do zarządzania tablice wirtualne i fizyczne urządzenia StorSimple. Należy również zauważyć, że wirtualny tablicy teraz używa nowego portalu Azure zamiast klasycznego portalu Azure.

![Zarządzanie magazynami oparte na chmurze](./media/storsimple-ova-overview/cloud-based-storage-management.png)

### <a name="location-independent-backup"></a>Niezależnym od lokalizacji kopii zapasowej
Z tablicą wirtualnego migawki w chmurze dostarcza niezależnym od lokalizacji, w momencie kopię woluminu lub udziału. Migawki w chmurze są domyślnie włączone i nie można wyłączyć. Wszystkie woluminy i udziały są kopii zapasowej się jednocześnie za pośrednictwem jednej zasady tworzenia kopii zapasowej codziennie i należy wykonać dodatkowe ad hoc kopii zapasowych, gdy jest to niezbędne.

### <a name="data-protection-and-disaster-recovery"></a>Dane ochrony i odzyskiwania po awarii
Tablica wirtualny obsługuje następujące ochrony danych i scenariuszy odzyskiwania po awarii:

* **Przywracanie woluminu lub udziału** — Użyj przywracania jako nowego przepływu pracy, aby odzyskać wolumin lub udział. Takie podejście umożliwia odzyskanie całego woluminu lub udziału.
* **Element poziomu odzyskiwania** — udziałów dostęp do uproszczony ostatnie kopie zapasowe. Można łatwo odzyskać pojedynczy plik z specjalnego *.backup* folderów, które są dostępne w chmurze. Ta funkcja przywracania jest oparte na użytkownika i administracyjne interwencja nie jest wymagane.
* **Odzyskiwanie po awarii** — umożliwia odzyskiwanie, wszystkie woluminy lub udziały do tablicy wirtualnego nowych możliwości trybu failover. Utwórz nowy wirtualny tablicy i zarejestrowanie go za pomocą usługi Menedżer StorSimple urządzenia następnie awaryjnie oryginalny wirtualny tablicy. Nowy wirtualny tablicy przyjmie następnie udostępnione zasoby.

## <a name="storsimple-virtual-array-components"></a>Składniki tablicy wirtualnego StorSimple

Tablica wirtualnego obejmuje następujące składniki:

* [Tablica wirtualnego](#virtual-array) — urządzenie magazynujące hybrydowe chmury oparte na maszynie wirtualnej, udostępnione w środowisku zwirtualizowanym lub funkcji hypervisor.
* [Usługa Menedżera urządzeń StorSimple](#storsimple-device-manager-service) — jako rozszerzenia portalu Azure, która umożliwia zarządzanie co najmniej jedno urządzenie StorSimple z interfejsu jednej sieci web, którego można korzystać z położeniem geograficznym. Tworzenie i zarządzanie usługami, wyświetlać i zarządzać urządzeniami i alertami, i zarządzanie nimi woluminów, udziałów i migawek istniejącej, można użyć usługi Menedżer StorSimple urządzenia.
* [Interfejs użytkownika sieci web lokalnego](#local-web-user-interface) — oparte na sieci web interfejsu użytkownika, który służy do konfigurowania na urządzeniu, dzięki czemu można nawiązać połączenia z siecią lokalną i rejestrowania urządzenia w usłudze Menedżer urządzeń StorSimple. 
* [Interfejs wiersza polecenia](#command-line-interface) — interfejsu A Windows PowerShell, który można użyć, aby rozpocząć sesję pomocy technicznej w macierzy wirtualnego.
  W poniższych sekcjach opisano każdy z tych składników bardziej szczegółowo i wyjaśnić sposób rozwiązania rozmieszcza danych przydziela magazynu i ułatwia zarządzanie magazynami i ochrony danych.

### <a name="virtual-array"></a>Macierz wirtualna

Tablica wirtualnego jest rozwiązanie jednowęzłowej magazynu, które zapewnia podstawowy magazyn, zarządza komunikacją z magazynu w chmurze i pomaga zapewnić bezpieczeństwo i poufność wszystkich danych przechowywanych na urządzeniu.

Tablica wirtualnego jest dostępna w jednym modelu, który jest dostępny do pobrania. Wirtualne tablicy ma maksymalną pojemność 6,4 TB na urządzeniu (z podstawowej wymaganie magazynu o rozmiarze 8 TB) i tym 64 TB magazynu w chmurze.

Wirtualne tablica zawiera następujące funkcje:

* Jest kosztowne. Go sprawia, że wykorzystanie istniejącej infrastruktury wirtualizacji i może zostać wdrożony w sieci istniejących funkcji hypervisor Hyper-V lub VMware.
* Znajduje się w centrum danych, a może być skonfigurowany jako serwer iSCSI lub na serwerze plików.
* Jest zintegrowany z chmurą.
* Kopie zapasowe są przechowywane w chmurze, która może ułatwić odzyskiwanie po awarii i uprościć odzyskiwanie na poziomie elementu (ILR).
* Aktualizacje można stosować do wirtualnego tablicy, tak samo, jak może zastosować je do urządzenia fizycznego.

> [!NOTE]
> Nie można rozwijać wirtualnego tablicy. W związku z tym należy udostępnić odpowiednie magazynu podczas tworzenia wirtualnej tablicy.

### <a name="storsimple-device-manager-service"></a>Usługa menedżera urządzeń StorSimple

Microsoft Azure StorSimple udostępnia interfejs użytkownika sieci web, usługi Menedżera urządzeń StorSimple, która umożliwia centralne zarządzanie magazynu StorSimple. Usługę Menedżer StorSimple urządzenia umożliwia wykonywanie następujących zadań:

* Zarządzanie wiele tablic wirtualnych StorSimple z jednej usługi.
* Konfigurowanie i zarządzanie ustawieniami zabezpieczeń dla tablic wirtualne StorSimple. (Szyfrowanie w chmurze jest zależna od interfejsów API usługi Microsoft Azure).
* Konfigurowanie poświadczeń konta magazynu i właściwości.
* Konfigurowanie i zarządzanie nimi woluminy lub udziały.
* Tworzenie kopii zapasowej i przywracania danych na woluminy lub udziały.
* Monitorowanie wydajności.
* Przejrzyj ustawienia systemu i identyfikowania potencjalnych problemów.

Usługę Menedżer StorSimple urządzenia umożliwia wykonywać codzienne zadania administracyjne macierzy wirtualnego.

Aby uzyskać więcej informacji, przejdź do [zarządzać urządzenia StorSimple przy użyciu usługi Menedżer StorSimple urządzenia](storsimple-virtual-array-manager-service-administration.md).

### <a name="local-web-user-interface"></a>Interfejs użytkownika sieci web lokalnego

Tablica wirtualnego obejmuje opartych na sieci web interfejsu użytkownika, który jest używany do jednorazowej konfiguracji i rejestracji urządzenia w usłudze Menedżer StorSimple urządzenia. Można go zamknąć i ponownie uruchomić wirtualnego tablicy, uruchamiania testów diagnostycznych, aktualizacji oprogramowania, Zmień hasło administratora urządzenia, wyświetlać dzienniki systemu i skontaktuj się z Microsoft Support do pliku żądania obsługi.

Aby dowiedzieć się, jak za pomocą interfejsu użytkownika sieci web, przejdź do [zarządzać tablica wirtualnego StorSimple przy użyciu interfejsu użytkownika sieci web](storsimple-ova-web-ui-admin.md).

### <a name="command-line-interface"></a>Interfejs wiersza polecenia

Uwzględnione interfejsu programu Windows PowerShell można zainicjować sesję pomocy technicznej z Microsoft Support, dzięki czemu mogą one pomóc rozwiązywania oraz usuwania problemów, które mogą wystąpić w sieci wirtualnej macierzy.

## <a name="storage-management-technologies"></a>Technologie magazynowania zarządzania

Oprócz tablicy wirtualnych i inne składniki rozwiązania StorSimple używa następujące technologie zapewniają szybki dostęp do ważnych danych, zmniejszenia użycia magazynu i ochrony danych znajdujących się w sieci wirtualnej tablicy:

* [Warstwy magazynowania automatyczne](#automatic-storage-tiering) 
* [I woluminów przypiętych lokalnie udziałów](#locally-pinned-shares-and-volumes)
* [Na potrzeby deduplikacji i kompresji danych do warstwy lub kopii zapasowej w chmurze](#deduplication-and-compression-for-data-tiered/backed-up-to-the-cloud) 
* [Tworzenie kopii zapasowych zaplanowanych, jak i na żądanie](#scheduled-and-on-demand-backups)

### <a name="automatic-storage-tiering"></a>Warstwy magazynowania automatyczne
Do zarządzania przechowywanych danych w tablicy wirtualne i chmury wirtualne tablicy używane nowego mechanizmu warstw. Istnieją tylko dwa warstw: lokalnej tablicy wirtualne i usługi Azure magazynu w chmurze. Tablica wirtualnego StorSimple automatycznie rozmieszcza danych warstwami w zależności od Mapa cieplna, który śledzi bieżące użycie, wieku oraz relacje z innymi danymi. Dane, które jest najbardziej aktywne (najnowszych) są przechowywane lokalnie, podczas gdy mniej aktywnych i nieaktywnych dane są automatycznie migrowane do chmury. (Wszystkie kopie zapasowe są przechowywane w chmurze). StorSimple można dostosować i Reorganizuje dane i zmień przydziałów magazynowania jako wzorców użycia. Na przykład niektóre informacje mogą stać się mniej aktywne w czasie. Ponieważ staje się stopniowo mniej aktywne, jest warstwowa wychodzących do chmury. Jeśli ten sam danych stanie się ponownie aktywna, jest warstwowa w do tablicy magazynu.

Dane dla danego udziału warstwowych lub woluminu jest gwarantowana własną przestrzeń warstwie lokalnej (około 10% całkowitej elastycznie miejsca dostępnego dla tego udziału lub wolumin). Podczas zmniejsza dostępną pamięć masową na wirtualnego macierzy w tym udziale lub woluminie, gwarantuje to, że obsługa poziomów dla jednego udziału lub wolumin nie wpłynie warstw potrzeb inne udziały lub woluminy. W związku z tym bardzo zajęty obciążenie jednego udziału lub wolumin nie może wymusić innych obciążeń do chmury.

Woluminy warstwowe utworzone dla interfejsu iSCSI ma maksymalną lokalnego rezerwacji 200 GB niezależnie od rozmiaru woluminu.

![Warstwy magazynowania automatyczne](./media/storsimple-ova-overview/automatic-storage-tiering.png)

> [!NOTE]
> Można określić wolumin przypięty lokalnie, w takim przypadku danych pozostaje w macierzy wirtualnego i nie jest nigdy do warstwy do chmury. Aby uzyskać więcej informacji, przejdź do [przypięty lokalnie, woluminów i udziałów](#locally-pinned-shares-and-volumes).


### <a name="locally-pinned-shares-and-volumes"></a>I woluminów przypiętych lokalnie udziałów

Możesz utworzyć odpowiednie udziały i woluminy przypięte lokalnie. Ta funkcja zapewnia, że dane wymagane przez aplikacje krytyczne pozostaje w tablicy wirtualnego i nigdy nie jest do warstwy do chmury. I woluminów przypiętych lokalnie udziałów oferują następujące funkcje:

* Nie są one chmury opóźnienia i problemy z połączeniem.
* One nadal korzystać z StorSimple chmury kopii zapasowych i odzyskiwaniem po awarii funkcji odzyskiwania.

Można przywrócić udziału przypiętych lokalnie lub woluminie warstwowej lub warstwowych udziału lub przypięty lokalnie woluminu. 

Aby uzyskać więcej informacji na temat woluminów przypiętych lokalnie, przejdź do [Zarządzanie woluminami przy użyciu usługi Menedżer StorSimple urządzenia](storsimple-virtual-array-manage-volumes.md).

### <a name="deduplication-and-compression-for-data-tiered-or-backed-up-to-the-cloud"></a>Na potrzeby deduplikacji i kompresji danych do warstwy lub kopii zapasowej w chmurze

StorSimple używa kompresji danych i deduplikacji w celu dalszego ograniczenia wymagania dotyczące magazynu w chmurze. Funkcja deduplikacji zmniejsza ogólną ilość danych przechowywanych przez wyeliminowanie nadmiarowości w zestawie przechowywanych danych. Informacje o zmian StorSimple ignoruje niezmienione dane i przechwytywanie tylko zmiany. Ponadto StorSimple zmniejsza ilość przechowywanych danych identyfikowanie i usuwając zduplikowane informacje.

> [!NOTE]
> Dane przechowywane w tablicy wirtualnego nie jest deduplikowany lub skompresowane. Wszystkie deduplikacji i kompresji występuje tuż przed dane są wysyłane do chmury.

### <a name="scheduled-and-on-demand-backups"></a>Tworzenie kopii zapasowych zaplanowanych, jak i na żądanie

Funkcje ochrony danych StorSimple pozwalają na tworzenie kopii zapasowych na żądanie. Ponadto domyślny harmonogram tworzenia kopii zapasowej zapewnia, że jest wykonywana kopia zapasowa codziennie danych. Kopie zapasowe są pobierane w postaci migawki przyrostowe, które są przechowywane w chmurze. Migawki, w których rejestrowane tylko zmiany od ostatniej kopii zapasowej, można tworzyć i szybko przywrócić. Te migawki może być bardzo ważny w scenariuszach odzyskiwania po awarii, ponieważ zastąpić systemy dodatkowej magazynu (na przykład kopii zapasowej na taśmie) i umożliwiają przywrócenie danych do centrum danych lub alternatywnej witryn, w razie potrzeby.

## <a name="gdpr-compliance"></a>GDPR zgodności
[Rozporządzenia ogólne ochrony danych (GDPR)](http://ec.europa.eu/justice/data-protection/reform/index_en.htm) jest prawo ochrona i prywatność danych Unii Europejskiej (UE). GDPR zawiera wiele wymagań dotyczących sposobu zbierania, przechowywania i Użyj informacji osobistych. Reguły GDPR nakłada się na firm, agencji rządowych i innych organizacji, które działają w UE i zbierać, Analizuj dane powiązane mieszkańców Unii Europejskiej.

Menedżer urządzeń StorSimple serii wirtualnego jest GDPR zgodne. Istnieją dwa wystąpienia klucza, gdzie informacje osobiste są zbierane i wyświetlane w usłudze:
 - Alert, gdy adresy e-mail użytkowników są skonfigurowane ustawienia użytkownika. Te informacje można wyczyścić przez administratora. 
 - Użytkownicy, którzy mają dostęp do danych znajdujących się w akcji. Listę użytkowników, którzy mogą uzyskiwać dostęp do danych udziałów jest wyświetlana i mogą być eksportowane. Ta lista również zostanie usunięta po usunięciu akcji.

Aby uzyskać więcej informacji, przejrzyj [zasad Privacy firmy Microsoft w Centrum zaufania](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [przygotowanie portalu wirtualnego tablicy](storsimple-virtual-array-deploy1-portal-prep.md).
