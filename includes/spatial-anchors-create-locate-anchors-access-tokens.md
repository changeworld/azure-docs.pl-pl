---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 63725d55e2b2935ec6a899789249259b096865c3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "67182926"
---
### <a name="access-tokens"></a>Tokeny dostępu

Tokeny dostępu są bardziej niezawodną metodą uwierzytelniania za pomocą zakotwiczeń przestrzennych platformy Azure. Zwłaszcza podczas przygotowywania aplikacji do wdrożenia produkcyjnego. Podsumowanie tego podejścia jest skonfigurowanie usługi zaplecza, które aplikacja kliencka może bezpiecznie uwierzytelnić. Interfejsy usługi zaplecza z usługą AAD w czasie wykonywania i z usługą Azure Spatial Anchors Secure Token Service w celu żądania tokenu dostępu. Ten token jest następnie dostarczany do aplikacji klienckiej i używany w SDK do uwierzytelniania za pomocą zakotwiczeń przestrzennych platformy Azure.
