---
title: Bot — C# -v3
titleSuffix: Language Understanding - Azure Cognitive Services
description: Przy użyciu języka C# twórz czatbot zintegrowany z usługą Language Understanding (LUIS). Ten czatbot używa wstępnie domeny HomeAutomation można szybko wdrożyć rozwiązanie botów.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/09/2019
ms.author: diberry
ms.openlocfilehash: f23cf78bfca48b3a78e234520d645abdb354038f
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58878472"
---
# <a name="luis-bot-in-c-with-the-bot-framework-3x-and-the-azure-web-app-bot"></a>Bot usługi LUIS w C# przy użyciu platformy Bot Framework bota aplikacji sieci Web platformy Azure i 3.x

Przy użyciu języka C# twórz czatbot zintegrowany z usługą Language Understanding (LUIS). Ten czatbot używa wstępnie domeny HomeAutomation można szybko wdrożyć rozwiązanie botów. Bot został utworzony za pomocą platformy Bot Framework bota aplikacji sieci Web platformy Azure i 3.x.

## <a name="prerequisite"></a>Wymagania wstępne

* [Aplikacją usługi LUIS HomeAutomation](luis-get-started-create-app.md). Intencji z tej usługi LUIS mapy aplikacji do obsługi okna dialogowego botów. 

## <a name="luis-homeautomation-intents"></a>Usługa LUIS HomeAutomation intencji

| Intencja | Przykładowa wypowiedź | Bot funkcji |
|:----:|:----------:|---|
| HomeAutomation.TurnOn | Włączenie świateł. | Gdy celem usługi LUIS `HomeAutomation.TurnOn` zostanie wykryte, bot wywołuje `OnIntent` okno obsługi. To okno dialogowe jest, gdzie możesz wywołać usługi IoT w celu włączenia na urządzeniu, a następnie poinformuj użytkowników, że urządzenie zostało włączone. |
| HomeAutomation.TurnOff | Wyłącz światła sypialni. | Gdy celem usługi LUIS `HomeAutomation.TurnOff` zostanie wykryte, bot wywołuje `OffIntent` okno obsługi. To okno dialogowe jest, gdzie możesz wywołać usługi IoT, aby wyłączyć urządzenie i poinformuj użytkownika, że urządzenie zostało wyłączone. |

## <a name="create-a-language-understanding-bot-with-bot-service"></a>Tworzenie botów Language Understanding przy użyciu usługi Bot Service

1. W [witryny Azure portal](https://portal.azure.com), wybierz opcję **Tworzenie nowego zasobu** w górnym menu po lewej stronie.

    ![Tworzenie nowego zasobu w witrynie Azure portal](./media/luis-tutorial-cscharp-web-bot/bot-service-creation.png)

2. W polu wyszukiwania, wyszukaj **Bot aplikacji sieci Web**. 

    ![Wybierz pozycję web Apps jako typ zasobu](./media/luis-tutorial-cscharp-web-bot/bot-service-selection.png)

3. W oknie Bot aplikacji sieci Web kliknij **Utwórz**.

4. W **Bot Service**, podaj wymagane informacje i kliknij przycisk **Utwórz**. To tworzy i wdraża bot service i LUIS aplikacji na platformie Azure. Jeśli chcesz używać [zalewanie mowy](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming), przejrzyj [wymagania region](troubleshooting.md#what-luis-regions-support-bot-framework-speech-priming) przed utworzeniem bota. 
   * Ustaw **nazwy aplikacji** nazwę Twój bot. Nazwa jest używana jako domenę podrzędną, gdy Twój bot jest wdrażane w chmurze (na przykład mynotesbot.azurewebsites.net). <!-- This name is also used as the name of the LUIS app associated with your bot. Copy it to use later, to find the LUIS app associated with the bot. -->
   * Wybierz subskrypcję, [grupy zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), plan usługi App service, a [lokalizacji](https://azure.microsoft.com/regions/).
   * Aby uzyskać **szablonu Bota**, wybierz opcję:
       * **Zestaw SDK w wersji 3**
       * **C#**
       * **Rozumienia języka**
   * Wybierz **lokalizacji aplikacji LUIS**. Jest to, tworzeniem [region](luis-reference-regions.md) aplikacja zostanie utworzona w.
   * Zaznacz pole wyboru potwierdzenia prawne. Warunki prawne są wyświetlane poniżej pola wyboru.

     ![Bot Service](./media/luis-tutorial-cscharp-web-bot/bot-service-setting-callout-template.png)


5. Upewnij się, czy usługa bot service został pomyślnie wdrożony.
    * Kliknij przycisk powiadomienia (ikonę dzwonka, który znajduje się wzdłuż górnej krawędzi w witrynie Azure Portal). Powiadomienie zmieni się z **Wdrażanie rozpoczęte** do **wdrażanie zakończyło się pomyślnie**.
    * Po powiadomienie zmieni się na **wdrażanie zakończyło się pomyślnie**, kliknij przycisk **przejdź do zasobu** na powiadomienia.

> [!Note]
> Ten proces tworzenia bota aplikacji sieci web również utworzona nowa aplikacja usługi LUIS. Został przeszkolony i opublikowane dla Ciebie. 

## <a name="try-the-default-bot"></a>Spróbuj bot domyślne

Upewnij się, że bot został wdrożony, wybierając **powiadomienia** pola wyboru. Powiadomienia zmieni się z **wdrażanie jest w toku...**  do **wdrażanie zakończyło się pomyślnie**. Kliknij przycisk **przejdź do zasobu** przycisk, aby otworzyć zasobów botów.

Po wdrożeniu bot kliknij **testu w czatów internetowych** aby otworzyć okienko czatów internetowych. Wpisz "hello" w czatów internetowych.

  ![Testowanie bota w czatów internetowych](./media/luis-tutorial-cscharp-web-bot/bot-service-web-chat.png)

Bot odpowiada mówiąc "osiągnięto pozdrowienie. Można powiedzieć: hello ".  Ta odpowiedź potwierdza, że bot ma Odebrano wiadomość i przekazanego aplikacją usługi LUIS, utworzony domyślny. To ustawienie domyślne aplikacją usługi LUIS wykrył intencji pozdrowienie. W następnym kroku połączysz bot się utworzoną wcześniej zamiast domyślnego aplikacją usługi LUIS aplikację usługi LUIS.

## <a name="connect-your-luis-app-to-the-bot"></a>Połącz z aplikacją usługi LUIS do robota

Otwórz **ustawienia aplikacji** i edytować **LuisAppId** pole będzie zawierać identyfikator aplikacji z aplikacją usługi LUIS. Jeśli utworzono aplikacją usługi HomeAutomation LUIS w regionie innym niż zachodnie stany USA, należy zmienić **LuisAPIHostName** także. **LuisAPIKey** jest aktualnie skonfigurowana do tworzenia klucza. Możesz zmienić to do klucza punktu końcowego w przypadku ruchu przekracza limit przydziału w warstwie bezpłatna. 

  ![Zaktualizuj identyfikator aplikacji usługi LUIS na platformie Azure](./media/luis-tutorial-cscharp-web-bot/bot-service-app-settings.png)

> [!Note]
> Jeśli nie masz identyfikator aplikacji usługi LUIS [Home automatyzacji aplikacji](luis-get-started-create-app.md), zaloguj się do [LUIS](luis-reference-regions.md) witryny sieci Web przy użyciu tego samego konta, które umożliwia logowanie do platformy Azure. 
> 1. Kliknij pozycję **Moje aplikacje**. 
> 2. Znajdź LUIS utworzonego wcześniej, zawierający intencje i podmioty, z domeny HomeAutomation.
> 3. W **ustawienia** stronie aplikacji usługi LUIS, Znajdź i skopiuj identyfikator aplikacji. Upewnij się, że jest [uczonego](luis-interactive-test.md) i [opublikowane](luis-how-to-publish-app.md). 
> 
> [!WARNING]
> Jeśli usuniesz klucz Identyfikatora lub LUIS aplikacji, bot przestaną działać.

## <a name="modify-the-bot-code"></a>Modyfikowanie kodu bot

1. Kliknij przycisk **kompilacji** a następnie kliknij przycisk **Otwórz online Edytor kodu**.

   ![Otwórz online Edytor kodu](./media/luis-tutorial-cscharp-web-bot/bot-service-build.png)

2. Kliknij prawym przyciskiem myszy `build.cmd` i wybierz polecenie **uruchamiane z poziomu konsoli** do skompilowania aplikacji. Istnieje kilka kroków kompilacji, które usługa kończy się automatycznie dla Ciebie. Kompilacja zostanie zakończona, po jego zakończeniu "Zakończyło się pomyślnie."

3. Otwórz w edytorze kodu `/Dialogs/BasicLuisDialog.cs`. Zawiera następujący kod:

   [!code-csharp[Default BasicLuisDialog.cs](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/Default_BasicLuisDialog.cs "Default BasicLuisDialog.cs")]

## <a name="change-code-to-homeautomation-intents"></a>Zmiana kodu na intencje HomeAutomation


1. Usuń trzy atrybuty intencji i metody **pozdrowienia**, **anulować**, i **pomocy**. Te opcje nie są używane w domenie wstępnie HomeAutomation. Upewnij się zachować **Brak** atrybut intencji i metody. 

2. Dodaj zależności do górnej części pliku, przy użyciu innych zależności:

   [!code-csharp[Dependencies](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=4-5&dedent=8 "dependencies")]

3. Dodaj stałe do zarządzania ciągów w górnej części `BasicLuisDialog` klasy:

   [!code-csharp[Add Intent and Entity Constants](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=23-32&dedent=8 "Add Intent and Entity Constants")]

4. Dodaj kod dla nowych intencji `HomeAutomation.TurnOn` i `HomeAutomation.TurnOff` wewnątrz `BasicLuisDialog` klasy:

   [!code-csharp[Add Intents](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=61-71&dedent=8 "Add Intents")]

5. Dodaj kod, aby pobrać wszystkie jednostki znalezione przez usługi LUIS wewnątrz `BasicLuisDialog` klasy:

   [!code-csharp[Collect entities](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=34-53&dedent=8 "Collect entities")]

6. Zmiana **ShowLuisResult** method in Class metoda `BasicLuisDialog` klasy zaokrąglić wynik, zbieraj jednostek i wyświetli komunikat odpowiedzi w chatbot:

   [!code-csharp[Display message in chatbot](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=73-83&dedent=8 "Display message in chatbot")]

## <a name="build-the-bot"></a>Tworzenie bota
W edytorze kodu, kliknij prawym przyciskiem myszy `build.cmd` i wybierz **uruchamiane z poziomu konsoli**.

![Tworzenie botów w sieci Web](./media/luis-tutorial-cscharp-web-bot/bot-service-build-run-from-console.png)

Widok kodu jest zastępowany okno terminalu pokazujący postęp i wyniki kompilacji.

![Kompilacja zakończona sukcesem w sieci Web bot](./media/luis-tutorial-cscharp-web-bot/bot-service-build-success.png)

> [!TIP]
> Alternatywną metodą tworzenia bota jest wybierz nazwa robota na górnym pasku niebieski, a następnie wybierz pozycję **otwartej konsoli Kudu**. Otwiera konsolę **D:\home**. 
> 
> Zmień katalog na **D:\home\site\wwwroot** , wpisując: `cd site\wwwroot`
>
> Uruchom skrypt kompilacji, wpisując: `build.cmd`

## <a name="test-the-bot"></a>Testowanie robota

W witrynie Azure portal kliknij pozycję **testowania w czatów internetowych** do testowania robota. Typ wiadomości like "Włączenie świateł" i "turn off Moje heater" do wywołania intencji, które dodano do niego.

   ![Testowanie HomeAutomation bot w czatów internetowych](./media/luis-tutorial-cscharp-web-bot/bot-service-chat-results.png)

> [!TIP]
> Mogą przechowywać aplikacją usługi LUIS bez żadnych modyfikacji kodu Twój bot. Zobacz [Dodawanie wypowiedzi przykład](https://docs.microsoft.com/azure/cognitive-services/LUIS/add-example-utterances) i [nauczenia i przetestowania aplikacją usługi LUIS](https://docs.microsoft.com/azure/cognitive-services/LUIS/luis-interactive-test). 

## <a name="download-the-bot-to-debug"></a>Pobierz bot do debugowania
Jeśli Twój bot nie działa, pobrać projektu na komputerze lokalnym i kontynuować [debugowania](https://docs.microsoft.com/bot-framework/bot-service-debug-bot). 

## <a name="learn-more-about-bot-framework"></a>Dowiedz się więcej na temat struktury Bot Framework
Dowiedz się więcej o [platformy Bot Framework](https://dev.botframework.com/) i [3.x](https://github.com/Microsoft/BotBuilder) i [4.x](https://github.com/Microsoft/botbuilder-dotnet) zestawów SDK.

## <a name="next-steps"></a>Kolejne kroki

Dodawanie intencji LUIS i Bot okien obsługi **pomocy**, **anulować**, i **pozdrowienia** intencji. Pamiętaj, aby uczyć, publikowanie i [kompilacji](#build-the-bot) bot aplikacji sieci web. Bot i LUIS powinny mieć ten sam intencji.

Zobacz więcej [przykładów](https://github.com/Microsoft/AI) z botami konwersacyjnymi. 

> [!div class="nextstepaction"]
> [Dodawanie intencji](./luis-how-to-add-intents.md)
> [zalewanie mowy](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming)

<!-- tested on Win10 -->
