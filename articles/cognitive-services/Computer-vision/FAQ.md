---
title: Często zadawane pytania dotyczące interfejsu API przetwarzania obrazów
titlesuffix: Azure Cognitive Services
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące interfejsu API przetwarzania obrazów w usługach Azure Cognitive Services.
services: cognitive-services
author: KellyDF
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 01/26/2017
ms.author: kefre
ms.openlocfilehash: 55b474d0edbb8dc01b9f35d4f8799e53e37862df
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2018
ms.locfileid: "49166376"
---
# <a name="computer-vision-api-frequently-asked-questions"></a>Interfejs API przetwarzania obrazów — często zadawane pytania

### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-computer-vision-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Jeśli nie możesz znaleźć odpowiedzi na pytania w tym — często zadawane pytania, zadaj społeczności interfejs API przetwarzania obrazów na [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) lub skontaktuj się z [Pomoc i obsługa techniczna w usłudze UserVoice](https://cognitive.uservoice.com/)

-----

**Pytanie**: *mogą uczyć się za pomocą tagów niestandardowych, interfejs API przetwarzania obrazów?  Na przykład chcę otrzymywać kanału informacyjnego w obrazach RAS cat szkolenie sztucznej Inteligencji, a następnie wartość na rynku emulatorowi w żądaniu sztucznej Inteligencji.*

**Odpowiedź**: Ta funkcja jest obecnie niedostępna. Jednak nasi inżynierowie zajmujący się nad przenieść tej funkcji do przetwarzania obrazów.

-----

**Pytanie**: *przetwarzania obrazów może być używany lokalnie, bez połączenia z Internetem?*

**Odpowiedź**: obecnie nie oferujemy lokalną lub rozwiązania lokalnego.

-----

**Pytanie**: *które języki są obsługiwane przy użyciu przetwarzania obrazów?*

**Odpowiedź**: obsługiwane języki:

| | | Obsługiwane języki | | |
|---------------- |------------------ |------------------ |--------------------------- |--------------------
| Duński (da k)  | Holenderski (nl-NL)     | Polski           | Fiński (fi-FI)            |Francuski (fr-FR)
| Niemiecki (de-DE)  | Grecki (el-GR)     | Węgierski (hu-HU) | Włoski (it-IT)            | Japoński (ja-JP)
| Koreański (ko-KR)  | Norweski (nb nie) | Polski (pl-PL)    | Portugalski (pt-BR) (pt-PT) | Rosyjski (ru-RU)
| Hiszpański (es-ES)   | Szwedzki (sv SV)     | Turecki (tr-TR)   |                            |

-----

**Pytanie**: *przetwarzania obrazów może być użyte do odczytu talerzy licencji?*

**Odpowiedź**: interfejs API przetwarzania zapewnia dobre wykrywania tekstu za pomocą oprogramowania OCR, ale nie jest obecnie zoptymalizowany dla rejestracyjnych. Firma Microsoft stale dążymy do poprawy naszych usług i dodaliśmy optyczne rozpoznawanie znaków rozpoznawania płytkę licencji automatycznie do naszej listy żądania funkcji.

-----

**Pytanie:** *które języki są obsługiwane w przypadku rozpoznawania pisma ręcznego?*

**Odpowiedź**: aktualnie obsługiwana jest tylko język angielski.

-----

**Pytanie**: *jakiego rodzaju pisania powierzchni są obsługiwane w przypadku rozpoznawania pisma ręcznego?*

**Odpowiedź**: technologia działa z różnymi rodzajami powierzchnie, w tym tablic, oficjalny dokument i żółty notatki.

-----

**Pytanie**: *jak operacja rozpoznawania pisma ręcznego długo?*

**Odpowiedź**: ilość czasu, potrzebny jest zależna od długości tekstu. Dla dłuższych tekstów może potrwać kilka sekund. W związku z tym po zakończeniu operacji rozpoznawanie tekstu odręcznego, konieczne może być oczekiwania, zanim będzie można pobrać wyniki za pomocą operacji Pobierz odręcznego wynik operacji tekstu.

-----

**Pytanie**: *jakie tekst uchwyt technologii rozpoznawania pisma odręcznego, który został wstawiony przy użyciu daszek pośrodku linii?*

**Odpowiedź**: takiego tekstu jest zwracana jako oddzielnych wierszach przez operację rozpoznawania pisma ręcznego.

-----

**Pytanie**: *jak technologii rozpoznawania pisma ręcznego obsługuje przekreślonym słowa lub wierszy?*

**Odpowiedź**: Jeśli słowa zostanie przekroczony z wieloma wierszami do renderowania ich nierozpoznawalną, operacja rozpoznawania pisma ręcznego nie je pobrać. Jednak jeśli słowa zostanie przekroczony przy użyciu pojedynczego wiersza, tego przejścia, jest traktowany jako hałasu i wyrazy nadal uzyskać odebrane przez operację rozpoznawania pisma ręcznego.

-----

**Pytanie**: *orientacji tekstu, jakie są obsługiwane w przypadku technologii rozpoznawania pisma ręcznego?*

**Odpowiedź**: tekst o orientacji pod kątem programu do około 30 stopni na stopnie 40 Pobierz odebrane przez operację rozpoznawania pisma ręcznego.

-----
