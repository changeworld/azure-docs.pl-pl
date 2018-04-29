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
ms.openlocfilehash: f25c39b602449be3ab9d1cd7e67d6fcfc78afb17
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="monitoring-usage-and-estimated-costs"></a>Monitorowanie użycia i szacowanych kosztów

W Centrum Monitor w portalu Azure **użycia i szacowane koszty** użycie podstawowej platformy monitorowania funkcji, takich jak wyjaśniono strony [alertów, metryki, powiadomienia](https://azure.microsoft.com/pricing/details/monitor/), [Analiza dzienników Azure ](https://azure.microsoft.com/pricing/details/log-analytics/), i [Azure Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). W przypadku klientów na planów cenowych dostępne przed 2018 kwietnia obejmuje to także zakupione w ramach szczegółowych danych użycia usługi Analiza dzienników i oferować Analytics.

Na tej stronie użytkownicy mogą wyświetlać ich użycia zasobów w ciągu ostatnich 31 dni, zagregowane dla subskrypcji. Przechodzenie do szczegółów dodatków Pokaż trendów użycia przez 31 dni. Dużą ilość danych musi grupuje dla ta szacowana, więc prosimy o cierpliwość jako ładowania strony.

W tym przykładzie przedstawiono sposób monitorowania użycia i oszacowanie kosztów wynikowy:

![Użycie i zrzut ekranu portalu szacowanych kosztów](./media/monitoring-usage-and-estimated-costs/001.png)

Wybierz łącze w kolumnie miesięczne użycie otworzyć wykres przedstawia trendy użycia przez ostatnich 31 dni:

![Uwzględnione na węzeł paska wykresu zrzut ekranu](./media/monitoring-usage-and-estimated-costs/002.png)

W tym miejscu jest inny podobny sposób użycia i podsumowanie kosztów. Ten przykład przedstawia subskrypcji w nowej 2018 kwietnia na podstawie zużycia cenową modelu. Zwróć uwagę na brak rozliczeniowych żadnych oparte na węzłach. Wprowadzanie danych i przechowywania dla analizy dzienników i usługi Application Insights, teraz są zgłaszane na nowy licznik wspólnej.

![Użycie i zrzut ekranu portalu szacowane koszty — cennik 2018 kwietnia](./media/monitoring-usage-and-estimated-costs/003.png)

## <a name="new-pricing-model"></a>Nowego modelu cenowego

W 2018 kwietnia nowego modelu cenowego monitorowania został wydany. Zawiera funkcje, przyjazny dla chmury, na podstawie zużycia cennik. Płacisz tylko za używane, bez zobowiązań oparte na węzłach. Szczegóły nowego modelu cenowego są dostępne dla [alertów, metryki, powiadomienia](https://azure.microsoft.com/pricing/details/monitor/), [analizy dzienników](https://azure.microsoft.com/pricing/details/log-analytics/), i [usługi Application Insights](https://azure.microsoft.com/pricing/details/application-insights/).

Dla klientów, którzy zacząć używać analizy dzienników lub usługi Application Insights po 2 kwietnia 2018 nowego modelu cenowego jest jedyną opcją. Klienci, którzy już korzystania z tych usług przenoszenia do nowego modelu cenowego jest opcjonalna.

## <a name="assessing-the-impact-of-the-new-pricing-model"></a>Ocena wpływu nowego modelu cenowego

Nowego modelu cenowego będzie mieć inny wpływ na każdego klienta na podstawie ich monitorowania wzorców użycia. Klienci, którzy korzystali z analizy dzienników lub usługi Application Insights przed 2 kwietnia 2018 **użycia i szacowany koszt** strony w monitorze Azure szacuje zmiany kosztów, jeśli zostały przeniesione do nowego modelu cenowego. Zapewnia sposób, aby przenieść subskrypcję do nowego modelu. Dla większości klientów nowego modelu cenowego będzie korzystne. W przypadku klientów z wzorców użycia szczególnie wysokiej danych lub w regionach wyższy koszt to nie być wielkość liter.

Aby wyświetlić oszacowanie kosztów dla subskrypcji wybrane na **użycia i szacowane koszty** wybierz niebieski banerze w górnej części strony. Najlepiej jest zrobić to jedną subskrypcję w czasie, ponieważ jest to poziom nowego modelu cenowego może zostać zaakceptowany.

![Monitorowanie użycia i szacowanych kosztów nowego zrzutu ekranu cenową modelu](./media/monitoring-usage-and-estimated-costs/004.png)

Nowa strona zawiera podobne wersji wcześniejszych strony z zielonym transparent:

![Monitorowanie użycia i szacowane koszty w bieżącym cenową zrzut ekranu modelu](./media/monitoring-usage-and-estimated-costs/005.png)

Strona zawiera również inny zestaw liczników, które odpowiadają nowego modelu cenowego. Ta lista jest przykładem:

- Szczegółowe dane i Analytics\Overage na węzeł
- Szczegółowe dane i Analytics\Included na węzeł
- Nadwyżkowe danych Insights\Basic aplikacji
- Dane Insights\Included aplikacji

Nowego modelu cenowego nie ma alokacji danych dołączone opartych na węźle. W związku z tym liczniki wprowadzanie te dane są łączone w nowy licznik wprowadzanie wspólnych danych o nazwie **udostępnionych wprowadzanie Services\Data**. 

Istnieje inna zmiana danych pozyskanych do analizy dzienników lub usługi Application Insights w regionach z wyższe koszty. Dane dotyczące tych regionów wysokich kosztów pojawi się nowych metod rozliczeń regionalne. Na przykład **wprowadzanie danych (nam zachodnie centralnej)**.

> [!NOTE]
> Na subskrypcję szacowane koszty nie uwzględnić poziomu kont na węzeł uprawnienia subskrypcji Operations Management Suite (OMS). Zapoznaj się z przedstawicielem konta bardziej szczegółowym omówieniem nowego modelu cenowego w takim przypadku.

## <a name="new-pricing-model-and-operations-management-suite-subscription-entitlements"></a>Cennik nowego modelu i uprawnienia do subskrypcji usługi Operations Management Suite

Klienci, którzy kupili E1 programu Microsoft Operations Management Suite i E2 na węzeł danych wprowadzanie uprawnień dla kwalifikują się do [analizy dzienników](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite) i [usługi Application Insights](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-pricing#the-price-plans). Aby otrzymywać te uprawnienia dla analizy dzienników obszarów roboczych lub usługi Application Insights zasobów w ramach danej subskrypcji, że subskrypcja na model cenowy muszą pozostać w modelu cenowego pre kwietnia 2018. To, gdzie są dostępne analizy dzienników "Na węzłami (OMS)" warstwa cenowa i usługi Application Insights "Przedsiębiorstwa" planów cenowych. W zależności od liczby węzłów pakietu nabytym przez organizację przeniesienie niektórych subskrypcji do nowego modelu cenowego nadal może być korzystne. Ale wymaga to kwestia zachować ostrożność.

## <a name="changes-when-youre-moving-to-the-new-pricing-model"></a>Zmiany w przypadku przenoszenia do nowego modelu cenowego

Przenoszenie subskrypcji do nowego modelu cenowego spowoduje zmianę warstwy cenowej dla każdego analizy dzienników na nową warstwę za GB, a zostanie przesunięty w dowolnym (o nazwie "pergb2018" w usłudze Azure Resource Manager). To przeniesienie spowoduje również zmianę żadnych zasobów usługi Application Insights w planie przedsiębiorstwa na podstawowy plan. Szacowanie kosztów pokazuje wpływu tych zmian.

## <a name="moving-to-the-new-pricing-model"></a>Przenoszenie do nowego modelu cenowego

Jeśli zdecydujesz się wdrożyć nowego modelu cenowego dla subskrypcji, wybierz **cennik wybór modelu** opcji w górnej części **użycia i szacowane koszty** strony:

![Monitorowanie użycia i szacowanych kosztów nowego zrzutu ekranu cenową modelu](./media/monitoring-usage-and-estimated-costs/006.png)

**Cennik wybór modelu** zostanie otwarta strona. Go zawiera listę poszczególnych subskrypcje, które są wyświetlane na stronie przed:

![Zrzut ekranu wyboru model cen](./media/monitoring-usage-and-estimated-costs/007.png)

Aby przenieść subskrypcję do nowego modelu cenowego, po prostu zaznacz pole wyboru, a następnie wybierz **zapisać**. Można cofnąć do starszych modelu cenowego w taki sam sposób. Należy pamiętać, że właściciel subskrypcji lub aby zmienić model cen są wymagane uprawnienia współautora.
