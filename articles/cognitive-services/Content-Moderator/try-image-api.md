---
title: Moderowanie obrazów za pomocą konsoli INTERFEJSU API — Moderator zawartości
titleSuffix: Azure Cognitive Services
description: Użyj interfejsu API moderowania obrazu w usłudze Azure Content Moderator, aby zainicjować przepływy pracy moderowania skanowania i przeglądu zawartości obrazu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 714621fdcc307ee8b29567fc0d95ca41d31aa9e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75448258"
---
# <a name="moderate-images-from-the-api-console"></a>Moderowanie obrazów z konsoli interfejsu API

Użyj [interfejsu API moderowania obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c) w usłudze Azure Content Moderator, aby zainicjować przepływy pracy moderowania skanowania i przeglądu zawartości obrazu. Zadanie moderowania skanuje zawartość w poszukiwaniu wulgaryzmów i porównuje je z niestandardowymi i udostępnionymi listami zablokowanych.

## <a name="use-the-api-console"></a>Korzystanie z konsoli interfejsu API
Aby można było przetestować interfejs API w konsoli online, potrzebny jest klucz subskrypcji. Znajduje się on na karcie **Ustawienia** w polu **Ocp-Apim-Subscription-Key.** Aby uzyskać więcej informacji, zobacz [Omówienie](overview.md).

1. Przejdź do [odwołania interfejsu API moderowania obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c).

   Zostanie otwarta strona **Image - Oceń** moderację obrazu.

2. W przypadku **konsoli testowania otwartego interfejsu API**wybierz region, który najbardziej opisuje Twoją lokalizację. 

   ![Wypróbuj obraz — oceń wybór regionu strony](images/test-drive-region.png)
  
   Zostanie otwarta konsola **Image - Evaluate** API.

3. W polu **Ocp-Apim-Subscription-Key** wprowadź klucz subskrypcji.

   ![Wypróbuj obraz — oceń klucz subskrypcji konsoli](images/try-image-api-1.PNG)

4. W **treści Żądanie** użyj domyślnego przykładowego obrazu lub określ obraz do skanowania. Sam obraz można przesłać jako binarne dane bitowe lub określić publicznie dostępny adres URL obrazu. 

   W tym przykładzie użyj ścieżki podanej w **treści Żądanie,** a następnie wybierz pozycję **Wyślij**. 

   ![Wypróbuj obraz — ocena treści żądania konsoli](images/try-image-api-2.PNG)

   Oto obraz pod tym adresem URL:

   ![Wypróbuj obraz — ocena przykładowego obrazu konsoli](images/sample-image.jpg) 

5. Wybierz pozycję **Wyślij**.

6. Interfejs API zwraca wynik prawdopodobieństwa dla każdej klasyfikacji. Zwraca również określenie, czy obraz spełnia warunki (**prawda** czy **fałsz**). 

   ![Wypróbuj obraz — ocena wyniku prawdopodobieństwa konsoli i określenie stanu](images/try-image-api-3.PNG)

## <a name="face-detection"></a>Wykrywanie twarzy

Za pomocą interfejsu API moderowania obrazu można zlokalizować twarze na obrazie. Ta opcja może być przydatna, gdy masz problemy z prywatnością i chcesz zapobiec publikowaniu określonej twarzy na twojej platformie. 

1. W [odwołaniu interfejsu API moderowania obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c)w menu po lewej stronie w obszarze **Obraz**wybierz pozycję **Znajdź twarze**. 

   Zostanie otwarta strona **Obraz — Znajdź twarze.**

2. W przypadku **konsoli testowania otwartego interfejsu API**wybierz region, który najbardziej opisuje Twoją lokalizację. 

   ![Wypróbuj obraz — znajdź wybór regionu strony Ściany](images/test-drive-region.png)

   Zostanie otwarta konsola interfejsu API **Obraz — Znajdź twarze.**

3. Określ obraz do skanowania. Sam obraz można przesłać jako binarne dane bitowe lub określić publicznie dostępny adres URL do obrazu. W tym przykładzie znajdują się łącza do obrazu używanego w wątku CNN.

   ![Wypróbuj obraz — znajdź przykładowy obraz twarzy](images/try-image-api-face-image.jpg)

   ![Wypróbuj obraz — żądanie przykładu Znajdź twarze](images/try-image-api-face-request.png)

4. Wybierz pozycję **Wyślij**. W tym przykładzie interfejs API znajduje dwie ściany i zwraca ich współrzędne na obrazie.

   ![Wypróbuj obraz — pole zawartość próbki Znajdź twarze](images/try-image-api-face-response.png)

## <a name="text-detection-via-ocr-capability"></a>Wykrywanie tekstu za pomocą funkcji OCR

Za pomocą funkcji OCR moderatora zawartości można wykrywać tekst na obrazach.

1. W [odwołaniu interfejsu API moderowania obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c)w menu po lewej stronie w obszarze **Obraz**wybierz pozycję **OCR**. 

   Zostanie otwarta strona **Obraz — OCR.**

2. W przypadku **konsoli testowania otwartego interfejsu API**wybierz region, który najbardziej opisuje Twoją lokalizację. 

   ![Obraz — wybór regionu strony OCR](images/test-drive-region.png)

   Zostanie otwarta konsola interfejsu API **Image — OCR.**

3. W polu **Ocp-Apim-Subscription-Key** wprowadź klucz subskrypcji.

4. W **treści Żądanie** użyj domyślnego przykładowego obrazu. Jest to ten sam obraz, który jest używany w poprzedniej sekcji.

5. Wybierz pozycję **Wyślij**. Wyodrębniony tekst jest wyświetlany w JSON:

   ![Obraz — pole zawartości próbki OCR Response](images/try-image-api-ocr.PNG)

## <a name="next-steps"></a>Następne kroki

Użyj interfejsu API REST w kodzie lub postępuj zgodnie z [paskiem szybkiego dostępu do interfejsu .NET SDK,](dotnet-sdk-quickstart.md) aby dodać moderację obrazu do aplikacji.
