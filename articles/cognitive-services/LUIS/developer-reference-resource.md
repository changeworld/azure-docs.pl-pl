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
ms.date: 09/18/2019
ms.author: diberry
ms.openlocfilehash: 01d01461c540949693855ca407920d52b22ad714
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71106857"
---
# <a name="developer-resources-for-language-understanding"></a>Zasoby dla deweloperów Language Understanding

Deweloperzy mogą używać interfejsów API REST i zestawów SDK dla Language Understanding. 

## <a name="azure-resource-management"></a>Zarządzanie zasobami platformy Azure

Korzystając z warstwy zarządzania usługi Azure Cognitive Services, można tworzyć, edytować, wyświetlać i usuwać zasoby Language Understanding lub usług poznawczych.

Zapoznaj się z dokumentacją referencyjną opartą na narzędziu:

* [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/cognitiveservices?view=azure-cli-latest#az_cognitiveservices_list)

* [Azure RM PowerShell](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/?view=azurermps-4.4.1#cognitive_services)

## <a name="language-understanding-authoring-and-prediction-requests"></a>Language Understanding tworzenia i przewidywania żądań

Usługa Language Understanding jest dostępna z zasobów platformy Azure, które należy utworzyć. Istnieją dwa zasoby: Tworzenie i prognozowanie zasobów punktów końcowych. Oba te zasoby umożliwiają kontrolowanie zasobów LUIS. 

### <a name="rest-apis"></a>Interfejsy API REST

Interfejsy API tworzenia i przewidywania są dostępne w interfejsach API REST:

* Tworzenie [dokumentacji referencyjnej](https://go.microsoft.com/fwlink/?linkid=2092087)
* [Dokumentacja dotycząca dokumentacji dotyczącej](https://go.microsoft.com/fwlink/?linkid=2092356) środowiska uruchomieniowego przewidywania

### <a name="language-based-sdks"></a>Zestawy SDK oparte na języku

|Język |Dokumentacja referencyjna|Pakiet|Przykłady|Przewodniki Szybki start|
|--|--|--|--|--|
|C#|[Tworzenie](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[Przewidywanie](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[Tworzenie pakietów NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[Prognoza NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[Przykłady zestawu SDK dla platformy .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/LUIS)|[Tworzenie aplikacji i zarządzanie nią](sdk-csharp-quickstart-authoring-app.md)<br>[Punkt końcowy przewidywania zapytań](sdk-csharp-quickstart-query-prediction-endpoint.md)|
|Z rzeczywistym użyciem|[Tworzenie i prognozowanie](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)|[Tworzenie](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/go)<br>[Przewidywanie](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/go)|[Tworzenie przy użyciu REST](luis-get-started-go-add-utterance.md)<br>[Prognoza przy użyciu REST](luis-get-started-go-get-intent.md)|
|Java|[Tworzenie i prognozowanie](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-java-stable)|[Tworzenie Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Przewidywania Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|[Tworzenie](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/java)<br>[Przewidywanie](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/java)|[Tworzenie](luis-get-started-java-add-utterance.md)<br>[Przewidywanie](luis-get-started-java-get-intent.md)
|Node.js|[Tworzenie](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)<br>[Przewidywanie](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)|[Tworzenie NPM](https://www.npmjs.com/package/azure-cognitiveservices-luis-authoring)<br>[Przewidywania NPM](https://www.npmjs.com/package/azure-cognitiveservices-luis-runtime)|[Tworzenie](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/node)<br>[Przewidywanie](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/node)|[Tworzenie przy użyciu REST](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-node-get-intent)<br>[Prognoza przy użyciu REST](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-node-add-utterance)|
|Python|[Tworzenie i prognozowanie](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/?view=azure-python)|[Graczy](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[Tworzenie](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)|[Tworzenie](sdk-python-quickstart-authoring-app.md)<br>[Prognoza przy użyciu REST](luis-get-started-python-get-intent.md)

## <a name="other-tools-and-sdks"></a>Inne narzędzia i zestawy SDK

Bot Framework jest dostępny jako [zestaw SDK](https://github.com/Microsoft/botframework) w różnych językach i jako usługa korzystająca [Azure bot Service](https://dev.botframework.com/). 

Bot Framework udostępnia [kilka narzędzi](https://github.com/microsoft/botbuilder-tools) , które ułatwiają Language Understanding, w tym:

* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown) — Kompilowanie modeli Luis, przy użyciu plików z promocji
* [Interfejs wiersza polecenia Luis](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUIS) — tworzenie aplikacji Luis.AI i zarządzanie nimi
* [Wysyłanie](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Dispatch)— Zarządzanie aplikacjami nadrzędnymi i podrzędnymi
* [LUISGen](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUISGen) — automatycznie Generuj zapasowe C#klasy/TypeScript dla Twoich Luis i jednostek.
* [Emulator bot](https://github.com/Microsoft/BotFramework-Emulator/releases) — aplikacja klasyczna, która umożliwia deweloperom bot testowanie i debugowanie botów utworzonych przy użyciu zestawu SDK bot Framework


## <a name="next-steps"></a>Następne kroki

Informacje o typowych [kodach błędów HTTP](luis-reference-response-codes.md)