---
title: Często zadawane pytania dotyczące usługi interfejsu API krój | Dokumentacja firmy Microsoft
description: Poniżej przedstawiono odpowiedzi na najpopularniejsze pytania dotyczące usługi interfejsu API twarzy na obrazie.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 01/26/2017
ms.author: sbowles
ms.openlocfilehash: da2f75deef8a8beea3ba23b6a39eb6d2fe104b54
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346989"
---
# <a name="face-api-frequently-asked-questions"></a>Powierzchni interfejsu API często zadawane pytania
### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-face-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Jeśli nie możesz znaleźć odpowiedzi na pytania w tych często zadawanych PYTAŃ, spróbuj pytaniem społeczności powierzchni interfejsu API na [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) lub skontaktuj się z pomocy i obsługi technicznej na [UserVoice](https://cognitive.uservoice.com/).

-----
**Pytanie**: jakie czynniki mogą zmniejszyć dokładność powierzchni interfejsu API dla rozpoznawania, weryfikacji i Znajdź podobne?

**Odpowiedzi**: najczęściej jest tej samej przypadki, w którym ludzi mieć trudności w identyfikacji osoby w tym;
* Przeszkód zablokowanie oczu jeden lub oba
* Podświetlenie ostrym oświetlenia, np. poważny
* Zmiany włosów stylu lub Maseczka włosów
* Zmian z powodu wieku
* Extreme twarzy (np. Piski)

Powierzchnia interfejsu API jest często powiodło się. w takich przypadkach trudne, ale można zmniejszyć dokładności. Aby wprowadzić rozpoznawania bardziej niezawodne i rozwiązać te problemy, uczenia osobach fotografii, obejmujących różnorodności kąty i oświetlenia.

-----
**Pytanie**: am I przekazywanie danych binarnych obrazu w, ale występuje błąd "nieprawidłowy krój obrazu".

**Odpowiedzi**: oznacza to, że algorytm wystąpiły problemy podczas analizowania obrazu. Dostępne są następujące przyczyny:
* Formaty obrazu wejściowego obsługiwanych obejmuje JPEG, PNG, GIF (pierwszej ramki), BMP.
* Rozmiar pliku obrazu nie powinna być większa niż 4MB
* Zakres rozmiaru wykrywalny krój to 36 x 36 do 4096 x 4096 pikseli. Nie są wykrywane kroje poza tym zakresem.
* Niektóre kroje mogą nie być wykrywane ze względu na problemy techniczne, np. w przypadku bardzo dużych kąty przedniej (ułożenia head), duża okluzji. Kroje czołowego i niemal czołowego ma uzyskania najlepszych wyników

-----
