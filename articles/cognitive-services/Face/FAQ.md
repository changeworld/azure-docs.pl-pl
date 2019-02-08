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
ms.openlocfilehash: ceaffebde65402db385ff8b906d77c2ddadc6f97
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55860954"
---
# <a name="face-api-frequently-asked-questions"></a>Interfejs API rozpoznawania twarzy — często zadawane pytania

### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-face-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Jeśli nie możesz znaleźć odpowiedzi na pytania w tym artykule, spróbuj pytanie społeczności usługi interfejsu API rozpoznawania twarzy na [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) lub skontaktuj się z Pomoc i obsługa techniczna na [UserVoice](https://cognitive.uservoice.com/).

-----
**Pytanie**: Jakie czynniki może zmniejszyć dokładność API rozpoznawania twarzy dla rozpoznawania, weryfikacji i Znajdź podobne?

**Odpowiedź**: Ogólnie to te same przypadki, gdzie ludzie mają trudności z identyfikowania osoby w tym;
* Blokowanie oczy jeden lub oba przeszkód
* Podświetlenie ostrym, oświetlenia, np. poważny
* Zmiany do włosów włosów stylu lub twarzy
* Zmiany ze względu na wiek
* Extreme twarzy (np. Piski)

Interfejs API rozpoznawania twarzy często zakończy się w takich przypadkach wyzwanie, ale można zmniejszyć dokładność. Aby wprowadzić bardziej niezawodne rozpoznawanie i rozwiązać te problemy, szkolenie osobach ze zdjęciami, które zawierają różnorodność kąty i oświetlenia.

-----
**Pytanie**:  Próbuję jakoś dane binarny w, ale pojawia się błąd "nieprawidłowy twarzy image".

**Odpowiedź**:  Oznacza to, że algorytm miał wystąpił problem podczas analizowania obrazu. Przyczyny:
* Formaty obsługiwane obrazu wejściowego obejmuje JPEG, PNG, GIF (pierwsza ramka), BMP.
* Rozmiar pliku obrazu nie powinien być większy niż 4MB
* Zakres rozmiaru wykrywalny twarzy to 36 x 36 do 4096 x 4096 pikseli. Twarze poza tym zakresem nie zostanie wykryty.
* Niektóre twarzy mogą nie zostać wykryte z powodu techniczne wyzwania, np. w przypadku bardzo dużych powierzchni kąty (poza główne), duże zamknięcia. Czołowa i niemal czołowego twarzy mają najlepszych wyników

-----
