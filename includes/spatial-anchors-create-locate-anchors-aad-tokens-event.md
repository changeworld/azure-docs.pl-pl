---
ms.openlocfilehash: 7219a457a2631f9ff6beee06eff34bce0ff5a23f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60232634"
---
Podobnie jak przy użyciu tokenów dostępu, jeśli nie ustawiono tokenu usługi Azure AD, musisz obsługi zdarzenia TokenRequired lub zaimplementować metodę tokenRequired na protokole delegata.

Obsługuj zdarzenie synchronicznie, ustawiając właściwość dla argumentów zdarzeń.
