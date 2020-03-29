---
title: Często zadawane pytania - Vision komputerowy
titleSuffix: Azure Cognitive Services
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące interfejsu API przetwarzania obrazów w usługach Azure Cognitive Services.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: e8ab205a43e5cb1e8e2b96dbd9600e1fceb29403
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68564600"
---
# <a name="computer-vision-api-frequently-asked-questions"></a>Interfejs API wizji komputerowej Często zadawane pytania

> [!TIP]
> Jeśli nie możesz znaleźć odpowiedzi na swoje pytania w tym faq, spróbuj zadać społeczności interfejsu API przetwarzania obrazów komputerowych w [usłudze StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) lub skontaktuj się z [pomocą i pomocą techniczną w usłudze UserVoice](https://cognitive.uservoice.com/)

---

**Pytanie:** *Czy mogę trenować interfejs API przetwarzania danych w celu używania tagów niestandardowych?  Na przykład, chciałbym karmić na zdjęciach ras kotów do "pociągu" AI, a następnie otrzymać wartość rasy na żądanie AI.*

**Odpowiedź**: Ta funkcja jest obecnie niedostępna. Jednak nasi inżynierowie pracują nad wprowadzeniem tej funkcji do funkcji Computer Vision.

---

**Pytanie**: *Czy computer vision może być używany lokalnie bez połączenia z Internetem?*

**Odpowiedź**: Obecnie nie oferujemy rozwiązania lokalnego ani lokalnego.

---

**Pytanie**: *Czy computer vision może być używany do odczytu tablic rejestracyjnych?*

**Odpowiedź**: Interfejs API Vision zapewnia dobre wykrywanie tekstu za pomocą OCR, ale obecnie nie jest zoptymalizowany pod kątem tablic rejestracyjnych. Nieustannie staramy się ulepszać nasze usługi i dodaliśmy OCR do automatycznego rozpoznawania tablic rejestracyjnych do naszej listy żądań funkcji.

---

**Pytanie:** *Jakie typy powierzchni do pisania są obsługiwane w celu rozpoznawania pisma ręcznego?*

**Odpowiedź**: Technologia działa z różnymi rodzajami powierzchni, w tym tablicami, białym papierem i żółtymi kartami samoprzylepnych.

---

**Pytanie:** *Jak długo trwa operacja rozpoznawania pisma ręcznego?*

**Odpowiedź**: Czas potrzebny zależy od długości tekstu. W przypadku dłuższych tekstów może to potrwać nawet kilka sekund. W związku z tym po zakończeniu operacji Rozpoznaj tekst odręczny może być konieczne zaczekanie, zanim będzie można pobrać wyniki przy użyciu operacji Pobierz wynik operacji odręcznego tekstu.

---

**Pytanie:** *W jaki sposób technologia rozpoznawania pisma ręcznego obsługuje tekst wstawiony przy użyciu cieszy w środku wiersza?*

**Odpowiedź:** Taki tekst jest zwracany jako osobny wiersz za pomocą operacji rozpoznawania pisma ręcznego.

---

**Pytanie:** *W jaki sposób technologia rozpoznawania pisma ręcznego obsługuje przekreślone słowa lub linie?*

**Odpowiedź:** Jeśli wyrazy zostaną przekreślone wieloma wierszami, aby uczynić je nierozpoznawalnymi, operacja rozpoznawania pisma ręcznego nie powoduje ich odebrania. Jeśli jednak wyrazy zostaną przekreślone za pomocą jednej linii, to przejście jest traktowane jako hałas, a słowa nadal są odbierane przez operację rozpoznawania pisma ręcznego.

---

**Pytanie:** *Jakie orientacje tekstu są obsługiwane dla technologii rozpoznawania pisma ręcznego?*

**Odpowiedź**: Tekst zorientowany pod kątem do około 30 stopni do 40 stopni może zostać odebrany przez operację rozpoznawania pisma ręcznego.

---
