---
title: Wdrażanie usługi internetowej Machine Learning | Dokumentacja firmy Microsoft
description: Jak konwertowanie eksperymentu szkolenia na eksperyment predykcyjny, przygotowywania ich do wdrożenia, a następnie wdrożyć go jako usługi sieci web Azure Machine Learning.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.custom: (previous ms.author yahajiza)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: 73a3e9c6-00d0-41d4-8cf1-2ec87713867e
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.openlocfilehash: 36fcc7b5d1f5e67aee1bc285d12734e386b87d44
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/16/2018
ms.locfileid: "51820957"
---
# <a name="deploy-an-azure-machine-learning-web-service"></a>Wdrażanie usługi sieci Web Azure Machine Learning
Usługa Azure Machine Learning umożliwia tworzenie, testowanie i wdrażanie rozwiązań z zakresu analiz predykcyjnych.

Z wysokiego poziomu punktu widzenia można to zrobić w trzech krokach:

* **[Tworzenie eksperymentu szkolenia]**  -Azure Machine Learning Studio to środowisko współpracy programistyczne visual używanej do nauczenia i przetestowania modelu analizy predykcyjnej przy użyciu danych szkoleniowych, które podasz.
* **[Przekonwertuj go na eksperyment predykcyjny]**  — gdy modelu po zapoznaniu z istniejącymi danymi i możesz z niej korzystać, aby oceniać nowe dane, Przygotuj i usprawnić eksperymentu zapewniających prognozy.
* **[Go wdrożyć jako usługę sieci web]**  — można wdrożyć eksperyment predykcyjny jako [Nowy] lub [Model Klasyczny] usługi sieci web platformy Azure. Użytkownicy mogą wysyłać dane do modelu i otrzymują modelu prognozy.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="create-a-training-experiment"></a>Tworzenie eksperymentu szkolenia
Do nauczenia modelu analizy predykcyjnej, używasz usługi Azure Machine Learning Studio do tworzenia eksperymentu szkolenia gdzie obejmują różnych modułów do załadowania danych szkoleniowych, przygotowywania danych zgodnie z potrzebami, zastosuj algorytmów uczenia maszynowego i ocena wyników. Można wykonania iteracji eksperymentu, a następnie spróbuj algorytmów uczenia maszynowego różnych do porównywania i ocena wyników.

Proces tworzenia i zarządzania nimi eksperymenty szkolenia zostało omówione dokładniej w innym miejscu. Więcej informacji można znaleźć w tych artykułach:

* [Tworzenie prostego eksperymentu w usłudze Azure Machine Learning Studio](create-experiment.md)
* [Tworzenie rozwiązania predykcyjnego za pomocą usługi Azure Machine Learning](walkthrough-develop-predictive-solution.md)
* [Importowanie danych szkoleniowych do usługi Azure Machine Learning Studio](import-data.md)
* [Zarządzanie iteracjami eksperymentów w usłudze Azure Machine Learning Studio](manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Konwertowanie eksperymentu szkolenia na eksperyment predykcyjny
Gdy już uczony model, możesz przystąpić do przekonwertowania eksperymentu szkolenia na eksperyment predykcyjny, aby oceniać nowe dane.

Dokonując przekonwertowania na eksperyment predykcyjny, otrzymujesz uczonego modelu gotowa do wdrożenia jako usługi internetowej przyznawania ocen. Użytkownicy usługi sieci web mogą przesyłać dane wejściowe do modelu i model zostanie odsyłania wyników przewidywań. Jak przekonwertować eksperyment predykcyjny, należy pamiętać, jak oczekujesz, że model ma być używany przez inne osoby.

Aby przekonwertować eksperymentu szkolenia na eksperyment predykcyjny, kliknij przycisk **Uruchom** w dolnej części obszaru roboczego eksperymentu, kliknij przycisk **ustawić usługę sieci Web**, a następnie wybierz **predykcyjne usługi sieci Web**.

![Konwertowanie na ocenianie eksperymentu](./media/publish-a-machine-learning-web-service/figure-1.png)

Aby uzyskać więcej informacji na temat sposobu wykonania tej konwersji, zobacz [jak przygotować modelu wdrożenia w usłudze Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md).

W poniższych krokach opisano wdrażanie eksperyment predykcyjny jako nową usługę sieci web. Można także wdrożyć eksperymentu jako klasyczna usługa sieci web.

## <a name="deploy-it-as-a-web-service"></a>Go wdrożyć jako usługę sieci web

Możesz wdrożyć eksperyment predykcyjny jako nową usługę sieci web lub klasycznej usługi sieci web.

### <a name="deploy-the-predictive-experiment-as-a-new-web-service"></a>Wdrożyć eksperyment predykcyjny jako nową usługę sieci web
Teraz, gdy został przygotowany eksperyment predykcyjny, możesz go wdrożyć jako nową usługę sieci web platformy Azure. Przy użyciu usługi sieci web, użytkownicy mogą wysyłać dane do modelu i model zwróci jego prognozy.

Aby wdrożyć eksperyment predykcyjny, kliknij **Uruchom** w dolnej części obszaru roboczego eksperymentu. Po zakończeniu eksperymentu, kliknij przycisk **wdrażanie usługi sieci Web** i wybierz **wdrażanie usługi sieci Web [Nowy]**.  Zostanie otwarta strona wdrożenia w portalu usługi internetowej Machine Learning.

> [!NOTE] 
> Aby wdrożyć nową usługę sieci web musi masz wystarczające uprawnienia w ramach subskrypcji, do której możesz wdrażanie usługi sieci web. Aby uzyskać więcej informacji, zobacz [Zarządzanie usługą sieci Web przy użyciu portalu usług sieci Web Azure Machine Learning](manage-new-webservice.md). 

#### <a name="machine-learning-web-service-portal-deploy-experiment-page"></a>Usługi Machine Learning sieci Web portalu wdrażanie eksperymentu strony
Na stronie wdrażanie eksperymentu wprowadź nazwę usługi sieci web.
Wybierz plan cenowy. Jeśli masz istniejącego planu taryfowego, możesz ją wybrać, w przeciwnym razie należy utworzyć nowy plan ceny dla usługi.

1. W **Plan cenowy** listę rozwijaną, wybierz istniejący plan lub **wybierz nowy plan** opcji.
2. W **Nazwa planu**, wpisz nazwę, która będzie identyfikowała planu na rachunku.
3. Wybierz jedną z **miesięcznego planu warstwy**. Plan domyślną warstwy planów domyślnym regionem i usługi sieci web jest wdrażane do tego regionu.

Kliknij przycisk **Wdróż** i **Szybki Start** zostanie otwarta strona usługi sieci web.

Strony Szybki Start usługi sieci web zawiera dostępu i wskazówki dotyczące typowych zadań, które należy wykonać po utworzeniu usługi sieci web. W tym miejscu można łatwo uzyskać dostęp zarówno strony testowej, jak i zużywania strony.

<!-- ![Deploy the web service](./media/publish-a-machine-learning-web-service/figure-2.png)-->

#### <a name="test-your-new-web-service"></a>Testowanie nowej usługi sieci web
Aby przetestować nowe usługi sieci web, kliknij pozycję **przetestować usługę sieci web** w ramach typowych zadań. Na stronie testu można testować usługi sieci web jako usługę odpowiedzi na żądanie (RRS) lub usługę wykonywania wsadowego (BES).

Strona testowa RRS wyświetla dane wejściowe, dane wyjściowe i parametry globalne, zdefiniowane eksperymentu. Aby przetestować usługę sieci web, możesz ręcznie wprowadź odpowiednie wartości dla danych wejściowych lub podać rozdzielonych przecinkami (CSV) sformatowane pliku wartości zawierającą wartości testowe.

Aby przetestować przy użyciu rekordów zasobów, w trybie widoku listy, wprowadź odpowiednie wartości dla danych wejściowych, a następnie kliknij przycisk **testowania odpowiedź na żądanie**. Wyniki prognozy są wyświetlane w kolumnie wyników po lewej stronie.

![Wdrażanie usługi sieci Web](./media/publish-a-machine-learning-web-service/figure-5-test-request-response.png)

Aby przetestować usługi BES, **partii**. Na stronie testu usługi Batch kliknij przycisk Przeglądaj, w obszarze dane wejściowe, a następnie wybierz plik CSV zawierający przykładowe odpowiednie wartości. Jeśli nie masz plik CSV i tworzenia eksperymentu predykcyjnego przy użyciu usługi Machine Learning Studio, możesz pobrać zestaw danych do eksperymentu predykcyjnego i jej używać.

Aby pobrać zestaw danych, należy otworzyć usługi Machine Learning Studio. Otwórz eksperyment predykcyjny i kliknij prawym przyciskiem myszy dane wejściowe na potrzeby eksperymentu. Z menu kontekstowego wybierz **dataset** , a następnie wybierz **Pobierz**.

![Wdrażanie usługi sieci Web](./media/publish-a-machine-learning-web-service/figure-7-mls-download.png)

Kliknij przycisk **testu**. Stan zadania wykonywania wsadowego są wyświetlane po prawej stronie w obszarze **zadania wsadowe testów**.

![Wdrażanie usługi sieci Web](./media/publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/publish-a-machine-learning-web-service/figure-3.png)-->

Na **konfiguracji** strony, można zmienić opis tytuł, zaktualizować klucz konta magazynu i Włącz przykładowe dane do usługi sieci web.

![Konfiguruj usługę sieci web](./media/publish-a-machine-learning-web-service/figure-8-arm-configure.png)

Po wdrożeniu usługi sieci web, możesz wykonywać następujące czynności:

* **Dostęp** ją przy użyciu interfejsu API usługi sieci web.
* **Zarządzanie** ją przy użyciu portalu usług sieci web Azure Machine Learning.
* **Aktualizacja** go w przypadku zmiany modelu.

#### <a name="access-your-new-web-service"></a>Dostęp do nowej usługi sieci web
Po wdrożeniu usługi sieci web z usługi Machine Learning Studio możesz wysyłać dane do usługi i otrzymywać odpowiedzi programowo.

**Zużywania** strona zawiera wszystkie informacje, które są potrzebne do uzyskania dostępu do usługi sieci web. Na przykład klucz interfejsu API jest umożliwiający autoryzowanego dostępu do usługi.

Aby uzyskać więcej informacji na temat uzyskiwania dostępu do usługi sieci web Machine Learning, zobacz [jak korzystanie z usługi Azure Machine Learning w sieci Web](consume-web-services.md).

#### <a name="manage-your-new-web-service"></a>Zarządzanie nowej usługi sieci web
Możesz zarządzać Twoim nowym portalu usług sieci Web Machine Learning web services. Z [głównej strony portalu](https://services.azureml-test.net/), kliknij przycisk **usług sieci Web**. Ze strony usługi sieci web można usunąć lub skopiować usługi. Monitorowanie określonej usługi, kliknij usługę, a następnie kliknij przycisk **pulpit nawigacyjny**. Aby monitorować zadania wsadowe związane z usługą sieci web, kliknij **dziennika żądań usługi Batch**.

### <a name="deploy-the-predictive-experiment-as-a-classic-web-service"></a>Wdrożyć eksperyment predykcyjny jako klasycznej usługi sieci web

Teraz, gdy został odpowiednio przygotowany eksperyment predykcyjny, możesz go wdrożyć jako usługę sieci web klasycznej platformie Azure. Przy użyciu usługi sieci web, użytkownicy mogą wysyłać dane do modelu i model zwróci jego prognozy.

Aby wdrożyć eksperyment predykcyjny, kliknij **Uruchom** w dolnej części eksperymentu kanwy, a następnie kliknij przycisk **wdrażanie usługi sieci Web**. Usługa sieci web jest skonfigurowany, i są umieszczane w pulpicie nawigacyjnym usługi sieci web.

![Wdrażanie usługi sieci Web](./media/publish-a-machine-learning-web-service/figure-2.png)

#### <a name="test-your-classic-web-service"></a>Testowanie klasyczne usługi sieci web

Możesz przetestować usługę sieci web w portalu usług sieci Web Machine Learning lub w usłudze Machine Learning Studio.

Aby przetestować usługę sieci web w odpowiedzi na żądanie, kliknij pozycję **Test** przycisku w pulpicie nawigacyjnym usługi sieci web. Okno dialogowe pojawia się monit o podanie danych wejściowych dla usługi. Są to kolumn oczekiwany przez oceniania eksperymentu. Wprowadź zestawu danych, a następnie kliknij przycisk **OK**. Wyniki generowane przez usługę sieci web są wyświetlane w dolnej części pulpitu nawigacyjnego.

Możesz kliknąć pozycję **Test** Podgląd łącza do testowania usługi w portalu usług sieci Web Azure Machine Learning, jak pokazano wcześniej w sekcji usługi sieci web.

Aby przetestować usługę wykonywania wsadowego, kliknij przycisk **Test** link (wersja zapoznawcza). Na stronie testu usługi Batch kliknij przycisk Przeglądaj, w obszarze dane wejściowe, a następnie wybierz plik CSV zawierający przykładowe odpowiednie wartości. Jeśli nie masz plik CSV i tworzenia eksperymentu predykcyjnego przy użyciu usługi Machine Learning Studio, możesz pobrać zestaw danych do eksperymentu predykcyjnego i jej używać.

![Test usługi sieci web](./media/publish-a-machine-learning-web-service/figure-3.png)

Na **konfiguracji** strony, można zmienić nazwy wyświetlanej usługi i nadaj jej opis. Nazwa i opis jest wyświetlany w [witryny Azure portal](https://portal.azure.com/) gdzie zarządzania usługami sieci web.

Możesz podać opis danych wejściowych, danych wyjściowych i sieci web parametry usługi, wprowadzając ciąg dla każdej kolumny w obszarze **schemat danych wejściowych**, **schemat danych wyjściowych**, i **usługi sieci Web PARAMETR**. Opisy te są używane w przykładowej dokumentacji kodu dostarczane przez usługę sieci web.

Można włączyć rejestrowanie, aby zdiagnozować wszelkie błędy, które występują podczas uzyskiwania dostępu do usługi sieci web. Aby uzyskać więcej informacji, zobacz [Włączanie rejestrowania usług sieci web Machine Learning](web-services-logging.md).

![Konfiguruj usługę sieci web](./media/publish-a-machine-learning-web-service/figure-4.png)

Można również skonfigurować punkty końcowe usługi sieci web, w portalu usług sieci Web Azure Machine Learning, podobnie jak wcześniej przedstawionych w sekcji usługi sieci web. Dostępne opcje zależą, można dodać lub zmienić opisu usługi, Włącz rejestrowanie i Włącz przykładowe dane do testowania.

#### <a name="access-your-classic-web-service"></a>Dostęp do usługi sieci web klasyczny
Po wdrożeniu usługi sieci web z usługi Machine Learning Studio możesz wysyłać dane do usługi i otrzymywać odpowiedzi programowo.

Pulpit nawigacyjny zawiera wszystkie informacje potrzebne do uzyskania dostępu do usługi sieci web. Na przykład klucz interfejsu API jest udostępniany umożliwia autoryzowanego dostępu do usługi i stron pomocy interfejsu API są dostarczane w celu rozpoczęciem pisania kodu.

Aby uzyskać więcej informacji na temat uzyskiwania dostępu do usługi sieci web Machine Learning, zobacz [jak korzystanie z usługi Azure Machine Learning w sieci Web](consume-web-services.md).

#### <a name="manage-your-classic-web-service"></a>Zarządzać z klasycznej usługi sieci web
Istnieją różne akcje możesz wykonać do monitorowania usługi sieci web. Można go zaktualizować i usuń go. Można również dodać dodatkowe punkty końcowe na klasycznej usługi sieci web, oprócz domyślnego punktu końcowego, który jest tworzony podczas jego wdrażania.

Aby uzyskać więcej informacji, zobacz [Zarządzanie obszarem roboczym usługi Azure Machine Learning](manage-workspace.md) i [Zarządzanie usługą sieci web przy użyciu portalu usług sieci Web Azure Machine Learning](manage-new-webservice.md).

<!-- When this article gets published, fix the link and uncomment
For more information on how to manage Azure Machine Learning web service endpoints using the REST API, see **Azure machine learning web service endpoints**.
-->

## <a name="update-the-web-service"></a>Aktualizacja usługi sieci web
Można wprowadzić zmiany do usługi sieci web, takie jak aktualizowanie modelu danych dodatkowe szkolenie i wdrożyć ją ponownie, zastępując oryginalne usługi sieci web.

Aby zaktualizować usługę sieci web, otwórz oryginalnego eksperyment predykcyjny umożliwia wdrażanie usługi sieci web i wprowadź edytowalnej kopii, klikając **SAVE AS**. Wprowadź zmiany, a następnie kliknij przycisk **wdrażanie usługi sieci Web**.

Ponieważ wdrożono tego eksperymentu, zanim zostanie wyświetlony monit, czy chcesz zastąpić (klasycznej usługi sieci Web) lub zaktualizować istniejącą usługę (nowej usługi sieci web). Klikając **tak** lub **aktualizacji** zatrzymuje istniejącej usługi sieci web i wdraża nowy eksperyment predykcyjny jest wdrażana w tym miejscu.

> [!NOTE]
> Jeśli wprowadzono zmiany w konfiguracji oryginalnej usługi sieci web, na przykład, wprowadź nową nazwę wyświetlaną i opis, konieczne będzie ponowne wprowadzenie tych wartości.
> 
> 

Jedną z opcji aktualizowania usługi sieci web jest programowe ponowne trenowanie modelu. Aby uzyskać więcej informacji, zobacz temat [Retrain Machine Learning models programmatically](retrain-models-programmatically.md) (Ponowne trenowanie modeli uczenia maszynowego programowo).

<!-- internal links -->
[Tworzenie eksperymentu szkolenia]: #create-a-training-experiment
[Przekonwertuj go na eksperyment predykcyjny]: #convert-the-training-experiment-to-a-predictive-experiment
[Go wdrożyć jako usługę sieci web]: #deploy-it-as-a-web-service
[Nowy]: #deploy-the-predictive-experiment-as-a-new-web-service
[Model Klasyczny]: #deploy-the-predictive-experiment-as-a-classic-web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service
