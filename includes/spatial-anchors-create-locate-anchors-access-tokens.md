---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 0e4c0b6d622fe0d8d20692ec6c0c889779e4601a
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753106"
---
### <a name="access-tokens"></a>Tokeny dostępu

Tokeny dostępu są bardziej niezawodne metody uwierzytelniania w usłudze Azure przestrzenne kotwic. Szczególnie, podczas przygotowywania aplikacji na potrzeby wdrożenia produkcyjnego. Podsumowanie tego podejścia jest do skonfigurowania usługi zaplecza, który aplikacja kliencka może bezpiecznego uwierzytelniania za pomocą. Twoje interfejsy usługi zaplecza przy użyciu usługi AAD w czasie wykonywania, a także z usługą Azure przestrzenne kotwic STS do żądania tokenu dostępu. Token ten jest następnie dostarczane do aplikacji klienckiej i używane w zestawie SDK do uwierzytelniania w usłudze Azure przestrzenne kotwic.
