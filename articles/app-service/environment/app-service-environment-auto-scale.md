---
title: Skalowanie automatyczne i środowisko App Service Environment v1 — platformy Azure
description: Skalowanie automatyczne i środowisko usługi App Service
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
ms.custom: seodec18
ms.openlocfilehash: 29a639142395c43fea06c1d6d18909b3c9f33b86
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60769502"
---
# <a name="autoscaling-and-app-service-environment-v1"></a>Skalowanie automatyczne i środowisko App Service Environment w wersji 1

> [!NOTE]
> Ten artykuł dotyczy środowiska App Service Environment w wersji 1.  Istnieje nowsza wersja usługi App Service Environment jest łatwiejszy w obsłudze, która działa na bardziej zaawansowanych infrastruktury. Aby dowiedzieć się więcej na temat nowej wersji rozpoczynać [wprowadzenie do usługi App Service Environment](intro.md).
> 

Obsługa środowiska Azure App Service Environment *skalowania automatycznego*. Możesz to zrobić skalowania automatycznego poszczególne pule procesów roboczych na podstawie metryki lub harmonogramu.

![Opcje automatycznego skalowania puli procesów roboczych.][intro]

Skalowanie automatyczne optymalizuje wykorzystanie zasobów przez automatycznie stałym wzbogacaniu i zmniejszanie środowiska usługi App Service w taki sposób, aby dopasować swój budżet i/lub Załaduj profil.

## <a name="configure-worker-pool-autoscale"></a>Konfigurowanie automatycznego skalowania puli procesów roboczych
Możesz uzyskać dostęp z funkcji skalowania automatycznego z **ustawienia** kartę puli procesów roboczych.

![Karta Ustawienia puli procesów roboczych.][settings-scale]

Z tego miejsca interfejsu powinny być dość dobrze, ponieważ jest taki sam sposób, zobacz w przypadku skalowania planu usługi App Service. 

![Skalowanie ręczne ustawienia.][scale-manual]

Można również skonfigurować profil skalowania automatycznego.

![Ustawienia automatycznego skalowania.][scale-profile]

Profile automatycznego skalowania są przydatne do ustawić limity skalowania. W ten sposób można mieć spójną wydajność środowiska, ustawiając wartość skali dolna granica (1) i limit wydatków przewidywalne, ustawiając górną granicę opóźnień (2).

![Ustawienia skalowania w profilu.][scale-profile2]

Po zdefiniowaniu profilu, możesz dodać reguły skalowania automatycznego skalowania w górę lub w dół liczbę wystąpień w puli procesów roboczych w granicach zdefiniowane przez profil. Reguły skalowania automatycznego są określane na podstawie.

![Reguły skalowania.][scale-rule]

 Dowolnej puli procesów roboczych lub metryk frontonu może służyć do zdefiniowania reguł skalowania automatycznego. Te metryki są te same metryki można monitorować na wykresach w bloku zasobów lub ustawić alerty dla.

## <a name="autoscale-example"></a>Przykład skalowania automatycznego
Funkcja automatycznego skalowania środowiska App Service najlepiej można zilustrowane Instruktaż scenariusza.

W tym artykule opisano wszystkie niezbędne zagadnienia podczas konfigurowania automatycznego skalowania. Artykuł przeprowadzi Cię przez interakcji, które dochodzą do głosu, gdy należy wziąć pod uwagę automatycznego skalowania środowiska App Service Environment, które są hostowane w środowisku App Service.

### <a name="scenario-introduction"></a>Wprowadzenie do scenariusza
Frank jest sysadmin w przedsiębiorstwie, który został zmigrowany część obciążeń, które zarządza on do środowiska usługi App Service.

Środowisko usługi App Service jest skonfigurowane pod kątem skalowanie ręczne w następujący sposób:

* **Frontony:** 3
* **Pula procesów roboczych 1**: 10
* **Pula procesów roboczych 2**: 5
* **Pula procesów roboczych 3**: 5

Pula procesów roboczych 1 jest używany w przypadku obciążeń produkcyjnych puli procesów roboczych 2 i 3. pula procesów roboczych, które są używane do zapewniania jakości (QA) i obciążeń deweloperskich.

Plany usługi App Service dla odpowiedzi na pytania i deweloperów są skonfigurowane do skalowanie ręczne. Plan usługi App Service w środowisku produkcyjnym ustawiono automatyczne skalowanie radzenia sobie z wahania obciążenia i ruch.

Frank jest bardzo podobnie do funkcji z aplikacją. Zna się, że godzinach szczytu obciążenia są między 9:00 i 18:00:00, ponieważ jest to aplikacja line-of-business (LOB), używanego przez pracowników, gdy znajdują się w biurze. Po spadku wykorzystania, gdy użytkownicy są wykonywane na ten dzień. Poza godzinami szczytu występuje nadal niektóre obciążenia, ponieważ użytkownicy mogą uzyskiwać dostęp aplikacji zdalnie za pomocą ich urządzeń przenośnych lub komputerów głównego. Plan usługi App Service w środowisku produkcyjnym jest już skonfigurowana do automatycznego skalowania na podstawie użycia procesora CPU z następującymi regułami:

![Określone ustawienia dla aplikacji biznesowych.][asp-scale]

| **Profil skalowania automatycznego — dni tygodnia — plan usługi App Service** | **Profil skalowania automatycznego — weekendy — plan usługi App Service** |
| --- | --- |
| **Nazwa:** Profil dzień tygodnia |**Nazwa:** Profil weekend |
| **Skalowanie przez:** Reguły harmonogramu i wydajności |**Skalowanie przez:** Reguły harmonogramu i wydajności |
| **Profil:** Dni tygodnia |**Profil:** Weekend |
| **Typ:** Cykl |**Typ:** Cykl |
| **Zakres docelowy:** 5 do 20 wystąpień |**Zakres docelowy:** 3 do 10 wystąpień |
| **Liczba dni:** Poniedziałek, Wtorek, Środa, czwartek, piątek |**Liczba dni:** SATURDAY lub Sunday |
| **Godzina rozpoczęcia:** 9:00 AM |**Godzina rozpoczęcia:** 9:00 AM |
| **Strefa czasowa:** UTC-08 |**Strefa czasowa:** UTC-08 |
|  | |
| **Reguły skalowania automatycznego (skalowanie w górę)** |**Reguły skalowania automatycznego (skalowanie w górę)** |
| **Zasób:** (Usługa App Service Environment) w środowisku produkcyjnym |**Zasób:** (Usługa App Service Environment) w środowisku produkcyjnym |
| **Metryka:** % procesora CPU |**Metryka:** % procesora CPU |
| **Operacja:** Przekracza 60% |**Operacja:** Większe niż 80% |
| **Czas trwania:** 5 minut |**Czas trwania:** 10 minut |
| **Agregacja czasu:** Średnia |**Agregacja czasu:** Średnia |
| **Akcja:** Zwiększ liczbę o 2 |**Akcja:** Zwiększ liczbę o 1 |
| **Czas ochładzania (minuty):** 15 |**Czas ochładzania (minuty):** 20 |
|  | |
| **Reguły skalowania automatycznego (skalowanie w dół)** |**Reguły skalowania automatycznego (skalowanie w dół)** |
| **Zasób:** (Usługa App Service Environment) w środowisku produkcyjnym |**Zasób:** (Usługa App Service Environment) w środowisku produkcyjnym |
| **Metryka:** % procesora CPU |**Metryka:** % procesora CPU |
| **Operacja:** Mniej niż 30% |**Operacja:** Mniej niż 20% |
| **Czas trwania:** 10 minut |**Czas trwania:** 15 minut |
| **Agregacja czasu:** Średnia |**Agregacja czasu:** Średnia |
| **Akcja:** Zmniejsz liczbę o 1 |**Akcja:** Zmniejsz liczbę o 1 |
| **Czas ochładzania (minuty):** 20 |**Czas ochładzania (minuty):** 10 |

### <a name="app-service-plan-inflation-rate"></a>Stawka inflacji planu usługi App Service
Plany usługi App Service, które są skonfigurowane do automatycznego skalowania to zrobić z maksymalną szybkością na godzinę. Tego kursu mogą być obliczane na podstawie wartości podanych dla reguły skalowania automatycznego.

Opis i obliczanie *stawka inflacji planu usługi App Service* jest ważne w przypadku automatycznego skalowania środowiska App Service, ponieważ zmiany skali w puli procesów roboczych są natychmiastowe.

Stawka inflacji planu usługi App Service jest obliczany w następujący sposób:

![Obliczanie współczynnik inflacji planu usługi App Service.][ASP-Inflation]

Oparte na automatyczne skalowanie — Skaluj w górę reguły odnoszące się do profilu dzień tygodnia, produkcji, plan usługi App Service:

![Usługa App Service kurs inflacji plan oparte na automatyczne skalowanie — reguła Skaluj w górę w dni robocze.][Equation1]

W przypadku skalowania automatycznego — reguła Skaluj w górę profilu Weekend produkcji planu usługi App Service, formuła może rozpoznać:

![Usługa App Service kurs inflacji plan weekendy oparte na automatyczne skalowanie — reguła Skaluj w górę.][Equation2]

Tę wartość można obliczyć, operacji skalowania w dół.

Oparte na automatyczne skalowanie — reguła skalowania w dół dla profilu Weekday produkcji planu usługi App Service, to będzie wyglądać w następujący sposób:

![Stawka inflacji planu usługi App Service dla dni tygodnia, automatyczne skalowanie — reguła skalowania w dół w oparciu.][Equation3]

W przypadku skalowania automatycznego — reguła skalowania w dół dla profilu Weekend produkcji planu usługi App Service, formuła może rozpoznać:  

![Usługa App Service kurs inflacji plan weekendy oparte na automatyczne skalowanie — reguła skalowania w dół.][Equation4]

Plan usługi App Service w środowisku produkcyjnym można rozwijać w maksymalnej wysokości osiem godzinę wystąpienia w tygodniu i cztery godzinę wystąpienia przez weekend. Wydających wystąpieniami maksymalnie cztery wystąpienia/godz. w tygodniu i sześć wystąpień/godz. podczas weekendów.

Jeśli wiele planów usługi App Service jest hostowany w puli procesów roboczych, musisz obliczyć *całkowitej szybkości inflacji* jako suma stawce inflacji dla wszystkich planów usługi App Service, które są w tej puli procesów roboczych.

![Obliczenia całkowitej szybkości inflacji dla wielu planów usługi App Service, hostowany w puli procesów roboczych.][ASP-Total-Inflation]

### <a name="use-the-app-service-plan-inflation-rate-to-define-worker-pool-autoscale-rules"></a>Użyj stawka inflacji planu usługi App Service do zdefiniowania reguł automatycznego skalowania puli procesów roboczych
Proces roboczy pul obsługujących planów usługi App Service, które są skonfigurowane do automatycznego skalowania, należy można przydzielić bufora pojemności. Bufor zezwala na powiększanie i zmniejszanie planu usługi App Service, odpowiednio do potrzeb operacji skalowania automatycznego. Minimalna buforu byłoby obliczeniowe całkowitej aplikacji Service Plan inflacji szybkości.

Ponieważ operacje skalowania środowiska App Service zająć dużo czasu, aby zastosować, wszelkie zmiany powinny uwzględniać dalszych zmian zapotrzebowania, które może się zdarzyć, gdy trwa operacja skalowania. Aby uwzględnić to opóźnienie, zaleca się użyć obliczeniowego całkowitej aplikacji Service Plan inflacji szybkości jako minimalna liczba wystąpień, które są dodawane dla każdej operacji skalowania automatycznego.

Dzięki tym informacjom Frank można zdefiniować następujące profilu skalowania automatycznego i reguł:

![Zasady profilu skalowania automatycznego na przykład LOB.][Worker-Pool-Scale]

| **Profil skalowania automatycznego — dni tygodnia** | **Profil skalowania automatycznego — weekendy** |
| --- | --- |
| **Nazwa:** Profil dzień tygodnia |**Nazwa:** Profil weekend |
| **Skalowanie przez:** Reguły harmonogramu i wydajności |**Skalowanie przez:** Reguły harmonogramu i wydajności |
| **Profil:** Dni tygodnia |**Profil:** Weekend |
| **Typ:** Cykl |**Typ:** Cykl |
| **Zakres docelowy:** 13-25 wystąpień |**Zakres docelowy:** wystąpienia 6-15 |
| **Liczba dni:** Poniedziałek, Wtorek, Środa, czwartek, piątek |**Liczba dni:** SATURDAY lub Sunday |
| **Godzina rozpoczęcia:** 7:00 AM |**Godzina rozpoczęcia:** 9:00 AM |
| **Strefa czasowa:** UTC-08 |**Strefa czasowa:** UTC-08 |
|  | |
| **Reguły skalowania automatycznego (skalowanie w górę)** |**Reguły skalowania automatycznego (skalowanie w górę)** |
| **Zasób:** Pula procesów roboczych 1 |**Zasób:** Pula procesów roboczych 1 |
| **Metryka:** WorkersAvailable |**Metryka:** WorkersAvailable |
| **Operacja:** Mniejsze niż 8 |**Operacja:** Mniej niż 3 |
| **Czas trwania:** 20 minut |**Czas trwania:** 30 minut |
| **Agregacja czasu:** Średnia |**Agregacja czasu:** Średnia |
| **Akcja:** Zwiększ liczbę o 8 |**Akcja:** Zwiększ liczbę o 3 |
| **Czas ochładzania (minuty):** 180 |**Czas ochładzania (minuty):** 180 |
|  | |
| **Reguły skalowania automatycznego (skalowanie w dół)** |**Reguły skalowania automatycznego (skalowanie w dół)** |
| **Zasób:** Pula procesów roboczych 1 |**Zasób:** Pula procesów roboczych 1 |
| **Metryka:** WorkersAvailable |**Metryka:** WorkersAvailable |
| **Operacja:** Większe niż 8 |**Operacja:** Większy niż 3 |
| **Czas trwania:** 20 minut |**Czas trwania:** 15 minut |
| **Agregacja czasu:** Średnia |**Agregacja czasu:** Średnia |
| **Akcja:** Zmniejsz liczbę o 2 |**Akcja:** Zmniejsz liczbę o 3 |
| **Czas ochładzania (minuty):** 120 |**Czas ochładzania (minuty):** 120 |

Zakres docelowy, zdefiniowaną w profilu jest obliczana przez minimalny wystąpień, zdefiniowaną w profilu dla planu usługi App Service + buforu.

Maksymalny zakres będzie sumę wszystkich maksymalna zakresy dla wszystkich planów usługi App Service, hostowany w puli procesów roboczych.

Zwiększ liczbę dla skalowania w górę reguły powinna być równa co najmniej 1 X stawka inflacji planu usługi aplikacji, dla skalowania w.

Zmniejsz liczbę można zmienić na coś między 1/2 X lub 1 X stawka inflacji planu usługi aplikacji, skalowanie w dół.

### <a name="autoscale-for-front-end-pool"></a>Funkcja automatycznego skalowania dla puli frontonu
Reguły automatycznego skalowania frontonu są prostsze niż w przypadku pul procesów roboczych. Przede wszystkim wykonaj następujące czynności  
Upewnij się, że czas trwania pomiaru i czasomierzy ochładzania należy wziąć pod uwagę operacje skalowania na plan usługi App Service nie są natychmiastowe.

W tym scenariuszu Frank wie, że współczynnik błędów wzrasta, gdy Frontony osiągnie 80% wykorzystania procesora CPU i Ustawia regułę skalowania automatycznego, aby zwiększyć wystąpień w następujący sposób:

![Ustawienia automatycznego skalowania dla puli frontonu.][Front-End-Scale]

| **Profil skalowania automatycznego — frontonów** |
| --- |
| **Nazwa:** Automatyczne skalowanie — frontonów |
| **Skalowanie przez:** Reguły harmonogramu i wydajności |
| **Profil:** codziennie |
| **Typ:** Cykl |
| **Zakres docelowy:** 3 do 10 wystąpień |
| **Liczba dni:** codziennie |
| **Godzina rozpoczęcia:** 9:00 AM |
| **Strefa czasowa:** UTC-08 |
|  |
| **Reguły skalowania automatycznego (skalowanie w górę)** |
| **Zasób:** Pula frontonu |
| **Metryka:** % procesora CPU |
| **Operacja:** Przekracza 60% |
| **Czas trwania:** 20 minut |
| **Agregacja czasu:** Średnia |
| **Akcja:** Zwiększ liczbę o 3 |
| **Czas ochładzania (minuty):** 120 |
|  |
| **Reguły skalowania automatycznego (skalowanie w dół)** |
| **Zasób:** Pula procesów roboczych 1 |
| **Metryka:** % procesora CPU |
| **Operacja:** Mniej niż 30% |
| **Czas trwania:** 20 minut |
| **Agregacja czasu:** Średnia |
| **Akcja:** Zmniejsz liczbę o 3 |
| **Czas ochładzania (minuty):** 120 |

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
