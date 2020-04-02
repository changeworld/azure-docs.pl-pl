---
title: Reguły akcji dla alertów usługi Azure Monitor
description: Opis reguł akcji w usłudze Azure Monitor oraz sposobu konfigurowania ich i zarządzania nimi.
ms.topic: conceptual
ms.date: 04/25/2019
ms.subservice: alerts
ms.openlocfilehash: 0498325984641641d6dfc9ee6b89f66800b5c7d2
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546482"
---
# <a name="action-rules-preview"></a>Reguły akcji (wersja zapoznawcza)

Reguły akcji ułatwiządefiniowanie lub pomijanie akcji w dowolnym zakresie usługi Azure Resource Manager (subskrypcja platformy Azure, grupa zasobów lub zasób docelowy). Mają różne filtry, które pomagają zawęzić określony podzbiór wystąpień alertów, które chcesz działać na.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4qXeL]

## <a name="why-and-when-should-you-use-action-rules"></a>Dlaczego i kiedy należy używać reguł akcji?

### <a name="suppression-of-alerts"></a>Tłumienie alertów

Istnieje wiele scenariuszy, w których warto pominąć powiadomienia generowane przez alerty. Te scenariusze wahają się od wygaszenia podczas okna planowanej konserwacji do tłumienia w godzinach pracy. Na przykład zespół odpowiedzialny za **ContosoVM** chce pominąć powiadomienia alertów na nadchodzący weekend, ponieważ **ContosoVM** jest w trakcie planowanej konserwacji. 

Mimo że zespół może wyłączyć każdą regułę alertu, która jest skonfigurowana na **ContosoVM** ręcznie (i włączyć go ponownie po konserwacji), nie jest to prosty proces. Reguły akcji ułatwiają definiowanie tłumienia alertów na dużą skalę z możliwością elastycznego konfigurowania okresu wygaszanie. W poprzednim przykładzie zespół może zdefiniować jedną regułę akcji na **ContosoVM,** która pomija wszystkie powiadomienia alertów na weekend.


### <a name="actions-at-scale"></a>Działania na dużą skalę

Mimo że reguły alertów pomagają zdefiniować grupę akcji, która wyzwala po wygenerowaniu alertu, klienci często mają wspólną grupę akcji w całym zakresie operacji. Na przykład zespół odpowiedzialny za grupę zasobów **ContosoRG** prawdopodobnie zdefiniuje tę samą grupę akcji dla wszystkich reguł alertów zdefiniowanych w **programie ContosoRG**. 

Reguły akcji ułatwiają ten proces. Definiując akcje na dużą skalę, grupa akcji może być wyzwalana dla każdego alertu, który jest generowany w skonfigurowanym zakresie. W poprzednim przykładzie zespół może zdefiniować jedną regułę akcji na **ContosoRG,** która wyzwoli tę samą grupę akcji dla wszystkich alertów wygenerowanych w nim.

> [!NOTE]
> Reguły akcji obecnie nie mają zastosowania do alertów usługi Azure Service Health.

## <a name="configuring-an-action-rule"></a>Konfigurowanie reguły akcji

Dostęp do tej funkcji można uzyskać, wybierając **pozycję Zarządzaj akcjami** ze strony docelowej **Alerty** w usłudze Azure Monitor. Następnie wybierz pozycję **Reguły akcji (wersja zapoznawcza)**. Dostęp do reguł można uzyskać, wybierając **reguły akcji (podgląd)** z pulpitu nawigacyjnego strony docelowej dla alertów.

![Reguły akcji ze strony docelowej usługi Azure Monitor](media/alerts-action-rules/action-rules-landing-page.png)

Wybierz **+ Nowa reguła akcji**. 

![Dodaj nową regułę akcji](media/alerts-action-rules/action-rules-new-rule.png)

Alternatywnie można utworzyć regułę akcji podczas konfigurowania reguły alertu.

![Dodaj nową regułę akcji](media/alerts-action-rules/action-rules-alert-rule.png)

Teraz powinna zostać wyświetlona strona przepływu do tworzenia reguł akcji. Skonfiguruj następujące elementy: 

![Przepływ tworzenia nowych reguł akcji](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>Zakres

Najpierw wybierz zakres (subskrypcja platformy Azure, grupa zasobów lub zasób docelowy). Można również wybrać wiele kombinacji zakresów w ramach jednej subskrypcji.

![Zakres reguły akcji](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>Kryteria filtrowania

Można dodatkowo zdefiniować filtry, aby zawęzić je do określonego podzbioru alertów. 

Dostępne są następujące filtry: 

* **Ważność:** Opcja wyboru co najmniej jednej ważności alertu. **Ważność = Sev1** oznacza, że reguła akcji ma zastosowanie do wszystkich alertów ustawionych na Sev1.
* **Usługa monitorowania:** filtr oparty na źródłowej usłudze monitorowania. Ten filtr jest również wielokrotnym wybierany. Na przykład **usługa monitorowania = "Usługa aplikacji"** oznacza, że reguła akcji ma zastosowanie do wszystkich alertów opartych na usłudze Application Insights.
* **Typ zasobu:** Filtr oparty na określonym typie zasobu. Ten filtr jest również wielokrotnym wybierany. Na przykład **typ zasobu = "Maszyny wirtualne"** oznacza, że reguła akcji ma zastosowanie do wszystkich maszyn wirtualnych.
* **Identyfikator reguły alertu:** Opcja filtrowania dla określonych reguł alertów przy użyciu identyfikatora Menedżera zasobów reguły alertu.
* **Stan monitora:** Filtr dla wystąpień alertów z **funkcją Zwolniony** lub **Rozwiązany** jako warunek monitora.
* **Opis**: Wyrażenie regularne (wyrażenie regularne) jest zgodne z opisem, zdefiniowanym jako część reguły alertu. Na przykład **Opis zawiera "prod"** będzie pasować do wszystkich alertów, które zawierają ciąg "prod" w ich opisach.
* **Kontekst alertu (ładunek)**: Dopasowanie wyrażenia regularnego, które definiuje dopasowanie ciągu do pól kontekstu alertu ładunku alertu. Na przykład **kontekst alertu (ładunek) zawiera "Komputer-01"** będzie pasować do wszystkich alertów, których ładunki zawierają ciąg "Komputer-01".

Filtry te są stosowane w połączeniu ze sobą. Na przykład jeśli ustawisz **typ zasobu' = Maszyny wirtualne** i **ważność' = Sev0**, następnie zostały przefiltrowane dla wszystkich alertów **Sev0** tylko na maszynach wirtualnych. 

![Filtry reguł akcji](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>Tłumienie lub konfiguracja grupy akcji

Następnie skonfiguruj regułę akcji dla pomijania alertów lub obsługi grup akcji. Nie można wybrać obu. Konfiguracja działa na wszystkie wystąpienia alertów, które pasują do wcześniej zdefiniowanego zakresu i filtrów.

#### <a name="suppression"></a>Pomijanie

W przypadku **wybrania opcji wygaszania**należy skonfigurować czas trwania pomijania akcji i powiadomień. Wybierz jedną z następujących opcji:
* **Od teraz (Zawsze)**: Pomija wszystkie powiadomienia przez czas nieokreślony.
* **W zaplanowanym czasie:** pomija powiadomienia w ograniczonym czasie trwania.
* **Z cyklem:** pomija powiadomienia w cyklicznym harmonogramie dziennym, tygodniowym lub miesięcznym.

![Pomijanie reguł akcji](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>Grupa akcji

Jeśli w przełączniku wybierzesz **grupę Akcji,** dodaj istniejącą grupę akcji lub utwórz nową. 

> [!NOTE]
> Z regułą akcji można skojarzyć tylko jedną grupę akcji.

![Dodawanie lub tworzenie nowej reguły akcji przez wybranie grupy Akcji](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>Szczegóły reguły akcji

Na koniec skonfiguruj następujące szczegóły reguły akcji:
* Nazwa
* Grupa zasobów, w której jest zapisana
* Opis 

## <a name="example-scenarios"></a>Przykładowe scenariusze

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>Scenariusz 1: Tłumienie alertów na podstawie ważności

Contoso chce pominąć powiadomienia dla wszystkich alertów Sev4 na wszystkich maszynach wirtualnych w ramach subskrypcji **ContosoSub** w każdy weekend.

**Rozwiązanie:** Tworzenie reguły akcji za pomocą:
* Zakres = **ContosoSub**
* Filtry
    * Ważność = **Sev4**
    * Typ zasobu = **maszyny wirtualne**
* Tłumienie z cyklem ustawionym na co tydzień, a **sobota** i **niedziela** sprawdzone

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>Scenariusz 2: Pomijanie alertów na podstawie kontekstu alertu (ładunek)

Contoso chce pominąć powiadomienia dla wszystkich alertów dziennika generowanych dla **komputera-01** w **ContosoSub** przez czas nieokreślony, ponieważ przechodzi konserwację.

**Rozwiązanie:** Tworzenie reguły akcji za pomocą:
* Zakres = **ContosoSub**
* Filtry
    * Usługa monitorowania = **analiza dzienników**
    * Kontekst alertu (ładunek) zawiera **komputer-01**
* Tłumienie ustawione na **Od teraz (Zawsze)**

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>Scenariusz 3: Grupa akcji zdefiniowana w grupie zasobów

Contoso zdefiniował [alert metryki na poziomie subskrypcji](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor). Ale chce zdefiniować akcje, które wyzwalają specjalnie dla alertów generowanych z grupy zasobów **ContosoRG**.

**Rozwiązanie:** Tworzenie reguły akcji za pomocą:
* Zakres = **ContosoRG**
* Brak filtrów
* Grupa akcji ustawiona na **ContosoActionGroup**

> [!NOTE]
> *Grupy akcji zdefiniowane w regułach akcji i regułach alertów działają niezależnie, bez deduplikacji.* W scenariuszu opisanym wcześniej, jeśli grupa akcji jest zdefiniowana dla reguły alertu, wyzwala się w połączeniu z grupą akcji zdefiniowaną w regule akcji. 

## <a name="managing-your-action-rules"></a>Zarządzanie regułami działania

Reguły akcji można wyświetlać i zarządzać nimi z widoku listy:

![Widok listy reguł akcji](media/alerts-action-rules/action-rules-list-view.png)

W tym miejscu można włączyć, wyłączyć lub usunąć reguły akcji na dużą skalę, zaznaczając pole wyboru obok nich. Po wybraniu reguły akcji zostanie otwarta jej strona konfiguracji. Strona pomaga zaktualizować definicję reguły akcji i włączyć lub wyłączyć ją.

## <a name="best-practices"></a>Najlepsze rozwiązania

Alerty dziennika utworzone z [liczbą wyników](alerts-unified-log.md) opcja generowania pojedynczego wystąpienia alertu przy użyciu całego wyniku wyszukiwania (które mogą obejmować na wielu komputerach). W tym scenariuszu jeśli reguła akcji używa **filtru Kontekst alertu (ładunek),** działa na wystąpienie alertu, tak długo, jak istnieje dopasowanie. W scenariuszu 2, opisane wcześniej, jeśli wyniki wyszukiwania dla wygenerowanego alertu dziennika zawierają **komputer-01** i **komputer-02,** całe powiadomienie jest pomijane. Nie ma żadnych powiadomień generowanych dla **Computer-02** w ogóle.

![Reguły akcji i alerty dziennika (liczba wyników)](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

Aby najlepiej używać alertów dziennika z regułami akcji, należy utworzyć alerty dziennika z opcją [pomiaru metryki.](alerts-unified-log.md) Ta opcja generuje oddzielne wystąpienia alertów na podstawie zdefiniowanego pola grupy. Następnie w scenariuszu 2 są generowane oddzielne wystąpienia alertów dla **komputerów-01** i **komputer-02**. Ze względu na regułę akcji opisaną w scenariuszu tylko powiadomienie dla **komputera-01** jest pomijane. Powiadomienie dla **Computer-02** nadal strzelać jak zwykle.

![Reguły akcji i alerty dziennika (liczba wyników)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>Często zadawane pytania

### <a name="while-im-configuring-an-action-rule-id-like-to-see-all-the-possible-overlapping-action-rules-so-that-i-avoid-duplicate-notifications-is-it-possible-to-do-that"></a>Podczas konfigurowania reguły akcji, chciałbym zobaczyć wszystkie możliwe nakładające się reguły akcji, aby uniknąć zduplikowanych powiadomień. Czy jest to możliwe?

Po zdefiniowaniu zakresu podczas konfigurowania reguły akcji można wyświetlić listę reguł akcji, które nakładają się na ten sam zakres (jeśli istnieje). To nakładanie może być jedną z następujących opcji:

* Dopasowanie ścisłe: na przykład definiowana reguła akcji i nakładająca się reguła akcji znajdują się w tej samej subskrypcji.
* Podzbiór: Na przykład reguła akcji, którą definiujesz, znajduje się w ramach subskrypcji, a nakładająca się reguła akcji znajduje się w grupie zasobów w ramach subskrypcji.
* Nadzbiór: Na przykład reguła akcji, którą definiujesz, znajduje się w grupie zasobów, a nakładająca się reguła akcji znajduje się w subskrypcji zawierającej grupę zasobów.
* Przecięcie: Na przykład definiowana reguła akcji znajduje się na **maszynach VM1** i **VM2,** a nakładająca się reguła akcji dotyczy **vm2** i **VM3**.

![Nakładające się reguły akcji](media/alerts-action-rules/action-rules-overlapping.png)

### <a name="while-im-configuring-an-alert-rule-is-it-possible-to-know-if-there-are-already-action-rules-defined-that-might-act-on-the-alert-rule-im-defining"></a>Podczas konfigurowania reguły alertów, czy można wiedzieć, czy istnieją już zdefiniowane reguły akcji, które mogą działać zgodnie z definiowaną przeze mnie regułą alertu?

Po zdefiniowaniu zasobu docelowego dla reguły alertu można wyświetlić listę reguł akcji, które działają w tym samym zakresie (jeśli istnieje), wybierając **pozycję Wyświetl skonfigurowane akcje** w sekcji **Akcje.** Ta lista jest wypełniana na podstawie następujących scenariuszy dla zakresu:

* Dokładne dopasowanie: na przykład definiowana reguła alertu i reguła akcji są w tej samej subskrypcji.
* Podzbiór: Na przykład reguła alertu, którą definiujesz, znajduje się w ramach subskrypcji, a reguła akcji znajduje się w grupie zasobów w ramach subskrypcji.
* Nadzbiór: Na przykład reguła alertu, którą definiujesz, znajduje się w grupie zasobów, a reguła akcji znajduje się w subskrypcji zawierającej grupę zasobów.
* Przecięcie: Na przykład definiowana reguła alertu znajduje się na **maszynach VM1** i **VM2,** a reguła akcji jest na **maszynach VM2** i **VM3**.
    
![Nakładające się reguły akcji](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

### <a name="can-i-see-the-alerts-that-have-been-suppressed-by-an-action-rule"></a>Czy mogę zobaczyć alerty, które zostały pominięte przez regułę akcji?

Na [stronie listy alertów](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-managing-alert-instances)można wybrać dodatkową kolumnę o nazwie **Stan tłumienia**. Jeśli powiadomienie o wystąpieniu alertu zostało pominięte, wyświetliłoby ten stan na liście.

![Pominięte wystąpienia alertów](media/alerts-action-rules/action-rules-suppressed-alerts.png)

### <a name="if-theres-an-action-rule-with-an-action-group-and-another-with-suppression-active-on-the-same-scope-what-happens"></a>Jeśli istnieje reguła akcji z grupą akcji i inną z aktywnym tłumieniem w tym samym zakresie, co się stanie?

Pominiaszanie zawsze ma pierwszeństwo w tym samym zakresie.

### <a name="what-happens-if-i-have-a-resource-thats-monitored-in-two-separate-action-rules-do-i-get-one-or-two-notifications-for-example-vm2-in-the-following-scenario"></a>Co się stanie, jeśli mam zasób, który jest monitorowany w dwóch oddzielnych regułach akcji? Czy otrzymuję jedno lub dwa powiadomienia? Na przykład **VM2** w następującym scenariuszu:

      action rule AR1 defined for VM1 and VM2 with action group AG1
      action rule AR2 defined for VM2 and VM3 with action group AG1

Dla każdego alertu na VM1 i VM3 grupa akcji AG1 zostanie wyzwolona raz. Dla każdego alertu na **VM2**grupa akcji AG1 zostanie wyzwolona dwukrotnie, ponieważ reguły akcji nie deduplikują akcji. 

### <a name="what-happens-if-i-have-a-resource-monitored-in-two-separate-action-rules-and-one-calls-for-action-while-another-for-suppression-for-example-vm2-in-the-following-scenario"></a>Co się stanie, jeśli zasób jest monitorowany w dwóch oddzielnych regułach akcji i jeden wymaga działania, podczas gdy inny do tłumienia? Na przykład **VM2** w następującym scenariuszu:

      action rule AR1 defined for VM1 and VM2 with action group AG1 
      action rule AR2 defined for VM2 and VM3 with suppression

Dla każdego alertu na VM1 grupa akcji AG1 zostanie wyzwolona raz. Akcje i powiadomienia dla każdego alertu na VM2 i VM3 zostaną pominięte. 

### <a name="what-happens-if-i-have-an-alert-rule-and-an-action-rule-defined-for-the-same-resource-calling-different-action-groups-for-example-vm1-in-the-following-scenario"></a>Co się stanie, jeśli mam regułę alertu i regułę akcji zdefiniowaną dla tego samego zasobu wywołującego różne grupy akcji? Na przykład **VM1** w następującym scenariuszu:

      alert rule rule1 on VM1 with action group AG2
      action rule AR1 defined for VM1 with action group AG1 
 
Dla każdego alertu na VM1 grupa akcji AG1 zostanie wyzwolona raz. Za każdym razem, gdy reguła alertu "rule1" jest wyzwalana, spowoduje to również dodatkowe uruchomienie ag2. Grupy akcji zdefiniowane w regułach akcji i regułach alertów działają niezależnie, bez deduplikacji. 

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej o alertach na platformie Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)
