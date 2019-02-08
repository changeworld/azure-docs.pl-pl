---
title: Moderuj zawartość ludziom dokonywanie przy użyciu konsoli interfejsu API — Content Moderator
titlesuffix: Azure Cognitive Services
description: Za pomocą API Przejrzyj Przegląd operacji, utworzyć obraz lub tekst przeglądy dotyczące moderowania ludzi.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: d963cdd484727ac46aed1915643dd92462e2c643
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55857819"
---
# <a name="create-reviews-from-the-api-console"></a>Utwórz przeglądy z poziomu konsoli interfejsu API

Użyj API Przejrzyj [Przejrzyj operacje](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4) utworzyć image lub text przeglądów do moderacji ludzi. Moderatorzy ludzi narzędzie Przegląd Przejrzyj zawartość. Przy użyciu tej operacji, w oparciu o logikę biznesową po Moderowanie. Użyć go po zawartości zostały przeskanowane, korzystając z dowolnego obrazu Content Moderator, tekst interfejsów API lub inne interfejsy API usług Cognitive Services. 

Po ludzi moderator przeglądy automatycznie przypisane tagi i dane prognozowania i przesyła decyzji końcowego Moderowanie, interfejs API przeglądu przesyła wszystkie informacje do punktu końcowego interfejsu API.

## <a name="use-the-api-console"></a>Użyj konsoli interfejsu API
Będą mogli ją testować interfejs API za pomocą konsoli usługi online, konieczne jest kilku wartości w celu wprowadzenia w życie konsoli programu:

- **teamName**: Nazwa zespołu, które zostało utworzone podczas konfigurowania konta narzędzie do przeglądu. 
- **ContentId**: Ten ciąg jest przekazywany do interfejsu API i zwrócone za pośrednictwem wywołania zwrotnego. ContentId przydaje się do kojarzenia identyfikatory wewnętrznego lub metadanych z wyników zadania moderowania.
- **metadane**: Niestandardowe pary klucz wartość zwracana do punktu końcowego interfejsu API podczas wywołania zwrotnego. Jeśli klucz jest krótki kod, który jest zdefiniowany w narzędzie do przeglądu, pojawia się jako tag.
- **Ocp-Apim-Subscription-Key**: Znajduje się na **ustawienia** kartę. Aby uzyskać więcej informacji, zobacz [Omówienie](overview.md).

Najprostszym sposobem na dostęp do konsoli testowania pochodzi z **poświadczenia** okna.

1.  W **poświadczenia** wybierz [dokumentacja interfejsu API przeglądu](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4).

  **Przejrzyj — tworzenie** zostanie otwarta strona.

2.  Aby uzyskać **konsoli testowania interfejsu Open API**, wybierz region, który najlepiej opisuje Twojej lokalizacji.

  ![Przejrzyj — Tworzenie strony pole region](images/test-drive-region.png)

  **Przejrzyj — tworzenie** zostanie otwarta konsola interfejsu API.
  
3.  Wprowadź wartości parametrów zapytania wymagany, typ zawartości i klucz subskrypcji. W **treść żądania** Określ zawartości (na przykład obraz lokalizacji), metadane i inne informacje związane z zawartością.

  ![Przejrzyj — tworzenie parametry zapytania konsoli, nagłówki i okno treści żądania](images/test-drive-review-1.PNG)
  
4.  Wybierz pozycję **Wyślij**. Zostanie utworzony identyfikator przeglądu. Skopiuj ten identyfikator do użycia w kolejnych krokach.

  ![Przejrzyj — Tworzenie konsoli zawartości, że pole zawiera identyfikator przeglądu odpowiedzi](images/test-drive-review-2.PNG)
  
5.  Wybierz **uzyskać**, a następnie otwórz interfejs API, wybierając przycisk, który odpowiada Twoim regionie. Na stronie wynikowy wprowadź wartości dla **teamName**, **ReviewID**, i **klucz subskrypcji**. Wybierz **wysyłania** przycisk na stronie. 

  ![Przejrzyj — Tworzenie konsoli wyniki Get](images/test-drive-review-3.PNG)
  
6.  Zobaczysz wyniki skanowania.

  ![Przejrzyj — Tworzenie pola zawartości odpowiedzi konsoli](images/test-drive-review-4.PNG)
  
7.  Na pulpicie nawigacyjnym Content Moderator wybierz **przeglądu** > **obraz**. Pojawia się obraz, który skanowany, gotowe do przeglądu przez ludzi.

  ![Obraz narzędzie do przeglądu piłka nożna](images/test-drive-review-5.PNG)

## <a name="next-steps"></a>Kolejne kroki

W kodzie za pomocą interfejsu API REST lub rozpoczynać się [Szybki Start .NET przeglądy](moderation-reviews-quickstart-dotnet.md) do integracji z aplikacją.
