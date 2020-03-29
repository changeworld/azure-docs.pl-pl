---
title: Jak zaktualizować projekt do interfejsu API 3.0
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak zaktualizować projekty usługi Custom Vision z poprzedniej wersji interfejsu API do interfejsu API 3.0.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: areddish
ms.openlocfilehash: c134f30b124113a23df0e73cd1bbc8209e335183
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647501"
---
# <a name="update-to-the-30-api"></a>Aktualizacja interfejsu API 3.0

Usługa Custom Vision osiągnęła ogólną dostępność i została poddana aktualizacji interfejsu API.
Ta aktualizacja zawiera kilka nowych funkcji i, co ważne, kilka przełomowych zmian:

* Interfejs API przewidywania jest teraz podzielony na dwie części na podstawie typu projektu.
* Opcja eksportu zestawu Vision AI Developer Kit (VAIDK) wymaga utworzenia projektu w określony sposób.
* Domyślne iteracje zostały usunięte na rzecz publikowania / unpublish nazwie iteracji.

W tym przewodniku pokazano, jak zaktualizować projekty do pracy z nową wersją interfejsu API. Pełna lista zmian znajduje się w [informacji o wersji.](release-notes.md)

## <a name="use-the-updated-prediction-api"></a>Korzystanie ze zaktualizowanego interfejsu API przewidywania

Interfejsy API 2.x używane tego samego wywołania przewidywania zarówno klasyfikatorów obrazu i projektów detektora obiektów. Oba typy projektów były dopuszczalne **dla wywołań PredictImage** i **PredictImageUrl.** Począwszy od 3.0, mamy podzielić ten interfejs API, dzięki czemu trzeba dopasować typ projektu do wywołania:

* Użyj **[ClassifyImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)** i **[ClassifyImageUrl,](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c14)** aby uzyskać prognoz dla projektów klasyfikacji obrazu.
* Użyj **[DetectImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c19)** i **[DetectImageUrl,](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c18)** aby uzyskać prognoz dla projektów wykrywania obiektów.

## <a name="use-the-new-iteration-publishing-workflow"></a>Korzystanie z nowego przepływu pracy publikowania iteracji

Interfejsy API 2.x używane domyślnej iteracji lub określony identyfikator iteracji, aby wybrać iterację do użycia do prognozowania. Począwszy od 3.0, przyjęliśmy przepływ publikowania, w którym najpierw opublikować iteracji pod określoną nazwą z interfejsu API szkolenia. Następnie przekazać nazwę do metod przewidywania, aby określić, które iteracji do użycia.

> [!IMPORTANT]
> Interfejsy API 3.0 nie używają domyślnej funkcji iteracji. Dopóki nie przestarzaniemy starszych interfejsów API, można nadal używać interfejsów API 2.x, aby przełączyć iterację jako domyślną. Te interfejsy API będą obsługiwane przez pewien czas i można **[wywołać UpdateIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b818)** metody, aby oznaczyć iteracji jako domyślne.

### <a name="publish-an-iteration"></a>Publikowanie iteracji

Po przeszkoleniu iteracji, można udostępnić go do przewidywania przy użyciu **[PublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c82db28bf6a2b11a8247bbc)** metody. Aby opublikować iterację, potrzebny jest identyfikator zasobu przewidywania, który jest dostępny na stronie ustawień witryny CustomVision.

![Strona Ustawienia witryny sieci Web usługi Owe wizjuj z nakreślonym identyfikatorem zasobu przewidywania.](./media/update-application-to-3.0-sdk/prediction-id.png)

> [!TIP]
> Te informacje można również uzyskać z [witryny Azure Portal,](https://portal.azure.com) przechodząc do zasobu Przewidywanie wizji niestandardowej i wybierając **pozycję Właściwości**.

Po opublikowaniu iteracji aplikacje mogą używać go do przewidywania, określając nazwę w wywołaniu interfejsu API przewidywania. Aby iteracja była niedostępna dla wywołań przewidywania, użyj interfejsu API **[UnpublishIteration.](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b81a)**

## <a name="next-steps"></a>Następne kroki

* [Dokumentacja referencyjna interfejsu API szkolenia (REST)](https://go.microsoft.com/fwlink/?linkid=865446)
* [Dokumentacja referencyjna interfejsu API przewidywania (REST)](https://go.microsoft.com/fwlink/?linkid=865445)