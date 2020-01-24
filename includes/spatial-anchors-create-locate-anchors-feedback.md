---
ms.openlocfilehash: 7b9954930c668e338cc289e948a5591c09ec2654
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76694516"
---
## <a name="provide-feedback-to-the-user"></a>Prześlij opinię użytkownikowi

Można napisać kod do obsługi zdarzenia zaktualizowanej sesji. To zdarzenie jest wyzwalane za każdym razem, gdy sesja poprawi swoje otoczenie. Dzięki temu można:

- Użyj klasy `UserFeedback`, aby przekazać użytkownikowi informacje zwrotne, gdy urządzenie jest przenoszone, a sesja aktualizuje swoją wiedzę o środowisku. W tym celu
- Określ, w jaki sposób ma być wystarczająco dużo śledzonych danych przestrzennych, aby utworzyć kotwice przestrzenne. Należy określić tę wartość przy użyciu `ReadyForCreateProgress` lub `RecommendedForCreateProgress`. Gdy `ReadyForCreateProgress` powyżej 1, mamy wystarczającą ilość danych, aby zapisać kotwicę przestrzenną w chmurze, ale zalecamy poczekanie do momentu, aż `RecommendedForCreateProgress` do tego celu.
