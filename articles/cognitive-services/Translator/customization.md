---
title: Dostosowanie tłumaczenia — interfejs API tekstu usługi Translator
titlesuffix: Azure Cognitive Services
description: Użyj Centrum w usłudze Translator firmy Microsoft, aby utworzyć własny system tłumaczenia maszynowego, korzystając z preferowanych terminologii i stylu.
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: v-pawal
ms.openlocfilehash: cb15ae375f412a66b9d7939b6a580ebb95f2f7a8
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66515101"
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
> Starsze Centrum w usłudze Translator firmy Microsoft zostaną wycofane z dniem 17 maja 2019 r. [Wyświetl informacje na temat migracji ważne i dat](https://www.microsoft.com/translator/business/hub/).  

## <a name="custom-translator-versus-hub"></a>Niestandardowe w usłudze Translator w stosunku do Centrum

|   | **Centrum** | **Niestandardowe w usłudze Translator**|
|:-----|:----:|:----:|
|Stan funkcji dostosowywania   | Ogólna dostępność  | Ogólna dostępność |
| Interfejs API tłumaczenia tekstu w wersji  | Tylko w wersji 2   | Tylko w wersji 3 |
| Dostosowywanie SMT | Yes   | Nie |
| Dostosowywanie NMT | Nie    | Yes |
| Nowe ujednolicone dostosowywanie usług mowy | Nie    | Tak |
| [Bez śledzenia](https://www.aka.ms/notrace) | Tak  | Tak |

## <a name="collaborative-translations-framework"></a>Framework współpracy tłumaczenia

> [!NOTE]
> Od 1 lutego 2018 r. AddTranslation() i AddTranslationArray() nie są już dostępne do użytku z programem V2.0 interfejs API tekstu usługi Translator. Te metody zakończy się niepowodzeniem i nic nie zostanie zapisany. V3.0 interfejs API tekstu usługi Translator nie obsługuje tych metod.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Skonfiguruj system dostosowane języka, przy użyciu niestandardowych w usłudze Translator](https://aka.ms/CustomTranslatorDocs)
