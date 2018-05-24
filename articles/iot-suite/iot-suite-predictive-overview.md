---
title: Omówienie akceleratora rozwiązania do konserwacji predykcyjnej — Azure | Microsoft Docs
description: Opis akceleratora rozwiązania Azure do konserwacji predykcyjnej.
services: iot-suite
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: b370b3d7-2ce5-4906-9818-3aeedd471ee3
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2017
ms.author: dobett
ms.openlocfilehash: 4b1667b6449f9e425ea601f47f91bfe8cce8538e
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/18/2018
ms.locfileid: "34304211"
---
# <a name="predictive-maintenance-solution-accelerator-overview"></a>Omówienie akceleratora rozwiązania do konserwacji predykcyjnej

[Akcelerator rozwiązania] do *konserwacji predykcyjnej*[lnk_preconfigured_solutions] jest jednym z [akceleratorów rozwiązań Microsoft Azure IoT][lnk_iot_suite]. To rozwiązanie obejmuje zbieranie danych telemetrycznych z urządzeń w czasie rzeczywistym i model predykcyjny utworzony za pomocą usługi [Azure Machine Learning][lnk-machine-learning].

Akceleratory rozwiązań Azure IoT pozwalają szybko i łatwo łączyć się z zasobami i monitorować je oraz analizować dane telemetryczne w czasie rzeczywistym w ramach pulpitów nawigacyjnych i wizualizacji. W przypadku akceleratora rozwiązania do konserwacji predykcyjnej pulpity nawigacyjne i wizualizacje dostarczają nowe informacje analityczne, które pozwalają zwiększyć wydajność i wygenerować dodatkowe źródła przychodów.

## <a name="the-scenario"></a>Scenariusz

Fabrikam to regionalny przewoźnik lotniczy, ukierunkowany na zapewnienie doskonałej obsługi klientów przy zachowaniu konkurencyjnych cen. Jedną z przyczyn powodujących opóźnienia lotów są kwestie związane z obsługą techniczną samolotów. Dotyczy to w szczególności konserwacji silników. Firma Fabrikam musi za wszelką cenę zapobiegać awariom silników podczas lotu, zatem przeprowadza regularne przeglądy sprzętu i tworzy odpowiedni harmonogram konserwacji. Jednak występują różnice dotyczące stopnia zużycia silników samolotów. Zdarzają się przypadki wykonania prac konserwacyjnych, które nie były konieczne. Co więcej, pojawiają się problemy, które mogą prowadzić do uziemienia danego samolotu, dopóki nie zostanie przeprowadzona konserwacja. Te problemy powodują kosztowne opóźnienia, zwłaszcza jeśli samolot znajduje się w lokalizacji, w której nie są dostępne części zamienne lub odpowiednio wykwalifikowany personel.

Silniki samolotów linii Fabrikam są wyposażone w czujniki, które monitorują stan silnika podczas lotu. Firma Fabrikam korzysta z akceleratora rozwiązania do konserwacji predykcyjnej w celu gromadzenia danych zebranych z czujników podczas lotu. Zbierane przez całe lata dane dotyczące pracy i awarii silników umożliwiły inżynierom danych w firmie Fabrikam opracowanie modelu przewidywania pozostałego czasu eksploatacji silnika samolotu. Model korzysta z zależności między danymi pochodzącymi z czterech czujników w silniku a zużyciem silnika, które może prowadzić do wystąpienia awarii. Linie Fabrikam wciąż regularnie przeprowadzają przeglądy w celu zapewnienia bezpieczeństwa, ale dysponują również modelami, które umożliwiają obliczenie pozostałego czasu eksploatacji poszczególnych silników po każdym locie. Model wykorzystuje dane telemetryczne zebrane z silników podczas lotu. Pozwala to przewidywać przyszłe awarie i odpowiednio wcześniej zaplanować prace konserwacyjne i naprawcze.

> [!NOTE]
> W modelu rozwiązania wykorzystano dane dotyczące rzeczywistego zużycia silników.

Dzięki możliwości przewidywania terminu wymaganej obsługi technicznej firma Fabrikam może zoptymalizować swoje operacje, aby obniżyć koszty.

Współpraca koordynatorów ds. konserwacji z personelem odpowiedzialnym za rozkład lotów ma na celu:

- Opracowanie harmonogramu, który umożliwia przeprowadzanie prac technicznych podczas postoju samolotu w danej lokalizacji.
- Zapewnienie, że dostępna jest wystarczająca ilość czasu na serwisowanie samolotu, aby nie spowodować występowania zakłóceń w rozkładzie.
- Zaplanowanie pracy personelu technicznego tak, aby serwisowanie samolotów odbywało się bez przestojów.

Menedżerowie odpowiedzialni za magazyny podzespołów mają dostęp do planów konserwacji, dzięki czemu mogą zoptymalizować zapasy części zamiennych i proces ich zamawiania.

Te działania umożliwiają firmie Fabrikam zminimalizowanie czasu obsługi naziemnej samolotów i zmniejszenie kosztów operacyjnych przy jednoczesnym zapewnieniu bezpieczeństwa pasażerów i załóg.

Aby dowiedzieć się, jakie funkcje dostępne w [akceleratorach rozwiązań Azure IoT][lnk_iot_suite] umożliwiają klientom wykorzystanie potencjalnych możliwości konserwacji predykcyjnej, zapoznaj się z tą [grafiką informacyjną][lnk_infographic].

## <a name="how-the-predictive-maintenance-solution-accelerator-is-built"></a>Jak zbudowany jest akcelerator rozwiązania do konserwacji predykcyjnej

Rozwiązanie korzysta z istniejącego modelu usługi Azure Machine Learning dostępnego w postaci szablonu, który pozwala na demonstrację działania funkcji przy użyciu danych telemetrycznych pochodzących z urządzeń i zebranych za pomocą usług akceleratorów rozwiązań IoT. Firma Microsoft opracowała [model regresji][lnk_regression_model] silnika samolotu na podstawie publicznie dostępnych danych<sup>\[1\]</sup> oraz szczegółowe wskazówki dotyczące używania tego modelu.

Akcelerator rozwiązania Azure IoT do konserwacji predykcyjnej używa modelu regresji utworzonego na podstawie tego szablonu. Model jest wdrożony w ramach subskrypcji platformy Azure i dostępny za pośrednictwem automatycznie generowanego interfejsu API. Rozwiązanie to zawiera podzbiór danych testowych odpowiadających 4 ze 100 silników oraz strumieniom danych z 4 z 21 czujników. Te dane wystarczają do uzyskania dokładnego wyniku za pomocą nauczonego modelu.

*\[1\] A. Saxena and K. Goebel (2008). „Turbofan Engine Degradation Simulation Data Set” (Zestaw danych dotyczących symulacji degradacji silnika turbowentylatorowego), repozytorium danych prognostycznych NASA w Ames (http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/), ośrodek badawczy NASA w Ames, Moffett Field, Kalifornia*

## <a name="get-started-with-predictive-maintenance"></a>Rozpoczynanie pracy z konserwacja predykcyjną

W tym samouczku przedstawiono, jak wykonać aprowizację akceleratora rozwiązania do konserwacji predykcyjnej. Samouczek zawiera również opis podstawowych funkcji akceleratora rozwiązania do konserwacji predykcyjnej. Dostęp do wielu z tych funkcji można uzyskać za pośrednictwem pulpitu nawigacyjnego rozwiązania, który jest wdrażany razem z akceleratorem rozwiązania.

Do wykonania kroków tego samouczka jest potrzebna aktywna subskrypcja platformy Azure.

> [!NOTE]
> Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz artykuł [Bezpłatna wersja próbna platformy Azure][lnk_free_trial].

1. Zaloguj się w witrynie [azureiotsuite.com][lnk-azureiotsuite] przy użyciu poświadczeń konta platformy Azure i kliknij pozycję **+**, aby utworzyć rozwiązanie.
1. **Wybierz** kafelek **Konserwacja predykcyjna**.
1. W polu **Nazwa rozwiązania** wprowadź nazwę akceleratora rozwiązania do konserwacji predykcyjnej.
1. W polach **Region** i **Subskrypcja** wybierz wartości, których chcesz użyć do aprowizacji rozwiązania.
1. Kliknij pozycję **Utwórz rozwiązanie**, aby rozpocząć proces aprowizowania. Zwykle trwa on kilka minut.

### <a name="wait-for-the-provisioning-process-to-complete"></a>Oczekiwanie na ukończenie procesu aprowizowania

1. Kliknij kafelek swojego rozwiązania zawierający informację o stanie **aprowizacji**.
1. Zwróć uwagę na informację o **stanach aprowizacji** podczas wdrażania usług Azure w Twojej subskrypcji platformy Azure.
1. Po ukończeniu aprowizowania stan zmieni się na wartość **Gotowe**.
1. Kliknij kafelek, aby wyświetlić szczegóły rozwiązania w prawym okienku. Z poziomu tego okienka możesz uruchomić pulpit nawigacyjny rozwiązania i uzyskać dostęp do obszaru roboczego usługi Machine Learning.

> [!NOTE]
> Jeśli podczas wdrażania akceleratora rozwiązania pojawią się problemy, zapoznaj się z tematami [Uprawnienia w witrynie azureiotsuite.com][lnk-permissions] i [Często zadawane pytania][lnk-faq]. Jeśli problemy będą się powtarzać, utwórz żądanie pomocy w [portalu][lnk-portal].

Czy istnieją jakieś szczegóły dotyczące Twojego rozwiązania, które nie są wyświetlane, a Twoim zdaniem powinny być widoczne? Utwórz swoje propozycje dotyczące funkcji, korzystając ze strony [User Voice](https://feedback.azure.com/forums/321918-azure-iot) (Opinie użytkowników).

## <a name="view-the-solution"></a>Wyświetlanie rozwiązania

W tej sekcji opisano interfejs użytkownika rozwiązania.

### <a name="predictive-maintenance-dashboard"></a>Pulpit nawigacyjny konserwacji predykcyjnej

Na tej stronie aplikacji sieci Web są używane kontrolki JavaScript usługi Power BI (zobacz [repozytorium PowerBI-visuals][lnk-powerbi]), które umożliwiają wizualizację następujących elementów:

* Dane wyjściowe zadań usługi Stream Analytics przechowywane w magazynie obiektów blob.
* Liczba cykli i pozostały czas eksploatacji silnika.

### <a name="observing-the-behavior-of-the-cloud-solution"></a>Monitorowanie działania rozwiązania w chmurze

W portalu Azure przejdź do grupy zasobów z nazwą wybranego rozwiązania, aby wyświetlić aprowizowane zasoby.

![][img-resource-group]

Po przeprowadzeniu aprowizacji akceleratora rozwiązania otrzymasz wiadomość e-mail z linkiem do obszaru roboczego usługi Machine Learning. Do obszaru roboczego usługi Machine Learning można także przejść ze strony [azureiotsuite.com][lnk-azureiotsuite] zaprowizowanego rozwiązania. Kafelek jest dostępny na tej stronie, gdy rozwiązanie jest w stanie **Gotowe**.

![][img-machine-learning]

W portalu rozwiązania możesz zobaczyć, że do aprowizacji przykładu użyto czterech symulowanych urządzeń (dwa samoloty z dwoma silnikami każdy) i czterech czujników na silnik. Gdy po raz pierwszy przejdziesz do portalu rozwiązania, zobaczysz, że symulacja jest zatrzymana.

![][img-simulation-stopped]

Kliknij pozycję **Rozpocznij symulację**, aby uruchomić symulację. Na pulpicie nawigacyjnym zostanie wyświetlona liczba cykli, historia danych z czujników i pozostały czas eksploatacji wraz z historią.

![][img-simulation-running]

Jeśli wartość pozostałego czasu eksploatacji jest mniejsza niż 160 (arbitralna wartość progowa dla celów demonstracyjnych), w portalu rozwiązania zostanie wyświetlony symbol ostrzeżenia obok pozostałego czasu eksploatacji. Dodatkowo w portalu rozwiązania silnik samolotu zostanie wyróżniony kolorem żółtym. Zwróć uwagę na to, jak pozostały czas eksploatacji ma ogólną tendencję zniżkową ze skokami w górę i w dół. Takie zachowanie wynika z dokładności modelu i różnych czasów trwania cykli.

![][img-simulation-warning]

Pełna symulacja, obejmująca 148 cykli, trwa około 35 minut. Pozostały czas eksploatacji po raz pierwszy osiąga wartość progową równą 160 po upływie około 5 minut, a oba silniki osiągają wartości progowe po upływie około 8 minut.

W trakcie symulacji obejmującej 148 cykli jest przetwarzany pełny zestaw danych, co prowadzi do określenia końcowej liczby cykli i wartości pozostałego czasu eksploatacji.

Symulację można zatrzymać w dowolnym momencie, ale kliknięcie przycisku **Rozpocznij symulację** powoduje ponowne uruchomienie symulacji od początku zestawu danych.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o obsłudze scenariuszy konserwacji predykcyjnej w Pakiecie IoT Azure, zapoznaj się z dokumentem[Capture value from the Internet of Things][lnk_capture_value] (Korzyści z Internetu rzeczy).

Skorzystaj z [przewodnika][lnk-predictive-walkthrough] po akceleratorze rozwiązania do konserwacji predykcyjnej.

Możesz także wypróbować niektóre inne funkcje i możliwości akceleratorów rozwiązań IoT:

* [Często zadawane pytania dotyczące akceleratorów rozwiązań IoT][lnk-faq]
* [Zabezpieczenia IoT od podstaw][lnk-security-groundup]

[img-resource-group]: media/iot-suite-predictive-overview/resource-group.png
[img-simulation-stopped]: media/iot-suite-predictive-overview/simulation-stopped.png
[img-simulation-running]: media/iot-suite-predictive-overview/simulation-running.png
[img-simulation-warning]: media/iot-suite-predictive-overview/simulation-warning.png
[img-machine-learning]: media/iot-suite-predictive-overview/machine-learning.png

[lnk-powerbi]: https://www.github.com/Microsoft/PowerBI-visuals
[lnk-predictive-walkthrough]: iot-suite-predictive-walkthrough.md
[lnk_preconfigured_solutions]:../iot-accelerators/iot-accelerators-what-are-solution-accelerators.md
[lnk_iot_suite]: iot-suite-options.md
[lnk_infographic]: https://www.microsoft.com/server-cloud/predictivemaintenance/Index.html
[lnk_regression_model]: http://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3

[lnk_capture_value]: http://download.microsoft.com/download/0/7/D/07D394CE-185D-4B96-AC3C-9B61179F7080/Capture_value_from_the_Internet%20of%20Things_with_Predictive_Maintenance.PDF
[lnk-faq]: iot-suite-v1-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-permissions]: iot-suite-v1-permissions.md
[lnk-portal]: http://portal.azure.com/
[lnk-machine-learning]: https://azure.microsoft.com/services/machine-learning/