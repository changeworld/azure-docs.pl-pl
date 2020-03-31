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
ms.date: 02/24/2020
ms.openlocfilehash: c8791e933882832dc7b0037c860a4c4e1e9a54c7
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389039"
---
# <a name="retrain-models-with-azure-machine-learning-designer-preview"></a>Ponowne trenowanie modeli za pomocą projektanta usługi Azure Machine Learning (wersja zapoznawcza)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

W tym artykule opisano, jak używać projektanta usługi Azure Machine Learning do ponownego przeszkolenia modelu uczenia maszynowego. Dowiedz się, jak używać opublikowanych potoków do automatyzacji przepływów pracy uczenia maszynowego w celu przekwalifikowania.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Trenuj model uczenia maszynowego.
> * Utwórz parametr potoku.
> * Opublikuj swój potok szkolenia.
> * Przekwalifikowanie modelu.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://aka.ms/AMLFree).
* Obszar roboczy usługi Azure Machine Learning z jednostką SKU przedsiębiorstwa.

W tym artykule założono, że masz podstawową wiedzę na temat tworzenia potoków w projektancie. Aby zapoznać się z wprowadzeniem z przewodnikiem do projektanta, wykonaj [samouczek](tutorial-designer-automobile-price-train-score.md). 

### <a name="sample-pipeline"></a>Przykładowy potok

Potok używany w tym artykule jest zmienioną wersją tej znalezionej w [próbce 3: Przewidywanie dochodów](how-to-designer-sample-classification-predict-income.md). Używa [importu danych](algorithm-module-reference/import-data.md) modułu zamiast przykładowego zestawu danych, aby pokazać, jak trenować model przy użyciu własnych danych.

![Zrzut ekranu przedstawiający zmodyfikowany potok próbek z polem wyróżniającym moduł Importuj dane](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="train-a-machine-learning-model"></a>Szkolenie modelu uczenia maszynowego

Aby przeszkolić model, potrzebny jest model początkowy. W tej sekcji dowiesz się, jak trenować model i uzyskać dostęp do zapisanego modelu przy użyciu projektanta.

1. Wybierz moduł **Importuj dane.**
1. W okienku właściwości określ źródło danych.

   ![Zrzut ekranu przedstawiający przykładową konfigurację modułu Importuj dane](./media/how-to-retrain-designer/import-data-settings.png)

   W tym przykładzie dane są przechowywane w [magazynie danych platformy Azure](how-to-access-data.md). Jeśli nie masz jeszcze magazynu danych, możesz go utworzyć teraz, wybierając **pozycję Nowy magazyn danych**.

1. Określ ścieżkę do danych. Można również wybrać **opcję Przeglądaj ścieżkę,** aby przejść do magazynu danych. 
1. Wybierz **pozycję Prześlij** u góry obszaru roboczego.
    
   > [!NOTE]
   > Jeśli ustawiono już domyślne obliczenia dla tego projektu potoku, potok zostanie uruchomiony automatycznie. W przeciwnym razie możesz wykonać monity w okienku ustawień, aby ustawić je teraz.

### <a name="find-your-trained-model"></a>Znajdź swój wyszkolony model

Projektant zapisuje wszystkie dane wyjściowe potoku, w tym modele uczony, na domyślnym koncie magazynu. Można jednak również uzyskać dostęp do wyszkolonych modeli bezpośrednio w projektancie:

1. Poczekaj na zakończenie działania potoku.
1. Wybierz moduł **Train Model** (Trenowanie modelu).
1. W okienku ustawień wybierz pozycję **Wyjścia+dzienniki**.
1. Wybierz ikonę **Wyświetl dane wyjściowe** i postępuj zgodnie z instrukcją w wyskakującym oknie, aby znaleźć przeszkolony model.

![Zrzut ekranu przedstawiający sposób pobierania przeszkolonego modelu](./media/how-to-retrain-designer/trained-model-view-output.png)

## <a name="create-a-pipeline-parameter"></a>Tworzenie parametru potoku

Dodaj parametry potoku, aby dynamicznie ustawiać zmienne w czasie wykonywania. W przypadku tego potoku dodaj parametr dla ścieżki danych szkoleniowych, aby można było ponownie trenować model w nowym zestawie danych.

1. Wybierz moduł **Importuj dane.**
1. W okienku ustawień wybierz elipsy nad polem **Ścieżka.**
1. Wybierz **polecenie Dodaj do parametru potoku**.
1. Podaj nazwę parametru i wartość domyślną.

   > [!NOTE]
   > Parametry potoku można sprawdzić i edytować, wybierając ikonę Przekładni **Ustawienia** obok tytułu wersji roboczej potoku. 

![Zrzut ekranu przedstawiający sposób tworzenia parametru potoku](media/how-to-retrain-designer/add-pipeline-parameter.png)

## <a name="publish-a-training-pipeline"></a>Publikowanie potoku szkoleniowego

Podczas publikowania potoku tworzy punkt końcowy potoku. Punkty końcowe potoku umożliwiają ponowne użycie i zarządzanie potokami w celu powtarzalności i automatyzacji. W tym przykładzie skonfigurować potok do ponownego szkolenia.

1. Wybierz **pozycję Publikuj** nad kanwą projektanta.
1. Wybierz lub utwórz punkt końcowy potoku.

   > [!NOTE]
   > Można opublikować wiele potoków do jednego punktu końcowego. Każdy potok w punkcie końcowym otrzymuje numer wersji, który można określić podczas wywoływania punktu końcowego potoku.

1. Wybierz pozycję **Publikuj**.

## <a name="retrain-your-model"></a>Przekwalifikowanie modelu

Teraz, gdy masz opublikowany potok szkolenia, można go użyć do ponownego przeszkolenia modelu przy użyciu nowych danych. Można przesłać przebiegi z punktu końcowego potoku z witryny Azure portal lub przesłać je programowo.

### <a name="submit-runs-by-using-the-designer"></a>Przesyłanie przebiegów przy użyciu projektanta

Aby przesłać uruchomienie punktu końcowego potoku od projektanta, należy wykonać następujące kroki:

1. Przejdź do strony **Punkty końcowe.**
1. Wybierz kartę **Punkty końcowe potoku.**
1. Wybierz punkt końcowy potoku.
1. Wybierz kartę **Opublikowane potoki.**
1. Wybierz potok, który chcesz uruchomić.
1. Wybierz pozycję **Prześlij**.
1. W oknie dialogowym instalacji można określić nową wartość wartości ścieżki danych wejściowych. Ta wartość wskazuje nowy zestaw danych.

![Zrzut ekranu przedstawiający sposób konfigurowania sparametryzowanego przebiegu potoku w projektancie](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-by-using-code"></a>Przesyłanie przebiegów przy użyciu kodu

Punkt końcowy REST opublikowanego potoku można znaleźć w panelu przeglądu. Wywołując punkt końcowy, można ponownie trenować opublikowany potok.

Aby nawiązać połączenie REST, potrzebujesz nagłówka uwierzytelniania typu okaziciela OAuth 2.0. Aby uzyskać informacje dotyczące konfigurowania uwierzytelniania w obszarze roboczym i wykonywania sparametryzowanego wywołania REST, zobacz [Tworzenie potoku usługi Azure Machine Learning do oceniania wsadowego.](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint)

## <a name="next-steps"></a>Następne kroki

Postępuj zgodnie z [samouczka projektanta](tutorial-designer-automobile-price-train-score.md) do szkolenia i wdrożyć model regresji.
