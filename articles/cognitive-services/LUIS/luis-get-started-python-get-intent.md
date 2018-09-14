---
title: Przewodnik Szybki start dotyczący wywoływania aplikacji Language Understanding (LUIS) przy użyciu języka Python | Microsoft Docs
description: Z tego przewodnika Szybki start dowiesz się, jak wywołać aplikację usługi LUIS przy użyciu języka Python.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 06/27/2018
ms.author: diberry
ms.openlocfilehash: bc7ae912d762a98c34b9a1b2d6a82d5630c4794b
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/24/2018
ms.locfileid: "43771755"
---
# <a name="quickstart-call-a-luis-endpoint-using-python"></a>Szybki start: wywoływanie punktu końcowego aplikacji LUIS przy użyciu języka Python
W tym przewodniku Szybki start przekażesz wypowiedzi do punktu końcowego aplikacji LUIS i uzyskasz intencje oraz jednostki.

<!-- green checkmark -->
<!--
> [!div class="checklist"]
> * Create LUIS subscription and copy key value for later use
> * View LUIS endpoint results from browser to public sample IoT app
> * Create Visual Studio C# console app to make HTTPS call to LUIS endpoint
-->

Na potrzeby tego artykułu jest wymagane bezpłatne konto usługi [LUIS](luis-reference-regions.md#luis-website), które umożliwia utworzenie aplikacji usługi LUIS.

<a name="create-luis-subscription-key"></a>
## <a name="create-luis-endpoint-key"></a>Tworzenie klucza punktu końcowego usługi LUIS
Aby wykonywać wywołania przykładowej aplikacji LUIS używanej w tym przewodniku, potrzebny jest klucz interfejsu API usług Cognitive Services. 

Aby uzyskać klucz interfejsu API, wykonaj następujące czynności: 

1. Najpierw należy utworzyć [konto interfejsu API usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) w witrynie Azure Portal. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

2. Zaloguj się do witryny Azure Portal na stronie https://portal.azure.com. 

3. Aby uzyskać klucz, postępuj zgodnie z instrukcjami w temacie [Tworzenie kluczy punktu końcowego przy użyciu platformy Azure](./luis-how-to-azure-subscription.md).

4. Wróć do witryny internetowej usługi [LUIS](luis-reference-regions.md) i zaloguj się przy użyciu konta platformy Azure. 

    [![](media/luis-get-started-node-get-intent/app-list.png "Zrzut ekranu przedstawiający listę aplikacji")](media/luis-get-started-node-get-intent/app-list.png)

## <a name="understand-what-luis-returns"></a>Interpretacja wyników zwracanych przez aplikację LUIS

Aby zrozumieć, co zwraca aplikacja LUIS, możesz wkleić adres URL przykładowej aplikacji LUIS w oknie przeglądarki. Przykładowa aplikacja to aplikacja IoT, która wykrywa, czy użytkownik chce włączyć, czy wyłączyć światła.

1. Punkt końcowy przykładowej aplikacji ma następujący format: `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=<YOUR_API_KEY>&verbose=false&q=turn%20on%20the%20bedroom%20light`. Skopiuj ten adres URL i zastąp wartość pola `subscription-key` swoim kluczem punktu końcowego.
2. Wklej adres URL w oknie przeglądarki, a następnie naciśnij klawisz Enter. W przeglądarce zostanie wyświetlony wynik w formacie JSON, który wskazuje, że usługa LUIS wykryła intencję `HomeAutomation.TurnOn` i jednostkę `HomeAutomation.Room` o wartości `bedroom`.

    ![Wynik w formacie JSON z informacją o wykryciu intencji TurnOn](./media/luis-get-started-node-get-intent/turn-on-bedroom.png)
3. Zmień wartość parametru `q=` w adresie URL na wartość `turn off the living room light`, a następnie naciśnij klawisz Enter. Wynik wskazuje teraz, że aplikacja LUIS wykryła intencję `HomeAutomation.TurnOff` i jednostkę `HomeAutomation.Room` o wartości `living room`. 

    ![Wynik w formacie JSON z informacją o wykryciu intencji TurnOff](./media/luis-get-started-node-get-intent/turn-off-living-room.png)


## <a name="consume-a-luis-result-using-the-endpoint-api-with-python"></a>Korzystanie z wyniku aplikacji LUIS przy użyciu interfejsu API punktu końcowego za pomocą języka Python

Aby uzyskać dostęp do tego samego wyniku, który został wyświetlony w oknie przeglądarki w poprzednim kroku, można użyć języka Python.

1. Skopiuj jeden z następujących fragmentów kodu do pliku o nazwie `quickstart-call-endpoint.py`:

   [!code-python[Console app code that calls a LUIS endpoint for Python 2.7](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/2.x/quickstart-call-endpoint-2-7.py)]

   [!code-python[Console app code that calls a LUIS endpoint for Python 3.6](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/3.x/quickstart-call-endpoint-3-6.py)]

2. Zastąp wartość pola `Ocp-Apim-Subscription-Key` swoim kluczem punktu końcowego usługi LUIS.

3. Zainstaluj zależności przy użyciu polecenia `pip install requests`.

4. Uruchom skrypt przy użyciu polecenia `python ./quickstart-call-endpoint.py`. Wyświetli ona ten sam wynik JSON, który został wyświetlony wcześniej w oknie przeglądarki.
<!-- 
![Console window displays JSON result from LUIS](./media/luis-get-started-python-get-intent/console-turn-on.png)
-->

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Dwa zasoby utworzone w tym samouczku to klucz punktu końcowego usługi LUIS i projekt w języku C#. Usuń klucz punktu końcowego usługi LUIS z witryny Azure Portal. Zamknij projekt programu Visual Studio i usuń katalog z systemu plików. 

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Dodawanie wypowiedzi](luis-get-started-python-add-utterance.md)