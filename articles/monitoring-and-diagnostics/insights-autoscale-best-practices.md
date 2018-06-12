---
title: Najlepsze praktyki dotyczące skalowania automatycznego
description: Wzorce skalowania automatycznego na platformie Azure aplikacje sieci Web, zestawy skalowania maszyny wirtualnej i usługi w chmurze
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/07/2017
ms.author: ancav
ms.component: autoscale
ms.openlocfilehash: e9fc5a4c5d2e23750493cc320dffa380724347fb
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35262447"
---
# <a name="best-practices-for-autoscale"></a>Najlepsze rozwiązania dotyczące automatycznego skalowania
Ten artykuł zawiera najlepsze rozwiązania w celu skalowania automatycznego na platformie Azure. Azure Monitor skalowania automatycznego ma zastosowanie tylko do [zestawy skalowania maszyny wirtualnej](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [usługi w chmurze](https://azure.microsoft.com/services/cloud-services/), i [usługi aplikacji — aplikacje sieci Web](https://azure.microsoft.com/services/app-service/web/). Innymi usługami Azure, użyj metod skalowania.

## <a name="autoscale-concepts"></a>Pojęcia dotyczące skalowania automatycznego
* Zasób może mieć tylko *jeden* ustawieniu skalowania automatycznego
* Ustawienia automatycznego skalowania może zawierać jeden lub więcej profilów i każdy profil może mieć co najmniej jedną regułę automatycznego skalowania.
* Ustawienia skalowania automatycznego skaluje wystąpień w poziomie, który jest *limit* przez odpowiednie zwiększenie wystąpień i *w* przez zmniejszenie liczby wystąpień.
  Ustawienie skalowania automatycznego zawiera maksymalną, minimum i wartości domyślnej wystąpień.
* Zadanie automatycznego skalowania ma zawsze wartość skojarzona metryki skalowania, sprawdzania, czy ma ona przekroczyła skonfigurowany próg dla skalowalnego w poziomie lub w skali. Umożliwia wyświetlanie listy metryk tego skalowania automatycznego można skalować przez w [metryki wspólnej Skalowanie automatyczne Azure Monitor](insights-autoscale-common-metrics.md).
* Wszystkie Progi są obliczane na poziomie wystąpienia. Na przykład "skali przez 1 wystąpienie gdy średni Procesora > 80%, gdy liczba wystąpień jest równa 2", oznacza skalowalnego w poziomie, gdy średnie wykorzystanie Procesora we wszystkich wystąpieniach jest większa niż 80%.
* Dziennik aktywności są rejestrowane wszystkie błędy skalowania automatycznego. Następnie można skonfigurować [alert dziennika aktywności](./monitoring-activity-log-alerts.md) , dzięki czemu użytkownik może zostać powiadomiony w wiadomościach e-mail i SMS, webhook, itp., gdy istnieje błąd automatycznego skalowania.
* Podobnie wszystkich akcji skalowania pomyślnie są publikowane w dzienniku aktywności. Następnie można skonfigurować alert dziennika aktywności, dzięki czemu użytkownik może zostać powiadomiony w wiadomościach e-mail i SMS, elementów webhook, itp., gdy istnieje akcja automatycznego skalowania pomyślnie. Można również skonfigurować powiadomienia e-mail lub elementu webhook Otrzymuj powiadomienia dla akcji skalowania pomyślnie za pomocą karty powiadomienia w ustawieniu skalowania automatycznego.

## <a name="autoscale-best-practices"></a>Najlepsze rozwiązania w zakresie skalowania automatycznego
Następujące najlepsze rozwiązania należy używać, ponieważ używają automatycznego skalowania.

### <a name="ensure-the-maximum-and-minimum-values-are-different-and-have-an-adequate-margin-between-them"></a>Upewnij się, maksymalne i minimalne wartości są różne, a także mieć odpowiedni margines między nimi
Jeśli to ustawienie, które ma co najmniej = 2, maksymalna = 2, bieżące liczba wystąpień jest 2, może wystąpić żadnej akcji skalowania. Zachować odpowiedni poziom między liczby wystąpień maksymalne i minimalne, które łączą się. Funkcja automatycznego skalowania skaluje zawsze między tymi limitami.

### <a name="manual-scaling-is-reset-by-autoscale-min-and-max"></a>Ręczne skalowanie jest resetowany przez skalowania automatycznego min i max
Jeśli liczba wystąpień jest ręcznie zaktualizować wartość powyżej lub poniżej wartości maksymalnej, aparat skalowania automatycznego jest automatycznie skalowany do minimum (Jeśli poniżej) lub maksymalną (jeśli powyżej). Na przykład można ustawić z zakresu od 3 do 6. Jeśli masz jedno uruchomione wystąpienie aparatu automatycznego skalowania może obsłużyć 3 wystąpień po jego następnym uruchomieniu. Podobnie jeśli ręcznie wartość skali 8 wystąpień, przy następnym skalowania automatycznego uruchamiania będzie go skalować do 6 wystąpień po jego następnym uruchomieniu.  Ręczne skalowanie jest bardzo tymczasowe, chyba że zostanie zresetowane z regułami automatycznego skalowania.

### <a name="always-use-a-scale-out-and-scale-in-rule-combination-that-performs-an-increase-and-decrease"></a>Zawsze używaj połączenia skalowalnego w poziomie i w skali reguł, które wykonuje wzrostu i zmniejszenie
Jeśli używasz tylko jednej części kombinacja dostęp do skalowania automatycznego skalowania — w tym pojedynczy przy, aż do maksymalnej lub minimalnej, zostanie osiągnięty.

### <a name="choose-the-appropriate-statistic-for-your-diagnostics-metric"></a>Wybierz odpowiednie dane statystyczne Twoje metryki diagnostyki
Diagnostyka miar, można wybierać między *średni*, *co najmniej*, *maksymalna* i *całkowita* jako metryki skalowania przez. Statystyka najczęściej jest *średni*.

### <a name="choose-the-thresholds-carefully-for-all-metric-types"></a>Wybierz progi dokładnie dla wszystkich typów metryki
Zaleca się uważnie wybranie innego progi dla skalowalnego w poziomie i skali w sytuacji, w praktyce w oparciu.

Firma Microsoft *nie jest zalecane* przykłady poniżej z wartości progowe takie same lub bardzo podobne do, wyloguj się i zaloguj warunków, takich jak ustawienia skalowania automatycznego:

* Zwiększyć wystąpień 1 Jeśli liczba Liczba wątków < = 600
* Zmniejsz wystąpienia o 1 Jeśli liczba Liczba wątków > = 600

Oto przykład co może prowadzić do zachowania, które mogą być mylące. Należy wziąć pod uwagę następujące sekwencji.

1. Załóżmy, istnieją 2 wystąpienia rozpoczynać się od znaku, a następnie średnią liczbę wątków dla każdego wystąpienia zwiększania rozmiaru w celu 625.
2. Funkcja automatycznego skalowania skaluje się dodanie 3 wystąpienia.
3. Następnie przyjęto założenie, że liczba wątków średni przez wystąpienie mieści się 575.
4. Przed skalowania, próbuje skalowania automatycznego oszacować jakie stan końcowy będzie, jeśli jest on skalowany w. Na przykład (bieżąca liczba wystąpień) 575 x 3 = 1,725 / 2 (końcowego liczba wystąpień, gdy skalowany w dół) = 862.5 wątków. Oznacza to, że skalowania automatycznego musiałaby natychmiast skalowalnego w poziomie ponownie nawet po skalowania, jeśli liczba wątków średnia jest taka sama lub nawet znajduje się tylko niewielką ilość. Jednak jeśli skalowanie go ponownie, cały proces będzie Powtórz, prowadzące do nieskończonej pętli.
5. Aby uniknąć tej sytuacji (określane jako "flapping"), skalowania automatycznego nie obsługuje w dół w ogóle. Zamiast tego pomija i reevaluates warunek ponownie przy kolejnym uruchomieniu wykonuje zadanie usługi. Może to mylić wiele osób, ponieważ skalowania automatycznego nie pojawi się do pracy, gdy liczba wątków średni 575.

Szacowanie podczas skalowania w jest przeznaczony do uniknięcia "niestabilny" sytuacji, gdy akcje w skali i skalowalnego w poziomie stale Przejdź i z powrotem. Zachowaj to zachowanie, pamiętając, po wybraniu tego samego progi dla skalowalnego w poziomie i w.

Firma Microsoft zaleca, wybierając odpowiedni poziom między skalowalne w poziomie i w progów. Na przykład należy wziąć pod uwagę następujących kombinacji lepsze reguły.

* Zwiększyć wystąpień 1 Jeśli liczba procesora CPU Wynoszące % > = 80
* Zmniejsz wystąpienia o 1 Jeśli liczba procesora CPU Wynoszące % < = 60

W takim przypadku  

1. Załóżmy, że istnieją zacząć 2 wystąpienia.
2. Jeśli średni procent użycia procesora CPU w wystąpieniach przechodzi do 80, funkcja automatycznego skalowania skaluje się dodanie trzeci wystąpienia.
3. Teraz założenie, że wraz z upływem czasu procesora CPU Wynoszące % należy do 60.
4. Jego skalowania automatycznego skalowania w reguły szacuje stan końcowy gdyby w skali. Na przykład (bieżąca liczba wystąpień) 60 x 3 = 180 / 2 (końcowego liczba wystąpień, gdy skalowany w dół) = 90. Dlatego skalowania automatycznego nie skali w ponieważ może mieć do skalowania natychmiast ponownie. Zamiast tego pomija skalowania.
5. Umożliwia sprawdzenie dalej skalowania automatycznego czasu Procesora w dalszym ciągu wchodzą do 50. Ponownie - szacuje wystąpienia 50 x 3 = 150 / 2 wystąpień = 75, który jest poniżej progu skalowalnego w poziomie 80, więc go może obsłużyć w pomyślnie 2 wystąpienia.

### <a name="considerations-for-scaling-threshold-values-for-special-metrics"></a>Zagadnienia dotyczące wartości progowe dla specjalnych metryki skalowania
 Dla specjalnych metryki na przykład metryka długość magazynu lub kolejką usługi Service Bus próg wynosi średnią liczbę wiadomości na bieżąca liczba wystąpień. Wybierz uważnie wartość progowa dla tego metryki.

Załóżmy zilustrowania przykład, aby upewnić się, że rozumiesz lepsze zachowanie.

* Zwiększenie wystąpienia według liczby 1, gdy liczba komunikatów kolejki magazynu > = 50
* Zmniejsz wystąpienia według liczby 1 Jeśli liczba komunikatów kolejki magazynu < = 10

Należy wziąć pod uwagę następującej kolejności:

1. Istnieją 2 wystąpienia kolejki magazynu.
2. Zachowaj przesyłanych wiadomości i przeglądając kolejki magazynu, łączna liczba odczytuje 50. Użytkownik może założenie, że tego skalowania automatycznego powinien rozpocząć akcji skalowania w poziomie. Jednak należy pamiętać, że nadal 50/2 = 25 komunikaty dla każdego wystąpienia. Tak skalowalnych w poziomie nie występuje. Dla pierwszego skalowalne w poziomie na to całkowita liczba wiadomości w kolejce magazynu powinna być 100.
3. Następnie przyjęto założenie, że całkowita liczba wiadomości osiągnie 100.
4. 3 wystąpienia kolejki magazynu jest dodawana z powodu akcji skalowania w poziomie.  Następnej akcji skalowania w poziomie nie nastąpi aż całkowita liczba wiadomości w kolejce osiągnie 150, ponieważ 150/3 = 50.
5. Teraz maleją liczbę wiadomości w kolejce. Z wystąpieniami 3 pierwszej akcji skalowania się stanie, jeśli całkowita liczba wiadomości we wszystkich kolejkach dodać maksymalnie 30, ponieważ 30/3 = 10 komunikaty dla każdego wystąpienia, czyli próg skali.

### <a name="considerations-for-scaling-when-multiple-profiles-are-configured-in-an-autoscale-setting"></a>Zagadnienia dotyczące skalowania, gdy wiele profilów skonfigurowanych w ustawieniu skalowania automatycznego
W ustawieniu skalowania automatycznego można wybrać profil domyślny, który jest zawsze stosowane bez jej zależności od harmonogramu lub czasu, lub możesz cyklicznego profil lub określony zakres daty i godziny.

Kiedy usługa skalowania automatycznego przetwarza je, zawsze sprawdzają w następującej kolejności:

1. Stałe profilu daty
2. Cykliczne profilu
3. Domyślny profil ("Always")

Jeśli profil warunek jest spełniony, skalowania automatycznego nie sprawdza dalej warunku profilu poniżej. Funkcja automatycznego skalowania przetwarza tylko jeden profil naraz. Oznacza to, jeśli chcesz również obejmować warunek przetwarzania z profilu niższego poziomu, te reguły musi zawierać również w bieżącym profilu.

Umożliwia przeglądanie to przykładu:

Na poniższym obrazie pokazano ustawieniu skalowania automatycznego profil domyślny minimalny wystąpień = 2 do maksymalnej wystąpień = 10. W tym przykładzie zasady są skonfigurowane do skalowania w poziomie, gdy liczba wiadomości w kolejce jest większa niż 10 i skalowania w przypadku liczba wiadomości w kolejce jest mniejsza niż 3. Dlatego teraz zasobu można skalować między wystąpieniami 2 do 10.

Ponadto jest profil cyklicznego ustawić od poniedziałku. Jest ustawiony dla wystąpień minimalna = 3, a maksymalna wystąpienia = 10. Oznacza to, w poniedziałek, pierwszy skalowania automatycznego czasu sprawdza, czy ten stan, jeśli liczba wystąpień jest równa 2, jego może obsłużyć nowy co najmniej 3. Tak długo, jak skalowania automatycznego nadal znaleźć ten warunek profilu dopasowane (poniedziałek), jego tylko reguły są przetwarzane na podstawie procesora CPU skalowania w poziomie/w skonfigurowane dla tego profilu. W tej chwili nie sprawdza długość kolejki. Jednak jeśli chcesz również można sprawdzić warunku długość kolejki, należy uwzględnić te reguły z domyślnego profilu również w profilu od poniedziałku.

Podobnie gdy zmienia skalowania automatycznego do domyślnego profilu, najpierw sprawdza, czy zostały spełnione warunki minimalną i maksymalną. Liczba wystąpień w czasie w przypadku 12, jego skaluje w 10, maksymalny dozwolony profil domyślny.

![Ustawienia skalowania automatycznego](./media/insights-autoscale-best-practices/insights-autoscale-best-practices-2.png)

### <a name="considerations-for-scaling-when-multiple-rules-are-configured-in-a-profile"></a>Zagadnienia dotyczące skalowania, gdy skonfigurowano wiele reguł w profilu
Istnieją przypadki, w którym należy ustawić wiele reguł w profilu. Następujący zestaw reguł skalowania automatycznego są używane przy użyciu usługi ustawianą wiele reguł.

Na *skalowanie*, skalowania automatycznego jest uruchamiany, jeśli reguły jest spełniony.
Na *w skali*, skalowania automatycznego wymagają wszystkich reguł, które muszą być spełnione.

Aby zilustrować, założono, że istnieje następujące reguły automatycznego skalowania 4:

* Jeśli Procesor < 30%, skalowania w 1
* Jeśli pamięci < 50% skali w 1
* Jeśli Procesor > 75%, skalowalnych w poziomie przez 1
* Jeśli pamięci > 75%, skalowalnych w poziomie przez 1

Następnie wykonaj występuje:

* Jeśli 76% jest procesora CPU i pamięci to 50%, możemy skalowalnego w poziomie.
* Jeśli procesora CPU wynosi 50% pamięci wynosi 76% możemy skalowalnego w poziomie.

Z drugiej strony, jeśli Procesora wynosi 25% i pamięci 51% skalowania automatycznego nie **nie** w skali. W celu skalowania w, procesora CPU musi być 29% i pamięć 49%.

### <a name="always-select-a-safe-default-instance-count"></a>Wybierz zawsze bezpieczne domyślną liczbę wystąpień
Domyślna liczba wystąpień jest ważne skalowania automatycznego skaluje usługi do tej liczby, gdy nie są dostępne metryki. W związku z tym wybierz domyślną liczbę wystąpień, które jest bezpieczne dla obciążeń.

### <a name="configure-autoscale-notifications"></a>Konfigurowanie powiadomień skalowania automatycznego
Funkcja automatycznego skalowania zostanie wysłany do dziennika aktywności, jeśli wystąpienia któregokolwiek z następujących warunków:

* Operacja skalowania wystawia skalowania automatycznego
* Usługa automatycznego skalowania pomyślnie ukończy akcji skalowania
* Funkcja automatycznego skalowania usługi nie powiedzie się zająć akcji skalowania.
* Metryki nie są dostępne dla usługi skalowania automatycznego podejmowania decyzji skali.
* Metryki są dostępne (odzyskiwania) ponownie podjęcie decyzji skali.

Alert dziennik aktywności umożliwia również monitorowanie kondycji aparat skalowania automatycznego. Poniżej przedstawiono przykłady [tworzenie działania dziennika alertów można monitorować wszystkie operacje aparat skalowania automatycznego na subskrypcję](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert) lub [tworzenie działania dziennika alertów można monitorować wszystkie skalowania automatycznego skalowania nie powiodło się w / skalowanie w poziomie operacje w ramach subskrypcji](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert).

Oprócz używania alertów dotyczących działań w dzienniku, można również skonfigurować powiadomienia e-mail lub elementu webhook Otrzymuj powiadomienia dla akcji skalowania pomyślnie za pomocą karty powiadomienia w ustawieniu skalowania automatycznego.

## <a name="next-steps"></a>Następne kroki
- [Utwórz działanie dziennika alertów monitorowania wszystkich operacji aparatu skalowania automatycznego na subskrypcję.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Utwórz działanie dziennika alertów można monitorować wszystkie skalowania automatycznego skalowania nie powiodło się w / skalowanie operacji dla Twojej subskrypcji](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)
