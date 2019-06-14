---
title: Wdrażanie usługi internetowej Machine Learning Studio
titleSuffix: Azure Machine Learning Studio
description: Jak konwertowanie eksperymentu szkolenia na eksperyment predykcyjny, przygotowywania ich do wdrożenia, a następnie wdrożyć go jako usługi sieci web Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 01/06/2017
ms.openlocfilehash: 2ffc9055f23b8221a6f711f741b6146545ff0821
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60334032"
---
# <a name="deploy-an-azure-machine-learning-studio-web-service"></a>Wdrażanie usługi sieci web Azure Machine Learning Studio

Usługa Azure Machine Learning Studio umożliwia kompilowanie i testowanie analityczne rozwiązania do analizy predykcyjnej. Następnie można wdrożyć rozwiązanie jako usługę sieci web.

Usługi sieci web usługi Machine Learning Studio zapewniają interfejs między aplikacją a modelem oceniania przepływu pracy usługi Machine Learning Studio. Aplikacja zewnętrzna może komunikować się z modelem oceniania przepływu pracy usługi Machine Learning Studio w czasie rzeczywistym. Wywołanie usługi sieci web Machine Learning Studio zwraca wyniki prognozowania do aplikacji zewnętrznej. Wywołanie usługi sieci Web polega na przekazaniu klucza interfejsu API utworzonego podczas wdrażania tej usługi. Usługa sieci web Machine Learning Studio zależy od interfejsu REST — popularnej architektury w projektach programistycznych w sieci web.

Usługa Azure Machine Learning Studio udostępnia dwa typy usług sieci web:

* Usługa odpowiedzi na żądanie (RRS): O niskich opóźnieniach i wysoce skalowalna usługa, która ocenia rekord danych jednego.
* Usługa wykonywania wsadowego (BES): Asynchroniczna Usługa przeznaczona do oceniania partii rekordów danych.

Dane wejściowe dla usługi BES przypominają dane wejściowe używane przez usługę RRS. Główna różnica polega na tym, że usługa BES odczytuje blok rekordów z różnych źródeł, takich jak usługa Azure Blob Storage, usługa Azure Table Storage, usługa Azure SQL Database, usługa HDInsight (zapytanie Hive) i źródła HTTP.

Z wysokiego poziomu punktu widzenia model jest wdrażany w trzech krokach:

* **[Tworzenie eksperymentu szkolenia]**  — w programie Studio możesz uczenie i testowanie modelu analizy predykcyjnej przy użyciu danych szkoleniowych, które podasz, przy użyciu szerokiej gamy algorytmów uczenia maszynowego.
* **[Przekonwertuj go na eksperyment predykcyjny]**  — gdy modelu po zapoznaniu z istniejącymi danymi i możesz z niej korzystać, aby oceniać nowe dane, Przygotuj i usprawnić eksperymentu zapewniających prognozy.
* **Wdrażanie** go jako **[Nowa usługa sieci web]** lub **[Klasyczna usługa sieci web]** — Jeśli wdrażasz swoje eksperyment predykcyjny jako Usługa sieci web platformy Azure, użytkownicy mogą wysyłać dane do modelu i otrzymywać modelu prognozy.

## <a name="create-a-training-experiment"></a>Tworzenie eksperymentu szkolenia

Do nauczenia modelu analizy predykcyjnej, używasz usługi Azure Machine Learning Studio do tworzenia eksperymentu szkolenia gdzie obejmują różnych modułów do załadowania danych szkoleniowych, przygotowywania danych zgodnie z potrzebami, zastosuj algorytmów uczenia maszynowego i ocena wyników. Można wykonania iteracji eksperymentu, a następnie spróbuj algorytmów uczenia maszynowego różnych do porównywania i ocena wyników.

Proces tworzenia i zarządzania nimi eksperymenty szkolenia zostało omówione dokładniej w innym miejscu. Więcej informacji można znaleźć w tych artykułach:

* [Tworzenie prostego eksperymentu w usłudze Azure Machine Learning Studio](create-experiment.md)
* [Tworzenie rozwiązania predykcyjnego za pomocą usługi Azure Machine Learning Studio](tutorial-part1-credit-risk.md)
* [Importowanie danych szkoleniowych do usługi Azure Machine Learning Studio](import-data.md)
* [Zarządzanie iteracjami eksperymentów w usłudze Azure Machine Learning Studio](manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Konwertowanie eksperymentu szkolenia na eksperyment predykcyjny

Gdy już uczony model, możesz przystąpić do przekonwertowania eksperymentu szkolenia na eksperyment predykcyjny, aby oceniać nowe dane.

Dokonując przekonwertowania na eksperyment predykcyjny, otrzymujesz uczonego modelu gotowa do wdrożenia jako usługi internetowej przyznawania ocen. Użytkownicy usługi sieci web mogą przesyłać dane wejściowe do modelu i model zostanie odsyłania wyników przewidywań. Jak przekonwertować eksperyment predykcyjny, należy pamiętać, jak oczekujesz, że model ma być używany przez inne osoby.

Aby przekonwertować eksperymentu szkolenia na eksperyment predykcyjny, kliknij przycisk **Uruchom** w dolnej części obszaru roboczego eksperymentu, kliknij przycisk **ustawić usługę sieci Web**, a następnie wybierz **predykcyjne usługi sieci Web**.

![Konwertowanie na ocenianie eksperymentu](./media/publish-a-machine-learning-web-service/figure-1.png)

Aby uzyskać więcej informacji na temat sposobu wykonania tej konwersji, zobacz [jak przygotować modelu wdrożenia w usłudze Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md).

W poniższych krokach opisano wdrażanie eksperyment predykcyjny jako nową usługę sieci web. Można także wdrożyć eksperymentu jako klasyczna usługa sieci web.

## <a name="deploy-it-as-a-new-web-service"></a>Wdróż je jako nową usługę sieci web

Teraz, gdy został przygotowany eksperyment predykcyjny, możesz go wdrożyć jako nową usługę internetową platformy Azure (w oparciu o usługi Resource Manager). Przy użyciu usługi sieci web, użytkownicy mogą wysyłać dane do modelu i model zwróci jego prognozy.

Aby wdrożyć eksperyment predykcyjny, kliknij **Uruchom** w dolnej części obszaru roboczego eksperymentu. Po zakończeniu eksperymentu, kliknij przycisk **wdrażanie usługi sieci Web** i wybierz **wdrażanie usługi sieci Web [Nowy]** .  Zostanie otwarta strona wdrożenia w portalu usługi internetowej Machine Learning Studio.

> [!NOTE] 
> Aby wdrożyć nową usługę sieci web musi masz wystarczające uprawnienia w ramach subskrypcji, do której możesz wdrażanie usługi sieci web. Aby uzyskać więcej informacji, zobacz [Zarządzanie usługą sieci Web przy użyciu portalu usług sieci Web Azure Machine Learning](manage-new-webservice.md). 

### <a name="machine-learning-studio-web-service-portal-deploy-experiment-page"></a>Usługi Machine Learning Studio w sieci Web portalu wdrażanie eksperymentu strony

Na stronie wdrażanie eksperymentu wprowadź nazwę usługi sieci web.
Wybierz plan cenowy. Jeśli masz istniejącego planu taryfowego, możesz ją wybrać, w przeciwnym razie należy utworzyć nowy plan ceny dla usługi.

1. W **Plan cenowy** listę rozwijaną, wybierz istniejący plan lub **wybierz nowy plan** opcji.
2. W **Nazwa planu**, wpisz nazwę, która będzie identyfikowała planu na rachunku.
3. Wybierz jedną z **miesięcznego planu warstwy**. Plan domyślną warstwy planów domyślnym regionem i usługi sieci web jest wdrażane do tego regionu.

Kliknij przycisk **Wdróż** i **Szybki Start** zostanie otwarta strona usługi sieci web.

Strony Szybki Start usługi sieci web zawiera dostępu i wskazówki dotyczące typowych zadań, które należy wykonać po utworzeniu usługi sieci web. W tym miejscu można łatwo uzyskać dostęp zarówno strony testowej, jak i zużywania strony.

<!-- ![Deploy the web service](./media/publish-a-machine-learning-web-service/figure-2.png)-->

### <a name="test-your-new-web-service"></a>Testowanie nowej usługi sieci web

Aby przetestować nowe usługi sieci web, kliknij pozycję **przetestować usługę sieci web** w ramach typowych zadań. Na stronie testu można testować usługi sieci web jako usługę odpowiedzi na żądanie (RRS) lub usługę wykonywania wsadowego (BES).

Strona testowa RRS wyświetla dane wejściowe, dane wyjściowe i parametry globalne, zdefiniowane eksperymentu. Aby przetestować usługę sieci web, możesz ręcznie wprowadź odpowiednie wartości dla danych wejściowych lub podać rozdzielonych przecinkami (CSV) sformatowane pliku wartości zawierającą wartości testowe.

Aby przetestować przy użyciu rekordów zasobów, w trybie widoku listy, wprowadź odpowiednie wartości dla danych wejściowych, a następnie kliknij przycisk **testowania odpowiedź na żądanie**. Wyniki prognozy są wyświetlane w kolumnie wyników po lewej stronie.

![Wprowadź odpowiednie wartości, aby przetestować usługę sieci web](./media/publish-a-machine-learning-web-service/figure-5-test-request-response.png)

Aby przetestować usługi BES, **partii**. Na stronie testu usługi Batch kliknij przycisk Przeglądaj, w obszarze dane wejściowe, a następnie wybierz plik CSV zawierający przykładowe odpowiednie wartości. Jeśli nie masz plik CSV i tworzenia eksperymentu predykcyjnego przy użyciu usługi Machine Learning Studio, możesz pobrać zestaw danych do eksperymentu predykcyjnego i jej używać.

Aby pobrać zestaw danych, należy otworzyć usługi Machine Learning Studio. Otwórz eksperyment predykcyjny i kliknij prawym przyciskiem myszy dane wejściowe na potrzeby eksperymentu. Z menu kontekstowego wybierz **dataset** , a następnie wybierz **Pobierz**.

![Pobierz zestaw danych z obszar roboczy Studio](./media/publish-a-machine-learning-web-service/figure-7-mls-download.png)

Kliknij przycisk **testu**. Stan zadania wykonywania wsadowego są wyświetlane po prawej stronie w obszarze **zadania wsadowe testów**.

![Testowanie zadania wykonywania wsadowego za pomocą portalu usług sieci web](./media/publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/publish-a-machine-learning-web-service/figure-3.png)-->

Na **konfiguracji** strony, można zmienić opis tytuł, zaktualizować klucz konta magazynu i Włącz przykładowe dane do usługi sieci web.

![Konfiguruj usługę sieci web](./media/publish-a-machine-learning-web-service/figure-8-arm-configure.png)

### <a name="access-your-new-web-service"></a>Dostęp do nowej usługi sieci web

Po wdrożeniu usługi sieci web z usługi Machine Learning Studio możesz wysyłać dane do usługi i otrzymywać odpowiedzi programowo.

**Zużywania** strona zawiera wszystkie informacje, które są potrzebne do uzyskania dostępu do usługi sieci web. Na przykład klucz interfejsu API jest umożliwiający autoryzowanego dostępu do usługi.

Aby uzyskać więcej informacji na temat uzyskiwania dostępu do usługi sieci web Machine Learning Studio, zobacz [jak korzystanie z usługi Azure Machine Learning Studio w sieci Web](consume-web-services.md).

### <a name="manage-your-new-web-service"></a>Zarządzanie nowej usługi sieci web

Możesz zarządzać Twoim nowym portalu usług sieci Web programu Machine Learning Studio w postaci usługi sieci web. Z [głównej strony portalu](https://services.azureml-test.net/), kliknij przycisk **usług sieci Web**. Ze strony usługi sieci web można usunąć lub skopiować usługi. Monitorowanie określonej usługi, kliknij usługę, a następnie kliknij przycisk **pulpit nawigacyjny**. Aby monitorować zadania wsadowe związane z usługą sieci web, kliknij **dziennika żądań usługi Batch**.

### <a id="multi-region"></a> Wdrażanie nowej usługi sieci web do wielu regionów

Można łatwo wdrożyć nowej usługi sieci web do wielu regionów, bez konieczności używania wielu subskrypcje i obszary robocze.

Ceny są określone, region, dlatego należy zdefiniować planu rozliczeniowego dla każdego regionu, w której zostanie wdrożona usługa sieci web.

#### <a name="create-a-plan-in-another-region"></a>Tworzenie planu w innym regionie

1. Zaloguj się do [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/).
2. Kliknij przycisk **plany** opcji menu.
3. W przypadku planów za pośrednictwem strony widoku kliknij przycisk **New**.
4. Z **subskrypcji** listy rozwijanej wybierz subskrypcję, w którym będzie przechowywany nowy plan.
5. Z **Region** listę rozwijaną, wybierz region dla nowego planu. Zostaną wyświetlone opcje planu dla wybranego regionu **opcje planu** części strony.
6. Z **grupy zasobów** listy rozwijanej wybierz zasób grupy dla tego planu. Aby uzyskać więcej informacji na temat grup zasobów, zobacz [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).
7. W **Nazwa planu** wpisz nazwę planu.
8. W obszarze **opcje planu**, kliknij odpowiedni poziom rozliczeń dla nowego planu.
9. Kliknij pozycję **Utwórz**.

#### <a name="deploy-the-web-service-to-another-region"></a>Wdrażanie usługi sieci web do innego regionu

1. Na stronie usług sieci Web Microsoft Azure Machine Learning, kliknij **usług sieci Web** opcji menu.
2. Wybierz usługę sieci Web są wdrażane w nowym regionie.
3. Kliknij przycisk **kopiowania**.
4. W **nazwa usługi sieci Web**, wpisz nową nazwę dla usługi sieci web.
5. W **opisu usługi internetowej**, wpisz opis usługi sieci web.
6. Z **subskrypcji** listy rozwijanej wybierz subskrypcję, w której będą znajdować się nowej usługi sieci web.
7. Z **grupy zasobów** listy rozwijanej wybierz zasób grupy dla usługi sieci web. Aby uzyskać więcej informacji na temat grup zasobów, zobacz [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).
8. Z **Region** listę rozwijaną, wybierz region, w której ma zostać wdrożona usługa sieci web.
9. Z **konta magazynu** listę rozwijaną, wybierz magazyn konta, w którym będzie przechowywany usługi sieci web.
10. Z **Plan cenowy** listę rozwijaną, wybierz plan w regionie, który został wybrany w kroku 8.
11. Kliknij przycisk **kopiowania**.

## <a name="deploy-it-as-a-classic-web-service"></a>Wdróż je jako klasycznej usługi sieci web

Teraz, gdy został odpowiednio przygotowany eksperyment predykcyjny, możesz go wdrożyć jako usługę sieci web klasycznej platformie Azure. Przy użyciu usługi sieci web, użytkownicy mogą wysyłać dane do modelu i model zwróci jego prognozy.

Aby wdrożyć eksperyment predykcyjny, kliknij **Uruchom** w dolnej części eksperymentu kanwy, a następnie kliknij przycisk **wdrażanie usługi sieci Web**. Usługa sieci web jest skonfigurowany, i są umieszczane w pulpicie nawigacyjnym usługi sieci web.

![Wdrażanie usługi sieci web z programu Studio](./media/publish-a-machine-learning-web-service/figure-2.png)

### <a name="test-your-classic-web-service"></a>Testowanie klasyczne usługi sieci web

Możesz przetestować usługę sieci web w portalu usług sieci Web programu Machine Learning Studio lub w usłudze Machine Learning Studio.

Aby przetestować usługę sieci web w odpowiedzi na żądanie, kliknij pozycję **Test** przycisku w pulpicie nawigacyjnym usługi sieci web. Okno dialogowe pojawia się monit o podanie danych wejściowych dla usługi. Są to kolumn oczekiwany przez oceniania eksperymentu. Wprowadź zestawu danych, a następnie kliknij przycisk **OK**. Wyniki generowane przez usługę sieci web są wyświetlane w dolnej części pulpitu nawigacyjnego.

Możesz kliknąć pozycję **Test** Podgląd łącza do testowania usługi w portalu usług sieci Web programu Azure Machine Learning Studio, jak pokazano wcześniej w sekcji usługi sieci web.

Aby przetestować usługę wykonywania wsadowego, kliknij przycisk **Test** link (wersja zapoznawcza). Na stronie testu usługi Batch kliknij przycisk Przeglądaj, w obszarze dane wejściowe, a następnie wybierz plik CSV zawierający przykładowe odpowiednie wartości. Jeśli nie masz plik CSV i tworzenia eksperymentu predykcyjnego przy użyciu usługi Machine Learning Studio, możesz pobrać zestaw danych do eksperymentu predykcyjnego i jej używać.

![Test usługi sieci web](./media/publish-a-machine-learning-web-service/figure-3.png)

Na **konfiguracji** strony, można zmienić nazwy wyświetlanej usługi i nadaj jej opis. Nazwa i opis jest wyświetlany w [witryny Azure portal](https://portal.azure.com/) gdzie zarządzania usługami sieci web.

Możesz podać opis danych wejściowych, danych wyjściowych i sieci web parametry usługi, wprowadzając ciąg dla każdej kolumny w obszarze **schemat danych wejściowych**, **schemat danych wyjściowych**, i **usługi sieci Web PARAMETR**. Opisy te są używane w przykładowej dokumentacji kodu dostarczane przez usługę sieci web.

Można włączyć rejestrowanie, aby zdiagnozować wszelkie błędy, które występują podczas uzyskiwania dostępu do usługi sieci web. Aby uzyskać więcej informacji, zobacz [Włączanie rejestrowania usług sieci web Machine Learning Studio](web-services-logging.md).

![Włącz rejestrowanie w portalu usług sieci web](./media/publish-a-machine-learning-web-service/figure-4.png)

Można również skonfigurować punkty końcowe usługi sieci web, w portalu usług sieci Web Azure Machine Learning, podobnie jak wcześniej przedstawionych w sekcji usługi sieci web. Dostępne opcje zależą, można dodać lub zmienić opisu usługi, Włącz rejestrowanie i Włącz przykładowe dane do testowania.

### <a name="access-your-classic-web-service"></a>Dostęp do usługi sieci web klasyczny

Po wdrożeniu usługi sieci web z usługi Machine Learning Studio możesz wysyłać dane do usługi i otrzymywać odpowiedzi programowo.

Pulpit nawigacyjny zawiera wszystkie informacje potrzebne do uzyskania dostępu do usługi sieci web. Na przykład klucz interfejsu API jest udostępniany umożliwia autoryzowanego dostępu do usługi i stron pomocy interfejsu API są dostarczane w celu rozpoczęciem pisania kodu.

Aby uzyskać więcej informacji na temat uzyskiwania dostępu do usługi sieci web Machine Learning Studio, zobacz [jak korzystanie z usługi Azure Machine Learning Studio w sieci Web](consume-web-services.md).

### <a name="manage-your-classic-web-service"></a>Zarządzać z klasycznej usługi sieci web

Istnieją różne akcje możesz wykonać do monitorowania usługi sieci web. Można go zaktualizować i usuń go. Można również dodać dodatkowe punkty końcowe na klasycznej usługi sieci web, oprócz domyślnego punktu końcowego, który jest tworzony podczas jego wdrażania.

Aby uzyskać więcej informacji, zobacz [Zarządzanie obszarem roboczym usługi Azure Machine Learning Studio](manage-workspace.md) i [Zarządzanie usługą sieci web przy użyciu portalu usług sieci Web programu Azure Machine Learning Studio](manage-new-webservice.md).

## <a name="update-the-web-service"></a>Aktualizacja usługi sieci web
Można wprowadzić zmiany do usługi sieci web, takie jak aktualizowanie modelu danych dodatkowe szkolenie i wdrożyć ją ponownie, zastępując oryginalne usługi sieci web.

Aby zaktualizować usługę sieci web, otwórz oryginalnego eksperyment predykcyjny umożliwia wdrażanie usługi sieci web i wprowadź edytowalnej kopii, klikając **SAVE AS**. Wprowadź zmiany, a następnie kliknij przycisk **wdrażanie usługi sieci Web**.

Ponieważ wdrożono tego eksperymentu, zanim zostanie wyświetlony monit, czy chcesz zastąpić (klasycznej usługi sieci Web) lub zaktualizować istniejącą usługę (nowej usługi sieci web). Klikając **tak** lub **aktualizacji** zatrzymuje istniejącej usługi sieci web i wdraża nowy eksperyment predykcyjny jest wdrażana w tym miejscu.

> [!NOTE]
> Jeśli wprowadzono zmiany w konfiguracji oryginalnej usługi sieci web, na przykład, wprowadź nową nazwę wyświetlaną i opis, konieczne będzie ponowne wprowadzenie tych wartości.

Jedną z opcji aktualizowania usługi sieci web jest programowe ponowne trenowanie modelu. Aby uzyskać więcej informacji, zobacz [Retrain Machine Learning Studio modeli](/azure/machine-learning/studio/retrain-machine-learning-model).

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać więcej szczegółowych informacji technicznych na temat działania wdrożenia, zobacz [jak Machine Learning Studio modelu w miarę z eksperymentu do zoperacjonalizowanej usługi sieci Web](model-progression-experiment-to-web-service.md).

* Aby uzyskać więcej informacji na temat sposobu przygotowania do wdrożenia modelu, zobacz [jak przygotować modelu wdrożenia w usłudze Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md).

* Istnieje kilka sposobów uzyskiwania dostępu do usługi sieci Web za pomocą interfejsu API REST. Zobacz [sposobu korzystania z usługi sieci web Azure Machine Learning Studio](consume-web-services.md).

<!-- internal links -->
[Tworzenie eksperymentu szkolenia]: #create-a-training-experiment
[Przekonwertuj go na eksperyment predykcyjny]: #convert-the-training-experiment-to-a-predictive-experiment
[Nowa usługa sieci web]: #deploy-it-as-a-new-web-service
[Klasyczna usługa sieci web]: #deploy-it-as-a-classic-web-service
[Nowy]: #deploy-it-as-a-new-web-service
[classic]: #deploy-the-predictive-experiment-as-a-classic-web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service
