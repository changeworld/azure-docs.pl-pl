---
title: Przygotuj model do wdrożenia
titleSuffix: ML Studio (classic) - Azure
description: Jak przygotować przeszkolony model do wdrożenia jako usługę sieci Web, konwertując eksperyment Machine Learning Studio (klasyczny) do eksperymentu predykcyjnego.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.date: 03/28/2017
ms.openlocfilehash: 82db8fb2d8b8fc38542e202cc9e590663dc8bbab
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77150063"
---
# <a name="how-to-prepare-your-model-for-deployment-in-azure-machine-learning-studio-classic"></a>Jak przygotować model do wdrożenia w Azure Machine Learning Studio (klasyczny)

Azure Machine Learning Studio (klasyczny) oferuje narzędzia potrzebne do opracowania modelu analizy predykcyjnej, a następnie operacjonalizować go przez wdrożenie go jako usługi sieci Web platformy Azure.

W tym celu należy użyć programu Studio (klasycznego) do utworzenia eksperymentu o nazwie *szkolenia* , w którym można wyszkolić, oceny i edytować model. Gdy skończysz, uzyskasz gotowy do wdrożenia model, konwertując eksperyment szkoleniowy na *eksperyment predykcyjny* , który jest skonfigurowany do oceny danych użytkownika.

Przykładowy proces można zobaczyć w [samouczku 1: przewidywanie ryzyka kredytowego](tutorial-part1-credit-risk.md).

Ten artykuł przedstawia szczegółowo omówi sposób eksperymentu szkolenia są konwertowane na eksperyment predykcyjny i sposób wdrażania tego eksperyment predykcyjny. Dzięki zrozumieniu tymi szczegółowymi informacjami, nauczysz się, jak skonfigurować wdrożonego modelu się bardziej skuteczne.



## <a name="overview"></a>Omówienie 

Konwertowanie eksperymentu szkolenia na eksperyment predykcyjny proces obejmuje trzy kroki:

1. Zastąp algorytm modułów za pomocą uczonego modelu uczenia maszynowego.
2. Przytnij eksperymentu do tych modułów, które są potrzebne do oceniania. Eksperyment obejmuje pewną liczbę modułów, które są niezbędne do szkolenia, ale nie są wymagane, gdy model jest uczony.
3. Zdefiniuj sposób modelu będzie akceptować dane użytkownika usługi sieci web i jakie dane zostaną zwrócone.

> [!TIP]
> Eksperymentu szkolenia dotąd istniała zaniepokojona szkolenia i oceniania modelu przy użyciu własnych danych. Jednak po wdrożeniu użytkownicy będą wysyłać nowe dane do modelu i zwróci wyniki przewidywań. Tak jak przekonwertować eksperymentu szkolenia w eksperyment predykcyjny w celu przygotowania do wdrożenia, należy pamiętać, jak model będzie używany przez inne osoby.
> 
> 

## <a name="set-up-web-service-button"></a>Przycisk Ustaw usługę sieci Web
Po uruchomieniu eksperymentu (kliknij pozycję **Uruchom** u dołu kanwy eksperymentu) kliknij przycisk **Konfiguruj usługę sieci Web** (wybierz opcję **predykcyjnej usługi sieci Web** ). **Konfiguracja usługi sieci Web** wykonuje trzy kroki konwertowania eksperymentu szkoleniowego na eksperyment predykcyjny:

1. Model ten jest zapisywany w sekcji **przeszkolonych modeli** w palecie modułów (po lewej stronie kanwy eksperymentu). Spowoduje to zamienienie algorytmu uczenia maszynowego i [przeszkolenie modułów modelu][train-model] z zapisanym przeszkolonym modelem.
2. Analizuje eksperymentu, a usuwa modułów, które wyraźnie były używane tylko na potrzeby szkolenia i nie są już potrzebne.
3. Wstawia moduły danych wejściowych i _wyjściowych_ _usługi sieci Web_ do domyślnych lokalizacji w Twoim eksperymentie (te moduły akceptują i zwracają dane użytkownika).

Na przykład poniższego eksperymentu szkolenie modeli modelu drzewa decyzyjnego dwuklasowych przy użyciu przykładowych danych spisu:

![Eksperymentu szkolenia](./media/convert-training-experiment-to-scoring-experiment/figure1.png)

Moduły w tym eksperymencie wykonywać zasadniczo cztery różne funkcje:

![Funkcje modułu](./media/convert-training-experiment-to-scoring-experiment/figure2.png)

Podczas konwertowania tego eksperymentu szkolenia eksperyment predykcyjny niektóre moduły te nie są już potrzebne, lub teraz obsługiwać w innym celu:

* **Dane** — dane w tym przykładowym zestawie danych nie są używane podczas oceniania — użytkownik usługi sieci Web dostarczy dane do oceny. Jednak metadane z tego zestawu danych, takich jak typy danych są używane przez trenowanego modelu. Dlatego należy zachować zestawu danych w eksperyment predykcyjny, dzięki czemu umożliwia ona te metadane.

* **Przygotowanie** — w zależności od danych użytkownika, które zostaną przesłane do oceniania, te moduły mogą lub nie muszą być wymagane do przetwarzania danych przychodzących. Przycisk **Konfiguruj usługę sieci Web** nie dotyka tych — musisz zdecydować, jak chcesz je obsłużyć.
  
    Na przykład, w tym przykładzie przykładowy zestaw danych może mieć brakujące wartości, więc dołączono do nich [nieoczyszczony moduł danych][clean-missing-data] . Ponadto przykładowy zestaw danych zawiera kolumny, które nie są wymagane do nauczenia modelu. W związku z tym dodano [kolumny SELECT w module DataSet][select-columns] , aby wykluczyć te dodatkowe kolumny z przepływu danych. Jeśli wiesz, że dane, które zostaną przesłane na potrzeby oceniania za pomocą usługi sieci Web, nie mają brakujących wartości, możesz usunąć [nieczysty moduł danych][clean-missing-data] . Jednak ponieważ moduł [Wybierz kolumny w zestawie danych][select-columns] ułatwia definiowanie kolumn danych oczekiwanych przez szkolony model, ten moduł musi pozostać.

* **Uczenie** — te moduły są używane do uczenia modelu. Po kliknięciu przycisku **Konfiguruj usługę sieci Web**te moduły są zastępowane jednym modułem zawierającym przeszkolony model. Ten nowy moduł jest zapisywany w sekcji **przeszkolonych modeli** w palecie modułów.

* **Wynik** — w tym przykładzie moduł [Split Data][split] jest używany do dzielenia strumienia danych na dane testowe i dane szkoleniowe. W eksperymentie predykcyjnym nie jesteśmy już szkoleniami, więc można usunąć [dane podzielone][split] . Analogicznie, drugi moduł [modelu][score-model] oceny i moduł [oceny modelu][evaluate-model] są używane do porównywania wyników danych testowych, więc te moduły nie są konieczne w przypadku eksperymentu predykcyjnego. Moduł pozostały [model oceny][score-model] jest jednak wymagany do zwrócenia wyniku oceny przez usługę sieci Web.

Oto jak wygląda przykład po kliknięciu przycisku **Skonfiguruj usługę sieci Web**:

![Przekonwertowana eksperyment predykcyjny](./media/convert-training-experiment-to-scoring-experiment/figure3.png)

Działanie wykonywane przez **usługę sieci Web** może być wystarczające, aby przygotować eksperyment do wdrożenia jako usługę sieci Web. Można jednak wykonania dodatkowych czynności dodatkowej specyficzne dla swojego eksperymentu.

### <a name="adjust-input-and-output-modules"></a>Dostosuj modułów wejściowych i wyjściowych
W eksperymentu szkolenia używany zestaw danych szkoleniowych, a następnie została część przetwarzania, aby uzyskać dane w postaci i potrzebny Algorytm uczenia maszynowego. Jeśli dane, które mają zostać odebrane przez usługę sieci Web, nie będą potrzebne do tego przetwarzania, można je ominąć: Połącz dane wyjściowe **modułu danych wejściowych usługi sieci Web** z innym modułem w Twoim eksperymentie. Dane użytkownika teraz pojawić się w modelu, w tym miejscu.

Na przykład **Usługa sieci Web** domyślnie umieszcza moduł **wejściowy usługi sieci Web** w górnej części przepływu danych, jak pokazano na rysunku powyżej. Można jednak ręcznie ustawić dane **wejściowe usługi sieci Web** za pomocą modułów przetwarzania danych:

![Przenoszenie danych wejściowych usługi internetowej](./media/convert-training-experiment-to-scoring-experiment/figure4.png)

Dane wejściowe, dostępne za pośrednictwem usługi sieci web będzie teraz przekazać bezpośrednio do modułu Score Model bez żadnych przetwarzania wstępnego.

Podobnie domyślnie **Konfiguracja usługi** sieci Web powoduje umieszczenie modułu wyjściowego usługi sieci Web u dołu przepływu danych. W tym przykładzie usługa sieci Web zwróci do użytkownika dane wyjściowe modułu [modelu wynikowego][score-model] , który zawiera kompletny wektor danych wejściowych i wyniki oceniania.
Jeśli jednak wolisz zwrócić coś innego, możesz dodać kolejne moduły przed modułem **wyjściowym usługi sieci Web** . 

Aby na przykład zwrócić tylko wyniki oceny, a nie cały wektor danych wejściowych, Dodaj do modułu DataSet ( [Wybieranie kolumn w zestawie danych][select-columns] ) wszystkie kolumny z wyjątkiem wyników oceniania. Następnie Przenieś moduł **wyjściowy usługi sieci Web** do danych wyjściowych modułu [SELECT Columns in DataSet][select-columns] . Eksperyment wygląda następująco:

![Przenoszenie danych wyjściowych usługi sieci web](./media/convert-training-experiment-to-scoring-experiment/figure5.png)

### <a name="add-or-remove-additional-data-processing-modules"></a>Dodawanie lub usuwanie modułów dodatkowego przetwarzania danych
Jeśli istnieje więcej modułów w eksperymencie, że wiesz, że nie będą potrzebne podczas oceniania, można usunąć je. Na przykład ze względu na to, że moduł danych **wejściowych usługi sieci Web** został przeniesiony do punktu po modułach przetwarzania danych, możemy usunąć [nieczysty moduł danych][clean-missing-data] z eksperymentu predykcyjnego.

Nasz eksperyment predykcyjny wygląda teraz następująco:

![Usuwanie dodatkowych modułów](./media/convert-training-experiment-to-scoring-experiment/figure6.png)


### <a name="add-optional-web-service-parameters"></a>Dodaj opcjonalne parametry usługi sieci Web
W niektórych przypadkach warto umożliwić użytkownikowi zmienić zachowanie modułów podczas uzyskiwania dostępu do usługi Usługa sieci web. *Parametry usługi sieci Web* umożliwiają wykonanie tej czynności.

Typowym przykładem jest skonfigurowanie modułu [importowania danych][import-data] , dzięki czemu użytkownik wdrożonej usługi sieci Web może określić inne źródło danych podczas uzyskiwania dostępu do usługi sieci Web. Lub skonfiguruj moduł [eksportu danych][export-data] , aby można było określić inną lokalizację docelową.

Można zdefiniować parametry usługi sieci Web i skojarzyć je z co najmniej jeden parametr modułu i czy są one wymagane lub opcjonalne. Użytkownik usługi sieci web zawiera wartości dla parametrów podczas uzyskiwania dostępu do usługi i akcje moduł są odpowiednio modyfikowane.

Więcej informacji o parametrach usługi sieci Web i sposobach ich użycia można znaleźć w temacie [Using Azure Machine Learning Web Service Parameters][webserviceparameters].

[webserviceparameters]: web-service-parameters.md


## <a name="deploy-the-predictive-experiment-as-a-web-service"></a>Wdrożenie eksperymentu predykcyjnego jako usługi internetowej
Teraz, gdy został odpowiednio przygotowany eksperyment predykcyjny, możesz go wdrożyć jako usługę sieci web platformy Azure. Przy użyciu usługi sieci web, użytkownicy mogą wysyłać dane do modelu i model zwróci jego prognozy.

Aby uzyskać więcej informacji na temat kompletnego procesu wdrażania, zobacz [wdrażanie usługi sieci web Azure Machine Learning][deploy]

[deploy]: deploy-a-machine-learning-web-service.md

<!-- Module References -->
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[export-data]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
