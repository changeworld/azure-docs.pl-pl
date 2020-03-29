---
title: Najważniejsze wskazówki dotyczące skalowania automatycznego
description: Wzorce skalowania automatycznego na platformie Azure dla aplikacji sieci Web, zestawów skalowania maszyn wirtualnych i usług w chmurze
ms.topic: conceptual
ms.date: 07/07/2017
ms.subservice: autoscale
ms.openlocfilehash: a05cf87e660cc6c388ea2055bb174c47b99da4a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248920"
---
# <a name="best-practices-for-autoscale"></a>Najlepsze rozwiązania dotyczące automatycznego skalowania
Skalowanie automatyczne usługi Azure Monitor dotyczy tylko [zestawów skalowania maszyn wirtualnych,](https://azure.microsoft.com/services/virtual-machine-scale-sets/) [usług w chmurze,](https://azure.microsoft.com/services/cloud-services/) [usługi aplikacji — aplikacje sieci Web](https://azure.microsoft.com/services/app-service/web/)i usług [zarządzania interfejsami API](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

## <a name="autoscale-concepts"></a>Pojęcia dotyczące skalowania automatycznego

* Zasób może mieć tylko *jedno* ustawienie skalowania automatycznego
* Ustawienie skalowania automatycznego może mieć jeden lub więcej profili, a każdy profil może mieć jedną lub więcej reguł skalowania automatycznego.
* Ustawienie skalowania automatycznego skaluje wystąpienia w poziomie, co jest *obecnie* przez zwiększenie wystąpień i *zmniejszenie* liczby wystąpień.
  Ustawienie skalowania automatycznego ma maksymalną, minimalną i domyślną wartość wystąpień.
* Zadanie skalowania automatycznego zawsze odczytuje skojarzoną metrykę do skalowania, sprawdzając, czy przekroczyła ona skonfigurowany próg skalowania w poziomie lub skalowania. Listę metryk, które można skalować automatycznie, można skalować w [usłudze Azure Monitor skalowania automatycznego wspólnych metryk.](autoscale-common-metrics.md)
* Wszystkie progi są obliczane na poziomie wystąpienia. Na przykład "skalowanie w poziomie przez jedno wystąpienie, gdy średni procesor CPU > 80%, gdy liczba wystąpień wynosi 2", oznacza skalowanie w poziomie, gdy średni procesor CPU we wszystkich wystąpieniach jest większy niż 80%.
* Wszystkie błędy skalowania automatycznego są rejestrowane w dzienniku aktywności. Następnie można skonfigurować [alert dziennika aktywności,](./../../azure-monitor/platform/activity-log-alerts.md) dzięki czemu można otrzymywać powiadomienia za pośrednictwem poczty e-mail, WIADOMOŚCI SMS lub webhooks, gdy występuje błąd skalowania automatycznego.
* Podobnie wszystkie udane akcje skalowania są księgowane w dzienniku aktywności. Następnie można skonfigurować alert dziennika aktywności, dzięki czemu można otrzymywać powiadomienia za pośrednictwem poczty e-mail, WIADOMOŚCI SMS lub webhooks, gdy istnieje pomyślna akcja skalowania automatycznego. Można również skonfigurować powiadomienia e-mail lub webhook, aby otrzymywać powiadomienia o pomyślnych akcjach skalowania za pośrednictwem karty powiadomień na ustawieniu skalowania automatycznego.

## <a name="autoscale-best-practices"></a>Najważniejsze wskazówki dotyczące skalowania automatycznego

Podczas korzystania ze skalowania automatycznego należy stosować następujące najlepsze rozwiązania.

### <a name="ensure-the-maximum-and-minimum-values-are-different-and-have-an-adequate-margin-between-them"></a>Upewnij się, że wartości maksymalne i minimalne są różne, a między nimi jest odpowiedni margines

Jeśli masz ustawienie, które ma minimum = 2, maximum = 2 i bieżąca liczba wystąpień wynosi 2, nie może wystąpić żadna akcja skalowania. Zachowaj odpowiedni margines między maksymalną i minimalną liczbą wystąpień, które są włącznie. Skalowanie automatyczne zawsze skaluje się między tymi limitami.

### <a name="manual-scaling-is-reset-by-autoscale-min-and-max"></a>Skalowanie ręczne jest resetowane przez wartości minimalne i maksymalne autoskalowania

Jeśli ręcznie zaktualizujesz liczbę wystąpień do wartości powyżej lub poniżej wartości maksymalnej, aparat skalowania automatycznego automatycznie przeskaluje się do minimum (jeśli poniżej) lub do maksimum (jeśli powyżej). Na przykład można ustawić zakres od 3 do 6. Jeśli masz jedno uruchomione wystąpienie, aparat skalowania automatycznego skaluje się skaluje do trzech wystąpień w następnym uruchomieniu. Podobnie, jeśli ręcznie ustawisz skalę na osiem wystąpień, w następnym uruchomieniu skalowanie automatyczne będzie skalować go z powrotem do sześciu wystąpień w następnym uruchomieniu.  Skalowanie ręczne jest tymczasowe, chyba że zresetować reguły skalowania automatycznego, jak również.

### <a name="always-use-a-scale-out-and-scale-in-rule-combination-that-performs-an-increase-and-decrease"></a>Zawsze używaj kombinacji reguł skalowaj w poziomie i skalowania, która wykonuje wzrost i zmniejszenie
Jeśli używasz tylko jednej części kombinacji, skalowanie automatyczne będzie działać tylko w jednym kierunku (skalować w poziomie lub w), dopóki nie osiągnie maksymalną lub minimalną liczbę wystąpień zdefiniowanych w profilu. Nie jest to optymalne, najlepiej, jeśli chcesz, aby twój zasób był skalowany w czasie wysokiego użycia, aby zapewnić dostępność. Podobnie w czasach niskiego użycia chcesz, aby zasoby były skalowane w dół, dzięki czemu można zrealizować oszczędności kosztów.

### <a name="choose-the-appropriate-statistic-for-your-diagnostics-metric"></a>Wybierz odpowiednią statystykę dla metryki diagnostycznej
W przypadku metryk diagnostycznych można wybrać opcję *Średnia*, *Minimalna*, *Maksymalna* i *Suma* jako metrykę do skalowania według. Najczęstszą statystyką jest *Średnia*.

### <a name="choose-the-thresholds-carefully-for-all-metric-types"></a>Ostrożnie wybieraj progi dla wszystkich typów metryk
Zalecamy staranne doboru różnych progów skalowania w poziomie i skalowania w oparciu o sytuacje praktyczne.

*Nie zaleca się* skalowania automatycznego ustawień, takich jak poniższe przykłady z tymi samymi lub podobnymi wartościami progowymi dla out i w warunkach:

* Zwiększanie wystąpień o 1 liczbę, gdy liczba wątków >= 600
* Zmniejsz liczbę wystąpień o 1, gdy liczba wątków <= 600

Spójrzmy na przykład tego, co może prowadzić do zachowania, które może wydawać się mylące. Należy wziąć pod uwagę następującą sekwencję.

1. Załóżmy, że istnieją dwa wystąpienia, aby rozpocząć, a następnie średnia liczba wątków na wystąpienie rośnie do 625.
2. Skalowanie automatyczne skaluje się w poziomie, dodając trzecie wystąpienie.
3. Następnie załóżmy, że średnia liczba wątków w wystąpieniu spada do 575.
4. Przed skalowaniem w dół skalowanie automatyczne próbuje oszacować, jaki będzie stan końcowy, jeśli zostanie przeskalowany. Na przykład 575 x 3 (bieżąca liczba wystąpień) = 1725 / 2 (ostateczna liczba wystąpień po skalowaniu w dół) = 862,5 wątków. Oznacza to, że skalowanie automatyczne musiałoby natychmiast skalować w poziomie, nawet po jego skalowaniu, jeśli średnia liczba wątków pozostaje taka sama, a nawet spada tylko niewielką ilość. Jednakże, jeśli ponownie skalowane, cały proces będzie powtarzać, co prowadzi do nieskończonej pętli.
5. Aby uniknąć tej sytuacji (określanej jako "trzepotanie"), skalowanie automatyczne nie skaluje się w dół. Zamiast tego pomija i ponownie wycenia warunek przy następnym wykonaniu zadania usługi. Stan trzepotanie może mylić wiele osób, ponieważ skalowanie automatyczne nie wydaje się działać, gdy średnia liczba wątków wynosiła 575.

Szacowanie podczas skalowania w ma na celu uniknięcie sytuacji "trzepotanie", gdzie skala w i skalowanie w poziomie akcje stale iść tam iz powrotem. Należy o tym zachować, gdy wybierzesz te same progi dla skalowania w poziomie i w.

Zalecamy wybranie odpowiedniego marginesu między skalą w poziomie a progami. Na przykład należy wziąć pod uwagę następujące lepszą kombinację reguł.

* Zwiększ liczbę wystąpień o 1, gdy % cpu >= 80
* Zmniejsz liczbę wystąpień o 1, gdy % cpu <= 60

W takim przypadku  

1. Załóżmy, że istnieją 2 wystąpienia, aby rozpocząć.
2. Jeśli średni% procesora CPU w różnych wystąpieniach wynosi 80, skalowanie automatyczne jest skalowane w poziomie, dodając trzecie wystąpienie.
3. Teraz załóżmy, że z czasem cpu% spada do 60.
4. Reguła skalowania automatycznego wydjęje wartość końcowego stanu, jeśli ma być skalowana. Na przykład 60 x 3 (bieżąca liczba wystąpień) = 180 / 2 (ostateczna liczba wystąpień po zmniejszeniu) = 90. Skalowanie automatyczne nie jest skalowane, ponieważ musiałoby natychmiast natychmiast wyskalować w poziomie. Zamiast tego pomija skalowanie w dół.
5. Następnym razem, gdy automatyczne skalowanie sprawdza, procesor nadal spada do 50. Szacuje ponownie - 50 x 3 wystąpienie = 150 / 2 wystąpień = 75, który jest poniżej progu skalowania w poziomie 80, więc skaluje się pomyślnie do 2 wystąpień.

### <a name="considerations-for-scaling-threshold-values-for-special-metrics"></a>Zagadnienia dotyczące skalowania wartości progowych dla metryk specjalnych
 W przypadku specjalnych metryk, takich jak metryka Długość kolejki magazynu lub usługi Service Bus, próg jest średnią liczbą komunikatów dostępnych na bieżącą liczbę wystąpień. Ostrożnie wybierz wartość progową dla tej metryki.

Zilustrujmy to przykładem, aby upewnić się, że lepiej rozumiesz zachowanie.

* Zwiększanie wystąpień o 1 liczbę, gdy liczba komunikatów kolejki magazynu >= 50
* Zmniejszanie wystąpień o 1 liczbę, gdy liczba komunikatów kolejki magazynu <= 10

Należy wziąć pod uwagę następującą sekwencję:

1. Istnieją dwa wystąpienia kolejki magazynu.
2. Wiadomości nadchodzą, a podczas przeglądania kolejki magazynu całkowita liczba odczytuje 50. Można założyć, że skalowanie automatyczne należy rozpocząć akcję skalowania w poziomie. Należy jednak pamiętać, że nadal jest 50/2 = 25 komunikatów na wystąpienie. Tak więc skalowanie w poziomie nie występuje. Dla pierwszego skalowania w poziomie, całkowita liczba komunikatów w kolejce magazynu powinna wynosić 100.
3. Następnie załóżmy, że całkowita liczba wiadomości osiągnie 100.
4. Trzecie wystąpienie kolejki magazynu jest dodawane z powodu akcji skalowania w poziomie.  Następna akcja skalowania w poziomie nie nastąpi, dopóki całkowita liczba wiadomości w kolejce nie osiągnie 150, ponieważ 150/3 = 50.
5. Teraz liczba wiadomości w kolejce staje się mniejsza. W przypadku trzech wystąpień pierwsza akcja skalowania wstępuje, gdy całkowita liczba wiadomości we wszystkich kolejkach sumuje się do 30, ponieważ 30/3 = 10 komunikatów na wystąpienie, co jest prógem skalowania.

### <a name="considerations-for-scaling-when-multiple-profiles-are-configured-in-an-autoscale-setting"></a>Zagadnienia dotyczące skalowania w przypadku skonfigurowania wielu profilów w ustawieniu autoskalowania
W ustawieniach skalowania automatycznego można wybrać profil domyślny, który jest zawsze stosowany bez zależności od harmonogramu lub godziny, lub można wybrać profil cykliczny lub profil dla ustalonego okresu z zakresem daty i godziny.

Podczas automatycznego skalowania usługi przetwarza je, zawsze sprawdza w następującej kolejności:

1. Profil stałej daty
2. Profil cykliczny
3. Domyślny profil ("Zawsze")

Jeśli warunek profilu jest spełniony, skalowanie automatyczne nie sprawdza następnego warunku profilu pod nim. Skalowanie automatyczne przetwarza tylko jeden profil naraz. Oznacza to, że jeśli chcesz również dołączyć warunek przetwarzania z profilu niższego poziomu, należy uwzględnić te reguły, jak również w bieżącym profilu.

Przejrzyjmy przykład:

Poniższy obraz ekspozycyjnie przedstawia ustawienie skalowania automatycznego z domyślnym profilem minimalnych wystąpień = 2 i maksymalne wystąpienia = 10. W tym przykładzie reguły są skonfigurowane do skalowania w poziomie, gdy liczba wiadomości w kolejce jest większa niż 10 i skalowania w, gdy liczba wiadomości w kolejce jest mniejsza niż trzy. Więc teraz zasób można skalować od dwóch do dziesięciu wystąpień.

Ponadto istnieje cykliczny zestaw profili na poniedziałek. Jest ustawiona dla minimalnych wystąpień = 3 i maksymalne wystąpienia = 10. Oznacza to, że w poniedziałek, po raz pierwszy automatyczna skala sprawdza dla tego warunku, jeśli liczba wystąpień jest dwa, skaluje się do nowego minimum trzech. Tak długo, jak skalowanie automatyczne będzie nadal znajdować ten warunek profilu dopasowany (poniedziałek), przetwarza tylko reguły skalowania w poziomie i w poziomie procesora CPU skonfigurowane dla tego profilu. W tej chwili nie sprawdza długości kolejki. Jeśli jednak chcesz również sprawdzić warunek długości kolejki, należy uwzględnić te reguły z profilu domyślnego, a także w profilu poniedziałkowym.

Podobnie, gdy skalowanie automatyczne przełącza się z powrotem do profilu domyślnego, najpierw sprawdza, czy spełnione są warunki minimalne i maksymalne. Jeśli liczba wystąpień w tym czasie wynosi 12, skaluje się do 10, maksymalna dozwolona dla profilu domyślnego.

![ustawienia skalowania automatycznego](./media/autoscale-best-practices/insights-autoscale-best-practices-2.png)

### <a name="considerations-for-scaling-when-multiple-rules-are-configured-in-a-profile"></a>Zagadnienia dotyczące skalowania w przypadku skonfigurowania wielu reguł w profilu

Istnieją przypadki, w których może być trzeba ustawić wiele reguł w profilu. Następujące reguły skalowania automatycznego są używane przez aparat skalowania automatycznego, gdy ustawiono wiele reguł.

W *skalowaniu w poziomie*skalowanie automatyczne jest uruchamiane, jeśli zostanie spełniona dowolna reguła.
Skalowanie *w skali,* skalowanie automatyczne wymaga spełnienia wszystkich reguł.

Aby zilustrować, załóżmy, że masz następujące cztery reguły skalowania automatycznego:

* Jeśli procesor < 30%, skalowanie przez 1
* Jeśli pamięć < 50%, skalowanie przez 1
* Jeśli procesor > 75%, skalowanie w poziomie o 1
* Jeśli pamięć > 75%, skalowanie w poziomie o 1

Następnie następuje:

* Jeśli procesor CPU wynosi 76%, a pamięć wynosi 50%, skalujemy w poziomie.
* Jeśli procesor CPU wynosi 50%, a pamięć wynosi 76%, skalujemy w poziomie.

Z drugiej strony, jeśli procesor JEST 25%, a pamięć jest 51% skalowanie automatyczne **nie** skaluje się w. Aby skalować, procesor musi wynosić 29%, a pamięć 49%.

### <a name="always-select-a-safe-default-instance-count"></a>Zawsze wybieraj bezpieczną domyślną liczbę wystąpień
Domyślna liczba wystąpień jest ważna, ponieważ skalowanie automatyczne skaluje usługę do tej liczby, gdy metryki nie są dostępne. W związku z tym wybierz domyślną liczbę wystąpień, która jest bezpieczna dla obciążeń.

### <a name="configure-autoscale-notifications"></a>Skonfiguruj powiadomienia autoskalowania
Skalowanie automatyczne będzie księgować w dzienniku aktywności, jeśli wystąpi którykolwiek z następujących warunków:

* Skalowanie automatyczne wystawia operację skalowania.
* Usługa skalowania automatycznego pomyślnie kończy akcję skalowania.
* Usługa skalowania automatycznego nie może podjąć akcji skalowania.
* Metryki nie są dostępne dla usługi skalowania automatycznego, aby podjąć decyzję o skali.
* Metryki są ponownie dostępne (odzyskiwanie), aby podjąć decyzję o skali.

Można również użyć alertu dziennika aktywności, aby monitorować kondycję aparatu skalowania automatycznego. Poniżej przedstawiono przykłady [tworzenia alertu dziennika aktywności w celu monitorowania wszystkich operacji aparatu skalowania automatycznego w ramach subskrypcji](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert) lub [utworzenia alertu dziennika aktywności w celu monitorowania wszystkich nieudanych skalowania automatycznego w dół/skalowania w poziomie operacji w ramach subskrypcji](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert).

Oprócz używania alertów dziennika aktywności można również skonfigurować powiadomienia e-mail lub webhook, aby otrzymywać powiadomienia o pomyślnych akcjach skalowania za pomocą karty powiadomień w ustawieniach skalowania automatycznego.

## <a name="next-steps"></a>Następne kroki
- [Utwórz alert dziennika aktywności, aby monitorować wszystkie operacje silnika w skali automatycznej w ramach subskrypcji.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Tworzenie alertu dziennika aktywności w celu monitorowania wszystkich nieudanych operacji skalowania automatycznego w subskrypcji/skalowania w poziomie](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

