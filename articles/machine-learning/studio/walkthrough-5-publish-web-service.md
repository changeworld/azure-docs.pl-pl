---
title: 'Krok 5: Wdrażanie usługi sieci web Machine Learning | Dokumentacja firmy Microsoft'
description: 'Krok 5 opracowanie wskazówki rozwiązanie predykcyjne: Wdrażanie eksperyment predykcyjny w usłudze Machine Learning Studio jako usługę sieci web.'
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 3fca74a3-c44b-4583-a218-c14c46ee5338
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.openlocfilehash: 6472b795c9b3651160af4d1520bfd1c90a500b5f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="walkthrough-step-5-deploy-the-azure-machine-learning-web-service"></a>Przewodnik, krok 5. Wdrażanie usługi sieci Web Azure Machine Learning
Jest to krok piąty tego przewodnika, [tworzenie rozwiązania analizy predykcyjnej w usłudze Azure Machine Learning](walkthrough-develop-predictive-solution.md)

1. [Tworzenie obszaru roboczego usługi Machine Learning](walkthrough-1-create-ml-workspace.md)
2. [Przekazywanie istniejących danych](walkthrough-2-upload-data.md)
3. [Tworzenie nowego eksperymentu](walkthrough-3-create-new-experiment.md)
4. [Nauczanie i ocena modeli](walkthrough-4-train-and-evaluate-models.md)
5. **Wdrażanie usługi sieci Web**
6. [Uzyskiwanie dostępu do usługi sieci Web](walkthrough-6-access-web-service.md)

- - -
Udostępniania możliwość użycia modelu predykcyjnego, który opracowaliśmy w ramach tego przewodnika, możemy go wdrożyć jako usługę sieci web na platformie Azure.

Do tego momentu możemy były zmieniane uczenie modelu. Ale wdrożonej usługi nie będzie przeprowadzenie szkolenia — będzie można wygenerować nowy prognoz przez oceniania oparte na modelu danych wejściowych użytkownika. Dlatego chcemy są pewne przygotowania można przekonwertować tego doświadczenia z ***szkolenia*** eksperymentu do ***predykcyjnej*** eksperymentu. 

Jest to proces trzech etapów:  

1. Usuń jeden z modeli
2. Konwertuj *eksperyment uczenia* utworzyliśmy do *eksperyment predykcyjny*
3. Wdrażanie eksperyment predykcyjny jako usługę sieci web

## <a name="remove-one-of-the-models"></a>Usuń jeden z modeli

Najpierw musimy nieco trim tego eksperymentu w dół. Obecnie istnieją dwa różne modele w eksperymencie, ale chcemy używać jednego modelu do nas wdrażania to jako usługę sieci web.  

Załóżmy, że decydujesz się, że boosted drzewa modelu działa lepiej niż SVM model. Dlatego najpierw musisz usunąć [maszyny wektorowa obsługa Two-Class] [ two-class-support-vector-machine] modułu i modułów, które były używane na potrzeby uczenia go. Warto najpierw utworzyć kopię eksperyment, klikając **Zapisz jako** w dolnej części obszaru roboczego eksperymentu.

Musimy usunąć następujących modułów:  

* [Obsługa Two-Class wektor maszyny][two-class-support-vector-machine]
* [Uczenie modelu] [ train-model] i [Score Model] [ score-model] modułów, które zostały dołączone do niego
* [Normalizuj danych] [ normalize-data] (oba)
* [Ocena modelu] [ evaluate-model] (ponieważ jesteśmy zakończono ocenę modeli)

Wybierz każdego modułu i naciśnij klawisz Delete, lub kliknij prawym przyciskiem myszy moduł i zaznacz **usunąć**. 

![Usunięte modelu SVM][3a]

Nasz model powinien teraz wyglądać mniej więcej tak:

![Usunięte modelu SVM][3]

Teraz już wszystko gotowe do wdrożenia tego model przy użyciu [Two-Class Boosted drzewa decyzyjnego][two-class-boosted-decision-tree].

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Konwertuj eksperyment uczenia eksperyment predykcyjny

W celu przygotowania do wdrożenia tego modelu, musimy przekonwertować tej eksperyment uczenia eksperyment predykcyjny. Obejmuje to trzy kroki:

1. Zapisz model możemy gdy udało się nauczyć, a następnie zastąp naszych modułów szkoleniowych
2. TRIM eksperyment, aby usunąć modułów, które były potrzebne tylko w przypadku szkolenia
3. Zdefiniuj którym usługa sieci web będzie akceptować dane wejściowe i gdzie generuje dane wyjściowe

Firma Microsoft może to zrobić ręcznie, ale na szczęście wszystkie trzy kroki można wykonać, klikając **ustawić usługę sieci Web** w dolnej części obszaru roboczego eksperymentu (i wybierając **predykcyjnej usługi sieci Web** opcja).

> [!TIP]
> Jeśli chcesz bardziej szczegółowe informacje, na co się dzieje podczas konwertowania eksperyment uczenia eksperyment predykcyjny, zobacz [jak przygotować modelu do wdrożenia w usłudze Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md).

Po kliknięciu **ustawić usługę sieci Web**, ma miejsce kilka rzeczy:

* Trenowanego modelu jest konwertowana na jeden **Uczonego modelu** modułu i przechowywane na palecie modułów z lewej strony obszaru roboczego eksperymentu (można znaleźć go w **przeszkolone modele**)
* Moduły, które były używane do trenowania zostaną usunięte; w szczególności:
  * [Two-Class Boosted algorytm][two-class-boosted-decision-tree]
  * [Train Model][train-model]
  * [Podział danych][split]
  * drugi [wykonanie skryptu języka R] [ execute-r-script] moduł, który był używany dla danych testowych
* Zapisane trenowanego modelu jest dodawany do eksperymentu
* **Usługi danych wejściowych w sieci Web** i **sieci Web usług** moduły są dodawane (te informacje pozwalają określić gdzie przechodzą w modelu danych użytkownika i jakie dane są zwracane podczas uzyskiwania dostępu do usługi sieci web)

> [!NOTE]
> Widać, że eksperyment został zapisany w dwóch częściach na kartach, które zostały dodane w górnej części obszaru roboczego eksperymentu. Oryginalny eksperyment uczenia się na karcie **eksperyment uczenia**, i nowo utworzony eksperyment predykcyjny podlega **eksperyment predykcyjny**. Eksperyment predykcyjny jest firma Microsoft będzie wdrożyć jako usługę sieci web.

Musimy wykonać jednego dodatkowego kroku z tego konkretnego eksperymentu.
Dodaliśmy dwie [wykonanie skryptu języka R] [ execute-r-script] modułów, aby zapewnić funkcji wagę w odniesieniu do danych. Po prostu lewy, czego potrzeba do uczenie i testowanie, która jest więc firma Microsoft może zająć się tych modułów w ostatnim modelu.
Usługa Machine Learning Studio usunąć jedną [wykonanie skryptu języka R] [ execute-r-script] modułu po jego usunięciu [podziału] [ split] modułu. Teraz możemy usunąć inny i połączyć [Edytor metadanych] [ metadata-editor] bezpośrednio do [Score Model][score-model].    

Naszym doświadczeniu powinna wyglądać następująco:  

![Ocenianie trenowanego modelu][4]  

> [!NOTE]
> Możesz się zastanawiać, dlaczego możemy wywołało dataset danych karty kredytowej niemiecki UCI eksperyment predykcyjny. Usługa ma wynik danych użytkownika, nie oryginalnego zestawu danych, więc Dlaczego pozostaw oryginalnego zestawu danych w modelu?
> 
> Jest to wartość true, że usługa nie wymaga oryginalnych danych karty kredytowej. Jednak musi schematu dla danych, które obejmują takie informacje jak liczbę kolumn są i kolumny, które są liczbowych. Informacje o schemacie jest interpretować dane użytkownika. Firma Microsoft pozostawić te składniki, połączony, dzięki czemu oceniania moduł ma schemat zestawu danych, gdy usługa jest uruchomiona. Dane nie jest używana, tylko schemat.  
> 
> 

Uruchom eksperyment raz ostatni (kliknij **Uruchom**.) Jeśli chcesz sprawdzić, czy model nadal działa, kliknij przycisk dane wyjściowe [Score Model] [ score-model] moduł i zaznacz **wyświetlanie wyników**. Widać, że oryginalne dane zostanie wyświetlona, wraz z wartości ryzyka środki ("oceniane etykiety") i oceniania wartość prawdopodobieństwa ("wynik prawdopodobieństwa".) 

## <a name="deploy-the-web-service"></a>Wdrażanie usługi sieci Web
Eksperyment można wdrożyć jako albo klasycznym usługi sieci web, lub Nowa usługa sieci web, która jest oparta na usłudze Azure Resource Manager.

### <a name="deploy-as-a-classic-web-service"></a>Wdrożyć jako usługę sieci web klasycznego
Aby wdrożyć usługę sieci web klasycznego pochodzące z naszych eksperyment, kliknij przycisk **wdrażanie usługi sieci Web** poniżej kanwy i wybierz **wdrażanie usługi sieci Web [klasyczny]**. Usługa Machine Learning Studio wdraża eksperymentu jako usługę sieci web i przejście do pulpitu nawigacyjnego dla tej usługi sieci web. Na tej stronie możesz powrócić do eksperymentu (**widoku migawki** lub **wyświetlić najnowszych**) i uruchamianie testu proste usługi sieci web (zobacz **przetestować usługę sieci web** poniżej). Istnieje również tutaj informacje dotyczące tworzenia aplikacji, które mogą uzyskiwać dostęp do usługi sieci web (omówiona w następnym kroku w tym przewodniku).

![Pulpit nawigacyjny usługi sieci Web][6]

Usługę można skonfigurować, klikając **konfiguracji** kartę. W tym miejscu można zmodyfikować nazwy usługi (go została podana nazwa eksperymentu domyślnie) i nadaj mu opis. Możesz też udzielić większej liczby etykiet przyjazną dla danych wejściowych i wyjściowych.  

![Konfiguruj usługę sieci web][5]  

### <a name="deploy-as-a-new-web-service"></a>Wdrożyć jako nową usługę sieci web

> [!NOTE] 
> Aby wdrożyć nową usługę sieci web musi mieć wystarczające uprawnienia do subskrypcji, w której wdrażasz usługi sieci web. Aby uzyskać więcej informacji, zobacz [zarządzania usługi sieci web przy użyciu portalu usługi sieci Web systemu Azure Machine Learning](manage-new-webservice.md). 

Aby wdrożyć nową usługę sieci web pochodzące z naszym doświadczeniu:

1. Kliknij przycisk **wdrażanie usługi sieci Web** poniżej kanwy i wybierz **wdrażanie usługi sieci Web [New]**. Usługa Machine Learning Studio przenosi do usług sieci web Azure Machine Learning **wdrażanie eksperymentu** strony.

2. Wprowadź nazwę usługi sieci web. 

3. Dla **planu cen**, można wybrać istniejącego planu cenowego, lub wybierz opcję "Utwórz nowy" i także nazwę nowego planu i wybierz opcję miesięczne planu. Plan domyślny warstw do planów dla regionu domyślnego i usługi sieci web jest wdrażany do tego regionu.

4. Kliknij przycisk **wdrażanie**.

Po kilku minutach **szybkiego startu** zostanie otwarta strona dla usługi sieci web.

Usługę można skonfigurować, klikając **Konfiguruj** kartę. W tym miejscu można zmodyfikować usługi tytuł i nadaj mu opis. 

Aby przetestować usługę sieci web, kliknij przycisk **Test** kartę (zobacz **przetestować usługę sieci web** poniżej). Aby uzyskać informacje dotyczące tworzenia aplikacji, które mogą uzyskiwać dostęp do usługi sieci web, kliknij przycisk **Consume** kartę (do następnego kroku w tym przewodniku zostaną umieszczone w bardziej szczegółowo).

> [!TIP]
> Należy zaktualizować usługę sieci web, po jej wdrożeniu. Na przykład, jeśli chcesz zmienić model, a następnie edytować eksperyment uczenia, dostosować parametry modelu i kliknij przycisk **wdrażanie usługi sieci Web**, wybierając żądane **wdrażanie usługi sieci Web [klasyczny]** lub **Wdrażanie usługi sieci Web [New]**. Wdrażając eksperyment ponownie, zastępuje usługę sieci web za pomocą zaktualizowanej modelu.  
> 
> 

## <a name="test-the-web-service"></a>Testowanie usługi sieci web

Podczas uzyskiwania dostępu do usługi sieci web dane użytkownika wprowadza za pośrednictwem **sieci Web dane wejściowe usługi** modułu, w którym są przekazywane do [Score Model] [ score-model] modułu i oceniane. Sposobu skonfigurowaliśmy eksperyment predykcyjny modelu oczekuje danych w tym samym formacie co oryginalnego zestawu danych ryzyko kredytowe.
Wyniki są zwracane do użytkownika z usługi sieci web za pośrednictwem **sieci Web usług** modułu.

> [!TIP]
> Sposób mamy eksperyment predykcyjny skonfigurowane, wynikiem całą [Score Model] [ score-model] modułu są zwracane. W tym wszystkie dane wejściowe oraz wartość ryzyko kredytowe i oceniania prawdopodobieństwa. Ale może inny zwracać — na przykład można zwrócić tylko wartość ryzyko kredytowe. Aby to zrobić, Wstaw [kolumny projektu] [ project-columns] modułu między [Score Model] [ score-model] i **sieci Web produktu**wyeliminować kolumny nie mają usługę sieci web do zwrócenia. 
> 
> 

Można przetestować web klasycznym usługi w **Machine Learning Studio** lub **usługi sieci Web systemu Azure Machine Learning** portalu.
Możesz przetestować nową sieć web service tylko w **usługi sieci Web usługi Machine Learning** portalu.

> [!TIP]
> Podczas testowania, w portalu usługi sieci Web systemu Azure Machine Learning, może mieć portalu tworzenie przykładowych danych, które służy do testowania usługi żądań i odpowiedzi. Na **Konfiguruj** wybierz przycisk Tak, aby uzyskać **przykładowych danych włączone?**. Po otwarciu karty żądań i odpowiedzi na **testu** strony portalu wypełnia przykładowe dane pobrane z oryginalnego zestawu danych ryzyko kredytowe.

### <a name="test-a-classic-web-service"></a>Testowanie usługi sieci web klasycznego

Usługi sieci web klasycznego można przetestować w usłudze Machine Learning Studio lub w portalu usługi sieci Web usługi Machine Learning. 

#### <a name="test-in-machine-learning-studio"></a>Testowanie w usłudze Machine Learning Studio

1. Na **pulpitu NAWIGACYJNEGO** usługi sieci web kliknij pozycję **testu** przycisku w obszarze **domyślny punkt końcowy**. Okno dialogowe wyskakującej i monituje o dane wejściowe dla usługi. Są to te same kolumny, które znajdowały się w zestawie danych oryginalnego ryzyko kredytowe.  

2. Wprowadź zestaw danych, a następnie kliknij przycisk **OK**. 

#### <a name="test-in-the-machine-learning-web-services-portal"></a>Testowanie w portalu usługi sieci Web usługi Machine Learning

1. Na **pulpitu NAWIGACYJNEGO** usługi sieci web kliknij pozycję **Podgląd testów** łącze w obszarze **domyślny punkt końcowy**. Strona testowa w portalu usługi sieci Web systemu Azure Machine Learning punktu końcowego usługi sieci web otwiera i monituje o dane wejściowe dla usługi. Są to te same kolumny, które znajdowały się w zestawie danych oryginalnego ryzyko kredytowe.

2. Kliknij przycisk **Test żądań i odpowiedzi**. 

### <a name="test-a-new-web-service"></a>Przetestować nową usługę sieci web

Możesz przetestować nową usługę sieci web tylko w portalu usługi sieci Web usługi Machine Learning.

1. W [usługi sieci Web systemu Azure Machine Learning](https://services.azureml.net/quickstart) portalu, kliknij przycisk **testu** w górnej części strony. **Testu** zostanie otwarta strona i można wprowadzić dane dotyczące usługi. Wyświetlane pól wejściowych odpowiada kolumn, które znajdowały się w zestawie danych oryginalnego ryzyko kredytowe. 

2. Wprowadź zestaw danych, a następnie kliknij przycisk **testu żądanie-odpowiedź**.

Wyniki testu są wyświetlane po prawej stronie strony w kolumnie wyników. 


## <a name="manage-the-web-service"></a>Zarządzanie usługą sieci web

Po wdrożeniu usługi sieci web, czy klasycznego lub nowe, można zarządzać nim z [usługi sieci Web Microsoft Azure Machine Learning](https://services.azureml.net/quickstart) portalu.

Aby monitorować wydajność usługi sieci web:

1. Zaloguj się do [usługi sieci Web Microsoft Azure Machine Learning](https://services.azureml.net/quickstart) portalu
2. Kliknij przycisk **usług sieci Web**
3. Kliknij opcję usługi sieci web
4. Kliknij przycisk **pulpitu nawigacyjnego**

- - -
**Następnie: [dostęp do usługi sieci web](walkthrough-6-access-web-service.md)**

[3]: ./media/walkthrough-5-publish-web-service/publish3.png
[3a]: ./media/walkthrough-5-publish-web-service/publish3a.png
[4]: ./media/walkthrough-5-publish-web-service/publish4.png
[5]: ./media/walkthrough-5-publish-web-service/publish5.png
[6]: ./media/walkthrough-5-publish-web-service/publish6.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[project-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
