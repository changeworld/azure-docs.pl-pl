---
title: Dostosowanie tłumaczenia — interfejs API tłumaczenia tekstu w usłudze Translator
titleSuffix: Azure Cognitive Services
description: Użyj centrum usługi Microsoft translator, aby skompilować własny system tłumaczenia maszynowego przy użyciu preferowanej terminologii i stylu.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: e6771a0d72592f1952853642ceea196b02774ada
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595314"
---
# <a name="customize-your-text-translations"></a>Dostosowywanie tłumaczenia tekstu

Usługa Microsoft Translator firmy Microsoft jest funkcją usługi tłumaczenia, która umożliwia użytkownikom dostosowanie zaawansowanego tłumaczenia maszynowego neuronowych w usłudze Microsoft Translator podczas tłumaczenia tekstu przy użyciu interfejs API tłumaczenia tekstu w usłudze Translator (tylko wersja 3).

Funkcja ta może również służyć do dostosowywania tłumaczenia mowy, gdy jest używany z [Cognitive Services mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/).

## <a name="custom-translator"></a>Custom Translator

Dzięki usłudze translator niestandardowy można budować systemy tłumaczenia neuronowych, które znają terminologię używaną we własnej firmie i branży. Dostosowany system tłumaczenia zostanie następnie zintegrowany z istniejącymi aplikacjami, przepływami pracy i witrynami sieci Web.

### <a name="how-does-it-work"></a>Jak to działa?

Skorzystaj z wcześniej przetłumaczonych dokumentów (ulotek, stron sieci Web, dokumentacji itp.), aby skompilować system tłumaczenia, który odzwierciedla terminologię i styl specyficzny dla domeny, lepszy niż ogólny system tłumaczenia. Użytkownicy mogą przekazywać dokumenty TMX, XLIFF, TXT, DOCX i XLSX.  

System akceptuje również dane, które są równoległe na poziomie dokumentu, ale nie są jeszcze wyrównane na poziomie zdania. Jeśli użytkownicy mają dostęp do wersji tej samej zawartości w wielu językach, ale w oddzielnych dokumentach translator niestandardowy będzie mógł automatycznie dopasować zdania w dokumentach.  System może również używać danych w języku lub w obu językach, aby uzupełnić równoległe dane szkoleniowe w celu usprawnienia tłumaczenia.

Dostosowany system jest następnie dostępny za pośrednictwem zwykłego wywołania do interfejs API tłumaczenia tekstu w usłudze Translator firmy Microsoft przy użyciu parametru kategorii.

Uwzględniając odpowiedni typ i ilość danych szkoleniowych, nie zdarza się, że występuje różnica między 5 a 10, lub jeszcze więcej punktów BLEU dotyczących jakości tłumaczenia przy użyciu translatora niestandardowego.

Więcej szczegółowych informacji na temat różnych poziomów dostosowywania opartych na dostępnych danych można znaleźć w [podręczniku użytkownika usługi tłumaczenia niestandardowego](https://aka.ms/CustomTranslatorDocs).


## <a name="microsoft-translator-hub"></a>Microsoft Translator Hub

> [!NOTE]
> Starsza wersja centrum usługi Microsoft Translator zostanie wycofana w dniu 17 maja 2019. [Wyświetlanie ważnych informacji i dat migracji](https://www.microsoft.com/translator/business/hub/).  

## <a name="custom-translator-versus-hub"></a>Niestandardowe w usłudze Translator w stosunku do Centrum

|   | **Centrum** | **Translator niestandardowy**|
|:-----|:----:|:----:|
|Stan funkcji dostosowywania   | Ogólna dostępność  | Ogólna dostępność |
| Interfejs API tłumaczenia tekstu w wersji  | Tylko wersja 2   | Tylko wersja 3 |
| Dostosowywanie SMT | Yes   | Nie |
| Dostosowywanie NMT | Nie    | Yes |
| Nowe ujednolicone dostosowywanie usług mowy | Nie    | Yes |
| [Brak śladu](https://www.aka.ms/notrace) | Yes  | Tak |

## <a name="collaborative-translations-framework"></a>Struktura tłumaczeń współpracy

> [!NOTE]
> Od 1 lutego 2018, addtranslation () i AddTranslationArray () nie można już używać z interfejs API tłumaczenia tekstu w usłudze Translator V 2.0. Te metody zakończą się niepowodzeniem i nic nie zostanie zapisaniu. Interfejs API tłumaczenia tekstu w usłudze Translator V 3.0 nie obsługuje tych metod.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Skonfiguruj dostosowany system językowy przy użyciu translatora niestandardowego](https://aka.ms/CustomTranslatorDocs)
