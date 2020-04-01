---
ms.openlocfilehash: 7b9954930c668e338cc289e948a5591c09ec2654
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76694516"
---
## <a name="provide-feedback-to-the-user"></a>Przekaż opinię użytkownikowi

Można napisać kod do obsługi zdarzenia zaktualizowanej sesji. To zdarzenie jest uruchamiane za każdym razem, gdy sesja poprawia jego zrozumienie otoczenia. W ten sposób można:

- Użyj `UserFeedback` klasy, aby przekazać opinię do użytkownika, jak urządzenie porusza się i sesji aktualizuje jego zrozumienia środowiska. W tym celu
- Określ, w jakim momencie jest wystarczająco dużo śledzonych danych przestrzennych, aby utworzyć kotwice przestrzenne. Można to ustalić `ReadyForCreateProgress` za `RecommendedForCreateProgress`pomocą jednego lub . Gdy `ReadyForCreateProgress` jest powyżej 1, mamy wystarczająco dużo danych, aby zapisać `RecommendedForCreateProgress` kotwicę przestrzenną chmury, choć zalecamy poczekać, aż jest powyżej 1, aby to zrobić.
