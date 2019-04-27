---
title: Umiarkowany obrazów przy użyciu konsoli interfejsu API — Content Moderator
titlesuffix: Azure Cognitive Services
description: Użyj interfejsu API moderowania obrazów w usłudze Azure Content Moderator zainicjować Moderowanie skanowania i Przejrzyj przepływy pracy na potrzeby zawartości obrazu.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 1e4efa5e06525194bfdc7d1932fcfec5ec9f8c6b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60607407"
---
# <a name="moderate-images-from-the-api-console"></a>Umiarkowany obrazów z poziomu konsoli interfejsu API

Użyj [interfejs API moderowania obrazów](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c) w usłudze Azure Content Moderator zainicjować Moderowanie skanowania i Przejrzyj przepływy pracy na potrzeby zawartości obrazu. Zadanie Moderowanie skanuje zawartości pod kątem wulgarności i porównuje ją z udostępnionych i niestandardowych list elementów zabronionych.

## <a name="use-the-api-console"></a>Użyj konsoli interfejsu API
Zanim można Testuj interfejs API w konsoli usługi online, należy się klucz subskrypcji. Ten element znajduje się na **ustawienia** na karcie **Ocp-Apim-Subscription-Key** pole. Aby uzyskać więcej informacji, zobacz [Omówienie](overview.md).

1. Przejdź do [dokumentacja interfejsu API moderowania obrazów](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c).

   **Obrazu — ocena** zostanie otwarta strona moderowania obrazów.

2. Aby uzyskać **konsoli testowania interfejsu Open API**, wybierz region, który najlepiej opisuje Twojej lokalizacji. 

   ![Wypróbuj obrazu — ocena pole region strony](images/test-drive-region.png)
  
   **Obrazu — ocena** zostanie otwarta konsola interfejsu API.

3. W **Ocp-Apim-Subscription-Key** wprowadź swój klucz subskrypcji.

   ![Wypróbuj obrazu — ocena konsoli klucz subskrypcji](images/try-image-api-1.PNG)

4. W **treść żądania** pole, użyć domyślnego obrazu przykładowe lub Określ obraz do skanowania. Możesz przesłać obraz jako wartość binarną bitów danych lub określ publicznie dostępny adres URL obrazu. 

   W tym przykładzie użyj ścieżce podanej w **treść żądania** , a następnie wybierz **wysyłania**. 

   ![Wypróbuj obrazu — ocena treści żądania w konsoli](images/try-image-api-2.PNG)

   Jest to obraz pod tym adresem URL:

   ![Wypróbuj obrazu — ocena konsoli przykładowy obraz](images/sample-image.jpg) 

5. Wybierz pozycję **Wyślij**.

6. Interfejs API zwraca wynik prawdopodobieństwa dla każdej klasyfikacji. Zwraca również ustalenie tego, czy obraz, który spełnia warunki (**true** lub **false**). 

   ![Spróbuj obrazu — ocena wynik prawdopodobieństwa konsoli i określenie warunku](images/try-image-api-3.PNG)

## <a name="face-detection"></a>Wykrywanie twarzy

Aby zlokalizować twarzy na obrazie, można użyć interfejsu API moderowania obrazów. Ta opcja może być przydatna po kwestie prywatności i aby zapobiec określonych rozpoznawania twarzy jest opublikowany na Twojej platformie. 

1. W [dokumentacja interfejsu API moderowania obrazów](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c), w menu po lewej stronie w obszarze **obraz**, wybierz opcję **znaleźć twarzy**. 

   **Obraz — Znajdź twarzy** zostanie otwarta strona.

2. Aby uzyskać **konsoli testowania interfejsu Open API**, wybierz region, który najlepiej opisuje Twojej lokalizacji. 

   ![Wypróbuj obrazu — Znajdź wyboru regionu strony twarzy](images/test-drive-region.png)

   **Obraz — Znajdź twarzy** zostanie otwarta konsola interfejsu API.

3. Określ obraz do skanowania. Możesz przesłać obraz jako wartość binarną bitów danych lub określ publicznie dostępny adres URL obrazu. Ten przykład prowadzi do obrazu, który jest używany w wątku CNN.

   ![Wypróbuj obrazu — Znajdź twarzy przykładowy obraz](images/try-image-api-face-image.jpg)

   ![Wypróbuj obrazu — Znajdź twarzy przykładowe żądanie](images/try-image-api-face-request.png)

4. Wybierz pozycję **Wyślij**. W tym przykładzie interfejs API znajdzie dwie twarze i zwraca ich współrzędne na obrazie.

   ![Wypróbuj obrazu — Znajdź okno zawartości odpowiedzi przykładowe twarzy](images/try-image-api-face-response.png)

## <a name="text-detection-via-ocr-capability"></a>Wykrywanie tekstu za pomocą funkcji optycznego rozpoznawania znaków

Funkcja Content Moderator OCR umożliwia wykrywanie tekstu w obrazach.

1. W [dokumentacja interfejsu API moderowania obrazów](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c), w menu po lewej stronie w obszarze **obraz**, wybierz opcję **optyczne rozpoznawanie znaków**. 

   **Obraz — optyczne rozpoznawanie znaków** zostanie otwarta strona.

2. Aby uzyskać **konsoli testowania interfejsu Open API**, wybierz region, który najlepiej opisuje Twojej lokalizacji. 

   ![Obraz — pole region strony optyczne rozpoznawanie znaków](images/test-drive-region.png)

   **Obraz — optyczne rozpoznawanie znaków** zostanie otwarta konsola interfejsu API.

3. W **Ocp-Apim-Subscription-Key** wprowadź swój klucz subskrypcji.

4. W **treść żądania** Użyj domyślnego obrazu próbki. Jest to ten sam obraz, który jest używany w poprzedniej sekcji.

5. Wybierz pozycję **Wyślij**. Wyodrębniony tekst jest wyświetlany w formacie JSON:

   ![Obraz — pola zawartości odpowiedzi przykładowe optyczne rozpoznawanie znaków](images/try-image-api-ocr.PNG)

## <a name="next-steps"></a>Kolejne kroki

W kodzie za pomocą interfejsu API REST lub rozpoczynać się [Szybki Start .NET moderowania obrazów](image-moderation-quickstart-dotnet.md) do integracji z aplikacją.
