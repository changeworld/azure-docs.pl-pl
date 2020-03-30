---
title: Monitorowanie użycia i szacowanych kosztów w usłudze Azure Monitor
description: Omówienie procesu korzystania z usługi Azure Monitor i strony szacowanych kosztów
author: dalekoetke
services: azure-monitor
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: mbullwin
ms.reviewer: Dale.Koetke
ms.subservice: ''
ms.openlocfilehash: 22c7243cd966f458610c2abc67ed5540b37357b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658819"
---
# <a name="monitoring-usage-and-estimated-costs-in-azure-monitor"></a>Monitorowanie użycia i szacowanych kosztów w usłudze Azure Monitor

> [!NOTE]
> W tym artykule opisano sposób wyświetlania użycia i szacowanych kosztów w wielu funkcjach monitorowania platformy Azure. Powiązane artykuły dotyczące określonych składników usługi Azure Monitor obejmują:
> - [Zarządzanie użyciem i kosztami za pomocą dzienników usługi Azure Monitor](manage-cost-storage.md) opisuje sposób kontrolowania kosztów przez zmianę okresu przechowywania danych oraz analizowanie użycia danych i ostrzeganie o ich użyciu.
> - [Zarządzanie użyciem i kosztami usługi Application Insights](../../azure-monitor/app/pricing.md) opisuje sposób analizowania użycia danych w usłudze Application Insights.

## <a name="azure-monitor-pricing-model"></a>Model cenowy usługi Azure Monitor

Podstawowy model rozliczeń usługi Azure Monitor to cennik oparty na chmurze i oparty na zużyciu ("Płatność zgodnie z rzeczywistym użyciem"). Płaci się wyłącznie za użyte zasoby. Dostępne są szczegóły cenowe dotyczące [alertów, metryk, powiadomień,](https://azure.microsoft.com/pricing/details/monitor/) [analizy dzienników](https://azure.microsoft.com/pricing/details/log-analytics/) i [wglądu w aplikacje.](https://azure.microsoft.com/pricing/details/application-insights/) 

Oprócz modelu płatności zgodnie z rzeczywistym i rzeczywistym względem danych dziennika usługa Log Analytics ma rezerwacje pojemności, które umożliwiają zaoszczędzenie aż 25% w porównaniu z ceną płatności zgodnie z rzeczywistym i wyjazdem. Cena rezerwacji pojemności umożliwia zakup rezerwacji od 100 GB/dzień. Wszelkie użycie powyżej poziomu rezerwacji będzie rozliczane według stawki płatności zgodnie z rzeczywistym użyciem. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/monitor/) o cenach rezerwacji pojemności.

Niektórzy klienci będą mieli dostęp do [starszych warstw cen usługi Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers) i [starszej warstwy cenowej Usługi Enterprise Application Insights.](https://docs.microsoft.com/azure/azure-monitor/app/pricing#legacy-enterprise-per-node-pricing-tier) 

## <a name="understanding-your-azure-monitor-costs"></a>Opis kosztów usługi Azure Monitor

Istnieją dwie fazy zrozumienia kosztów. Pierwszym z nich jest, gdy rozważa azure monitor jako rozwiązanie do monitorowania. 

### <a name="estimating-the-costs-to-manage-your-environment"></a>Szacowanie kosztów zarządzania środowiskiem

Jeśli nie używasz jeszcze dzienników usługi Azure Monitor, możesz użyć [kalkulatora cen usługi Azure Monitor,](https://azure.microsoft.com/pricing/calculator/?service=monitor) aby oszacować koszt korzystania z usługi Azure Monitor. Zacznij od wprowadzenia "Azure Monitor" w polu wyszukiwania i kliknięcia powstałego kafelka usługi Azure Monitor. Przewiń stronę w dół do usługi Azure Monitor i wybierz jedną z opcji z listy rozwijanej Typ:

- Metryki zapytań i alertów  
- Log Analytics
- Application Insights

W każdym z nich kalkulator cen pomoże Ci oszacować prawdopodobne koszty na podstawie oczekiwanego wykorzystania.

Na przykład za pomocą usługi Log Analytics można wprowadzić liczbę maszyn wirtualnych i GB danych, które mają być zbierane z każdej maszyny Wirtualnej. Zazwyczaj 1 GB do 3 GB miesiąca danych jest pozyskiwania z typowej maszyny Wirtualnej platformy Azure. Jeśli już oceniasz dzienniki usługi Azure Monitor, możesz użyć statystyk danych z własnego środowiska. Poniżej opisano, jak określić [liczbę monitorowanych maszyn wirtualnych](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data) i [ilość danych, które pochłonął obszar roboczy.](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume)

Podobnie w przypadku usługi Application Insights, jeśli włączysz funkcję "Oszacuj ilość danych na podstawie działania aplikacji", można podać dane wejściowe dotyczące aplikacji (żądania miesięczne i widoki strony miesięcznie, w przypadku, gdy będziesz zbierać dane telemetryczne po stronie klienta), a następnie kalkulator powie ci mediana i 90 percentyl ilość danych zebranych przez podobne aplikacje. Te aplikacje obejmują zakres konfiguracji usługi Application Insights (np. niektóre mają domyślne próbkowanie, niektóre nie mają próbkowania itp.), więc nadal masz kontrolę, aby zmniejszyć ilość danych, które pochłoniesz znacznie poniżej poziomu mediany za pomocą próbkowania. Ale jest to punkt wyjścia, aby zrozumieć, co inni, podobni klienci widzą. [Dowiedz się więcej](https://docs.microsoft.com/azure/azure-monitor/app/pricing#estimating-the-costs-to-manage-your-application) o szacowaniu kosztów usługi Application Insights.

### <a name="understanding-your-usage-and-estimated-costs"></a>Opis użytkowania i szacowanych kosztów

Ważne jest, aby zrozumieć i śledzić użycie po użyciu usługi Azure Monitor, a istnieje bogaty zestaw narzędzi, aby to ułatwić. 

Platforma Azure oferuje wiele przydatnych funkcji w centrum [Azure Cost Management + Billing](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) Hub. Po otwarciu **usługi Azure Cost Management + Billing** Hub, kliknij zarządzanie **kosztami** i wybierz [zakres](https://docs.microsoft.com/azure/cost-management/understand-work-scopes) (zestaw zasobów do zbadania). 

Następnie, aby wyświetlić koszty usługi Azure Monitor w ciągu ostatnich 30 dni, kliknij kafelek **Koszty dzienne,** wybierz "Ostatnie 30 dni" w obszarze Względne daty i dodaj filtr, który wybiera nazwy usługi:

1. Azure Monitor
2. Application Insights
3. Log Analytics
4. Wgląd w dane i analizy

Powoduje to widok, taki jak:

![Zrzut ekranu przedstawiający usługę Azure Cost Management](./media/usage-estimated-costs/010.png)

W tym miejscu można wery drążyć z tego skumulowanego podsumowania kosztów, aby uzyskać dokładniejsze szczegóły w widoku "Koszt według zasobu". W bieżących warstwach cenowych dane dziennika platformy Azure są naliczane na ten sam zestaw liczników, niezależnie od tego, czy pochodzą z usługi Log Analytics lub usługi Application Insights. Aby oddzielić koszty od użycia usługi Log Analytics lub usługi Application Insights, można dodać filtr **typu zasobu**. Aby wyświetlić wszystkie koszty usługi Application Insights, przefiltruj typ zasobu na "microsoft.insights/components", a w przypadku kosztów usługi Log Analytics przefiltruj typ zasobu na "microsoft.operationalinsights/workspaces". 

Więcej szczegółów użycia jest dostępnych po [pobraniu użycia z witryny Azure portal](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal). W pobranym arkuszu kalkulacyjnym możesz zobaczyć użycie na zasób platformy Azure dziennie. W tym arkuszu kalkulacyjnym programu Excel użycie zasobów usługi Application Insights można znaleźć, filtrując najpierw kolumnę "Kategoria miernika", aby wyświetlić "Usługa Application Insights" i "Log Analytics", a następnie dodając filtr w kolumnie "Identyfikator wystąpienia", która jest "zawiera microsoft.insights/components".  Większość użycia usługi Application Insights jest raportowana na licznikach z kategorią mierników usługi Log Analytics, ponieważ istnieje pojedyncza wiązka danych zaplecza dzienników dla wszystkich składników usługi Azure Monitor.  Tylko zasoby usługi Application Insights dotyczące starszych warstw cenowych i wieloetapowych testów sieci Web są zgłaszane za pomocą kategorii mierników usługi Application Insights.  Użycie jest wyświetlane w kolumnie "Ilość zużyta", a jednostka dla każdego wpisu jest wyświetlana w kolumnie "Jednostka miary".  Więcej informacji można znaleźć, aby ułatwić [zrozumienie rachunku za pomocą platformy Microsoft Azure.](https://docs.microsoft.com/azure/billing/billing-understand-your-bill) 

> [!NOTE]
> Korzystanie z **zarządzania kosztami** w centrum **Azure Cost Management + Billing** hub jest preferowanym podejściem do szerokiego zrozumienia kosztów monitorowania.  Środowisko **Użycia i szacowanych kosztów** [dla usługi Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understand-your-usage-and-estimate-costs) i [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/pricing#understand-your-usage-and-estimate-costs) zapewnia szczegółowe informacje dla każdej z tych części usługi Azure Monitor.

Inną opcją wyświetlania użycia usługi Azure Monitor jest **użycie i szacowane koszty** strony w Monitor centrum. Pokazuje użycie podstawowych funkcji monitorowania, takich jak [alerty, metryki, powiadomienia,](https://azure.microsoft.com/pricing/details/monitor/) [usługa Azure Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/)i usługa Azure Application [Insights](https://azure.microsoft.com/pricing/details/application-insights/). W przypadku klientów korzystających z planów cenowych dostępnych przed kwietniem 2018 r. obejmuje to również użycie usługi Log Analytics zakupione za pośrednictwem oferty Statystyk i Analiz.

Na tej stronie użytkownicy mogą wyświetlać swoje użycie zasobów w ciągu ostatnich 31 dni, agregowane na subskrypcję. `Drill-ins`trendów użytkowania w okresie 31 dni. Wiele danych musi się zebrać dla tego oszacowania, więc prosimy o cierpliwość w miarę wczyty strony.

W tym przykładzie pokazano monitorowanie użycia i oszacowanie kosztów wynikających:

![Zrzut ekranu z portalu kosztów użycia i szacowanych kosztów](./media/usage-estimated-costs/001.png)

Wybierz łącze w kolumnie miesięcznego użycia, aby otworzyć wykres przedstawiający trendy użycia z ostatniego 31-dniowego okresu: 

![Zrzut ekranu przedstawiający wykres słupkowy w zestawieniu dla węzłów](./media/usage-estimated-costs/002.png)

## <a name="operations-management-suite-subscription-entitlements"></a>Uprawnienia do subskrypcji pakietu Operations Management Suite

Klienci, którzy zakupili pakiet Microsoft Operations Management Suite E1 i E2, mają prawo do pozyskiwania danych dla wielu węzłów w [usłudze Log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) i [usłudze Application Insights.](https://docs.microsoft.com/azure/application-insights/app-insights-pricing) Aby otrzymać te uprawnienia do obszarów roboczych usługi Log Analytics lub zasobów usługi Application Insights w danej subskrypcji: 

- Obszary robocze usługi Log Analytics powinny używać warstwy cenowej "Na węzeł (OMS)".
- Zasoby usługi Application Insights powinny używać warstwy cenowej "Enterprise".

W zależności od liczby węzłów pakietu zakupionego przez organizację, przeniesienie niektórych subskrypcji do warstwy cenowej Płatności zgodnie z rzeczywistym użyciem (na GB) może być korzystne, ale wymaga to starannego rozważenia.

> [!WARNING]
> Jeśli twoja organizacja ma aktualny pakiet Microsoft Operations Management Suite E1 i E2, zazwyczaj najlepiej jest zachować obszary robocze usługi Log Analytics w warstwie cenowej "Węzeł dla węzła (OMS)" i zasoby usługi Application Insights w warstwie cenowej "Przedsiębiorstwo". 
>
