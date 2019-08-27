---
title: Omówienie alertów i monitorowania powiadomień na platformie Azure
description: Przegląd alertów na platformie Azure. Alerty, klasyczne alerty, interfejs alertów.
author: rboucher
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 01/28/2018
ms.author: robb
ms.subservice: alerts
ms.openlocfilehash: 67318fee540195fc913739d78e80649100c54e70
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034813"
---
# <a name="overview-of-alerts-in-microsoft-azure"></a>Przegląd alertów w Microsoft Azure 

W tym artykule opisano alerty, ich zalety oraz sposób rozpoczynania korzystania z nich.  




## <a name="what-are-alerts-in-microsoft-azure"></a>Co to są alerty w Microsoft Azure?
Alerty z wyprzedzeniem powiadamiają Cię, gdy w danych monitorowania zostaną znalezione ważne warunki. Umożliwiają identyfikowanie i rozwiązywanie problemów przed zapisaniem ich przez użytkowników systemu. 

W tym artykule omówiono ujednolicone środowisko alertów w Azure Monitor, które zawierają teraz alerty zarządzane przez Log Analytics i Application Insights. [Poprzednie środowisko alertów](alerts-classic.overview.md) i typy alertów są nazywane **alertami klasycznymi**. Możesz wyświetlić te starsze środowisko i starszy typ alertu, klikając pozycję **Wyświetl klasyczne alerty** w górnej części strony alertu. 

## <a name="overview"></a>Omówienie

Poniższy diagram przedstawia przepływ alertów. 

![Przepływ alertów](media/alerts-overview/Azure-Monitor-Alerts.svg)

Reguły alertów są oddzielone od alertów i akcji, które są wykonywane, gdy zostanie wyzwolony alert. 

**Reguła alertu** — reguła alertu przechwytuje cel i kryteria alertów. Reguła alertu może być w stanie włączony lub wyłączony. Alerty są wyzwalane tylko wtedy, gdy są włączone. 

Atrybuty klucza reguły alertu są następujące:

**Zasób docelowy** — definiuje zakres i sygnały dostępne dla alertów. Obiektem docelowym może być dowolny zasób platformy Azure. Przykładowe cele: maszyna wirtualna, konto magazynu, zestaw skalowania maszyn wirtualnych, obszar roboczy Log Analytics lub Application Insights zasób. W przypadku niektórych zasobów (takich jak Virtual Machines) można określić wiele zasobów jako obiekt docelowy reguły alertu.

Sygnały sygnałów są emitowane przez zasób docelowy i mogą być różne typy. Metryka, dziennik aktywności, Application Insights i dziennik.

**Kryteria** — kryteria są kombinacją sygnału i logiki zastosowanej w zasobie docelowym. Przykłady: 
   - Procent > procesora CPU 70%
   - Czas odpowiedzi serwera > 4 MS 
   - Liczba wyników zapytania dziennika > 100

**Nazwa alertu** — nazwa określona dla reguły alertu skonfigurowanej przez użytkownika

**Opis alertu** — opis reguły alertu skonfigurowanej przez użytkownika

**Ważność** — ważność alertu po spełnieniu kryteriów określonych w regule alertu. Ważność może być z zakresu od 0 do 4.

**Akcja** — określona Akcja podejmowana po wyzwoleniu alertu. Aby uzyskać więcej informacji, zobacz [grupy akcji](../../azure-monitor/platform/action-groups.md).

## <a name="what-you-can-alert-on"></a>Co można ostrzec

Możesz otrzymywać alerty dotyczące metryk i dzienników zgodnie z opisem w temacie [monitorowanie źródeł danych](../../azure-monitor/platform/data-sources-reference.md). Należą do nich, ale nie są ograniczone do:
- Wartości metryk
- Zapytania wyszukiwania w dzienniku
- Zdarzenia dziennika aktywności
- Kondycja podstawowej platformy platformy Azure
- Testy dostępności witryny sieci Web

Wcześniej Azure Monitor metryki, Application Insights, Log Analytics i Service Health miały oddzielne funkcje alertów. W miarę upływu czasu platforma Azure poprawiła i połączona zarówno z interfejsem użytkownika, jak i różnymi metodami alertów. Ta Konsolidacja jest nadal w toku. W związku z tym nadal istnieją pewne funkcje alertów, które nie są jeszcze dostępne w nowym systemie alertów.  

| **Źródło monitora** | **Typ sygnału**  | **Opis** | 
|-------------|----------------|-------------|
| Kondycja usługi | Dziennik aktywności  | Nieobsługiwane. Zobacz [tworzenie alertów dziennika aktywności dla powiadomień dotyczących usług](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).  |
| Application Insights | Testy dostępności sieci Web | Nieobsługiwane. Zobacz [alerty testu sieci Web](../../azure-monitor/app/monitor-web-app-availability.md). Dostępne dla każdej witryny sieci Web, która jest Instrumentacją do wysyłania danych do Application Insights. Otrzymuj powiadomienie, gdy dostępność lub czas odpowiedzi witryny sieci Web jest poniżej oczekiwań. |

## <a name="manage-alerts"></a>Zarządzanie alertami
Można ustawić stan alertu, aby określić, gdzie znajduje się w procesie rozwiązywania. Po spełnieniu kryteriów określonych w regule alertu zostanie utworzony lub wywołany alert o stanie *Nowy*. Stan można zmienić po potwierdzeniu alertu i po jego zamknięciu. Wszystkie zmiany stanu są przechowywane w historii alertu.

Obsługiwane są następujące stany alertów.

| State | Opis |
|:---|:---|
| Nowa | Problem został właśnie wykryty i nie został jeszcze zweryfikowany. |
| Potwierdzone | Administrator sprawdził alert i rozpoczął jego pracę. |
| Zamknięte | Problem został rozwiązany. Po zamknięciu alertu można go otworzyć ponownie, zmieniając go na inny stan. |

**Stan alertu** jest różny i niezależny od **warunku monitora**. Stan alertu jest ustawiany przez użytkownika. Warunek monitora jest ustawiany przez system. Po uruchomieniu alertu warunek monitora alertu jest ustawiany na wartość *wyzwolone*. Gdy podstawowy warunek, który spowodował wyczyszczenie alertu, zostanie ustawiony jako *rozwiązany*. Stan alertu nie jest zmieniany, dopóki użytkownik nie zmieni go. Dowiedz się [, jak zmienić stan alertów i grup inteligentnych](https://aka.ms/managing-alert-smart-group-states).

## <a name="smart-groups"></a>Grupy inteligentne 
Grupy inteligentne są w wersji zapoznawczej. 

Grupy inteligentne to agregacja alertów w oparciu o algorytmy uczenia maszynowego, co może pomóc w zmniejszeniu szumu i pomocy w rozwiązywaniu problemów. [Dowiedz się więcej o grupach inteligentnych](https://aka.ms/smart-groups) i [sposobach zarządzania grupami inteligentnymi](https://aka.ms/managing-smart-groups).


## <a name="alerts-experience"></a>Środowisko alertów 
Domyślna strona alerty zawiera podsumowanie alertów, które są tworzone w określonym przedziale czasu. Wyświetla łączną liczbę alertów dla każdej o ważności z kolumnami, które identyfikują sumę wszystkich alertów w każdym stanie dla każdej ważności. Wybierz dowolną z serwerów, aby otworzyć stronę [wszystkie alerty](#all-alerts-page) odfiltrowaną o tej ważności.

Alternatywnie można [programowo wyliczyć wystąpienia alertów generowanych w ramach subskrypcji za pomocą interfejsów API REST](#manage-your-alert-instances-programmatically).

> [!NOTE]
   >  Tylko alerty wygenerowane w ciągu ostatnich 30 dni mogą być dostępne w środowisku użytkownika lub za pomocą interfejsów API REST.

Nie pokazuje ani nie śledzi starszych [klasycznych alertów](#classic-alerts). Można zmienić subskrypcje lub parametry filtru, aby zaktualizować stronę. 

![Strona alerty](media/alerts-overview/alerts-page.png)

Możesz filtrować ten widok, wybierając wartości z menu rozwijanego w górnej części strony.

| Kolumna | Opis |
|:---|:---|
| Subscription | Wybierz subskrypcje platformy Azure, dla których chcesz wyświetlić alerty. Opcjonalnie możesz wybrać wszystkie subskrypcje. W widoku są uwzględniane tylko alerty, do których masz dostęp w wybranych subskrypcjach. |
| Resource group | Wybierz pojedynczą grupę zasobów. W widoku są uwzględniane tylko alerty z obiektami docelowymi w wybranej grupie zasobów. |
| Zakres czasu | W widoku są uwzględniane tylko alerty wywoływane w wybranym przedziale czasu. Obsługiwane wartości to Ostatnia godzina, ostatnie 24 godziny, ostatnie 7 dni i ostatnie 30 dni. |

Wybierz poniższe wartości w górnej części strony alerty, aby otworzyć inną stronę.

| Value | Opis |
|:---|:---|
| Łączna liczba alertów | Całkowita liczba alertów spełniających wybrane kryteria. Wybierz tę wartość, aby otworzyć widok wszystkie alerty bez filtrowania. |
| Grupy inteligentne | Całkowita liczba grup inteligentnych, które zostały utworzone na podstawie alertów spełniających wybrane kryteria. Wybierz tę wartość, aby otworzyć listę grup inteligentnych w widoku wszystkie alerty.
| Łączna liczba reguł alertów | Łączna liczba reguł alertów w wybranej subskrypcji i grupie zasobów. Wybierz tę wartość, aby otworzyć widok reguły przefiltrowanych według wybranej subskrypcji i grupy zasobów.


## <a name="manage-alert-rules"></a>Zarządzaj regułami alertów
Kliknij pozycję **Zarządzaj regułami alertów** , aby wyświetlić stronę **reguły** . **Zasady** są pojedynczym miejscem do zarządzania wszystkimi regułami alertów w ramach subskrypcji platformy Azure. Wyświetla listę wszystkich reguł alertów i można ją sortować na podstawie zasobów docelowych, grup zasobów, nazwy reguły lub stanu. Reguły alertów można również edytować, włączać lub wyłączać na tej stronie.  

 ![alerty — reguły](./media/alerts-overview/alerts-preview-rules.png)


## <a name="create-an-alert-rule"></a>Tworzenie reguły alertu
Alerty mogą być tworzone w spójny sposób, niezależnie od usługi monitorowania lub typu sygnału. Wszystkie wyzwolone alerty i powiązane szczegóły są dostępne na jednej stronie.
 
Utwórz nową regułę alertu, wykonując następujące trzy kroki:
1. Wybierz _element docelowy_ dla alertu.
1. Wybierz _sygnał_ z dostępnych sygnałów dla elementu docelowego.
1. Określ _logikę_ , która ma zostać zastosowana do danych ze sygnału.
 
Ten uproszczony proces tworzenia nie wymaga już znajomości źródła monitorowania lub sygnałów, które są obsługiwane przed wybraniem zasobów platformy Azure. Lista dostępnych sygnałów jest automatycznie filtrowana na podstawie wybranych zasobów docelowych. W oparciu o ten obiekt docelowy jest przeprowadzana automatyczna Definiowanie logiki reguły alertu.  

Więcej informacji o sposobie tworzenia reguł alertów można znaleźć w temacie [Tworzenie i wyświetlanie alertów oraz zarządzanie nimi przy użyciu Azure monitor](../../azure-monitor/platform/alerts-metric.md).

Alerty są dostępne w ramach kilku usług monitorowania platformy Azure. Informacje o tym, jak i kiedy należy używać poszczególnych usług, można znaleźć w temacie [monitorowanie aplikacji i zasobów platformy Azure](../../azure-monitor/overview.md). 


## <a name="all-alerts-page"></a>Strona wszystkie alerty 
Kliknij pozycję suma alertów, aby wyświetlić stronę wszystkie alerty. W tym miejscu można wyświetlić listę alertów, które zostały utworzone w wybranym przedziale czasu. Można wyświetlić listę indywidualnych alertów lub listę grup inteligentnych, które zawierają alerty. Wybierz transparent w górnej części strony, aby przełączać się między widokami.

![Strona wszystkie alerty](media/alerts-overview/all-alerts-page.png)

Widok można filtrować, wybierając poniższe wartości z menu rozwijanego w górnej części strony.

| Kolumna | Opis |
|:---|:---|
| Subscription | Wybierz subskrypcje platformy Azure, dla których chcesz wyświetlić alerty. Opcjonalnie możesz wybrać wszystkie subskrypcje. W widoku są uwzględniane tylko alerty, do których masz dostęp w wybranych subskrypcjach. |
| Resource group | Wybierz pojedynczą grupę zasobów. W widoku są uwzględniane tylko alerty z obiektami docelowymi w wybranej grupie zasobów. |
| Typ zasobu | Wybierz co najmniej jeden typ zasobu. W widoku są uwzględniane tylko alerty z obiektami docelowymi wybranego typu. Ta kolumna jest dostępna tylko po określeniu grupy zasobów. |
| Resource | Wybierz zasób. W widoku są uwzględniane tylko alerty z tym zasobem. Ta kolumna jest dostępna tylko po określeniu typu zasobu. |
| severity | Wybierz ważność alertu lub wybierz pozycję *wszystkie* , aby uwzględnić alerty wszystkich serwerów. |
| Stan monitora | Wybierz warunek monitorowania lub wybierz pozycję *wszystkie* , aby uwzględnić alerty warunków. |
| Stan alertu | Wybierz stan alertu lub wybierz pozycję *wszystkie* , aby uwzględnić alerty Stanów. |
| Monitorowanie usługi | Wybierz usługę lub wybierz pozycję *wszystkie* , aby uwzględnić wszystkie usługi. Uwzględniane są tylko alerty utworzone przez reguły korzystające z usługi jako celu. |
| Zakres czasu | W widoku są uwzględniane tylko alerty wywoływane w wybranym przedziale czasu. Obsługiwane wartości to Ostatnia godzina, ostatnie 24 godziny, ostatnie 7 dni i ostatnie 30 dni. |

Zaznacz **kolumny** w górnej części strony, aby wybrać kolumny, które mają być wyświetlane. 

## <a name="alert-details-page"></a>Strona szczegółów alertu
Po wybraniu alertu zostanie wyświetlona strona szczegółów alertu. Zawiera szczegółowe informacje dotyczące alertu i umożliwia zmianę jego stanu.

![Szczegóły alertu](media/alerts-overview/alert-detail2.png)

Strona szczegóły alertu zawiera następujące sekcje.

| `Section` | Opis |
|:---|:---|
| Podsumowanie | Wyświetla właściwości i inne istotne informacje dotyczące alertu. |
| Historia | Wyświetla listę wszystkich akcji podejmowanych przez alert oraz wszelkich zmian wprowadzonych w alercie. Obecnie ograniczone do zmian stanu. |
| Diagnostyka | Informacje o grupie inteligentnej, do której jest dołączony alert. *Liczba alertów* odnosi się do liczby alertów uwzględnionych w grupie inteligentnej. Zawiera inne alerty w tej samej grupie inteligentnej, które zostały utworzone w ciągu ostatnich 30 dni bez względu na filtr czasu na stronie listy alertów. Wybierz Alert, aby wyświetlić jego szczegóły. |

## <a name="role-based-access-control-rbac-for-your-alert-instances"></a>Kontrola dostępu oparta na rolach (RBAC) dla wystąpień alertów

Użycie i Zarządzanie wystąpieniami alertów wymaga, aby użytkownik miał wbudowane role RBAC lub [czytelnik](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) [monitorowania](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) . Role te są obsługiwane w dowolnym zakresie Azure Resource Manager od poziomu subskrypcji do szczegółowych przypisań na poziomie zasobów. Jeśli na przykład użytkownik ma tylko dostęp współautora dla maszyny wirtualnej "ContosoVM1", może korzystać tylko z alertów generowanych w "ContosoVM1" i zarządzać nimi.

## <a name="manage-your-alert-instances-programmatically"></a>Programowe Zarządzanie wystąpieniami alertów

Istnieje wiele scenariuszy, w których warto programowo wykonać zapytania dotyczące alertów generowanych w ramach subskrypcji. Może to być Tworzenie niestandardowych widoków poza Azure Portal lub analizowanie alertów w celu identyfikowania wzorców i trendów.

Możesz wykonywać zapytania dotyczące alertów generowanych w ramach subskrypcji za pomocą [interfejsu API rest alert Management](https://aka.ms/alert-management-api) lub przy użyciu [interfejsu API REST usługi Azure Resource Graph dla alertów](https://docs.microsoft.com/rest/api/azureresourcegraph/resources/resources).

[Interfejs API REST usługi Azure Resource Graph dla alertów](https://docs.microsoft.com/rest/api/azureresourcegraph/resources/resources) umożliwia wykonywanie zapytań o wystąpieniach alertów w odpowiedniej skali. Jest to zalecane w scenariuszach, w których trzeba zarządzać alertami wygenerowanymi w wielu subskrypcjach. 

Następujące przykładowe żądanie do interfejsu API zwraca liczbę alertów w ramach jednej subskrypcji:

```json
{
  "subscriptions": [
    <subscriptionId>
  ],
  "query": "where type =~ 'Microsoft.AlertsManagement/alerts' | summarize count()",
  "options": {
            "dataset":"alerts"
  }
}
```
Do alertów można wykonywać zapytania dotyczące pól ["podstawowe"](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-definitions#essentials-fields) .

Za pomocą [interfejsu API REST alert Management](https://aka.ms/alert-management-api) można uzyskać więcej informacji na temat określonych alertów, w tym ich pól [kontekstu alertu](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-definitions#alert-context-fields) .

## <a name="classic-alerts"></a>Alerty klasyczne 

Funkcja wysyłania alertów dotyczących metryk Azure Monitor i dziennika aktywności przed 2018 czerwca jest nazywana "alertami (klasycznymi)". 

Aby uzyskać więcej informacji, zobacz [klasyczne alerty](./../../azure-monitor/platform/alerts-classic.overview.md)


## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej o grupach inteligentnych](https://aka.ms/smart-groups)
- [Więcej informacji na temat grup akcji](../../azure-monitor/platform/action-groups.md)
- [Zarządzanie wystąpieniami alertów na platformie Azure](https://aka.ms/managing-alert-instances)
- [Zarządzanie grupami inteligentnymi](https://aka.ms/managing-smart-groups)






