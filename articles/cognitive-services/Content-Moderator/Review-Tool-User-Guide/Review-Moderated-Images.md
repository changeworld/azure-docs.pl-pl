---
title: Użyj zawartości przeglądy przez narzędzie do przeglądu - Content Moderator
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak narzędzie do przeglądu umożliwia moderatorzy ludzi zapoznać się z obrazów w portalu sieci web.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/15/2019
ms.author: sajagtap
ms.openlocfilehash: a482ecf4a0d321525ab7e392695d2c4c0eebeadc
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58758484"
---
# <a name="create-human-reviews"></a>Utwórz ludziom dokonywanie

W tym przewodniku dowiesz się, jak skonfigurować [przeglądy](../review-api.md#reviews) witrynie sieci Web narzędzie do przeglądu. Przeglądy przechowywania i wyświetlania zawartości dla moderatorów ludzi do oceny. Moderatorzy mogą zmienić zastosować znaczniki i Zastosuj własne niestandardowe tagi, zgodnie z potrzebami. Po ukończeniu weryfikacji użytkownika wyniki są wysyłane do punktu końcowego określonego wywołania zwrotnego, a zawartość zostanie usunięta z lokacji.

## <a name="prerequisites"></a>Wymagania wstępne

- Zaloguj się lub Utwórz konto w pakiecie Content Moderator [narzędzie do przeglądu](https://contentmoderator.cognitive.microsoft.com/) lokacji.

## <a name="image-reviews"></a>Przeglądy obrazów

1. Przejdź do [narzędzie do przeglądu](https://contentmoderator.cognitive.microsoft.com/), wybierz opcję **spróbuj** , a następnie przekaż niektóre obrazy, aby przejrzeć.
1. Przekazane obrazy zostały wykonane przetwarzanie, przejdź do **przeglądu** kartę, a następnie wybierz pozycję **obraz**.

    ![Przeglądarka Chrome przedstawiający narzędzie do przeglądu z podświetloną opcją Przejrzyj obrazu](images/review-images-1.png)

    Wyświetl obrazy wszystkich etykiet, które zostały przypisane przez proces moderowania automatyczne. Obrazy, które zostały przesłane przez narzędzie do przeglądu nie są widoczne dla innych recenzentów.

1. Opcjonalnie można przenieść **recenzji do wyświetlenia** suwak (1), aby dostosować liczbę obrazów, które są wyświetlane na ekranie. Kliknij pozycję **otagowanych** lub **nieoznakowany** przycisków (2), aby odpowiednio sortować obrazów. Kliknij panel tag (3) lub wyłącz ją wyłączyć.

    ![Przeglądarka Chrome przedstawiający narzędzie do przeglądu przy użyciu oznakowane obrazów do przeglądu](images/review-images-2.png)

1. Aby uzyskać więcej informacji na obrazie, kliknij wielokropek w miniatury, a następnie wybierz pozycję **wyświetlić szczegóły**. Obraz można przypisać do ustalenie z **przenieść do** opcji (zobacz [zespołów](./configure.md#manage-team-and-subteams) sekcji, aby dowiedzieć się więcej o zespoły podrzędne).

    ![Obraz z podświetloną opcją szczegółów widoku](images/review-images-3.png)

1. Przeglądaj informacje Moderowanie obrazów, na stronie szczegółów.

    ![Obraz ze szczegółami Moderowanie wymienione w oddzielne okienko](images/review-images-4.png)

1. Po przejrzeniu i aktualizowane przypisania tagu w razie potrzeby kliknij **dalej** przesłać swoje recenzje. Po przesłaniu, około pięciu sekundach być konieczne kliknięcie pozycji **poprzedni** przycisk, aby powrócić do poprzedniego ekranu i ponownie Przejrzyj obrazy. Po tym obrazy nie są już w kolejce przesyłania i **poprzedni** przycisk nie jest już dostępna.

## <a name="text-reviews"></a>Przeglądy tekstu

Tekst przegląda działają podobnie jak recenzje obrazu. Zamiast przekazywania zawartości, możesz po prostu zapisać lub wklej tekst (maksymalnie 1024 znaki). Następnie pakietu Content Moderator analizuje tekst i ma zastosowanie znaczników (oprócz innych informacji Moderowanie, takich jak wulgaryzmów i danych osobowych). W przeglądy tekstu użytkownik może przełączać zastosować znaczniki i/lub zastosować znaczniki niestandardowe przed przesłaniem przeglądu.

![Zrzut ekranu przedstawiający narzędzie do przeglądu oflagowane tekstu w oknie przeglądarki Chrome](../images/reviewresults_text.png)

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku przedstawiono sposób konfigurowania i używania weryfikacje od pakietu Content Moderator [narzędzie do przeglądu](https://contentmoderator.cognitive.microsoft.com). Następnie możesz zapoznać się [Podręcznik interfejsu API REST](../try-review-api-review.md) lub [przewodnika zestawu SDK .NET](../moderation-reviews-quickstart-dotnet.md) dowiesz się, jak programowo utworzyć recenzji.