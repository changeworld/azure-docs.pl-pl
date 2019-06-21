---
ms.openlocfilehash: 7219a457a2631f9ff6beee06eff34bce0ff5a23f
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182933"
---
Podobnie jak przy użyciu tokenów dostępu, jeśli nie ustawiono tokenu usługi Azure AD, musisz obsługi zdarzenia TokenRequired lub zaimplementować metodę tokenRequired na protokole delegata.

Obsługuj zdarzenie synchronicznie, ustawiając właściwość dla argumentów zdarzeń.
