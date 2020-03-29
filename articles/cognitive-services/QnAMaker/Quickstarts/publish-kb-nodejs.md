---
title: 'Szybki start: QnA Maker z interfejsami API REST dla node.js'
description: Ten przewodnik Szybki start pokazuje, jak rozpocząć pracę z interfejsami API REST programu QnA Maker dla pliku Node.js. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań.  Usługa QnA Maker umożliwia zasilanie usługi pytań i odpowiedzi za pomocą częściowo ustrukturyzowanej zawartości, takiej jak dokumenty z często zadawanymi pytaniami, adresy URL i podręczniki produktów.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: ecc3fb144fb4b4e27182567925199f841b1c4357
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851675"
---
# <a name="quickstart-qna-maker-rest-apis-for-nodejs"></a>Szybki start: interfejsy API REST programu QnA Maker dla pliku Node.js

Wprowadzenie do interfejsów API rest dla aplikacji QnA Maker dla pliku Node.js. Wykonaj następujące kroki, aby wypróbować przykładowy kod dla podstawowych zadań.  Usługa QnA Maker umożliwia zasilanie usługi pytań i odpowiedzi za pomocą częściowo ustrukturyzowanej zawartości, takiej jak dokumenty z często zadawanymi pytaniami, adresy URL i podręczniki produktów.

Użyj interfejsów API REST programu QnA Maker dla pliku Node.js, aby:

* Tworzenie bazy wiedzy
* Zastępowanie bazy wiedzy
* Publikowanie bazy wiedzy
* Usuwanie bazy wiedzy
* Pobieranie bazy wiedzy
* Uzyskaj stan operacji

[Dokumentacja referencyjna](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase) | [Node.js Samples](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/tree/master/documentation-samples/quickstarts/rest-api)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję platformy Azure](https://azure.microsoft.com/free/)
* Bieżąca wersja [pliku Node.js](https://nodejs.org).
* Musisz mieć [usługę QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Aby pobrać klucz i punkt końcowy (który zawiera nazwę zasobu), wybierz **szybki start** dla zasobu w witrynie Azure portal.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-qna-maker-azure-resource"></a>Tworzenie zasobu platformy Azure programu QnA Maker

Usługi Azure Cognitive Services są reprezentowane przez zasoby platformy Azure, które subskrybujesz. Utwórz zasób dla programu QnA Maker przy użyciu [witryny Azure portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) lub [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na komputerze lokalnym.

Po otrzymaniu klucza z zasobu [utwórz zmienne środowiskowe](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla zasobu o nazwie `QNAMAKER_RESOURCE_KEY` i `QNAMAKER_AUTHORING_ENDPOINT`. Użyj wartości klucza i punktu końcowego znajdującego się na stronie **Szybki start** zasobu w witrynie Azure portal.

### <a name="create-a-new-nodejs-application"></a>Tworzenie nowej aplikacji Node.js

W oknie konsoli (takim jak cmd, PowerShell lub Bash) utwórz nowy katalog aplikacji i przejdź do niego.

```console
mkdir myapp && cd myapp
```

Uruchom `npm init -y` polecenie, aby `package.json` utworzyć plik węzła.

```console
npm init -y
```

Dodaj `reqeuestretry` pakiety i `request` NPM:

```console
npm install requestretry request --save
```

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą interfejsów API REST programu QnA Maker dla pliku Node.js:

* [Tworzenie bazy wiedzy](#create-a-knowledge-base)
* [Zastępowanie bazy wiedzy](#replace-a-knowledge-base)
* [Publikowanie bazy wiedzy](#publish-a-knowledge-base)
* [Usuwanie bazy wiedzy](#delete-a-knowledge-base)
* [Pobieranie bazy wiedzy](#download-the-knowledge-base)
* [Uzyskaj stan operacji](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>Dodawanie zależności

Utwórz plik `rest-apis.js` o nazwie i dodaj następujące _wymaga_ instrukcji do żądania HTTP.

```javascript
const request = require("requestretry");
```

## <a name="add-azure-resource-information"></a>Dodawanie informacji o zasobach platformy Azure

Tworzenie zmiennych dla punktu końcowego i klucza platformy Azure zasobu. Jeśli po uruchomieniu aplikacji utworzono zmienną środowiskową, należy zamknąć i ponownie otworzyć edytor, IDE lub powłokę uruchamianą ją, aby uzyskać dostęp do zmiennej.

Ustaw następujące wartości środowiska:

* `QNAMAKER_RESOURCE_KEY`- **Klucz** jest ciągiem znaków 32 i jest dostępny w witrynie Azure portal, na zasobie QnA Maker, na stronie **Szybki start.** To nie jest taka sama jak klucz punktu końcowego przewidywania.
* `QNAMAKER_AUTHORING_ENDPOINT`- Twój punkt końcowy tworzenia, `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`w formacie , zawiera **nazwę zasobu**. Nie jest to ten sam adres URL, który jest używany do wykonywania zapytań o punkt końcowy przewidywania.

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=authorization)]

## <a name="create-a-knowledge-base"></a>Tworzenie bazy wiedzy

Baza wiedzy przechowuje pary pytań i odpowiedzi, utworzone na podstawie obiektu JSON:

* **Treść redakcyjna**.
* **Pliki** — pliki lokalne, które nie wymagają żadnych uprawnień.
* **Adresy URL** — publicznie dostępne adresy URL.

Użyj [interfejsu API REST, aby utworzyć bazę wiedzy](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create).

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=createKb)]

## <a name="replace-a-knowledge-base"></a>Zastępowanie bazy wiedzy

Użyj [interfejsu API REST, aby zastąpić bazę wiedzy](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace).

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=replaceKb)]

## <a name="publish-a-knowledge-base"></a>Publikowanie bazy wiedzy

Opublikuj bazę wiedzy. Ten proces udostępnia bazę wiedzy z punktu końcowego przewidywania zapytań HTTP.

Użyj [interfejsu API REST, aby opublikować bazę wiedzy](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish).


[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=publish)]

## <a name="download-the-knowledge-base"></a>Pobierz bazę wiedzy

Użyj [interfejsu API REST, aby pobrać bazę wiedzy](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/download).

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=download)]

## <a name="delete-a-knowledge-base"></a>Usuwanie bazy wiedzy

Po zakończeniu korzystania z bazy wiedzy usuń ją.

Użyj [interfejsu API REST, aby usunąć bazę wiedzy](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/delete).

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=deleteKb)]

## <a name="get-status-of-an-operation"></a>Uzyskaj stan operacji

Długotrwałe procesy, takie jak proces tworzenia zwraca identyfikator operacji, który należy sprawdzić za pomocą oddzielnego wywołania interfejsu API REST. Ta funkcja przyjmuje treść odpowiedzi tworzenia. Ważnym kluczem `operationState`jest , który określa, czy trzeba kontynuować sondowanie.

Interfejs API REST służy [do monitorowania operacji w bazie wiedzy.](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)


[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=operationDetails)]


## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację `node rest-apis.js` za pomocą polecenia z katalogu aplikacji.

```console
node rest-apis.js
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję usług Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie innych skojarzonych z nią zasobów.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
>[Samouczek: Tworzenie i odpowiadanie na kb](../tutorials/create-publish-query-in-portal.md)

* [Co to jest interfejs API programu QnA Maker?](../Overview/overview.md)
* [Edytowanie bazy wiedzy](../how-to/edit-knowledge-base.md)
* [Uzyskaj analizę użycia](../how-to/get-analytics-knowledge-base.md)
* Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/rest-api/rest-api.js).