---
ms.openlocfilehash: 7219a457a2631f9ff6beee06eff34bce0ff5a23f
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632356"
---
Podobnie jak przy użyciu tokenów dostępu, jeśli nie ustawiono tokenu usługi Azure AD, musisz obsługi zdarzenia TokenRequired lub zaimplementować metodę tokenRequired na protokole delegata.

Obsługuj zdarzenie synchronicznie, ustawiając właściwość dla argumentów zdarzeń.
