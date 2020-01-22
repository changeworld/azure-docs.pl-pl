---
title: Ponowne uczenie modeli przy użyciu narzędzia Azure Machine Learning Designer
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak ponownie szkolić modele z opublikowanymi potokami w programie Azure Machine Learning Designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: peterlu
author: peterclu
ms.date: 12/15/2019
ms.openlocfilehash: d1382da739fd8ca56d4cc53c2c302331bdfbf1c3
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76311873"
---
# <a name="retrain-models-with-azure-machine-learning-designer"></a>Ponowne uczenie modeli za pomocą narzędzia Azure Machine Learning Designer
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym instruktażu dowiesz się, jak za pomocą narzędzia Azure Machine Learning Designer ponownie przeprowadzić uczenie modelu uczenia maszynowego. Dowiedz się, jak używać opublikowanych potoków do automatyzowania przepływów pracy uczenia maszynowego na potrzeby ponownego szkolenia.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Uczenie modelu uczenia maszynowego.
> * Utwórz parametr potoku.
> * Opublikuj potok szkoleniowy.
> * Ponownie nauczenie modelu.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, Utwórz [bezpłatne konto](https://aka.ms/AMLFree).

* Azure Machine Learning obszar roboczy z jednostką SKU przedsiębiorstwa.

Ta procedura polega na tym, że masz podstawową wiedzę na temat tworzenia potoków w projektancie. Aby zapoznać się z przewodnikiem po wprowadzeniu do projektanta, Ukończ [samouczek](tutorial-designer-automobile-price-train-score.md). 

### <a name="sample-pipeline"></a>Przykładowy potok

Potok użyty w tym artykule to zmieniona wersja tego artykułu, którą można znaleźć w [przykładowym Przeprognozowaniu 3-dochodowym](how-to-designer-sample-classification-predict-income.md). Używa modułu [Importuj dane](algorithm-module-reference/import-data.md) zamiast przykładowego zestawu danych, aby pokazać, jak szkolić model przy użyciu własnych danych.

![Zrzut ekranu przedstawiający zmodyfikowany potok przykładowy z polem wyróżniania modułu Importuj dane](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="train-a-machine-learning-model"></a>Uczenie modelu uczenia maszynowego

Aby ponownie przeprowadzić uczenie modelu, potrzebny jest model początkowy. W tej sekcji dowiesz się, jak szkolić model i uzyskać dostęp do zapisanego modelu przy użyciu narzędzia Projektant.

1. Wybierz moduł **Importuj dane** .
1. W okienku Właściwości Określ źródło danych.

    ![Zrzut ekranu przedstawiający przykładową konfigurację modułu importu danych](./media/how-to-retrain-designer/import-data-settings.png)

    W tym przykładzie dane są przechowywane w [magazynie danych platformy Azure](how-to-access-data.md). Jeśli nie masz jeszcze magazynu danych, możesz utworzyć go teraz, wybierając pozycję **nowy magazyn**danych.

1. Określ ścieżkę do danych. Możesz również wybrać pozycję **Przeglądaj ścieżkę** , aby wizualnie nawigować do magazynu danych. 

1. Wybierz pozycję **Uruchom** w górnej części kanwy.
    
    > [!NOTE]
    > Jeśli ustawiono już domyślne obliczenia dla tego elementu roboczego potoku, potok zostanie uruchomiony automatycznie. W przeciwnym razie można postępować zgodnie z instrukcjami wyświetlanymi w okienku Ustawienia, aby ustawić je teraz.

### <a name="locate-your-trained-model"></a>Lokalizowanie przeszkolonego modelu

Projektant zapisuje wszystkie dane wyjściowe potoku, w tym modele przeszkolone, do domyślnego konta magazynu. Dostęp do przeszkolonego modelu można także uzyskać bezpośrednio w projektancie:

1. Poczekaj na zakończenie działania potoku.

1. Wybierz moduł **uczenie modelu** .

1. W okienku Ustawienia wybierz pozycję dane **wyjściowe**.

1. Wybierz **Trained_model** , aby pobrać model.

![Zrzut ekranu przedstawiający sposób pobierania przeszkolonego modelu](./media/how-to-retrain-designer/download-model.png)

## <a name="create-a-pipeline-parameter"></a>Tworzenie parametru potoku

Dodaj parametry potoku, aby dynamicznie ustawiać zmienne w czasie wykonywania. Dla tego potoku Dodaj parametr dla ścieżki danych szkoleniowych, aby można było ponownie nauczyć model na nowym zestawie danych.

1. Wybierz moduł **Importuj dane** .
1. W okienku Ustawienia wybierz wielokropek powyżej pola **ścieżka** .
1. Wybierz pozycję **Dodaj do parametru potoku**.
1. Podaj nazwę parametru i wartość domyślną.

    > [!NOTE]
    > Parametry potoku można sprawdzić i edytować, wybierając **ikonę koła zębatego** obok tytułu wersji roboczej potoku. 

![Zrzut ekranu przedstawiający sposób tworzenia parametru potoku](media/how-to-retrain-designer/add-pipeline-parameter.png)

## <a name="publish-a-training-pipeline"></a>Publikowanie potoku szkoleniowego

Po opublikowaniu potoku tworzony jest punkt końcowy potoku. Punkty końcowe potoku umożliwiają ponowne użycie potoków i zarządzanie nimi na potrzeby powtarzalności i automatyzacji. W tym przykładzie skonfigurowano potok do ponownego szkolenia.

1. Wybierz pozycję **Publikuj** powyżej kanwy projektanta.
1. Wybierz lub Utwórz nowy punkt końcowy potoku.

    > [!NOTE]
    > Można opublikować wiele potoków w jednym punkcie końcowym. Każdy potok w punkcie końcowym ma numer wersji, który można określić podczas wywoływania punktu końcowego potoku.

1. Wybierz pozycję **Publikuj**.

## <a name="retrain-your-model"></a>Ponowne uczenie modelu

Teraz, gdy masz opublikowany potok szkoleniowy, możesz go użyć do ponownego nauczenia modelu przy użyciu nowych danych. Można przesyłać przebiegi z punktu końcowego potoku z poziomu portalu lub programowo.

### <a name="submit-runs-with-the-designer"></a>Przesyłanie przebiegów z projektantem

Wykonaj następujące kroki, aby przesłać punkt końcowy potoku z narzędzia Projektant:

1. Przejdź do strony **punkty końcowe** .

1. Wybierz kartę **punkty końcowe potoku** .

1. Wybierz punkt końcowy potoku.

1. Wybierz kartę **opublikowane potoki** .

1. Wybierz potok, który chcesz uruchomić.

1. Wybierz pozycję **Uruchom**.

1. W oknie dialogowym Konfiguracja można określić nową wartość ścieżki danych wejściowych, która wskazuje nowy zestaw danych.

![Zrzut ekranu przedstawiający sposób skonfigurowania sparametryzowanego uruchomienia potoku w projektancie](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-with-code"></a>Prześlij przebiegi z kodem

Istnieje wiele sposobów uzyskiwania dostępu do punktu końcowego REST programowo w zależności od środowiska deweloperskiego. Możesz znaleźć przykłady kodu, które pokazują, jak przesłać uruchomienia potoku z parametrami na karcie **Korzystanie** z potoku.

## <a name="next-steps"></a>Następne kroki

Postępuj zgodnie z [samouczkiem](tutorial-designer-automobile-price-train-score.md) projektanta, aby nauczyć i wdrożyć model regresji.
