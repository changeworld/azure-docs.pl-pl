---
title: Jak przeprowadzić migrację projektu do wersji 3.0 interfejsu API
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak przeprowadzić migrację projektów Custom Vision z poprzedniej wersji interfejsu API 3.0 interfejsu API.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: areddish
ms.openlocfilehash: 9dd473aadd7123cafc27209f5c34322fdbcffb71
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60816459"
---
# <a name="migrate-to-the-30-api"></a>Migracja do wersji 3.0 interfejsu API

Custom Vision osiągnęła teraz ogólnie dostępne i zostały poddane aktualizacji interfejsu API.
Ta aktualizacja obejmuje kilka nowych funkcji i, co ważniejsze, kilka zmian niepowodujących:

* Prediction API teraz zostanie podzielona na dwie zależności od typu projektu.
* Opcja eksportowania wizji sztucznej Inteligencji Developer Kit (VAIDK) wymaga utworzenia projektu w określony sposób.
* Domyślna liczba iteracji zostały usunięte na rzecz publikowania / Cofnij publikowanie nazwane iteracji.

Ten przewodnik będzie pokazują, jak zaktualizować swoje projekty do pracy z nową wersją interfejsu API. Zobacz [informacje o wersji](release-notes.md) pełną listę zmian.

## <a name="use-the-updated-prediction-api"></a>Za pomocą zaktualizowanego interfejsu API prognozowania

2\.x interfejsy API używane to samo wywołanie prognozowania zarówno klasyfikatorów obrazów, jak i obiekt wykrywanie projektów. Oba typy projektu były do przyjęcia **PredictImage** i **PredictImageUrl** wywołania. Począwszy od 3.0, firma Microsoft ma podzielić ten interfejs API, aby konieczne jest zgodny z typem projektu do wywołania:

* Użyj **[ClassifyImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)** i **[ClassifyImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c14)** można pobrać prognoz dotyczących projektów klasyfikacji obrazów.
* Użyj **[DetectImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c19)** i **[DetectImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c18)** można uzyskiwać prognozy dla obiektu wykrywanie projektów.

## <a name="use-the-new-iteration-publishing-workflow"></a>Użyj nowy przepływ publikowania iteracji

Interfejsy API 2.x umożliwia domyślnej iteracji lub identyfikator iteracji wybierz iterację do użycia na potrzeby prognozowania. Począwszy od 3.0 wdrożyliśmy przepływ publikowania, według której najpierw opublikować iteracji w określonej nazwie z interfejsu API szkolenia. Nazwa jest następnie przekazać do metody prognozowania, aby określić, które iteracji do użycia.

> [!IMPORTANT]
> 3\.0 interfejsów API należy używać domyślnej funkcji iteracji. Do czasu firma Microsoft wycofana starszych interfejsów API, można nadal używać 2.x interfejsy API umożliwiające przełączanie iteracji jako domyślny. Te interfejsy API będzie przechowywany w okresie czasu, a może wywołać **[UpdateIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b818)** metodę, aby oznaczyć iteracji jako domyślny.

### <a name="publish-an-iteration"></a>Publikowanie iteracji

Po przygotowaniu iteracji można udostępnić go do prognozowania przy użyciu **[PublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c82db28bf6a2b11a8247bbc)** metody. Aby opublikować iteracji, należy identyfikator zasobu prognozowania, który jest dostępny na stronie Ustawienia CustomVision witryny internetowej.

![Custom Vision strona Ustawienia witryny sieci Web o identyfikatorze zasobu prognoz, które są opisane.](./media/update-application-to-3.0-sdk/prediction-id.png)

> [!TIP]
> Można także uzyskać tę informację z [witryny Azure Portal](https://portal.azure.com) , przechodząc do zasobu Custom Vision prognoz i wybierając **właściwości**.

Po opublikowaniu swojej iteracji aplikacje mogą używać jej do prognozowania, określając nazwę w ich wywołania interfejsu API prognoz. Aby iteracji był niedostępny dla wywołań prognoz, użyj **[UnpublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b81a)** interfejsu API.

## <a name="additional-export-options"></a>Opcje dodatkowe eksportu

Za pomocą 3.0 interfejsy API są firma Microsoft udostępnia dwa dodatkowe eksportowanie elementów docelowych: Architektura ARM i wizji sztucznej Inteligencji Developer Kit.

* Aby użyć ARM, wystarczy wybrać Compact domeny i następnie wybierz plik DockerFile i następnie ARM jako opcje eksportu.
* Dla przetwarzania sztucznej Inteligencji zestaw deweloperski, projekt musi zostać utworzona z __ogólne (CD)__ domeny, a także określić VAIDK w celu eksportowania argument platform.

## <a name="next-steps"></a>Kolejne kroki

* [Szkolenia, dokumentacji interfejsu API (REST)](https://go.microsoft.com/fwlink/?linkid=865446)
* [Dokumentacja referencyjna interfejsu API produkcji (REST)](https://go.microsoft.com/fwlink/?linkid=865445)