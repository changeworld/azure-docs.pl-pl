---
title: Samouczek — przeglądanie użycia i kosztów za pomocą rozwiązania Cloudyn na platformie Azure | Microsoft Docs
description: Ten samouczek przedstawia przeglądanie użycia i kosztów w celu śledzenia trendów, wykrywania niewydajności i tworzenia alertów.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/07/2018
ms.topic: tutorial
ms.service: cost-management
ms.custom: seodec18
manager: benshy
ms.openlocfilehash: 928b8fd8ef076afa2c60c870fb705a9a682003d1
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53093611"
---
<!-- Intent: As a cloud-consuming user, I need to view usage and costs for my cloud resources and services.
-->

# <a name="tutorial-review-usage-and-costs"></a>Samouczek: Przeglądanie użycia i kosztów

Rozwiązanie Cloudyn umożliwia przeglądanie użycia i kosztów w celu śledzenia trendów, wykrywania niewydajności i tworzenia alertów. Wszystkie dane użycia i kosztów są wyświetlane na pulpitach nawigacyjnych i w raportach usługi Cloudyn. Przykłady w tym samouczku ilustrują przeglądanie użycia i kosztów za pomocą pulpitów nawigacyjnych i raportów.

Usługa Azure Cost Management oferuje podobne funkcje jak rozwiązanie Cloudyn. Usługa Azure Cost Management to natywne rozwiązanie do zarządzania kosztami na platformie Azure. Ułatwia ona analizowanie kosztów, tworzenie budżetów i zarządzanie nimi, eksportowanie danych, a także zapoznawanie się z rekomendacjami dotyczącymi optymalizacji i ich wdrażanie w celu zaoszczędzenia pieniędzy. Aby uzyskać więcej informacji, zobacz [Azure Cost Management](overview-cost-mgt.md).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Śledzenie trendów użycia i kosztów
> * Wykrywanie przypadków niewydajnego użycia
> * Tworzenie alertów dotyczących nietypowych wydatków i przekraczania wydatków
> * Eksportowanie danych

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć konto platformy Azure.
- Musisz mieć rejestrację próbną lub płatną subskrypcję usługi Cloudyn.

## <a name="open-the-cloudyn-portal"></a>Otwieranie portalu Cloudyn

Wszystkie informacje o użyciu i kosztach można przeglądać w portalu Cloudyn. Otwórz portal Cloudyn z poziomu witryny Azure Portal lub przejdź na stronę https://azure.cloudyn.com i zaloguj się.

## <a name="track-usage-and-cost-trends"></a>Śledzenie trendów użycia i kosztów

Możesz śledzić rzeczywiste pieniądze wydawane na użycie i koszty za pomocą raportów Over Time (Na przestrzeni czasu), aby identyfikować trendy. Aby rozpocząć przyglądanie się trendom, skorzystaj z raportu Actual Cost Over Time (Koszt rzeczywisty na przestrzeni czasu). W lewym górnym rogu portalu kliknij pozycję **Cost** (Koszty) > **Cost Analysis** (Analiza kosztów) > **Actual Cost Over Time** (Koszt rzeczywisty na przestrzeni czasu). Przy pierwszym otwarciu raportu nie są do niego zastosowane żadne grupy ani filtry.

Oto przykład raportu:

![Przykładowy raport dotyczący rzeczywistego kosztu w czasie](./media/tutorial-review-usage/actual-cost01.png)

W tym raporcie przedstawione są wszystkie wydatki w ciągu ostatnich 30 dni. Aby wyświetlić tylko wydatki na usługi platformy Azure, zastosuj grupę Service (Usługi), a następnie przefiltruj pod kątem wszystkich usług platformy Azure. Na poniższej ilustracji przedstawiono przefiltrowane usługi.

![Przykład pokazujący przefiltrowane usługi platformy Azure](./media/tutorial-review-usage/actual-cost02.png)

W poprzednim przykładzie od dnia 2018-10-29 wydano mniej pieniędzy. Jednak zbyt wiele kolumn może przesłonić oczywisty trend. Widok raportu można zmienić na wykres liniowy lub powierzchniowy, aby wyświetlić dane w innych widokach. Na poniższej ilustracji przedstawiono ten trend wyraźniej.

![Przykład pokazujący trend spadających kosztów maszyny wirtualnej platformy Azure](./media/tutorial-review-usage/actual-cost03.png)

Kontynuując analizowanie tego przykładu, widać, że koszt maszyny wirtualnej platformy Azure spadł. Koszty związane z innymi usługami platformy Azure również zaczęły spadać tego dnia. Co więc spowodowało obniżenie wydatków? W tym przykładzie zakończono duży projekt roboczy, dlatego użycie wielu usług platformy Azure również spadło.

Aby obejrzeć samouczek wideo dotyczący śledzenia trendów użycia i kosztów, zobacz [Analyzing your cloud billing data vs. time with Cloudyn](https://youtu.be/7LsVPHglM0g) (Analizowanie danych rozliczeń dla chmury na przestrzeni czasu przy użyciu rozwiązania Cloudyn).

## <a name="detect-usage-inefficiencies"></a>Wykrywanie przypadków niewydajnego użycia

Raporty optymalizatora pozwalają zwiększyć wydajność, zoptymalizować użycie i zidentyfikować metody oszczędzania wydatków na zasoby w chmurze. Są one szczególnie przydatne w przypadku zaleceń dotyczących ekonomicznych rozmiarów mających pomóc w ograniczeniu używania bezczynnych lub kosztownych maszyn wirtualnych.

Powszechnym problemem, który wpływa na organizacje wstępnie przenoszące zasoby do chmury, jest ich strategia wirtualizacji. Często stosują one podejście podobne do tego, którego używały do tworzenia maszyn wirtualnych w lokalnych środowiskach wirtualizacji. Zakładają też, że koszty zostają obniżone przez przeniesienie lokalnych maszyn wirtualnych do chmury w postaci takiej, w jakiej są. Jednak takie podejście raczej nie zmniejsza kosztów.

Problem polega na tym, że za istniejącą infrastrukturę już zapłacono. Użytkownicy mogli, jeśli chcieli, tworzyć i pozostawiać uruchomione duże maszyny wirtualne — bezczynne lub nie — z niewielkimi negatywnymi konsekwencjami. Przeniesienie dużych lub bezczynnych maszyn wirtualnych do chmury prawdopodobnie *zwiększy* koszty. Przydzielanie kosztów dla zasobów jest ważne w przypadku zawierania umów z dostawcami usług w chmurze. Trzeba zapłacić za zatwierdzone zasoby niezależnie od tego, czy się je w pełni wykorzystało.

Raport Cost Effective Sizing Recommendations (Zalecenia dotyczące ekonomicznych rozmiarów) pozwala zidentyfikować potencjalne oszczędności roczne przez porównanie pojemności typów wystąpień maszyn wirtualnych z ich danymi historycznymi dotyczącymi użycia pamięci i procesorów.  

W menu w górnej części portalu kliknij pozycję **Optimizer** (Optymalizator) > **Sizing Optimization** (Optymalizacja rozmiarów) > **Cost Effective Sizing Recommendations** (Zalecenia dotyczące ekonomicznych rozmiarów). Jeśli jest to przydatne, należy zastosować filtr w celu ograniczenia wyników. Oto przykładowa ilustracja.

![Raport zawierający zalecenia dotyczące oszczędności związanych z rozmiarami maszyn wirtualnych platformy Azure](./media/tutorial-review-usage/sizing01.png)

W tym przykładzie można zaoszczędzić 2 382 USD, stosując się do zaleceń zmiany typów wystąpień maszyn wirtualnych. Kliknij symbol znaku plus (+) w obszarze **Details** (Szczegóły) dotyczący pierwszego zalecenia. Oto szczegółowe informacje dotyczące pierwszego zalecenia.

![Przykład pokazujący szczegóły zaleceń](./media/tutorial-review-usage/sizing02.png)

Wyświetl identyfikatory wystąpień maszyn wirtualnych, klikając symbol znaku plus obok pozycji **List of Candidates** (Lista kandydatów).

![Przykład pokazujący listę maszyn wirtualnych, których rozmiar można zmienić](./media/tutorial-review-usage/sizing03.png)

Aby obejrzeć samouczek wideo dotyczący wykrywania przypadków niewydajnego użycia, zobacz [Optimizing VM Size in Cloudyn ](https://youtu.be/1xaZBNmV704) (Optymalizowanie rozmiarów maszyn wirtualnych w rozwiązaniu Cloudyn).

Usługa Azure Cost Management udostępnia także zalecenia dotyczące oszczędności dla usług platformy Azure. Aby uzyskać więcej informacji, zobacz [Tutorial: Optimize costs from recommendations (Samouczek: Optymalizowanie kosztów na podstawie zaleceń)](tutorial-acm-opt-recommendations.md).

## <a name="create-alerts-for-unusual-spending"></a>Tworzenie alertów dotyczących nietypowych wydatków

Alerty automatycznie powiadamiają uczestników projektu o anomaliach wydatków i ryzykach związanych z nadmiarowymi wydatkami. Alerty można szybko i łatwo tworzyć za pomocą raportów obsługujących alerty oparte na budżecie i progach kosztów.

Alert dotyczący dowolnego wydatku można utworzyć za pomocą dowolnego raportu kosztów. W tym przykładzie skorzystasz z raportu Actual Cost Over Time (Koszt rzeczywisty na przestrzeni czasu), aby uzyskać powiadomienie, gdy wydatki na maszyny wirtualne platformy Azure zbliżą się do poziomu budżetu całkowitego. Wszystkie poniższe kroki są wymagane do utworzenia alertu. W menu w górnej części portalu kliknij pozycję **Cost** (Koszty) > **Cost Analysis** (Analiza kosztów) > **Actual Cost Over Time** (Koszt rzeczywisty na przestrzeni czasu). Ustaw pozycję **Groups** (Grupy) na **Service** (Usługa), a pozycję **Filter on the service** (Filtruj usługę) na **Azure/VM**. W prawym górnym rogu raportu kliknij pozycję **Actions** (Akcje), a następnie wybierz pozycję **Schedule report** (Zaplanuj raport).

W polu zapisywania lub planowania tego raportu skorzystaj z karty **Scheduling** (Planowanie), aby wysyłać do siebie informacje z raportu w wiadomości e-mail z wybraną częstotliwością. Należy wybrać opcję **Send via email** (Wyślij w wiadomości e-mail). Wszystkie użyte tagi, grupowania i filtrowania będą uwzględnione w raporcie wysłanym w wiadomości e-mail. Kliknij kartę **Threshold** (Próg) i wybierz pozycję **Actual Cost vs. Threshold** (Koszt rzeczywisty a próg). Jeśli budżet całkowity wynosi 20 000 USD i chcesz, aby powiadomienie zostało wysłane, gdy koszty zbliżą się do jego połowy, utwórz alert **Red alert** (Czerwony alert) o wartości 10 000 USD i **Yellow alert** (Żółty alert) o wartości 9 000 USD. Nie uwzględniaj przecinków we wprowadzanych wartościach. Następnie wybierz liczbę kolejnych alertów. Po wysłaniu określonej całkowitej liczby alertów dodatkowe alerty nie są już wysyłane. Zapisz zaplanowany raport.

![Przykład pokazujący czerwone i żółte alerty na podstawie progów wydatków](./media/tutorial-review-usage/schedule-alert01.png)

Możesz również wybrać metrykę Cost Percentage vs. Budget threshold (Procent kosztów a próg budżetu), aby utworzyć alerty. W przypadku tej metryki można używać wartości procentowych zamiast walutowych budżetu.

## <a name="export-data"></a>Eksportowanie danych

Podobnie do sposobu tworzenia alertów na potrzeby raportów możesz też wyeksportować dane z dowolnego raportu. Na przykład możesz wyeksportować listę kont Cloudyn lub innych danych użytkowników. Aby wyeksportować dowolny raport, otwórz raport, a następnie w prawym górnym rogu raportu kliknij opcję **Akcje**. Jedną z akcji, które możesz wykonać, jest opcja **Eksportuj wszystkie dane raportów**, która umożliwia pobranie lub wydrukowanie informacji. Możesz też wybrać opcję **Zaplanuj raport**, aby zaplanować raport i wysłać go jako wiadomość e-mail.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Śledzenie trendów użycia i kosztów
> * Wykrywanie przypadków niewydajnego użycia
> * Tworzenie alertów dotyczących nietypowych wydatków i przekraczania wydatków
> * Eksportowanie danych


Przejdź do następnego samouczka, aby dowiedzieć się, jak prognozować wydatki przy użyciu danych historycznych.

> [!div class="nextstepaction"]
> [Prognozowanie wydatków w przyszłości](tutorial-forecast-spending.md)
