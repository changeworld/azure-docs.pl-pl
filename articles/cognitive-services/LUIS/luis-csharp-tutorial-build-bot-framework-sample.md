---
title: Integracja LUIS z robotów dla C# na platformie Azure przy użyciu zestawu SDK konstruktora Bot | Dokumentacja firmy Microsoft
description: Tworzenie robotów zintegrowany z aplikacją LUIS przy użyciu platformy Bot.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/06/2018
ms.author: v-geberr
ms.openlocfilehash: 3db2588edefa2f07d22609d7c454367d5572d5e0
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "35356132"
---
# <a name="web-app-bot-using-the-luis-template-for-c"></a>Bot aplikacji sieci Web przy użyciu szablonu LUIS w języku C#

Tworzenie chatbot opis zintegrowane języka.

## <a name="prerequisite"></a>Wymagania wstępne

* [Aplikacja LUIS HomeAutomation](luis-get-started-create-app.md). Profile z tej mapy aplikacji LUIS do bot okno obsługi. 

## <a name="luis-homeautomation-intents"></a>Intencje LUIS HomeAutomation

| Celem | Przykład utterance | Funkcje BOT |
|:----:|:----------:|---|
| HomeAutomation.TurnOn | Włącz kontrolki. | Gdy zamiar LUIS `HomeAutomation.TurnOn` wykryciu wywołuje bot `OnIntent` okno obsługi. To okno dialogowe jest, gdzie należy wywołać usługi IoT, aby włączyć na urządzeniu i monituj użytkownika, że urządzenie zostało włączone. |
| HomeAutomation.TurnOff | Wyłącz światła sypialnię. | Gdy zamiar LUIS `HomeAutomation.TurnOff` wykryciu wywołuje bot `OffIntent` okno obsługi. To okno dialogowe jest, gdzie należy wywołać usługi IoT, aby wyłączyć urządzenie i poinformuj użytkownika, że urządzenie zostało wyłączone. |

## <a name="create-a-language-understanding-bot-with-bot-service"></a>Utwórz bot opis języka z usługą Bot

1. W [portalu Azure](https://portal.azure.com), wybierz pozycję **utworzyć nowy zasób** w menu u góry po lewej stronie.

    ![Tworzenie nowego zasobu](./media/luis-tutorial-cscharp-web-bot/bot-service-creation.png)

2. W polu wyszukiwania, wyszukaj **Bot aplikacji sieci Web**. 

    ![Tworzenie nowego zasobu](./media/luis-tutorial-cscharp-web-bot/bot-service-selection.png)

3. W oknie Bot aplikacji sieci Web kliknij **Utwórz**.

4. W **usługi Bot**, podaj wymagane informacje i kliknij przycisk **Utwórz**. To tworzy i wdraża bot usług i aplikacji LUIS na platformie Azure. Jeśli chcesz użyć [plucia mowy](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming), przejrzyj [wymagania region](luis-resources-faq.md#what-luis-regions-support-bot-framework-speech-priming) przed utworzeniem sieci bot. 
    * Ustaw **Nazwa aplikacji** Twojego bot nazwy. Nazwa jest używana jako poddomeny po wdrożeniu programu bot do chmury (na przykład mynotesbot.azurewebsites.net). <!-- This name is also used as the name of the LUIS app associated with your bot. Copy it to use later, to find the LUIS app associated with the bot. -->
    * Wybierz subskrypcję, [grupy zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), plan usługi App service, i [lokalizacji](https://azure.microsoft.com/regions/).
    * Wybierz **opis języka (C#)** szablon **szablonu Bot** pola.
    * Wybierz **lokalizacji aplikacji LUIS**. Jest to tworzeniem [region] [ LUIS] aplikacji jest tworzony w.
    * Zaznacz pole wyboru potwierdzenia powiadomień prawnych. Warunki prawne komunikatu znajdują się poniżej pola wyboru.

    ![Bot Service](./media/luis-tutorial-cscharp-web-bot/bot-service-setting-callout-template.png)


5. Upewnij się, że wdrożono usługę bot.
    * Kliknij przycisk powiadomienia (ikonę dzwonka, która jest wzdłuż górnej krawędzi portalu Azure). Powiadomienie zostanie zmieniony z **rozpoczęto wdrażanie** do **wdrożenie zakończyło się pomyślnie**.
    * Po zmianie powiadomienia do **wdrożenie zakończyło się pomyślnie**, kliknij przycisk **przejdź do zasobu** na powiadomienie.

> [!Note]
> Ten proces tworzenia bot aplikacji sieci web również utworzone Nowa aplikacja LUIS automatycznie. Został uczony i opublikowanych dla użytkownika. 

## <a name="try-the-default-bot"></a>Spróbuj bot domyślne

Upewnij się, że bot został wdrożony, wybierając **powiadomienia** wyboru. Powiadomienia zmieni się z **wdrożenie w toku...**  do **wdrożenie zakończyło się pomyślnie**. Kliknij przycisk **przejdź do zasobu** przycisk, aby otworzyć bot zasobów.

Po wdrożeniu bot kliknij **testów w sieci Web rozmowę** aby otworzyć okienko rozmowę sieci Web. Wpisz tekst "hello" w sieci Web rozmawiać.

  ![Testowanie bot w sieci Web rozmowę](./media/luis-tutorial-cscharp-web-bot/bot-service-web-chat.png)

Bot odpowiada mówiąc "osiągnięto pozdrowienie. Możesz powiedzieć: hello ".  Ta odpowiedź potwierdza, że bot ma odebrane wiadomości i przekazanego domyślnej aplikacji LUIS, utworzony. To ustawienie domyślne LUIS aplikacji wykryto zamiar pozdrowienie. W następnym kroku połączę się z bot aplikacji LUIS utworzonych wcześniej zamiast domyślnej LUIS aplikacji.

## <a name="connect-your-luis-app-to-the-bot"></a>Łączenie aplikacji z LUIS bot

Otwórz **ustawienia aplikacji** i edytować **LuisAppId** pole ma zawierać identyfikator aplikacji LUIS aplikacji. Jeśli utworzono aplikację HomeAutomation LUIS w regionie innym niż zachodnie stany USA, musisz zmienić **LuisAPIHostName** również. **LuisAPIKey** ma obecnie ustawioną tworzenia klucza. Możesz zmienić to do Twojej subskrypcji klucza podczas ruchu przekracza limit przydziału warstwę bezpłatna. 

  ![Aktualizuj LUIS identyfikator aplikacji na platformie Azure](./media/luis-tutorial-cscharp-web-bot/bot-service-app-settings.png)

> [!Note]
> Jeśli nie masz identyfikator aplikacji LUIS [aplikacji Home automatyzacji](luis-get-started-create-app.md), zaloguj się do [LUIS](luis-reference-regions.md) witryny sieci Web przy użyciu tego samego konta, które są używane do logowania się do platformy Azure. 
> 1. Polecenie **Moje aplikacje**. 
> 2. Znajdź LUIS utworzonego wcześniej, zawierający intencje i jednostek z domeny HomeAutomation.
> 3. W **ustawienia** stronie LUIS aplikacji, należy znaleźć i skopiować identyfikator aplikacji. Upewnij się, że jest [uczonego](Train-Test.md) i [opublikowane](PublishApp.md). 

    > [!WARNING]
    > If you delete your app ID or LUIS key, the bot will stop working.

## <a name="modify-the-bot-code"></a>Zmodyfikuj kod bot

1. Kliknij przycisk **kompilacji** , a następnie kliknij przycisk **Edytor Otwórz kodu online**.

   ![Otwórz online Edytor kodu](./media/luis-tutorial-cscharp-web-bot/bot-service-build.png)

2. Kliknij prawym przyciskiem myszy `build.cmd` i wybierz polecenie **uruchomić z konsoli** do tworzenia aplikacji. Istnieje kilka czynności kompilacji, które usługa kończy się automatycznie za Ciebie. Kompilacja zostanie zakończona, po jego zakończeniu "Zakończyło się pomyślnie."

3. Otwórz w edytorze kodu `/Dialogs/BasicLuisDialog.cs`. Ten przewodnik zawiera następujący kod:

   [!code-csharp[Default BasicLuisDialog.cs](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/Default_BasicLuisDialog.cs "Default BasicLuisDialog.cs")]

## <a name="change-code-to-homeautomation-intents"></a>Zmiana kodu na intencje HomeAutomation


1. Usuń trzy atrybuty konwersji i metody dla **pozdrowienia**, **anulować**, i **pomocy**. Te opcje nie są używane w domenie wbudowane HomeAutomation. Upewnij się zachować **Brak** atrybutu konwersji i metody. 

2. Dodaj zależności do górnej części pliku, z innych zależności:

   [!code-csharp[Dependencies](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=4-5&dedent=8 "dependencies")]

3. Dodaj stałe zarządzanie ciągów w górnej części `BasicLuisDialog ` klasy:

   [!code-csharp[Add Intent and Entity Constants](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=23-32&dedent=8 "Add Intent and Entity Constants")]

4. Dodaj kod dla nowych opcji programu `HomeAutomation.TurnOn` i `HomeAutomation.TurnOff` wewnątrz `BasicLuisDialog ` klasy:

   [!code-csharp[Add Intents](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=61-71&dedent=8 "Add Intents")]

5. Dodaj kod, aby pobrać wszystkie elementy znalezione przez LUIS wewnątrz `BasicLuisDialog ` klasy:

   [!code-csharp[Collect entities](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=34-53&dedent=8 "Collect entities")]

6. Zmień **ShowLuisResult** metoda `BasicLuisDialog ` klasy zaokrąglona wynik zbierania jednostek i wyświetlenia komunikatu odpowiedzi w chatbot:

   [!code-csharp[Display message in chatbot](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=73-83&dedent=8 "Display message in chatbot")]

## <a name="build-the-bot"></a>Tworzenie bot
W edytorze kodu, kliknij prawym przyciskiem myszy `build.cmd` i wybierz **uruchomić z konsoli**.

![Tworzenie bot sieci Web ](./media/luis-tutorial-cscharp-web-bot/bot-service-build-run-from-console.png)

Widok kodu jest zastępowany terminali okna pokazującego postęp i wyniki kompilacji.

![Kompilacja zakończona sukcesem bot sieci Web](./media/luis-tutorial-cscharp-web-bot/bot-service-build-success.png)

> [!TIP]
> Alternatywna metoda tworzenia bot jest wybierz nazwę bot w górnym pasku niebieski, a następnie wybierz **Otwórz konsolę Kudu**. Otwiera konsolę do **D:\home**. 
> 
> Zmień katalog na **D:\home\site\wwwroot** , wpisując: `cd site\wwwroot`
>
> Uruchom skrypt kompilacji, wpisując: `build.cmd`

## <a name="test-the-bot"></a>Testowanie bot

W portalu Azure kliknij **testów w sieci Web rozmowę** do testowania bot. Wpisz komunikaty like "Włącz kontrolki" i "Wyłącz Moje grzejnik" można wywołać lokalizacji docelowych, które dodano do niego.

   ![Testowanie HomeAutomation bot w sieci Web rozmowę](./media/luis-tutorial-cscharp-web-bot/bot-service-chat-results.png)

> [!TIP]
> Można ponownie ucz aplikacji LUIS bez żadnych modyfikacji kodu z bot. Zobacz [dodać zniesławiających przykład](https://docs.microsoft.com/azure/cognitive-services/LUIS/add-example-utterances) i [nauczenia i przetestowania aplikacji LUIS](https://docs.microsoft.com/azure/cognitive-services/LUIS/train-test). 

## <a name="download-the-bot-to-debug"></a>Pobierz bot do debugowania
Jeśli Twoje bot nie działa, Pobierz projektu na komputerze lokalnym i kontynuować [debugowania](https://docs.microsoft.com/bot-framework/bot-service-debug-bot#debug-an-azure-app-service-web-app-c-bot). 

## <a name="learn-more-about-bot-framework"></a>Dowiedz się więcej o Bot Framework
Dowiedz się więcej o [Bot Framework](https://dev.botframework.com/) i [3.x](https://github.com/Microsoft/BotBuilder) i [4.x](https://github.com/Microsoft/botbuilder-dotnet) zestawów SDK.

## <a name="next-steps"></a>Kolejne kroki

Dodaj LUIS intencje i Bot okien obsługi **pomocy**, **anulować**, i **pozdrowienia** lokalizacji docelowych. Pamiętaj, aby uczenia, publikowanie i [kompilacji](#build-the-bot) bot aplikacji sieci web. Zarówno LUIS i bot powinien mieć tej samej lokalizacji docelowych.

> [!div class="nextstepaction"]
> [Dodaj intencje](./luis-how-to-add-intents.md)
> [plucia mowy](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming)
<!-- Links -->
[Github-BotFramework-Emulator-Download]: https://aka.ms/bot-framework-emulator
[Github-LUIS-Samples]: https://github.com/Microsoft/LUIS-Samples
[Github-LUIS-Samples-cs-hotel-bot]: https://github.com/Microsoft/LUIS-Samples/tree/master/bot-integration-samples/hotel-finder/csharp
[Github-LUIS-Samples-cs-hotel-bot-readme]: https://github.com/Microsoft/LUIS-Samples/blob/master/bot-integration-samples/hotel-finder/csharp/README.md
[BFPortal]: https://dev.botframework.com/
[RegisterInstructions]: https://docs.microsoft.com/bot-framework/portal-register-bot
[BotFramework]: https://docs.microsoft.com/bot-framework/
[AssignedEndpointDoc]:https://docs.microsoft.com/azure/cognitive-services/LUIS/manage-keys
[VisualStudio]: https://www.visualstudio.com/
[LUIS]:luis-reference-regions.md
<!-- tested on Win10 -->
