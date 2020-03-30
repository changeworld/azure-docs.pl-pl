---
title: Wdrażanie usługi internetowej
titleSuffix: ML Studio (classic) - Azure
description: Jak przekonwertować eksperyment szkoleniowy na eksperyment predykcyjny, przygotować go do wdrożenia, a następnie wdrożyć go jako usługę Azure Machine Learning Studio (klasyczną).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 01/06/2017
ms.openlocfilehash: 6c81e50560de69f7702e852d4602680fde7f01f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218127"
---
# <a name="deploy-an-azure-machine-learning-studio-classic-web-service"></a>Wdrażanie usługi sieci Web usługi Azure Machine Learning Studio (klasycznej)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Usługa Azure Machine Learning Studio (klasyczna) umożliwia tworzenie i testowanie predykcyjnego rozwiązania analitycznego. Następnie można wdrożyć rozwiązanie jako usługę sieci web.

Usługi sieci web Machine Learning Studio (klasyczne) zapewniają interfejs między aplikacją a modelem oceniania przepływu pracy machine learning studio (klasycznym). Zewnętrzna aplikacja może komunikować się z modelem oceniania przepływu pracy Machine Learning Studio (klasyczny) w czasie rzeczywistym. Wywołanie usługi sieci web Machine Learning Studio (klasycznej) zwraca wyniki prognozowania do aplikacji zewnętrznej. Wywołanie usługi sieci Web polega na przekazaniu klucza interfejsu API utworzonego podczas wdrażania tej usługi. Usługa internetowa Machine Learning Studio (klasyczna) jest oparta na REST, popularnej architekturze dla projektów programowania sieci Web.

Usługa Azure Machine Learning Studio (klasyczna) ma dwa typy usług sieci web:

* Usługa żądania odpowiedzi (RRS): usługa o małym opóźnieniu i wysoce skalowalna usługa, która ocenia pojedynczy rekord danych.
* Usługa wykonywania wsadowego (BES): usługa asynchroniza, która ocenia partię rekordów danych.

Dane wejściowe dla usługi BES przypominają dane wejściowe używane przez usługę RRS. Główna różnica polega na tym, że usługa BES odczytuje blok rekordów z różnych źródeł, takich jak usługa Azure Blob Storage, usługa Azure Table Storage, usługa Azure SQL Database, usługa HDInsight (zapytanie Hive) i źródła HTTP.

Z punktu widzenia wysokiego poziomu można wdrożyć model w trzech krokach:

* **[Tworzenie eksperymentu szkoleniowego]** — w studio (klasyczny), można trenować i testować model analizy predykcyjnej przy użyciu danych szkoleniowych, które dostarczasz, przy użyciu dużego zestawu wbudowanych algorytmów uczenia maszynowego.
* **[Przekonwertuj go na eksperyment predykcyjny]** — po przeszkoleniu modelu z istniejącymi danymi i gotowym do użycia go do uzyskania nowych danych, przygotowujesz i usprawnisz eksperyment pod kątem prognoz.
* **Wdrażanie** go jako **[nowej usługi sieci web]** lub **[klasycznej usługi sieci web]** — podczas wdrażania eksperymentu predykcyjnego jako usługi sieci Web platformy Azure użytkownicy mogą wysyłać dane do modelu i odbierać prognozy modelu.

## <a name="create-a-training-experiment"></a>Tworzenie eksperymentu szkoleniowego

Aby wyszkolić model analizy predykcyjnej, za pomocą usługi Azure Machine Learning Studio (classic) można utworzyć eksperyment szkoleniowy, w którym można uwzględnić różne moduły do ładowania danych szkoleniowych, przygotować dane w razie potrzeby, zastosować algorytmy uczenia maszynowego i ocenić Wyniki. Można iterować w eksperymencie i wypróbować różne algorytmy uczenia maszynowego, aby porównać i ocenić wyniki.

Proces tworzenia i zarządzania eksperymentami szkoleniowymi jest dokładniej omówiony gdzie indziej. Więcej informacji można znaleźć w tych artykułach:

* [Tworzenie prostego eksperymentu w usłudze Azure Machine Learning Studio (klasyczny)](create-experiment.md)
* [Opracowanie rozwiązania predykcyjnego za pomocą usługi Azure Machine Learning Studio (klasyczne)](tutorial-part1-credit-risk.md)
* [Importowanie danych szkoleniowych do usługi Azure Machine Learning Studio (klasyczne)](import-data.md)
* [Zarządzanie iteracjami eksperymentów w usłudze Azure Machine Learning Studio (klasyczny)](manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Konwertowanie eksperymentu trenowania w eksperyment predykcyjny

Po przeszkoleniu modelu możesz przekonwertować eksperyment szkoleniowy na eksperyment predykcyjny w celu zdobycia nowych danych.

Konwertując do eksperymentu predykcyjnego, otrzymujesz przeszkolony model gotowy do wdrożenia jako usługa sieci web oceniania. Użytkownicy usługi sieci web można wysyłać dane wejściowe do modelu i modelu wyśle z powrotem wyniki przewidywania. Podczas konwersji do eksperymentu predykcyjnego, należy pamiętać, jak oczekujesz, że model będzie używany przez innych.

Aby przekonwertować eksperyment szkoleniowy na eksperyment predykcyjny, kliknij pozycję **Uruchom** u dołu obszaru roboczego eksperymentu, kliknij pozycję **Skonfiguruj usługę sieci Web**, a następnie wybierz pozycję **Predykcyjna usługa sieci Web**.

![Konwertuj na eksperyment oceniania](./media/publish-a-machine-learning-web-service/figure-1.png)

Aby uzyskać więcej informacji na temat wykonywania tej konwersji, zobacz [Jak przygotować model do wdrożenia w usłudze Azure Machine Learning Studio (klasyczny).](convert-training-experiment-to-scoring-experiment.md)

W poniższych krokach opisano wdrażanie eksperymentu predykcyjnego jako nowej usługi sieci web. Eksperyment można również wdrożyć jako klasyczną usługę sieci web.

## <a name="deploy-it-as-a-new-web-service"></a>Wdrażanie go jako nowej usługi sieci web

Teraz, gdy eksperyment predykcyjny został przygotowany, można wdrożyć go jako nową usługę sieci web platformy Azure (opartą na menedżerze zasobów). Za pomocą usługi sieci web użytkownicy mogą wysyłać dane do modelu, a model zwróci jego prognoz.

Aby wdrożyć eksperyment predykcyjny, kliknij przycisk **Uruchom** u dołu kanwy eksperymentu. Po zakończeniu eksperymentu kliknij pozycję **Wdrażanie usługi sieci Web** i wybierz pozycję **Wdrażanie usługi sieci Web [Nowy]**.  Zostanie otwarta strona wdrażania portalu usługi sieci Web machine learning studio (klasycznej).

> [!NOTE] 
> Aby wdrożyć nową usługę sieci web, musisz mieć wystarczające uprawnienia w subskrypcji, do której wdrażasz usługę sieci web. Aby uzyskać więcej informacji, zobacz [Zarządzanie usługą sieci Web przy użyciu portalu usług azure machine learning web .](manage-new-webservice.md) 

### <a name="web-service-portal-deploy-experiment-page"></a>Strona eksperymentu wdrażania w portalu usługi sieci Web

Na stronie Wdrażanie eksperymentu wprowadź nazwę usługi sieci web.
Wybierz plan cenowy. Jeśli masz istniejący plan cenowy, możesz go wybrać, w przeciwnym razie musisz utworzyć nowy plan cenowy dla usługi.

1. W rozwijaniu **Planu cen** wybierz istniejący plan lub wybierz opcję Wybierz **nowy plan.**
2. W **części Nazwa planu**wpisz nazwę, która będzie identyfikować plan na rachunku.
3. Wybierz jedną z **warstw planu miesięcznego**. Plan domyślnie jest dzielony na warstwy planów dla domyślnego regionu, a usługi internetowe są wdrażane dla tego regionu.

Kliknij **pozycję Wdrażanie** i zostanie otwarta strona **Szybki start** dla usługi sieci web.

Strona Szybki start usługi sieci web zapewnia dostęp i wskazówki dotyczące najczęstszych zadań wykonywanych po utworzeniu usługi sieci web. W tym miejscu można łatwo uzyskać dostęp zarówno do strony testowej, jak i strony Zużycie.

<!-- ![Deploy the web service](./media/publish-a-machine-learning-web-service/figure-2.png)-->

### <a name="test-your-new-web-service"></a>Testowanie nowej usługi internetowej

Aby przetestować nową usługę sieci web, kliknij przycisk **Przetestuj usługę sieci web** w obszarze typowych zadań. Na stronie testowej można przetestować usługę sieci web jako usługę żądania odpowiedzi (RRS) lub usługę wykonywania wsadowego (BES).

Strona testowa RRS wyświetla dane wejściowe, wyjścia i wszystkie parametry globalne zdefiniowane dla eksperymentu. Aby przetestować usługę sieci web, można ręcznie wprowadzić odpowiednie wartości dla danych wejściowych lub podać plik sformatowany z wartością rozdzieloną przecinkami (CSV) zawierający wartości testowe.

Aby przetestować przy użyciu rrs, w trybie widoku listy wprowadź odpowiednie wartości dla danych wejściowych i kliknij **przycisk Test request-response**. Wyniki prognozowania są wyświetlane w kolumnie wyjściowej po lewej stronie.

![Wprowadź odpowiednie wartości, aby przetestować usługę sieci web](./media/publish-a-machine-learning-web-service/figure-5-test-request-response.png)

Aby przetestować bes, kliknij pozycję **Partia**. Na stronie testowej Batch kliknij pozycję Przeglądaj pod wejściem i wybierz plik CSV zawierający odpowiednie wartości przykładowe. Jeśli nie masz pliku CSV i eksperyment predykcyjny został utworzony przy użyciu programu Machine Learning Studio (klasyczny), możesz pobrać zestaw danych dla eksperymentu predykcyjnego i użyć go.

Aby pobrać zestaw danych, otwórz studio machine learning studio (klasyczny). Otwórz eksperyment predykcyjny i kliknij prawym przyciskiem myszy dane wejściowe eksperymentu. Z menu kontekstowego wybierz **zestaw danych,** a następnie wybierz polecenie **Pobierz**.

![Pobierz zestaw danych ze studia (klasycznego) kanwy](./media/publish-a-machine-learning-web-service/figure-7-mls-download.png)

Kliknij **przycisk Testuj**. Stan zadania wykonywania wsadowego jest wyświetlany po prawej stronie w obszarze **Zadania wsadowe testu**.

![Przetestuj zadanie wykonywania wsadowego za pomocą portalu usługi sieci web](./media/publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/publish-a-machine-learning-web-service/figure-3.png)-->

Na stronie **KONFIGURACJA** można zmienić opis, tytuł, zaktualizować klucz konta magazynu i włączyć przykładowe dane dla usługi sieci web.

![Konfigurowanie usługi sieci Web](./media/publish-a-machine-learning-web-service/figure-8-arm-configure.png)

### <a name="access-your-new-web-service"></a>Dostęp do nowej usługi sieci web

Po wdrożeniu usługi sieci web z usługi Machine Learning Studio (classic), można wysyłać dane do usługi i odbierać odpowiedzi programowo.

**Strona Consume** zawiera wszystkie informacje potrzebne do uzyskania dostępu do usługi sieci web. Na przykład klucz interfejsu API jest dostarczany w celu umożliwienia autoryzowanego dostępu do usługi.

Aby uzyskać więcej informacji na temat uzyskiwania dostępu do usługi sieci web Machine Learning Studio (klasycznej), zobacz [Jak korzystać z usługi Azure Machine Learning Studio (klasycznej).](consume-web-services.md)

### <a name="manage-your-new-web-service"></a>Zarządzanie nową usługą sieci web

Nowe usługi sieci Web można zarządzać za pomocą portalu Machine Learning Studio (klasyczne) usługi sieci Web. Na [głównej stronie portalu](https://services.azureml.net/)kliknij pozycję **Usługi sieci Web**. Na stronie usług sieci web można usunąć lub skopiować usługę. Aby monitorować określoną usługę, kliknij usługę, a następnie kliknij pozycję **Pulpit nawigacyjny**. Aby monitorować zadania wsadowe skojarzone z usługą sieci web, kliknij pozycję **Dziennik żądań wsadowych**.

### <a name="deploy-your-new-web-service-to-multiple-regions"></a><a id="multi-region"></a>Wdrażanie nowej usługi sieci web w wielu regionach

Nową usługę sieci web można łatwo wdrożyć w wielu regionach bez konieczności wielu subskrypcji lub obszarów roboczych.

Cennik jest specyficzny dla regionu, więc należy zdefiniować plan rozliczeń dla każdego regionu, w którym będzie wdrażać usługę sieci web.

#### <a name="create-a-plan-in-another-region"></a>Tworzenie planu w innym regionie

1. Zaloguj się do [usług microsoft azure machine learning web services](https://services.azureml.net/).
2. Kliknij opcję menu **Plany.**
3. Na stronie Plany nad widokiem kliknij pozycję **Nowy**.
4. Z listy rozwijanej **Subskrypcja** wybierz subskrypcję, w której będzie mieszkał nowy plan.
5. Z listy rozwijanej **Region** wybierz region dla nowego planu. Opcje planu dla wybranego regionu zostaną wyświetlone w sekcji **Opcje planu** na stronie.
6. Z **listy** rozwijanej Grupa zasobów wybierz grupę zasobów dla planu. Więcej informacji na temat grup zasobów można znaleźć w [usłudze Azure Resource Manager .](../../azure-resource-manager/management/overview.md)
7. W **nazwie planu** wpisz nazwę planu.
8. W obszarze **Opcje planu**kliknij poziom rozliczeń dla nowego planu.
9. Kliknij przycisk **Utwórz**.

#### <a name="deploy-the-web-service-to-another-region"></a>Wdrażanie usługi sieci web w innym regionie

1. Na stronie Usługi sieci Web uczenia maszynowego platformy Microsoft Azure kliknij opcję menu **Usługi sieci Web.**
2. Wybierz wdrażaną usługę sieci Web w nowym regionie.
3. Kliknij **pozycję Kopiuj**.
4. W **aplikacji Web Service Name**wpisz nową nazwę usługi sieci web.
5. W **opisie usługi sieci Web**wpisz opis usługi sieci web.
6. Z listy rozwijanej **Subskrypcja** wybierz subskrypcję, w której będzie się resorcie nowej usługi sieci web.
7. Z **listy** rozwijanej Grupa zasobów wybierz grupę zasobów dla usługi sieci web. Więcej informacji na temat grup zasobów można znaleźć w [usłudze Azure Resource Manager .](../../azure-resource-manager/management/overview.md)
8. Z listy rozwijanej **Region** wybierz region, w którym ma być wdrażana usługa sieci web.
9. Z **listy** rozwijanej Konto magazynu wybierz konto magazynu, na którym ma być przechowywana usługa sieci web.
10. Z listy rozwijanej **Plan cen** wybierz plan w regionie wybranym w kroku 8.
11. Kliknij **pozycję Kopiuj**.

## <a name="deploy-it-as-a-classic-web-service"></a>Wdrażanie go jako klasycznej usługi sieci web

Teraz, gdy eksperyment predykcyjny został wystarczająco przygotowany, można go wdrożyć jako klasyczną usługę sieci web platformy Azure. Za pomocą usługi sieci web użytkownicy mogą wysyłać dane do modelu, a model zwróci jego prognoz.

Aby wdrożyć eksperyment predykcyjny, kliknij pozycję **Uruchom** u dołu obszaru roboczego eksperymentu, a następnie kliknij pozycję **Wdrażanie usługi sieci Web**. Usługa sieci web jest skonfigurowana i umieszczana na pulpicie nawigacyjnym usługi sieci Web.

![Wdrażanie usługi sieci web w studio (klasyczny)](./media/publish-a-machine-learning-web-service/figure-2.png)

### <a name="test-your-classic-web-service"></a>Testowanie klasycznej usługi internetowej

Usługę sieci web można przetestować w portalu usługi online Machine Learning Studio (klasyczny) lub Machine Learning Studio (klasyczny).

Aby przetestować usługę sieci web Odpowiedzi żądania, kliknij przycisk **Testuj** na pulpicie nawigacyjnym usługi sieci web. Pojawi się okno dialogowe z prośbą o podanie danych wejściowych dla usługi. Są to kolumny oczekiwane przez eksperyment oceniania. Wprowadź zestaw danych, a następnie kliknij przycisk **OK**. Wyniki generowane przez usługę sieci web są wyświetlane w dolnej części pulpitu nawigacyjnego.

Możesz kliknąć **łącze Test** preview, aby przetestować usługę w portalu Azure Machine Learning Studio (klasyczny) Web Services, jak pokazano wcześniej w sekcji Nowa usługa sieci web.

Aby przetestować usługę wykonywania partii, kliknij przycisk **Testuj** łącze podglądu . Na stronie testowej Batch kliknij pozycję Przeglądaj pod wejściem i wybierz plik CSV zawierający odpowiednie wartości przykładowe. Jeśli nie masz pliku CSV i eksperyment predykcyjny został utworzony przy użyciu programu Machine Learning Studio (klasyczny), możesz pobrać zestaw danych dla eksperymentu predykcyjnego i użyć go.

![Testowanie usługi internetowej](./media/publish-a-machine-learning-web-service/figure-3.png)

Na stronie **KONFIGURACJA** można zmienić nazwę wyświetlaną usługi i nadać jej opis. Nazwa i opis jest wyświetlany w [witrynie Azure portal,](https://portal.azure.com/) gdzie można zarządzać usługami sieci web.

Można podać opis danych wejściowych, danych wyjściowych i parametrów usługi sieci web, wprowadzając ciąg dla każdej kolumny w obszarze **SCHEMAT WEJŚCIOWY**, **SCHEMAT WYJŚCIOWY**i **PARAMETR USŁUGI SIECI Web**. Opisy te są używane w przykładowej dokumentacji kodu dostarczonej dla usługi sieci web.

Rejestrowanie można włączyć w celu zdiagnozowania wszelkich błędów, które są widoczne podczas uzyskiwania dostępu do usługi sieci web. Aby uzyskać więcej informacji, zobacz [Włączanie rejestrowania dla usług sieci Web Machine Learning Studio (klasycznych).](web-services-logging.md)

![Włączanie logowania w portalu usług internetowych](./media/publish-a-machine-learning-web-service/figure-4.png)

Można również skonfigurować punkty końcowe dla usługi sieci web w portalu usług Azure Machine Learning Web Services podobne do procedury pokazanej wcześniej w sekcji Nowa usługa sieci web. Opcje są różne, można dodać lub zmienić opis usługi, włączyć rejestrowanie i włączyć przykładowe dane do testowania.

### <a name="access-your-classic-web-service"></a>Dostęp do klasycznej usługi sieci web

Po wdrożeniu usługi sieci web z usługi Azure Machine Learning Studio (classic), można wysyłać dane do usługi i odbierać odpowiedzi programowo.

Pulpit nawigacyjny zawiera wszystkie informacje potrzebne do uzyskania dostępu do usługi sieci web. Na przykład klucz interfejsu API jest dostarczany w celu umożliwienia autoryzowanego dostępu do usługi, a strony pomocy interfejsu API są udostępniane w celu rozpoczęcia pisania kodu.

Aby uzyskać więcej informacji na temat uzyskiwania dostępu do usługi sieci web Machine Learning Studio (klasycznej), zobacz [Jak korzystać z usługi Azure Machine Learning Studio (klasycznej).](consume-web-services.md)

### <a name="manage-your-classic-web-service"></a>Zarządzanie klasyczną usługą sieci web

Istnieją różne akcje, które można wykonać w celu monitorowania usługi sieci web. Można go zaktualizować i usunąć. Można również dodać dodatkowe punkty końcowe do klasycznej usługi sieci web oprócz domyślnego punktu końcowego, który jest tworzony podczas wdrażania.

Aby uzyskać więcej informacji, zobacz [Zarządzanie obszarem roboczym usługi Azure Machine Learning Studio (klasyczny)](manage-workspace.md) i [zarządzanie usługą sieci web przy użyciu portalu azure machine learning studio (klasyczny) web services](manage-new-webservice.md).

## <a name="update-the-web-service"></a>Aktualizowanie usługi sieci Web
Można wprowadzić zmiany w usłudze sieci web, takie jak aktualizowanie modelu za pomocą dodatkowych danych szkoleniowych i wdrażanie go ponownie, zastępując oryginalną usługę sieci web.

Aby zaktualizować usługę sieci web, otwórz oryginalny eksperyment predykcyjny użyty do wdrożenia usługi sieci web i wykonać edytowalną kopię, klikając przycisk **ZAPISZ JAKO**. Wprowadzać zmiany, a następnie kliknąć **pozycję Wdrażanie usługi sieci Web**.

Ponieważ ten eksperyment został wdrożony wcześniej, zostaniesz zapytany, czy chcesz zastąpić (klasyczną usługę sieci Web) lub zaktualizować (nowa usługa sieci web) istniejącą usługą. Kliknięcie **przycisku TAK** lub **Update** zatrzymuje istniejącą usługę sieci web i wdraża nowy eksperyment predykcyjny jest wdrażany w jego miejsce.

> [!NOTE]
> Jeśli wprowadzono zmiany konfiguracji w oryginalnej usłudze sieci web, na przykład wprowadzenie nowej nazwy lub opisu wyświetlanego, należy ponownie wprowadzić te wartości.

Jedną z opcji aktualizacji usługi sieci web jest przekwalifikowanie modelu programowo. Aby uzyskać więcej informacji, zobacz [programowo retrain Machine Learning Studio (klasyczne) modele](/azure/machine-learning/studio/retrain-machine-learning-model).

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji technicznych na temat sposobu wdrażania, zobacz [Jak model machine learning studio (klasyczny) przechodzi z eksperymentu do operacyjnej usługi sieci Web.](model-progression-experiment-to-web-service.md)

* Aby uzyskać szczegółowe informacje na temat przygotowania modelu do wdrożenia, zobacz [Jak przygotować model do wdrożenia w usłudze Azure Machine Learning Studio (klasyczny).](convert-training-experiment-to-scoring-experiment.md)

* Istnieje kilka sposobów uzyskiwania dostępu do usługi sieci Web za pomocą interfejsu API REST. Zobacz [Jak korzystać z usługi Azure Machine Learning Studio (klasycznej).](consume-web-services.md)

<!-- internal links -->
[Tworzenie eksperymentu szkoleniowego]: #create-a-training-experiment
[Konwertuj go na eksperyment predykcyjny]: #convert-the-training-experiment-to-a-predictive-experiment
[Nowa usługa internetowa]: #deploy-it-as-a-new-web-service
[Klasyczna usługa internetowa]: #deploy-it-as-a-classic-web-service
[Nowy]: #deploy-it-as-a-new-web-service
[classic]: #deploy-the-predictive-experiment-as-a-classic-web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service
