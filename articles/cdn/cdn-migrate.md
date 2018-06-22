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
ms.openlocfilehash: 615f35c602f9a086bdc5c32b04a97efa368411a3
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36308129"
---
# <a name="migrate-an-azure-cdn-profile-from-standard-verizon-to-premium-verizon"></a>Migracja profilu usługi Azure CDN from Verizon standardowa do Premium Verizon

Azure sieci dostarczania zawartości (CDN) oferuje cztery różne produkty, z których każdy zawiera różne funkcje: 
- **Usługi Azure CDN Standard firmy Microsoft**
- **Usługi Azure CDN Standard from Akamai**
- **Usługi Azure CDN Standard from Verizon**
- **Azure CDN Premium from Verizon**.

Jeśli używasz **Azure CDN Standard from Verizon** profilu, aby zarządzać punktami końcowymi CDN, masz opcję, aby uaktualnić go do **Azure CDN Premium from Verizon** profilu i zachować wszystkie dane.

Aby uaktualnić **Azure CDN Standard from Verizon** profilu, skontaktuj się z pomocą [Microsoft Support](https://azure.microsoft.com/support/options/).

> [!IMPORTANT]
> Po uaktualnieniu do **Azure CDN Premium from Verizon** profilu, nie można później przekonwertować go do **Azure CDN Standard from Verizon** profilu.
> 

## <a name="profile-comparison"></a>Porównanie profilu
**Azure CDN Premium from Verizon** profile zawierają następujące różnice klucza **Azure CDN Standard from Verizon** profile:
- Dla niektórych Azure CDN funkcje, takie jak [kompresji](cdn-improve-performance.md), buforowanie reguł, i [filtrowania geograficznie](cdn-restrict-access-by-country.md), nie można użyć interfejsu usługi Azure CDN, musisz użyć portalu Verizon za pomocą **Zarządzaj**przycisku.
- Interfejs API: W przeciwieństwie do standardowych Verizon nie można użyć interfejsu API do kontrolowania tych funkcji, które są dostępne z portalu Premium Verizon. Jednak można użyć interfejsu API do kontrolowania wspólne funkcje, takie jak tworzenie/usuwanie punktu końcowego, przeczyszczanie/ładowania zasobów pamięci podręcznej i włączenie/wyłączenie domeny niestandardowej.
- Cennik: Premium Verizon ma inną strukturę cen transferów danych niż standardowe Verizon. Aby uzyskać więcej informacji, zobacz [cennik Content Delivery Network](https://azure.microsoft.com/pricing/details/cdn/).

**Azure CDN Premium from Verizon** profile zawierają następujące dodatkowe funkcje:
- [Token uwierzytelniania](cdn-token-auth.md): pozwala użytkownikom uzyskać i użyj tokenu, aby pobrać bezpiecznych zasobów.
- [Aparat reguł](cdn-rules-engine.md): umożliwia dostosowanie sposób obsługi żądań HTTP.
- Narzędzia zaawansowane metody analizy:
   - [Szczegółowe analizy HTTP](cdn-advanced-http-reports.md)
   - [Analiza wydajności krawędzi](cdn-edge-performance.md)
   - [Analiza w czasie rzeczywistym](cdn-real-time-alerts.md)


## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać szczegółowe porównanie funkcji produktu Azure CDN, zobacz [funkcji produktu Azure CDN](Compare Azure CDN product features).

Aby dowiedzieć się więcej na temat aparatu reguł, zobacz [zasady usługi Azure CDN aparat odwołanie](cdn-rules-engine-reference.md).

