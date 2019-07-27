---
title: Często zadawane pytania — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące interfejs API przetwarzania obrazów w usłudze Azure Cognitive Services.
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
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564600"
---
# <a name="computer-vision-api-frequently-asked-questions"></a>interfejs API przetwarzania obrazów często zadawane pytania

> [!TIP]
> Jeśli nie możesz znaleźć odpowiedzi na pytania w tym często zadawane pytania, spróbuj zadać interfejs API przetwarzania obrazów społeczność [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) lub skontaktuj się z pomocą [techniczną w witrynie UserVoice](https://cognitive.uservoice.com/)

---

**Pytanie**: *Czy mogę wyszkolić interfejs API przetwarzania obrazów, aby używać tagów niestandardowych?  Na przykład chcę uzyskać strumieniowe źródło danych z zdjęć Cat do "uczenia" AI, a następnie odebrać wartość rasy w żądaniu AI.*

**Odpowiedź**: Ta funkcja jest obecnie niedostępna. Jednak nasze inżynierowie pracują nad tym, aby skorzystać z tej funkcji przetwarzanie obrazów.

---

**Pytanie**: *Czy przetwarzanie obrazów można używać lokalnie bez połączenia z Internetem?*

**Odpowiedź**: Obecnie nie oferujemy rozwiązania lokalnego lub lokalnego.

---

**Pytanie**: *Czy przetwarzanie obrazów można używać do odczytywania tablic licencji?*

**Odpowiedź**: Interfejs API przetwarzania wizji oferuje dobre wykrywanie tekstu przy użyciu OCR, ale nie jest obecnie zoptymalizowany pod kątem tablic licencji. Ciągle próbujemy ulepszyć nasze usługi i dodałeś OCR do rozpoznawania płyt z licencją Autotekstu do naszej listy żądań funkcji.

---

**Pytanie**: *Jakie typy powierzchni pisania są obsługiwane na potrzeby rozpoznawania pisma ręcznego?*

**Odpowiedź**: Technologia współpracuje z różnymi rodzajami powierzchni, takimi jak tablice, oficjalny dokument i żółte notatki.

---

**Pytanie**: *Jak długo trwa operacja rozpoznawania pisma ręcznego?*

**Odpowiedź**: Ilość czasu, jaką zajmie, zależy od długości tekstu. W przypadku dłuższych tekstów może upłynąć do kilku sekund. W związku z tym po zakończeniu operacji rozpoznawania tekstu napisanego ręcznie może być konieczne poczekanie przed pobraniem wyników przy użyciu operacji pobierania tekstu odręcznego.

---

**Pytanie**: *Jak technologia rozpoznawania pisma ręcznego obsługuje tekst, który został wstawiony przy użyciu karetki w środku linii?*

**Odpowiedź**: Ten tekst jest zwracany jako osobny wiersz przez operację rozpoznawania pisma ręcznego.

---

**Pytanie**: *W jaki sposób technologia rozpoznawania pisma ręcznego zawiera przekroczenie słów lub wierszy?*

**Odpowiedź**: Jeśli słowa są przekreślone z wieloma wierszami w celu renderowania ich nierozpoznawalności, operacja rozpoznawania pisma ręcznego nie zostanie wybrana. Jeśli jednak słowa są przekreślone przy użyciu jednego wiersza, przekroczenia są traktowane jako szum, a wyrazy nadal są pobierane przez operację rozpoznawania pisma ręcznego.

---

**Pytanie**: *Jakie orientacje tekstu są obsługiwane przez technologię rozpoznawania pisma ręcznego?*

**Odpowiedź**: Tekst zorientowany na wartości o wysokości do około 30 stopni do 40 stopni może być pobierany przez operację rozpoznawania pisma ręcznego.

---
