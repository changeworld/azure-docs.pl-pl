---
title: Tworzenie pierwszej aplikacji LUIS w 10 minut
titleSuffix: Azure Cognitive Services
description: Utworzysz aplikację LUIS używającą wstępnie utworzonej domeny `HomeAutomation` na potrzeby włączania i wyłączania świateł i urządzeń. Ta wstępnie utworzona domena zapewnia intencje, jednostki i przykładowe wypowiedzi. Po zakończeniu samouczka punkt końcowy usługi LUIS będzie działał w chmurze.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: cc4a1a7f99de657a6199985dd0c963eed741ac97
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2018
ms.locfileid: "50210027"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Szybki start: używanie wstępnie utworzonej aplikacji Home Automation

W tym przewodniku Szybki start utworzysz aplikację usługi LUIS, która używa wstępnie utworzonej domeny `HomeAutomation` na potrzeby włączania i wyłączania świateł i urządzeń. Ta wstępnie utworzona domena zapewnia intencje, jednostki i przykładowe wypowiedzi. Po zakończeniu samouczka punkt końcowy usługi LUIS będzie działał w chmurze.

## <a name="prerequisites"></a>Wymagania wstępne

Na potrzeby tego artykułu musisz dysponować bezpłatnym kontem usługi LUIS utworzonym w portalu usługi LUIS pod adresem [https://www.luis.ai](https://www.luis.ai). 

## <a name="create-a-new-app"></a>Tworzenie nowej aplikacji
Tworzenie aplikacji i zarządzanie nimi jest możliwe w obszarze **My Apps** (Moje aplikacje). 

1. Zaloguj się do portalu usługi LUIS.

2. Wybierz opcję **Create new app** (Utwórz nową aplikację).

    [![](media/luis-quickstart-new-app/app-list.png "Zrzut ekranu przedstawiający listę aplikacji")](media/luis-quickstart-new-app/app-list.png)

3. W oknie dialogowym podaj dla aplikacji nazwę „Home Automation”.

    [![](media/luis-quickstart-new-app/create-new-app-dialog.png "Zrzut ekranu z wyskakującym oknem dialogowym Tworzenie nowej aplikacji")](media/luis-quickstart-new-app/create-new-app-dialog.png)

4. Wybierz kulturę aplikacji. Na potrzeby tej aplikacji Home Automation wybierz język angielski. Następnie wybierz pozycję **Done** (Gotowe). Usługa LUIS utworzy aplikację Home Automation. 

    >[!NOTE]
    >Kultury nie można zmienić po utworzeniu aplikacji. 

## <a name="add-prebuilt-domain"></a>Dodawanie wstępnie utworzonej domeny

Wybierz pozycję **Prebuilt domains** (Wstępnie utworzone domeny) w okienku nawigacyjnym po lewej stronie. Następnie wyszukaj frazę „Home”. Wybierz opcję **Add domain** (Dodaj domenę).

[![](media/luis-quickstart-new-app/home-automation.png "Zrzut ekranu przedstawiający domenę Home Automation wywołaną w menu wstępnie utworzonych domen")](media/luis-quickstart-new-app/home-automation.png)

Po pomyślnym dodaniu domeny pole wstępnie utworzonej domeny wyświetla przycisk **Remove domain** (Usuń domenę).

[![](media/luis-quickstart-new-app/remove-domain.png "Zrzut ekranu przedstawiający domenę Home Automation z przyciskiem Remove (Usuń)")](media/luis-quickstart-new-app/remove-domain.png)

## <a name="intents-and-entities"></a>Intencje i jednostki

Wybierz opcję **Intents** (Intencje) w okienku nawigacji po lewej stronie, aby sprawdzić intencje domeny HomeAutomation. Każda intencja zawiera przykładowe wypowiedzi.

> [!NOTE]
> **None** (Brak) to intencja zapewniana przez wszystkie aplikacje usługi LUIS. Używa się jej do obsługi wypowiedzi, które nie odnoszą się do funkcji zapewnianej przez aplikację. 

Wybierz intencję **HomeAutomation.TurnOff**. Możesz zobaczyć, że intencja zawiera listę wypowiedzi, które są oznaczone przy użyciu jednostek.

[![](media/luis-quickstart-new-app/home-automation-turnon.png "Zrzut ekranu przedstawiający intencję HomeAutomation.TurnOff")](media/luis-quickstart-new-app/home-automation-turnon.png)

## <a name="train-the-luis-app"></a>Uczenie aplikacji LUIS

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-your-app"></a>Testowanie aplikacji
Po przeprowadzeniu uczenia aplikacji możesz ją przetestować. Wybierz opcję **Test** (Testuj) w górnym menu nawigacyjnym. Wpisz wypowiedź testową, np. „Turn off the lights” (Wyłącz światło) w okienku Interactive Testing (Testowanie interaktywne), a następnie naciśnij klawisz Enter. 

```
Turn off the lights
```

Sprawdź, czy intencja o najwyższym wyniku odpowiada oczekiwanej intencji dla każdej wypowiedzi testowej.

W tym przykładzie wypowiedź „Turn off the lights” (Wyłącz światło) jest prawidłowo identyfikowana jako najwyżej oceniana intencja „HomeAutomation.TurnOff”.

[![](media/luis-quickstart-new-app/test.png "Zrzut ekranu przedstawiający panel testowania z wyróżnioną wypowiedzią")](media/luis-quickstart-new-app/test.png)


Ponownie wybierz opcję **Test** (Testuj), aby zwinąć okienko testowania. 

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publikowanie aplikacji w celu uzyskania adresu URL punktu końcowego

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>Wysyłanie zapytania do punktu końcowego za pomocą różnych wypowiedzi

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

2. Przejdź na koniec tego adresu URL i wprowadź ciąg `turn off the living room light`, a następnie naciśnij klawisz Enter. W przeglądarce zostanie wyświetlona odpowiedź punktu końcowego HTTP w formacie JSON.

    [![](media/luis-quickstart-new-app/turn-off-living-room.png "Zrzut ekranu przedstawiający przeglądarkę z wynikami w formacie JSON z informacją o wykryciu intencji TurnOff")](media/luis-quickstart-new-app/turn-off-living-room.png)
    
## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

Punkt końcowy możesz wywołać z kodu:

> [!div class="nextstepaction"]
> [Wywoływanie punktu końcowego usługi LUIS przy użyciu kodu](luis-get-started-cs-get-intent.md)
