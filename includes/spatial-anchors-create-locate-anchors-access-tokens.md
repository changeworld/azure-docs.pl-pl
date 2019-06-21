---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 63725d55e2b2935ec6a899789249259b096865c3
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182926"
---
### <a name="access-tokens"></a>Tokeny dostępu

Tokeny dostępu są bardziej niezawodne metody uwierzytelniania w usłudze Azure przestrzenne kotwic. Szczególnie, podczas przygotowywania aplikacji na potrzeby wdrożenia produkcyjnego. Podsumowanie tego podejścia jest do skonfigurowania usługi zaplecza, który aplikacja kliencka może bezpiecznego uwierzytelniania za pomocą. Twoje interfejsy usługi zaplecza przy użyciu usługi AAD w czasie wykonywania i usługą Azure przestrzenne kotwic Secure Token żądania tokenu dostępu. Token ten jest następnie dostarczane do aplikacji klienckiej i używane w zestawie SDK do uwierzytelniania w usłudze Azure przestrzenne kotwic.
