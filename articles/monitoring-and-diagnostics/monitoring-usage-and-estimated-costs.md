---
title: Monitorowanie użycia i szacowanych kosztów w monitorze Azure | Dokumentacja firmy Microsoft
description: Omówienie procesu przy użyciu strona szacowanych kosztów i monitorowanie Azure użycia
author: dalekoetke
manager: carmonmills
editor: mrbullwinkle
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/09/2018
ms.author: Dale.Koetke;mbullwin
ms.openlocfilehash: ce295c449b01de4fa99df9198805a6b0727c0d18
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="monitoring-usage-and-estimated-costs"></a>Monitorowanie użycia i szacowanych kosztów

W Centrum Monitor w portalu Azure **użycia i szacowane koszty** strony ułatwia perspektywy zrozumienia sposobów korzystania z podstawowej platformy monitorowania funkcji, takich jak [alertów, metryki, powiadomienia](https://azure.microsoft.com/pricing/details/monitor/), [Dziennika analizy](https://azure.microsoft.com/pricing/details/log-analytics/) i [usługi Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). W przypadku klientów na planów cenowych dostępne przed 2018 kwietnia obejmuje to także zakupione w ramach szczegółowych danych użycia usługi Analiza dzienników i oferować Analytics.

Na tej stronie użytkownicy mogą wyświetlać ich użycia tych zasobów w ciągu ostatnich 31 dni, zagregowane dla subskrypcji z Plug-in Przechodzenie do szczegółów, aby wyświetlić trend użycia w tym okresie. Brak dużą ilość danych niezbędnych do scalenia i wprowadzić ta szacowana, prosimy o cierpliwość jako ładowania strony.
Oto przykład przedstawiający sposób monitorowania użycia i oszacowanie kosztów wynikowy:

![Użycie i zrzut ekranu portalu szacowanych kosztów](./media/monitoring-usage-and-estimated-costs/001.png)

Kliknięcie w łącze w kolumnie miesięcznego użycia spowoduje otwarcie wykres przedstawiający trendów użycia przez ostatnich 31 dni:

![Uwzględnione na węzeł zrzut ekranu 671.47 GB](./media/monitoring-usage-and-estimated-costs/002.png)

W tym miejscu jest inny podobny sposób użycia i kosztów podsumowania, w tym przypadku w ramach subskrypcji w nowej 2018 kwietnia na podstawie zużycia model cen. Obserwować braku dowolnego rozliczania opartego na węźle i że wprowadzanie danych i przechowywania dla analizy dzienników i usługi Application Insights przekazywane są teraz nowe wspólne licznika.

![Użycie i zrzut ekranu portalu szacowanych kosztów](./media/monitoring-usage-and-estimated-costs/003.png)

## <a name="new-pricing-model"></a>Nowego modelu cenowego

W 2018 kwietnia nowego modelu cenowego monitorowania został wydany.  To funkcje przyjaznych dla chmury, na podstawie zużycia cennik. Płacisz tylko za używane, bez zobowiązań oparte na węzłach. Szczegóły nowego modelu cenowego są dostępne dla [alertów, metryki, powiadomienia](https://azure.microsoft.com/pricing/details/monitor/), [analizy dzienników](https://azure.microsoft.com/pricing/details/log-analytics/) i [usługi Application Insights](https://azure.microsoft.com/pricing/details/application-insights/).

Do dołączenia do klientów do analizy dzienników lub usługi Application Insights po 2 kwietnia 2018 nowego modelu cenowego jest jedyną opcją. Klienci, którzy już został przy użyciu tych usług przenoszenia do nowego modelu cenowego jest opcjonalna.

## <a name="assessing-the-impact-of-the-new-pricing-model"></a>Ocena wpływu nowego modelu cenowego

Nowego modelu cenowego będzie mieć wpływ różne dla każdego klienta na podstawie ich monitorowania wzorców użycia. Dla klientów korzystających z już analizy dzienników lub usługi Application Insights przed 2018 2 kwietnia **użycia i szacowany koszt** strony w monitorze Azure umożliwia oszacowania zmiany kosztów po przeniesieniu do nowego modelu cenowego oraz zapewnia sposób, aby przenieść subskrypcję do nowego modelu. Dla większości klientów nowego modelu cenowego będą korzystne, ale w przypadku klientów z wzorców użycia szczególnie wysokiej danych lub w regionach wyższy koszt, nie jest wielkość liter.

Aby wyświetlić oszacowanie kosztów dla subskrypcji wybrane na **użycia i szacowane koszty** niebieski banerze w górnej części strony kliknij pozycję. Najlepiej wykonaj to jedną subskrypcję w czasie, ponieważ jest poziomu nowego modelu cenowego może zostać zaakceptowany.

![Zrzut ekranu wybierz model cen](./media/monitoring-usage-and-estimated-costs/004.png)

Teraz zostanie wyświetlony podobne wersja tej strony z zielonym transparent:

![Zrzut ekranu wybierz model cen](./media/monitoring-usage-and-estimated-costs/005.png)

W tym miejscu zobaczysz inny zestaw liczników — liczniki, które odpowiadają nowego modelu cenowego. Na przykład dane liczników wprowadzanie takich jak

1. Szczegółowe dane i Analytics\Overage na węzeł
2. Szczegółowe dane i Analytics\Included na węzeł
3. Nadwyżkowe danych Insights\Basic aplikacji
4. Dane Insights\Included aplikacji

są łączone w nowy licznik wprowadzanie wspólnych danych o nazwie **udostępnionych wprowadzanie Services\Data** od nowego modelu cenowego nie ma uprawnienia do danych uwzględnione na węzeł.

Inna zmiana zobaczysz jest pozyskanych do analizy dzienników danych lub usługi Application Insights w regionach z wyższe koszty będą wyświetlane nowe liczniki regionalnych uwzględnienie prawidłowo, na przykład **"wprowadzanie danych (środkowe zachodnie stany USA)**.

> [!NOTE]
> Koszty szacowane na subskrypcji nie uwzględnić uprawnień do poziomu każdego węzła konta subskrypcji Operations Management Suite (OMS). Zapoznaj się z przedstawicielem konta bardziej szczegółowym omówieniem nowego modelu cenowego w takim przypadku.

## <a name="new-pricing-model-and-operations-management-suite-subscription-entitlements"></a>Cennik nowego modelu i uprawnienia do subskrypcji usługi Operations Management Suite

Klienci, którzy kupili E1 programu Microsoft Operations Management Suite i E2 kwalifikują się do każdego węzła danych wprowadzanie uprawnień dla [analizy dzienników](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite) i [usługi Application Insights](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-pricing#the-price-plans). Aby otrzymywać te uprawnienia dla analizy dzienników obszarów roboczych lub zasobów w ramach danej subskrypcji, że subskrypcja na model cenowy muszą pozostać w wersji pre kwietnia 2018 cenową usługi Application Insights modelu where analizy dzienników "na węzłami (OMS)" warstwy cenowej i Usługa Application Insights "Przedsiębiorstwa" cennik planu są dostępne. W zależności od liczby węzłów pakietu nabytym przez organizację przeniesienie niektórych subskrypcji do nowego modelu cenowego nadal może być korzystne, ale wymaga dokładne brany pod uwagę. 

## <a name="changes-when-moving-to-the-new-pricing-model"></a>Zmiany podczas przenoszenia do nowego modelu cenowego

Przenoszenie subskrypcji do nowego modelu cenowego spowoduje zmianę warstwy cenowej dla każdego analizy dzienników na nową warstwę za GB, a zostanie przesunięty w dowolnym (o nazwie "pergb2018" w usłudze Azure Resource Manager). To przeniesienie spowoduje również zmianę żadnych zasobów usługi Application Insights w planie przedsiębiorstwa na podstawowy plan. Efekty te są wyświetlane na szacowania kosztów opisane powyżej. 

## <a name="moving-to-the-new-pricing-model"></a>Przenoszenie do nowego modelu cenowego

Jeśli zdecydujesz się wdrożyć nowego modelu cenowego dla subskrypcji, kliknij przycisk **cennik wybór modelu** opcji w górnej części **użycia i szacowane koszty** strony:

![Monitorowanie użycia szacowanych kosztów nowego zrzutu ekranu cenową modelu](./media/monitoring-usage-and-estimated-costs/006.png)

Spowoduje to otwarcie **cennik wybór modelu** strony wyświetlania każdej subskrypcji była wyświetlana na stronie przed:

![Zrzut ekranu wyboru model cen](./media/monitoring-usage-and-estimated-costs/007.png)

Aby przenieść subskrypcję do nowego modelu cenowego, możesz po prostu zaznacz pole wyboru i kliknij przycisk **zapisać**.  Można cofnąć do starszych modelu cenowego w taki sam sposób. Należy pamiętać, że subskrypcja właścicielem lub współautorem wymagane są uprawnienia do zmiany modelu cenowego.
