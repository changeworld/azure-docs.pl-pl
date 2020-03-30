---
title: Prognozowanie partii za pomocą projektanta usługi Azure Machine Learning (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak trenować model i skonfigurować potok przewidywania partii przy użyciu projektanta. Wdrażanie potoku jako sparametryzowanej usługi sieci web, która może być wyzwalana z dowolnej biblioteki HTTP.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: peterlu
author: peterclu
ms.date: 02/24/2020
ms.custom: Ignite2019
ms.openlocfilehash: 01d69bffcf2c17abceba8ba2e0893360bead8b12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477225"
---
# <a name="run-batch-predictions-using-azure-machine-learning-designer-preview"></a>Prognozowanie partii za pomocą projektanta usługi Azure Machine Learning (wersja zapoznawcza)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

W tym artykule dowiesz się, jak użyć projektanta do utworzenia potoku przewidywania partii. Przewidywanie partii umożliwia ciągłe ocenianie dużych zestawów danych na żądanie przy użyciu usługi sieci web, która może być wyzwalana z dowolnej biblioteki HTTP.

W tym instrukcje, można nauczyć się wykonywać następujące zadania:

> [!div class="checklist"]
> * Tworzenie i publikowanie potoku wnioskowania partii
> * Korzystanie z punktu końcowego potoku
> * Zarządzanie wersjami punktów końcowych

Aby dowiedzieć się, jak skonfigurować usługi oceniania partii przy użyciu zestawu SDK, zobacz [towarzyszące instrukcje](how-to-run-batch-predictions.md).

## <a name="prerequisites"></a>Wymagania wstępne

Ten sposób zakłada, że masz już potoku szkolenia. Aby zapoznać się z wprowadzeniem z przewodnikiem do projektanta, wypełnij [część 1 samouczka projektanta](tutorial-designer-automobile-price-train-score.md). 

## <a name="create-a-batch-inference-pipeline"></a>Tworzenie potoku wnioskowania partii

Potoku szkolenia musi być uruchomiony co najmniej raz, aby można było utworzyć potok wnioskowania.

1. Przejdź do karty **Projektant** w obszarze roboczym.

1. Wybierz potok szkolenia, który trenuje model, którego chcesz użyć do przewidywania.

1. **Prześlij** potok.

    ![Prześlij potok](./media/how-to-run-batch-predictions-designer/run-training-pipeline.png)

Teraz, gdy potok szkolenia został uruchomiony, można utworzyć potok wnioskowania partii.

1. Obok **pozycji Prześlij**wybierz nową pozycję **Rozwijanie Utwórz potok wnioskowania**.

1. Wybierz **potok wnioskowania partii**.

    ![Tworzenie potoku wnioskowania partii](./media/how-to-run-batch-predictions-designer/create-batch-inference.png)
    
Wynikiem jest domyślny potok wnioskowania partii. 

### <a name="add-a-pipeline-parameter"></a>Dodawanie parametru potoku

Aby utworzyć prognoz na nowych danych, można ręcznie połączyć inny zestaw danych w tym widoku roboczym potoku lub utworzyć parametr dla zestawu danych. Parametry umożliwiają zmianę zachowania procesu wnioskowania partii w czasie wykonywania.

W tej sekcji utworzysz parametr zestawu danych, aby określić inny zestaw danych do przewidywania.

1. Wybierz moduł zestawu danych.

1. Po prawej stronie kanwy pojawi się okienko. U dołu okienka wybierz polecenie **Ustaw jako parametr potoku**.
   
    Wprowadź nazwę parametru lub zaakceptuj wartość domyślną.

## <a name="publish-your-batch-inferencing-pipeline"></a>Publikowanie potoku wnioskowania wsadowego

Teraz możesz przystąpić do wdrażania potoku wnioskowania. Spowoduje to wdrożenie potoku i udostępni go innym osobom do użycia.

1. Wybierz przycisk **Publikuj**.

1. W wyświetlonym oknie dialogowym rozwiń pozycję rozwijanej **dla pipelineendpoint**i wybierz pozycję **Nowy punkt zakończenia potoku**.

1. Podaj nazwę punktu końcowego i opis opcjonalny.

    W dolnej części okna dialogowego można zobaczyć parametr skonfigurowany z domyślną wartością identyfikatora zestawu danych używanego podczas szkolenia.

1. Wybierz pozycję **Publikuj**.

![Publikowanie potoku](./media/how-to-run-batch-predictions-designer/publish-inference-pipeline.png)


## <a name="consume-an-endpoint"></a>Korzystanie z punktu końcowego

Teraz masz opublikowany potok z parametrem zestawu danych. Potok użyje uczonego modelu utworzonego w potoku szkolenia, aby uzyskać ocenę zestawu danych, który podasz jako parametr.

### <a name="submit-a-pipeline-run"></a>Przesyłanie przebiegu potoku 

W tej sekcji skonfigurujesz ręczne uruchomienie potoku i zmienisz parametr potoku, aby uzyskać nowe dane. 

1. Po zakończeniu wdrażania przejdź do sekcji **Punkty końcowe.**

1. Wybierz **pozycję Punkty końcowe potoku**.

1. Wybierz nazwę utworzonego punktu końcowego.

![Łącze punktu końcowego](./media/how-to-run-batch-predictions-designer/manage-endpoints.png)

1. Wybierz **pozycję Opublikowane potoki**.

    Na tym ekranie przedstawiono wszystkie opublikowane potoki opublikowane w ramach tego punktu końcowego.

1. Wybierz opublikowany potok.

    Strona szczegółów potoku zawiera szczegółowe informacje o historii uruchamiania i ciągu połączenia dla potoku. 
    
1. Wybierz **przycisk Prześlij,** aby utworzyć ręczne uruchomienie potoku.

    ![Szczegóły rurociągu](./media/how-to-run-batch-predictions-designer/submit-manual-run.png)
    
1. Zmień parametr, aby użyć innego zestawu danych.
    
1. Wybierz **przycisk Prześlij,** aby uruchomić potok.

### <a name="use-the-rest-endpoint"></a>Korzystanie z punktu końcowego REST

Informacje na temat sposobu korzystania z punktów końcowych potoku i opublikowanego potoku można znaleźć w sekcji **Punkty końcowe.**

Punkt końcowy REST punktu końcowego potoku można znaleźć w panelu przeglądu przebiegu. Wywołując punkt końcowy, zużywają jego domyślny opublikowany potok.

Można również korzystać z opublikowanego potoku na stronie **Opublikowane potoki.** Wybierz opublikowany potok i znajdź jego punkt końcowy REST. 

![Szczegóły punktu końcowego spoczynku](./media/how-to-run-batch-predictions-designer/rest-endpoint-details.png)

Aby nawiązać połączenie REST, potrzebny jest nagłówek uwierzytelniania typu okaziciela OAuth 2.0. Zobacz poniższą [sekcję samouczka,](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint) aby uzyskać więcej informacji na temat konfigurowania uwierzytelniania w obszarze roboczym i wykonywania sparametryzowanego wywołania REST.

## <a name="versioning-endpoints"></a>Punkty końcowe przechowywania wersji

Projektant przypisuje wersję do każdego kolejnego potoku, który można opublikować w punkcie końcowym. Można określić wersję potoku, który ma zostać wykonany jako parametr w wywołaniu REST. Jeśli nie określisz numeru wersji, projektant użyje domyślnego potoku.

Podczas publikowania potoku, można wybrać, aby uczynić go nowym domyślnym potoku dla tego punktu końcowego.

![Ustawianie domyślnego potoku](./media/how-to-run-batch-predictions-designer/set-default-pipeline.png)

Można również ustawić nowy domyślny potok na karcie **Opublikowane potoki** punktu końcowego.

![Ustawianie domyślnego potoku](./media/how-to-run-batch-predictions-designer/set-new-default-pipeline.png)

## <a name="next-steps"></a>Następne kroki

Postępuj zgodnie z [samouczka](tutorial-designer-automobile-price-train-score.md) projektanta do szkolenia i wdrożyć model regresji.
''