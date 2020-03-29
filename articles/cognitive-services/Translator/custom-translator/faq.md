---
title: Najczęściej zadawane pytania - Tłumacz niestandardowy
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure Cognitive Services Custom Translator.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: reference
ms.openlocfilehash: 97d399f4a8ec704fd90eb6c49f0835be7e9e4537
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73836591"
---
# <a name="custom-translator-frequently-asked-questions"></a>Często zadawane pytania dotyczące tłumacza niestandardowego

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące [usługi Custom Translator](https://portal.customtranslator.azure.ai).

## <a name="what-are-the-current-restrictions-in-custom-translator"></a>Jakie są bieżące ograniczenia w translatorze niestandardowym?

Istnieją ograniczenia i ograniczenia dotyczące rozmiaru pliku, szkolenia modelu i wdrażania modelu. Należy pamiętać o tych ograniczeniach podczas konfigurowania szkolenia w celu utworzenia modelu w uktory niestandardowej.

- Przesłane pliki muszą mieć rozmiar mniejszy niż 100 MB.
- Dane jednojęzyczne nie są obsługiwane.

## <a name="when-should-i-request-deployment-for-a-translation-system-that-has-been-trained"></a>Kiedy należy zażądać wdrożenia dla systemu tłumaczenia, który został przeszkolony?

Stworzenie optymalnego systemu tłumaczenia dla projektu może potrwać kilka szkoleń. Możesz spróbować użyć więcej danych szkoleniowych lub dokładniej filtrowanych danych, jeśli wynik BLEU i/ lub wyniki testów nie są zadowalające. Powinieneś być surowy i ostrożny w projektowaniu zestawu tuningowego i zestawu testowego, aby być w pełni reprezentatywnym dla terminologii i stylu materiału, który chcesz przetłumaczyć. Możesz być bardziej liberalny w komponowaniu danych szkoleniowych i eksperymentować z różnymi opcjami. Zażądaj wdrożenia systemu, gdy jesteś zadowolony z tłumaczeń w wynikach testów systemu, nie masz więcej danych do dodania do szkolenia w celu ulepszenia przeszkolonego systemu i chcesz uzyskać dostęp do przeszkolonego modelu za pośrednictwem interfejsów API.

## <a name="how-many-trained-systems-can-be-deployed-in-a-project"></a>Ile wyszkolonych systemów można wdrożyć w projekcie?

Tylko jeden przeszkolony system można wdrożyć na projekt. Stworzenie odpowiedniego systemu tłumaczenia dla projektu może potrwać kilka szkoleń, a my zachęcamy do zażądania wdrożenia szkolenia, które daje najlepszy wynik. Można określić jakość szkolenia według wyniku BLEU (wyższa jest lepsza) i konsultacji z recenzentami przed podjęciem decyzji, że jakość tłumaczeń jest odpowiednia do wdrożenia.

## <a name="when-can-i-expect-my-trainings-to-be-deployed"></a>Kiedy mogę się spodziewać, że moje szkolenia zostaną wdrożone?

Wdrożenie zwykle trwa mniej niż godzinę.

## <a name="how-do-you-access-a-deployed-system"></a>Jak uzyskać dostęp do wdrożonego systemu?

Wdrożone systemy są dostępne za pośrednictwem interfejsu API tekstu usługi Microsoft Translator w wersji 3, określając identyfikator kategorii. Więcej informacji na temat interfejsu API tekstu tłumacza można znaleźć na stronie internetowej [odwołania do interfejsu API.](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)

## <a name="how-do-i-skip-alignment-and-sentence-breaking-if-my-data-is-already-sentence-aligned"></a>Jak pominąć wyrównanie i łamanie zdania, jeśli moje dane są już wyrównane zdanie?

Translator niestandardowy pomija wyrównanie zdań i łamanie zdania `.align` dla plików TMX i plików tekstowych z rozszerzeniem. `.align`pliki dają użytkownikom możliwość pominięcia procesu łamania i wyrównywania zdania tłumacza niestandardowego dla plików, które są idealnie wyrównane i nie wymagają dalszego przetwarzania. Zalecamy `.align` używanie rozszerzenia tylko dla plików, które są idealnie wyrównane.

Jeśli liczba wyodrębnionych zdań nie jest zgodna z dwoma plikami o tej samej nazwie podstawowej, translator niestandardowy nadal będzie uruchamiał wyrównywanie zdań w plikach. `.align`

## <a name="i-tried-uploading-my-tmx-but-it-says-document-processing-failed"></a>Próbowałem przesłać mój TMX, ale mówi "przetwarzanie dokumentów nie powiodło się".

Upewnij się, że TMX jest zgodny ze specyfikacją TMX 1.4b pod adresem <https://www.gala-global.org/tmx-14b>.
