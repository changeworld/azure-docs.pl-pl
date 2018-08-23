---
title: 'Krok 5: Wdrażanie usługi sieci web Machine Learning | Dokumentacja firmy Microsoft'
description: 'Krok 5 Programowanie przewodnik rozwiązania do analizy predykcyjnej: wdrożyć eksperyment predykcyjny w usłudze Machine Learning Studio jako usługę sieci web.'
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 3fca74a3-c44b-4583-a218-c14c46ee5338
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.openlocfilehash: 436656195e00311dd350a5526b01fffa56ac02ca
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2018
ms.locfileid: "42056513"
---
# <a name="walkthrough-step-5-deploy-the-azure-machine-learning-web-service"></a>Przewodnik, krok 5. Wdrażanie usługi sieci Web Azure Machine Learning
Jest to krok piąty tego przewodnika, [tworzenia rozwiązania analizy predykcyjnej w usłudze Azure Machine Learning](walkthrough-develop-predictive-solution.md)

1. [Tworzenie obszaru roboczego usługi Machine Learning](walkthrough-1-create-ml-workspace.md)
2. [Przekazywanie istniejących danych](walkthrough-2-upload-data.md)
3. [Tworzenie nowego eksperymentu](walkthrough-3-create-new-experiment.md)
4. [Nauczanie i ocena modeli](walkthrough-4-train-and-evaluate-models.md)
5. **Wdrażanie usługi sieci Web**
6. [Uzyskiwanie dostępu do usługi sieci Web](walkthrough-6-access-web-service.md)

- - -
Udostępniania możliwość użycia model predykcyjny, który został opracowany przez nas w tym przewodniku, możemy go wdrożyć jako usługę sieci web na platformie Azure.

Do tej pory możemy były zmieniane szkolenia nasz model. Ale wdrożonej usługi nie jest już zamierza wykonać szkolenia — będzie znajdował się generowanie nowych przewidywań według oceniania na podstawie naszych modelu danych wejściowych użytkownika. Więc zamierzamy przygotowywaniem można przekonwertować tego eksperymentu z ***szkolenia*** eksperymentów do ***predykcyjne*** eksperymentować. 

Jest to proces trzech kroków:  

1. Usuń jeden z modeli
2. Konwertuj *eksperymentu szkolenia* utworzyliśmy do *eksperyment predykcyjny*
3. Wdrożyć eksperyment predykcyjny jako usługę sieci web

## <a name="remove-one-of-the-models"></a>Usuń jeden z modeli

Po pierwsze potrzebujemy nieco trim tego eksperymentu w dół. Obecnie nie mamy dwa różne modele w eksperymencie, ale chcemy do użycia jednego modelu, gdy będziemy wdrażać ją jako usługę sieci web.  

Załóżmy, że firma Microsoft decydujesz, czy model wzmocnionego drzewa spisywała się lepiej niż SVM model. Dlatego najpierw należy usunąć [Two-Class pomocy technicznej Vector Machine] [ two-class-support-vector-machine] moduł i moduły, które zostały użyte do trenowania go. Warto najpierw utworzyć kopię eksperymentu, klikając **Zapisz jako** w dolnej części obszaru roboczego eksperymentu.

Należy usunąć następujących modułów:  

* [Obsługa Two-Class wektor maszyny][two-class-support-vector-machine]
* [Uczenie modelu] [ train-model] i [Score Model] [ score-model] modułów, które zostały podłączone do niego
* [Normalizacji danych] [ normalize-data] (oba z nich)
* [Ocena modelu] [ evaluate-model] (ponieważ jesteśmy Zakończono oceniania modeli)

Wybierz każdy moduł i naciśnij klawisz Delete, lub kliknij prawym przyciskiem myszy moduł i zaznacz **Usuń**. 

![Usunięte modelu SVM][3a]

Nasz model powinien teraz wyglądać mniej więcej tak:

![Usunięte modelu SVM][3]

Teraz jesteśmy gotowi do wdrożenia przy użyciu tego modelu [Two-Class Boosted Decision drzewa][two-class-boosted-decision-tree].

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Konwertowanie eksperymentu szkolenia na eksperyment predykcyjny

Aby uzyskać ten model jest gotowe do wdrożenia, musimy przekonwertować tego eksperymentu szkolenia eksperyment predykcyjny. Ten proces obejmuje trzy kroki:

1. Zapisywanie modelu, mamy już uczony, a następnie zastąp naszych moduły szkoleniowe
2. TRIM eksperymentu do usunięcia modułów, które były potrzebne tylko w przypadku szkolenia
3. Zdefiniuj, którym usługa sieci web będzie akceptować dane wejściowe i gdzie generuje dane wyjściowe

Firma Microsoft może zrobić to ręcznie, ale na szczęście wszystkich trzech krokach można osiągnąć, klikając **ustawić usługę sieci Web** w dolnej części obszaru roboczego eksperymentu (i wybierając polecenie **predykcyjne usługi sieci Web** opcji).

> [!TIP]
> Jeśli chcesz uzyskać więcej informacji na co się stanie, gdy konwertujesz eksperymentu szkolenia eksperyment predykcyjny, zobacz [jak przygotować modelu wdrożenia w usłudze Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md).

Po kliknięciu **ustawić usługę sieci Web**, ma miejsce kilka rzeczy:

* Uczony model jest konwertowany na pojedynczej **Uczonego modelu** modułu i zapisywane w palety modułów, z lewej strony obszaru roboczego eksperymentu (można znaleźć w obszarze **przeszkolone modele**)
* Moduły, które zostały użyte do trenowania zostaną usunięte; w szczególności:
  * [Two-Class wzmocnione drzewo decyzyjnego][two-class-boosted-decision-tree]
  * [Trenowanie modelu][train-model]
  * [Podział danych][split]
  * drugi [wykonanie skryptu języka R] [ execute-r-script] moduł, który był używany dla danych testowych
* Zapisane uczony model jest ponownie dodane do eksperymentu
* **Dane wejściowe usługi w sieci Web** i **sieci Web usługi danych wyjściowych** moduły są dodawane (identyfikatory te identyfikują gdzie przejdzie modelu danych użytkownika i jakie dane są zwracane podczas uzyskiwania dostępu do usługi sieci web)

> [!NOTE]
> Widać, że eksperyment został zapisany w dwóch częściach na kartach, które zostały dodane w górnej części obszaru roboczego eksperymentu. Oryginalny eksperymentu szkolenia znajduje się w karcie **eksperymentu szkolenia**, a nowo utworzony eksperyment predykcyjny podlega **eksperyment predykcyjny**. Eksperyment predykcyjny jest ten, który wdrożymy jako usługę sieci web.

Musimy wykonać jeden dodatkowy krok z tego określonego eksperymentu.
Dodaliśmy dwie [wykonanie skryptu języka R] [ execute-r-script] modułów, aby zapewnić funkcji wagi danych. To było po prostu lewy, czego potrzeba do szkolenia i testowania, dzięki czemu możemy Opróżnij tych modułów w ostatnim modelu.
Usługa Machine Learning Studio usunąć jedną [wykonanie skryptu języka R] [ execute-r-script] modułu po jego usunięciu [podziału] [ split] modułu. Teraz możemy usunąć inny i połączyć [Edytor metadanych] [ metadata-editor] bezpośrednio do [Score Model][score-model].    

Nasz eksperyment powinien teraz wyglądać następująco:  

![Ocenianie uczonego modelu][4]  

> [!NOTE]
> Możesz się zastanawiać, dlaczego pozostawiliśmy zestawu danych danymi karty kredytowej niemiecki UCI w eksperyment predykcyjny. Usługa będzie oceniać dane użytkownika, a nie oryginalnego zestawu danych, zatem Dlaczego oryginalnego zestawu danych w modelu?
> 
> To PRAWDA, że usługa nie musi oryginalne dane karty kredytowej. Ale musi on schemat dla danych, które zawierają informacje, takie jak liczbę kolumn są i kolumny, które są liczbowe. Te informacje schematu są niezbędne do interpretacji danych przez użytkownika. Pozostawimy te składniki połączenia tak, aby moduł oceniania ma schemat zestawu danych, gdy usługa jest uruchomiona. Dane nie jest używana, po prostu schematu.  
> 
>Co warto zwrócić uwagę to, że jeśli oryginalny zestaw danych zawiera etykietę, następnie Oczekiwano schematu z danych wejściowych w sieci web będzie również oczekują kolumnę z etykietą! Rozwiązanie tego problemu jest usunąć etykiety i inne dane w zestawie danych szkoleniowych, ale nie będzie w danych wejściowych w sieci web, przed nawiązaniem połączenia z sieci web dane wejściowe i zestaw danych szkoleniowych do moduł wspólny. 
> 

Uruchom eksperyment jeszcze jeden raz (kliknij **Uruchom**.) Jeśli chcesz sprawdzić, czy model nadal działa, kliknij wyjście [Score Model] [ score-model] modułu, a następnie wybierz pozycję **wyświetlanie wyników**. Widać, że oryginalne dane są wyświetlane, wraz z wartość o podwyższonym ryzyku środków ("Labels oceniane") i oceniania wartość prawdopodobieństwa ("wynik prawdopodobieństwa".) 

## <a name="deploy-the-web-service"></a>Wdrażanie usługi sieci Web
Możesz wdrożyć eksperymentu jako albo klasyczne usługi sieci web, lub Nowa usługa sieci web, która jest oparta na usłudze Resource Manager.

### <a name="deploy-as-a-classic-web-service"></a>Wdróż jako klasycznej usługi sieci web
Aby wdrożyć klasycznej usługi sieci web pochodzące z naszych eksperymentu, kliknij **wdrażanie usługi sieci Web** poniżej obszaru roboczego, a następnie wybierz pozycję **wdrażanie usługi sieci Web [klasyczny]**. Usługi Machine Learning Studio wdraża eksperymentu jako usługę sieci web i przejście do pulpitu nawigacyjnego dla danej usługi sieci web. Na tej stronie możesz wrócić do eksperymentu (**Wyświetl migawkę** lub **wyświetlanie najnowszych**) i uruchom prosty test usługi sieci web (zobacz **przetestować usługę sieci web** poniżej). Istnieje również tutaj informacje dotyczące tworzenia aplikacji mogących uzyskiwać dostęp do usługi sieci web (więcej informacji na temat, w następnym kroku w tym przewodniku).

![Pulpit nawigacyjny usług sieci Web][6]

Usługę można skonfigurować, klikając **konfiguracji** kartę. W tym miejscu możesz zmodyfikować nazwę usługi (go została podana nazwa eksperymentu domyślnie) i nadaj jej opis. Możesz również dostarczyć więcej przyjazne etykiety danych wejściowych i wyjściowych.  

![Konfiguruj usługę sieci web][5]  

### <a name="deploy-as-a-new-web-service"></a>Wdróż jako nowej usługi sieci web

> [!NOTE] 
> Aby wdrożyć nową usługę sieci web musi mieć wystarczające uprawnienia w ramach subskrypcji, do którego jest wdrażana usługa sieci web. Aby uzyskać więcej informacji, zobacz [Zarządzanie usługą sieci web przy użyciu portalu usług sieci Web Azure Machine Learning](manage-new-webservice.md). 

Do wdrożenia nowej usługi sieci web pochodzące z naszym doświadczeniu:

1. Kliknij przycisk **wdrażanie usługi sieci Web** poniżej obszaru roboczego, a następnie wybierz pozycję **wdrażanie usługi sieci Web [New]**. Usługa Machine Learning Studio przesyła do usług sieci web Azure Machine Learning **wdrażanie eksperymentu** strony.

2. Wprowadź nazwę usługi sieci web. 

3. Aby uzyskać **Plan cenowy**, należy można wybrać istniejącego planu taryfowego lub wybierz pozycję "Utwórz nową" i Nazwij nowy plan i wybierz opcję miesięcznego planu. Plan domyślną warstwy planów domyślnym regionem i usługi sieci web jest wdrażane do tego regionu.

4. Kliknij przycisk **wdrażanie**.

Po kilku minutach **Szybki Start** zostanie otwarta strona usługi sieci web.

Usługę można skonfigurować, klikając **Konfiguruj** kartę. W tym miejscu można zmodyfikować usługi tytułu i nadaj jej opis. 

Aby przetestować usługę sieci web, kliknij pozycję **Test** kartę (zobacz **przetestować usługę sieci web** poniżej). Aby uzyskać informacje na temat tworzenia aplikacji, które mogą uzyskać dostęp do usługi sieci web, kliknij przycisk **zużywania** tab (do następnego kroku w tym instruktażu będą umieszczane w bardziej szczegółowo).

> [!TIP]
> Po jej wdrożeniu, należy zaktualizować usługę sieci web. Na przykład, jeśli chcesz zmienić model, a następnie edytować eksperymentu szkolenia, dostosować parametry modelu i kliknij **wdrażanie usługi sieci Web**, wybierając opcję **wdrażanie usługi sieci Web [klasyczny]** lub **Wdrażanie usługi sieci Web [New]**. Gdy wdrożysz ponownie eksperyment, zastępuje usługę sieci web, teraz przy użyciu zaktualizowanych modelu.  
> 
> 

## <a name="test-the-web-service"></a>Test usługi sieci web

Podczas uzyskiwania dostępu do usługi sieci web przez użytkownika dane są wprowadzane za pośrednictwem **sieci Web dane wejściowe usługi** modułu, w którym zostanie on przekazany do [Score Model] [ score-model] modułu i oceniane. Sposobu skonfigurowaliśmy eksperyment predykcyjny model oczekuje, że dane w formacie oryginalnego zestawu danych ryzyko kredytowe.
Wyniki są zwracane do użytkownika z usługi sieci web za pośrednictwem **sieci Web usługi danych wyjściowych** modułu.

> [!TIP]
> Sposób mamy eksperyment predykcyjny skonfigurowane, całą powstały na skutek [Score Model] [ score-model] moduł są zwracane. Obejmuje to wszystkie dane wejściowe oraz wartość ryzyko kredytowe i oceniania prawdopodobieństwa. Ale może coś innego zwrócić — na przykład, można zwrócić tylko wartość ryzyka środków na korzystanie. Aby to zrobić, należy wstawić [kolumny projektu] [ project-columns] modułu między [Score Model] [ score-model] i **sieci Web usługi danych wyjściowych**, aby wyeliminować kolumny nie chcesz, usługę sieci web do zwrócenia. 
> 
> 

Możesz przetestować klasyczne sieci web usług albo w **usługi Machine Learning Studio** lub **usług sieci Web Azure Machine Learning** portalu.
Można przetestować nową web service tylko w **usług sieci Web Machine Learning** portalu.

> [!TIP]
> Podczas testowania, w portalu usług sieci Web Azure Machine Learning, masz portal Utwórz przykładowe dane, które można użyć, aby przetestować usługę odpowiedzi na żądanie. Na **Konfiguruj** zaznacz opcję "Tak" dla **przykładowych danych włączono?**. Po otwarciu karty odpowiedzi na żądanie na **testu** stronie portalu wypełnia przykładowe dane pobierane z oryginalnego zestawu danych ryzyko kredytowe.

### <a name="test-a-classic-web-service"></a>Testowanie klasycznej usługi sieci web

Możesz przetestować klasycznej usługi sieci web w usłudze Machine Learning Studio lub w portalu usług sieci Web Machine Learning. 

#### <a name="test-in-machine-learning-studio"></a>Testowanie w usłudze Machine Learning Studio

1. Na **pulpit NAWIGACYJNY** strony usługi sieci web, kliknij przycisk **testu** przycisku w obszarze **domyślny punkt końcowy**. Okno dialogowe pojawia się i monituje o dane wejściowe dla usługi. Są to te same kolumny, które znajdowały się w oryginalnego zestawu danych ryzyko kredytowe.  

2. Wprowadź zestawu danych, a następnie kliknij przycisk **OK**. 

#### <a name="test-in-the-machine-learning-web-services-portal"></a>Testowanie w portalu usług sieci Web Machine Learning

1. Na **pulpit NAWIGACYJNY** strony usługi sieci web, kliknij przycisk **Test w wersji zapoznawczej** łącze w obszarze **domyślny punkt końcowy**. Strona testowa w portalu usług sieci Web Azure Machine Learning dla punktu końcowego usługi sieci web otwiera i monituje o dane wejściowe dla usługi. Są to te same kolumny, które znajdowały się w oryginalnego zestawu danych ryzyko kredytowe.

2. Kliknij przycisk **testowania odpowiedź na żądanie**. 

### <a name="test-a-new-web-service"></a>Testowanie nowej usługi sieci web

Możesz przetestować nową usługę sieci web tylko w portalu usług sieci Web Machine Learning.

1. W [usług sieci Web Azure Machine Learning](https://services.azureml.net/quickstart) portalu, kliknij przycisk **testu** w górnej części strony. **Testu** zostanie otwarta strona i można wpisać danych dla usługi. Wyświetlane pola wejściowego odnoszą się do kolumn, które znajdowały się w oryginalnego zestawu danych ryzyko kredytowe. 

2. Wprowadź zestawu danych, a następnie kliknij przycisk **odpowiedź na żądanie testu**.

Wyniki testu są wyświetlane w prawej części strony, w kolumnie wyników. 


## <a name="manage-the-web-service"></a>Zarządzanie usługą sieci web

Po wdrożeniu usługi sieci web czy klasycznego lub nowego, można zarządzać nim z [usług sieci Web Microsoft Azure Machine Learning](https://services.azureml.net/quickstart) portalu.

Aby monitorować wydajność usługi sieci web:

1. Zaloguj się do [usług sieci Web Microsoft Azure Machine Learning](https://services.azureml.net/quickstart) portalu
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
