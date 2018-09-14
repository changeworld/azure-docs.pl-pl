---
title: Tworzenie pierwszej aplikacji usługi Language Understanding (LUIS) w ciągu 10 minut — Cognitive Services LUIS | Microsoft Docs
description: W tym przewodniku Szybki start utworzysz aplikację usługi LUIS, która używa wstępnie utworzonej domeny `HomeAutomation` na potrzeby włączania i wyłączania świateł i urządzeń. Ta wstępnie utworzona domena zapewnia intencje, jednostki i przykładowe wypowiedzi. Po zakończeniu samouczka punkt końcowy usługi LUIS będzie działał w chmurze.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/22/2018
ms.author: diberry
ms.openlocfilehash: 457f23936dec0cf85e9aebbf3e54bba37c2f3ca3
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/23/2018
ms.locfileid: "43771646"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Szybki start: używanie wstępnie utworzonej aplikacji Home Automation

W tym przewodniku Szybki start utworzysz aplikację usługi LUIS, która używa wstępnie utworzonej domeny `HomeAutomation` na potrzeby włączania i wyłączania świateł i urządzeń. Ta wstępnie utworzona domena zapewnia intencje, jednostki i przykładowe wypowiedzi. Po zakończeniu samouczka punkt końcowy usługi LUIS będzie działał w chmurze.

## <a name="prerequisites"></a>Wymagania wstępne

Na potrzeby tego artykułu musisz dysponować bezpłatnym kontem usługi LUIS utworzonym w portalu usługi LUIS pod adresem [http://www.luis.ai](http://www.luis.ai). 

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

Wybierz opcję **Intents** (Intencje) w okienku nawigacji po lewej stronie, aby sprawdzić intencje domeny HomeAutomation. 

[![](media/luis-quickstart-new-app/home-automation-intents.png "Zrzut ekranu przedstawiający listę Intents (Intencje) z nazwami intencji wyróżnionymi w tabeli")](media/luis-quickstart-new-app/home-automation-intents.png)

Każda intencja zawiera przykładowe wypowiedzi.

> [!NOTE]
> **None** (Brak) to intencja zapewniana przez wszystkie aplikacje usługi LUIS. Używa się jej do obsługi wypowiedzi, które nie odnoszą się do funkcji zapewnianej przez aplikację. 

Wybierz intencję **HomeAutomation.TurnOff**. Możesz zobaczyć, że intencja zawiera listę wypowiedzi, które są oznaczone przy użyciu jednostek.

[![](media/luis-quickstart-new-app/home-automation-turnon.png "Zrzut ekranu przedstawiający intencję HomeAutomation.TurnOff")](media/luis-quickstart-new-app/home-automation-turnon.png)

## <a name="train-your-app"></a>Uczenie aplikacji

Wybierz opcję **Train** (Ucz) w górnym menu nawigacyjnym.

[![](media/luis-quickstart-new-app/trained.png "Zrzut ekranu przedstawiający intencję HomeAutomation.TurnOff z zielonym powiadomieniem o powodzeniu")](media/luis-quickstart-new-app/trained.png)

## <a name="test-your-app"></a>Testowanie aplikacji
Po przeprowadzeniu uczenia aplikacji możesz ją przetestować. Wybierz opcję **Test** (Testuj) w górnym menu nawigacyjnym. Wpisz wypowiedź testową, np. „Turn off the lights” (Wyłącz światło) w okienku Interactive Testing (Testowanie interaktywne), a następnie naciśnij klawisz Enter. 

```
Turn off the lights
```

Sprawdź, czy intencja o najwyższym wyniku odpowiada oczekiwanej intencji dla każdej wypowiedzi testowej.

W tym przykładzie wypowiedź „Turn off the lights” (Wyłącz światło) jest prawidłowo identyfikowana jako najwyżej oceniana intencja „HomeAutomation.TurnOff”.

[![](media/luis-quickstart-new-app/test.png "Zrzut ekranu przedstawiający panel testowania z wyróżnioną wypowiedzią")](media/luis-quickstart-new-app/test.png)


Ponownie wybierz opcję **Test** (Testuj), aby zwinąć okienko testowania. 

## <a name="publish-your-app"></a>Publikowanie aplikacji
Wybierz opcję **Publish** (Publikuj) w górnym menu nawigacyjnym. 

[![](media/luis-quickstart-new-app/publish.png "Zrzut ekranu aplikacji z wyróżnionym przyciskiem Publish (Publikuj)")](media/luis-quickstart-new-app/publish.png)

Wybierz miejsce produkcyjne i przycisk **Publish** (Publikuj).

Zielony pasek powiadomienia w górnej części wskazuje pomyślne opublikowanie aplikacji.

[![](media/luis-quickstart-new-app/published.png "Zrzut ekranu przedstawiający aplikację, która została pomyślnie opublikowana")](media/luis-quickstart-new-app/published.png)

Po pomyślnym opublikowaniu możesz użyć adresu URL punktu końcowego wyświetlanego na stronie **Publish app** (Publikowanie aplikacji).

[![](media/luis-quickstart-new-app/endpoint.png "Zrzut ekranu przedstawiający stronę publikowania z wyróżnionym adresem URL punktu końcowego")](media/luis-quickstart-new-app/endpoint.png)

## <a name="use-your-app"></a>Używanie aplikacji
Opublikowany punkt końcowy możesz przetestować w przeglądarce przy użyciu wygenerowanego adresu URL. Otwórz ten adres URL w przeglądarce i ustaw parametr adresu URL „&q” na zapytanie testowe. Na przykład dodaj wypowiedź `turn off the living room light` na końcu adresu URL, a następnie naciśnij klawisz Enter. W przeglądarce zostanie wyświetlona odpowiedź punktu końcowego HTTP w formacie JSON.


[![](media/luis-quickstart-new-app/turn-off-living-room.png "Zrzut ekranu przedstawiający przeglądarkę z wynikami w formacie JSON z informacją o wykryciu intencji TurnOff")](media/luis-quickstart-new-app/turn-off-living-room.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Gdy aplikacja LUIS nie będzie już potrzebna, usuń ją. Aby to zrobić, wybierz wielokropek (**...**) po prawej stronie nazwy aplikacji na liście aplikacji i wybierz polecenie ***Delete*** (Usuń). W wyskakującym oknie dialogowym **Delete app?** (Usunąć aplikację?) wybierz pozycję **OK**.

## <a name="next-steps"></a>Następne kroki

Punkt końcowy możesz wywołać z kodu:

> [!div class="nextstepaction"]
> [Wywoływanie punktu końcowego usługi LUIS przy użyciu kodu](luis-get-started-cs-get-intent.md)
