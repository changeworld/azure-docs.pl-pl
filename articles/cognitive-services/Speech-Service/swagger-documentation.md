---
title: Dokumentacja Swagger - Usługa mowy
titleSuffix: Azure Cognitive Services
description: Dokumentacja Swagger może służyć do automatycznego generowania zestawu SDK dla wielu języków programowania. Wszystkie operacje w naszym serwisie są obsługiwane przez Swagger
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: reference
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: fcc43caf895dadfaf832a47c3254f9b828bcb71a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77430809"
---
# <a name="swagger-documentation"></a>Dokumentacja platformy Swagger

Usługa mowy oferuje specyfikację Swagger do interakcji z kilkoma interfejsami API REST używanymi do importowania danych, tworzenia modeli, testowania dokładności modelu, tworzenia niestandardowych punktów końcowych, kolejkowania transkrypcji partii i zarządzania subskrypcjami. Większość operacji dostępnych za pośrednictwem portalu mowy niestandardowej można wykonać programowo przy użyciu tych interfejsów API.

> [!NOTE]
> Operacje zamiany mowy na tekst i zamiany tekstu na mowę są obsługiwane jako interfejsy API REST, które z kolei są udokumentowane w specyfikacji Swagger.

## <a name="generating-code-from-the-swagger-specification"></a>Generowanie kodu ze specyfikacji Swagger

[Specyfikacja Swagger](https://cris.ai/swagger/ui/index) posiada opcje, które pozwalają szybko przetestować różne ścieżki. Jednak czasami jest pożądane, aby wygenerować kod dla wszystkich ścieżek, tworząc pojedynczą bibliotekę wywołań, które można oprzeć przyszłych rozwiązań na. Przyjrzyjmy się procesowi generowania biblioteki Języka Python.

Musisz ustawić Swagger do tego samego regionu, co subskrypcja usługi mowy. Możesz potwierdzić swój region w witrynie Azure portal w ramach zasobu usługi mowy. Aby uzyskać pełną listę obsługiwanych regionów, zobacz [regiony](regions.md).

1. Przejdź do strony https://editor.swagger.io
2. Kliknij pozycję **Plik**, a następnie kliknij pozycję **Importuj**
3. Wprowadź adres URL swagger, w tym region subskrypcji usługi mowy`https://<your-region>.cris.ai/docs/v2.0/swagger`
4. Kliknij **pozycję Generuj klienta** i wybierz python
5. Zapisywanie biblioteki klienta

Można użyć biblioteki języka Python wygenerowanej za pomocą [przykładów usługi mowy w usłudze GitHub](https://aka.ms/csspeech/samples).

## <a name="reference-docs"></a>Dokumenty referencyjne

* [REST (Swagger): Transkrypcja partii i dostosowywanie](https://westus.cris.ai/swagger/ui/index)
* [INTERFEJS API REST: Zamiana mowy na tekst](rest-speech-to-text.md)
* [INTERFEJS API REST: Zamiana tekstu na mowę](rest-text-to-speech.md)

## <a name="next-steps"></a>Następne kroki

* [Przykłady usługi mowy w usłudze GitHub](https://aka.ms/csspeech/samples).
* [Uzyskaj bezpłatny klucz subskrypcji usługi mowy](get-started.md)
