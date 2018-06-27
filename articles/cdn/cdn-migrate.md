---
title: Migracji profilu usługi Azure CDN z Verizon Standard do Verizon Premium | Dokumentacja firmy Microsoft
description: Poznaj szczegółowe informacje o migracji profilu ze standardowego Verizon Verizon Premium.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: v-deasim
ms.custom: ''
ms.openlocfilehash: 074dbb094e7ae2cd1f1719016928bd7348da3949
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2018
ms.locfileid: "36958059"
---
# <a name="migrate-an-azure-cdn-profile-from-standard-verizon-to-premium-verizon"></a>Migracja profilu usługi Azure CDN from Verizon standardowa do Premium Verizon

Podczas tworzenia profilu Azure sieci dostarczania zawartości (CDN), aby zarządzać punktami końcowymi usługi Azure CDN oferuje cztery różne produkty, które można wybrać. Aby uzyskać informacje o różnych produktów i ich dostępnych funkcji, zobacz [funkcji produktu porównanie usługi Azure CDN](cdn-features.md).

Jeśli po utworzeniu **Azure CDN Standard from Verizon** profilu i jest ona używana do zarządzania punktami końcowymi CDN masz opcję, aby uaktualnić go do **Azure CDN Premium from Verizon** profilu. Po uaktualnieniu, punktami końcowymi CDN i wszystkie dane zostaną zachowane. 

> [!IMPORTANT]
> Po uaktualnieniu do **Azure CDN Premium from Verizon** profilu, nie można później przekonwertować go do **Azure CDN Standard from Verizon** profilu.
> 

Aby uaktualnić **Azure CDN Standard from Verizon** profilu, skontaktuj się z pomocą [Microsoft Support](https://azure.microsoft.com/support/options/).

## <a name="profile-comparison"></a>Porównanie profilu
**Azure CDN Premium from Verizon** profile zawierają następujące różnice klucza **Azure CDN Standard from Verizon** profile:
- Dla niektórych Azure CDN funkcje, takie jak [kompresji](cdn-improve-performance.md), [buforowanie reguły](cdn-caching-rules.md), i [filtrowania geograficznie](cdn-restrict-access-by-country.md), nie można użyć interfejsu usługi Azure CDN, musisz użyć portalu Verizon za pomocą **Zarządzaj** przycisku.
- Interfejs API: W odróżnieniu od z Verizon standardowe, nie można użyć interfejsu API do kontrolowania tych funkcji, które są dostępne z portalu Premium Verizon. Jednak można użyć interfejsu API do kontrolowania wspólne funkcje, takie jak tworzenie/usuwanie punktu końcowego, przeczyszczanie/ładowania zasobów pamięci podręcznej i włączenie/wyłączenie domeny niestandardowej.
- Cennik: Premium Verizon ma inną strukturę cen transferów danych niż standardowe Verizon. Aby uzyskać więcej informacji, zobacz [cennik Content Delivery Network](https://azure.microsoft.com/pricing/details/cdn/).

**Azure CDN Premium from Verizon** profile zawierają następujące dodatkowe funkcje:
- [Token uwierzytelniania](cdn-token-auth.md): pozwala użytkownikom uzyskać i użyj tokenu, aby pobrać bezpiecznych zasobów.
- [Aparat reguł](cdn-rules-engine.md): umożliwia dostosowanie sposób obsługi żądań HTTP.
- Narzędzia zaawansowane metody analizy:
   - [Szczegółowe analizy HTTP](cdn-advanced-http-reports.md)
   - [Analiza wydajności krawędzi](cdn-edge-performance.md)
   - [Analiza w czasie rzeczywistym](cdn-real-time-alerts.md)


## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat aparatu reguł, zobacz [zasady usługi Azure CDN aparat odwołanie](cdn-rules-engine-reference.md).

