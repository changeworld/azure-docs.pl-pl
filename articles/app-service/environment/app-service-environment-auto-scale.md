---
title: V1 Skalowanie automatyczne i środowiska usługi aplikacji
description: Skalowanie automatyczne i środowiska usługi aplikacji
services: app-service
documentationcenter: ''
author: btardif
manager: erikre
editor: ''
ms.assetid: c23af2d8-d370-4b1f-9b3e-8782321ddccb
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.openlocfilehash: 0feb6e4862f643c35a58c0321181bdda22b032e9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23837134"
---
# <a name="autoscaling-and-app-service-environment-v1"></a>V1 Skalowanie automatyczne i środowiska usługi aplikacji

> [!NOTE]
> Ten artykuł dotyczy v1 środowiska usługi aplikacji.  Istnieje nowsza wersja środowiska usługi aplikacji jest łatwiejsza w użyciu, który jest uruchamiany na bardziej zaawansowanych infrastruktury. Aby dowiedzieć się więcej o nowy początek wersji z [wprowadzenie do środowiska usługi aplikacji](intro.md).
> 

Środowiska usługi aplikacji Azure obsługuje *Skalowanie automatyczne*. Możesz pule poszczególnych procesów roboczych skalowania automatycznego na podstawie metryk lub harmonogram.

![Opcje automatycznego skalowania puli procesów roboczych.][intro]

Skalowanie automatyczne optymalizuje z wykorzystania zasobów przez automatycznie powiększania i zmniejszanie środowiska usługi aplikacji budżet dopasować i załadować profilu.

## <a name="configure-worker-pool-autoscale"></a>Konfigurowanie automatycznego skalowania puli procesu roboczego
Można uzyskać dostępu do funkcji automatycznego skalowania **ustawienia** kartę puli procesów roboczych.

![Karta Ustawienia puli procesów roboczych.][settings-scale]

Z tego miejsca interfejs powinna być dobrze znać, ponieważ jest to samo środowisko korzystania, zobacz podczas skalowania plan usługi aplikacji. 

![Ustawienia ręcznej skali.][scale-manual]

Można również skonfigurować profil skalowania automatycznego.

![Ustawienia skalowania automatycznego.][scale-profile]

Profile skalowania automatycznego są przydatne można ustawić limity dla Twojego skali. Dzięki temu można mieć wydajności spójne środowisko, ustawiając wartość skalowania dolna granica (1) i ograniczenie wydatków przewidywalną ustawiając górna granica (2).

![Ustawienia skalowania w profilu.][scale-profile2]

Po zdefiniowaniu profilu, musisz dodać reguły skalowania automatycznego skalowania w górę lub w dół liczbę wystąpień w puli procesów roboczych w granicach zdefiniowane przez profil. Reguły skalowania automatycznego są określane na podstawie.

![Reguły skalowania.][scale-rule]

 Wszelkie puli procesów roboczych lub frontonu metryki może służyć do definiowania reguł skalowania automatycznego. Te metryki są te same metryki, można monitorować na wykresach bloku zasobów lub Ustaw alerty dla.

## <a name="autoscale-example"></a>Przykład skalowania automatycznego
Funkcja automatycznego skalowania środowiska usługi aplikacji najlepiej można zilustrowane Instruktaż scenariusza.

W tym artykule opisano zagadnienia niezbędne podczas konfigurowania automatycznego skalowania. Artykuł przeprowadzi Cię przez interakcji, które są dostarczane do odtwarzania po uwzględnieniu Skalowanie automatyczne środowiska usługi aplikacji, które znajdują się w środowisku usługi aplikacji.

### <a name="scenario-introduction"></a>Wprowadzenie do scenariusza
Piotr jest sysadmin w przedsiębiorstwie, który został zmigrowany część obciążenia, które zarządza środowiska usługi aplikacji.

Środowisko usługi aplikacji jest skonfigurowana do ręcznego skali:

* **Zakończenia przód:** 3
* **Proces roboczy puli 1**: 10
* **Proces roboczy puli 2**: 5
* **Proces roboczy puli 3**: 5

Puli procesów roboczych 1 jest używany w przypadku obciążeń produkcyjnych, podczas gdy puli procesów roboczych 2 i 3. puli procesów roboczych służą do zapewniania jakości (QA) i rozwoju obciążeń.

Planów usługi aplikacji — odpowiedzi na pytania i deweloperów są skonfigurowane do ręcznego skali. Produkcji planu usługi aplikacji ma ustawioną skalowania automatycznego na wypadek wahania obciążenia i ruchu.

Piotr jest bardzo zapoznać się z aplikacji. Zna, że są godzinach szczytu obciążenia między 9:00 i 18:00:00, ponieważ jest z biznesowych aplikacji (LOB), używanego przez pracowników, gdy są one w biurze. Użycie spadnie po tym, gdy użytkownicy są wykonywane za ten dzień. Poza godzinami szczytu występuje nadal niektóre obciążenia, ponieważ użytkownicy mogą zdalnie dostęp do aplikacji za pomocą ich urządzeń przenośnych lub komputerów domowych. Produkcji planu usługi aplikacji jest już skonfigurowana do skalowania automatycznego na podstawie użycia procesora CPU z następującymi regułami:

![Ustawienia specyficzne dla aplikacji biznesowych.][asp-scale]

| **Profil skalowania automatycznego — dni tygodnia — plan usługi aplikacji** | **Profil skalowania automatycznego — weekendów — plan usługi aplikacji** |
| --- | --- |
| **Nazwa:** profilu dzień tygodnia |**Nazwa:** weekendowe profilu |
| **Skala przez:** reguły wydajności i harmonogramu |**Skala przez:** reguły wydajności i harmonogramu |
| **Profil:** dni tygodnia |**Profil:** weekendowe |
| **Typ:** cyklu |**Typ:** cyklu |
| **Zakres docelowy:** wystąpień 5 do 20 |**Zakres docelowy:** wystąpień 3 – 10 |
| **Liczba dni:** poniedziałek, Wtorek, środę, czwartek i piątek |**Liczba dni:** sobota, niedziela |
| **Czas rozpoczęcia:** 9:00 AM |**Czas rozpoczęcia:** 9:00 AM |
| **Strefa czasowa:** UTC-08 |**Strefa czasowa:** UTC-08 |
|  | |
| **Reguły automatycznego skalowania (Skaluj w górę)** |**Reguły automatycznego skalowania (Skaluj w górę)** |
| **Zasób:** produkcji (środowiska usługi aplikacji) |**Zasób:** produkcji (środowiska usługi aplikacji) |
| **Metryka:** procent użycia procesora CPU |**Metryka:** procent użycia procesora CPU |
| **Operacja:** przekracza 60% |**Operacja:** większe niż 80% |
| **Czas trwania:** 5 minut |**Czas trwania:** 10 minut |
| **Czas agregacji:** średni |**Czas agregacji:** średni |
| **Akcja:** zwiększyć liczbę przez 2 |**Akcja:** zwiększyć liczbę 1 |
| **Chłodny w dół (w minutach):** 15 |**Chłodny w dół (w minutach):** 20 |
|  | |
| **Reguły automatycznego skalowania (skali w dół)** |**Reguły automatycznego skalowania (skali w dół)** |
| **Zasób:** produkcji (środowiska usługi aplikacji) |**Zasób:** produkcji (środowiska usługi aplikacji) |
| **Metryka:** procent użycia procesora CPU |**Metryka:** procent użycia procesora CPU |
| **Operacja:** mniej niż 30% |**Operacja:** mniej niż 20% |
| **Czas trwania:** 10 minut |**Czas trwania:** 15 minut |
| **Czas agregacji:** średni |**Czas agregacji:** średni |
| **Akcja:** zmniejszyć liczbę 1 |**Akcja:** zmniejszyć liczbę 1 |
| **Chłodny w dół (w minutach):** 20 |**Chłodny w dół (w minutach):** 10 |

### <a name="app-service-plan-inflation-rate"></a>Szybkość inflacji planu usługi aplikacji
Planów usługi aplikacji, które są skonfigurowane do automatycznego skalowania zrobić maksymalna szybkość na godzinę. Ta stawka można jest obliczana na podstawie wartości podanych dla reguły skalowania automatycznego.

Opis i obliczanie *stawka inflacji planu usługi aplikacji* jest ważne w przypadku automatycznego skalowania środowiska usługi aplikacji, ponieważ nie są natychmiastowe zmiany skali w puli procesów roboczych.

Szybkość inflacji planu usługi aplikacji jest obliczana w następujący sposób:

![Obliczanie stawki inflacji planu usługi aplikacji.][ASP-Inflation]

Oparte na skalowania automatycznego — Skaluj w górę reguły dla profilu dzień tygodnia produkcji planu usługi aplikacji:

![Szybkość inflacji planu usługi aplikacji — dla dni tygodnia, w oparciu skalowania automatycznego — Skaluj w górę reguły.][Equation1]

W przypadku skalowania automatycznego — Skaluj w górę reguły dla profilu weekendowe produkcji planu usługi aplikacji rozwiąże formułę do:

![Usługi aplikacji szybkość inflacji planu weekendów oparte na skalowania automatycznego — Skaluj w górę reguły.][Equation2]

Ta wartość oblicza się do operacji w dół.

Oparte na skalowania automatycznego — zasada skali w dół profilu dzień tygodnia produkcji planu usługi aplikacji to będzie wyglądać następująco:

![Szybkość inflacji planu usługi aplikacji — dla dni tygodnia, w oparciu skalowania automatycznego — reguły do skali w dół.][Equation3]

W przypadku skalowania automatycznego — zasada skali w dół profilu weekendowe produkcji planu usługi aplikacji rozwiąże formułę do:  

![Usługi aplikacji szybkość inflacji planu weekendów oparte na skalowania automatycznego — reguły do skali w dół.][Equation4]

Produkcji planu usługi aplikacji może zwiększyć maksymalną szybkość osiem wystąpień na godzinę w tygodniu i cztery godzinę wystąpienia przez weekend. Można go wersji wystąpień maksymalnie cztery godzinę wystąpienia w tygodniu i sześciu wystąpień na godzinę w weekendy.

Jeśli wiele planów usługi aplikacji są obsługiwane w puli procesów roboczych, należy obliczyć *całkowitej szybkości inflacji* jako suma wartości współczynnika inflacji dla wszystkich planów usługi aplikacji, które są w tej puli procesów roboczych.

![Całkowita liczba inflacji Obliczanie dla wielu planów usługi aplikacji hostowanej w puli procesów roboczych.][ASP-Total-Inflation]

### <a name="use-the-app-service-plan-inflation-rate-to-define-worker-pool-autoscale-rules"></a>Umożliwia definiowanie reguł automatycznego skalowania puli procesu roboczego stawka inflacji planu usługi aplikacji
Proces roboczy puli hostujących planów usługi aplikacji, które są skonfigurowane do automatycznego skalowania konieczne można przydzielić bufora pojemności. Bufor umożliwia operacji skalowania automatycznego zwiększyć lub zmniejszyć plan usługi aplikacji, zgodnie z potrzebami. Minimalny rozmiar buforu będzie obliczona całkowitej aplikacji usługi Planowanie inflacji szybkości.

Ponieważ operacji skalowania środowiska usługi aplikacji zająć dużo czasu, aby zastosować, należy uwzględnić wszelkie zmiany dalszych zmian zapotrzebowania, które może się zdarzyć, gdy trwa operacja skalowania. Aby uwzględnić tego opóźnienia, zalecane jest użycie obliczeniowej całkowitej aplikacji usługi Planowanie inflacji szybkości jako minimalna liczba wystąpień, które są dodawane dla każdej operacji skalowania automatycznego.

Dzięki tym informacjom Paweł można zdefiniować następujące profilów skalowania automatycznego i reguł:

![Reguły profilów skalowania automatycznego na przykład LOB.][Worker-Pool-Scale]

| **Profil skalowania automatycznego — dni tygodnia** | **Profil skalowania automatycznego — weekendów** |
| --- | --- |
| **Nazwa:** profilu dzień tygodnia |**Nazwa:** weekendowe profilu |
| **Skala przez:** reguły wydajności i harmonogramu |**Skala przez:** reguły wydajności i harmonogramu |
| **Profil:** dni tygodnia |**Profil:** weekendowe |
| **Typ:** cyklu |**Typ:** cyklu |
| **Zakres docelowy:** wystąpień 13-25 |**Zakres docelowy:** wystąpień 6-15 |
| **Liczba dni:** poniedziałek, Wtorek, środę, czwartek i piątek |**Liczba dni:** sobota, niedziela |
| **Czas rozpoczęcia:** 7:00:00 |**Czas rozpoczęcia:** 9:00 AM |
| **Strefa czasowa:** UTC-08 |**Strefa czasowa:** UTC-08 |
|  | |
| **Reguły automatycznego skalowania (Skaluj w górę)** |**Reguły automatycznego skalowania (Skaluj w górę)** |
| **Zasób:** puli procesów roboczych 1 |**Zasób:** puli procesów roboczych 1 |
| **Metryka:** WorkersAvailable |**Metryka:** WorkersAvailable |
| **Operacja:** mniej niż 8 |**Operacja:** mniej niż 3 |
| **Czas trwania:** 20 minut |**Czas trwania:** 30 minut |
| **Czas agregacji:** średni |**Czas agregacji:** średni |
| **Akcja:** zwiększyć liczbę 8 |**Akcja:** zwiększyć liczbę przez 3 |
| **Chłodny w dół (w minutach):** 180 |**Chłodny w dół (w minutach):** 180 |
|  | |
| **Reguły automatycznego skalowania (skali w dół)** |**Reguły automatycznego skalowania (skali w dół)** |
| **Zasób:** puli procesów roboczych 1 |**Zasób:** puli procesów roboczych 1 |
| **Metryka:** WorkersAvailable |**Metryka:** WorkersAvailable |
| **Operacja:** większa niż 8 |**Operacja:** wyższej niż 3 |
| **Czas trwania:** 20 minut |**Czas trwania:** 15 minut |
| **Czas agregacji:** średni |**Czas agregacji:** średni |
| **Akcja:** zmniejszyć liczbę o 2 |**Akcja:** zmniejszyć liczbę 3 |
| **Chłodny w dół (w minutach):** 120 |**Chłodny w dół (w minutach):** 120 |

Zakres docelowy zdefiniowaną w profilu jest obliczana na podstawie wystąpienia minimalna zdefiniowaną w profilu dla planu usługi App Service + buforu.

Maksymalny zakres będzie sumą wszystkich określonych zakresów maksymalną dla wszystkich planów usługi aplikacji hostowanej w puli procesów roboczych.

Zwiększ liczbę elementów w skali reguł powinny zostać skonfigurowane na co najmniej 1 X aplikacji usługi planu inflacji stopa skali.

Zmniejszenie liczby można dostosować do coś między 1/2 X lub 1 X aplikacji usługi planu inflacji współczynnik skali w dół.

### <a name="autoscale-for-front-end-pool"></a>Funkcja automatycznego skalowania puli frontonu
Zasady automatycznego skalowania frontonu są prostsze niż dla pule procesów roboczych. Przede wszystkim należy  
Upewnij się, że czas trwania pomiaru i czasomierze cooldown należy wziąć pod uwagę operacji skalowania dla planu usługi aplikacji nie są natychmiastowe.

W tym scenariuszu Paweł wie, że współczynnik błędów zwiększa się, gdy interfejsy osiągnie 80% wykorzystania Procesora, a następnie ustawia zasady automatycznego skalowania zwiększające wystąpień w następujący sposób:

![Ustawienia automatycznego skalowania puli frontonu.][Front-End-Scale]

| **Profil skalowania automatycznego — zakończenia przód** |
| --- |
| **Nazwa:** skalowania automatycznego — przodu kończy się |
| **Skala przez:** reguły wydajności i harmonogramu |
| **Profil:** codzienne |
| **Typ:** cyklu |
| **Zakres docelowy:** wystąpień 3 – 10 |
| **Liczba dni:** codzienne |
| **Czas rozpoczęcia:** 9:00 AM |
| **Strefa czasowa:** UTC-08 |
|  |
| **Reguły automatycznego skalowania (Skaluj w górę)** |
| **Zasób:** puli frontonu |
| **Metryka:** procent użycia procesora CPU |
| **Operacja:** przekracza 60% |
| **Czas trwania:** 20 minut |
| **Czas agregacji:** średni |
| **Akcja:** zwiększyć liczbę przez 3 |
| **Chłodny w dół (w minutach):** 120 |
|  |
| **Reguły automatycznego skalowania (skali w dół)** |
| **Zasób:** puli procesów roboczych 1 |
| **Metryka:** procent użycia procesora CPU |
| **Operacja:** mniej niż 30% |
| **Czas trwania:** 20 minut |
| **Czas agregacji:** średni |
| **Akcja:** zmniejszyć liczbę 3 |
| **Chłodny w dół (w minutach):** 120 |

<!-- IMAGES -->
[intro]: ./media/app-service-environment-auto-scale/introduction.png
[settings-scale]: ./media/app-service-environment-auto-scale/settings-scale.png
[scale-manual]: ./media/app-service-environment-auto-scale/scale-manual.png
[scale-profile]: ./media/app-service-environment-auto-scale/scale-profile.png
[scale-profile2]: ./media/app-service-environment-auto-scale/scale-profile-2.png
[scale-rule]: ./media/app-service-environment-auto-scale/scale-rule.png
[asp-scale]: ./media/app-service-environment-auto-scale/asp-scale.png
[ASP-Inflation]: ./media/app-service-environment-auto-scale/asp-inflation-rate.png
[Equation1]: ./media/app-service-environment-auto-scale/equation1.png
[Equation2]: ./media/app-service-environment-auto-scale/equation2.png
[Equation3]: ./media/app-service-environment-auto-scale/equation3.png
[Equation4]: ./media/app-service-environment-auto-scale/equation4.png
[ASP-Total-Inflation]: ./media/app-service-environment-auto-scale/asp-total-inflation-rate.png
[Worker-Pool-Scale]: ./media/app-service-environment-auto-scale/wp-scale.png
[Front-End-Scale]: ./media/app-service-environment-auto-scale/fe-scale.png
