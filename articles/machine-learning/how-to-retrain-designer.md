---
title: Przekwalifikowanie modeli przy użyciu projektanta usługi Azure Machine Learning (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak przeszkolić modele za pomocą opublikowanych potoków w projektancie usługi Azure Machine Learning (wersja zapoznawcza).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: keli19
author: likebupt
ms.date: 04/06/2020
ms.openlocfilehash: 721e5414fc4753cd5d58a17fc7ed51ea99868778
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80810355"
---
# <a name="retrain-models-with-azure-machine-learning-designer-preview"></a>Ponowne trenowanie modeli za pomocą projektanta usługi Azure Machine Learning (wersja zapoznawcza)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

W tym artykule opisano, jak używać projektanta usługi Azure Machine Learning do ponownego przeszkolenia modelu uczenia maszynowego. Użyjesz opublikowanych potoków do automatyzacji przepływu pracy i ustawić parametry, aby trenować model na nowych danych. 

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Trenuj model uczenia maszynowego.
> * Utwórz parametr potoku.
> * Opublikuj swój potok szkolenia.
> * Przekwalifikowanie modelu z nowymi parametrami.

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Azure Machine Learning z jednostką SKU przedsiębiorstwa.
* Zestaw danych dostępny dla projektanta. Może to być jedna z następujących czynności:
   * Zarejestrowany zestaw danych usługi Azure Machine Learning
    
     **-lub-**
   * Plik danych przechowywany w magazynie danych usługi Azure Machine Learning.
   
Aby uzyskać informacje na temat dostępu do danych przy użyciu projektanta, zobacz [Jak zaimportować dane do projektanta](how-to-designer-import-data.md).

W tym artykule przyjęto również założenie, że masz podstawową wiedzę na temat tworzenia potoków w projektancie. Aby zapoznać się z wprowadzeniem z przewodnikiem, ukończ [samouczek](tutorial-designer-automobile-price-train-score.md). 

### <a name="sample-pipeline"></a>Przykładowy potok

Potok używany w tym artykule jest zmienioną wersją [próbki 3: Przewidywanie dochodów](samples-designer.md#classification-samples). Potok używa [importu danych](algorithm-module-reference/import-data.md) modułu zamiast przykładowego zestawu danych, aby pokazać, jak trenować modele przy użyciu własnych danych.

![Zrzut ekranu przedstawiający zmodyfikowany potok próbek z polem wyróżniającym moduł Importuj dane](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="create-a-pipeline-parameter"></a>Tworzenie parametru potoku

Tworzenie parametrów potoku do dynamicznego ustawiania zmiennych w czasie wykonywania. W tym przykładzie zmienisz ścieżkę danych szkoleniowych ze stałej wartości na parametr, dzięki czemu można ponownie trenować model na różnych danych.

1. Wybierz moduł **Importuj dane.**

    > [!NOTE]
    > W tym przykładzie użyto importu danych modułu dostępu do danych w zarejestrowanym magazynie danych. Jednak można wykonać podobne kroki, jeśli używasz wzorców dostępu do danych alternatywnych.

1. W okienku szczegółów modułu po prawej stronie kanwy wybierz źródło danych.

1. Wprowadź ścieżkę do danych. Możesz również wybrać **opcję Przeglądaj ścieżkę,** aby przeglądać drzewo plików. 

1. Najedź myszą na pole **Ścieżka** i zaznacz elipsy nad wyświetlonym polem **Ścieżka.**

    ![Zrzut ekranu przedstawiający sposób tworzenia parametru potoku](media/how-to-retrain-designer/add-pipeline-parameter.png)

1. Wybierz **polecenie Dodaj do parametru potoku**.

1. Podaj nazwę parametru i wartość domyślną.

   > [!NOTE]
   > Parametry potoku można sprawdzić i edytować, wybierając ikonę Przekładni **Ustawienia** obok tytułu wersji roboczej potoku. 

1. Wybierz **pozycję Zapisz**.

1. Prześlij przebieg potoku.

## <a name="find-a-trained-model"></a>Znajdowanie wyszkolonego modelu

Projektant zapisuje wszystkie dane wyjściowe potoku, w tym modele uczonych, na domyślnym koncie magazynu obszaru roboczego. Można również uzyskać dostęp do wyszkolonych modeli bezpośrednio w projektancie:

1. Poczekaj na zakończenie działania potoku.
1. Wybierz moduł **Train Model** (Trenowanie modelu).
1. W okienku szczegółów modułu po prawej stronie obszaru roboczego wybierz pozycję **Wyjścia + dzienniki**.
1. Model można znaleźć w **innych wyjść** wraz z dzienników uruchamiania.
1. Alternatywnie wybierz ikonę **Wyświetl dane wyjściowe.** W tym miejscu można wykonać instrukcje w oknie dialogowym, aby przejść bezpośrednio do magazynu danych. 

![Zrzut ekranu przedstawiający sposób pobierania przeszkolonego modelu](./media/how-to-retrain-designer/trained-model-view-output.png)

## <a name="publish-a-training-pipeline"></a>Publikowanie potoku szkoleniowego

Opublikuj potok do punktu końcowego potoku, aby łatwo ponownie użyć potoków w przyszłości. Punkt końcowy potoku tworzy punkt końcowy REST do wywołania potoku w przyszłości. W tym przykładzie punktu końcowego potoku umożliwia ponowne użycie potoku do ponownego przeszkolenia modelu na różnych danych.

1. Wybierz **pozycję Publikuj** nad kanwą projektanta.
1. Wybierz lub utwórz punkt końcowy potoku.

   > [!NOTE]
   > Można opublikować wiele potoków do jednego punktu końcowego. Każdy potok w danym punkcie końcowym otrzymuje numer wersji, który można określić podczas wywoływania punktu końcowego potoku.

1. Wybierz pozycję **Publikuj**.

## <a name="retrain-your-model"></a>Przekwalifikowanie modelu

Teraz, gdy masz opublikowany potok szkolenia, można go użyć do ponownego przeszkolenia modelu na nowe dane. Można przesłać przebiegi z punktu końcowego potoku z obszaru roboczego studio lub programowo.

### <a name="submit-runs-by-using-the-designer"></a>Przesyłanie przebiegów przy użyciu projektanta

Aby przesłać sparametryzowany przebieg punktu końcowego potoku od projektanta, należy wykonać następujące kroki:

1. Przejdź do strony **Punkty końcowe** w obszarze roboczym studio.
1. Wybierz kartę **Punkty końcowe potoku.** Następnie wybierz punkt końcowy potoku.
1. Wybierz kartę **Opublikowane potoki.** Następnie wybierz wersję potoku, którą chcesz uruchomić.
1. Wybierz pozycję **Prześlij**.
1. W oknie dialogowym instalacji można określić wartości parametrów dla przebiegu. W tym przykładzie zaktualizuj ścieżkę danych, aby trenować model przy użyciu zestawu danych spoza USA.

![Zrzut ekranu przedstawiający sposób konfigurowania sparametryzowanego przebiegu potoku w projektancie](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-by-using-code"></a>Przesyłanie przebiegów przy użyciu kodu

Punkt końcowy REST opublikowanego potoku można znaleźć w panelu przeglądu. Wywołując punkt końcowy, można ponownie trenować opublikowany potok.

Aby nawiązać połączenie REST, potrzebujesz nagłówka uwierzytelniania typu okaziciela OAuth 2.0. Aby uzyskać informacje dotyczące konfigurowania uwierzytelniania w obszarze roboczym i wykonywania sparametryzowanego wywołania REST, zobacz [Tworzenie potoku usługi Azure Machine Learning do oceniania wsadowego.](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint)

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się, jak utworzyć sparametryzowany punkt końcowy potoku szkolenia przy użyciu projektanta.

Aby uzyskać pełne wskazówki dotyczące sposobu wdrażania modelu do prognoz, zobacz [samouczek projektanta](tutorial-designer-automobile-price-train-score.md) do szkolenia i wdrażania modelu regresji.
