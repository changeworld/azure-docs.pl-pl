---
title: Dostosowywanie tłumaczeń — interfejs API tekstu tłumacza
titleSuffix: Azure Cognitive Services
description: Za pomocą Centrum Microsoft Translator Hub można utworzyć własny system tłumaczenia maszynowego przy użyciu preferowanej terminologii i stylu.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: be9c06d45da4d83c26f82343c9cb7b19ba19b4ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "71257619"
---
# <a name="customize-your-text-translations"></a>Dostosowywanie tłumaczeń tekstowych

Microsoft Custom Translator jest funkcją usługi Microsoft Translator, która umożliwia użytkownikom dostosowanie zaawansowanego tłumaczenia maszynowego usługi Microsoft Translator podczas tłumaczenia tekstu przy użyciu interfejsu API tekstu tłumacza (tylko wersja 3).

Funkcja ta może być również używana do dostosowywania tłumaczenia mowy, gdy jest używana z [programem Cognitive Services Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/).

## <a name="custom-translator"></a>Custom Translator

Za pomocą usługi Custom Translator można tworzyć systemy tłumaczeń neuronowych, które rozumieją terminologię używaną w twojej firmie i przemyśle. Dostosowany system tłumaczeń zostanie następnie zintegrowany z istniejącymi aplikacjami, przepływami pracy i witrynami sieci Web.

### <a name="how-does-it-work"></a>Jak to działa?

Użyj wcześniej przetłumaczonych dokumentów (ulotek, stron internetowych, dokumentacji itp.), aby zbudować system tłumaczeń, który odzwierciedla terminologię i styl specyficzny dla domeny, lepszy niż standardowy system tłumaczenia. Użytkownicy mogą przesyłać dokumenty TMX, XLIFF, TXT, DOCX i XLSX.  

System akceptuje również dane, które są równoległe na poziomie dokumentu, ale nie są jeszcze wyrównane na poziomie zdania. Jeśli użytkownicy mają dostęp do wersji tej samej zawartości w wielu językach, ale w oddzielnych dokumentach Translator niestandardowy będzie mógł automatycznie dopasowywać zdania między dokumentami.  System może również używać danych jednojęzycznych w jednym lub obu językach w celu uzupełnienia równoległych danych szkoleniowych w celu poprawy tłumaczeń.

Dostosowany system jest następnie dostępny za pośrednictwem regularnego wywołania interfejsu API tekstu usługi Microsoft Translator przy użyciu parametru kategorii.

Biorąc pod uwagę odpowiedni typ i ilość danych szkoleniowych nie jest rzadkością oczekiwać zysków między 5 i 10 lub nawet więcej punktów BLEU na jakość tłumaczenia przy użyciu translatora niestandardowego.

Więcej informacji na temat różnych poziomów dostosowywania na podstawie dostępnych danych można znaleźć w [Podręczniku użytkownika tłumacza niestandardowego](https://aka.ms/CustomTranslatorDocs).


## <a name="microsoft-translator-hub"></a>Centrum translatora firmy Microsoft

> [!NOTE]
> Starsze centrum Microsoft Translator Hub zostanie wycofane 17 maja 2019 r. [Wyświetlanie ważnych informacji o migracji i dat](https://www.microsoft.com/translator/business/hub/).  

## <a name="custom-translator-versus-hub"></a>Tłumacz niestandardowy a koncentrator

|   | **Koncentratora** | **Custom Translator**|
|:-----|:----:|:----:|
|Stan funkcji dostosowywania   | Ogólna dostępność  | Ogólna dostępność |
| Wersja interfejsu API tekstu  | Tylko V2   | Tylko V3 |
| Dostosowywanie SMT | Tak   | Nie |
| Dostosowywanie NMT | Nie    | Tak |
| Nowe ujednolicone dostosowanie usług mowy | Nie    | Tak |
| [Brak śladu](https://www.aka.ms/notrace) | Tak  | Tak |

## <a name="collaborative-translations-framework"></a>Ramy tłumaczeń współpracy

> [!NOTE]
> Od 1 lutego 2018 r. AddTranslation() i AddTranslationArray() nie są już dostępne do użycia z interfejsem API tekstu translatora v2.0. Te metody nie powiedzie się i nic nie zostanie napisane. Interfejs API tekstu tłumacza V3.0 nie obsługuje tych metod.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Konfigurowanie dostosowanego systemu językowego przy użyciu usługi Custom Translator](https://aka.ms/CustomTranslatorDocs)
