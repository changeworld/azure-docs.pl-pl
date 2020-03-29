---
title: Używanie recenzji zawartości za pomocą narzędzia Recenzja — Moderator zawartości
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak narzędzie Recenzja umożliwia moderatorom przeglądanie obrazów w portalu internetowym.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pafarley
ms.openlocfilehash: b02324923e3f004395105b8e04165390cb950fe7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73044126"
---
# <a name="create-human-reviews"></a>Tworzenie ludzkich recenzji

W tym przewodniku dowiesz się, jak skonfigurować [opinie](../review-api.md#reviews) w witrynie narzędzia do przeglądania. Recenzje przechowują i wyświetlają treści dla moderatorów ludzkich do oceny. Moderatorzy mogą zmieniać zastosowane tagi i odpowiednio stosować własne tagi niestandardowe. Gdy użytkownik zakończy przegląd, wyniki są wysyłane do określonego punktu końcowego wywołania zwrotnego, a zawartość jest usuwana z witryny.

## <a name="prerequisites"></a>Wymagania wstępne

- Zaloguj się lub utwórz konto w witrynie narzędzia Content Moderator [Review.](https://contentmoderator.cognitive.microsoft.com/)

## <a name="image-reviews"></a>Przeglądy obrazów

1. Przejdź do [narzędzia Recenzja](https://contentmoderator.cognitive.microsoft.com/), wybierz kartę **Try** i prześlij kilka obrazów do sprawdzenia.
1. Po zakończeniu przetwarzania przesłanych obrazów przejdź do karty **Recenzja** i wybierz pozycję **Obraz**.

    ![Przeglądarka Chrome z wyróżnioną opcją Recenzja obrazu](images/review-images-1.png)

    Obrazy są wyświetlane z dowolnymi etykietami, które zostały przypisane przez proces automatycznego moderowania. Obrazy przesłane za pośrednictwem narzędzia Recenzja nie są widoczne dla innych recenzentów.

1. Opcjonalnie **przesuń suwak Recenzje do wyświetlenia** (1), aby dostosować liczbę obrazów wyświetlanych na ekranie. Kliknij **oznaczone** lub **nieoznakowane** przyciski (2), aby odpowiednio posortować obrazy. Kliknij panel znaczników (3), aby go włączyć lub wyłączyć.

    ![Przeglądarka Chrome z narzędziem Recenzja z oznaczonymi obrazami do sprawdzenia](images/review-images-2.png)

1. Aby wyświetlić więcej informacji na temat obrazu, kliknij wielokropek w miniaturze i wybierz **pozycję Wyświetl szczegóły**. Obraz można przypisać do podzespołów z opcją **Przenieś do** (więcej informacji o zespołach można znaleźć w sekcji [Zespoły).](./configure.md#manage-team-and-subteams)

    ![Obraz z wyróżnioną opcją Wyświetl szczegóły](images/review-images-3.png)

1. Przejrzyj informacje o moderacji obrazu na stronie szczegółów.

    ![Obraz ze szczegółami moderowania wymienionymi w osobnym okienku](images/review-images-4.png)

1. Po przejrzeniu i zaktualizowaniu przypisań tagów w razie potrzeby kliknij przycisk **Dalej,** aby przesłać swoje opinie. Po przesłaniu masz około pięciu sekund na kliknięcie przycisku **Poprzedni,** aby powrócić do poprzedniego ekranu i ponownie przejrzeć obrazy. Następnie obrazy nie znajdują się już w kolejce Przesyłania, a przycisk **Prev** nie jest już dostępny.

## <a name="text-reviews"></a>Przeglądy tekstu

Recenzje tekstu działają podobnie do recenzji obrazów. Zamiast przesyłać zawartość, wystarczy napisać lub wkleić w tekście (do 1024 znaków). Następnie moderator treści analizuje tekst i stosuje znaczniki (oprócz innych informacji moderacji, takich jak wulgaryzmy i dane osobowe). W recenzjach tekstowych można przełączać zastosowane tagi i/lub stosować znaczniki niestandardowe przed przesłaniem recenzji.

![Zrzut ekranu przedstawiający narzędzie do recenzji przedstawiające oznaczony tekst w oknie przeglądarki Chrome](../images/reviewresults_text.png)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku dowiesz się, jak skonfigurować i używać recenzji z [narzędzia Content](https://contentmoderator.cognitive.microsoft.com)Moderator Review . Następnie zapoznaj się z [przewodnikiem interfejsu API REST](../try-review-api-review.md) lub [szybkimrozkierkiem .NET SDK,](../dotnet-sdk-quickstart.md) aby dowiedzieć się, jak programowo tworzyć recenzje.