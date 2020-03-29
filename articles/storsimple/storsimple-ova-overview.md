---
title: Omówienie tablicy wirtualnej usługi Microsoft Azure StorSimple
description: W tym artykule opisano system StorSimple Virtual Array , zintegrowane rozwiązanie magazynu, które zarządza zadaniami magazynu między lokalną macierzą wirtualną a magazynem w chmurze platformy Microsoft Azure.
author: alkohli
ms.assetid: 169c639b-1124-46a5-ae69-ba9695525b77
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 32781a83aec996b23f161f5fe695f39a0de38685
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76273865"
---
# <a name="introduction-to-the-storsimple-virtual-array"></a>Wprowadzenie do tablicy wirtualnej StorSimple

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

## <a name="overview"></a>Omówienie

Microsoft Azure StorSimple Virtual Array to zintegrowane rozwiązanie magazynu, które zarządza zadaniami magazynu między lokalną macierzą wirtualną działającą w hipernadzorcy a magazynem w chmurze platformy Microsoft Azure. Macierz wirtualna jest wydajnym, ekonomicznym i łatwo zarządzanym serwerem plików lub rozwiązaniem serwera iSCSI, które eliminuje wiele problemów i wydatków związanych z pamięcią masową i ochroną danych w przedsiębiorstwie. Tablica wirtualna jest szczególnie dobrze nadaje się do przechowywania rzadko dostępnych danych archiwalnych.

Ten artykuł zawiera omówienie tablicy wirtualnej — oto kilka innych zasobów:

* Aby uzyskać najlepsze rozwiązania, zobacz [StorSimple Virtual Array najlepszych praktyk.](storsimple-ova-best-practices.md)
* Przegląd urządzeń z serii StorSimple 8000 można uzyskać, przejdź do [serii StorSimple 8000: rozwiązanie chmury hybrydowej.](storsimple-overview.md)
* Aby uzyskać informacje na temat urządzeń z serii StorSimple 5000/7000, przejdź do [strony StorSimple Online Help](http://onlinehelp.storsimple.com/).

Tablica wirtualna obsługuje protokół iSCSI lub Server Message Block (SMB). Działa na istniejącej infrastrukturze funkcji hypervisor i zapewnia warstwowe do chmury, kopii zapasowej w chmurze, szybkiego przywracania, odzyskiwania na poziomie elementu i funkcji odzyskiwania po awarii.

W poniższej tabeli podsumowano ważne funkcje tablicy wirtualnej StorSimple.

| Funkcja | Macierz wirtualna usługi StorSimple |
| --- | --- |
| Wymagania dotyczące instalacji |Wykorzystuje infrastrukturę wirtualizacji (Hyper-V lub VMware) |
| Dostępność |Jeden węzeł |
| Całkowita pojemność (łącznie z chmurą) |Do 64 TB pojemności użytkowej na macierz wirtualny |
| Pojemność lokalna |Pojemność użytkowa od 390 GB do 6,4 TB na macierz wirtualną (potrzeba udostępnienia 500 GB do 8 TB miejsca na dysku) |
| Protokoły macierzyste |iSCSI lub SMB |
| Cel czasu odzyskiwania (recovery time objective, RTO) |iSCSI: mniej niż 2 minuty bez względu na rozmiar |
| Cel punktu odzyskiwania (recovery point objective, RPO) |Codzienne kopie zapasowe i kopie zapasowe na żądanie |
| Warstwowanie magazynu |Używa mapowania ciepła do określenia, jakie dane powinny być warstwowe w lub na zewnątrz |
| Pomoc techniczna |Infrastruktura wirtualizacji obsługiwana przez dostawcę |
| Wydajność |Różni się w zależności od podstawowej infrastruktury |
| Mobilność danych |Można przywrócić do tego samego urządzenia lub do odzyskiwania na poziomie elementu (serwer plików) |
| Warstwy magazynowania |Lokalna pamięć masowa hypervisor i chmura |
| Rozmiar udziału |Warstwowe: do 20 TB; lokalnie przypięte: do 2 TB |
| Rozmiar woluminu |Warstwowe: od 500 GB do 5 TB; lokalnie przypięte: od 50 GB do 200 GB <br> Maksymalna rezerwacja lokalna dla woluminów warstwowych wynosi 200 GB. |
| Migawki |Spójność na poziomie awarii |
| Odzyskiwanie na poziomie elementu |Tak. użytkownicy mogą przywracać z udziałów |

## <a name="why-use-storsimple"></a>Dlaczego warto skorzystać z StorSimple?

StorSimple łączy użytkowników i serwery z magazynem platformy Azure w ciągu kilku minut, bez modyfikacji aplikacji.

W poniższej tabeli opisano niektóre z kluczowych korzyści, które zapewnia rozwiązanie StorSimple Virtual Array.

| Funkcja | Korzyść |
| --- | --- |
| Przejrzysta integracja |Tablica wirtualna obsługuje protokół iSCSI lub SMB. Przenoszenie danych między warstwą lokalną a warstwą chmury jest bezproblemowe i przejrzyste dla użytkownika. |
| Niższe koszty magazynowania |Za pomocą StorSimple aprowizujesz wystarczającą ilość magazynu lokalnego, aby sprostać bieżącym wymaganiom najczęściej używanych danych gorących. Wraz ze wzrostem potrzeb związanych z magazynowaniem warstwy StorSimple są zimne dane w ekonomicznej pamięci masowej w chmurze. Dane są deduplikowane i kompresowane przed wysłaniem do chmury w celu dalszego zmniejszenia wymagań dotyczących magazynu i kosztów. |
| Uproszczone zarządzanie pamięcią masową |StorSimple zapewnia scentralizowane zarządzanie w chmurze za pomocą StorSimple Device Manager do zarządzania wieloma urządzeniami. |
| Ulepszone odzyskiwanie po awarii i zgodność z przepisami |StorSimple ułatwia szybsze odzyskiwanie po awarii, przywracając metadane natychmiast i przywracając dane w razie potrzeby. Oznacza to, że normalne operacje mogą być kontynuowane przy minimalnych zakłóceniach. |
| Mobilność danych |Dane warstwowe w chmurze są dostępne z innych lokacji w celu odzyskiwania i migracji. Należy zauważyć, że dane można przywrócić tylko do oryginalnej tablicy wirtualnej. Jednak za pomocą funkcji odzyskiwania po awarii, aby przywrócić całą tablicę wirtualną do innej tablicy wirtualnej. |

## <a name="storsimple-workload-summary"></a>StorSimple podsumowanie obciążenia

Podsumowanie obsługiwanych obciążeń StorSimple jest tabelaryczne poniżej.

|Scenariusz     |Obciążenie     |Obsługiwane      |Ograniczenia               | Dostępne wersje|
|-------------|-------------|---------------|---------------------------|--------------------|
|Zdalny oddział/oddział (ROBO)  |Udostępnianie plików     |Tak      |Zobacz [maksymalne limity dla serwera plików](storsimple-ova-limits.md).<br></br>Zobacz [wymagania systemowe dotyczące obsługiwanych wersji SMB](storsimple-ova-system-requirements.md).| Wszystkie wersje     |
|Archiwizacja w chmurze  |Udostępnianie plików archiwalnych     |Tak      |Zobacz [maksymalne limity dla serwera plików](storsimple-ova-limits.md).<br></br>Zobacz [wymagania systemowe dotyczące obsługiwanych wersji SMB](storsimple-ova-system-requirements.md).| Wszystkie wersje     |

StorSimple Virtual Array najlepiej nadaje się do rzadko dostępnych danych. Podczas gdy tablica wirtualna ma lokalną pamięć podręczną, aby zwiększyć wydajność, użytkownicy powinni założyć, że usługi urządzenia są pliki w najniższej warstwie magazynu (w chmurze). Każda macierz wirtualna może pisać i odczytywać do magazynu platformy Azure z prędkością około 100 Mb/s. To łącze jest współużytkowane przez wszystkie żądania przychodzące do urządzenia i może stać się wąskim gardłem, jak pokazano na poniższym diagramie.

![Archiwizacja w chmurze](./media/storsimple-ova-overview/cloud-archiving.png)

Gdy wielu równoczesnych użytkowników uzyskać dostęp do tablicy wirtualnej, wszystkie one współużytkują połączenie z platformą Azure, co prowadzi do niższej wydajności. Nie ma gwarantowanej wydajności na użytkownika, a urządzenie przetwarza poszczególne żądania po ich przybyciu.

StorSimple Virtual Array nie nadaje się do obciążeń, które wymagają wysokiej dostępności. Macierz wirtualna jest urządzeniem z jednym węzłem, które doświadcza przestojów po zainstalowaniu aktualizacji oprogramowania. Administratorzy powinni zaplanować okno konserwacji 30 minut 3-4 razy w roku.

## <a name="workflows"></a>Przepływy

Tablica wirtualna StorSimple jest szczególnie odpowiednia dla następujących przepływów pracy:

* [Zarządzanie pamięcią masową w chmurze](#cloud-based-storage-management)
* [Kopia zapasowa niezależna od lokalizacji](#location-independent-backup)
* [Ochrona danych i odzyskiwanie po awarii](#data-protection-and-disaster-recovery)

### <a name="cloud-based-storage-management"></a>Zarządzanie pamięcią masową w chmurze
Usługi StorSimple Device Manager uruchomionej w witrynie Azure Portal można używać do zarządzania danymi przechowywanymi na wielu urządzeniach i w wielu lokalizacjach. Jest to szczególnie przydatne w scenariuszach gałęzi rozproszonych. Należy zauważyć, że należy utworzyć oddzielne wystąpienia usługi StorSimple Device Manager do zarządzania tablicami wirtualnymi i fizycznymi urządzeniami StorSimple. Należy również zauważyć, że tablica wirtualna używa teraz nowej witryny Azure portal zamiast klasycznego portalu platformy Azure.

![zarządzanie pamięcią masową w chmurze](./media/storsimple-ova-overview/cloud-based-storage-management.png)

### <a name="location-independent-backup"></a>Kopia zapasowa niezależna od lokalizacji
Dzięki macierzy wirtualnej migawki w chmurze zapewniają niezależną od lokalizacji kopię woluminu lub udziału w programie. Migawki w chmurze są domyślnie włączone i nie można ich wyłączyć. Wszystkie woluminy i udziały są tworzenie kopii zapasowych w tym samym czasie za pośrednictwem jednej codziennej zasady tworzenia kopii zapasowych i można wykonać dodatkowe kopie zapasowe ad hoc, gdy jest to konieczne.

### <a name="data-protection-and-disaster-recovery"></a>Ochrona danych i odzyskiwanie po awarii
Tablica wirtualna obsługuje następujące scenariusze ochrony danych i odzyskiwania po awarii:

* **Przywracanie woluminu lub udziału** — odzyskiwanie woluminu lub udziału umożliwia przywrócenie jako nowy przepływ pracy. Użyj tej metody, aby odzyskać cały wolumin lub udział.
* **Odzyskiwanie na poziomie towaru** — udziały umożliwiają uproszczony dostęp do ostatnich kopii zapasowych. Możesz łatwo odzyskać pojedynczy plik ze specjalnego folderu *.backup* dostępnego w chmurze. Ta funkcja przywracania jest zależna od użytkownika i nie jest wymagana żadna interwencja administracyjna.
* **Odzyskiwanie po awarii** — użyj funkcji pracy awaryjnej, aby odzyskać wszystkie woluminy lub udziały do nowej tablicy wirtualnej. Utwórz nową tablicę wirtualną i zarejestruj ją za pomocą usługi StorSimple Device Manager, a następnie przełącz oryginalną tablicę wirtualną w tryb fail. Nowa tablica wirtualna następnie przyjmie aprowizowanych zasobów.

## <a name="storsimple-virtual-array-components"></a>Składniki tablicy wirtualnej StorSimple

Tablica wirtualna zawiera następujące składniki:

* [Tablica wirtualna](#virtual-array) — hybrydowe urządzenie magazynujące w chmurze oparte na maszynie wirtualnej aprowizowanej w środowisku zwirtualizowanym lub hipernadzorcy.
* [Usługa StorSimple Device Manager](#storsimple-device-manager-service) — rozszerzenie portalu Azure, które umożliwia zarządzanie jednym lub kilkoma urządzeniami StorSimple z jednego interfejsu sieci web, do którego można uzyskać dostęp z różnych lokalizacji geograficznych. Za pomocą usługi StorSimple Device Manager można tworzyć usługi, wyświetlać i zarządzać urządzeniami i alertami oraz zarządzać nimi oraz zarządzać woluminami, udziałami i istniejącymi migawkami.
* [Lokalny interfejs użytkownika sieci Web](#local-web-user-interface) — interfejs użytkownika oparty na sieci Web, który służy do konfigurowania urządzenia tak, aby można było połączyć się z siecią lokalną, a następnie zarejestrować urządzenie za pomocą usługi StorSimple Device Manager. 
* [Interfejs wiersza polecenia](#command-line-interface) — interfejs programu Windows PowerShell, którego można użyć do uruchomienia sesji pomocy technicznej w macierzy wirtualnej.
  W poniższych sekcjach opisano każdy z tych składników bardziej szczegółowo i wyjaśnić, jak rozwiązanie organizuje dane, przydziela magazyn i ułatwia zarządzanie magazynem i ochrony danych.

### <a name="virtual-array"></a>Tablica wirtualna

Macierz wirtualna jest rozwiązaniem magazynującym z jednym węzłem, które zapewnia magazyn podstawowy, zarządza komunikacją z magazynem w chmurze i pomaga zapewnić bezpieczeństwo i poufność wszystkich danych przechowywanych na urządzeniu.

Tablica wirtualna jest dostępna w jednym modelu, który jest dostępny do pobrania. Macierz wirtualna ma maksymalną pojemność 6,4 TB na urządzeniu (z podstawowym wymaganiem pamięci masowej 8 TB) i 64 TB, w tym magazynu w chmurze.

Tablica wirtualna ma następujące funkcje:

* Jest to opłacalne. Korzysta z istniejącej infrastruktury wirtualizacji i można go wdrożyć w istniejącym hipernadzorcy funkcji Hyper-V lub VMware.
* Znajduje się w centrum danych i może być skonfigurowany jako serwer iSCSI lub serwer plików.
* Jest zintegrowany z chmurą.
* Kopie zapasowe są przechowywane w chmurze, co może ułatwić odzyskiwanie po awarii i uprościć odzyskiwanie na poziomie elementu (ILR).
* Można zastosować aktualizacje do tablicy wirtualnej, tak jak można zastosować je do urządzenia fizycznego.

> [!NOTE]
> Nie można rozwinąć tablicy wirtualnej. W związku z tym ważne jest, aby aprowizować odpowiednią pamięć masową podczas tworzenia tablicy wirtualnej.

### <a name="storsimple-device-manager-service"></a>Usługa Menedżer urządzeń StorSimple

Usługa Microsoft Azure StorSimple udostępnia internetowy interfejs użytkownika, usługę StorSimple Device Manager, która umożliwia centralne zarządzanie magazynem StorSimple. Za pomocą usługi StorSimple Device Manager można wykonywać następujące zadania:

* Zarządzanie wieloma tablicami wirtualnymi StorSimple z jednej usługi.
* Konfigurowanie ustawień zabezpieczeń dla macierzy wirtualnych StorSimple i zarządzanie nimi. (Szyfrowanie w chmurze zależy od interfejsów API platformy Microsoft Azure).
* Konfigurowanie poświadczeń i właściwości konta magazynu.
* Konfigurowanie woluminów lub udziałów oraz zarządzanie nimi.
* Dziel zapasowe i przywracaj dane dotyczące woluminów lub udziałów.
* Monitoruj wydajność.
* Przejrzyj ustawienia systemu i zidentyfikuj możliwe problemy.

Usługa StorSimple Device Manager służy do codziennego administrowania macierzą wirtualną.

Aby uzyskać więcej informacji, przejdź do [witryny Zarządzanie urządzeniem StorSimple za pomocą usługi StorSimple Device Manager](storsimple-virtual-array-manager-service-administration.md).

### <a name="local-web-user-interface"></a>Lokalny interfejs użytkownika sieci Web

Tablica wirtualna zawiera interfejs użytkownika oparty na sieci Web, który jest używany do jednorazowej konfiguracji i rejestracji urządzenia za pomocą usługi StorSimple Device Manager. Można go używać do zamykania i ponownego uruchamiania macierzy wirtualnej, uruchamiania testów diagnostycznych, aktualizowania oprogramowania, zmieniania hasła administratora urządzenia, przeglądania dzienników systemowych i kontaktowania się z pomocą techniczną firmy Microsoft w celu złożenia żądania usługi.

Aby uzyskać informacje na temat korzystania z interfejsu użytkownika opartego na sieci Web, przejdź do [witryny Użyj interfejsu użytkownika opartego na sieci Web do administrowania tablicą wirtualną StorSimple](storsimple-ova-web-ui-admin.md).

### <a name="command-line-interface"></a>Interfejs wiersza polecenia

Dołączony interfejs programu Windows PowerShell umożliwia zainicjowanie sesji pomocy technicznej z pomocą techniczną firmy Microsoft, dzięki czemu mogą one pomóc w rozwiązywaniu problemów, które mogą wystąpić w macierzy wirtualnej.

## <a name="storage-management-technologies"></a>Technologie zarządzania pamięcią masową

Oprócz macierzy wirtualnej i innych składników rozwiązanie StorSimple wykorzystuje następujące technologie oprogramowania, aby zapewnić szybki dostęp do ważnych danych, zmniejszyć zużycie pamięci masowej i chronić dane przechowywane w macierzy wirtualnej:

* [Automatyczne tworzenie warstw pamięci masowej](#automatic-storage-tiering) 
* [Lokalnie przypięte udziały i wolumeny](#locally-pinned-shares-and-volumes)
* Deduplikacja i kompresja danych warstwowych lub kopii zapasowej w chmurze 
* [Zaplanowane kopie zapasowe na żądanie](#scheduled-and-on-demand-backups)

### <a name="automatic-storage-tiering"></a>Automatyczne tworzenie warstw pamięci masowej
Tablica wirtualna używa nowego mechanizmu warstwowego do zarządzania przechowywanymi danymi w macierzy wirtualnej i w chmurze. Istnieją tylko dwie warstwy: lokalna macierz wirtualna i magazyn w chmurze platformy Azure. Tablica wirtualna StorSimple automatycznie rozmieszcza dane w warstwach na podstawie mapy cieplnej, która śledzi bieżące użycie, wiek i relacje z innymi danymi. Dane, które są najbardziej aktywne (najgorętsze) są przechowywane lokalnie, podczas gdy mniej aktywne i nieaktywne dane są automatycznie migrowane do chmury. (Wszystkie kopie zapasowe są przechowywane w chmurze). StorSimple dostosowuje i zmienia przydziały danych i magazynu w miarę zmiany wzorców użycia. Na przykład niektóre informacje mogą stać się mniej aktywne w czasie. W miarę jak staje się stopniowo mniej aktywny, jest warstwowy do chmury. Jeśli te same dane stają się aktywne ponownie, jest warstwowy w macierzy magazynu.

Dane dla określonego udziału warstwowego lub woluminu są gwarantowane jego własne lokalne miejsce warstwy (około 10% całkowitej aprowizowanego miejsca dla tego udziału lub woluminu). Chociaż zmniejsza to dostępnego magazynu w macierzy wirtualnej dla tego udziału lub woluminu, gwarantuje, że warstwowe dla jednego udziału lub woluminu nie będzie miało wpływu na potrzeby warstwowe innych udziałów lub woluminów. W związku z tym bardzo zajęty obciążenia na jeden udział lub wolumin nie może wymusić wszystkie inne obciążenia do chmury.

Woluminy warstwowe utworzone dla interfejsu iSCSI mają maksymalną rezerwację lokalną 200 GB, niezależnie od rozmiaru woluminu.

![automatyczne tworzenie warstw pamięci masowej](./media/storsimple-ova-overview/automatic-storage-tiering.png)

> [!NOTE]
> Wolumin można określić jako przypięty lokalnie, w którym to przypadku dane pozostają w macierzy wirtualnej i nigdy nie są warstwowe w chmurze. Aby uzyskać więcej informacji, przejdź do [lokalnie przypiętych udziałów i woluminów](#locally-pinned-shares-and-volumes).


### <a name="locally-pinned-shares-and-volumes"></a>Lokalnie przypięte udziały i wolumeny

Można utworzyć odpowiednie udziały i woluminy jako przypięte lokalnie. Ta funkcja zapewnia, że dane wymagane przez krytyczne aplikacje pozostają w macierzy wirtualnej i nigdy nie są warstwowe w chmurze. Lokalnie przypięte udziały i woluminy mają następujące funkcje:

* Nie podlegają one opóźnieniom w chmurze ani problemom z łącznością.
* Nadal korzystają z funkcji tworzenia kopii zapasowych i odzyskiwania po awarii storsimple w chmurze.

Można przywrócić lokalnie przypięty udział lub wolumin jako warstwowy lub udział warstwowy lub wolumin jako przypięty lokalnie. 

Aby uzyskać więcej informacji na temat woluminów przypiętych lokalnie, przejdź do [strony Zarządzanie woluminami za pomocą usługi StorSimple Device Manager](storsimple-virtual-array-manage-volumes.md).

### <a name="deduplication-and-compression-for-data-tiered-or-backed-up-to-the-cloud"></a>Deduplikacja i kompresja danych warstwowych lub kopii zapasowej w chmurze

StorSimple używa deduplikacji i kompresji danych, aby jeszcze bardziej zmniejszyć wymagania dotyczące magazynu w chmurze. Deduplikacja zmniejsza ogólną ilość przechowywanych danych, eliminując nadmiarowość w przechowywanym zestawie danych. W miarę zmiany informacji StorSimple ignoruje niezmienione dane i przechwytuje tylko zmiany. Ponadto StorSimple zmniejsza ilość przechowywanych danych, identyfikując i usuwając zduplikowane informacje.

> [!NOTE]
> Dane przechowywane w tablicy wirtualnej nie są deduplikowane ani kompresowane. Wszystkie deduplikacji i kompresji występuje tuż przed dane są wysyłane do chmury.

### <a name="scheduled-and-on-demand-backups"></a>Zaplanowane kopie zapasowe na żądanie

Funkcje ochrony danych StorSimple umożliwiają tworzenie kopii zapasowych na żądanie. Ponadto domyślny harmonogram tworzenia kopii zapasowych zapewnia, że dane są archiwizowane codziennie. Kopie zapasowe są podejmowane w postaci migawek przyrostowych, które są przechowywane w chmurze. Migawki, które rejestrują tylko zmiany od czasu ostatniej kopii zapasowej, można szybko utworzyć i przywrócić. Te migawki mogą być niezwykle ważne w scenariuszach odzyskiwania po awarii, ponieważ zastępują pomocnicze systemy magazynowania (takie jak kopia zapasowa na taśmie) i umożliwiają przywracanie danych do centrum danych lub w razie potrzeby do alternatywnych lokacji.

## <a name="managing-personal-information"></a>Zarządzanie danymi osobowymi

Menedżer urządzeń StorSimple dla serii wirtualnych zbiera informacje osobiste w dwóch kluczowych wystąpieniach:
 - Alert ustawień użytkownika, w których skonfigurowane są adresy e-mail użytkowników. Te informacje mogą zostać wyczyszczone przez administratora. 
 - Użytkownicy, którzy mogą uzyskać dostęp do danych zamieszkałych w udziałach. Zostanie wyświetlona lista użytkowników, którzy mogą uzyskać dostęp do danych udziału i można ją wyeksportować. Ta lista jest również usuwana po usunięciu udziałów.

Aby uzyskać więcej informacji, zapoznaj się z [zasadami zachowania poufności firmy Microsoft w Centrum zaufania](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [przygotować portal tablic wirtualnych](storsimple-virtual-array-deploy1-portal-prep.md).
