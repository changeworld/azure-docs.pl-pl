---
title: Migrowanie profilu usługi Azure CDN z Verizon — standardowa do warstwy Premium firmy Verizon | Dokumentacja firmy Microsoft
description: Poznaj szczegółowe informacje o migracji profilu z Verizon — standardowa do warstwy Premium firmy Verizon.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: magattus
ms.custom: ''
ms.openlocfilehash: 7768dde424aedc295b53512db50c9dfc9db9ab8c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60635639"
---
# <a name="migrate-an-azure-cdn-profile-from-standard-verizon-to-premium-verizon"></a>Migracja profilu usługi Azure CDN z Verizon — standardowa do Verizon — Premium

Podczas tworzenia profilu usługi Azure Content Delivery Network (CDN), aby zarządzać punktami końcowymi usługi sieć CDN systemu Azure oferuje cztery różnych produktów, które można wybrać. Aby uzyskać informacji na temat różnych produktów i ich dostępnych funkcji, zobacz [funkcji produktu porównanie usługi Azure CDN](cdn-features.md).

Jeśli już utworzono **Azure CDN Standard from Verizon** profilowania i za jej pomocą zarządzać punktami końcowymi usługi CDN, istnieje możliwość uaktualnić go do **Azure CDN Premium from Verizon** profilu. Podczas uaktualniania, punktów końcowych sieci CDN i wszystkie dane zostaną zachowane. 

> [!IMPORTANT]
> Po uaktualnieniu do **Azure CDN Premium from Verizon** profilu, nie można później przekonwertować ją do **Azure CDN Standard from Verizon** profilu.
> 

Aby uaktualnić **Azure CDN Standard from Verizon** profilu, skontaktuj się z pomocą [Microsoft Support](https://azure.microsoft.com/support/options/).

## <a name="profile-comparison"></a>Porównanie profilu
**Usługa Azure CDN Premium from Verizon** profile mają następujące kluczowe różnice względem **Azure CDN Standard from Verizon** profilów:
- W przypadku niektórych wysokiej dostępności treści Azure funkcje, takie jak [kompresji](cdn-improve-performance.md), [reguły buforowania](cdn-caching-rules.md), i [filtrowania geograficznego](cdn-restrict-access-by-country.md), nie można użyć interfejsu usługi Azure CDN, należy użyć za pośrednictwem portalu firmy Verizon **Zarządzaj** przycisku.
- API: W odróżnieniu od przypadku standardowa firmy Verizon, nie można użyć interfejsu API do kontrolowania tych funkcji, które są dostępne z poziomu portalu Verizon — Premium. Jednak można użyć interfejsu API do kontrolowania inne typowe funkcje, takie jak tworzenie/usuwanie punktu końcowego, przeczyszczanie/ładowanie zasobów pamięci podręcznej i włączania/wyłączania domeny niestandardowej.
- Cennik: Verizon — Premium ma inną strukturę cennika w przypadku transferów danych niż Verizon — standardowa. Aby uzyskać więcej informacji, zobacz [cennik usługi Content Delivery Network](https://azure.microsoft.com/pricing/details/cdn/).

**Usługa Azure CDN Premium from Verizon** profile mają następujące dodatkowe funkcje:
- [Uwierzytelnianie przy użyciu tokenów](cdn-token-auth.md): Umożliwia użytkownikom uzyskanie i używają tokenu w celu pobrania bezpiecznych zasobów.
- [Aparat reguł](cdn-rules-engine.md): Umożliwia dostosowywanie sposobu obsługi żądań HTTP.
- Zaawansowane narzędzia analityczne:
   - [Szczegółowa analiza HTTP](cdn-advanced-http-reports.md)
   - [Analiza wydajności krańcowej](cdn-edge-performance.md)
   - [Analiza w czasie rzeczywistym](cdn-real-time-alerts.md)


## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat aparatu reguł, zobacz [dokumentacja aparatu reguł wysokiej dostępności treści Azure](cdn-rules-engine-reference.md).

