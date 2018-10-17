---
title: Tworzenie funkcji integrującej się z usługą Azure Logic Apps | Microsoft Docs
description: Utwórz funkcję integrującą się z usługą Azure Logic Apps i usługami Azure Cognitive Services, aby kategoryzować tonację w tweetach i wysyłać powiadomienia, gdy poziom nastrojów (tonacji) będzie niski.
services: functions, logic-apps, cognitive-services
keywords: workflow, cloud apps, cloud services, business processes, system integration, enterprise application integration, EAI
author: ggailey777
manager: jeconnoc
ms.assetid: 60495cc5-1638-4bf0-8174-52786d227734
ms.service: azure-functions
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: glenga
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 0b2e0ff800ab80a2c638293ce23fc1911390f2dd
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2018
ms.locfileid: "47221121"
---
# <a name="create-a-function-that-integrates-with-azure-logic-apps"></a>Tworzenie funkcji integrującej się z usługą Azure Logic Apps

Usługa Azure Functions integruje się z usługą Azure Logic Apps w Projektancie aplikacji usługi Logic Apps. Ta integracja umożliwia używanie mocy obliczeniowej usługi Functions w aranżacjach z innymi usługami platformy Azure oraz innych dostawców. 

W tym samouczku pokazano, jak za pomocą usługi Functions z usługami Logic Apps i Microsoft Cognitive Services na platformie Azure analizować tonację we wpisach w usłudze Twitter. Funkcja wyzwalana przez protokół HTTP kategoryzuje tweety jako zielone, żółte lub czerwone na podstawie wyniku tonacji. W razie wykrycia niskiego poziomu tonacji wysyłana jest wiadomość e-mail. 

![ilustracja: pierwsze dwa kroki aplikacji w Projektancie aplikacji usługi Logic Apps](media/functions-twitter-email/designer1.png)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie zasobu interfejsu API usług Cognitive Services.
> * Tworzenie funkcji kategoryzującej tonację w tweetach.
> * Tworzenie aplikacji logiki łączącej się z usługą Twitter.
> * Dodawanie wykrywania tonacji do aplikacji logiki. 
> * Łączenie aplikacji logiki z funkcją.
> * Wysyłanie wiadomości e-mail na podstawie odpowiedzi z funkcji.

## <a name="prerequisites"></a>Wymagania wstępne

+ Aktywne konto w usłudze [Twitter](https://twitter.com/). 
+ Konto usługi [Outlook.com](https://outlook.com/) (do wysyłania powiadomień).
+ Do wykonania czynności przedstawionych w tym temacie są wymagane zasoby utworzone w temacie [Tworzenie pierwszej funkcji w witrynie Azure Portal](functions-create-first-azure-function.md).  
Jeśli jeszcze tego nie zrobiono, wykonaj teraz te kroki, aby utworzyć aplikację funkcji.

## <a name="create-a-cognitive-services-resource"></a>Tworzenie zasobu usług Cognitive Services

Interfejsy API usług Cognitive Services są dostępne na platformie Azure jako pojedyncze zasoby. Użyj interfejsu API analizy tekstu do wykrywania tonacji monitorowanych tweetów.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. W lewym górnym rogu witryny Azure Portal kliknij przycisk **Utwórz zasób**.

3. Kliknij pozycję **Sztuczna inteligencja i analiza** > **Interfejs API analizy tekstu**. Następnie użyj ustawień określonych w tabeli, zaakceptuj warunki i wybierz opcję **Przypnij do pulpitu nawigacyjnego**.

    ![Tworzenie strony zasobu usług Cognitive](media/functions-twitter-email/cog_svcs_resource.png)

    | Ustawienie      |  Sugerowana wartość   | Opis                                        |
    | --- | --- | --- |
    | **Nazwa** | MyCognitiveServicesAccnt | Wybierz unikatową nazwę konta. |
    | **Lokalizacja** | Zachodnie stany USA | Użyj najbliższej lokalizacji. |
    | **Warstwa cenowa** | F0 | Rozpocznij od najniższej warstwy. Gdy wyczerpią się wywołania, przeskaluj do wyższego poziomu.|
    | **Grupa zasobów** | myResourceGroup | Użyj tej samej grupy zasobów dla wszystkich usług w tym samouczku.|

4. Kliknij przycisk **Utwórz**, aby utworzyć zasób. Po jego utworzeniu wybierz nowy zasób usług Cognitive Services przypięty do pulpitu nawigacyjnego. 

5. W kolumnie nawigacji po lewej stronie kliknij pozycję **Klucze**, a następnie skopiuj wartość pozycji **Klucz 1** i zapisz ją. Ten klucz umożliwia łączenie aplikacji logiki z interfejsem API usług Cognitive Services. 
 
    ![Klucze](media/functions-twitter-email/keys.png)

## <a name="create-the-function-app"></a>Tworzenie aplikacji funkcji

Usługa Functions zapewnia doskonały sposób na odciążanie przetwarzania zadań w przepływie pracy aplikacji logiki. W tym samouczku funkcja wyzwalana przez protokół HTTP używana jest do przetwarzania wyników tonacji tweetów z usług Cognitive Services i zwracania wartości kategorii.  

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-an-http-triggered-function"></a>Tworzenie funkcji wyzwalanej przez protokół HTTP  

1. Rozwiń aplikację funkcji i kliknij przycisk **+** obok pozycji **Funkcje**. Jeśli jest to pierwsza funkcja w aplikacji funkcji, wybierz pozycję **Funkcja niestandardowa**. Spowoduje to wyświetlenie pełnego zestawu szablonów funkcji.

    ![Strona szybkiego rozpoczynania pracy z usługą Functions w witrynie Azure Portal](media/functions-twitter-email/add-first-function.png)

2. W polu wyszukiwania wpisz `http`, a następnie wybierz język **C#** dla szablonu wyzwalacza HTTP. 

    ![Wybieranie wyzwalacza HTTP](./media/functions-twitter-email/select-http-trigger-portal.png)

    Wszystkie kolejne funkcje dodane do aplikacji funkcji używają szablonów języka C#.

3. Wpisz **nazwę** funkcji, wybierz pozycję `Function` na liście **[Poziom uwierzytelniania](functions-bindings-http-webhook.md#http-auth)**, a następnie wybierz przycisk **Utwórz**. 

    ![Tworzenie funkcji wyzwalanej przez protokół HTTP](./media/functions-twitter-email/select-http-trigger-portal-2.png)

    Spowoduje to utworzenie funkcji skryptu w języku C# przy użyciu szablonu wyzwalacza HTTP. Kod zostanie wyświetlony w nowym oknie jako `run.csx`.

4. Zastąp zawartość pliku `run.csx` poniższym kodem, a następnie kliknij przycisk **Zapisz**:

    ```csharp
    #r "Newtonsoft.Json"
    
    using System;
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        string category = "GREEN";
    
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        log.LogInformation(string.Format("The sentiment score received is '{0}'.", requestBody));
    
        double score = Convert.ToDouble(requestBody);
    
        if(score < .3)
        {
            category = "RED";
        }
        else if (score < .6) 
        {
            category = "YELLOW";
        }
    
        return requestBody != null
            ? (ActionResult)new OkObjectResult(category)
            : new BadRequestObjectResult("Please pass a value on the query string or in the request body");
    }
    ```
    Ten kod funkcji zwraca kategorię koloru na podstawie wyniku tonacji otrzymanego w żądaniu. 

4. Aby przetestować tę funkcję, kliknij przycisk **Testuj** przy prawej krawędzi, co spowoduje rozwinięcie karty testu. Wpisz wartość `0.2` w polu **Treść żądania**, a następnie kliknij przycisk **Uruchom**. W treści odpowiedzi jest zwracana wartość **RED** (Czerwony). 

    ![Testowanie funkcji w witrynie Azure Portal](./media/functions-twitter-email/test.png)

Została utworzona funkcja kategoryzująca wyniki tonacji. Następnie należy utworzyć aplikację logiki, która zintegruje funkcję z usługą Twitter i interfejsem API usług Cognitive Services. 

## <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki   

1. W witrynie Azure Portal kliknij przycisk **Nowy** znajdujący się w lewym górnym rogu tej witryny.

2. Kliknij pozycję **Integracja dla przedsiębiorstw** > **Aplikacja logiki**. Następnie użyj ustawień określonych w tabeli, wybierz opcję **Przypnij do pulpitu nawigacyjnego** i kliknij przycisk **Utwórz**.
 
4. Następnie wpisz **nazwę**, na przykład `TweetSentiment`,użyj ustawień określonych w tabeli, zaakceptuj warunki i wybierz opcję **Przypnij do pulpitu nawigacyjnego**.

    ![Tworzenie aplikacji logiki w witrynie Azure Portal](./media/functions-twitter-email/new_logic_app.png)

    | Ustawienie      |  Sugerowana wartość   | Opis                                        |
    | ----------------- | ------------ | ------------- |
    | **Nazwa** | TweetSentiment | Wybierz odpowiednią nazwę dla aplikacji. |
    | **Grupa zasobów** | myResourceGroup | Wybierz tę samą istniejącą grupę zasobów co wcześniej. |
    | **Lokalizacja** | Wschodnie stany USA | Wybierz bliską lokalizację. |    

4. Wybierz opcję **Przypnij do pulpitu nawigacyjnego** i kliknij przycisk **Utwórz**, aby utworzyć aplikację logiki. 

5. Po utworzeniu aplikacji kliknij nową aplikację logiki przypiętą do pulpitu nawigacyjnego. Następnie w Projektancie aplikacji usługi Logic Apps przewiń w dół i kliknij szablon **Pusta aplikacja logiki**. 

    ![Szablon pustej aplikacji usługi Logic Apps](media/functions-twitter-email/blank.png)

Teraz za pomocą Projektanta aplikacji usługi Logic Apps możesz dodać do aplikacji usługi i wyzwalacze.

## <a name="connect-to-twitter"></a>Łączenie z usługą Twitter

Najpierw utwórz połączenie z kontem w usłudze Twitter. Aplikacja logiki sonduje pod kątem tweetów, które wyzwalają uruchomienie aplikacji.

1. W projektancie kliknij usługę **Twitter**, a następnie kliknij wyzwalacz **Po wysłaniu nowego tweetu**. Zaloguj się na koncie w usłudze Twitter i zezwól aplikacji logiki na korzystanie z tego konta.

2. Użyj ustawień wyzwalacza usługi Twitter określonych w tabeli. 

    ![Ustawienia łącznika usługi Twitter](media/functions-twitter-email/azure_tweet.png)

    | Ustawienie      |  Sugerowana wartość   | Opis                                        |
    | ----------------- | ------------ | ------------- |
    | **Wyszukiwany tekst** | #Azure | Użyj hasztagu dostatecznie popularnego, aby wygenerować nowe tweety w wybranym interwale. Jeśli używasz warstwy bezpłatnej, a hasztag jest zbyt popularny, możesz szybko zużyć przydział transakcji w interfejsie API usług Cognitive Services. |
    | **Częstotliwość** | Minuta | Jednostka częstotliwości używana do sondowania usługi Twitter.  |
    | **Interwał** | 15 | Czas między żądaniami usługi Twitter w jednostkach częstotliwości. |

3.  Kliknij przycisk **Zapisz** w celu połączenia się z kontem usługi Twitter. 

Teraz aplikacja jest połączona z usługą Twitter. Następnie należy połączyć się z analizą tekstu w celu wykrywania tonacji zebranych tweetów.

## <a name="add-sentiment-detection"></a>Dodawanie wykrywania tonacji

1. Kliknij pozycję **Nowy krok**, a następnie pozycję **Dodaj akcję**.

    ![Nowy krok, a następnie pozycja Dodaj akcję](media/functions-twitter-email/new_step.png)

2. W obszarze **Wybierz akcję** kliknij pozycję **Analiza tekstu**, a następnie akcję **Wykryj tonację**.

    ![Wykrywanie tonacji](media/functions-twitter-email/detect_sent.png)

3. Wpisz nazwę połączenia, na przykład `MyCognitiveServicesConnection`, wklej klucz dla zapisanego interfejsu API usług Cognitive Services i kliknij przycisk **Utwórz**.  

4. Kliknij pozycję **Tekst do przeanalizowania** > **Tekst tweetu**, a następnie kliknij przycisk **Zapisz**.  

    ![Wykrywanie tonacji](media/functions-twitter-email/ds_tta.png)

Skonfigurowano już wykrywanie tonacji, więc można dodać połączenie z funkcją wykorzystującą dane wyjściowe wyniku tonacji.

## <a name="connect-sentiment-output-to-your-function"></a>Łączenie danych wyjściowych tonacji z funkcją

1. W Projektancie aplikacji usługi Logic Apps kliknij pozycję **Nowy krok** > **Dodaj akcję**, a następnie pozycję **Azure Functions**. 

2. Kliknij pozycję **Wybierz funkcję platformy Azure** i wybierz utworzoną wcześniej funkcję **CategorizeSentiment**.  

    ![Okno Azure Functions z wyświetloną pozycją Wybierz funkcję platformy Azure](media/functions-twitter-email/choose_fun.png)

3. W polu **Treść żądania** kliknij pozycję **Score** (Wynik), a następnie pozycję **Zapisz**.

    ![Wynik](media/functions-twitter-email/trigger_score.png)

Teraz funkcja jest wyzwalana po wysłaniu wyniku tonacji z aplikacji logiki. Funkcja zwraca aplikacji logiki kategorię kodowaną kolorem. Następnie zostanie dodane powiadomienie e-mail wysyłane w razie zwrócenia przez funkcję wartości tonacji **RED** (Czerwony). 

## <a name="add-email-notifications"></a>Dodawanie powiadomień w wiadomościach e-mail

Ostatnia część przepływu pracy polega na wyzwoleniu wiadomości e-mail, gdy wynik tonacji skategoryzowano jako _RED_ (Czerwony). W tym temacie używany jest łącznik usługi Outlook.com. Wykonując podobne kroki, można użyć łącznika usługi Gmail lub programu Outlook w usłudze Office 365.   

1. W Projektancie aplikacji usługi Logic Apps kliknij pozycję **Nowy krok** > **Dodaj połączenie**. 

2. Kliknij pozycję **Wybierz wartość**, a następnie pozycję **Treść**. Wybierz pozycję **jest równe**, kliknij pozycję **Wybierz wartość** i wpisz `RED`, a następnie kliknij przycisk **Zapisz**. 

    ![Dodaj warunek do aplikacji logiki.](media/functions-twitter-email/condition.png)

3. W obszarze **Jeśli prawda** kliknij pozycję **Dodaj akcję**, wyszukaj `outlook.com`, kliknij pozycję **Wyślij wiadomość e-mail** i zaloguj się na koncie w usłudze Outlook.com.
    
    ![Wybierz akcję dla warunku.](media/functions-twitter-email/outlook.png)

    > [!NOTE]
    > Jeśli nie masz konta usługi Outlook.com, możesz wybrać inny łącznik, taki jak usługi Gmail lub programu Outlook w usłudze Office 365.

4. W akcji **Wyślij wiadomość e-mail** użyj ustawień poczty e-mail określonych w tabeli. 

    ![Skonfiguruj wiadomość e-mail do wysłania oraz akcję jej wysyłania.](media/functions-twitter-email/send_email.png)

    | Ustawienie      |  Sugerowana wartość   | Opis  |
    | ----------------- | ------------ | ------------- |
    | **Do** | Wpisz adres e-mail | Adres e-mail, na który będą wysyłane powiadomienia. |
    | **Temat** | Wykryto negatywną tonację tweetów  | Wiersz tematu powiadomienia w wiadomości e-mail.  |
    | **Treść** | Tekst tweetu, lokalizacja | Kliknij parametry **Tekst tweetu** i **Lokalizacja**. |

5.  Kliknij pozycję **Zapisz**.

Przepływ pracy jest gotowy, można więc włączyć aplikację logiki i przyjrzeć się funkcji w działaniu.

## <a name="test-the-workflow"></a>Testowanie przepływu pracy

1. W Projektancie aplikacji usługi Logic Apps kliknij pozycję **Uruchom** w celu uruchomienia aplikacji.

2. W lewej kolumnie kliknij pozycję **Przegląd**, aby zobaczyć stan aplikacji logiki. 
 
    ![Stan wykonania aplikacji logiki](media/functions-twitter-email/over1.png)

3. Opcjonalnie: kliknij jeden z przebiegów, aby wyświetlić szczegóły wykonania.

4. Przejdź do funkcji, przejrzyj dzienniki i potwierdź, że wartości tonacji zostały odebrane i przetworzone.
 
    ![Wyświetlanie dzienników funkcji](media/functions-twitter-email/sent.png)

5. Po wykryciu potencjalnie negatywnej tonacji otrzymasz wiadomość e-mail. Jeśli wiadomość e-mail nie nadeszła, możesz tak zmienić kod funkcji, aby wartość RED (Czerwony) była zwracana zawsze:

        return req.CreateResponse(HttpStatusCode.OK, "RED");

    Po zweryfikowaniu powiadomień e-mail przywróć pierwotną postać kodu:

        return req.CreateResponse(HttpStatusCode.OK, category);

    > [!IMPORTANT]
    > Po ukończeniu tego samouczka najlepiej jest wyłączyć aplikację logiki. Wyłączenie aplikacji pozwala uniknąć naliczania opłat za wykonania i zużywania transakcji w interfejsie API usług Cognitive Services.

Teraz wiesz już, jak łatwe jest integrowanie usługi Functions w przepływie pracy usługi Logic Apps.

## <a name="disable-the-logic-app"></a>Wyłączanie aplikacji logiki

Aby wyłączyć aplikację logiki, kliknij pozycję **Przegląd**, a następnie pozycję **Wyłącz** w górnej części ekranu. Spowoduje to zatrzymanie uruchamiania aplikacji logiki i naliczania opłat bez usuwania aplikacji. 

![Dzienniki funkcji](media/functions-twitter-email/disable-logic-app.png)

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie zasobu interfejsu API usług Cognitive Services.
> * Tworzenie funkcji kategoryzującej tonację w tweetach.
> * Tworzenie aplikacji logiki łączącej się z usługą Twitter.
> * Dodawanie wykrywania tonacji do aplikacji logiki. 
> * Łączenie aplikacji logiki z funkcją.
> * Wysyłanie wiadomości e-mail na podstawie odpowiedzi z funkcji.

Przejdź do następnego samouczka, aby dowiedzieć się, jak utworzyć dla funkcji bezserwerowy interfejs API.

> [!div class="nextstepaction"] 
> [Tworzenie bezserwerowego interfejsu API za pomocą usługi Azure Functions](functions-create-serverless-api.md)

Aby dowiedzieć się więcej na temat usługi Logic Apps, zobacz [Azure Logic Apps](../logic-apps/logic-apps-overview.md).

