---
title: Średnie obrazów za pomocą moderatora zawartości platformy Azure | Dokumentacja firmy Microsoft
description: Test-Drive łagodzenia obrazu w konsoli zawartości moderatora interfejsu API.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 08/05/2017
ms.author: sajagtap
ms.openlocfilehash: fec54826c70ae10e56c68406f629c56639985295
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347057"
---
# <a name="moderate-images-from-the-api-console"></a>Umiarkowany obrazów z poziomu konsoli interfejsu API

Użyj [API łagodzenia obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c) w Azure moderatora zawartości do zainicjowania łagodzenia skanowania i Przejrzyj przepływy pracy, dla zawartości obrazu. Zadanie łagodzenia skanuje zawartość do niestosownych wyrażeń i porównuje ją z blacklists udostępnionego i niestandardowych.

## <a name="use-the-api-console"></a>Za pomocą konsoli interfejsu API
Zanim można test-drive interfejsu API w konsoli usługi online, należy klucz subskrypcji. Ten element znajduje się na **ustawienia** karcie **Ocp-Apim-subskrypcji — klucz** pole. Aby uzyskać więcej informacji, zobacz [omówienie](overview.md).

1.  Przejdź do [dokumentacja interfejsu API łagodzenia obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c).

  **Obrazu — oceny** obrazu zostanie otwarta strona łagodzenia.

2. Aby uzyskać **konsoli testowania otwarty interfejs API**, wybierz region, który najlepiej opisuje Twojej lokalizacji. 

  ![Wypróbuj obrazu — ocenić pole region strony](images/test-drive-region.png)
  
  **Obrazu — oceny** zostanie otwarta konsola interfejsu API.

3. W **Ocp-Apim-subskrypcji — klucz** wprowadź klucz subskrypcji.

  ![Wypróbuj obrazu — ocenić klucza subskrypcji konsoli](images/try-image-api-1.PNG)

4. W **treść żądania** pole, użyć domyślnego obrazu próbki lub Określ obraz do skanowania. Obraz jako dane binarne mogą przesyłać dane bitowe lub określ publicznie dostępny adres URL obrazu. 

  Na przykład użyć ścieżki w **treść żądania** , a następnie wybierz **wysyłania**. 

   ![Wypróbuj obrazu — ocena treści żądania konsoli](images/try-image-api-2.PNG)

  Jest to obraz pod tym adresem URL:

  ![Wypróbuj obrazu — ocenić Obraz przykładowej konsoli](images/sample-image.jpg) 

5. Wybierz pozycję **Wyślij**.

6. Interfejs API zwraca wynik prawdopodobieństwa dla każdej klasyfikacji. Zwraca także określenie, czy obraz spełnia warunki (**true** lub **false**). 

  ![Spróbuj obrazu — oceń wynik prawdopodobieństwa konsoli i oznaczanie warunku](images/try-image-api-3.PNG)

## <a name="face-detection"></a>Wykrywanie twarzy

API łagodzenia obraz umożliwia zlokalizować kroje obrazu. Ta opcja może być przydatna po prywatności oraz aby zapobiec określonych krój ogłaszany na platformie. 

1.  W [dokumentacja interfejsu API łagodzenia obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c), w menu po lewej stronie w obszarze **obrazu**, wybierz pozycję **znaleźć skierowany**. 

  **Obraz — Znajdź skierowany** zostanie otwarta strona.

2.  Aby uzyskać **konsoli testowania otwarty interfejs API**, wybierz region, który najlepiej opisuje Twojej lokalizacji. 

  ![Wypróbuj obrazu — Znajdź pole region kroje strony](images/test-drive-region.png)

  **Obraz — Znajdź skierowany** zostanie otwarta konsola interfejsu API.

3. Określ obraz do skanowania. Obraz jako dane binarne mogą przesyłać dane bitowe lub określ publicznie dostępny adres URL obrazu. Ten przykład prowadzi do obrazu, który jest używany w wątku CNN.

  ![Wypróbuj obrazu — Znajdź krojów przykładowych obrazu](images/try-image-api-face-image.jpg)

  ![Wypróbuj obrazu — Znajdź kroje przykładowe żądanie](images/try-image-api-face-request.png)

4. Wybierz pozycję **Wyślij**. W tym przykładzie interfejsu API znajduje obie strony i zwraca współrzędne ich w obrazie.

   ![Wypróbuj obrazu — Znajdź okno zawartości krojów przykładowych odpowiedzi](images/try-image-api-face-response.png)

## <a name="text-detection-via-ocr-capability"></a>Wykrywanie tekstu przy użyciu funkcji Rozpoznawania

Możliwość zawartości Rozpoznawania moderatora służy do wykrywania tekstu w obrazach.

1. W [dokumentacja interfejsu API łagodzenia obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c), w menu po lewej stronie w obszarze **obrazu**, wybierz pozycję **Rozpoznawania**. 

  **Obraz — Rozpoznawania** zostanie otwarta strona.

2. Aby uzyskać **konsoli testowania otwarty interfejs API**, wybierz region, który najlepiej opisuje Twojej lokalizacji. 

  ![Obraz — pole region Rozpoznawania strony](images/test-drive-region.png)

  **Obraz — Rozpoznawania** zostanie otwarta konsola interfejsu API.

3. W **Ocp-Apim-subskrypcji — klucz** wprowadź klucz subskrypcji.

4. W **treść żądania** , używaj domyślnego obrazu próbki. Jest to ten sam obraz, który jest używany w poprzedniej sekcji.

5. Wybierz pozycję **Wyślij**. Wyodrębnionego tekstu jest wyświetlana w formacie JSON:

  ![Obraz — Rozpoznawania pola zawartości odpowiedzi próbki](images/try-image-api-ocr.PNG)

## <a name="next-steps"></a>Kolejne kroki

W kodzie za pomocą interfejsu API REST lub rozpoczynać [szybkiego startu .NET łagodzenia obrazu](image-moderation-quickstart-dotnet.md) do integracji z aplikacji.
