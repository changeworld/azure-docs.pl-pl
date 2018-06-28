---
title: Samouczek dotyczący wywoływania aplikacji Language Understanding (LUIS) przy użyciu języka Node.js| Microsoft Docs
description: Z tego samouczka dowiesz się, jak wywołać aplikację LUIS przy użyciu języka Node.js.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: 62c6816e753ee2c29aee0b8f68dec0ebd9f4be44
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265408"
---
# <a name="tutorial-call-a-luis-endpoint-using-javascript"></a>Samouczek: Wywoływanie punktu końcowego aplikacji LUIS przy użyciu języka JavaScript
Przekazuj wypowiedzi do punktu końcowego aplikacji LUIS i uzyskuj intencje oraz jednostki.

<!-- green checkmark -->
> [!div class="checklist"]
> * Tworzenie subskrypcji usługi LUIS i kopiowanie wartości klucza do późniejszego użytku
> * Wyświetlanie wyników punktu końcowego aplikacji LUIS w przeglądarce dla publicznej przykładowej aplikacji IoT
> * Tworzenie aplikacji konsoli programu Visual Studio w języku C# w celu wykonywania wywołań HTTPS do punktu końcowego usługi LUIS

Na potrzeby tego artykułu wymagane jest bezpłatne konto usługi [LUIS][LUIS] w celu tworzenia aplikacji LUIS.

## <a name="create-luis-subscription-key"></a>Tworzenie klucza subskrypcji usługi LUIS
Aby wykonywać wywołania przykładowej aplikacji LUIS używanej w tym przewodniku, potrzebny jest klucz interfejsu API usług Cognitive Services. 

Aby uzyskać klucz interfejsu API, wykonaj następujące czynności: 

1. Najpierw należy utworzyć [konto interfejsu API usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) w witrynie Azure Portal. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

2. Zaloguj się do witryny Azure Portal na stronie https://portal.azure.com. 

3. Aby uzyskać klucz, postępuj zgodnie z instrukcjami w temacie [Tworzenie kluczy subskrypcji przy użyciu platformy Azure](./luis-how-to-azure-subscription.md).

4. Wróć do witryny internetowej usługi [LUIS](luis-reference-regions.md) i zaloguj się przy użyciu konta platformy Azure. 

    [![](media/luis-get-started-node-get-intent/app-list.png "Zrzut ekranu przedstawiający listę tworzenia aplikacji")](media/luis-get-started-node-get-intent/app-list.png)

## <a name="understand-what-luis-returns"></a>Interpretacja wyników zwracanych przez aplikację LUIS

Aby zrozumieć, co zwraca aplikacja LUIS, możesz wkleić adres URL przykładowej aplikacji LUIS w oknie przeglądarki. Przykładowa aplikacja to aplikacja IoT, która wykrywa, czy użytkownik chce włączyć, czy wyłączyć światła.

1. Punkt końcowy przykładowej aplikacji ma następujący format: `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=<YOUR_API_KEY>&verbose=false&q=turn%20on%20the%20bedroom%20light`. Skopiuj ten adres URL i zastąp wartość pola `subscription-key` swoim kluczem subskrypcji.

2. Wklej adres URL w oknie przeglądarki, a następnie naciśnij klawisz Enter. W przeglądarce zostanie wyświetlony wynik w formacie JSON, który wskazuje, że usługa LUIS wykryła intencję `HomeAutomation.TurnOn` i jednostkę `HomeAutomation.Room` o wartości `bedroom`.

    ![Wynik w formacie JSON z informacją o wykryciu intencji TurnOn](./media/luis-get-started-node-get-intent/turn-on-bedroom.png)

3. Zmień wartość parametru `q=` w adresie URL na wartość `turn off the living room light`, a następnie naciśnij klawisz Enter. Wynik wskazuje teraz, że aplikacja LUIS wykryła intencję `HomeAutomation.TurnOff` i jednostkę `HomeAutomation.Room` o wartości `living room`. 

    ![Wynik w formacie JSON z informacją o wykryciu intencji TurnOff](./media/luis-get-started-node-get-intent/turn-off-living-room.png)


## <a name="consume-a-luis-result-using-the-endpoint-api-with-javascript"></a>Korzystanie z wyniku aplikacji LUIS przy użyciu interfejsu API punktu końcowego za pomocą języka JavaScript 

Aby uzyskać dostęp do tego samego wyniku, który został wyświetlony w oknie przeglądarki w poprzednim kroku, możesz użyć języka JavaScript. 
1. Skopiuj poniższy kod i zapisz go w pliku HTML:

   [!code-javascript[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/endpoint-api-samples/javascript/call-endpoint.html)]
2. Zastąp ciąg `"YOUR SUBSCRIPTION KEY"` swoim kluczem subskrypcji w tym wierszu kodu: `xhrObj.setRequestHeader("Ocp-Apim-Subscription-Key","YOUR SUBSCRIPTION KEY");`

3. Otwórz plik, który został zapisany za pomocą przeglądarki internetowej.  Powinno zostać wyświetlone okno alertu z następującą informacją: `Detected the following intent: TurnOn`.

![Wyskakujące okno z informacją TurnOn](./media/luis-get-started-node-get-intent/popup-turn-on.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Dwa zasoby utworzone w tym samouczku to klucz subskrypcji usługi LUIS i projekt w języku C#. Usuń klucz subskrypcji usługi LUIS z witryny Azure Portal. Zamknij projekt programu Visual Studio i usuń katalog z systemu plików. 

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Dodawanie wypowiedzi](luis-get-started-javascript-add-utterance.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website