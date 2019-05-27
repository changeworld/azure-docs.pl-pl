---
ms.openlocfilehash: 7219a457a2631f9ff6beee06eff34bce0ff5a23f
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66110688"
---
Podobnie jak przy użyciu tokenów dostępu, jeśli nie ustawiono tokenu usługi Azure AD, musisz obsługi zdarzenia TokenRequired lub zaimplementować metodę tokenRequired na protokole delegata.

Obsługuj zdarzenie synchronicznie, ustawiając właściwość dla argumentów zdarzeń.
