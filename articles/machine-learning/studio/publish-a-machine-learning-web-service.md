---
title: Wdrażanie usługi sieci web usługi Machine Learning | Dokumentacja firmy Microsoft
description: Jak przekonwertować eksperyment uczenia eksperyment predykcyjny, przygotowania go do wdrożenia, a następnie wdrożyć go jako usługi sieci web uczenie maszynowe Azure.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 73a3e9c6-00d0-41d4-8cf1-2ec87713867e
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.openlocfilehash: 3ce4104040e90a4740442d7692b3bf4a0789bde0
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
ms.locfileid: "30185207"
---
# <a name="deploy-an-azure-machine-learning-web-service"></a>Wdrażanie usługi sieci Web Azure Machine Learning
Usługa Azure Machine Learning umożliwia tworzenia, testowania i wdrażania rozwiązań z zakresu analiz predykcyjnych.

Z wysokiego poziomu punktu widzenia można to zrobić w trzy kroki:

* **[Tworzenie eksperymentu szkolenia]**  -Azure Machine Learning Studio to środowisko visual programowanie zespołowe używanej do nauczenia i przetestowania modelu analizy predykcyjnej przy użyciu danych szkoleniowych, wprowadzona.
* **[Przekonwertuj go eksperyment predykcyjny]**  — po modelu po zapoznaniu z istniejącymi danymi i możesz go użyć do oceniać nowe dane, Przygotuj i usprawnić eksperymentu dla prognoz.
* **[Go wdrożyć jako usługę sieci web]**  — można wdrożyć predykcyjnej eksperymentu jako [Nowy] lub [Classic] usługi sieci web platformy Azure. Użytkownicy mogą wysyłać dane do modelu i odbierać do modelu prognozy.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="create-a-training-experiment"></a>Tworzenie eksperymentu szkolenia
Do uczenia modelu analizy predykcyjnej, umożliwia usłudze Azure Machine Learning Studio utworzyć eksperyment uczenia dodanie poszczególnych modułów, aby załadować dane szkoleniowe, przygotować dane w razie potrzeby Zastosuj algorytmów uczenia maszynowego i ocena wyników. Można iterację eksperymentu, a następnie spróbuj algorytmów uczenia maszynowego różnych porównać i ocena wyników.

Proces tworzenia i zarządzania nimi eksperymenty szkolenia zostało opisane bardziej dokładnie w innym miejscu. Więcej informacji można znaleźć w tych artykułach:

* [Tworzenie prostego eksperymentu w usłudze Azure Machine Learning Studio](create-experiment.md)
* [Tworzenie rozwiązania predykcyjnego przy użyciu usługi Azure Machine Learning](walkthrough-develop-predictive-solution.md)
* [Importowanie danych szkoleniowych w usłudze Azure Machine Learning Studio](import-data.md)
* [Zarządzanie iteracjami eksperymentów w usłudze Azure Machine Learning Studio](manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Konwertuj eksperyment uczenia eksperyment predykcyjny
Po gdy udało się nauczyć model, możesz przekonwertować eksperymentu uczenia eksperyment predykcyjny można oceniać nowe dane.

Konwertując eksperyment predykcyjny, otrzymujesz uczonego modelu gotowa do wdrożenia jako oceniania usługi sieci web. Użytkownicy usługi sieci web mogą przesyłać dane wejściowe do modelu i model zostanie odsyła wyniki prognozowania. Jak przekonwertować eksperyment predykcyjny, należy pamiętać, jak ma być modelu mają być używane przez innych użytkowników.

Aby przekonwertować eksperymentu uczenia eksperyment predykcyjny, kliknij przycisk **Uruchom** w dolnej części obszaru roboczego eksperymentu, kliknij przycisk **ustawić usługę sieci Web**, a następnie wybierz pozycję **predykcyjnej usługi sieci Web**.

![Konwertuj na oceniania eksperymentu](./media/publish-a-machine-learning-web-service/figure-1.png)

Aby uzyskać więcej informacji na temat sposobu wykonania konwersji, zobacz [jak przygotować modelu do wdrożenia w usłudze Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md).

W poniższych krokach opisano wdrażanie eksperyment predykcyjny jako nową usługę sieci web. Można także wdrożyć eksperymentu jako usługi sieci web klasycznego.

## <a name="deploy-it-as-a-web-service"></a>Go wdrożyć jako usługę sieci web

Eksperyment predykcyjny można wdrożyć jako nową usługę sieci web lub usługi sieci web klasycznego.

### <a name="deploy-the-predictive-experiment-as-a-new-web-service"></a>Wdróż eksperyment predykcyjny jako nową usługę sieci web
Teraz, eksperyment predykcyjny został przygotowany, można go wdrożyć jako nową usługę sieci web platformy Azure. Przy użyciu usługi sieci web, użytkownicy mogą wysyłać dane do modelu i model, którą będzie zwracać jej prognoz.

Aby wdrożyć predykcyjnej eksperymentu, kliknij przycisk **Uruchom** w dolnej części obszaru roboczego eksperymentu. NewPo skończeniu pracy eksperymentu kliknij **wdrażanie usługi sieci Web** i wybierz **wdrażanie usługi sieci Web [nowy]**.  Zostanie otwarta strona wdrożenia portalu Usługa sieci Web usługi Machine Learning.

> [!NOTE] 
> Aby wdrożyć nową usługę sieci web musi masz wystarczające uprawnienia do subskrypcji, do którego należy wdrożyć usługę sieci web. Aby uzyskać więcej informacji, zobacz [zarządzania usługi sieci Web przy użyciu portalu usługi sieci Web systemu Azure Machine Learning](manage-new-webservice.md). 

#### <a name="machine-learning-web-service-portal-deploy-experiment-page"></a>Wdróż eksperymentu strony portalu usługi sieci Web uczenie maszyny
Na stronie eksperymentu wdrażania wprowadź nazwę usługi sieci web.
Wybrać planu cenowego. Jeśli masz istniejące planu cenowego, że zostanie ona wybrana, w przeciwnym razie należy utworzyć nowy plan cen dla usługi.

1. W **planu cen** listy rozwijanej, wybierz istniejący plan lub **wybierz nowy plan** opcji.
2. W **Nazwa planu**, wpisz nazwę identyfikującą planu na rachunku.
3. Wybierz jedną z **miesięcznych poziomów Planowanie**. Plan domyślny warstw do planów dla regionu domyślnego i usługi sieci web jest wdrażany do tego regionu.

Kliknij przycisk **Wdróż** i **szybkiego startu** zostanie otwarta strona dla usługi sieci web.

Na stronie szybkiego startu usługi sieci web umożliwia dostęp i wskazówki na najbardziej typowych zadań, które ma zostać wykonane po utworzeniu usługi sieci web. W tym miejscu mogli łatwo uzyskiwać dostęp zarówno stronę testową i Consume strony.

<!-- ![Deploy the web service](./media/publish-a-machine-learning-web-service/figure-2.png)-->

#### <a name="test-your-new-web-service"></a>Testowanie nowej usługi sieci web
Aby przetestować nowej usługi sieci web, kliknij przycisk **testu sieci web usługi** w typowych zadań. Na stronie testowej można testować usługi sieci web jako usługa żądań i odpowiedzi (RR) lub usługę wykonywania wsadowego (BES).

Strona testowa rekordy zasobów wyświetla wejść, wyjść i globalne parametry, które zostały określone dla eksperymentu. Aby przetestować usługę sieci web, można ręcznie wprowadź odpowiednie wartości dla danych wejściowych lub podać rozdzielanych przecinkami (CSV) sformatowany pliku wartości zawierającą wartości testowe.

Aby przetestować go przy użyciu rekordów zasobów, trybu widoku listy, wprowadź odpowiednie wartości dla danych wejściowych, a następnie kliknij przycisk **Test żądanie-odpowiedź**. Wyświetl wyniki prognozowania w kolumnie wyników z lewej strony.

![Wdrażanie usługi sieci Web](./media/publish-a-machine-learning-web-service/figure-5-test-request-response.png)

Aby przetestować użytkownika BES, kliknij przycisk **partii**. Na stronie testowej partii kliknij przycisk Przeglądaj, w obszarze dane wejściowe i wybierz plik CSV zawierający przykładowe odpowiednie wartości. Jeśli nie ma pliku CSV, a utworzona predykcyjnej eksperymentu przy użyciu usługi Machine Learning Studio, można pobrać zestawu danych dla Twojego eksperyment predykcyjny i użyć go.

Aby pobrać zestaw danych, otwórz Machine Learning Studio. Otwórz predykcyjnej eksperymentu i kliknij prawym przyciskiem myszy dane wejściowe dla eksperymentu. Wybierz z menu kontekstowego **dataset** , a następnie wybierz **Pobierz**.

![Wdrażanie usługi sieci Web](./media/publish-a-machine-learning-web-service/figure-7-mls-download.png)

Kliknij przycisk **testu**. Wyświetla stan zadania wsadowe po prawej stronie w obszarze **zadań wsadowych testu**.

![Wdrażanie usługi sieci Web](./media/publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/publish-a-machine-learning-web-service/figure-3.png)-->

Na **konfiguracji** strony, można zmienić opisu, tytuł, Aktualizuj klucz konta magazynu i Włącz przykładowe dane do usługi sieci web.

![Konfiguruj usługę sieci web](./media/publish-a-machine-learning-web-service/figure-8-arm-configure.png)

Po wdrożeniu usługi sieci web, możesz:

* **Dostęp** ją przy użyciu interfejsu API usługi sieci web.
* **Zarządzanie** go za pośrednictwem portalu usługi sieci web uczenie maszynowe Azure.
* **Aktualizacja** go w przypadku zmiany modelu.

#### <a name="access-your-new-web-service"></a>Dostęp do nowej usługi sieci web
Po wdrożeniu usługi sieci web z usługi Machine Learning Studio można wysyłać dane do usługi i odbierania odpowiedzi programowo.

**Consume** zawiera wszystkie informacje wymagane do uzyskania dostępu do usługi sieci web. Na przykład klucz interfejsu API jest dostarczany autoryzowany dostęp do usługi.

Aby uzyskać więcej informacji na temat uzyskiwania dostępu do usługi sieci web uczenie maszynowe, zobacz [jak korzystać z usługi sieci Web Azure Machine Learning](consume-web-services.md).

#### <a name="manage-your-new-web-service"></a>Zarządzanie nowej usługi sieci web
Możesz zarządzać nowego portalu usługi sieci Web usługi Machine Learning usług sieci web. Z [strony głównej portalu](https://services.azureml-test.net/), kliknij przycisk **usług sieci Web**. Na stronie usługi sieci web można usunąć lub skopiuj usługi. Aby monitorować określonej usługi, kliknij usługę, a następnie kliknij przycisk **pulpitu nawigacyjnego**. Aby monitorować zadań wsadowych skojarzony z usługą sieci web, kliknij przycisk **dziennika żądania wsadowe**.

### <a name="deploy-the-predictive-experiment-as-a-classic-web-service"></a>Wdrażanie eksperyment predykcyjny jako usługę sieci web klasycznego

Teraz, eksperyment predykcyjny został odpowiednio przygotowany, można go wdrożyć jako usługę sieci web klasycznego Azure. Przy użyciu usługi sieci web, użytkownicy mogą wysyłać dane do modelu i model, którą będzie zwracać jej prognoz.

Aby wdrożyć predykcyjnej eksperymentu, kliknij przycisk **Uruchom** w dolnej części eksperyment obszaru roboczego, a następnie kliknij przycisk **wdrażanie usługi sieci Web**. Usługa sieci web jest skonfigurowana, a następnie są umieszczane w pulpicie nawigacyjnym usługi sieci web.

![Wdrażanie usługi sieci Web](./media/publish-a-machine-learning-web-service/figure-2.png)

#### <a name="test-your-classic-web-service"></a>Testowanie usługi sieci web klasycznego

Można przetestować usługę sieci web w portalu usługi sieci Web usługi Machine Learning lub Machine Learning Studio.

Aby przetestować odpowiedzi na żądanie usługi sieci web, kliknij przycisk **Test** przycisku na pulpicie nawigacyjnym usługi sieci web. Okno dialogowe wyskakującej się monit o podanie danych wejściowych dla usługi. To są kolumny oczekiwany przez oceniania eksperymentu. Wprowadź zestaw danych, a następnie kliknij przycisk **OK**. Wyniki generowane przez usługę sieci web są wyświetlane w dolnej części pulpitu nawigacyjnego.

Możesz kliknąć **Test** Podgląd łącza do testowania usługi w portalu usługi sieci Web systemu Azure Machine Learning, jak pokazano wcześniej w sekcji usługi sieci web.

Aby przetestować usługę wykonywania wsadowego, **Test** Podgląd łącza. Na stronie testowej partii kliknij przycisk Przeglądaj, w obszarze dane wejściowe i wybierz plik CSV zawierający przykładowe odpowiednie wartości. Jeśli nie ma pliku CSV, a utworzona predykcyjnej eksperymentu przy użyciu usługi Machine Learning Studio, można pobrać zestawu danych dla Twojego eksperyment predykcyjny i użyć go.

![Testowanie usługi sieci web](./media/publish-a-machine-learning-web-service/figure-3.png)

Na **konfiguracji** strony, można zmienić nazwę wyświetlaną usługi i nadaj mu opis. Nazwa i opis jest wyświetlany w [portalu Azure](https://portal.azure.com/) gdzie zarządzania usługami sieci web.

Można podać opis dla danych wejściowych, danych wyjściowych i sieci web parametry usługi wpisując ciąg dla każdej kolumny w obszarze **schemat danych wejściowych**, **SCHEMATEM WYJŚCIOWYM**, i **PARAMETR usługi sieci Web**. Opisy te są używane w przykładowy kod dokumentację dla usługi sieci web.

Można włączyć rejestrowanie, aby zdiagnozować wszelkie błędy, które jest wyświetlane podczas uzyskiwania dostępu do usługi sieci web. Aby uzyskać więcej informacji, zobacz [należy włączyć rejestrowanie dla usługi sieci web uczenie maszynowe](web-services-logging.md).

![Konfiguruj usługę sieci web](./media/publish-a-machine-learning-web-service/figure-4.png)

Można również skonfigurować punktów końcowych usługi sieci web w portalu usługi sieci Web systemu Azure Machine Learning podobne do procedury przedstawione wcześniej w sekcji usługi sieci web. Opcje są różne, można dodać lub zmienić opisu usługi, Włącz rejestrowanie i Włącz przykładowe dane do testowania.

#### <a name="access-your-classic-web-service"></a>Dostęp do usługi sieci web klasycznego
Po wdrożeniu usługi sieci web z usługi Machine Learning Studio można wysyłać dane do usługi i odbierania odpowiedzi programowo.

Pulpit nawigacyjny zawiera wszystkie informacje potrzebne do uzyskania dostępu do usługi sieci web. Na przykład klucz interfejsu API jest dostarczany autoryzowany dostęp do usługi i strony pomocy interfejsu API są dostarczane w celu rozpocząć pisanie kodu.

Aby uzyskać więcej informacji na temat uzyskiwania dostępu do usługi sieci web uczenie maszynowe, zobacz [jak korzystać z usługi sieci Web Azure Machine Learning](consume-web-services.md).

#### <a name="manage-your-classic-web-service"></a>Zarządzanie usługą sieci web klasycznego
Istnieją różne akcje możesz wykonać do monitorowania usługi sieci web. Można ją aktualizować i usuń go. Dodatkowe punkty końcowe można również dodać do usługi sieci web klasycznego oprócz domyślnego punktu końcowego, który jest tworzony podczas jego wdrażania.

Aby uzyskać więcej informacji, zobacz [Zarządzanie obszarem roboczym usługi Azure Machine Learning](manage-workspace.md) i [zarządzania usługi sieci web przy użyciu portalu usługi sieci Web systemu Azure Machine Learning](manage-new-webservice.md).

<!-- When this article gets published, fix the link and uncomment
For more information on how to manage Azure Machine Learning web service endpoints using the REST API, see **Azure machine learning web service endpoints**.
-->

## <a name="update-the-web-service"></a>Aktualizacja usługi sieci web
Można wprowadzić zmiany do usługi sieci web, takich jak uaktualnianie modelu danych dodatkowe szkolenie i wdrożyć ją ponownie, zastępując oryginalne usługi sieci web.

Aby zaktualizować usługę sieci web, otwórz oryginalny eksperyment predykcyjny używany do wdrażania usługi sieci web i Utwórz kopię można edytować, klikając **SAVE AS**. Wprowadź zmiany, a następnie kliknij przycisk **wdrażanie usługi sieci Web**.

Ponieważ wdrożeniu tego eksperymentu przed, zostanie wyświetlona prośba, jeśli chcesz zastąpić (usługa sieci Web klasycznego) lub zaktualizuj istniejącą usługę (nową usługę sieci web). Kliknięcie przycisku **tak** lub **aktualizacji** zatrzymuje istniejącej usługi sieci web i wdraża nowy eksperyment predykcyjny zostało wdrożone w jego miejscu.

> [!NOTE]
> Jeśli wprowadzono zmiany w konfiguracji oryginalnej usługi sieci web, na przykład wprowadzania nową nazwę wyświetlaną i opis, konieczne będzie ponowne wprowadzenie tych wartości.
> 
> 

Jedną z opcji aktualizowania usługi sieci web jest ponownie ucz modelu programowo. Aby uzyskać więcej informacji, zobacz temat [Retrain Machine Learning models programmatically](retrain-models-programmatically.md) (Ponowne trenowanie modeli uczenia maszynowego programowo).

<!-- internal links -->
[Tworzenie eksperymentu szkolenia]: #create-a-training-experiment
[Przekonwertuj go eksperyment predykcyjny]: #convert-the-training-experiment-to-a-predictive-experiment
[Go wdrożyć jako usługę sieci web]: #deploy-it-as-a-web-service
[Nowy]: #deploy-the-predictive-experiment-as-a-new-Web-service
[Classic]: #deploy-the-predictive-experiment-as-a-new-Web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service
