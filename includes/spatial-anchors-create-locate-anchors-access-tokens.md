---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 63725d55e2b2935ec6a899789249259b096865c3
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66110534"
---
### <a name="access-tokens"></a>Tokeny dostępu

Tokeny dostępu są bardziej niezawodne metody uwierzytelniania w usłudze Azure przestrzenne kotwic. Szczególnie, podczas przygotowywania aplikacji na potrzeby wdrożenia produkcyjnego. Podsumowanie tego podejścia jest do skonfigurowania usługi zaplecza, który aplikacja kliencka może bezpiecznego uwierzytelniania za pomocą. Twoje interfejsy usługi zaplecza przy użyciu usługi AAD w czasie wykonywania i usługą Azure przestrzenne kotwic Secure Token żądania tokenu dostępu. Token ten jest następnie dostarczane do aplikacji klienckiej i używane w zestawie SDK do uwierzytelniania w usłudze Azure przestrzenne kotwic.
