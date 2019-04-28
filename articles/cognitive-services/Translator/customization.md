---
title: Dostosowanie tłumaczenia — interfejs API tekstu usługi Translator
titlesuffix: Azure Cognitive Services
description: Użyj Centrum w usłudze Translator firmy Microsoft, aby utworzyć własny system tłumaczenia maszynowego, korzystając z preferowanych terminologii i stylu.
services: cognitive-services
author: v-pawal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: v-jansko
ms.openlocfilehash: b9a182da0ce03bf3964b6da30f1ed68de38c8141
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61468367"
---
# <a name="customize-your-text-translations"></a>Dostosuj tłumaczenia tekstu

Usługa Microsoft Translator niestandardowe to funkcja usługi Microsoft Translator, która umożliwia użytkownikom dostosowywanie zaawansowane neuronowego tłumaczenia maszynowego Microsoft Translator podczas tłumaczenia tekstu przy użyciu interfejsu API tłumaczenia tekstu (tylko wersja 3).

Tej funkcji można również dostosować tłumaczenia mowy, gdy jest używane z [mowy w usłudze Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/speech-service/).

## <a name="custom-translator"></a>Custom Translator

Translator niestandardowe umożliwiają tworzenie tłumaczenie neuronowe systemami, zapoznaj się z terminologią używane we własnych firm i branży. System tłumaczeń dostosowany zostanie następnie integrowanie istniejących aplikacji, przepływów pracy i witryn sieci Web.

### <a name="how-does-it-work"></a>Jak to działa?

Użyj wcześniej przetłumaczone dokumentów (ulotki, stron sieci Web, dokumentacji, itp.), system tłumaczeń, odzwierciedlający Twoje specyficzne dla domeny terminologii i stylu, lepiej niż system ogólnego tłumaczenia kompilacji. Użytkownicy mogą przesłać dokumenty TMX XLIFF, TXT, DOCX i XLSX.  

System akceptuje także danych, który jest równolegle na poziomie dokumentu, ale jeszcze nie jest wyrównany na poziomie pojedynczych zdań. Jeśli użytkownicy mają dostęp do wersji tej samej zawartości, w wielu językach, ale w oddzielnych dokumentów niestandardowe w usłudze Translator będzie można automatycznie dopasowywać zdań między dokumentów.  System umożliwia również jednojęzyczne danych w językach jednego lub obu tych uzupełniają dane równoległe szkolenie i ulepszać tłumaczenia.

Dostosowany system jest dostępna za pośrednictwem regularnych wywołania Microsoft interfejsu API tłumaczenia tekstu przy użyciu parametru kategorii.

Biorąc pod uwagę odpowiedniego typu i ilość danych szkoleniowych nie jest niczym niezwykłym, można oczekiwać zyski od 5 do 10 lub BELEU jeszcze więcej punktów jakości tłumaczenia przy użyciu niestandardowych w usłudze Translator.

Więcej informacji na temat różnych poziomów dostosowywania oparte na dostępnych danych można znaleźć w [podręcznika użytkownika w usłudze Translator niestandardowe](https://aka.ms/CustomTranslatorDocs).


## <a name="microsoft-translator-hub"></a>Microsoft Translator Hub

> [!NOTE]
> Starsze Centrum w usłudze Translator firmy Microsoft zostanie wycofana 30 kwietnia 2019 r. [Wyświetl informacje na temat migracji ważne i dat](https://www.microsoft.com/translator/business/hub/).  

## <a name="custom-translator-versus-hub"></a>Niestandardowe w usłudze Translator w stosunku do Centrum

|   | **Centrum** | **Niestandardowe w usłudze Translator**|
|:-----|:----:|:----:|
|Stan funkcji dostosowywania   | Ogólna dostępność  | Ogólna dostępność |
| Interfejs API tłumaczenia tekstu w wersji  | Tylko w wersji 2   | Tylko w wersji 3 |
| Dostosowywanie SMT | Yes   | Nie |
| Dostosowywanie NMT | Nie    | Yes |
| Nowe ujednolicone dostosowywanie usług mowy | Nie    | Yes |
| [Bez śledzenia](https://www.aka.ms/notrace) | Yes  | Yes |

## <a name="collaborative-translations-framework"></a>Framework współpracy tłumaczenia

> [!NOTE]
> Od 1 lutego 2018 r. AddTranslation() i AddTranslationArray() nie są już dostępne do użytku z programem V2.0 interfejs API tekstu usługi Translator. Te metody zakończy się niepowodzeniem i nic nie zostanie zapisany. V3.0 interfejs API tekstu usługi Translator nie obsługuje tych metod.
> 
> Podobne funkcje są dostępne w interfejsie API usługi Translator koncentratora. Zobacz [ https://hub.microsofttranslator.com/swagger ](https://hub.microsofttranslator.com/swagger).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Skonfiguruj system dostosowane języka, przy użyciu niestandardowych w usłudze Translator](https://aka.ms/CustomTranslatorDocs)
