---
title: Akcja reguły alertów usługi Azure Monitor
description: Zrozumienie, czym są regułami akcji i sposób konfigurowania i zarządzania nimi.
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: c260273c647dd5dd6050f1fd543ebd5a5aa47b89
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64922766"
---
# <a name="action-rules-preview"></a>Akcja reguły (wersja zapoznawcza)

W tym artykule opisano, co to są zasady działania i sposób konfigurowania i zarządzania nimi.

## <a name="what-are-action-rules"></a>Co to są zasady działania?

Akcja reguły umożliwiają definiowanie akcji (lub pomijanie akcji) w dowolnym zakresie Menedżera zasobów (subskrypcja, grupa zasobów, lub zasobu). Mają one różne filtry, które umożliwiają zawężenie konkretnego podzestawu wystąpień alertów, które działają na. 

Za pomocą akcji reguł można wykonywać następujące czynności:

* Pomiń akcje i powiadomień w przypadku planowanej konserwacji systemu windows lub for weekend/dni wolnych od pracy, zamiast konieczności całkowitego wyłączania każda reguła alertu o indywidualnie.
* Zdefiniuj akcje i powiadomień w dużej skali: Nie trzeba zdefiniować grupy akcji osobno dla każdej reguły alertu, można zdefiniować grupę akcji do wyzwalacza dla alertów generowanych w dowolnym zakresie. Na przykład czy można mieć wyzwalacz "ContosoActionGroup" grupy akcji dla każdego alertu generowany w ramach mojej subskrypcji.

## <a name="configuring-an-action-rule"></a>Konfigurowanie reguły akcji

Można uzyskać dostępu do funkcji, wybierając **zarządzać akcjami** z alertów, strona docelowa w usłudze Azure Monitor. Następnie wybierz pozycję **akcji reguł (wersja zapoznawcza)**. Mogli je, wybierając **akcji reguł (wersja zapoznawcza)** z poziomu pulpitu nawigacyjnego stroną docelową dla alertów.

![Akcja reguły na stronie docelowej usługi Azure Monitor](media/alerts-action-rules/action-rules-landing-page.png)

Wybierz **+ Nowa reguła akcji**. 

![Dodaj nową regułę akcji](media/alerts-action-rules/action-rules-new-rule.png)

Alternatywnie również można utworzyć reguły akcji podczas konfigurowania reguły alertu.

![Dodaj nową regułę akcji](media/alerts-action-rules/action-rules-alert-rule.png)

Powinien zostać wyświetlony przepływ tworzenia reguły działania Otwórz. Skonfiguruj następujące elementy: 

![Nowy przepływ tworzenia reguły akcji](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>Zakres

Należy najpierw wybrać zakres, czyli zasób docelowy, grupy zasobów lub subskrypcji. Istnieje również możliwość wielokrotnego wyboru kombinacji dowolne z powyższych (w ramach jednej subskrypcji). 

![Zakres reguły akcji](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>Kryteria filtrowania

Ponadto można zdefiniować filtry, aby zawęzić do określony podzbiór alerty dotyczące zdefiniowanego zakresu. 

Dostępne są następujące filtry: 

* **Ważność**: Wybierz co najmniej jeden poziomy ważności alertu. Ważność = Sev1 oznacza, że reguły akcji jest stosowana dla wszystkich alertów o ważności jako Sev1.
* **Monitorowanie usługi**: Filtr oparty na źródłowy usługi monitorowania. Dotyczy to również wielokrotnego wyboru. Na przykład usługi Monitor = "Application Insights" oznacza, że reguły akcji ma zastosowanie do wszystkich "Application Insights" na podstawie alertów.
* **Typ zasobu**: Filtr oparty na zasobów określonego typu. Dotyczy to również wielokrotnego wyboru. Na przykład typ zasobu = "Maszyny wirtualne" oznacza reguły akcji i dotyczy wszystkich maszyn wirtualnych.
* **Identyfikator reguły alertu**: Umożliwia filtrowanie dla określonych reguł alertów przy użyciu usługi Resource Manager Identyfikatora reguły alertu.
* **Monitorowanie stanu**: Filtr dla wystąpień alertów za pomocą "Fired" lub "Rozwiązane" jako warunek monitora.
* **Opis**: Wyrażenie regularne dopasowywania w opisie zdefiniowane jako część reguły alertu.
* **Kontekst alertu (ładunku)**: Wyrażenie regularne dopasowywania w [kontekst alertu](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-definitions#alert-context-fields) pola wystąpienia alertu.

Te filtry są stosowane w połączeniu ze sobą. Na przykład, jeśli ustawić "Resource type" = "Maszyny wirtualne" i "Waga" = "Sev0", to czy mogę mieć filtrowanie dla wszystkich alertów "Sev0" tylko maszyny wirtualne. 

![Akcja reguły filtrów](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>Pomijanie lub akcję konfiguracji grupy

Skonfiguruj reguły akcji dla pomijania alertów lub obsługa grupy akcji. Nie można wybrać obu. Konfiguracja działa we wszystkich wystąpieniach alertów pasujących do wcześniej zdefiniowanego zakresu i filtry.

#### <a name="suppression"></a>Pomijanie

Jeśli wybierzesz **pomijanie**, skonfiguruj czas trwania pomijanie działania i powiadomienia. Wybierz jedną z następujących czynności:
* **Od teraz (zawsze)**: Pomija wszystkie powiadomienia na czas nieokreślony.
* **W zaplanowanym czasie**: Pomiń powiadomienia w ramach ograniczonego czasu trwania.
* **Z cyklem**: Pomiń na harmonogramie cyklu, który może być codziennie, co tydzień lub co miesiąc.

![Akcja reguły pominięć](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>Grupa akcji

Jeśli wybierzesz **grupy akcji** w przełącznika, Dodaj istniejące grupy akcji lub Utwórz nową. 

> [!NOTE]
> Tylko jednej grupy akcji można skojarzyć z reguły akcji.

![Grupy akcji reguły akcji](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>Szczegóły reguły akcji

Na koniec skonfiguruj następujące informacje dla reguły akcji
* Name (Nazwa)
* Grupa zasobów, w którym zostanie zapisany
* Opis 

## <a name="example-scenarios"></a>Przykładowe scenariusze

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>Scenariusz 1: Pomijanie alertów na podstawie ich wagi

Firma Contoso chce Pomiń powiadomienia dla wszystkich alertów Sev4 na wszystkich maszynach wirtualnych w ramach ich subskrypcji "ContosoSub" co weekend.

**Rozwiązanie:** Tworzenie reguły za pomocą akcji
* Zakres = "ContosoSub"
* Filtry
    * Severity = 'Sev4'
    * Typ zasobu = "Maszyny wirtualnej"
* Pomijanie z cyklu zestawu co tydzień, a następnie sprawdzić "Sobota" i "Niedziela"

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>Scenariusz 2: Pomijanie alertów na podstawie kontekstu alertu (ładunku)

Firma Contoso chce Pomiń powiadomienia, aby rejestrować wszystkie alerty generowane ze względu na "Komputer-01" w "ContosoSub" na czas nieokreślony, ponieważ jest uaktualniany konserwacji.

**Rozwiązanie:** Tworzenie reguły za pomocą akcji
* Zakres = "ContosoSub"
* Filtry
    * Monitorowanie usługi = "Log Analytics"
    * Kontekst alertu (ładunku) zawiera "Komputer-01"
* Pomijanie ustawiona na "od teraz (zawsze)"

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>Scenariusz 3: Grupa akcji zdefiniowane w grupie zasobów

Firmy Contoso jest zdefiniowany [alert dotyczący metryki na poziomie subskrypcji](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor), ale chce zdefiniowania akcji, które wyzwalają alerty oddzielnie dla swojej grupy zasobów "ContosoRG".

**Rozwiązanie:** Tworzenie reguły za pomocą akcji
* Zakres = "ContosoRG"
* Brak filtrów
* Grupy akcji ustawiony na wartość "ContosoActionGroup"

## <a name="managing-your-action-rules"></a>Zarządzanie regułami akcji

Można wyświetlać i zarządzać regułami akcji z widoku listy, jak pokazano poniżej.

![Widok listy w regułach akcji](media/alerts-action-rules/action-rules-list-view.png)

W tym miejscu możesz regułami akcji Włącz/Wyłącz/usuwania na dużą skalę, wybierając pole wyboru obok nich. Kliknięcie dowolnej reguły akcji otwiera jego strony konfiguracji, dzięki czemu możesz aktualizować definicję i Włącz/Wyłącz ją.

## <a name="best-practices"></a>Najlepsze praktyki

Alerty utworzone za pomocą dzienników ["Liczba wyników"](https://docs.microsoft.com/azure-monitor/platform/alerts-unified-log) opcja Wygeneruj **pojedyncze wystąpienie alertu** przy użyciu wynik wyszukiwania całego, (która może być na przykład na wielu komputerach). W tym scenariuszu jeśli reguły akcji używa filtru "Kontekst alertu (ładunku)", będzie ona działać w wystąpieniu alertu tak długo, jak są zgodne. W scenariuszu 2 zgodnie z opisem w poprzedniej sekcji, jeśli wyniki wyszukiwania dla alertu dziennika wygenerowany zawierają "Komputer-01" i "Komputer-02" cały powiadomień jest pominięte (to znaczy, jest bez powiadomienia generowane dla "Komputer-02" na wszystkich).

![Akcja reguły i alerty dziennika (liczba wyników)](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

Do alertów dziennika najlepiej korzystać z regułami akcji, zaleca się tworzenie alertów dziennika z ["pomiar metryki"](https://docs.microsoft.com/azure-monitor/platform/alerts-unified-log) opcji. Korzystając z tej opcji osobnych wystąpień alertu są generowane, na podstawie pola grupy zdefiniowane. Następnie w scenariuszu 2 osobnych wystąpień alertu są generowane dla "Komputer-01" i "Komputer-02". Z regułą działania opisane w scenariuszu tylko powiadomienia "Komputer-01" będą pomijane podczas powiadomienie dotyczące "Komputer-02" będzie w dalszym ciągu pożaru w zwykły sposób.

![Akcja reguły i alerty dziennika (liczba wyników)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>Często zadawane pytania

* PYTANIE: Podczas konfigurowania reguły akcji, I chcesz zobaczyć, że wszystkie możliwe nakładających się akcja reguły, aby uniknąć zduplikowanych powiadomień. Czy jest możliwe to zrobić?

    A. Po zdefiniowaniu zakres podczas konfigurowania reguły akcji można wyświetlić listę zasad działania, które nakładają się na tym samym zakresie, (jeśli istnieje). Ta nakładania się może być jedną z następujących opcji:
    * Dokładne dopasowanie: Na przykład reguły akcji, które są definiowane i nakładających się reguły akcji znajdują się na tej samej subskrypcji.
    * Podzbiór: Na przykład reguły akcji, które definiujesz znajduje się w subskrypcji i nakładających się reguły akcji znajduje się na grupę zasobów w ramach subskrypcji.
    * Nadzbiór: Na przykład reguły akcji, które definiujesz znajduje się w grupie zasobów i nakładających się reguły akcji znajduje się w subskrypcji, która zawiera grupy zasobów.
    * Część wspólna: Na przykład reguły akcji, które definiujesz znajduje się na "VM1" i "VM2" i nakładających się reguły akcji znajduje się na "VM2" i "VM3".

    ![Nakładające się regułami akcji](media/alerts-action-rules/action-rules-overlapping.png)

* PYTANIE: Podczas konfigurowania reguły alertu, to jest to możliwe, aby dowiedzieć się, jeśli istnieją już zdefiniowane regułami akcji, który może być zajmującym się reguły alertu, który am I Definiowanie?

    A. Po zdefiniowaniu zasób docelowy reguły alertu wyświetlana lista akcji reguł, które działają na tym samym zakresie (jeśli istnieje), klikając polecenie "Wyświetl skonfigurowana akcje" w sekcji "Działania". Ta lista jest wypełniana w oparciu o następujące scenariusze dla zakresu:
    * Dokładne dopasowanie: Na przykład reguły alertu, który jest definiowany i reguły akcji znajdują się na tej samej subskrypcji.
    * Podzbiór: Na przykład reguły alertu, który jest definiowany znajduje się w subskrypcji i reguły akcji znajduje się na grupę zasobów w ramach subskrypcji.
    * Nadzbiór: Na przykład reguły alertu, który jest definiowany jest w grupie zasobów i reguły akcji znajduje się w subskrypcji, która zawiera grupy zasobów.
    * Część wspólna: Na przykład reguły alertu, który jest definiowany znajduje się na "VM1" i "VM2", a reguły akcji znajduje się na "VM2" i "VM3".
    
    ![Nakładające się regułami akcji](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

* PYTANIE: Może wyświetlać alerty, które zostały pominięte w regule akcji?

    A. W [strona listy alertów](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-managing-alert-instances), ma dodatkową kolumnę, które można wybrać o nazwie "Pomijanie Status". Jeśli powiadomienia w przypadku wystąpienia alertu zostało pominięte, spowoduje to pokazanie stan na liście.

    ![Pominiętych wystąpień alertów](media/alerts-action-rules/action-rules-suppressed-alerts.png)

* PYTANIE: W przypadku reguły akcji z grupą akcji, a drugi z pomijania aktywne na tym samym zakresie, co się dzieje?

    A. **Pomijanie zawsze ma pierwszeństwo przed, w tym samym zakresie**.

* PYTANIE: Co się stanie, jeśli zasób kontrolowany w dwóch reguł osobną akcję? Uzyskać co najmniej dwa powiadomienia? Na przykład "VM2" w tym scenariuszu:

      action rule 'AR1' defined for 'VM1' and 'VM2' with action group 'AG1' 
      action rule 'AR2' defined for 'VM2' and 'VM3' with action group 'AG1' 

    A. Dla każdego alertu na "VM1" i "VM3" grupy akcji "AG1" będzie wyzwalane jeden raz. Dla każdego alertu na "VM2" grupy akcji "AG1" może być wyzwalane, dwa razy (**regułami akcji usuwania nie zduplikowane akcje**). 

* PYTANIE: Co się stanie, jeśli mam zasobem kontrolowany w dwóch reguł osobną akcję i wymaga jednego działania, natomiast drugi dla pomijania? Na przykład "VM2", w tym scenariuszu:

      action rule 'AR1' defined for 'VM1' and 'VM2' with action group 'AG1' 
      action rule 'AR2' defined for 'VM2' and 'VM3' with suppression

    A. Dla każdego alertu na "VM1" grupy akcji "AG1" będzie wyzwalane jeden raz. Akcje i powiadomienia dla każdego alertu na "VM2" i 'VM3' zostanie pominięte. 

* PYTANIE: Co się stanie, jeśli mam regułę alertu i reguły zdefiniowane dla tego samego zasobu, grupy różnych akcji podczas wywoływania akcji? Na przykład "VM1", w tym scenariuszu:

     Reguła alertu "rule1" na "VM1" przy użyciu reguły akcji "AG2" grupy akcji "AR1" nie zostały zdefiniowane dla "VM1" z grupy akcji "AG1"  
 
    A. Dla każdego alertu na "VM1" grupy akcji "AG1" będzie wyzwalane jeden raz. Przy każdym wyzwoleniu reguły alertu "rule1" również wyzwoli on "AG2" Dodatkowo. (**akcji grup zdefiniowanych w ramach akcji reguł i reguł alertów działać niezależnie, i za pomocą nie deduplikacja**) 

## <a name="next-steps"></a>Kolejne kroki

- [Dowiedz się więcej na temat alertów na platformie Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)
