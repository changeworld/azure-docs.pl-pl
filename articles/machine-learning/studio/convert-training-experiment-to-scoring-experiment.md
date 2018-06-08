---
title: Jak przygotować modelu do wdrożenia w usłudze Azure Machine Learning Studio | Dokumentacja firmy Microsoft
description: Jak przygotować uczonego modelu do wdrożenia jako usługę sieci web konwertując eksperyment predykcyjny eksperymentu uczenia Machine Learning Studio.
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: eb943c45-541a-401d-844a-c3337de82da6
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2017
ms.openlocfilehash: 4bfbe22ba04f154c9f24daa13231d18e73316f9c
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34833846"
---
# <a name="how-to-prepare-your-model-for-deployment-in-azure-machine-learning-studio"></a>Jak przygotować modelu do wdrożenia w usłudze Azure Machine Learning Studio

Azure Machine Learning Studio udostępnia narzędzia potrzebne do opracowania modelu analizy predykcyjnej i operacjonalizacji go przez wdrożenie jej jako usługi sieci web platformy Azure.

Aby to zrobić, użyj Studio tworzenie eksperymentu - o nazwie *eksperyment uczenia* — której uczenia, wynik, a następnie edytować model. Po zakończeniu, możesz uzyskać modelu gotowe do wdrożenia za pomocą konwersji eksperymentu uczenia się *eksperyment predykcyjny* skonfigurowanego na dane użytkownika wynik.

Można zobaczyć przykład tego procesu w [wskazówki: tworzenie rozwiązania analizy predykcyjnej w celu oceny ryzyka kredytowego w usłudze Azure Machine Learning](walkthrough-develop-predictive-solution.md).

Ten artykuł przedstawia nowości w jaki sposób eksperyment uczenia pobiera konwertowane na eksperyment predykcyjny i wdrażanie tego eksperyment predykcyjny szczegóły. Zrozumienie tych informacji, nauczysz się, jak skonfigurować wdrożonym modelu umożliwia bardziej efektywne.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="overview"></a>Przegląd 

Proces konwersji eksperyment uczenia eksperyment predykcyjny obejmuje trzy kroki:

1. Zastąp algorytm modułów z uczonego modelu uczenia maszynowego.
2. Przytnij eksperyment, aby tylko moduły, które są potrzebne w przypadku oceniania. Eksperyment uczenia zawiera szereg modułów, które są niezbędne do szkolenia, ale nie są wymagane, gdy jest uczenia modelu.
3. Zdefiniuj sposób modelu będzie akceptować dane użytkownika usługi sieci web i jakie dane zostaną zwrócone.

> [!TIP]
> W eksperymencie szkolenia już został związanych z szkolenia i oceniania modelu za pomocą własnych danych. Jednak po wdrożeniu użytkownicy będą wysyłać nowe dane do modelu i zwróci wyniki prognozowania. Tak jak możesz przekonwertować eksperymentu uczenia eksperyment predykcyjny w celu przygotowania do wdrożenia, należy pamiętać, jak model będzie używany przez inne osoby.
> 
> 

## <a name="set-up-web-service-button"></a>Przycisk Ustaw usługi sieci Web
Po uruchomieniu eksperymentu (kliknij **Uruchom** w dolnej części obszaru roboczego eksperymentu), kliknij przycisk **ustawić usługę sieci Web** przycisk (wybierz **predykcyjnej usługi sieci Web** opcja). **Ustaw usługę sieci Web** wykonuje dla Ciebie konwersji eksperymentu uczenia eksperyment predykcyjny trzy kroki:

1. Zapisuje uczonego modelu w **przeszkolone modele** części palety modułów (do lewej strony obszaru roboczego eksperymentu). Następnie zastępuje algorytmu uczenia maszynowego i [Train Model] [ train-model] modułów z zapisanym trenowanego modelu.
2. Analizuje eksperymentu, a usuwa modułów, które zostały wyraźnie używana tylko w przypadku szkolenia i nie są już potrzebne.
3. Wstawia _sieci Web dane wejściowe usługi_ i _dane wyjściowe_ moduły do domyślnej lokalizacji w eksperymencie (zaakceptować te moduły i zwrócić dane użytkownika).

Na przykład następujące eksperymentu przygotowuje decyzji boosted dwuklasowych drzewa modelu przy użyciu przykładowych danych spisu:

![Eksperyment uczenia][figure1]

Moduły, w tym eksperymencie wykonywać zasadniczo cztery różne funkcje:

![Moduł funkcji][figure2]

Podczas konwertowania tego eksperymentu uczenia eksperyment predykcyjny niektóre z tych modułów nie są już potrzebne, lub ich teraz służą do różnych celów:

* **Dane** — dane w tym przykładowego zestawu danych nie jest używany podczas oceniania — usługi sieci web użytkownik będzie dostarczać dane do oceny. Jednak metadane z tego zestawu danych, takich jak typy danych jest używany przez trenowanego modelu. Dlatego należy przechowywać w eksperyment predykcyjny zestawu danych, dzięki czemu może udostępnić te metadane.

* **Przedprodukcyjnym** — w zależności od danych użytkownika, które zostaną przesłane do oceniania, te moduły mogą lub nie może być konieczne do przetwarzania przychodzących danych. **Ustawić usługę sieci Web** przycisku nie touch te — należy zdecydować, sposobu ich obsługę.
  
    Na przykład, w tym przykładzie, przykładowego zestawu danych może mieć wartości Brak, więc [Clean Missing Data] [ clean-missing-data] moduł został uwzględniony, aby zaradczych. Ponadto przykładowego zestawu danych zawiera kolumny, które nie są wymagane do nauczenia modelu. Dlatego [Select Columns in Dataset] [ select-columns] moduł został dołączony do wykluczenia te dodatkowe kolumny z przepływu danych. Jeśli wiesz, że dane, które zostaną przesłane do oceniania przez usługę sieci web nie ma brakujących wartości, a następnie można usunąć [Clean Missing Data] [ clean-missing-data] modułu. Ponieważ jednak [Select Columns in Dataset] [ select-columns] modułu pomaga zdefiniować kolumny danych, która oczekuje trenowanego modelu, powinna być ten moduł.

* **Szkolenie** — te moduły są używane do uczenia modelu. Po kliknięciu **ustawić usługę sieci Web**, te moduły są zamieniane na jeden moduł, który zawiera model szkolenia. Ten nowy moduł jest zapisywany w **przeszkolone modele** części palety modułów.

* **Wynik** — w tym przykładzie [podziału danych] [ split] moduł służy do dzielenia strumienia danych na dane testowe i danych szkoleniowych. W eksperyment predykcyjny, firma Microsoft nie uczony, dlatego [podziału danych] [ split] mogą zostać usunięte. Podobnie, drugi [Score Model] [ score-model] modułu i [Evaluate Model] [ evaluate-model] modułu są używane do porównywania wyników z danych testowych, więc te moduły nie są wymagane w eksperyment predykcyjny. Pozostałe [Score Model] [ score-model] modułu, jednak jest potrzebne do zwrócenia wyniku wynik za pośrednictwem usługi sieci web.

Oto, jak naszym przykładzie wygląda po kliknięciu przycisku **ustawić usługę sieci Web**:

![Przekonwertować eksperyment predykcyjny][figure3]

Pracy wykonanej przez **ustawić usługę sieci Web** może być wystarczające, aby przygotować eksperymentu do można wdrożyć jako usługę sieci web. Można jednak wykonania dodatkowych czynności dodatkowe specyficzne dla eksperymentu.

### <a name="adjust-input-and-output-modules"></a>Dostosuj wejściowymi i wyjściowymi modułów
W eksperymencie szkolenia używany zestaw danych szkoleniowych i została następnie przetwarza pobierania danych w formularzu, który wymagany Algorytm uczenia maszynowego. Jeśli nie wymaga to przetwarzanie danych powinien być wyświetlany za pośrednictwem usługi sieci web, można pominąć go: Połącz dane wyjściowe **wejściowych modułu usługi sieci Web** do innego modułu w eksperymencie. Dane użytkownika pojawią się teraz w modelu w tej lokalizacji.

Na przykład domyślnie **ustawić usługę sieci Web** umieszcza **sieci Web dane wejściowe usługi** modułu w górnej części przepływu danych, jak pokazano na rysunku powyżej. Ale możemy ręcznie umieścić **sieci Web dane wejściowe usługi** poza modułów danych przetwarzania:

![Przenoszenie danych wejściowych usługi sieci web][figure4]

Danych wejściowych dostarczonych przez usługę sieci web będzie teraz przekazać bezpośrednio do modułu Score Model bez żadnych przetwarzania wstępnego.

Podobnie, domyślnie **ustawić usługę sieci Web** umieszcza modułu wyjście usługi sieci Web w dolnej części z przepływu danych. W tym przykładzie Usługa sieci web zwróci użytkownikowi dane wyjściowe [Score Model] [ score-model] moduł, który zawiera wektor zakończenie danych wejściowych oraz wyników oceniania.
Jednak jeśli chcesz użyć do zwrócenia coś innego, następnie można dodać dodatkowe moduły przed **sieci Web usług** modułu. 

Na przykład, aby przywrócić tylko oceniania wyników, a nie całą wektora danych wejściowych, należy dodać [Select Columns in Dataset] [ select-columns] modułu, aby wykluczyć wszystkich kolumn z wyjątkiem wyników oceniania. Następnie przenieś **sieci Web usług** modułu z danymi wyjściowymi [Select Columns in Dataset] [ select-columns] modułu. Eksperyment wygląda następująco:

![Przenoszenie danych wyjściowych usługi sieci web][figure5]

### <a name="add-or-remove-additional-data-processing-modules"></a>Dodawanie lub usuwanie modułów dodatkowego przetwarzania danych
Jeśli istnieje więcej modułów w eksperymencie wiadomo, że nie będą potrzebne podczas oceniania, można usunąć je. Na przykład ponieważ przenieśliśmy **sieci Web dane wejściowe usługi** modułu punkcie po modułów przetwarzania danych, można usunąć [Clean Missing Data] [ clean-missing-data] modułu na podstawie eksperyment predykcyjny.

Nasze eksperyment predykcyjny teraz wygląda następująco:

![Usuwanie dodatkowych modułów][figure6]


### <a name="add-optional-web-service-parameters"></a>Dodaj opcjonalne parametry usługi sieci Web
W niektórych przypadkach warto umożliwia użytkownikowi zmianę zachowania modułów podczas uzyskiwania dostępu do usługi usługi sieci web. *Parametry usługi sieci Web* można to zrobić.

Typowym przykładem jest konfigurowanie [i zaimportuj dane] [ import-data] modułu, więc użytkownik wdrożonej usługi sieci web można określić innego źródła danych podczas uzyskiwania dostępu do usługi sieci web. Lub konfigurowania [eksportowanie danych] [ export-data] modułu, dzięki czemu można określić inną lokalizację docelową.

Można zdefiniować parametry usługi sieci Web i skojarz je z jednego lub więcej parametrów modułu i czy są one wymagane lub opcjonalne. Użytkownik usługi sieci web zawiera wartości tych parametrów podczas uzyskiwania dostępu do usługi i działania modułu są odpowiednio zmienione.

Aby uzyskać więcej informacji o parametry usługi sieci Web i sposobu ich używania, zobacz [przy użyciu parametry usługi sieci Web Azure Machine Learning][webserviceparameters].

[webserviceparameters]: web-service-parameters.md


## <a name="deploy-the-predictive-experiment-as-a-web-service"></a>Wdrażanie eksperyment predykcyjny jako usługę sieci web
Teraz, eksperyment predykcyjny został odpowiednio przygotowany, można go wdrożyć jako usługę sieci web platformy Azure. Przy użyciu usługi sieci web, użytkownicy mogą wysyłać dane do modelu i model, którą będzie zwracać jej prognoz.

Aby uzyskać więcej informacji na temat procesu wdrażania pełną zobacz [wdrażanie usługi sieci web Azure Machine Learning][deploy]

[deploy]: publish-a-machine-learning-web-service.md


<!-- Images -->
[figure1]:./media/convert-training-experiment-to-scoring-experiment/figure1.png
[figure2]:./media/convert-training-experiment-to-scoring-experiment/figure2.png
[figure3]:./media/convert-training-experiment-to-scoring-experiment/figure3.png
[figure4]:./media/convert-training-experiment-to-scoring-experiment/figure4.png
[figure5]:./media/convert-training-experiment-to-scoring-experiment/figure5.png
[figure6]:./media/convert-training-experiment-to-scoring-experiment/figure6.png


<!-- Module References -->
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[export-data]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
