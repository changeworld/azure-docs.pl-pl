---
title: Średnie zawartości przy użyciu przeglądami ludzkich w moderatora zawartości platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć przeglądami ludzkich w konsoli zawartości moderatora interfejsu API.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 08/05/2017
ms.author: sajagtap
ms.openlocfilehash: e9faf595e65ba4475a743e4cb45919fd30fbd6e8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347004"
---
# <a name="create-reviews-from-the-api-console"></a>Utwórz przeglądami z poziomu konsoli interfejsu API

Użyj API Przejrzyj [Przejrzyj operacji](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4) utworzyć przeglądami image lub text dla człowieka łagodzenia. Użyj człowieka moderatorów przeglądu narzędzie do przeglądania zawartości. Użyj tej operacji na podstawie na logice biznesowej łagodzenia po. Go użyć po zawartości ma skanowania przy użyciu dowolnej z obrazu moderatora zawartości lub tekst interfejsów API lub innych kognitywnych interfejsów API usługi. 

Po człowieka moderatora sprawdza automatycznie przypisane tagów i dane prognozowania i przesyła decyzji końcowego łagodzenia, interfejsu API przeglądu przesyła wszystkie informacje do punktu końcowego interfejsu API.

## <a name="use-the-api-console"></a>Za pomocą konsoli interfejsu API
Aby test-drive interfejsu API za pomocą konsoli usługi online, należy kilka wartości o wprowadzenie do konsoli:

- **teamName**: Nazwa zespołu, utworzony podczas konfigurowania konta narzędzia przeglądu. 
- **ContentId**: ten ciąg jest przekazany do interfejsu API i zwrócony przez metodę wywołania zwrotnego. ContentId jest przydatna do kojarzenia identyfikatory wewnętrzny lub metadane z wyników zadania łagodzenia.
- **Metadane**: niestandardowe pary klucz wartość zwracana do punktu końcowego interfejsu API podczas wywołania zwrotnego. Jeśli klucz jest krótki kod, który jest zdefiniowany w narzędziu przeglądu, zostanie wyświetlony jako tag.
- **OCP-Apim-subskrypcji — klucz**: znajdujących się na **ustawienia** kartę. Aby uzyskać więcej informacji, zobacz [omówienie](overview.md).

Najprostszym sposobem, aby uzyskać dostępu do konsoli testowania pochodzi z **poświadczenia** okna.

1.  W **poświadczenia** wybierz [dokumentacja interfejsu API przeglądu](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4).

  **Przejrzyj — tworzenie** zostanie otwarta strona.

2.  Aby uzyskać **konsoli testowania otwarty interfejs API**, wybierz region, który najlepiej opisuje Twojej lokalizacji.

  ![Przejrzyj — Utwórz pole region strony](images/test-drive-region.png)

  **Przejrzyj — tworzenie** zostanie otwarta konsola interfejsu API.
  
3.  Wprowadź wartości parametrów zapytania wymagane, typu zawartości i klucz subskrypcji. W **treść żądania** Określ zawartości (na przykład lokalizacji obrazu), metadane i inne informacje związane z zawartością.

  ![Przejrzyj — Tworzenie konsoli parametry zapytania, nagłówki i okno treści żądania](images/test-drive-review-1.PNG)
  
4.  Wybierz pozycję **Wyślij**. Zostanie utworzony identyfikator przeglądu. Skopiuj ten identyfikator do użycia w kolejnych krokach.

  ![Przejrzyj — Tworzenie konsoli odpowiedzi zawartości pole zawiera identyfikator przeglądu](images/test-drive-review-2.PNG)
  
5.  Wybierz **uzyskać**, a następnie otwórz interfejs API, wybierając przycisk, który odpowiada Twoim regionie. Na stronie wynikowy, wprowadź wartości dla **teamName**, **ReviewID**, i **klucza subskrypcji**. Wybierz **wysyłania** przycisk na stronie. 

  ![Przejrzyj — Tworzenie konsoli wyniki Get](images/test-drive-review-3.PNG)
  
6.  Zobaczysz wyniki skanowania.

  ![Przejrzyj — Utwórz pola zawartości odpowiedzi konsoli](images/test-drive-review-4.PNG)
  
7.  Na pulpicie nawigacyjnym moderatora zawartości, wybierz **przeglądu** > **obrazu**. Pojawia się obraz, który skanowany, gotowe do przeglądu człowieka.

  ![Przejrzyj obraz narzędzia piłkę nożną](images/test-drive-review-5.PNG)

## <a name="next-steps"></a>Kolejne kroki

W kodzie za pomocą interfejsu API REST lub rozpoczynać [szybkiego startu .NET przeglądami](moderation-reviews-quickstart-dotnet.md) do integracji z aplikacji.
