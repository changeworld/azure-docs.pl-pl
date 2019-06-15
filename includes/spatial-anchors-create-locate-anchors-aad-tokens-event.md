---
ms.openlocfilehash: 7219a457a2631f9ff6beee06eff34bce0ff5a23f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66110688"
---
Podobnie jak przy użyciu tokenów dostępu, jeśli nie ustawiono tokenu usługi Azure AD, musisz obsługi zdarzenia TokenRequired lub zaimplementować metodę tokenRequired na protokole delegata.

Obsługuj zdarzenie synchronicznie, ustawiając właściwość dla argumentów zdarzeń.
