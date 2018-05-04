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
ms.openlocfilehash: 08991565d56ffbf7d798944f108a1b86e4463c58
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
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

W 2018 kwietnia [monitorowania nowego modelu cenowego został wydany](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/).  To funkcje przyjaznych dla chmury, na podstawie zużycia cennik. Płacisz tylko za używane, bez zobowiązań oparte na węzłach. Szczegóły nowego modelu cenowego są dostępne dla [alertów, metryki, powiadomienia](https://azure.microsoft.com/pricing/details/monitor/), [analizy dzienników](https://azure.microsoft.com/pricing/details/log-analytics/) i [usługi Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). 

Do dołączenia do klientów do analizy dzienników lub usługi Application Insights po 2 kwietnia 2018 nowego modelu cenowego jest jedyną opcją. Klienci, którzy już korzystania z tych usług przenoszenia do nowego modelu cenowego jest opcjonalna.

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

Klienci, którzy kupili E1 programu Microsoft Operations Management Suite i E2 kwalifikują się do każdego węzła danych wprowadzanie uprawnień dla [analizy dzienników](https://www.microsoft.com/cloud-platform/operations-management-suite) i [usługi Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-pricing#the-price-plans). Aby otrzymywać te uprawnienia dla obszarów roboczych usługi Analiza dzienników lub zasobów usługi Application Insights w ramach danej subskrypcji: 

- W modelu pre kwietnia 2018 musi pozostać modelu cenowego subskrypcji.
- Obszary robocze analizy dziennika należy za pomocą "Na węzła (OMS)" warstwy cenowej.
- Zasoby Insights aplikacji należy używać cenową planu "przedsiębiorstwa".

W zależności od liczby węzłów pakietu nabytym przez organizację przeniesienie niektórych subskrypcji do nowego modelu cenowego może być korzystne, ale wymaga dokładne brany pod uwagę. Ogólnie rzecz biorąc zaleca się po prostu pozostać w modelu 2018 kwietnia wstępnie zgodnie z powyższym opisem.

> [!WARNING]
> Jeśli dana organizacja nabyła pakiet Microsoft Operations Management Suite E1 i E2, jest zazwyczaj najlepiej trzymać subskrypcji w modelu cenowego 2018 kwietnia poprzedzającego utworzenie kopii zapasowej. 
>

## <a name="changes-when-youre-moving-to-the-new-pricing-model"></a>Zmiany w przypadku przenoszenia do nowego modelu cenowego

Nowego modelu cenowego upraszcza analizy dzienników i usługi Application Insights cennik opcje tylko na jednej warstwy (lub plan). Przenoszenie subskrypcji do nowej cenowa będzie modelu:

- Zmień warstwę cenową dla każdego analizy dzienników na nową warstwę-GB (nazywane "pergb2018" w usłudze Azure Resource Manager)
- Wszystkie zasoby usługi Application Insights w planie przedsiębiorstwa jest zmieniany na podstawowy plan.

Szacowanie kosztów pokazuje wpływu tych zmian.

> [!WARNING]
> Ważna uwaga do wdrożenia za pomocą usługi Azure Resource Manager lub programu PowerShell w tym miejscu [analizy dzienników](https://docs.microsoft.com/azure/log-analytics/log-analytics-template-workspace-configuration) lub [usługi Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-powershell) model cenowy zostały przeniesione do nowej subskrypcji. Jeśli określisz warstwy/planu cenowego innych niż "pergb2018" analizy dzienników lub "Basic" dla usługi Application Insights, a nie kończą się niepowodzeniem wdrożenia z powodu określania nieprawidłowy cen warstwy/plan go powiedzie się **, ale będzie używać tylko prawidłowe plan/warstwy cenowej**. 
>

## <a name="moving-to-the-new-pricing-model"></a>Przenoszenie do nowego modelu cenowego

Jeśli zdecydujesz się wdrożyć nowego modelu cenowego dla subskrypcji, wybierz **cennik wybór modelu** opcji w górnej części **użycia i szacowane koszty** strony:

![Monitorowanie użycia i szacowanych kosztów nowego zrzutu ekranu cenową modelu](./media/monitoring-usage-and-estimated-costs/006.png)

**Cennik wybór modelu** zostanie otwarta strona. Go zawiera listę poszczególnych subskrypcje, które są wyświetlane na stronie przed:

![Zrzut ekranu wyboru model cen](./media/monitoring-usage-and-estimated-costs/007.png)

Aby przenieść subskrypcję do nowego modelu cenowego, po prostu zaznacz pole wyboru, a następnie wybierz **zapisać**. Można cofnąć do starszych modelu cenowego w taki sam sposób. Należy pamiętać, że właściciel subskrypcji lub aby zmienić model cen są wymagane uprawnienia współautora.
