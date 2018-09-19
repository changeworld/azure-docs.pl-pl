---
title: Często zadawane pytania — interfejs API rozpoznawania twarzy
titlesuffix: Azure Cognitive Services
description: Poniżej przedstawiono odpowiedzi na najpopularniejsze pytania dotyczące usługi interfejsu API rozpoznawania twarzy.
services: cognitive-services
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: conceptual
ms.date: 01/26/2017
ms.author: sbowles
ms.openlocfilehash: 9b30fa0fbbd655c03800dadb19cc2568d404204d
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2018
ms.locfileid: "46129560"
---
# <a name="face-api-frequently-asked-questions"></a>Interfejs API rozpoznawania twarzy — często zadawane pytania

### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-face-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Jeśli nie możesz znaleźć odpowiedzi na pytania w tym artykule, spróbuj pytanie społeczności usługi interfejsu API rozpoznawania twarzy na [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) lub skontaktuj się z Pomoc i obsługa techniczna na [UserVoice](https://cognitive.uservoice.com/).

-----
**Pytanie**: jakie czynniki może zmniejszyć dokładność API rozpoznawania twarzy dla rozpoznawania, weryfikacji i Znajdź podobne?

**Odpowiedź**: ogólnie to te same przypadki, gdzie ludzie mają trudności z identyfikowania osoby w tym;
* Blokowanie oczy jeden lub oba przeszkód
* Podświetlenie ostrym, oświetlenia, np. poważny
* Zmiany do włosów włosów stylu lub twarzy
* Zmiany ze względu na wiek
* Extreme twarzy (np. Piski)

Interfejs API rozpoznawania twarzy często zakończy się w takich przypadkach wyzwanie, ale można zmniejszyć dokładność. Aby wprowadzić bardziej niezawodne rozpoznawanie i rozwiązać te problemy, szkolenie osobach ze zdjęciami, które zawierają różnorodność kąty i oświetlenia.

-----
**Pytanie**: am I przekazywanie danych obrazów binarnych w, ale pojawia się błąd "nieprawidłowy twarzy image".

**Odpowiedź**: oznacza to, że algorytm miał wystąpił problem podczas analizowania obrazu. Przyczyny:
* Formaty obsługiwane obrazu wejściowego obejmuje JPEG, PNG, GIF (pierwsza ramka), BMP.
* Rozmiar pliku obrazu nie powinien być większy niż 4MB
* Zakres rozmiaru wykrywalny twarzy to 36 x 36 do 4096 x 4096 pikseli. Twarze poza tym zakresem nie zostanie wykryty.
* Niektóre twarzy mogą nie zostać wykryte z powodu techniczne wyzwania, np. w przypadku bardzo dużych powierzchni kąty (poza główne), duże zamknięcia. Czołowa i niemal czołowego twarzy mają najlepszych wyników

-----
