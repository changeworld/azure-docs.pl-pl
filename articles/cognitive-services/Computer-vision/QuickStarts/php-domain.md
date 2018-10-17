---
title: 'Szybki start: korzystanie z modelu domeny — REST, PHP — przetwarzanie obrazów'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start użyjesz modeli domeny do rozpoznania elementów krajobrazu na obrazie przy użyciu interfejsu API przetwarzania obrazów oraz języka PHP.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 40a4fa90442a5771f6b221a25ddca46d3ebc4f5c
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2018
ms.locfileid: "45629495"
---
# <a name="quickstart-use-a-domain-model-using-the-rest-api-and-php-in-computer-vision"></a>Szybki start: korzystanie z modelu domeny przy użyciu interfejsu API REST i języka PHP w przetwarzaniu obrazów

W tym przewodniku Szybki start użyjesz modelu domeny do rozpoznania elementów krajobrazu lub, opcjonalnie, celebrytów na zdalnie przechowywanym obrazie za pomocą interfejsu API REST przetwarzania obrazów. Metoda [Recognize Domain Specific Content](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) umożliwia zastosowanie modelu specyficznego dla domeny do rozpoznawania zawartości w ramach obrazu.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services).

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć zainstalowany język [PHP](https://secure.php.net/downloads.php).
- Musisz mieć zainstalowane repozytorium [Pear](https://pear.php.net).
- Musisz mieć klucz subskrypcji funkcji przetwarzania obrazów. Aby uzyskać klucz subskrypcji, zobacz [Obtaining Subscription Keys (Uzyskiwanie kluczy subskrypcji)](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="create-and-run-the-sample"></a>Tworzenie i uruchamianie próbki

Aby utworzyć i uruchomić przykład, wykonaj następujące kroki:

1. Zainstaluj pakiet [`HTTP_Request2`](http://pear.php.net/package/HTTP_Request2) PHP5.
   1. Otwórz okno wiersza polecenia jako administrator.
   1. Uruchom następujące polecenie:

      ```console
      pear install HTTP_Request2
      ```

   1. Po pomyślnym zainstalowaniu pakietu zamknij okno wiersza polecenia.

1. Skopiuj następujący kod do edytora tekstów.
1. W razie potrzeby wprowadź w kodzie następujące zmiany:
    1. Zastąp wartość `subscriptionKey` kluczem subskrypcji.
    1. W razie potrzeby zastąp wartość `uriBase` adresem URL punktu końcowego dla metody [Recognize Domain Specific Content](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) z regionu platformy Azure, z którego uzyskano klucze subskrypcji.
    1. Opcjonalnie zastąp wartość `imageUrl` adresem URL innego obrazu, który chcesz analizować.
    1. Opcjonalnie zastąp wartość parametru żądania `domain` wartością `celebrites`, jeśli chcesz używać modelu domeny `celebrities` zamiast modelu domeny `landmarks`.
1. Zapisz kod jako plik z rozszerzeniem `.php`. Na przykład `use-domain-model.php`.
1. Otwórz okno przeglądarki, która obsługuje język PHP.
1. Przeciągnij i upuść plik w oknie przeglądarki.

```php
<html>
<head>
    <title>Analyze Domain Model Sample</title>
</head>
<body>
<?php
// Replace <Subscription Key> with a valid subscription key.
$ocpApimSubscriptionKey = '<Subscription Key>';

// You must use the same location in your REST call as you used to obtain
// your subscription keys. For example, if you obtained your subscription keys
// from westus, replace "westcentralus" in the URL below with "westus".
$uriBase = 'https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/';

// Change 'landmarks' to 'celebrities' to use the Celebrities model.
$domain = 'landmarks';

$imageUrl =
    'https://upload.wikimedia.org/wikipedia/commons/2/23/Space_Needle_2011-07-04.jpg';

require_once 'HTTP/Request2.php';

$request = new Http_Request2($uriBase . 'models/' . $domain . '/analyze');
$url = $request->getUrl();

$headers = array(
    // Request headers
    'Content-Type' => 'application/json',
    'Ocp-Apim-Subscription-Key' => $ocpApimSubscriptionKey
);
$request->setHeader($headers);

$parameters = array(
    // Request parameters
    'model' => $domain
);
$url->setQueryVariables($parameters);

$request->setMethod(HTTP_Request2::METHOD_POST);

// Request body parameters
$body = json_encode(array('url' => $imageUrl));

// Request body
$request->setBody($body);

try
{
    $response = $request->send();
    echo "<pre>" .
        json_encode(json_decode($response->getBody()), JSON_PRETTY_PRINT) . "</pre>";
}
catch (HttpException $ex)
{
    echo "<pre>" . $ex . "</pre>";
}
?>
</body>
</html>
```

## <a name="examine-the-response"></a>Sprawdzanie odpowiedzi

Po pomyślnym przetworzeniu żądania zostanie zwrócona odpowiedź w formacie JSON. Przykładowa witryna internetowa analizuje i wyświetla pomyślną odpowiedź w oknie przeglądarki, podobnie jak w poniższym przykładzie:

```json
{
    "result": {
        "landmarks": [
            {
                "name": "Space Needle",
                "confidence": 0.9998177886009216
            }
        ]
    },
    "requestId": "4d26587b-b2b9-408d-a70c-1f8121d84b0d",
    "metadata": {
        "height": 4132,
        "width": 2096,
        "format": "Jpeg"
    }
}
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli plik nie jest już potrzebny, usuń go, a następnie odinstaluj pakiet `HTTP_Request2` PHP5. Aby odinstalować pakiet, wykonaj następujące czynności:

1. Otwórz okno wiersza polecenia jako administrator.
2. Uruchom następujące polecenie:

   ```console
   pear uninstall HTTP_Request2
   ```

3. Po pomyślnym odinstalowaniu pakietu zamknij okno wiersza polecenia.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z interfejsami API przetwarzania obrazów używanymi do analizy obrazu, wykrywania celebrytów i charakterystycznych elementów krajobrazu, tworzenia miniatur oraz wyodrębniania tekstu drukowanego i odręcznego. Aby szybko zacząć eksperymentować z interfejsem API przetwarzania obrazów, wypróbuj [konsolę testowania interfejsu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Zobacz, jak działa interfejs API przetwarzania obrazów](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
