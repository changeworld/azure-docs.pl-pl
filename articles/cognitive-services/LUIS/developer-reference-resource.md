---
title: Zasoby dla deweloperów — Language Understanding
titleSuffix: Azure Cognitive Services
description: Deweloperzy mają interfejsy API REST i zestawy SDK dla Language Understanding.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/22/2020
ms.author: diberry
ms.openlocfilehash: 2f351ac570080c83e78697bbca94340bb96cbcf7
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76716139"
---
# <a name="developer-resources-for-language-understanding"></a>Zasoby dla deweloperów Language Understanding

Deweloperzy mogą używać interfejsów API REST i zestawów SDK dla Language Understanding.

## <a name="azure-resource-management"></a>Zarządzanie zasobami platformy Azure

Korzystając z warstwy zarządzania usługi Azure Cognitive Services, można tworzyć, edytować, wyświetlać i usuwać zasoby Language Understanding lub usług poznawczych.

Zapoznaj się z dokumentacją referencyjną opartą na narzędziu:

* [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/cognitiveservices#az-cognitiveservices-list)

* [Azure RM PowerShell](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/?view=azurermps-4.4.1#cognitive_services)

## <a name="language-understanding-authoring-and-prediction-requests"></a>Language Understanding tworzenia i przewidywania żądań

Usługa Language Understanding jest dostępna z zasobów platformy Azure, które należy utworzyć. Istnieją dwa zasoby: Tworzenie i prognozowanie zasobów punktów końcowych. Oba te zasoby umożliwiają kontrolowanie zasobów LUIS.

Dowiedz się więcej o [punkcie końcowym przewidywania v3](luis-migration-api-v3.md).

### <a name="rest-apis"></a>Interfejsy API REST

Interfejsy API tworzenia i przewidywania są dostępne w interfejsach API REST:

|Typ|Wersja|
|--|--|
|Tworzenie|[V](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[wersja zapoznawcza v3](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|Prognozy|[V](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[Czytanie](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="language-based-sdks"></a>Zestawy SDK oparte na języku

|Język |Dokumentacja referencyjna|Pakiet|Przykłady|Przewodniki Szybki start|
|--|--|--|--|--|
|C#|[Tworzenie](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[Przewidywanie](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[Tworzenie pakietów NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[Prognoza NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[Przykłady zestawu SDK dla platformy .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/LUIS)|[Tworzenie aplikacji i zarządzanie nią](sdk-authoring.md?pivots=programming-language-csharp)<br>[Punkt końcowy przewidywania zapytań](sdk-query-prediction-endpoint.md)|
|Przejdź|[Tworzenie i prognozowanie](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)|[Tworzenie](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/go)<br>[Przewidywanie](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/go)|[Tworzenie i prognozowanie przy użyciu REST](luis-get-started-get-intent-from-rest.md)|
|Java|[Tworzenie i prognozowanie](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-java-stable)|[Tworzenie Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Przewidywania Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|[Tworzenie](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/java)<br>[Przewidywanie](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/java)|[Tworzenie i prognozowanie](luis-get-started-get-intent-from-rest.md)
|Node.js|[Tworzenie](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)<br>[Przewidywanie](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)|[Tworzenie NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)<br>[Przewidywania NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)|[Tworzenie](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/node)<br>[Przewidywanie](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/node)|[Tworzenie i prognozowanie przy użyciu REST](luis-get-started-get-intent-from-rest.md)|
|Python|[Tworzenie i prognozowanie](sdk-authoring.md?pivots=programming-language-python)|[Graczy](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[Tworzenie](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)|[Tworzenie](sdk-authoring.md?pivots=programming-language-python)<br>[Prognoza przy użyciu REST](luis-get-started-get-intent-from-rest.md)


### <a name="containers"></a>Containers

Language Understanding (LUIS) udostępnia [kontener](luis-container-howto.md) do udostępniania lokalnych i zawartych w nim wersji aplikacji.

### <a name="export-and-import-formats"></a>Formaty eksportu i importu

Language Understanding zapewnia możliwość zarządzania aplikacją i jej modelami w formacie JSON, formacie `.LU` ([LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)) i skompresowanym pakietem dla kontenera Language Understanding.

Importowanie i eksportowanie tych formatów jest dostępne z interfejsów API i portalu LUIS. Portal umożliwia importowanie i eksportowanie w ramach listy aplikacji i wersji.

## <a name="other-tools-and-sdks"></a>Inne narzędzia i zestawy SDK

Bot Framework jest dostępny jako [zestaw SDK](https://github.com/Microsoft/botframework) w różnych językach i jako usługa korzystająca [Azure bot Service](https://dev.botframework.com/).

Bot Framework udostępnia [kilka narzędzi](https://github.com/microsoft/botbuilder-tools) , które ułatwiają Language Understanding, w tym:

* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown) — Kompilowanie modeli Luis, przy użyciu plików z promocji
* [Interfejs wiersza polecenia Luis](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUIS) — tworzenie aplikacji Luis.AI i zarządzanie nimi
* [Wysyłanie](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Dispatch)— Zarządzanie aplikacjami nadrzędnymi i podrzędnymi
* [LUISGen](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUISGen) — automatycznie Generuj zapasowe C#klasy/TypeScript dla Twoich Luis i jednostek.
* [Emulator bot](https://github.com/Microsoft/BotFramework-Emulator/releases) — aplikacja klasyczna, która umożliwia deweloperom bot testowanie i debugowanie botów utworzonych przy użyciu zestawu SDK bot Framework


## <a name="next-steps"></a>Następne kroki

* Informacje o typowych [kodach błędów HTTP](luis-reference-response-codes.md)
* [Dokumentacja referencyjna](https://docs.microsoft.com/azure/index#pivot=sdkstools) dla wszystkich interfejsów API i zestawów SDK
* [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) i [Azure bot Service](https://dev.botframework.com/)
* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [Kontenery poznawcze](../cognitive-services-container-support.md)