---
title: 'Szybki start: biblioteka klienta analizy tekstu w wersji 3 | Dokumenty firmy Microsoft'
titleSuffix: Azure Cognitive Services
description: Ten przewodnik Szybki start umożliwia połączenie aplikacji z interfejsem API analizy tekstu z usługi Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
zone_pivot_groups: programming-languages-text-analytics
ms.openlocfilehash: cc3f48ec1113f954336cfae0bda2cba2499d9a1d
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80240125"
---
# <a name="quickstart-use-the-text-analytics-client-library"></a>Szybki start: korzystanie z biblioteki klienta analizy tekstu

Wprowadzenie do biblioteki klienta analizy tekstu. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań.

Użyj biblioteki klienta analizy tekstu, aby wykonać:

* Analiza tonacji
* Wykrywanie języka
* Rozpoznawanie jednostek
* Wyodrębnianie kluczowych fraz

::: zone pivot="programming-language-csharp"

> [!IMPORTANT]
> * Najnowsza wersja interfejsu API analizy `3.0-preview`tekstu w wersji zapoznawczej to , która zawiera publiczną wersję zapoznawczą dla [ulepszonej analizy tonacji](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) i [rozpoznawania nazwanych jednostek (NER).](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) Najnowsza stabilna `2.1`wersja to .
>    * Pamiętaj, aby postępować tylko zgodnie z instrukcjami dotyczącymi używanej wersji.
> * Kod w tym artykule używa metod synchronicznych i niezabezpieczonych poświadczeń magazynu ze względu na prostotę. W scenariuszach produkcyjnych zaleca się używanie wsadowych metod asynchronicznych dla wydajności i skalowalności. Zapoznaj się z poniższą dokumentacją referencyjną.

[!INCLUDE [C# quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

> [!IMPORTANT]
> * Ten przewodnik Szybki start `3.0-preview` jest przeznaczony tylko dla wersji biblioteki klienta analizy tekstu, która zawiera publiczną wersję zapoznawczą dla [ulepszonej analizy tonacji](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) i [rozpoznawania nazwanych jednostek (NER).](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)
> * Kod w tym artykule używa metod synchronicznych i niezabezpieczonych poświadczeń magazynu ze względu na prostotę. W scenariuszach produkcyjnych zaleca się używanie wsadowych metod asynchronicznych dla wydajności i skalowalności. Zapoznaj się z poniższą dokumentacją referencyjną.

[!INCLUDE [Java quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

> [!IMPORTANT]
> * Najnowsza wersja interfejsu API analizy `3.0-preview`tekstu w wersji zapoznawczej to , która zawiera publiczną wersję zapoznawczą dla [ulepszonej analizy tonacji](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) i [rozpoznawania nazwanych jednostek (NER).](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) Najnowsza stabilna `2.1`wersja to .
>    * Pamiętaj, aby postępować tylko zgodnie z instrukcjami dotyczącymi używanej wersji.
> * Kod w tym artykule używa metod synchronicznych i niezabezpieczonych poświadczeń magazynu ze względu na prostotę. W scenariuszach produkcyjnych zaleca się używanie wsadowych metod asynchronicznych dla wydajności i skalowalności. Zapoznaj się z poniższą dokumentacją referencyjną.
> * Możesz również uruchomić tę wersję biblioteki klienta analizy tekstu [w przeglądarce](https://github.com/Azure/azure-sdk-for-js/blob/master/documentation/Bundling.md).

[!INCLUDE [NodeJS quickstart](../includes/quickstarts/nodejs-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

> [!IMPORTANT]
> * Najnowsza wersja interfejsu API analizy `3.0-preview`tekstu w wersji zapoznawczej to , która zawiera publiczną wersję zapoznawczą dla [ulepszonej analizy tonacji](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) i [rozpoznawania nazwanych jednostek (NER).](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) Najnowsza stabilna `2.1`wersja to .
>    * Pamiętaj, aby postępować tylko zgodnie z instrukcjami dotyczącymi używanej wersji.
> * Kod w tym artykule używa metod synchronicznych i niezabezpieczonych poświadczeń magazynu ze względu na prostotę. W scenariuszach produkcyjnych zaleca się używanie wsadowych metod asynchronicznych dla wydajności i skalowalności. Zapoznaj się z poniższą dokumentacją referencyjną. 

[!INCLUDE [Python quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="programming-language-other"

## <a name="additional-language-support"></a>Dodatkowa obsługa języków

Jeśli klikniesz tę kartę, prawdopodobnie nie widzisz szybkiego startu w ulubionym języku programowania. Nie martw się, mamy dodatkowe dostępne przewodniki Szybki start. Użyj tabeli, aby znaleźć odpowiedni przykład dla języka programowania.

| Język | Dostępna wersja | 
|----------|------------------------|
| Ruby     | [Wersja 2.1](ruby-sdk.md) | 
| Przejdź       | [Wersja 2.1](go-sdk.md) | 

::: zone-end

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję usług Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie innych skojarzonych z nią zasobów.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Poznaj rozwiązanie](../text-analytics-user-scenarios.md#analyze-recorded-inbound-customer-calls)

* [Analiza tekstu — omówienie](../overview.md)
* [Analiza tonacji](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Rozpoznawanie jednostek](../how-tos/text-analytics-how-to-entity-linking.md)
* [Wykrywanie języka](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Rozpoznawanie języka](../how-tos/text-analytics-how-to-language-detection.md)
