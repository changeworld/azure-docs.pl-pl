---
title: Migrowanie profilu usługi Azure CDN z verizon standard do verizon premium
description: Dowiedz się więcej o szczegółach migracji profilu z Verizon Standard do Verizon Premium.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: 61c472281c64fba451bb9b05f69ebee09bc763fd
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260460"
---
# <a name="migrate-an-azure-cdn-profile-from-standard-verizon-to-premium-verizon"></a>Migrowanie profilu usługi Azure CDN ze standardu Verizon do premium Verizon

Podczas tworzenia profilu usługi Azure Content Delivery Network (CDN) do zarządzania punktami końcowymi usługa Azure CDN oferuje cztery różne produkty do wyboru. Aby uzyskać informacje o różnych produktach i ich dostępnych funkcjach, zobacz [Porównanie funkcji usługi Azure CDN.](cdn-features.md)

Jeśli utworzysz **standard usługi Azure CDN standard z** profilu Verizon i używasz go do zarządzania punktami końcowymi usługi CDN, możesz uaktualnić go do **usługi Azure CDN Premium z** profilu Verizon. Podczas uaktualniania punkty końcowe sieci CDN i wszystkie dane zostaną zachowane. 

> [!IMPORTANT]
> Po uaktualnieniu do **usługi Azure CDN Premium z** profilu Verizon nie można później przekonwertować go z powrotem do **standardu usługi Azure CDN z** profilu Verizon.
> 

Aby uaktualnić profil **standardu usługi Azure CDN z** poziomu verizon, skontaktuj się z [pomocą techniczną firmy Microsoft](https://azure.microsoft.com/support/options/).

## <a name="profile-comparison"></a>Porównanie profilu
**Profile usługi Azure CDN Premium firmy Verizon** mają następujące kluczowe różnice w porównaniu z **usługą Azure CDN Standard w** profilach verizon:
- W przypadku niektórych funkcji usługi Azure CDN, takich jak [kompresja,](cdn-improve-performance.md) [reguły buforowania](cdn-caching-rules.md)i [filtrowanie geograficzne,](cdn-restrict-access-by-country.md)nie można używać interfejsu usługi Azure CDN, należy użyć portalu Verizon za pomocą przycisku **Zarządzaj.**
- Interfejs API: W przeciwieństwie do standardu Verizon nie można używać interfejsu API do kontrolowania tych funkcji, które są dostępne z portalu Premium Verizon. Można jednak użyć interfejsu API do kontrolowania innych typowych funkcji, takich jak tworzenie/usuwanie punktu końcowego, czyszczenie/ładowanie buforowanych zasobów oraz włączanie/wyłączanie domeny niestandardowej.
- Ceny: Premium Verizon ma inną strukturę cen dla transferów danych niż Standard Verizon. Aby uzyskać więcej informacji, zobacz [Ceny sieci dostarczania zawartości](https://azure.microsoft.com/pricing/details/cdn/).

**Profile usługi Azure CDN Premium firmy Verizon** mają następujące dodatkowe funkcje:
- [Uwierzytelnianie tokenu:](cdn-token-auth.md)Umożliwia użytkownikom uzyskanie i użycie tokenu do pobierania bezpiecznych zasobów.
- [Aparat reguł:](cdn-rules-engine.md)Umożliwia dostosowanie sposobu obsługi żądań HTTP.
- Zaawansowane narzędzia analityczne:
   - [Szczegółowa analiza HTTP](cdn-advanced-http-reports.md)
   - [Analiza wydajności brzegowej](cdn-edge-performance.md)
   - [Analiza w czasie rzeczywistym](cdn-real-time-alerts.md)


## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o silniku reguł, zobacz [odwołanie do aparatu reguł usługi Azure CDN](cdn-rules-engine-reference.md).

