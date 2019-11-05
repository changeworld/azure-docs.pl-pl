---
title: Uczenie aplikacji — LUIS
titleSuffix: Azure Cognitive Services
description: Szkolenie to proces uczenia wersji aplikacji Language Understanding (LUIS) w celu usprawnienia jej interpretacji języka naturalnego. Uczenie aplikacji LUIS po aktualizacji modelu, takich jak dodawanie, edytowanie, etykietowanie lub usuwanie jednostek, intencje lub wyrażenia długości.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 259ea23c05f0c0a138ad54b6efd11aad2061cf7a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500236"
---
# <a name="train-your-active-version-of-the-luis-app"></a>Uczenie aktywnej wersji aplikacji LUIS 

Szkolenie to proces uczenia aplikacji Language Understanding (LUIS) w celu usprawnienia jej interpretacji języka naturalnego. Uczenie aplikacji LUIS po aktualizacji modelu, takich jak dodawanie, edytowanie, etykietowanie lub usuwanie jednostek, intencje lub wyrażenia długości. 

Szkolenie i [testowanie](luis-concept-test.md) aplikacji jest procesem iteracyjnym. Po przejściu do aplikacji LUIS przetestuj ją za pomocą przykładowej wyrażenia długości, aby sprawdzić, czy intencje i jednostki są rozpoznawane poprawnie. Jeśli tak nie jest, wprowadź ponownie aktualizacje aplikacji LUIS, uczenia i testowania. 

Szkolenia są stosowane do aktywnej wersji portalu LUIS. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="how-to-train-interactively"></a>Jak szkolić interaktywnie

Aby rozpocząć proces iteracyjny w [portalu Luis](https://www.luis.ai), najpierw musisz przeprowadzić uczenie aplikacji Luis co najmniej raz. Przed szkoleniem upewnij się, że każdy z zamiarów ma co najmniej jeden wypowiedź.

1. Uzyskaj dostęp do aplikacji, wybierając jej nazwę na stronie **Moje aplikacje** . 

2. W aplikacji wybierz pozycję **szkolenie** w górnym panelu. 

3. Po zakończeniu szkolenia w górnej części przeglądarki pojawia się zielony pasek powiadomień.

<!-- The following note refers to what might cause the error message "Training failed: FewLabels for model: <ModelName>" -->

>[!NOTE]
>Jeśli masz co najmniej jeden zamiar w aplikacji, który nie zawiera przykładu wyrażenia długości, nie możesz nauczyć się swojej aplikacji. Dodaj wyrażenia długości dla wszystkich Twoich intencji. Aby uzyskać więcej informacji, zobacz [Dodawanie przykładu wyrażenia długości](luis-how-to-add-example-utterances.md).

## <a name="training-date-and-time"></a>Data i godzina szkolenia

Data i godzina szkolenia to GMT + 2. 

## <a name="train-with-all-data"></a>Uczenie ze wszystkimi danymi

Szkolenie zużywa niewielką część próbkowania negatywnego. 

Jeśli chcesz używać wszystkich danych zamiast małego próbkowania negatywnego, użyj [interfejsu API](#version-settings-api-use-of-usealltrainingdata).

<!--

 or the [LUIS portal setting](#luis-portal-setting-to-use-all-training-data)

### LUIS portal setting to use all training data

!!!IGNITE


-->

### <a name="version-settings-api-use-of-usealltrainingdata"></a>Ustawienia wersji użycie interfejsu API UseAllTrainingData

Aby wyłączyć tę funkcję, użyj [interfejsu API ustawień wersji](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) z ustawieniem `UseAllTrainingData` na wartość true. 

## <a name="unnecessary-training"></a>Niepotrzebne szkolenie

Nie ma potrzeby uczenia się po każdej pojedynczej zmianie. Szkolenie należy wykonać po zastosowaniu grupy zmian do modelu i następnym kroku, który ma zostać przetestowany lub opublikowany. Jeśli nie ma potrzeby testowania ani publikowania, szkolenie nie jest konieczne. 

## <a name="training-with-the-rest-apis"></a>Szkolenie przy użyciu interfejsów API REST

Szkolenie w portalu LUIS to pojedynczy krok po naciśnięciu przycisku **uczenie** . Szkolenie z użyciem interfejsów API REST jest procesem dwuetapowym. Pierwszy polega na [zażądaniu szkolenia](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) przy użyciu protokołu HTTP POST. Następnie Zażądaj [stanu szkolenia](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) przy użyciu protokołu HTTP GET. 

Aby dowiedzieć się, kiedy szkolenie zostało zakończone, musisz sondować stan do momentu, w którym wszystkie modele zostały pomyślnie przeszkolone. 

## <a name="next-steps"></a>Następne kroki

* [Etykieta sugerowanych wyrażenia długości z LUIS](luis-how-to-review-endpoint-utterances.md) 
* [Korzystaj z funkcji, aby zwiększyć wydajność aplikacji LUIS](luis-how-to-add-features.md) 
