---
title: Samouczek — przeglądanie użycia i kosztów w usłudze Azure Cost Management | Microsoft Docs
description: Ten samouczek przedstawia przeglądanie użycia i kosztów w celu śledzenia trendów, wykrywania niewydajności i tworzenia alertów.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/18/2018
ms.topic: tutorial
ms.service: cost-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 820fea1aa2eb93fb383dca4def9ed607515c29b8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
<!-- Intent: As a cloud-consuming user, I need to view usage and costs for my cloud resources and services.
-->

# <a name="tutorial-review-usage-and-costs"></a>Samouczek: przeglądanie użycia i kosztów

Usługa Azure Cost Management umożliwia przeglądanie użycia i kosztów w celu śledzenia trendów, wykrywania niewydajności i tworzenia alertów. Wszystkie dane użycia i kosztów są wyświetlane na pulpitach nawigacyjnych i w raportach usługi Cloudyn. Przykłady w tym samouczku ilustrują przeglądanie użycia i kosztów za pomocą pulpitów nawigacyjnych i raportów. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Śledzenie trendów użycia i kosztów
> * Wykrywanie przypadków niewydajnego użycia
> * Tworzenie alertów dotyczących nietypowych wydatków i przekraczania wydatków

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć konto platformy Azure.
- Musisz mieć rejestrację próbną lub płatną subskrypcję usługi Azure Cost Management.

## <a name="open-the-cloudyn-portal"></a>Otwieranie portalu Cloudyn

Wszystkie informacje o użyciu i kosztach można przeglądać w portalu Cloudyn. Otwórz portal Cloudyn z poziomu witryny Azure Portal lub przejdź na stronę https://azure.cloudyn.com i zaloguj się.

## <a name="track-usage-and-cost-trends"></a>Śledzenie trendów użycia i kosztów

Możesz śledzić rzeczywiste pieniądze wydawane na użycie i koszty za pomocą raportów Over Time (Na przestrzeni czasu), aby identyfikować trendy. Aby rozpocząć przyglądanie się trendom, skorzystaj z raportu Actual Cost Over Time (Koszt rzeczywisty na przestrzeni czasu). W menu w górnej części portalu kliknij pozycję **Cost** (Koszty) > **Cost Analysis** (Analiza kosztów) > **Actual Cost Over Time** (Koszt rzeczywisty na przestrzeni czasu). Przy pierwszym otwarciu raportu nie są do niego zastosowane żadne grupy ani filtry.

Oto przykład raportu:

![przykładowy raport](./media/tutorial-review-usage/actual-cost01.png)

W tym raporcie przedstawione są wszystkie wydatki w ciągu ostatnich 30 dni. Aby wyświetlić tylko wydatki na usługi platformy Azure, zastosuj grupę Service (Usługi), a następnie przefiltruj pod kątem wszystkich usług platformy Azure. Na poniższej ilustracji przedstawiono przefiltrowane usługi.

![przefiltrowane usługi](./media/tutorial-review-usage/actual-cost02.png)

W poprzednim przykładzie od dnia 2017-08-31 wydano mniej pieniędzy niż wcześniej. Ten trend kosztów jest kontynuowany dla różnych usług przez około dziewięć dni. Następnie ponownie pojawiają się dodatkowe wydatki. Jednak zbyt wiele kolumn może przesłonić oczywisty trend. Widok raportu można zmienić na wykres liniowy lub powierzchniowy, aby wyświetlić dane w innych widokach. Na poniższej ilustracji przedstawiono ten trend wyraźniej.

![trend w raporcie](./media/tutorial-review-usage/actual-cost03.png)

W tym przykładzie wyraźnie widać, że koszty usługi Azure Storage spadły od dnia 2017-08-31, podczas gdy wydatki związane z innymi usługami platformy Azure pozostały na tym samym poziomie. Co więc spowodowało obniżenie wydatków? W tym przykładzie niektórzy pracownicy byli na urlopie i nie używali usługi Storage.

Aby obejrzeć samouczek wideo dotyczący śledzenia trendów użycia i kosztów, zobacz [Analyzing your cloud billing data vs. time with Azure Cost Management (Analizowanie danych rozliczeń dla chmury na przestrzeni czasu przy użyciu usługi Azure Cost Management)](https://youtu.be/7LsVPHglM0g).

## <a name="detect-usage-inefficiencies"></a>Wykrywanie przypadków niewydajnego użycia

Raporty optymalizatora pozwalają zwiększyć wydajność, zoptymalizować użycie i zidentyfikować metody oszczędzania wydatków na zasoby w chmurze. Są one szczególnie przydatne w przypadku zaleceń dotyczących ekonomicznych rozmiarów mających pomóc w ograniczeniu używania bezczynnych lub kosztownych maszyn wirtualnych.

Powszechnym problemem, który wpływa na organizacje wstępnie przenoszące zasoby do chmury, jest ich strategia wirtualizacji. Często stosują one podejście podobne do tego, którego używały do tworzenia maszyn wirtualnych w lokalnych środowiskach wirtualizacji. Zakładają też, że koszty zostają obniżone przez przeniesienie lokalnych maszyn wirtualnych do chmury w postaci takiej, w jakiej są. Jednak takie podejście raczej nie zmniejsza kosztów.

Problem polega na tym, że za istniejącą infrastrukturę już zapłacono. Użytkownicy mogli, jeśli chcieli, tworzyć i pozostawiać uruchomione duże maszyny wirtualne — bezczynne lub nie — z niewielkimi negatywnymi konsekwencjami. Przeniesienie dużych lub bezczynnych maszyn wirtualnych do chmury prawdopodobnie *zwiększy* koszty. Przydzielanie kosztów dla zasobów jest ważne w przypadku zawierania umów z dostawcami usług w chmurze. Trzeba zapłacić za zatwierdzone zasoby niezależnie od tego, czy się je w pełni wykorzystało.

Raport Cost Effective Sizing Recommendations (Zalecenia dotyczące ekonomicznych rozmiarów) pozwala zidentyfikować potencjalne oszczędności roczne przez porównanie pojemności typów wystąpień maszyn wirtualnych z ich danymi historycznymi dotyczącymi użycia pamięci i procesorów.  

W menu w górnej części portalu kliknij pozycję **Optimizer** (Optymalizator) > **Sizing Optimization** (Optymalizacja rozmiarów) > **Cost Effective Sizing Recommendations** (Zalecenia dotyczące ekonomicznych rozmiarów). Odfiltruj dostawcę Azure, aby przejrzeć tylko maszyny wirtualne platformy Azure. Oto przykładowa ilustracja.

![Maszyny wirtualne platformy Azure](./media/tutorial-review-usage/sizing01.png)

W tym przykładzie można zaoszczędzić 3 114 USD, stosując się do zaleceń zmiany typów wystąpień maszyn wirtualnych. Kliknij symbol znaku plus (+) w obszarze **Details** (Szczegóły) dotyczący pierwszego zalecenia. Oto szczegółowe informacje dotyczące pierwszego zalecenia.

![szczegóły zalecenia](./media/tutorial-review-usage/sizing02.png)

Wyświetl identyfikatory wystąpień maszyn wirtualnych, klikając symbol znaku plus obok pozycji **List of Candidates** (Lista kandydatów).

![Lista kandydatów](./media/tutorial-review-usage/sizing03.png)

Aby obejrzeć samouczek wideo dotyczące wykrywania przypadków niewydajnego użycia, zobacz [Optimizing VM Size in Azure Cost Management (Optymalizowanie rozmiarów maszyn wirtualnych w usłudze Azure Cost Management)](https://youtu.be/1xaZBNmV704).

## <a name="create-alerts-for-unusual-spending"></a>Tworzenie alertów dotyczących nietypowych wydatków

Alerty automatycznie powiadamiają uczestników projektu o anomaliach wydatków i ryzykach związanych z nadmiarowymi wydatkami. Alerty można szybko i łatwo tworzyć za pomocą raportów obsługujących alerty oparte na budżecie i progach kosztów.

Alert dotyczący dowolnego wydatku można utworzyć za pomocą dowolnego raportu kosztów. W tym przykładzie skorzystasz z raportu Actual Cost Over Time (Koszt rzeczywisty na przestrzeni czasu), aby uzyskać powiadomienie, gdy wydatki na maszyny wirtualne platformy Azure zbliżą się do poziomu budżetu całkowitego. Wszystkie poniższe kroki są wymagane do utworzenia alertu. W menu w górnej części portalu kliknij pozycję **Cost** (Koszty) > **Cost Analysis** (Analiza kosztów) > **Actual Cost Over Time** (Koszt rzeczywisty na przestrzeni czasu). Ustaw pozycję **Groups** (Grupy) na **Service** (Usługa), a pozycję **Filter on the service** (Filtruj usługę) na **Azure/VM**. W prawym górnym rogu raportu kliknij pozycję **Actions** (Akcje), a następnie wybierz pozycję **Schedule report** (Zaplanuj raport).

Skorzystaj z karty **Scheduling** (Planowanie), aby wysyłać do siebie informacje z raportu w wiadomości e-mail z wybraną częstotliwością. Należy wybrać opcję **Send via email** (Wyślij w wiadomości e-mail). Wszystkie użyte tagi, grupowania i filtrowania będą uwzględnione w raporcie wysłanym w wiadomości e-mail. Kliknij kartę **Threshold** (Próg) i wybierz pozycję **Actual Cost vs. Threshold** (Koszt rzeczywisty a próg). Jeśli budżet całkowity wynosi 500 000 USD i chcesz, aby powiadomienie zostało wysłane, gdy koszty zbliżą się do jego połowy, utwórz alert **Red alert** (Czerwony alert) o wartości 250 000 USD i **Yellow alert** (Żółty alert) o wartości 240 000 USD. Nie uwzględniaj przecinków we wprowadzanych wartościach. Następnie wybierz liczbę kolejnych alertów. Po wysłaniu określonej całkowitej liczby alertów dodatkowe alerty nie są już wysyłane. Zapisz zaplanowany raport.

![przykładowy raport](./media/tutorial-review-usage/schedule-alert01.png)

Możesz również wybrać metrykę Cost Percentage vs. Budget threshold (Procent kosztów a próg budżetu), aby utworzyć alerty. W przypadku tej metryki można używać wartości procentowych zamiast walutowych budżetu.


## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Śledzenie trendów użycia i kosztów
> * Wykrywanie przypadków niewydajnego użycia
> * Tworzenie alertów dotyczących nietypowych wydatków i przekraczania wydatków


Przejdź do następnego samouczka, aby dowiedzieć się, jak prognozować wydatki przy użyciu danych historycznych.

> [!div class="nextstepaction"]
> [Prognozowanie wydatków w przyszłości](tutorial-forecast-spending.md)
