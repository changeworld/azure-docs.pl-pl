---
title: Najlepsze rozwiązania dotyczące automatycznego skalowania
description: Wzorce skalowania automatycznego na platformie Azure dla aplikacji sieci Web, zestawy skalowania maszyn wirtualnych i usług w chmurze
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/07/2017
ms.author: ancav
ms.subservice: autoscale
ms.openlocfilehash: 3700fb90318da3787830f9b6c202436c0e45e2fe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61063396"
---
# <a name="best-practices-for-autoscale"></a>Najlepsze rozwiązania dotyczące automatycznego skalowania
Skalowanie automatyczne platformy Azure Monitor ma zastosowanie tylko do [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [usług w chmurze](https://azure.microsoft.com/services/cloud-services/), [App Service — Web Apps](https://azure.microsoft.com/services/app-service/web/), i [usługi API Management](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

## <a name="autoscale-concepts"></a>Pojęcia dotyczące skalowania automatycznego
* Zasób może mieć tylko *jeden* ustawienie skalowania automatycznego
* Ustawienia automatycznego skalowania może zawierać jeden lub więcej profilów, a każdy profil może mieć jedną lub więcej reguł skalowania automatycznego.
* Ustawienia automatycznego skalowania umożliwia skalowanie wystąpienia w poziomie, który jest *się* , zwiększając wystąpienia i *w* przez zmniejszenie liczby wystąpień.
  Ustawienie skalowania automatycznego zawiera maksymalną, minimum i wartość domyślna wystąpień.
* Zadanie automatycznego skalowania ma zawsze wartość skojarzona metrykę, aby skalować, sprawdzania, czy ma ona przekroczyła skonfigurowany próg dla skalowalnego w poziomie lub w skali. Można wyświetlić listę metryk tej Skalowanie automatyczne pozwala skalować przez na [typowe metryki automatycznego skalowania usługi Azure Monitor](autoscale-common-metrics.md).
* Wszystkie Progi są obliczane na poziomie wystąpienia. Na przykład "skalowanie na zewnątrz o jedno wystąpienie gdy średni procesora CPU > 80%, gdy liczba wystąpień to 2", oznacza skalowalnego w poziomie, gdy średnie użycie procesora CPU we wszystkich wystąpieniach jest większy niż 80%.
* Wszystkie błędy automatycznego skalowania są rejestrowane w dzienniku aktywności. Następnie możesz skonfigurować [alertu dziennika aktywności](./../../azure-monitor/platform/activity-log-alerts.md) , dzięki czemu użytkownik może zostać poinformowany za pośrednictwem poczty e-mail, wiadomości SMS lub elementów webhook, zawsze, gdy występuje błąd automatycznego skalowania.
* Podobnie wszystkie akcje skalowania pomyślnie są ogłaszane w dzienniku aktywności. Następnie należy skonfigurować alertu dziennika aktywności, dzięki czemu użytkownik może zostać poinformowany za pośrednictwem poczty e-mail, wiadomości SMS lub elementów webhook, zawsze wtedy, gdy ma akcji automatycznego skalowania pomyślnie. Można również skonfigurować adres e-mail lub elementu webhook powiadomienia otrzymywania powiadomień o akcjach skalowania pomyślnie za pomocą karty powiadomienia w ustawieniu skalowania automatycznego.

## <a name="autoscale-best-practices"></a>Najlepszych praktykach dotyczących skalowania
Zgodnie z używania funkcji automatycznego skalowania, należy użyć następujących najlepszych rozwiązań.

### <a name="ensure-the-maximum-and-minimum-values-are-different-and-have-an-adequate-margin-between-them"></a>Upewnij się, maksymalne i minimalne wartości są różne i masz odpowiedni poziom między nimi
W przypadku ustawienia, który ma co najmniej = 2, maksymalna = 2, a bieżąca liczba wystąpień to 2, nie akcji skalowania może wystąpić. Zachowaj odpowiedni poziom między liczby wystąpienie maksymalne i minimalne, które są włącznie. Między tymi limitami zawsze skalowania automatycznego.

### <a name="manual-scaling-is-reset-by-autoscale-min-and-max"></a>Skalowanie ręczne jest resetowany przez automatyczne skalowanie, min i max
Jeśli liczba wystąpień jest ręcznie zaktualizować wartość powyżej lub poniżej wartości maksymalnej, aparat skalowania automatycznego jest automatycznie skalowany do minimalnej (Jeśli poniżej) lub maksymalną (jeśli powyżej). Na przykład możesz ustawić z zakresu od 3 do 6. W przypadku jedno uruchomione wystąpienie aparatu automatycznego skalowania można skalować do trzech wystąpień po jego następnym uruchomieniu. Podobnie jeśli ręcznie ustawisz skalowania do ośmiu wystąpień, na następnej uruchamiania automatycznego skalowania będzie przeskalujesz ją do sześć wystąpień po jego następnym uruchomieniu.  Skalowanie ręczne jest tymczasowy, o ile nie zostanie zresetowane przez reguły skalowania automatycznego.

### <a name="always-use-a-scale-out-and-scale-in-rule-combination-that-performs-an-increase-and-decrease"></a>Zawsze używaj kombinacji reguła skalowania w poziomie i skalowania na zewnątrz, którą wykonuje zwiększyć i zmniejszyć
Jeśli używasz tylko jednej części zestawu, automatycznego skalowania zajmie tylko akcji w jednym kierunku (skala out ani in), dopóki nie osiągnie maksymalnej lub wystąpienia minimalną liczbę, zdefiniowaną w profilu. To nie jest optymalne, optymalnym rozwiązaniem jest zasób skalowanie w górę w czasie wysokiego użycia, aby zapewnić dostępność. Podobnie czasami z o niskim użyciu ma zasobu w taki sposób, aby skalować w dół tak, jakie możesz uzyskać oszczędności.

### <a name="choose-the-appropriate-statistic-for-your-diagnostics-metric"></a>Wybierz odpowiednie statystyki dla swojej metryki diagnostyki
Na metrykę diagnostyki można wybierać spośród *średni*, *co najmniej*, *maksymalna* i *łączna liczba* jako metrykę, aby skalować. Najbardziej typowe Statystyka jest *średni*.

### <a name="choose-the-thresholds-carefully-for-all-metric-types"></a>Wybierz wartości progowe dokładnie dla wszystkich typów metryki
Firma Microsoft zaleca, starannie wybierając progi różnią się dla skalowalnego w poziomie i skalowanie w oparciu o sytuacjach praktycznych.

Firma Microsoft *nie zaleca się* ustawień automatycznego skalowania, takich jak poniższych przykładów, przy użyciu tych samych lub bardzo podobnych progu wartości, a w warunkach:

* Zwiększ wystąpień o 1 Jeśli liczba Liczba wątków < = 600
* Zmniejsz wystąpień o 1 Jeśli liczba Liczba wątków > = 600

Spójrzmy na przykład co może prowadzić do zachowania, które mogą wydawać się niejasne. Należy wziąć pod uwagę następujące sekwencji.

1. Przyjęto założenie, istnieją dwa wystąpienia rozpoczynać się i następnie wzrośnie 625, średnia liczba wątków dla każdego wystąpienia.
2. Limit dodawania trzecie wystąpienie skalowania automatycznego.
3. Następnie przyjęto założenie, że liczba wątków średnia na wystąpienie mieści się 575.
4. Przed skalowania w dół prób skalowania automatycznego można oszacować, na jakie stan końcowy będzie, jeśli go przeskalować w pionie. Na przykład 575 x 3 (bieżąca liczba wystąpień) = 1,725 / 2 (końcowej liczby wystąpień podczas skalowania w dół) = 862.5 wątków. Oznacza to, że automatyczne skalowanie, musi natychmiast skalowalnego w poziomie ponownie mimo skalowania, jeśli liczba wątków średni pozostają bez zmian lub nawet znajduje się tylko niewielką ilość. Jednak jeśli on skalowany w górę ponownie całego procesu powtórzysz, co prowadzi do wejścia w nieskończoną pętlę.
5. Aby uniknąć tej sytuacji (określane jako "niestabilny"), skalowanie automatyczne nie skalować w dół w ogóle. Zamiast tego należy pomija i reevaluates warunek ponownie przy kolejnym uruchomieniu wykonuje zadania usługi. Może zdezorientować wiele osób, ponieważ automatyczne skalowanie nie pojawi się działają, gdy liczba wątków średni został 575.

Szacowanie podczas skalowania w jest przeznaczona do uniknięcia "niestabilny" sytuacje, gdzie akcji skalowania na zewnątrz i skalowalnego w poziomie stale go i z powrotem. Zachowaj to zachowanie, należy pamiętać, w przypadku wybrania tej samej wartości progowe dla skalowalnego w poziomie i w.

Firma Microsoft zaleca, wybierając odpowiedni poziom między skalowalne w poziomie i progów. Na przykład należy wziąć pod uwagę następujące lepsze połączenie reguły.

* Zwiększ wystąpień o 1 count, gdy procent użycia procesora CPU > = 80
* Zmniejsz wystąpień o 1 count, gdy procent użycia procesora CPU < = 60

W tym przypadku  

1. Załóżmy, że istnieją 2 wystąpienia zaczynać.
2. Jeśli średni procent procesora CPU na wystąpieniach przechodzi do 80, skalowania automatycznego skalowania Dodawanie trzecie wystąpienie.
3. Teraz założenie, że wraz z upływem czasu procent użycia procesora CPU spadnie do 60.
4. Automatyczne skalowanie przez reguły skalowania szacuje stan końcowy, gdyby do skalowania na zewnątrz. Na przykład 60 x 3 (bieżąca liczba wystąpień) = 180 / 2 (końcowej liczby wystąpień podczas skalowania w dół) = 90. Aby funkcja automatycznego skalowania nie skalowania w ponieważ musiałaby skalowalnego w poziomie natychmiast ponownie. Zamiast tego pomija, skalowanie w dół.
5. Umożliwia sprawdzenie dalej skalowania automatycznego czasu procesora CPU w dalszym ciągu wchodzą do 50. Ponownie - szacuje wystąpienia 50 x 3 = 150 / 2 wystąpienia = 75, czyli poniżej progu skalowalnego w poziomie 80, więc zostanie przeprowadzone skalowanie w pomyślnie do 2 wystąpienia.

### <a name="considerations-for-scaling-threshold-values-for-special-metrics"></a>Zagadnienia dotyczące skalowania wartości progowe dla specjalnych metryk
 Dla specjalnych metryki, takie jak metryki długość magazynu lub kolejki usługi Service Bus próg wynosi średnia liczba komunikatów dostępnych na bieżącą liczbę wystąpień. Starannie wybrać wartość progowa dla tej metryki.

Teraz pokazują je z przykładem, aby upewnić się, że rozumiesz lepsze zachowanie.

* Zwiększać wystąpień według liczby 1, gdy kolejka magazynu komunikatów liczba > = 50
* Zmniejsz wystąpień według liczby 1, gdy kolejka magazynu komunikatów liczba < = 10

Należy wziąć pod uwagę następującej sekwencji:

1. Istnieją dwa wystąpienia kolejki magazynu.
2. Zachowaj mieszczących się komunikaty i podczas przeglądania kolejki magazynu, łączna liczba odczytuje 50. Można zakładać, że automatyczne skalowanie na początek akcji skalowania do wewnątrz. Jednak należy pamiętać, że nadal 50/2 = 25 komunikaty dla każdego wystąpienia. Dlatego skalowalnego w poziomie nie występuje. Pierwszy skalowalne w poziomie do wykonania całkowita liczba wiadomości w kolejce magazynu powinna być 100.
3. Następnie przyjęto założenie, że całkowita liczba wiadomości osiągnie 100.
4. 3 wystąpienie kolejki magazynu jest dodawana z powodu akcji skalowania do wewnątrz.  Następnej akcji skalowania do wewnątrz nie nastąpi aż całkowita liczba wiadomości w kolejce osiągnie 150, ponieważ 150/3 = 50.
5. Teraz zawężona liczbę komunikatów w kolejce. Za pomocą trzech wystąpień pierwszej akcji skalowania na zewnątrz się dzieje, gdy łączna liczba wiadomości we wszystkich kolejkach dodać maksymalnie 30, ponieważ 30/3 = 10 wiadomości dla każdego wystąpienia, która jest próg skalowania na zewnątrz.

### <a name="considerations-for-scaling-when-multiple-profiles-are-configured-in-an-autoscale-setting"></a>Zagadnienia dotyczące skalowania, jeśli skonfigurowano wiele profilów w ustawieniu skalowania automatycznego
W ustawieniu skalowania automatycznego można wybrać profil domyślny, który jest zawsze włączana bez wszelkich zależności od harmonogramu lub czasu, lub można wybrać profil cyklicznego na czas za pomocą zakres dat i godzin.

Podczas automatycznego skalowania, usługa przetwarza je, zawsze sprawdzają w następującej kolejności:

1. Naprawiono profilu daty
2. Profil cykliczne
3. Domyślny profil ("Always")

Spełnieniu warunku profilu skalowania automatycznego nie sprawdza warunek profilu dalej poniżej. Automatyczne skalowanie przetwarza tylko jeden profil w danym momencie. Oznacza to, jeśli chcesz również zawierać warunek przetwarzania z niższego rzędu profilu, te reguły musi zawierać również w bieżącym profilu.

Podsumujmy to przy użyciu przykładu:

Na poniższym obrazie przedstawiono ustawienia automatycznego skalowania przy użyciu profilu domyślnego minimalne wystąpień = 2 do maksymalnej wystąpień = 10. W tym przykładzie reguły są skonfigurowane do skalowania w poziomie, gdy liczba komunikatów w kolejce jest większa niż 10 i skalowania w pionie, gdy liczba komunikatów w kolejce jest mniejszy niż 3. Teraz skalować zasób od 2 do 10 wystąpień.

Ponadto jest cykliczny profilu, ustaw dla kalendarza poniedziałek. Jest ustawiona dla wystąpień minimalne = 3 i maksymalne wystąpień = 10. Oznacza to, że w poniedziałek, sprawdza ten warunek skalowania automatycznego po raz pierwszy, jeśli liczba wystąpień jest dwóch, zostanie przeprowadzone skalowanie do nowego przynajmniej trzy. Tak długo, jak skalowanie automatyczne w dalszym ciągu Znajdź ten warunek profilu dopasowane (poniedziałek), on przetwarzał tylko dla komputerów z procesorem CPU scale-out/w skonfigurowanych reguł dla tego profilu. W tej chwili nie sprawdza ona, długość kolejki. Jednak jeśli chcesz również warunek długość kolejki do sprawdzenia, możesz powinna zawierać te reguły z domyślnego profilu również w Twoim profilu od poniedziałku.

Podobnie gdy automatyczne skalowanie przełącza się do domyślnego profilu, najpierw sprawdza, jeśli są spełnione warunki minimalną i maksymalną. Jeśli liczba wystąpień w czasie jest 12, zostanie przeprowadzone skalowanie w 10, maksymalną dozwoloną dla profilu domyślnego.

![Ustawienia automatycznego skalowania](./media/autoscale-best-practices/insights-autoscale-best-practices-2.png)

### <a name="considerations-for-scaling-when-multiple-rules-are-configured-in-a-profile"></a>Zagadnienia dotyczące skalowania, jeśli skonfigurowano wiele reguł w profilu
Istnieją przypadki, gdzie trzeba ustawić wiele reguł w profilu. Następujący zestaw reguł skalowania automatycznego są używane przy użyciu usługi, gdy wiele reguł.

Na *skalowanie w poziomie*, automatycznego skalowania jest uruchamiany, jeśli wszystkie reguły jest spełniony.
Na *skalowania na zewnątrz*, automatycznego skalowania wymagają wszystkich reguł, które muszą być spełnione.

Aby zilustrować, założono, że masz następujące cztery reguły skalowania automatycznego:

* Jeśli Procesor < 30%, skalowanie w 1
* Jeśli pamięci < 50%, skalowanie w 1
* Jeśli Procesor > 75%, skalowalnego w poziomie przez 1
* Jeśli pamięć > 75%, skalowalnego w poziomie przez 1

Następnie odbywa się działaniami:

* Jeśli pamięć wynosi 50% procesora CPU jest 76%, firma Microsoft skalowalnego w poziomie.
* Jeśli procesora CPU wynosi 50%, a ilość pamięci jest 76% możemy skalowalnego w poziomie.

Z drugiej strony, jeśli procesora CPU jest 25%, a pamięci jest 51% automatyczne skalowanie odbywa się **nie** skalowania na zewnątrz. W celu skalowania w procesora CPU musi być 29% i pamięci 49%.

### <a name="always-select-a-safe-default-instance-count"></a>Zawsze wybierać bezpieczne domyślna liczba wystąpień
Domyślna liczba wystąpień jest ważne, że usługi do tej liczby skalowania automatycznego, gdy metryki są niedostępne. W związku z tym wybierz domyślną liczbę wystąpień, który jest bezpieczny dla obciążeń.

### <a name="configure-autoscale-notifications"></a>Konfigurowanie powiadomień dotyczących automatycznego skalowania
Automatyczne skalowanie opublikuje tweet dziennika aktywności, jeśli wystąpi dowolne z następujących warunków:

* Automatyczne skalowanie wystawia operacji skalowania
* Automatyczne skalowanie usługi pomyślnym ukończeniu akcji skalowania
* Automatyczne skalowanie usługi nie powiedzie się wykonać akcji skalowania.
* Metryki nie są dostępne dla usługi skalowania automatycznego do podjęcia decyzji skali.
* Metryki są dostępne (odzyskiwania) ponownie, aby podjąć decyzję skali.

Umożliwia także alertu dziennika aktywności do monitorowania prawidłowości aparat skalowania automatycznego. Poniżej przedstawiono przykłady [utworzyć działanie dziennika alertów można monitorować wszystkie operacje aparat skalowania automatycznego w ramach subskrypcji](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert) lub [utworzyć działanie dziennika alertów można monitorować wszystkie skalowania automatycznego skalowania nie powiodło się w / skalowania w poziomie operacje usługi Subskrypcja](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert).

Oprócz używania alertów dziennika aktywności, można również skonfigurować adres e-mail lub elementu webhook powiadomienia otrzymywania powiadomień o akcjach skalowania pomyślnie za pomocą karty powiadomienia w ustawieniu skalowania automatycznego.

## <a name="next-steps"></a>Następne kroki
- [Utwórz działanie dziennika alertów można monitorować wszystkie operacje aparat skalowania automatycznego w ramach subskrypcji.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Utwórz działanie dziennika alertów można monitorować wszystkie skalowania automatycznego skalowania nie powiodło się w / skalowanie w poziomie operacje w ramach subskrypcji](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

