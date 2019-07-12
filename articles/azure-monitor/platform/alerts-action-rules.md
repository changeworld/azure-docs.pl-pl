---
title: Akcja reguły alertów usługi Azure Monitor
description: Zrozumienie, czym są zasady działania w usłudze Azure Monitor i sposób konfigurowania i zarządzania nimi.
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: df069ee398ea2937f03765b10576061b5e541390
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2019
ms.locfileid: "67656717"
---
# <a name="action-rules-preview"></a>Akcja reguły (wersja zapoznawcza)

Akcja reguły pomagają zdefiniować lub Pomiń akcje w dowolnym zakresie usługi Azure Resource Manager (subskrypcja platformy Azure, grupę zasobów lub zasobu docelowego). Mają one różne filtry, które działają na pomoc w zawężeniu konkretnego podzestawu wystąpień alertów.

## <a name="why-and-when-should-you-use-action-rules"></a>Kiedy i dlaczego należy użyć akcji reguły?

### <a name="suppression-of-alerts"></a>Pomijanie alertów

Istnieje wiele scenariuszy, gdzie jest to użyteczne wyłączyć powiadomienia, które generują alerty. Te scenariusze w zakresie od pomijania podczas okna zaplanowanej konserwacji na pomijanie poza godzinami pracy. Na przykład zespół odpowiedzialny za **ContosoVM** chce, aby pominąć powiadomień o alertach for weekend nadchodzących ponieważ **ContosoVM** jest poddawana planowanej konserwacji. 

Mimo że zespół może wyłączyć każdej reguły alertu, który jest skonfigurowany na **ContosoVM** ręcznie (i włączyć je ponownie po konserwacji) nie jest prostym procesem. Akcja reguły pomagającym w zdefiniowaniu pomijanie alertów na dużą skalę z możliwością elastycznie przedział pomijania. W poprzednim przykładzie, zespół może zdefiniować jedną regułę akcji na **ContosoVM** wszystkich powiadomień o alertach, pomija for weekend.


### <a name="actions-at-scale"></a>Akcje na dużą skalę

Mimo że reguł alertów pomagającym w zdefiniowaniu grupy akcji, która uruchamia, gdy alert jest generowany, klienci często mają wspólne grupy akcji w ich zakresie operacji. Na przykład zespół odpowiedzialny za grupy zasobów **ContosoRG** prawdopodobnie będą definiować tej samej grupy akcji dla wszystkich reguł alertów zdefiniowanych w **ContosoRG**. 

Akcja reguły ułatwiają upraszcza ten proces. Definiując akcje na dużą skalę, grupy akcji mogą być wywoływane dla dowolnego alertu, który jest generowany na skonfigurowanym zakresem. W poprzednim przykładzie, zespół może zdefiniować jedną regułę akcji na **ContosoRG** który spowoduje wyzwolenie tej samej grupy akcji dla wszystkich alertów generowanych w nim.

> [!NOTE]
> Akcja reguły aktualnie nie dotyczą alerty dotyczące kondycji usługi platformy Azure.

## <a name="configuring-an-action-rule"></a>Konfigurowanie reguły akcji

Można uzyskać dostępu do funkcji, wybierając **zarządzać akcjami** z **alerty** strony docelowej w usłudze Azure Monitor. Następnie wybierz **akcji reguł (wersja zapoznawcza)** . Dostęp do zasady, wybierając **akcji reguł (wersja zapoznawcza)** z poziomu pulpitu nawigacyjnego stroną docelową dla alertów.

![Akcja reguły na stronie docelowej usługi Azure Monitor](media/alerts-action-rules/action-rules-landing-page.png)

Wybierz **+ Nowa reguła akcji**. 

![Dodaj nową regułę akcji](media/alerts-action-rules/action-rules-new-rule.png)

Alternatywnie można utworzyć regułę akcji, podczas konfigurowania reguły alertu.

![Dodaj nową regułę akcji](media/alerts-action-rules/action-rules-alert-rule.png)

Powinna zostać teraz wyświetlona strona przepływu, do tworzenia reguł akcji. Skonfiguruj następujące elementy: 

![Nowy przepływ tworzenia reguły akcji](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>Scope

Najpierw wybierz zakresu (subskrypcji platformy Azure, grupę zasobów lub zasobu docelowego). Użytkownik może również wielu zaznaczania kombinacji zakresów w ramach jednej subskrypcji.

![Zakres reguły akcji](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>Kryteria filtrowania

Ponadto można zdefiniować filtry, aby zawęzić je do określony podzbiór alertów. 

Dostępne są następujące filtry: 

* **Ważność**: Możliwość wybrania jednego lub więcej ważności alertu. **Ważność = Sev1** oznacza, że zasada działania mające zastosowanie dla wszystkich alertów ustawienie Sev1.
* **Monitorowanie usługi**: Filtr oparty na źródłowy usługi monitorowania. Ten filtr jest również wiele wybierz. Na przykład **usługi Monitor = "Application Insights"** oznacza, że reguły akcji jest stosowana dla wszystkich alertów opartych na usłudze Application Insights.
* **Typ zasobu**:  Filtr oparty na zasobów określonego typu. Ten filtr jest również wiele wybierz. Na przykład **typ zasobu = "Maszyny wirtualne"** oznacza, że reguły akcji jest stosowana dla wszystkich maszyn wirtualnych.
* **Identyfikator reguły alertu**: Możliwość filtrowania dla określonych reguł alertów za pomocą usługi Resource Manager identyfikator reguły alertu.
* **Monitorowanie stanu**:  Filtr dla wystąpień alertów z oboma **Fired** lub **rozwiązane** jako warunek monitora.
* **Opis**: Dopasowanie wyrażenia regularnego (wyrażenie regularne), definiujący zgodność ciąg opisu, zdefiniowane jako część reguły alertu. Na przykład **opis zawiera "prod"** pokaże wszystkie alerty, które zawierają ciąg "prod" w ich opisy.
* **Alert kontekstu (ładunku)** : Dopasowanie wyrażenia regularnego, który definiuje ciąg dopasowywania pola kontekst alertu alert ładunku. Na przykład **Alert kontekst (ładunku) zawiera "Komputer-01"** pokaże wszystkie alerty, których ładunki zawiera ciąg "Komputer-01".

Te filtry są stosowane w połączeniu ze sobą. Na przykład jeśli ustawisz **typ zasobu "= maszyn wirtualnych** i **ważność" = Sev0**, a następnie przefiltrowaniu dla wszystkich **Sev0** alertów dotyczących tylko maszyny wirtualne. 

![Akcja reguły filtrów](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>Pomijanie lub akcję konfiguracji grupy

Następnie skonfiguruj reguły akcji dla pomijania alertów lub obsługa grupy akcji. Nie można wybrać obu. Konfiguracja działa we wszystkich wystąpieniach alertu, zgodne z wcześniej zdefiniowanego zakresu i filtry.

#### <a name="suppression"></a>Pomijanie

Jeśli wybierzesz **pomijanie**, skonfiguruj czas trwania pomijanie działania i powiadomienia. Wybierz jedną z następujących opcji:
* **Od teraz (zawsze)** : Pomija wszystkie powiadomienia na czas nieokreślony.
* **W zaplanowanym czasie**: Pomija powiadomienia w ramach ograniczonego czasu trwania.
* **Z cyklem**: Pomija powiadomień zgodnie z cyklicznym harmonogramem codziennie, co tydzień lub co miesiąc.

![Akcja reguły pominięć](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>grupy akcji

Jeśli wybierzesz **grupy akcji** w przełącznika, Dodaj istniejące grupy akcji lub Utwórz nową. 

> [!NOTE]
> Tylko jednej grupy akcji można skojarzyć z reguły akcji.

![Dodaj lub Utwórz nową regułę akcji, wybierając grupę akcji](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>Szczegóły reguły akcji

Ostatnie skonfiguruj następujące informacje dla reguły akcji:
* Name (Nazwa)
* Grupa zasobów, w którym jest zapisany
* Opis 

## <a name="example-scenarios"></a>Przykładowe scenariusze

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>Scenariusz 1: Pomijanie alertów na podstawie ich wagi

Firma Contoso chce Pomiń powiadomienia dla wszystkich alertów Sev4 na wszystkich maszynach wirtualnych w ramach subskrypcji **ContosoSub** co weekend.

**Rozwiązanie:** Tworzenie reguły akcji przy użyciu:
* Zakres = **ContosoSub**
* Filtry
    * Ważność = **Sev4**
    * Typ zasobu = **maszyn wirtualnych**
* Pomijanie z cyklu równa co tydzień, a **sobota** i **niedziela** zaznaczone

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>Scenariusz 2: Pomijanie alertów na podstawie kontekstu alertu (ładunku)

Firma Contoso chce Pomiń powiadomienia, aby rejestrować wszystkie alerty generowane ze względu na **komputera-01-** w **ContosoSub** przez czas nieokreślony postaci, w jakiej jest uaktualniany konserwacji.

**Rozwiązanie:** Tworzenie reguły akcji przy użyciu:
* Zakres = **ContosoSub**
* Filtry
    * Monitorowanie usługi = **Log Analytics**
    * Zgłoś alert, kontekst (ładunku) zawiera **komputera-01**
* Pomijanie równa **od teraz (zawsze)**

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>Scenariusz 3: Grupa akcji zdefiniowane w grupie zasobów

Firma Contoso ma zdefiniowane [alert dotyczący metryki na poziomie subskrypcji](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor). Ale chce definiują akcje, które mogą powodować specjalnie dla alertów generowanych w grupie zasobów **ContosoRG**.

**Rozwiązanie:** Tworzenie reguły akcji przy użyciu:
* Zakres = **ContosoRG**
* Brak filtrów
* Ustaw grupę akcji **ContosoActionGroup**

> [!NOTE]
> *Grupy akcji zdefiniowanych w regułach akcji i reguł alertów działać niezależnie, i nie deduplikacji.* W tym scenariuszu opisano wcześniej, jeśli grupy akcji jest zdefiniowany dla reguły alertu, wyzwala w połączeniu z grupą akcji zdefiniowane w regule akcji. 

## <a name="managing-your-action-rules"></a>Zarządzanie regułami akcji

Można wyświetlać i zarządzać regułami akcji z widoku listy:

![Widok listy w regułach akcji](media/alerts-action-rules/action-rules-list-view.png)

W tym miejscu można włączyć, wyłączyć lub usunąć regułami akcji na dużą skalę, wybierając pole wyboru obok nich. Po wybraniu reguły akcji, zostanie otwarta strona jego konfiguracji. Strona pomaga zaktualizować definicję reguły akcji i włączać lub wyłączać.

## <a name="best-practices"></a>Najlepsze praktyki

Alerty, które utworzyłeś z dzienników [liczba wyników](alerts-unified-log.md) opcję generowania pojedyncze wystąpienie alertu przy użyciu wynik wyszukiwania całego, (która mogą rozciągać się na wielu komputerach). W tym scenariuszu, jeśli korzysta z reguły akcji **kontekst alertu (ładunku)** filtru, działa w wystąpieniu alertu tak długo, jak są zgodne. W scenariuszu 2 opisanej powyżej, jeśli wyniki wyszukiwania dla alertu wygenerowanego dziennika zawiera zarówno **komputera-01-** i **komputera-02**, cały powiadomień jest pomijane. Nie jest prezentowane żadne powiadomienie, generowany dla **komputera-02** wcale.

![Akcja reguły i alerty dziennika (liczba wyników)](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

Aby najlepiej użyć alertów dziennika z regułami akcji, należy utworzyć alerty dzienników przy użyciu [pomiar metryki](alerts-unified-log.md) opcji. Oddzielne wystąpienia alertu są generowane przez tę opcję na podstawie jego pola zdefiniowanej grupy. Następnie w scenariuszu 2 osobnych wystąpień alertu są generowane dla **komputera-01-** i **komputera-02**. Ze względu na zasady działania opisane w tym scenariuszu tylko powiadomienia dla **komputera-01** jest pomijane. Powiadomienie dotyczące **komputera-02** nadal uruchomiony w zwykły sposób.

![Akcja reguły i alerty dziennika (liczba wyników)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>Często zadawane pytania

### <a name="while-im-configuring-an-action-rule-id-like-to-see-all-the-possible-overlapping-action-rules-so-that-i-avoid-duplicate-notifications-is-it-possible-to-do-that"></a>Gdy konfiguruję reguły akcji I chcesz zobaczyć wszystkich możliwych nakładających się regułami akcji tak, aby uniknąć zduplikowanych powiadomień. Czy jest możliwe to zrobić?

Po zdefiniowaniu zakresu, jak skonfigurować regułę akcji, można wyświetlić listę reguł akcji, które nakładają się na tym samym zakresie (jeśli istnieje). Ta nakładania się może być jedną z następujących opcji:

* Dokładne dopasowanie: Na przykład reguły akcji, które definiujesz i nakładających się reguły akcji znajdują się na tej samej subskrypcji.
* Podzbiór: Na przykład reguły akcji, które definiujesz znajduje się w subskrypcji i nakładających się reguły akcji znajduje się na grupę zasobów w ramach subskrypcji.
* Nadzbiór: Na przykład reguły akcji, które definiujesz znajduje się w grupie zasobów i nakładających się reguły akcji znajduje się w subskrypcji, która zawiera grupy zasobów.
* Część wspólna: Na przykład reguły akcji, które definiujesz znajduje się na **VM1** i **VM2**, a nakładające się reguły akcji znajduje się na **VM2** i **VM3**.

![Nakładające się regułami akcji](media/alerts-action-rules/action-rules-overlapping.png)

### <a name="while-im-configuring-an-alert-rule-is-it-possible-to-know-if-there-are-already-action-rules-defined-that-might-act-on-the-alert-rule-im-defining"></a>Gdy konfiguruję reguły alertu, jest on możliwość sprawdzenia, czy istnieją już zdefiniowane regułami akcji, który może działać na reguły alertu, które mnie I Definiowanie?

Po zdefiniowaniu zasób docelowy reguły alertu można wyświetlić listę regułami akcji, które działają w tym samym zakresie (jeśli istnieje), wybierając **widoku skonfigurowana akcje** w obszarze **akcje** sekcji. Ta lista jest wypełniana w oparciu o następujące scenariusze dla zakresu:

* Dokładne dopasowanie: Na przykład reguły alertu, który definiujesz i reguły akcji znajdują się na tej samej subskrypcji.
* Podzbiór: Na przykład reguły alertu, który definiujesz znajduje się w subskrypcji i reguły akcji znajduje się na grupę zasobów w ramach subskrypcji.
* Nadzbiór: Na przykład reguły alertu, który definiujesz znajduje się w grupie zasobów i reguły akcji znajduje się w subskrypcji, która zawiera grupy zasobów.
* Część wspólna: Na przykład reguły alertu, które definiujesz znajduje się na **VM1** i **VM2**, a reguły akcji znajduje się na **VM2** i **VM3**.
    
![Nakładające się regułami akcji](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

### <a name="can-i-see-the-alerts-that-have-been-suppressed-by-an-action-rule"></a>Może wyświetlać alerty, które zostały pominięte w regule akcji?

W [strona listy alertów](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-managing-alert-instances), możesz wybrać dodatkową kolumnę o nazwie **stan pomijanie**. Jeśli powiadomienia w przypadku wystąpienia alertu zostało pominięte, spowoduje to pokazanie stan na liście.

![Pominiętych wystąpień alertów](media/alerts-action-rules/action-rules-suppressed-alerts.png)

### <a name="if-theres-an-action-rule-with-an-action-group-and-another-with-suppression-active-on-the-same-scope-what-happens"></a>W przypadku reguły akcji z grupą akcji, a drugi z pomijania aktywne na tym samym zakresie, co się dzieje?

Pomijanie zawsze ma pierwszeństwo przed, w tym samym zakresie.

### <a name="what-happens-if-i-have-a-resource-thats-monitored-in-two-separate-action-rules-do-i-get-one-or-two-notifications-for-example-vm2-in-the-following-scenario"></a>Co się stanie, jeśli zasób, który jest monitorowany w dwie reguły osobną akcję? Uzyskać co najmniej dwa powiadomienia? Na przykład **VM2** w następującym scenariuszu:

      "action rule AR1 defined for VM1 and VM2 with action group AG1
      action rule AR2 defined for VM2 and VM3 with action group AG1"

Dla każdego alertu na maszyny VM1 i VM3 grupy akcji AG1 będzie wyzwalane jeden raz. Dla każdego alertu na **VM2**, grupy akcji AG1 będzie wyzwalane dwa razy, ponieważ akcja reguły nie należy przeprowadzać deduplikacji akcji. 

### <a name="what-happens-if-i-have-a-resource-monitored-in-two-separate-action-rules-and-one-calls-for-action-while-another-for-suppression-for-example-vm2-in-the-following-scenario"></a>Co się stanie, jeśli mam zasobem kontrolowany w dwóch reguł osobną akcję i wymaga jednego działania, natomiast drugi dla pomijania? Na przykład **VM2** w następującym scenariuszu:

      "action rule AR1 defined for VM1 and VM2 with action group AG1 
      action rule AR2 defined for VM2 and VM3 with suppression"

Dla każdego alertu VM1 grupy akcji AG1 będzie wyzwalane jeden raz. Akcje i powiadomienia dotyczące alertów, co dla maszyny VM2 i VM3 zostaną pominięte. 

### <a name="what-happens-if-i-have-an-alert-rule-and-an-action-rule-defined-for-the-same-resource-calling-different-action-groups-for-example-vm1-in-the-following-scenario"></a>Co się stanie, jeśli mam regułę alertu i reguły zdefiniowane dla tego samego zasobu, grupy różnych akcji podczas wywoływania akcji? Na przykład **VM1** w następującym scenariuszu:

      "alert rule rule1 on VM1 with action group AG2
      action rule AR1 defined for VM1 with action group AG1" 
 
Dla każdego alertu VM1 grupy akcji AG1 będzie wyzwalane jeden raz. Przy każdym wyzwoleniu reguły alertu "rule1" również wyzwoli on AG2 dodatkowo. Grupy akcji zdefiniowanych w regułach akcji i reguł alertów działać niezależnie, i nie deduplikacji. 

## <a name="next-steps"></a>Kolejne kroki

- [Dowiedz się więcej na temat alertów na platformie Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)
