---
title: Często zadawane pytania — interfejs API rozpoznawania twarzy
titlesuffix: Azure Cognitive Services
description: Poniżej przedstawiono odpowiedzi na najpopularniejsze pytania dotyczące usługi interfejsu API rozpoznawania twarzy.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 01/26/2017
ms.author: sbowles
ms.openlocfilehash: 47ce80e1b0cefc01752d2445b751ebe1c2d65d08
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351340"
---
# <a name="face-api-frequently-asked-questions"></a>Interfejs API rozpoznawania twarzy — często zadawane pytania

> [!TIP]
> Jeśli nie możesz znaleźć odpowiedzi na pytania w tym artykule, spróbuj pytanie społeczności usługi interfejsu API rozpoznawania twarzy na [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) lub skontaktuj się z Pomoc i obsługa techniczna na [UserVoice](https://cognitive.uservoice.com/).

-----
**Pytanie**: Jakie czynniki może zmniejszyć dokładność API rozpoznawania twarzy dla rozpoznawania, weryfikacji i Znajdź podobne?

**Odpowiedź**: Ogólnie rzecz biorąc jest te same przypadki, gdzie ludzie mają trudności z identyfikowania osoby w tym:
* Blokowanie oczy jeden lub oba przeszkód
* Oświetlenie ostrym (na przykład poważny Podświetlenie)
* Zmiany do włosów włosów stylu lub twarzy
* Zmiany ze względu na wiek
* Extreme twarzy (na przykład Piski)

Interfejs API rozpoznawania twarzy często zakończy się w przypadku trudne jak wyżej, ale można zmniejszyć dokładność. Aby wprowadzić bardziej niezawodne rozpoznawanie i rozwiązać te problemy, szkolenie osobach ze zdjęciami, które zawierają różnorodność kąty i oświetlenia.

-----
**Pytanie**:  Próbuję jakoś dane binarny w, ale pojawia się błąd "nieprawidłowy twarzy image".

**Odpowiedź**:  Ten błąd oznacza, że algorytm miał wystąpił problem podczas analizowania obrazu. Przyczyny:
* Formaty obsługiwane obrazu wejściowego obejmuje JPEG, PNG, GIF (pierwsza ramka), BMP.
* Rozmiar pliku obrazu nie powinien być większy niż 4 MB
* Zakres rozmiaru wykrywalny twarzy to 36 x 36 do 4096 x 4096 pikseli. Nie można wykryć twarzy poza tym zakresem
* Niektóre twarzy mogą nie zostać wykryte z powodu techniczne wyzwania, na przykład dużych powierzchni kąty (head ułożenia), duże zamknięcia. Czołowa i niemal czołowego twarzy mają najlepszych wyników

-----
