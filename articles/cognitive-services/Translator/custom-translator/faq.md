---
title: Często zadawane pytania — niestandardowe w usłudze Translator
titleSuffix: Azure Cognitive Services
description: Zawiera odpowiedzi na często zadawane pytania dotyczące niestandardowych w usłudze Translator.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-pawal
ms.topic: reference
ms.openlocfilehash: 79dc5baacb19143f2b31de7f3822af382c2e6cf4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66384037"
---
# <a name="frequently-asked-questions"></a>Często zadawane pytania

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące [niestandardowe w usłudze Translator](https://portal.customtranslator.azure.ai).

## <a name="what-are-the-current-restrictions-in-custom-translator"></a>Jakie są bieżące ograniczenia w niestandardowych w usłudze Translator?

Istnieją ograniczenia i limity pod kątem rozmiaru pliku, szkolenie modelu i wdrażania modelu. Podczas konfigurowania szkolenia w celu zbudowania modelu w niestandardowych w usłudze Translator należy pamiętać o tych ograniczeń.

- Przesłanych plikach musi być mniejszy niż 100 MB.
- Jednojęzyczne dane nie są obsługiwane.

## <a name="when-should-i-request-deployment-for-a-translation-system-that-has-been-trained"></a>Gdy żądanie wdrożenia systemu tłumaczenia, po zapoznaniu?

Może upłynąć kilka szkoleniach tworzenia systemu tłumaczenia optymalne dla Twojego projektu. Można spróbować użyć więcej danych szkoleniowych lub bardziej dokładnie odfiltrowane dane, jeśli wynik BELEU i / lub wyniki testu nie są zadowalające. Powinny być ściśle i ostrożność podczas projektowania dostosowywania zestawu i test ustawiona, to w pełni językiem terminologii i stylu materiały, które chcesz przetłumaczyć. Może być bardziej liberalne w tworzenie danych szkoleniowych i eksperymentować z różnymi opcjami. Żądanie wdrożenia systemu, gdy jesteś zadowolony z tłumaczenia, w wynikach testów systemu, nie zawierają więcej danych do dodania do szkoleń, aby poprawić uczonego systemu i chcesz uzyskać dostęp do uczonego modelu za pomocą interfejsów API.

## <a name="how-many-trained-systems-can-be-deployed-in-a-project"></a>Jak wiele systemów uczonego można wdrożyć w projekcie?

Tylko jeden system uczonego można wdrożyć na projekt. Może upłynąć kilka szkoleniach utworzenie systemu tłumaczenia odpowiednie dla Twojego projektu, a firma Microsoft zachęca do żądania wdrożenia szkolenia, zapewniająca najlepszych wyników. Można określić jakość szkolenia według wyników BELEU (większa jest lepsza) oraz przez konsultacji z recenzentów, przed podjęciem decyzji, czy jakość tłumaczenia jest odpowiednia dla wdrożenia.

## <a name="when-can-i-expect-my-trainings-to-be-deployed"></a>Kiedy można spodziewać się Moje szkoleniach do wdrożenia?

Wdrożenie zwykle trwa krócej niż godzinę.

## <a name="how-do-you-access-a-deployed-system"></a>Jak można uzyskać dostępu do wdrożonym systemie?

Wdrożonych systemów jest możliwy za pośrednictwem V3 interfejs API tekstu usługi Translator firmy Microsoft, określając CategoryID. Więcej informacji na temat interfejsu API tekstu usługi Translator można znaleźć w [dokumentacja interfejsu API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference) strony sieci Web.

## <a name="how-do-i-skip-alignment-and-sentence-breaking-if-my-data-is-already-sentence-aligned"></a>Jak pominąć wyrównanie i zdania, jeśli moje dane są już zdania wyrównane?

Niestandardowe w usłudze Translator pomija wyrównanie zdania i zdanie istotne dla plików TMX i szukać plików tekstowych za pomocą `.align` rozszerzenia. `.align` pliki użytkownikom opcję pomijania zdania niestandardowe Translator istotne i proces wyrównywania dla plików, które są dokładnie wyrównana, a potrzebują, nie dalszego przetwarzania. Firma Microsoft zaleca używanie `.align` rozszerzenia tylko dla plików, które są dokładnie wyrównana.

Liczba wyodrębnionych zdania niezgodna dwa pliki o tej samej nazwie podstawowej niestandardowe w usłudze Translator będą nadal działać aligner zdania `.align` plików.

## <a name="i-tried-uploading-my-tmx-but-it-says-document-processing-failed"></a>Podjęto przekazanie mojego TMX, ale jest wyświetlany komunikat, "przetwarzania dokumentów nie powiodło się.".

Upewnij się, że TMX odpowiada TMX 1.4b specyfikacji na <https://www.gala-global.org/tmx-14b>.
