---
title: Często zadawane pytania dotyczące wizji komputera interfejsu API | Dokumentacja firmy Microsoft
description: Odpowiedzi na często zadawane pytania dotyczące interfejsu API przetwarzania obrazów komputera w usługach kognitywnych firmy Microsoft.
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 01/26/2017
ms.author: kefre
ms.openlocfilehash: 5c862dd2fb26a005f4e785673a4e9358ecf9286f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346980"
---
# <a name="computer-vision-api-frequently-asked-questions"></a>Wizja komputera interfejsu API często zadawane pytania
### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-computer-vision-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Jeśli nie możesz znaleźć odpowiedzi na pytania w tych często zadawanych PYTAŃ, spróbuj pytaniem społeczności API przetwarzania obrazów komputera na [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) lub skontaktuj się z [Pomoc i obsługa techniczna na UserVoice](https://cognitive.uservoice.com/)

-----

**Pytanie**: *komputera wizji API do używania niestandardowych tagów można uczenia?  Na przykład chcę źródła danych w obrazach RAS cat uczenia AI, a następnie wartość rasy na żądanie AI.*

**Odpowiedzi**: Ta funkcja jest obecnie niedostępna. Jednak naszych inżynierów pracy można wyświetlić tej funkcji na komputerze wizji.

-----

**Pytanie**: *wizji komputer może być używane lokalnie bez połączenia z Internetem?*

**Odpowiedzi**: obecnie nie oferujemy lokalnymi lub rozwiązania lokalnego.

-----

**Pytanie**: *języki są obsługiwane z wizji komputera?*

**Odpowiedzi**: obsługiwane języki:

| | | Obsługiwane języki | | |
|---------------- |------------------ |------------------ |--------------------------- |--------------------
| Duński (da k)  | Niderlandzki (nl-NL)     | Polski           | Fiński (fi-FI)            |Francuski (fr-FR)
| Niemiecki (de-DE)  | Grecki (el-GR)     | Węgierski (hu-HU) | Włoski (it-IT)            | Japoński (ja-JP)
| Koreański (ko-KR)  | Norweski (nb-NO) | Polski (pl-PL)    | Portugalski (pt-BR) (pt-PT) | Rosyjski (ru-RU)
| Hiszpański (es-ES)   | Szwedzki (sv SV)     | Turecki (tr TU)   |                            |

-----

**Pytanie**: *wizji komputer może być używany do odczytu rejestracyjnych?*

**Odpowiedzi**: API wizji zapewnia dobry wykrywanie tekstu z Rozpoznawania, ale nie jest obecnie zoptymalizowana dla rejestracyjnych. Firma Microsoft stale próbuje poprawy naszych usług i dodano Rozpoznawania automatycznego rozpoznawania płytkę licencji do naszej listy żądania funkcji.

-----

**Pytanie:** *języki są obsługiwane w przypadku rozpoznawania pisma ręcznego?*

**Odpowiedzi**: obecnie jest obsługiwana tylko w języku angielskim.

-----

**Pytanie**: *rodzaje zapisywania powierzchni są obsługiwane w przypadku rozpoznawania pisma ręcznego?*

**Odpowiedzi**: technologii współpracuje z różnych rodzajów powierzchni, w tym tablic, oficjalny dokument i żółty notatki.

-----

**Pytanie**: *czas operacji rozpoznawania pisma ręcznego podąża?*

**Odpowiedzi**: czas potrzebny jest zależna od długość tekstu. Dla dłuższych tekstów może potrwać kilka sekund. W związku z tym po zakończeniu operacji Rozpoznaj tekst odręcznie, może być konieczne oczekiwania, zanim będzie można pobrać wyników przy użyciu operacji Get odręcznie wynik operacji tekstu.

-----

**Pytanie**: *jak tekst dojścia technologii rozpoznawania pisma ręcznego, wstawione na karetkę pośrodku wiersza?*

**Odpowiedzi**: takiego tekstu jest zwracana jako osobny wiersz przez operację rozpoznawania pisma ręcznego.

-----

**Pytanie**: *jak technologię rozpoznawania pisma ręcznego obsługuje przekreślonym słowa lub wierszy?*

**Odpowiedzi**: Jeśli wyrazy są przekroczyła z wielu wierszy do renderowania ich nierozpoznawalną, operacja rozpoznawania pisma ręcznego nie zachowuje je. Jednak jeśli wyrazy są przekroczyła przy użyciu jednego wiersza, że przecięcia jest traktowany jako szumu i wyrazów wciąż uzyskać pobrana przez operacji rozpoznawania pisma ręcznego.

-----

**Pytanie**: *orientacji tekstu, jakie są obsługiwane w przypadku technologii rozpoznawania pisma ręcznego?*

**Odpowiedzi**: tekst ukierunkowane pod kątem programu do około 30 stopni do 40 stopni może pobrać pobrana przez operację rozpoznawania pisma ręcznego.

-----
