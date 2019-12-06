---
title: Wdrażanie usługi sieci Web
titleSuffix: ML Studio (classic) - Azure
description: Jak przekonwertować eksperyment szkoleniowy na eksperyment predykcyjny, przygotować go do wdrożenia, a następnie wdrożyć jako usługę sieci Web Azure Machine Learning Studio (klasyczną).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 01/06/2017
ms.openlocfilehash: a2c1ba1d4cd2dfdbf2a94005c539e70705486ba4
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851098"
---
# <a name="deploy-an-azure-machine-learning-studio-classic-web-service"></a>Wdrażanie usługi sieci Web Azure Machine Learning Studio (klasycznej)

Azure Machine Learning Studio (klasyczny) umożliwia tworzenie i testowanie rozwiązań analitycznych predykcyjnych. Następnie możesz wdrożyć rozwiązanie jako usługę sieci Web.

Machine Learning Studio (klasyczne) usługi sieci Web zapewniają interfejs między aplikacją a modelem oceniania przepływu pracy Machine Learning Studio (klasycznym). Aplikacja zewnętrzna może komunikować się z modelem oceniania przepływu pracy Machine Learning Studio (klasyczny) w czasie rzeczywistym. Wywołanie usługi sieci Web Machine Learning Studio (klasycznej) zwraca wyniki prognozowania do aplikacji zewnętrznej. Wywołanie usługi sieci Web polega na przekazaniu klucza interfejsu API utworzonego podczas wdrażania tej usługi. Machine Learning Studio (klasyczny) usługa sieci Web jest oparta na architekturze REST, popularnej architektury dla projektów programowania w sieci Web.

Klasyczna wersja Azure Machine Learning Studio ma dwa typy usług sieci Web:

* Usługa żądanie-odpowiedź (RR): małe opóźnienia, wysoce skalowalna usługa, która ocenia pojedynczy rekord danych.
* Usługa wykonywania wsadowego (BES): asynchroniczna usługa służąca do oceniania partii rekordów danych.

Dane wejściowe dla usługi BES przypominają dane wejściowe używane przez usługę RRS. Główna różnica polega na tym, że usługa BES odczytuje blok rekordów z różnych źródeł, takich jak usługa Azure Blob Storage, usługa Azure Table Storage, usługa Azure SQL Database, usługa HDInsight (zapytanie Hive) i źródła HTTP.

Z punktu widzenia wysokiego poziomu można wdrożyć model w trzech krokach:

* **[Tworzenie eksperymentu szkoleniowego]** — w klasycznej wersji programu Studio można szkolić i testować model analizy predykcyjnej za pomocą dostarczonych danych szkoleniowych przy użyciu dużego zestawu wbudowanych algorytmów uczenia maszynowego.
* **[Przekonwertuj ją na eksperyment predykcyjny]** — po przeszkoleniu modelu z istniejącymi danymi i przygotowaniu się do korzystania z niego do oceny nowych danych możesz przygotować i usprawnić eksperymenty do prognoz.
* **Wdrażanie** go jako **[Nowa usługa sieci web]** Web lub **[Klasyczna usługa sieci web]** — w przypadku wdrożenia eksperymentu predykcyjnego jako usługi sieci Web platformy Azure użytkownicy mogą wysyłać dane do modelu i odbierać przewidywania modeli.

## <a name="create-a-training-experiment"></a>Tworzenie eksperymentu szkolenia

Aby przeprowadzić uczenie modelu analizy predykcyjnej, należy użyć klasycznej wersji Azure Machine Learning Studio do utworzenia eksperymentu szkoleniowego, w którym są uwzględniane różne moduły służące do ładowania danych szkoleniowych, przygotowywania danych w razie potrzeby, stosowania algorytmów uczenia maszynowego i Oceń wyniki. Możesz wykonywać iterację eksperymentu i próbować użyć różnych algorytmów uczenia maszynowego, aby porównać i oszacować wyniki.

Proces tworzenia eksperymentów szkoleniowych i zarządzania nimi został szczegółowo omówiony w innym miejscu. Więcej informacji można znaleźć w tych artykułach:

* [Tworzenie prostego eksperymentu w Azure Machine Learning Studio (klasyczny)](create-experiment.md)
* [Opracowywanie rozwiązania predykcyjnego za pomocą Azure Machine Learning Studio (klasyczny)](tutorial-part1-credit-risk.md)
* [Importowanie danych szkoleniowych do Azure Machine Learning Studio (klasyczne)](import-data.md)
* [Zarządzanie iteracjami eksperymentów w Azure Machine Learning Studio (klasyczny)](manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Konwertowanie eksperymentu trenowania w eksperyment predykcyjny

Po przeszkoleniu modelu możesz rozpocząć konwersję eksperymentu szkoleniowego na eksperyment predykcyjny, który będzie oceniać nowe dane.

Dzięki konwersji na eksperyment predykcyjny jest gotowy do wdrożenia przeszkolony model jako usługa sieci Web oceniania. Użytkownicy usługi sieci Web mogą wysyłać dane wejściowe do modelu, a Twój model wyśle wyniki przewidywania. Podczas konwertowania na eksperyment predykcyjny należy pamiętać o tym, jak oczekujemy, że model ma być używany przez inne osoby.

Aby dokonać konwersji eksperymentu szkoleniowego na eksperyment predykcyjny, kliknij pozycję **Uruchom** w dolnej części kanwy eksperymentu, kliknij pozycję **Skonfiguruj usługę sieci Web**, a następnie wybierz pozycję **predykcyjna usługa sieci Web**.

![Konwertuj na eksperyment oceniania](./media/publish-a-machine-learning-web-service/figure-1.png)

Aby uzyskać więcej informacji na temat wykonywania tej konwersji, zobacz [jak przygotować model do wdrożenia w Azure Machine Learning Studio (klasyczny)](convert-training-experiment-to-scoring-experiment.md).

Poniższe kroki opisują wdrożenie eksperymentu predykcyjnego jako nowej usługi sieci Web. Możesz również wdrożyć eksperyment jako klasyczną usługę sieci Web.

## <a name="deploy-it-as-a-new-web-service"></a>Wdróż ją jako nową usługę sieci Web

Teraz, gdy eksperyment predykcyjny został przygotowany, możesz go wdrożyć jako nową usługę sieci Web platformy Azure (Menedżer zasobów). Przy użyciu usługi sieci web, użytkownicy mogą wysyłać dane do modelu i model zwróci jego prognozy.

Aby wdrożyć eksperyment predykcyjny, kliknij pozycję **Uruchom** w dolnej części kanwy eksperymentu. Po zakończeniu eksperymentu kliknij pozycję **Wdróż usługę sieci Web** i wybierz pozycję **Wdróż usługę sieci Web [Nowy]** .  Zostanie otwarta strona wdrożenie portalu usługi sieci Web Machine Learning Studio (klasyczny).

> [!NOTE] 
> Aby wdrożyć nową usługę sieci web musi masz wystarczające uprawnienia w ramach subskrypcji, do której możesz wdrażanie usługi sieci web. Aby uzyskać więcej informacji, zobacz [Zarządzanie usługą sieci Web przy użyciu portalu usług sieci Web Azure Machine Learning](manage-new-webservice.md). 

### <a name="web-service-portal-deploy-experiment-page"></a>Strona wdrożenia portalu usługi sieci Web

Na stronie wdrażanie eksperymentu wprowadź nazwę usługi sieci Web.
Wybierz plan cenowy. Jeśli masz istniejący plan cenowy, możesz go wybrać, w przeciwnym razie musisz utworzyć nowy plan cenowy dla usługi.

1. Z listy rozwijanej **Plan cen** wybierz istniejący plan lub wybierz opcję **Wybierz nowy plan** .
2. W polu **Nazwa planu**wpisz nazwę, która będzie identyfikować plan na rachunku.
3. Wybierz jedną z **miesięcznych warstw planu**. Plan domyślnie jest dzielony na warstwy planów dla domyślnego regionu, a usługi internetowe są wdrażane dla tego regionu.

Kliknij przycisk **Wdróż** , a strona **szybkiego startu** dla usługi sieci Web zostanie otwarta.

Strona szybkiego startu usługi sieci Web zapewnia dostęp i wskazówki dotyczące najbardziej typowych zadań, które zostaną wykonane po utworzeniu usługi sieci Web. W tym miejscu możesz łatwo uzyskać dostęp do strony testowej i strony użycia.

<!-- ![Deploy the web service](./media/publish-a-machine-learning-web-service/figure-2.png)-->

### <a name="test-your-new-web-service"></a>Testowanie nowej usługi sieci Web

Aby przetestować nową usługę sieci Web, kliknij pozycję **Testuj usługę sieci Web** w obszarze Typowe zadania. Na stronie test można testować usługę sieci Web jako usługę żądanie-odpowiedź lub usługę wykonywania wsadowego (BES).

Na stronie test RR są wyświetlane dane wejściowe, wyjściowe i wszystkie parametry globalne zdefiniowane dla eksperymentu. Aby przetestować usługę sieci Web, można ręcznie wprowadzić odpowiednie wartości dla wejść lub podać plik z wartościami rozdzielanymi przecinkami (CSV) zawierający wartości testowe.

Aby przetestować przy użyciu rekordów RR, w trybie widoku listy wprowadź odpowiednie wartości danych wejściowych i kliknij pozycję **Testuj żądanie-odpowiedź**. Wyniki przewidywania są wyświetlane w kolumnie dane wyjściowe po lewej stronie.

![Wprowadź odpowiednie wartości, aby przetestować usługę sieci Web](./media/publish-a-machine-learning-web-service/figure-5-test-request-response.png)

Aby przetestować BES, kliknij pozycję **Batch**. Na stronie test wsadowy kliknij pozycję Przeglądaj w obszarze dane wejściowe i wybierz plik CSV zawierający odpowiednie przykładowe wartości. Jeśli nie masz pliku CSV i utworzysz eksperyment predykcyjny przy użyciu klasycznej wersji Machine Learning Studio, możesz pobrać zestaw danych dla eksperymentu predykcyjnego i użyć go.

Aby pobrać zestaw danych, Otwórz klasyczną wersję Machine Learning Studio. Otwórz eksperyment predykcyjny i kliknij prawym przyciskiem myszy dane wejściowe dla eksperymentu. Z menu kontekstowego wybierz pozycję **zestaw danych** , a następnie wybierz pozycję **Pobierz**.

![Pobierz zestaw danych z kanwy programu Studio (klasycznej)](./media/publish-a-machine-learning-web-service/figure-7-mls-download.png)

Kliknij przycisk **Testuj**. Stan zadania wykonywania wsadowego jest wyświetlany w prawej części **testu zadań wsadowych**.

![Testowanie zadania wykonywania wsadowego za pomocą portalu usługi sieci Web](./media/publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/publish-a-machine-learning-web-service/figure-3.png)-->

Na stronie **Konfiguracja** można zmienić opis, tytuł, zaktualizować klucz konta magazynu i włączyć przykładowe dane dla usługi sieci Web.

![Konfigurowanie usługi sieci Web](./media/publish-a-machine-learning-web-service/figure-8-arm-configure.png)

### <a name="access-your-new-web-service"></a>Uzyskaj dostęp do nowej usługi sieci Web

Po wdrożeniu usługi sieci Web z klasycznej wersji Machine Learning Studio można wysłać dane do usługi i programowo odbierać odpowiedzi.

Na stronie **Używanie** znajdują się wszystkie informacje potrzebne do uzyskania dostępu do usługi sieci Web. Na przykład klucz interfejsu API jest dostarczany, aby zezwolić na autoryzowany dostęp do usługi.

Aby uzyskać więcej informacji na temat uzyskiwania dostępu do usługi sieci Web Machine Learning Studio (klasycznej), zobacz [jak korzystać z usługi sieci web Azure Machine Learning Studio (klasycznej)](consume-web-services.md).

### <a name="manage-your-new-web-service"></a>Zarządzanie nową usługą sieci Web

Możesz zarządzać nowymi usługami sieci Web przy użyciu portalu usług sieci Web Machine Learning Studio (klasycznego). Na [stronie Portal główny](https://services.azureml.net/)kliknij pozycję **usługi sieci Web**. Na stronie usługi sieci Web można usunąć lub skopiować usługę. Aby monitorować określoną usługę, kliknij usługę, a następnie kliknij pozycję **pulpit nawigacyjny**. Aby monitorować zadania wsadowe skojarzone z usługą sieci Web, kliknij pozycję **Dziennik żądań wsadowych**.

### <a id="multi-region"></a>Wdrażanie nowej usługi sieci Web w wielu regionach

Nową usługę sieci Web można łatwo wdrożyć w wielu regionach bez konieczności stosowania wielu subskrypcji lub obszarów roboczych.

Cennik jest specyficzny dla regionu, dlatego należy zdefiniować plan rozliczeniowy dla każdego regionu, w którym zostanie wdrożona usługa sieci Web.

#### <a name="create-a-plan-in-another-region"></a>Tworzenie planu w innym regionie

1. Zaloguj się do [usług sieci Web Microsoft Azure Machine Learning](https://services.azureml.net/).
2. Kliknij przycisk **plany** opcji menu.
3. W przypadku planów za pośrednictwem strony widoku kliknij przycisk **New**.
4. Z **subskrypcji** listy rozwijanej wybierz subskrypcję, w którym będzie przechowywany nowy plan.
5. Z **Region** listę rozwijaną, wybierz region dla nowego planu. Zostaną wyświetlone opcje planu dla wybranego regionu **opcje planu** części strony.
6. Z **grupy zasobów** listy rozwijanej wybierz zasób grupy dla tego planu. Aby uzyskać więcej informacji na temat grup zasobów, zobacz [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).
7. W **Nazwa planu** wpisz nazwę planu.
8. W obszarze **opcje planu**, kliknij odpowiedni poziom rozliczeń dla nowego planu.
9. Kliknij przycisk **Utwórz**.

#### <a name="deploy-the-web-service-to-another-region"></a>Wdrażanie usługi sieci Web w innym regionie

1. Na stronie Microsoft Azure Machine Learning Web Services kliknij opcję menu **usługi sieci Web** .
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

## <a name="deploy-it-as-a-classic-web-service"></a>Wdróż ją jako klasyczną usługę sieci Web

Teraz, gdy eksperyment predykcyjny został wystarczająco przygotowany, możesz go wdrożyć jako klasyczną usługę sieci Web platformy Azure. Przy użyciu usługi sieci web, użytkownicy mogą wysyłać dane do modelu i model zwróci jego prognozy.

Aby wdrożyć eksperyment predykcyjny, kliknij pozycję **Uruchom** w dolnej części kanwy eksperymentu, a następnie kliknij pozycję **Wdróż usługę sieci Web**. Usługa sieci Web została skonfigurowana i zostanie umieszczona na pulpicie nawigacyjnym usługi sieci Web.

![Wdrażanie usługi sieci Web z poziomu programu Studio (wersja klasyczna)](./media/publish-a-machine-learning-web-service/figure-2.png)

### <a name="test-your-classic-web-service"></a>Testowanie klasycznej usługi sieci Web

Możesz przetestować usługę sieci Web w portalu usług sieci Web Machine Learning Studio (klasyczny) lub Machine Learning Studio (klasyczny).

Aby przetestować usługę sieci Web odpowiedzi na żądanie, kliknij przycisk **Testuj** na pulpicie nawigacyjnym usługi sieci Web. Zostanie wyświetlone okno dialogowe z monitem o podanie danych wejściowych dla usługi. Są to kolumny oczekiwane przez eksperyment oceniania. Wprowadź zestaw danych, a następnie kliknij przycisk **OK**. Wyniki generowane przez usługę sieci Web są wyświetlane w dolnej części pulpitu nawigacyjnego.

Możesz kliknąć link **test** Preview, aby przetestować usługę w klasycznej wersji portalu usług sieci Web Azure Machine Learning Studio, jak pokazano wcześniej w sekcji Nowa usługa sieci Web.

Aby przetestować usługę wykonywania wsadowego, kliknij link Podgląd **testowy** . Na stronie test wsadowy kliknij pozycję Przeglądaj w obszarze dane wejściowe i wybierz plik CSV zawierający odpowiednie przykładowe wartości. Jeśli nie masz pliku CSV i utworzysz eksperyment predykcyjny przy użyciu klasycznej wersji Machine Learning Studio, możesz pobrać zestaw danych dla eksperymentu predykcyjnego i użyć go.

![Testowanie usługi internetowej](./media/publish-a-machine-learning-web-service/figure-3.png)

Na stronie **Konfiguracja** można zmienić nazwę wyświetlaną usługi i podać opis. Nazwa i opis są wyświetlane w [Azure Portal](https://portal.azure.com/) , w którym można zarządzać usługami sieci Web.

Można podać opis danych wejściowych, danych wyjściowych i parametrów usługi sieci Web, wprowadzając ciąg dla każdej kolumny w obszarze **schemat wejściowy**, **schemat danych wyjściowych**i **parametr usługi sieci Web**. Opisy te są używane w przykładowej dokumentacji kodu podanej dla usługi sieci Web.

Możesz włączyć rejestrowanie, aby zdiagnozować wszystkie błędy widoczne podczas uzyskiwania dostępu do usługi sieci Web. Aby uzyskać więcej informacji, zobacz [Włączanie rejestrowania dla usług sieci web Machine Learning Studio (klasycznych)](web-services-logging.md).

![Włączanie rejestrowania w portalu usług sieci Web](./media/publish-a-machine-learning-web-service/figure-4.png)

Możesz również skonfigurować punkty końcowe usługi sieci Web w portalu usług sieci Web Azure Machine Learning, podobnie jak w przypadku procedury pokazanej wcześniej w sekcji Nowa usługa sieci Web. Opcje są różne, można dodać lub zmienić opis usługi, włączyć rejestrowanie i włączyć przykładowe dane do testowania.

### <a name="access-your-classic-web-service"></a>Uzyskiwanie dostępu do klasycznej usługi sieci Web

Po wdrożeniu usługi sieci Web z klasycznej wersji Machine Learning Studio można wysłać dane do usługi i programowo odbierać odpowiedzi.

Pulpit nawigacyjny zawiera wszystkie informacje potrzebne do uzyskania dostępu do usługi sieci Web. Na przykład klucz interfejsu API jest udostępniany w celu zezwolenia na dostęp do usługi, a strony pomocy interfejsu API są udostępniane, aby ułatwić rozpoczęcie pisania kodu.

Aby uzyskać więcej informacji na temat uzyskiwania dostępu do usługi sieci Web Machine Learning Studio (klasycznej), zobacz [jak korzystać z usługi sieci web Azure Machine Learning Studio (klasycznej)](consume-web-services.md).

### <a name="manage-your-classic-web-service"></a>Zarządzanie klasyczną usługą sieci Web

Istnieją różne akcje, które można wykonać, aby monitorować usługę sieci Web. Można go zaktualizować i usunąć. Oprócz domyślnego punktu końcowego, który jest tworzony podczas wdrażania, można także dodać kolejne punkty końcowe do klasycznej usługi sieci Web.

Aby uzyskać więcej informacji, zobacz temat [Zarządzanie obszarem roboczym Azure Machine Learning Studio (klasycznym)](manage-workspace.md) i [zarządzaniem usługą sieci Web przy użyciu portalu usług sieci Web Azure Machine Learning Studio (klasycznego)](manage-new-webservice.md).

## <a name="update-the-web-service"></a>Aktualizacja usługi sieci web
Możesz wprowadzić zmiany w usłudze sieci Web, takie jak aktualizowanie modelu z dodatkowymi danymi szkoleniowymi, i wdrożyć je ponownie, zastępując oryginalną usługę sieci Web.

Aby zaktualizować usługę sieci Web, Otwórz oryginalny eksperyment predykcyjny, który został użyty do wdrożenia usługi sieci Web i Utwórz edytowalną kopię, klikając pozycję **Zapisz jako**. Wprowadź zmiany, a następnie kliknij przycisk **Wdróż usługę sieci Web**.

Ponieważ ten eksperyment został wdrożony wcześniej, zostanie wyświetlony monit o zastępowanie (klasycznej usługi sieci Web) lub aktualizacji (nowej usługi sieci Web) istniejącej usługi. Kliknięcie przycisku **tak** lub **Aktualizuj** powoduje zatrzymanie istniejącej usługi sieci Web i wdrożenie nowego eksperymentu predykcyjnego w jego miejscu.

> [!NOTE]
> Jeśli wprowadzono zmiany w konfiguracji oryginalnej usługi sieci Web, na przykład wprowadzając nową nazwę wyświetlaną lub opis, należy ponownie wprowadzić te wartości.

Jedną z opcji aktualizowania usługi sieci Web jest możliwość programowego ponownego uczenia modelu. Aby uzyskać więcej informacji, zobacz temat ponowne [uczenie modeli Machine Learning Studio (klasycznych)](/azure/machine-learning/studio/retrain-machine-learning-model).

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać bardziej szczegółowe informacje techniczne na temat działania wdrożenia, zobacz, [jak model Machine Learning Studio (klasyczny) postępuje od eksperymentu do działającej usługi sieci Web](model-progression-experiment-to-web-service.md).

* Aby uzyskać szczegółowe informacje na temat przygotowywania modelu do wdrożenia, zobacz [jak przygotować model do wdrożenia w Azure Machine Learning Studio (klasyczny)](convert-training-experiment-to-scoring-experiment.md).

* Istnieje kilka sposobów uzyskiwania dostępu do usługi sieci Web za pomocą interfejsu API REST. Zobacz [, jak korzystać z usługi sieci web Azure Machine Learning Studio (klasycznej)](consume-web-services.md).

<!-- internal links -->
[Tworzenie eksperymentu szkoleniowego]: #create-a-training-experiment
[Przekonwertuj ją na eksperyment predykcyjny]: #convert-the-training-experiment-to-a-predictive-experiment
[Nowa usługa sieci web]: #deploy-it-as-a-new-web-service
[Klasyczna usługa sieci web]: #deploy-it-as-a-classic-web-service
[Nowy]: #deploy-it-as-a-new-web-service
[classic]: #deploy-the-predictive-experiment-as-a-classic-web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service
