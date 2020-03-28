---
title: 'Szybki start: zarządzanie bazą wiedzy za pomocą cURL & REST'
description: Ten przewodnik Szybki start pokazuje, jak tworzyć, publikować i wysyłać zapytania do bazy wiedzy przy użyciu interfejsów API REST.
ms.date: 02/27/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: quickstart
ms.openlocfilehash: 00ec52fe20fb0e6a976f3e7142386e835713c98c
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "78851211"
---
# <a name="quickstart-use-curl-and-rest-to-manage-knowledge-base"></a>Szybki start: zarządzanie bazą wiedzy za pomocą cURL i REST

Ten przewodnik Szybki start przeprowadzi Cię przez tworzenie, publikowanie i wykonywanie zapytań z bazy wiedzy. Usługa QnA Maker automatycznie wyodrębnia pytania i odpowiedzi z częściowo ustrukturyzowanej zawartości, na przykład często zadawanych pytań, ze [źródeł danych](../Concepts/knowledge-base.md). Model bazy wiedzy jest zdefiniowany w formacie JSON wysyłanym w treści żądania interfejsu API.

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Bieżąca wersja [cURL](https://curl.haxx.se/). W przewodnikach Szybki start używanych jest kilka przełączników wiersza polecenia, które są odnotowane w [dokumentacji cURL](https://curl.haxx.se/docs/manpage.html).
* Musisz mieć [zasób QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Aby pobrać nazwę klucza i zasobu, wybierz **szybki start** dla zasobu w witrynie Azure portal. Nazwa zasobu jest pierwszą częścią adresu URL punktu końcowego:

    `https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0`

> [!CAUTION]
> W poniższych przykładach `\` bash użyto znaku kontynuacji wiersza. Jeśli konsola lub terminal używa innego znaku kontynuacji wiersza, użyj tego znaku.

## <a name="create-a-knowledge-base"></a>Tworzenie bazy wiedzy

Aby utworzyć bazę wiedzy z interfejsami API REST i cURL, należy mieć następujące informacje:

|Informacje|Konfiguracja cURL|Przeznaczenie|
|--|--|--|
|Nazwa zasobu programu QnA Maker|Adres URL|używane do konstruowania adresu URL|
|Klucz zasobu programu QnA Maker|`-h`param `Ocp-Apim-Subscription-Key` dla nagłówka|Uwierzytelnij się w usłudze QnA Maker|
|JSON opisujący bazę wiedzy|`-d`Param|[Przykłady](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create#examples) JSON|
|Rozmiar JSON w bajtach|`-h`param `Content-Size` dla nagłówka||

Polecenie cURL jest wykonywane z powłoki BASH. Edytuj to polecenie przy obliczu własnej nazwy zasobu, klucza zasobów oraz wartości JSON i rozmiaru JSON.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/create \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:107" \
-d '{ name: "QnA Maker FAQ",urls: [ "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs"]}'
```

Odpowiedź cURL od QnA `operationId` Maker zawiera , który jest wymagany, aby [uzyskać status operacji.](#get-status-of-operation)

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2020-02-27T04:11:22Z",
  "lastActionTimestamp": "2020-02-27T04:11:22Z",
  "userId": "9596077b3e0441eb93d5080d6a15c64b",
  "operationId": "95a4f700-9899-4c98-bda8-5449af9faef8"
}
```

## <a name="get-status-of-operation"></a>Uzyskaj stan operacji

Podczas tworzenia bazy wiedzy, ponieważ operacja jest asynchronizna, odpowiedź zawiera informacje w celu określenia stanu.

|Informacje|Konfiguracja cURL|Przeznaczenie|
|--|--|--|
|Nazwa zasobu programu QnA Maker|Adres URL|używane do konstruowania adresu URL|
|Identyfikator operacji|Trasa adresu URL|`/operations/REPLACE-WITH-YOUR-OPERATION-ID`|
|Klucz zasobu programu QnA Maker|`-h`param `Ocp-Apim-Subscription-Key` dla nagłówka|Uwierzytelnij się w usłudze QnA Maker|

Polecenie cURL jest wykonywane z powłoki BASH. Edytuj to polecenie przy obliczu własnej nazwy zasobu, klucza zasobów i identyfikatora operacji.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/operations/REPLACE-WITH-YOUR-OPERATION-ID \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

Odpowiedź cURL zawiera stan. Jeśli stan operacji zakończy się `resourceLocation` pomyślnie, zawiera identyfikator bazy wiedzy.

```json
{
   "operationState": "Succeeded",
   "createdTimestamp": "2020-02-27T04:54:07Z",
   "lastActionTimestamp": "2020-02-27T04:54:19Z",
   "resourceLocation": "/knowledgebases/fe3971b7-cfaa-41fa-8d9f-6ceb673eb865",
   "userId": "f596077b3e0441eb93d5080d6a15c64b",
   "operationId": "f293f218-d080-48f0-a766-47993e9b26a8"
}
```

## <a name="publish-knowledge-base"></a>Publikowanie bazy wiedzy

Przed zapytaniem bazy wiedzy należy:
* Publikowanie bazy wiedzy
* Pobierz klucz punktu końcowego środowiska wykonawczego

To zadanie publikuje bazę wiedzy. Uzyskanie klucza punktu końcowego środowiska wykonawczego jest [osobnym zadaniem](#get-published-knowledge-bases-runtime-endpoint-key).

|Informacje|Konfiguracja cURL|Przeznaczenie|
|--|--|--|
|Nazwa zasobu programu QnA Maker|Adres URL|używane do konstruowania adresu URL|
|Klucz zasobu programu QnA Maker|`-h`param `Ocp-Apim-Subscription-Key` dla nagłówka|Uwierzytelnij się w usłudze QnA Maker|
|Identyfikator bazy wiedzy|Trasa adresu URL|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

Polecenie cURL jest wykonywane z powłoki BASH. Edytuj to polecenie przy obliczu własnej nazwy zasobu, klucza zasobów i identyfikatora bazy wiedzy.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-v \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
--data-raw ''
```

Stan odpowiedzi to 204 bez wyników. Użyj `-v` parametru wiersza polecenia, aby wyświetlić pełne dane wyjściowe dla polecenia cURL. Będzie to obejmować stan HTTP.

## <a name="get-published-knowledge-bases-runtime-endpoint-key"></a>Pobierz klucz punktu końcowego środowiska wykonawczego opublikowanej bazy wiedzy

Przed zapytaniem bazy wiedzy należy:
* Publikowanie bazy wiedzy
* Pobierz klucz punktu końcowego środowiska wykonawczego

To zadanie otrzymuje klucz punktu końcowego środowiska wykonawczego. Publikowanie bazy wiedzy jest [osobnym zadaniem](#publish-knowledge-base).

Klucz punktu końcowego środowiska uruchomieniowego jest tym samym kluczem dla wszystkich baz wiedzy przy użyciu zasobu QnA Maker.

|Informacje|Konfiguracja cURL|Przeznaczenie|
|--|--|--|
|Nazwa zasobu programu QnA Maker|Adres URL|używane do konstruowania adresu URL|
|Klucz zasobu programu QnA Maker|`-h`param `Ocp-Apim-Subscription-Key` dla nagłówka|Uwierzytelnij się w usłudze QnA Maker|

Polecenie cURL jest wykonywane z powłoki BASH. Edytuj to polecenie przy sterami własnej nazwy zasobu, klucza zasobów.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/endpointkeys \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```


Odpowiedź cURL zawiera klucze punktu końcowego środowiska wykonawczego. Użyj tylko jednego z kluczy podczas wykonywania zapytań, aby uzyskać odpowiedź z bazy wiedzy.

```json
{
  "primaryEndpointKey": "93e88a14-694a-44d5-883b-184a68aa8530",
  "secondaryEndpointKey": "92c98c16-ca31-4294-8626-6c57454a5063",
  "installedVersion": "4.0.5",
  "lastStableVersion": "4.0.6"
}
```

## <a name="query-for-answer-from-published-knowledge-base"></a>Zapytanie o odpowiedź z opublikowanej bazy wiedzy

Uzyskiwanie odpowiedzi z wiedzy odbywa się z oddzielnego środowiska uruchomieniowego niż zarządzanie bazą wiedzy. Ponieważ jest to oddzielne środowisko uruchomieniowe, należy uwierzytelnić za pomocą klucza środowiska uruchomieniowego.

|Informacje|Konfiguracja cURL|Przeznaczenie|
|--|--|--|
|Nazwa zasobu programu QnA Maker|Adres URL|używane do konstruowania adresu URL|
|Klucz środowiska uruchomieniowego programu QnA Maker|`-h`param `Authorization` dla nagłówka|Klucz jest częścią ciągu, który `Endpointkey `zawiera słowo . Uwierzytelnij się w usłudze QnA Maker|
|Identyfikator bazy wiedzy|Trasa adresu URL|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|
|JSON opisujące kwerendę|`-d`Param|[Żądania parametrów treści](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#request-body) i [przykładów](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#examples) JSON|
|Rozmiar JSON w bajtach|`-h`param `Content-Size` dla nagłówka||

Polecenie cURL jest wykonywane z powłoki BASH. Edytuj to polecenie przy obliczu własnej nazwy zasobu, klucza zasobów i identyfikatora bazy wiedzy.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.azurewebsites.net/qnamaker/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID/generateAnswer \
-X POST \
-H "Authorization: EndpointKey REPLACE-WITH-YOUR-RUNTIME-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:159" \
-d '{"question": "How are QnA Maker and LUIS used together?","top": 6,"isTest": true,  "scoreThreshold": 20, "strictFilters": [], "userId": "sd53lsY="}'
```

Pomyślna odpowiedź zawiera najwyższą odpowiedź wraz z innymi informacjami, które aplikacja kliencka, taka jak czat bot, musi wyświetlić odpowiedź dla użytkownika.

## <a name="delete-knowledge-base"></a>Usuwanie bazy wiedzy

Po zakończeniu pracy z bazą wiedzy usuń ją.

|Informacje|Konfiguracja cURL|Przeznaczenie|
|--|--|--|
|Nazwa zasobu programu QnA Maker|Adres URL|używane do konstruowania adresu URL|
|Klucz zasobu programu QnA Maker|`-h`param `Ocp-Apim-Subscription-Key` dla nagłówka|Uwierzytelnij się w usłudze QnA Maker|
|Identyfikator bazy wiedzy|Trasa adresu URL|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

Polecenie cURL jest wykonywane z powłoki BASH. Edytuj to polecenie przy obliczu własnej nazwy zasobu, klucza zasobów i identyfikatora bazy wiedzy.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-X DELETE \
-v \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

Stan odpowiedzi to 204 bez wyników. Użyj `-v` parametru wiersza polecenia, aby wyświetlić pełne dane wyjściowe dla polecenia cURL. Będzie to obejmować stan HTTP.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Tworzenie autorstwa](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase) Dokumentacja referencyjna
* [Środowisko wykonawcze](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/) Dokumentacja referencyjna
* [Przykładowe skrypty BASH przy użyciu cURL](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/curl/QnAMaker)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API Reference (Dokumentacja interfejsu API REST usługi QnA Maker w wersji 4)](https://go.microsoft.com/fwlink/?linkid=2092179)
