---
title: Koncepcje przeglądów, przepływów pracy i zadań — Content Moderator
titleSuffix: Azure Cognitive Services
description: Poznaj przeglądy, przepływy pracy i zadania
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: sajagtap
ms.openlocfilehash: a77b93c46c9989181cf4473e8b908571a3df2f20
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565553"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>Przeglądy, przepływy pracy i zadania moderowania zawartości

Content Moderator łączy moderowane maszynowo z funkcjami w pętli, aby utworzyć optymalny proces moderowania dla rzeczywistych scenariuszy. Odbywa się to za pośrednictwem narzędzia do [przeglądu](https://contentmoderator.cognitive.microsoft.com)opartego na chmurze. W tym przewodniku omówiono podstawowe pojęcia narzędzia do przeglądu: przeglądy, przepływy pracy i zadania.

## <a name="reviews"></a>Recenzje

W przeglądzie zawartość jest przekazywana do narzędzia przeglądu i pojawia się na karcie **Przegląd** . W tym miejscu użytkownicy mogą zmienić zastosowane znaczniki i zastosować własne niestandardowe znaczniki odpowiednio do potrzeb. Gdy użytkownik przesyła przegląd, wyniki są wysyłane do określonego punktu końcowego wywołania zwrotnego, a zawartość jest usuwana z lokacji.

![Przejrzyj witrynę internetową narzędzia Otwórz w przeglądarce, na karcie Przegląd](./Review-Tool-user-Guide/images/image-workflow-review.png)

Zapoznaj [](./review-tool-user-guide/review-moderated-images.md) się z przewodnikiem po narzędziu przeglądowym, aby rozpocząć tworzenie przeglądów, lub zobacz [Przewodnik po interfejsie API REST](./try-review-api-review.md) , aby dowiedzieć się, jak to zrobić programowo

## <a name="workflows"></a>Workflows

Przepływ pracy to dostosowany do chmury filtr dla zawartości. Przepływy pracy mogą łączyć się z różnymi usługami, aby filtrować zawartość na różne sposoby, a następnie podejmować odpowiednie działania. Za pomocą łącznika Content Moderator przepływ pracy może automatycznie stosować Tagi moderowania i tworzyć przeglądy z przesłaną zawartością.

### <a name="view-workflows"></a>Wyświetl przepływy pracy

Aby wyświetlić istniejące przepływy pracy, przejdź do [narzędzia przeglądu](https://contentmoderator.cognitive.microsoft.com/) i wybierz pozycję **Ustawienia** > **przepływy pracy**.

![Domyślny przepływ pracy](images/default-workflow-listed.PNG)

Przepływy pracy można całkowicie opisać jako ciągi JSON, co sprawia, że są one dostępne programowo. Jeśli wybierzesz opcję **Edytuj** dla przepływu pracy, a następnie wybierzesz kartę **JSON** , zobaczysz wyrażenie JSON podobne do następujących:

```json
{
    "Type": "Logic",
    "If": {
        "ConnectorName": "moderator",
        "OutputName": "isAdult",
        "Operator": "eq",
        "Value": "true",
        "Type": "Condition"
        },
    "Then": {
    "Perform": [
    {
        "Name": "createreview",
        "CallbackEndpoint": null,
        "Tags": []
    }
    ],
    "Type": "Actions"
    }
}
```

Aby rozpocząć [](./review-tool-user-guide/workflows.md) tworzenie i używanie przepływów pracy, zobacz Przewodnik po [interfejsie API REST](./try-review-api-workflow.md) , aby dowiedzieć się, jak to zrobić programowo.

## <a name="jobs"></a>Zadania

Zadanie moderowania służy jako rodzaj otoki dla funkcji moderowania zawartości, przepływów pracy i przeglądów. Zadanie skanuje zawartość przy użyciu interfejsu API moderowania obrazu Content Moderator lub interfejsu API moderowania tekstu, a następnie sprawdza go pod kątem określonego przepływu pracy. W oparciu o wyniki przepływu pracy może to spowodować, że nie można utworzyć przeglądu zawartości w narzędziu do [przeglądu](./review-tool-user-guide/human-in-the-loop.md). Podczas gdy zarówno przeglądy, jak i przepływy pracy można tworzyć i konfigurować przy użyciu odpowiednich interfejsów API, interfejs API zadań pozwala uzyskać szczegółowy raport dotyczący całego procesu (który można wysłać do określonego punktu końcowego wywołania zwrotnego).

Zobacz [Przewodnik po interfejsie API REST](./try-review-api-job.md) , aby rozpocząć pracę z zadaniami.

## <a name="next-steps"></a>Kolejne kroki

* Przetestuj [konsolę interfejsu API zadania](try-review-api-job.md)i użyj przykładów kodu interfejsu API REST. Jeśli znasz program Visual Studio i C#zapoznaj się z zadaniami [Szybki Start dla programu .NET](moderation-jobs-quickstart-dotnet.md). 
* Aby zapoznać się z przeglądami, Rozpocznij pracę z [konsolą interfejsu API przeglądu](try-review-api-review.md)i Skorzystaj z przykładów kodu interfejsu API REST. Zobacz [Przegląd przewodnika Szybki Start dla platformy .NET](moderation-reviews-quickstart-dotnet.md).
* Aby poznać Recenzje wideo, Skorzystaj z [przewodnika Szybki Start](video-reviews-quickstart-dotnet.md)dotyczącego przeglądu filmów wideo i Dowiedz się, jak [dodać transkrypcje do przeglądu wideo](video-transcript-reviews-quickstart-dotnet.md).
