---
title: Przygotowanie modelu wdrożenia
titleSuffix: Azure Machine Learning Studio
description: Jak przygotować uczonego modelu do wdrożenia jako usługę sieci web przy użyciu konwertowanie eksperymentu szkolenia usługi Machine Learning Studio na eksperyment predykcyjny.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.date: 03/28/2017
ms.openlocfilehash: 2a318edada5cdc4124e221fdc8c441ab323a9289
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60751974"
---
# <a name="how-to-prepare-your-model-for-deployment-in-azure-machine-learning-studio"></a>Jak przygotować modelu wdrożenia w usłudze Azure Machine Learning Studio

Usługa Azure Machine Learning Studio zapewnia narzędzia potrzebne do opracowania modelu analizy predykcyjnej i operacjonalizacji go, wdrażając go jako usługę sieci web platformy Azure.

Aby to zrobić, użyj Studio do tworzenia eksperymentu - o nazwie *eksperymentu szkolenia* — gdzie szkolenie, ocena i edytowanie modelu. Po zakończeniu, otrzymasz modelu gotowe do wdrożenia przez konwertowanie eksperymentu szkolenia do *eksperyment predykcyjny* skonfigurowanego na dane użytkownika wynik.

Możesz zobaczyć przykład ten proces w [samouczek 1: Prognozowanie ryzyka kredytowego](tutorial-part1-credit-risk.md).

Ten artykuł przedstawia szczegółowo omówi sposób eksperymentu szkolenia są konwertowane na eksperyment predykcyjny i sposób wdrażania tego eksperyment predykcyjny. Dzięki zrozumieniu tymi szczegółowymi informacjami, nauczysz się, jak skonfigurować wdrożonego modelu się bardziej skuteczne.



## <a name="overview"></a>Przegląd 

Konwertowanie eksperymentu szkolenia na eksperyment predykcyjny proces obejmuje trzy kroki:

1. Zastąp algorytm modułów za pomocą uczonego modelu uczenia maszynowego.
2. Przytnij eksperymentu do tych modułów, które są potrzebne do oceniania. Eksperyment obejmuje pewną liczbę modułów, które są niezbędne do szkolenia, ale nie są wymagane, gdy model jest uczony.
3. Zdefiniuj sposób modelu będzie akceptować dane użytkownika usługi sieci web i jakie dane zostaną zwrócone.

> [!TIP]
> Eksperymentu szkolenia dotąd istniała zaniepokojona szkolenia i oceniania modelu przy użyciu własnych danych. Jednak po wdrożeniu użytkownicy będą wysyłać nowe dane do modelu i zwróci wyniki przewidywań. Tak jak przekonwertować eksperymentu szkolenia w eksperyment predykcyjny w celu przygotowania do wdrożenia, należy pamiętać, jak model będzie używany przez inne osoby.
> 
> 

## <a name="set-up-web-service-button"></a>Przycisk Ustaw usługę sieci Web
Po uruchomieniu eksperymentu (kliknij **Uruchom** w dolnej części obszaru roboczego eksperymentu), kliknij przycisk **ustawić usługę sieci Web** przycisku (wybierz **predykcyjne usługi sieci Web** opcji). **Ustaw usługę sieci Web** wykonuje dla Ciebie konwertowanie eksperymentu szkolenia na eksperyment predykcyjny trzy kroki:

1. Zapisuje uczonego modelu w **przeszkolone modele** części palety modułów (do lewej strony obszaru roboczego eksperymentu). Następnie zastępuje algorytmu uczenia maszynowego i [Train Model] [ train-model] modułów z zapisanych trenowanego modelu.
2. Analizuje eksperymentu, a usuwa modułów, które wyraźnie były używane tylko na potrzeby szkolenia i nie są już potrzebne.
3. Wstawia _sieci Web dane wejściowe usługi_ i _dane wyjściowe_ moduły do domyślne lokalizacje w eksperymencie (te moduły akceptują i zwracają dane użytkownika).

Na przykład poniższego eksperymentu szkolenie modeli modelu drzewa decyzyjnego dwuklasowych przy użyciu przykładowych danych spisu:

![Eksperymentu szkolenia](./media/convert-training-experiment-to-scoring-experiment/figure1.png)

Moduły w tym eksperymencie wykonywać zasadniczo cztery różne funkcje:

![Funkcje modułu](./media/convert-training-experiment-to-scoring-experiment/figure2.png)

Podczas konwertowania tego eksperymentu szkolenia eksperyment predykcyjny niektóre moduły te nie są już potrzebne, lub teraz obsługiwać w innym celu:

* **Dane** — dane w tym przykładowym zestawie danych nie jest używana podczas oceniania — użytkownik usługi sieci web będzie dostarczać dane, które mają zostać ocenione. Jednak metadane z tego zestawu danych, takich jak typy danych są używane przez trenowanego modelu. Dlatego należy zachować zestawu danych w eksperyment predykcyjny, dzięki czemu umożliwia ona te metadane.

* **Przygotowywanie** — w zależności od danych użytkownika, które zostaną przesłane do oceniania, te moduły mogą lub nie może być konieczne przetwarzania przychodzących danych. **Ustawić usługę sieci Web** przycisku nie touch te — należy zdecydować, jaki chcesz je obsłużyć.
  
    Na przykład, w tym przykładzie przykładowego zestawu danych może mieć wartości Brak, więc [Clean Missing Data] [ clean-missing-data] moduł został dołączony do czynienia z nimi. Ponadto przykładowy zestaw danych zawiera kolumny, które nie są wymagane do nauczenia modelu. Dlatego [Select Columns in Dataset] [ select-columns] moduł został uwzględniony, aby wykluczyć te dodatkowe kolumny z przepływu danych. Jeśli wiesz, że dane, które zostaną przesłane do oceniania przez usługę sieci web nie będzie brakujące wartości, a następnie można usunąć [Clean Missing Data] [ clean-missing-data] modułu. Jednak ponieważ [Select Columns in Dataset] [ select-columns] modułu pomaga zdefiniować kolumny danych, która oczekuje trenowanego modelu, powinna być ten moduł.

* **Szkolenie** — te moduły są używane do nauczenia modelu. Po kliknięciu **ustawić usługę sieci Web**, te moduły są zastępowane pojedynczy moduł, który zawiera model szkolenia. Ten nowy moduł jest zapisywany w **przeszkolone modele** części palety modułów.

* **Wynik** — w tym przykładzie [podziału danych] [ split] moduł jest używany do dzielenia strumienia danych na dane testowe oraz dane szkoleniowe. W eksperyment predykcyjny, firma Microsoft nie uczony, więc [podziału danych] [ split] może zostać usunięty. Podobnie, drugi [Score Model] [ score-model] modułu i [Evaluate Model] [ evaluate-model] moduł są używane do porównywania wyników z danych testowych, więc te moduły nie są potrzebne w eksperyment predykcyjny. Pozostałe [Score Model] [ score-model] modułu, jednak nie jest konieczna zwracanie wyniku oceny za pośrednictwem usługi sieci web.

Oto jak wygląda nasz przykład po kliknięciu przycisku **ustawić usługę sieci Web**:

![Przekonwertowana eksperyment predykcyjny](./media/convert-training-experiment-to-scoring-experiment/figure3.png)

Pracy wykonanej przez **ustawić usługę sieci Web** może być wystarczające, aby przygotować eksperymentu, który można wdrożyć jako usługę sieci web. Można jednak wykonania dodatkowych czynności dodatkowej specyficzne dla swojego eksperymentu.

### <a name="adjust-input-and-output-modules"></a>Dostosuj modułów wejściowych i wyjściowych
W eksperymentu szkolenia używany zestaw danych szkoleniowych, a następnie została część przetwarzania, aby uzyskać dane w postaci i potrzebny Algorytm uczenia maszynowego. Jeśli dane oczekuje się za pośrednictwem usługi sieci web nie będzie potrzebował tego przetwarzania, można pominąć go: Połącz wyjście **danych wejściowych modułu usługi sieci Web** do innego modułu w eksperymencie. Dane użytkownika teraz pojawić się w modelu, w tym miejscu.

Na przykład domyślnie **ustawić usługę sieci Web** umieszcza **sieci Web dane wejściowe usługi** modułu w górnej części przepływu danych, jak pokazano na rysunku powyżej. Ale możemy ręcznie pozycji **sieci Web dane wejściowe usługi** ostatnie modułów danych przetwarzania:

![Przenoszenie danych wejściowych usługi internetowej](./media/convert-training-experiment-to-scoring-experiment/figure4.png)

Dane wejściowe, dostępne za pośrednictwem usługi sieci web będzie teraz przekazać bezpośrednio do modułu Score Model bez żadnych przetwarzania wstępnego.

Podobnie, domyślnie **ustawić usługę sieci Web** umieszcza modułu danych wyjściowych usługi sieci Web w dolnej części przepływu danych. W tym przykładzie Usługa sieci web zwróci użytkownikowi dane wyjściowe [Score Model] [ score-model] moduł, który obejmuje wektor kompletne dane wejściowe oraz wyniki oceny.
Jednakże, jeśli chcesz użyć zwrócić coś innego, następnie można dodać dodatkowe moduły przed **sieci Web usługi danych wyjściowych** modułu. 

Na przykład, aby zwrócić tylko oceniania wyników, a nie całej wektor dane wejściowe, należy dodać [Select Columns in Dataset] [ select-columns] modułu, aby wykluczyć wszystkie kolumny z wyjątkiem oceniania wyników. Następnie przenieś **sieci Web usługi danych wyjściowych** modułu z danymi wyjściowymi [Select Columns in Dataset] [ select-columns] modułu. Eksperyment wygląda następująco:

![Przenoszenie danych wyjściowych usługi sieci web](./media/convert-training-experiment-to-scoring-experiment/figure5.png)

### <a name="add-or-remove-additional-data-processing-modules"></a>Dodawanie lub usuwanie modułów dodatkowego przetwarzania danych
Jeśli istnieje więcej modułów w eksperymencie, że wiesz, że nie będą potrzebne podczas oceniania, można usunąć je. Na przykład ponieważ przeszliśmy **sieci Web dane wejściowe usługi** modułu do punktu po modułów przetwarzania danych, możemy usunąć [Clean Missing Data] [ clean-missing-data] modułu na podstawie eksperyment predykcyjny.

Nasz eksperyment predykcyjny wygląda teraz następująco:

![Usuwanie dodatkowych modułów](./media/convert-training-experiment-to-scoring-experiment/figure6.png)


### <a name="add-optional-web-service-parameters"></a>Dodaj opcjonalne parametry usługi sieci Web
W niektórych przypadkach warto umożliwić użytkownikowi zmienić zachowanie modułów podczas uzyskiwania dostępu do usługi Usługa sieci web. *Parametry usługi w sieci Web* pozwalają w tym celu.

Typowym przykładem jest konfigurowanie [importu danych] [ import-data] modułu, dzięki czemu użytkownik wdrożonej usługi sieci web można określić inne źródło danych, podczas uzyskiwania dostępu do usługi sieci web. Lub konfigurowanie [Eksport danych] [ export-data] modułu, aby można określić inną lokalizację docelową.

Można zdefiniować parametry usługi sieci Web i skojarzyć je z co najmniej jeden parametr modułu i czy są one wymagane lub opcjonalne. Użytkownik usługi sieci web zawiera wartości dla parametrów podczas uzyskiwania dostępu do usługi i akcje moduł są odpowiednio modyfikowane.

Aby uzyskać więcej informacji o parametry usługi sieci Web i sposobu ich używania, zobacz [przy użyciu parametry usługi sieci Web Azure Machine Learning][webserviceparameters].

[webserviceparameters]: web-service-parameters.md


## <a name="deploy-the-predictive-experiment-as-a-web-service"></a>Wdrożyć eksperyment predykcyjny jako usługę sieci web
Teraz, gdy został odpowiednio przygotowany eksperyment predykcyjny, możesz go wdrożyć jako usługę sieci web platformy Azure. Przy użyciu usługi sieci web, użytkownicy mogą wysyłać dane do modelu i model zwróci jego prognozy.

Aby uzyskać więcej informacji na temat procesu całego procesu wdrażania, zobacz [wdrażanie usługi sieci web Azure Machine Learning][deploy]

[deploy]: publish-a-machine-learning-web-service.md

<!-- Module References -->
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[export-data]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
