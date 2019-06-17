---
title: Recenzje, przepływy pracy i zadania pojęcia — Content Moderator
titlesuffix: Azure Cognitive Services
description: Więcej informacji na temat zadań, przepływy pracy i przeglądy
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: sajagtap
ms.openlocfilehash: c1d4ef640e2ae072dacba7a665b6689e3224c55c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60607297"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>Przeglądy moderowanie zawartości, zadań i przepływów pracy

Pakiet Content Moderator łączy wspomaganego maszynowo moderowania możliwości człowieka w pętli, aby utworzyć proces moderowania optymalne dla scenariuszy w rzeczywistych warunkach. Jest to realizowane za pośrednictwem oparte na chmurze [narzędzie do przeglądu](https://contentmoderator.cognitive.microsoft.com). Ten przewodnik zawiera informacje o podstawowych pojęciach narzędzie do przeglądu: recenzje, zadań i przepływów pracy.

## <a name="reviews"></a>Przeglądy

W przeglądzie, zawartość zostanie przekazany do narzędzia do przeglądu i pojawia się w obszarze **Przejrzyj** kartę. W tym miejscu użytkownicy mogą zmieniać zastosować znaczniki i Zastosuj własne niestandardowe tagi, zgodnie z potrzebami. Gdy użytkownik przesyła przeglądu, wyniki są wysyłane do punktu końcowego określonego wywołania zwrotnego, a zawartość zostanie usunięta z lokacji.

![Narzędzie do przeglądu witryny sieci Web otwórz w przeglądarce, na karcie Przegląd](./Review-Tool-user-Guide/images/image-workflow-review.png)

Zobacz [przewodnik narzędzie do przeglądu](./review-tool-user-guide/review-moderated-images.md) wprowadzenie do tworzenia przeglądów lub zobacz [Podręcznik interfejsu API REST](./try-review-api-review.md) Aby dowiedzieć się, jak to zrobić programowo.

## <a name="workflows"></a>Przepływy pracy

Przepływ pracy jest oparta na chmurze dostosowane filtr dla zawartości. Przepływy pracy mogą łączyć się z różnych usług do filtrowania zawartości na różne sposoby, a następnie podjąć odpowiednie działania. Z łącznikiem usługi Content Moderator przepływu pracy można automatycznie zastosować znaczniki Moderowanie i Utwórz przeglądy przesłanych treści.

### <a name="view-workflows"></a>Wyświetlanie przepływów pracy

Aby wyświetlić z istniejącymi przepływami pracy, przejdź do [narzędzie do przeglądu](https://contentmoderator.cognitive.microsoft.com/) i wybierz **ustawienia** > **przepływy pracy**.

![Domyślny przepływ pracy](images/default-workflow-listed.PNG)

Przepływy pracy mogą całkowicie opisane jako ciągi formatu JSON, co sprawia, że ich dostępny programowo. Jeśli wybierzesz **Edytuj** opcji dla przepływu pracy, a następnie wybierz pozycję **JSON** kartę, zostanie wyświetlone wyrażenie JSON, jak pokazano poniżej:

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

Zobacz [przewodnik narzędzie do przeglądu](./review-tool-user-guide/workflows.md) Rozpocznij tworzenie i korzystanie z przepływów pracy, lub zobacz [Podręcznik interfejsu API REST](./try-review-api-workflow.md) Aby dowiedzieć się, jak to zrobić programowo.

## <a name="jobs"></a>Zadania

Zadania Moderowanie służy jako typ otoki dla funkcji moderowania zawartości, przepływy pracy i recenzje. Zadanie skanowania zawartości przy użyciu Moderowanie obrazów Content Moderator, interfejsu API lub interfejs API moderowania tekstu i następnie porównuje ją z wyznaczonym przepływu pracy. Na podstawie przepływu pracy wyników, może lub nie może utworzyć przeglądu zawartości w [narzędzie do przeglądu](./review-tool-user-guide/human-in-the-loop.md). Przeglądy i przepływów pracy można utworzone i skonfigurowane za pomocą ich odpowiednich interfejsów API, zadanie interfejsu API pozwala uzyskać szczegółowy raport dotyczący całego procesu, (które mogą być wysyłane do punktu końcowego określonego wywołania zwrotnego).

Zobacz [Podręcznik interfejsu API REST](./try-review-api-job.md) na rozpoczęcie pracy przy użyciu zadań.

## <a name="next-steps"></a>Kolejne kroki

* Wersja testowa [Konsola interfejsu API zadań](try-review-api-job.md)i przykłady kodu interfejsu API REST. Jeśli znasz program Visual Studio w języku C#, Sprawdź również [.NET zadania szybkiego startu](moderation-jobs-quickstart-dotnet.md). 
* Aby rozpocząć pracę z [Konsola interfejsu API przeglądu](try-review-api-review.md)i przykłady kodu interfejsu API REST. Następnie zobacz [Szybki Start .NET przeglądy](moderation-reviews-quickstart-dotnet.md).
* Przeglądy wideo, można użyć [wideo Przejrzyj Przewodnik Szybki Start](video-reviews-quickstart-dotnet.md)i Dowiedz się, jak [Dodaj transkrypcji do wideo przeglądu](video-transcript-reviews-quickstart-dotnet.md).
