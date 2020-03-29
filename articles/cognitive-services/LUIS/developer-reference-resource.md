---
title: Zasoby dla deweloperów — opis języka
description: SDK, interfejsy API REST, interfejs wiersza polecenia, pomoc w opracowywaniu aplikacji do rozumienia języka (LUIS) w języku programowania. Zarządzanie zasobami platformy Azure i prognozami usługi LUIS.
ms.topic: reference
ms.date: 03/16/2020
ms.openlocfilehash: 5e375157cef4789bc2980f6154ea8d59e765ff3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79457988"
---
# <a name="sdk-rest-and-cli-developer-resources-for-language-understanding-luis"></a>Zasoby deweloperskie zestawu SDK, REST i CLI do rozumienia języka (LUIS)

SDK, interfejsy API REST, interfejs wiersza polecenia, pomoc w opracowywaniu aplikacji do rozumienia języka (LUIS) w języku programowania. Zarządzanie zasobami platformy Azure i prognozami usługi LUIS.

## <a name="azure-resource-management"></a>Zarządzanie zasobami platformy Azure

Warstwa Usługi Azure Cognitive Services Management służy do tworzenia, edytowania, wystawiania i usuwania zasobu Opis języka lub Usługi Cognitive Service.

Znajdź dokumentację referencyjną opartą na narzędziu:

* [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/cognitiveservices#az-cognitiveservices-list)

* [Azure RM PowerShell](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/?view=azurermps-4.4.1#cognitive_services)


## <a name="language-understanding-authoring-and-prediction-requests"></a>Opis języka żądania tworzenia i przewidywania

Usługa opisu języka jest dostępna z zasobu platformy Azure, który należy utworzyć. Istnieją dwa zasoby:

* Zasób **tworzenia** służy do szkolenia do tworzenia, edytowania, szkolenia i publikowania.
* Użyj **przewidywanie** środowiska wykonawczego, aby wysłać tekst użytkownika i otrzymać przewidywanie.

Dowiedz się więcej o [punkcie końcowym przewidywania V3](luis-migration-api-v3.md).

Użyj [przykładowego kodu usług Cognitive Services,](https://github.com/Azure-Samples/cognitive-services-quickstart-code) aby nauczyć się i korzystać z najczęstszych zadań.

### <a name="rest-specifications"></a>Specyfikacje REST

[Specyfikacje usługi LUIS REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/cognitiveservices/data-plane/LUIS)wraz ze [wszystkimi specyfikacjami usługi Azure REST](https://github.com/Azure/azure-rest-api-specs)są publicznie dostępne w usłudze GitHub.

### <a name="rest-apis"></a>Interfejsy API REST

Zarówno tworzenie, jak i przewidywanie punktów końcowych APIS są dostępne w interfejsach API REST:

|Typ|Wersja|
|--|--|
|Tworzenie|[V2 (w 2.](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[podgląd V3](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|Prediction (Prognoza)|[V2 (w 2.](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[Wersja V3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="rest-endpoints"></a>PUNKTY KOŃCOWE REST

Usługa LUIS ma obecnie 2 typy punktów końcowych:

* tworzenie na punkcie końcowym szkolenia
* przewidywanie kwerend w punkcie końcowym środowiska wykonawczego.

|Przeznaczenie|Adres URL|
|--|--|
|Tworzenie w punkcie końcowym szkolenia|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|V2 Runtime — wszystkie prognozy w punkcie końcowym środowiska wykonawczego|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|
|V3 Runtime — przewidywanie wersji w punkcie końcowym środowiska wykonawczego|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/versions/{versionId}/predict?query={query}[&verbose][&log][&show-all-intents]`|
|V3 Runtime — przewidywanie gniazda w punkcie końcowym środowiska wykonawczego|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/slots/{slotName}/predict?query={query}[&verbose][&log][&show-all-intents]`|

W poniższej tabeli wyjaśniono parametry oznaczone `{}`nawiasami klamrowymi w poprzedniej tabeli.

|Parametr|Przeznaczenie|
|--|--|
|`your-resource-name`|Nazwa zasobu platformy Azure|
|`q` lub `query`|tekst wypowiedź wysłany z aplikacji klienckiej, takiej jak chat bot|
|`version`|10-znakowa nazwa wersji|
|`slot`| `production` lub `staging`|

### <a name="language-based-sdks"></a>SDK oparte na języku

|Język |Dokumentacja referencyjna|Pakiet|Samples|Przewodniki Szybki start|
|--|--|--|--|--|
|C#|[Tworzenie](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[Przewidywanie](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[Tworzenie nuget](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[Przewidywanie NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[Próbki SDK platformy .Net](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/LUIS)|[Tworzenie aplikacji i zarządzanie nią](sdk-authoring.md?pivots=programming-language-csharp)<br>[Punkt końcowy przewidywania zapytania](sdk-query-prediction-endpoint.md)|
|Przejdź|[Tworzenie i przewidywanie](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[Sdk](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)|[Tworzenie](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/go)<br>[Przewidywanie](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/go)|[Tworzenie i przewidywanie przy użyciu REST](luis-get-started-get-intent-from-rest.md)|
|Java|[Tworzenie i przewidywanie](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-java-stable)|[Tworzenie maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Przewidywanie Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|[Tworzenie](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/java)<br>[Przewidywanie](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/java)|[Tworzenie i przewidywanie](luis-get-started-get-intent-from-rest.md)
|Node.js|[Tworzenie](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)<br>[Przewidywanie](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)|[Tworzenie npm](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)<br>[Przewidywanie NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)|[Tworzenie](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/node)<br>[Przewidywanie](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/node)|[Tworzenie i przewidywanie przy użyciu REST](luis-get-started-get-intent-from-rest.md)|
|Python|[Tworzenie i przewidywanie](sdk-authoring.md?pivots=programming-language-python)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[Tworzenie](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)|[Tworzenie](sdk-authoring.md?pivots=programming-language-python)<br>[Przewidywanie przy użyciu REST](luis-get-started-get-intent-from-rest.md)


### <a name="containers"></a>Kontenery

Zrozumienie języka (LUIS) udostępnia [kontener](luis-container-howto.md) do dostarczania lokalnych i zawartych wersji aplikacji.

### <a name="export-and-import-formats"></a>Formaty eksportu i importu

Opis języka zapewnia możliwość zarządzania aplikacją i jej modelami w formacie JSON, formacie `.LU` [(LUDown)](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)i skompresowanym pakiecie dla kontenera Zrozumienie języka.

Importowanie i eksportowanie tych formatów jest dostępne w interfejsach API i z portalu usługi LUIS. Portal udostępnia importowanie i eksportowanie jako część listy Aplikacje i wersje listy.

## <a name="other-tools-and-sdks"></a>Inne narzędzia i zestawY SDK

Struktura botów jest dostępna jako [zestaw SDK](https://github.com/Microsoft/botframework) w różnych językach i jako usługa korzystająca z [usługi Azure Bot Service.](https://dev.botframework.com/)

Struktura botów zawiera [kilka narzędzi](https://github.com/microsoft/botbuilder-tools) ułatwiających zrozumienie języka, w tym:

* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown) - Tworzenie modeli rozumienia języka usługi LUIS przy użyciu plików znaczników
* [WIERSZ POLECENIA USŁUGI LUIS](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUIS) — tworzenie aplikacji LUIS.ai i zarządzanie nimi
* [Wysyłka](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Dispatch)- zarządzanie aplikacjami nadrzędnymi i podrzędnymi
* [LUISGen](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUISGen) — automatyczne generowanie kopii C#/Typescript klas intencji usługi LUIS i jednostek.
* [Emulator Bot Framework](https://github.com/Microsoft/BotFramework-Emulator/releases) - aplikacja komputerowa, która umożliwia deweloperom botów testowanie i debugowanie botów zbudowanych przy użyciu SDK Programu Bot Framework
* [Bot Framework Composer](https://github.com/microsoft/BotFramework-Composer/blob/stable/README.md) - zintegrowane narzędzie programistyczne dla programistów i zespołów multidyscyplinarnych do tworzenia botów i doświadczeń konwersacyjnych za pomocą programu Microsoft Bot Framework
* [microsoft/NLU. DevOps](https://github.com/microsoft/NLU.DevOps) — narzędzia wspierające ciągłą integrację i wdrażanie usług NLU.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o typowych [kodach błędów HTTP](luis-reference-response-codes.md)
* [Dokumentacja referencyjna](https://docs.microsoft.com/azure/index) dla wszystkich interfejsów API i SDK
* [Struktura botów](https://github.com/Microsoft/botbuilder-dotnet) i [usługa Azure Bot Service](https://dev.botframework.com/)
* [LuDown ( LUDown )](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [Pojemniki poznawcze](../cognitive-services-container-support.md)
