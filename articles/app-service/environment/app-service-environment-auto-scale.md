---
title: Skalowanie automatyczne w wersji 1
description: Skalowanie automatyczne i środowisko usługi aplikacji w wersji 1. Ten doc jest dostępna tylko dla klientów, którzy używają starszej wersji ASE w wersji 1.
author: btardif
ms.assetid: c23af2d8-d370-4b1f-9b3e-8782321ddccb
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 4f071c0d09fc2fa97eeea45bd82228b7eb8434a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687285"
---
# <a name="autoscaling-and-app-service-environment-v1"></a>Skalowanie automatyczne i środowisko usługi aplikacji w wersji 1

> [!NOTE]
> Ten artykuł dotyczy środowiska usługi app service w wersji 1.  Istnieje nowsza wersja środowiska usługi app service, która jest łatwiejsza w użyciu i działa na bardziej zaawansowanej infrastruktury. Aby dowiedzieć się więcej o nowej wersji, zacznij od [wprowadzenia do środowiska usługi app service](intro.md).
> 

Środowiska usługi Azure App Service obsługują *skalowanie automatyczne*. Można automatycznie skalować poszczególne pule procesów roboczych na podstawie metryk lub harmonogramu.

![Opcje skalowania automatycznego dla puli procesów roboczych.][intro]

Skalowanie automatyczne optymalizuje wykorzystanie zasobów przez automatyczne powiększanie i zmniejszanie środowiska usługi app service, aby dopasować je do budżetu i profilu obciążenia.

## <a name="configure-worker-pool-autoscale"></a>Konfigurowanie automatycznej skali puli procesów roboczych
Dostęp do funkcji skalowania automatycznego można uzyskać na karcie **Ustawienia** puli procesów roboczych.

![Karta Ustawienia puli pracowników.][settings-scale]

Stamtąd interfejs powinien być dość znane, ponieważ jest to samo środowisko, które można zobaczyć podczas skalowania planu usługi app service. 

![Ręczne ustawienia skali.][scale-manual]

Można również skonfigurować profil skalowania automatycznego.

![Ustawienia skalowania automatycznego.][scale-profile]

Profile skalowania automatycznego są przydatne do ustawiania limitów na skali. W ten sposób można mieć spójne środowisko wydajności, ustawiając wartość skali dolnej granicy (1) i przewidywalny limit wydatków, ustawiając górną granicę (2).

![Skalowanie ustawień w profilu.][scale-profile2]

Po zdefiniowaniu profilu można dodać reguły skalowania automatycznego, aby skalować w górę lub w dół liczbę wystąpień w puli procesów roboczych w granicach zdefiniowanych przez profil. Reguły skalowania automatycznego są oparte na metrykach.

![Reguła skali.][scale-rule]

 Do definiowania reguł skalowania automatycznego można użyć dowolnej puli procesów roboczych lub metryk front-endu. Te metryki są te same metryki, które można monitorować na wykresach bloku zasobów lub ustawić alerty dla.

## <a name="autoscale-example"></a>Przykład skalowania automatycznego
Skalowanie automatyczne środowiska usługi aplikacji można najlepiej zilustrować, przechodząc przez scenariusz.

W tym artykule opisano wszystkie niezbędne zagadnienia podczas konfigurowania skalowania automatycznego. W tym artykule przeprowadzi Cię przez interakcje, które wchodzą w grę, gdy czynnik w skalowanie automatyczne środowiska usługi app service, które są hostowane w środowisku usługi app service.

### <a name="scenario-introduction"></a>Wprowadzenie do scenariusza
Frank jest sysadmin dla przedsiębiorstwa, które migrowane część obciążeń, które zarządzają do środowiska usługi app service.

Środowisko usługi app service jest skonfigurowane do ręcznego skalowania w następujący sposób:

* **Przednie końce:** 3
* **Pula pracowników 1**: 10
* **Pula pracowników 2**: 5
* **Pula pracowników 3**: 5

Pula procesów roboczych 1 jest używana dla obciążeń produkcyjnych, podczas gdy pula procesów roboczych 2 i pula pracowników 3 są używane do zapewniania jakości (QA) i obciążeń deweloperskich.

Plany usługi app service dla kontroli jakości i deweloperów są skonfigurowane do ręcznego skalowania. Produkcyjny plan usługi app service jest ustawiony na skalowanie automatyczne, aby radzić sobie ze zmianami obciążenia i ruchu.

Frank jest bardzo zaznajomiony z aplikacją. Wiedzą, że godziny szczytu obciążenia są między 9:00 a 18:00, ponieważ jest to aplikacja biznesowa (LOB), z której korzystają pracownicy, gdy są w biurze. Użycie spada po tym, gdy użytkownicy są wykonywane dla tego dnia. Poza godzinami szczytu nadal istnieje pewne obciążenie, ponieważ użytkownicy mogą uzyskać zdalny dostęp do aplikacji za pomocą swoich urządzeń mobilnych lub komputerów domowych. Produkcyjny plan usługi app service jest już skonfigurowany do skalowania automatycznego na podstawie użycia procesora CPU z następującymi regułami:

![Określone ustawienia aplikacji LOB.][asp-scale]

| **Profil skalowania automatycznego — dni powszednie — plan usługi aplikacji** | **Profil skalowania automatycznego – weekendy – plan usługi aplikacji** |
| --- | --- |
| **Nazwa:** Profil w dni powszednie |**Nazwa:** Profil weekendowy |
| **Skaluj według:** Harmonogram i reguły wydajności |**Skaluj według:** Harmonogram i reguły wydajności |
| **Profil:** Dni powszednie |**Profil:** Weekend |
| **Typ:** Cyklu |**Typ:** Cyklu |
| **Zakres docelowy:** od 5 do 20 instancji |**Zakres docelowy:** od 3 do 10 wystąpień |
| **Dni:** Poniedziałek, Wtorek, Środa, Czwartek, Piątek |**Dni:** Sobota, Niedziela |
| **Czas rozpoczęcia:** 9:00 AM |**Czas rozpoczęcia:** 9:00 AM |
| **Strefa czasowa:** UTC-08 |**Strefa czasowa:** UTC-08 |
|  | |
| **Reguła skalowania automatycznego (skalowanie w górę)** |**Reguła skalowania automatycznego (skalowanie w górę)** |
| **Zasób:** Produkcja (środowisko usługi aplikacji) |**Zasób:** Produkcja (środowisko usługi aplikacji) |
| **Metryka:** % procesora |**Metryka:** % procesora |
| **Działanie:** Większa niż 60% |**Działanie:** Większa niż 80% |
| **Czas trwania:** 5 minut |**Czas trwania:** 10 minut |
| **Agregacja czasu:** Średnia |**Agregacja czasu:** Średnia |
| **Działanie:** Liczba zwiększona o 2 |**Działanie:** Liczba zwiększania o 1 |
| **Ochłodzenie (minuty):** 15 |**Ochłodzić się (minuty):** 20 |
|  | |
| **Reguła skalowania automatycznego (skalowanie w dół)** |**Reguła skalowania automatycznego (skalowanie w dół)** |
| **Zasób:** Produkcja (środowisko usługi aplikacji) |**Zasób:** Produkcja (środowisko usługi aplikacji) |
| **Metryka:** % procesora |**Metryka:** % procesora |
| **Działanie:** Mniej niż 30% |**Działanie:** Mniej niż 20% |
| **Czas trwania:** 10 minut |**Czas trwania:** 15 minut |
| **Agregacja czasu:** Średnia |**Agregacja czasu:** Średnia |
| **Działanie:** Zmniejsz liczbę o 1 |**Działanie:** Zmniejsz liczbę o 1 |
| **Ochłodzić się (minuty):** 20 |**Ochłodzić się (minuty):** 10 |

### <a name="app-service-plan-inflation-rate"></a>Stopa inflacji planu usługi App Service
Plany usługi App Service skonfigurowane do skalowania automatycznego robią to z maksymalną szybkością na godzinę. Szybkość ta może być obliczana na podstawie wartości podanych w regule skalowania automatycznego.

Zrozumienie i obliczanie *stopy inflacji planu usługi app service* jest ważne dla środowiska usługi App Service skalowania automatycznego, ponieważ zmiany skali do puli procesów roboczych nie są natychmiastowe.

Stopa inflacji planu usługi app service jest obliczana w następujący sposób:

![Obliczanie stopy inflacji planu usługi App Service.][ASP-Inflation]

Na podstawie reguły Skalowanie automatyczne — skalowanie w górę dla profilu w dni tygodnia produkcyjnego planu usługi app service:

![Usługa app service planuje stopę inflacji w dni powszednie na podstawie reguły skalowania automatycznego — skalowanie w górę.][Equation1]

W przypadku reguły Skalowanie automatyczne — skalowanie w górę dla profilu weekendowego produkcyjnego planu usługi app service formuła rozwiązałaby następujące rozwiązanie:

![Usługa app service planuje stopę inflacji w weekendy na podstawie reguły skalowania automatycznego — skalowanie w górę.][Equation2]

Tę wartość można również obliczyć dla operacji skalowania w dół.

Na podstawie reguły Skalowanie automatyczne — skalowanie w dół dla profilu w dni tygodnia produkcyjnego planu usługi app service, będzie wyglądać w następujący sposób:

![Usługa app service planuje stopę inflacji w dni powszednie na podstawie reguły skalowania automatycznego — skalowanie w dół.][Equation3]

W przypadku reguły Skalowanie automatyczne — skalowanie w dół dla profilu weekendowego produkcyjnego planu usługi app service formuła rozwiązałaby następujące rozwiązanie:  

![Usługa app service planuje stopę inflacji w weekendy na podstawie reguły skalowania automatycznego — skalowanie w dół.][Equation4]

Produkcyjny plan usługi app service może rosnąć z maksymalną szybkością ośmiu wystąpień/godzinę w ciągu tygodnia i czterech wystąpień/godzinę w weekend. Może zwolnić wystąpienia z maksymalną szybkością czterech wystąpień/godzinę w ciągu tygodnia i sześciu wystąpień/godzinę w weekendy.

Jeśli wiele planów usługi app service są hostowane w puli pracowników, należy obliczyć *całkowitą stopę inflacji* jako sumę stopy inflacji dla wszystkich planów usługi app service, które są hostowane w tej puli pracowników.

![Obliczanie całkowitej stopy inflacji dla wielu planów usługi App Service hostowanych w puli pracowników.][ASP-Total-Inflation]

### <a name="use-the-app-service-plan-inflation-rate-to-define-worker-pool-autoscale-rules"></a>Aby zdefiniować reguły skalowania automatycznego puli pracowników za pomocą stopy inflacji planu usługi app service
Pule procesów roboczych obsługujące plany usługi App Service skonfigurowane do skalowania automatycznego muszą być przydzielone bufor pojemności. Bufor umożliwia operacji skalowania automatycznego do wzrostu i zmniejszenia planu usługi app service w razie potrzeby. Minimalny bufor będzie obliczona całkowita stopa inflacji planu usługi app service.

Ponieważ operacje skalowania środowiska usługi App Service zająć trochę czasu, aby zastosować, wszelkie zmiany powinny uwzględniać dalsze zmiany popytu, które mogą się zdarzyć, gdy operacja skalowania jest w toku. Aby uwzględnić to opóźnienie, zaleca się użycie obliczonej stopy inflacji planu usługi aplikacji jako minimalnej liczby wystąpień, które są dodawane dla każdej operacji skalowania automatycznego.

Dzięki tym informacjom Frank może zdefiniować następujący profil i reguły skalowania automatycznego:

![Reguły profilu skalowania automatycznego dla przykładu LOB.][Worker-Pool-Scale]

| **Profil skalowania automatycznego — dni powszednie** | **Profil skalowania automatycznego – weekendy** |
| --- | --- |
| **Nazwa:** Profil w dni powszednie |**Nazwa:** Profil weekendowy |
| **Skaluj według:** Harmonogram i reguły wydajności |**Skaluj według:** Harmonogram i reguły wydajności |
| **Profil:** Dni powszednie |**Profil:** Weekend |
| **Typ:** Cyklu |**Typ:** Cyklu |
| **Zakres docelowy:** od 13 do 25 wystąpień |**Zakres docelowy:** od 6 do 15 wystąpień |
| **Dni:** Poniedziałek, Wtorek, Środa, Czwartek, Piątek |**Dni:** Sobota, Niedziela |
| **Czas rozpoczęcia:** 7:00 AM |**Czas rozpoczęcia:** 9:00 AM |
| **Strefa czasowa:** UTC-08 |**Strefa czasowa:** UTC-08 |
|  | |
| **Reguła skalowania automatycznego (skalowanie w górę)** |**Reguła skalowania automatycznego (skalowanie w górę)** |
| **Zasób:** Pula pracowników 1 |**Zasób:** Pula pracowników 1 |
| **Metryka:** PracownicyDostępni |**Metryka:** PracownicyDostępni |
| **Działanie:** Mniej niż 8 |**Działanie:** Mniej niż 3 |
| **Czas trwania:** 20 minut |**Czas trwania:** 30 minut |
| **Agregacja czasu:** Średnia |**Agregacja czasu:** Średnia |
| **Działanie:** Liczba zwiększonych o 8 |**Działanie:** Liczba zwiększona o 3 |
| **Ochłodzenie (minuty):** 180 |**Ochłodzenie (minuty):** 180 |
|  | |
| **Reguła skalowania automatycznego (skalowanie w dół)** |**Reguła skalowania automatycznego (skalowanie w dół)** |
| **Zasób:** Pula pracowników 1 |**Zasób:** Pula pracowników 1 |
| **Metryka:** PracownicyDostępni |**Metryka:** PracownicyDostępni |
| **Działanie:** Większa niż 8 |**Działanie:** Większa niż 3 |
| **Czas trwania:** 20 minut |**Czas trwania:** 15 minut |
| **Agregacja czasu:** Średnia |**Agregacja czasu:** Średnia |
| **Działanie:** Zmniejsz liczbę o 2 |**Działanie:** Zmniejsz liczbę o 3 |
| **Ochłodzenie (minuty):** 120 |**Ochłodzenie (minuty):** 120 |

Zakres docelowy zdefiniowany w profilu jest obliczany przez minimalne wystąpienia zdefiniowane w profilu planu usługi app service + buforu.

Maksymalny zakres będzie sumą wszystkich maksymalnych zakresów dla wszystkich planów usługi app service hostowanych w puli procesów roboczych.

Liczba podwyżek dla reguł skalowania w górę powinna być ustawiona na co najmniej 1X wskaźnik inflacji planu usługi app service dla skalowania w górę.

Liczba zmniejszeń można dostosować do poziomu między 1/2x lub 1X stopą inflacji planu usługi app service dla skalowania w dół.

### <a name="autoscale-for-front-end-pool"></a>Automatyczne skalowanie dla puli front-end
Reguły skalowania automatycznego front-end są prostsze niż w przypadku puli procesów roboczych. Przede wszystkim należy  
upewnij się, że czas trwania pomiaru i czasomierze czasu odnowienia uważają, że operacje skalowania w planie usługi app service nie są natychmiastowe.

W tym scenariuszu Frank wie, że poziom błędu wzrasta po front ends osiągnąć 80% wykorzystania procesora CPU i ustawia regułę skalowania automatycznego, aby zwiększyć wystąpień w następujący sposób:

![Ustawienia skalowania automatycznego dla puli front-end.][Front-End-Scale]

| **Profil skalowania automatycznego – przednie końce** |
| --- |
| **Nazwa:** Skalowanie automatyczne – przednie końce |
| **Skaluj według:** Harmonogram i reguły wydajności |
| **Profil:** Codziennego |
| **Typ:** Cyklu |
| **Zakres docelowy:** od 3 do 10 wystąpień |
| **Dni:** Codziennego |
| **Czas rozpoczęcia:** 9:00 AM |
| **Strefa czasowa:** UTC-08 |
|  |
| **Reguła skalowania automatycznego (skalowanie w górę)** |
| **Zasób:** Basen front-end |
| **Metryka:** % procesora |
| **Działanie:** Większa niż 60% |
| **Czas trwania:** 20 minut |
| **Agregacja czasu:** Średnia |
| **Działanie:** Liczba zwiększona o 3 |
| **Ochłodzenie (minuty):** 120 |
|  |
| **Reguła skalowania automatycznego (skalowanie w dół)** |
| **Zasób:** Pula pracowników 1 |
| **Metryka:** % procesora |
| **Działanie:** Mniej niż 30% |
| **Czas trwania:** 20 minut |
| **Agregacja czasu:** Średnia |
| **Działanie:** Zmniejsz liczbę o 3 |
| **Ochłodzenie (minuty):** 120 |

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
