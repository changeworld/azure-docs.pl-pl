---
title: Struktury swagger dokumentacja — usługi mowy
titleSuffix: Azure Cognitive Services
description: Dokumentacja programu Swagger umożliwia automatyczne generowanie zestawów SDK dla wielu języków programowania. Wszystkie operacje w naszej usłudze są obsługiwane przez strukturę Swagger
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 04/12/2019
ms.author: erhopf
ms.openlocfilehash: 6cf3ab6480900aa763598120e6ff7e308f5044e1
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2019
ms.locfileid: "66743220"
---
# <a name="swagger-documentation"></a>Dokumentacja platformy Swagger

Usługi mowy oferują Specyfikacja Swagger do interakcji z kilku interfejsów API REST umożliwia importowanie danych, tworzenie modeli, testowanie dokładności modelu, tworzenie niestandardowych punktów końcowych, transkrypcje usługi batch w kolejce i zarządzania subskrypcjami. Większość operacji dostępne za pośrednictwem portalu usługi Custom Speech mogą być wprowadzane w sposób programowy za pomocą tych interfejsów API. 

> [!NOTE]
> Operacje zamiany mowy na tekst i zamiany tekstu na mowę są obsługiwane dostępne jako interfejsy API REST, który z kolei są opisane w specyfikacji Swagger.

## <a name="generating-code-from-the-swagger-specification"></a>Generowanie kodu z specyfikacją struktury Swagger

[Specyfikacjami struktury Swagger](https://cris.ai/swagger/ui/index) zawiera opcje, które pozwalają na szybkie testowanie dla różnych ścieżek. Jednak czasami jest pożądane, aby wygenerować kod dla wszystkich ścieżek, jedna biblioteka wywołań, które przyszłe rozwiązania można oprzeć na tworzenie. Przyjrzyjmy się tego procesu, aby wygenerować bibliotekę języka Python.

Należy ustawić struktury Swagger do tego samego regionu co w przypadku subskrypcji usługa rozpoznawania mowy. Możesz potwierdzić swój region w witrynie Azure portal w ramach zasobu usług przetwarzania mowy. Aby uzyskać pełną listę obsługiwanych regionów, zobacz [regionów](regions.md).

1. Przejdź do strony https://editor.swagger.io
2. Kliknij przycisk **pliku**, następnie kliknij przycisk **importu**
3. Wprowadź adres URL programu swagger, w tym regionie dla Twojej subskrypcji usługi mowy `https://<your-region>.cris.ai/docs/v2.0/swagger`
4. Kliknij przycisk **wygenerować klienta** oraz wybieranie języka Python
5. Zapisz biblioteki klienckiej

Możesz użyć biblioteki Python, który został wygenerowany za pomocą [przykłady usług mowy w serwisie GitHub](https://aka.ms/csspeech/samples).

## <a name="reference-docs"></a>Dokumentacja

* [REST (Swagger): Dostosowywanie i transkrypcji usługi Batch](https://westus.cris.ai/swagger/ui/index)
* [INTERFEJS API REST: Zamiany mowy na tekst](rest-speech-to-text.md)
* [INTERFEJS API REST: Zamiana tekstu na mowę](rest-text-to-speech.md)

## <a name="next-steps"></a>Kolejne kroki

* [Przykłady usług mowy w serwisie GitHub](https://aka.ms/csspeech/samples).
* [Bezpłatnie Uzyskaj klucz subskrypcji usług przetwarzania mowy](get-started.md)
