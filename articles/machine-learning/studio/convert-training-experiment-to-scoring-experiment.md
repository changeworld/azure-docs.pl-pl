---
title: Przygotowanie modelu do wdrożenia
titleSuffix: ML Studio (classic) - Azure
description: Jak przygotować przeszkolony model do wdrożenia jako usługa sieci web, konwertując eksperyment szkoleniowy machine learning studio (klasyczny) na eksperyment predykcyjny.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 03/28/2017
ms.openlocfilehash: 061c340f8c4952d5a0f2a3873f7475e4f733c290
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204515"
---
# <a name="how-to-prepare-your-model-for-deployment-in-azure-machine-learning-studio-classic"></a>Jak przygotować model do wdrożenia w usłudze Azure Machine Learning Studio (klasyczny)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Usługa Azure Machine Learning Studio (klasyczna) udostępnia narzędzia potrzebne do opracowania modelu analizy predykcyjnej, a następnie operacjonalizacji go, wdrażając go jako usługę sieci Web platformy Azure.

Aby to zrobić, użyj Studio (classic) do utworzenia eksperymentu - o nazwie *eksperyment szkolenia* - gdzie trenować, ocena i edytować model. Po zakończeniu tworzenia danych modelu można przygotować do wdrożenia, konwertując eksperyment szkoleniowy na *eksperyment predykcyjny* skonfigurowany do oceniania danych użytkownika.

Przykład tego procesu można zobaczyć w [samouczku 1: Przewidywanie ryzyka kredytowego](tutorial-part1-credit-risk.md).

W tym artykule opisano szczegółowo, w jaki sposób eksperyment szkoleniowy zostanie przekształcony w eksperyment predykcyjny i jak wdrożono ten eksperyment predykcyjny. Dzięki zrozumieniu tych szczegółów można dowiedzieć się, jak skonfigurować wdrożony model, aby uczynić go bardziej skutecznym.



## <a name="overview"></a>Omówienie 

Proces konwersji eksperymentu szkoleniowego na eksperyment predykcyjny obejmuje trzy kroki:

1. Zastąp moduły algorytmu uczenia maszynowego na przeszkolonym modelu.
2. Przytnij eksperyment tylko do tych modułów, które są potrzebne do oceniania. Eksperyment szkoleniowy zawiera szereg modułów, które są niezbędne do szkolenia, ale nie są potrzebne po przeszkoleniu modelu.
3. Zdefiniuj sposób akceptowania danych przez użytkownika usługi sieci web i jakie dane zostaną zwrócone.

> [!TIP]
> W eksperymencie treningowym zajmujesz się szkoleniem i ocenianiem modelu przy użyciu własnych danych. Ale po wdrożeniu użytkownicy będą wysyłać nowe dane do modelu i zwróci wyniki prognozowania. Tak jak konwertować eksperyment szkolenia do eksperymentu predykcyjnego, aby przygotować go do wdrożenia, należy pamiętać, jak model będzie używany przez innych.
> 
> 

## <a name="set-up-web-service-button"></a>Przycisk Konfigurowanie usługi sieci Web
Po uruchomieniu eksperymentu (kliknij przycisk **URUCHOM** u dołu obszaru roboczego eksperymentu) kliknij przycisk **Konfiguruj usługę sieci Web** (wybierz opcję **Predykcyjna usługa sieci Web).** **Konfigurowanie usługi sieci Web** wykonuje trzy kroki konwersji eksperymentu szkoleniowego na eksperyment predykcyjny:

1. Zapisuje przeszkolony model w sekcji **Wyszkolone modele** palety modułu (po lewej stronie kanwy eksperymentu). Następnie zastępuje algorytm uczenia maszynowego i [moduły modelu pociągu][train-model] zapisanym wyszkolonym modelem.
2. Analizuje eksperyment i usuwa moduły, które były wyraźnie używane tylko do treningu i nie są już potrzebne.
3. Wstawia moduły _wejściowe_ i _wyjściowe_ usługi sieci Web do domyślnych lokalizacji w eksperymencie (moduły te akceptują i zwracają dane użytkownika).

Na przykład następujący eksperyment trenuje dwuklasowy model drzewa decyzji z premią przy użyciu przykładowych danych spisowych:

![Eksperyment szkoleniowy](./media/convert-training-experiment-to-scoring-experiment/figure1.png)

Moduły w tym eksperymencie wykonują w zasadzie cztery różne funkcje:

![Funkcje modułu](./media/convert-training-experiment-to-scoring-experiment/figure2.png)

Podczas konwertowania tego eksperymentu szkoleniowego na eksperyment predykcyjny niektóre z tych modułów nie są już potrzebne lub służą teraz innemu celowi:

* **Dane** — dane w tym przykładowym zestawie danych nie są używane podczas oceniania — użytkownik usługi sieci web dostarczy dane do oceny. Jednak metadane z tego zestawu danych, takie jak typy danych, jest używany przez model przeszkolony. Dlatego należy zachować zestaw danych w eksperymencie predykcyjnym, aby mógł dostarczyć te metadane.

* **Przygotowanie** — w zależności od danych użytkownika, które zostaną przesłane do oceniania, moduły te mogą lub nie muszą być konieczne do przetworzenia danych przychodzących. Przycisk **Skonfiguruj usługę sieci Web** nie dotyka tych — musisz zdecydować, jak chcesz sobie z nimi radzić.
  
    Na przykład w tym przykładzie przykładowy zestaw danych może mieć brakujące wartości, więc [moduł Clean Missing Data][clean-missing-data] został dołączony do radzenia sobie z nimi. Ponadto przykładowy zestaw danych zawiera kolumny, które nie są potrzebne do szkolenia modelu. Tak więc [wybierz kolumny w][select-columns] module zestawu danych został dołączony do wykluczenia tych dodatkowych kolumn z przepływu danych. Jeśli wiesz, że dane, które zostaną przesłane do oceniania za pośrednictwem usługi sieci web nie będą miały brakujących wartości, możesz usunąć moduł [Czyste brakujące dane.][clean-missing-data] Jednak ponieważ [Wybierz kolumny w module Zestawu danych][select-columns] pomaga zdefiniować kolumny danych, które oczekuje przeszkolony model, moduł ten musi pozostać.

* **Pociąg** — moduły te są używane do szkolenia modelu. Po **kliknięciu przycisku Skonfiguruj usługę sieci Web**moduły te zostaną zastąpione pojedynczym modułem zawierającym przeszkolony model. Ten nowy moduł jest zapisywany w sekcji **Modele wyszkolone** w palecie modułów.

* **Wynik** — w tym przykładzie [moduł Podziel dane][split] służy do dzielenia strumienia danych na dane testowe i dane szkoleniowe. W eksperymencie predykcyjnym nie trenujemy już, więc [dane podziału][split] mogą zostać usunięte. Podobnie drugi moduł [modelu wynikowego][score-model] i [oceń model][evaluate-model] modułu są używane do porównywania wyników z danych testowych, więc te moduły nie są potrzebne w eksperymencie predykcyjnym. Pozostały [wynik modelu][score-model] modułu, jednak jest potrzebne do zwrócenia wyniku za pośrednictwem usługi sieci web.

Oto jak wygląda nasz przykład po kliknięciu przycisku **Skonfiguruj usługę sieci Web:**

![Przekonwertowany eksperyment predykcyjny](./media/convert-training-experiment-to-scoring-experiment/figure3.png)

Praca wykonana przez **usługę Konfigurowanie sieci Web** może być wystarczająca do przygotowania eksperymentu do wdrożenia jako usługi sieci web. Warto jednak wykonać dodatkową pracę specyficzną dla eksperymentu.

### <a name="adjust-input-and-output-modules"></a>Dostosowywanie modułów wejściowych i wyjściowych
W eksperymencie szkolenia użyto zestawu danych szkoleniowych, a następnie przeprowadzono pewne przetwarzanie, aby uzyskać dane w formie, której potrzebny jest algorytm uczenia maszynowego. Jeśli dane, które mają być odbierane za pośrednictwem usługi sieci web, nie będą wymagały tego przetwarzania, można je pominąć: połącz dane wyjściowe **modułu wejściowego usługi sieci Web** z innym modułem w eksperymencie. Dane użytkownika zostaną teraz opublikowane w modelu w tej lokalizacji.

Na przykład domyślnie **Set Up Web Service** umieszcza moduł **wejściowy usługi sieci Web** w górnej części przepływu danych, jak pokazano na rysunku powyżej. Ale możemy ręcznie umieścić **dane wejściowe usługi sieci Web** obok modułów przetwarzania danych:

![Przenoszenie danych wejściowych usługi sieci Web](./media/convert-training-experiment-to-scoring-experiment/figure4.png)

Dane wejściowe dostarczane za pośrednictwem usługi sieci web będą teraz przekazywane bezpośrednio do modułu Score Model bez wstępnego przetwarzania.

Podobnie domyślnie **Set Up Web Service** umieszcza moduł wyjściowy usługi sieci Web u dołu przepływu danych. W tym przykładzie usługa sieci web zwróci użytkownikowi dane wyjściowe modułu [Score Model,][score-model] który zawiera pełny wektor danych wejściowych oraz wyniki oceniania.
Jeśli jednak wolisz zwrócić coś innego, możesz dodać dodatkowe moduły przed modułem **wyjściowym usługi sieci Web.** 

Na przykład, aby zwrócić tylko wyniki oceniania, a nie cały wektor danych wejściowych, dodaj wybierz kolumny w module [Zestawu danych,][select-columns] aby wykluczyć wszystkie kolumny z wyjątkiem wyników oceniania. Następnie przenieś moduł **wyjściowy usługi sieci Web** na dane wyjściowe modułu Wybierz kolumny w module [Zestawu danych.][select-columns] Eksperyment wygląda następująco:

![Przenoszenie danych wyjściowych usługi sieci web](./media/convert-training-experiment-to-scoring-experiment/figure5.png)

### <a name="add-or-remove-additional-data-processing-modules"></a>Dodawanie lub usuwanie dodatkowych modułów przetwarzania danych
Jeśli w eksperymencie jest więcej modułów, o których wiadomo, że nie będą potrzebne podczas oceniania, można je usunąć. Na przykład, ponieważ przenieśliśmy moduł **wejściowy usługi sieci Web** do punktu po modułach przetwarzania danych, możemy usunąć moduł [Clean Missing Data][clean-missing-data] z eksperymentu predykcyjnego.

Nasz eksperyment predykcyjny wygląda teraz następująco:

![Usuwanie dodatkowego modułu](./media/convert-training-experiment-to-scoring-experiment/figure6.png)


### <a name="add-optional-web-service-parameters"></a>Dodawanie opcjonalnych parametrów usługi sieci Web
W niektórych przypadkach można zezwolić użytkownikowi usługi sieci web na zmianę zachowania modułów, gdy usługa jest dostępna. *Parametry usługi sieci Web* umożliwiają to zrobić.

Typowym przykładem jest skonfigurowanie [modułu Importuj dane,][import-data] aby użytkownik wdrożonej usługi sieci web mógł określić inne źródło danych podczas uzyskiwania dostępu do usługi sieci web. Lub konfigurowanie modułu [Eksportuj dane,][export-data] tak aby można było określić inne miejsce docelowe.

Można zdefiniować parametry usługi sieci Web i skojarzyć je z co najmniej jednym parametrem modułu i określić, czy są one wymagane, czy opcjonalne. Użytkownik usługi sieci web podaje wartości dla tych parametrów, gdy usługa jest dostępna, a akcje modułu są odpowiednio modyfikowane.

Aby uzyskać więcej informacji na temat parametrów usługi sieci Web i sposobu ich używania, zobacz [Korzystanie z parametrów usługi azure machine learning web .][webserviceparameters]

[webserviceparameters]: web-service-parameters.md


## <a name="deploy-the-predictive-experiment-as-a-web-service"></a>Wdrożenie eksperymentu predykcyjnego jako usługi internetowej
Teraz, gdy eksperyment predykcyjny został wystarczająco przygotowany, można go wdrożyć jako usługę sieci web platformy Azure. Za pomocą usługi sieci web użytkownicy mogą wysyłać dane do modelu, a model zwróci jego prognoz.

Aby uzyskać więcej informacji na temat pełnego procesu wdrażania, zobacz [Wdrażanie usługi sieci Web usługi Azure Machine Learning][deploy]

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
