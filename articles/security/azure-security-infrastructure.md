---
title: Zabezpieczenie infrastruktury platformy Azure | Dokumentacja firmy Microsoft
description: W artykule opisano, jak firma Microsoft współpracuje zapewnienie naszych centrów danych platformy Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: 9385c6ea8d2a04e9a8595a22e6e2ff7638394b41
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2018
ms.locfileid: "39172976"
---
# <a name="azure-infrastructure-security"></a>Zabezpieczenie infrastruktury platformy Azure
Microsoft Azure działa w centrach danych, zarządzana i obsługiwana przez firmę Microsoft. Te geograficznie rozproszone centra danych zgodne z najważniejszymi standardami branżowymi, takie jak ISO/IEC 27001: 2013 i SP NIST 800-53, bezpieczeństwo i niezawodność. Centrach danych są zarządzane, monitorowane i administrowane przez pracowników operacyjnych firmy Microsoft. Operatorzy mają lat doświadczenia w dostarczaniu największych na świecie usług online z 24 x 7 ciągłość działalności biznesowej.

Ta seria artykułów zawiera informacje dotyczące firmy Microsoft jest zapewnienie infrastruktury platformy Azure. Adres artykuły:

- [zabezpieczenia fizyczne](azure-physical-security.md)
- [Dostępność](azure-infrastructure-availability.md)
- [Składniki i granice](azure-infrastructure-components.md)
- [Architektura sieci](azure-infrastructure-network.md)
- [Sieci produkcyjnej](azure-production-network.md)
- [SQL Database](azure-infrastructure-sql.md)
- [Operacje](azure-infrastructure-operations.md)
- [Monitorowanie](azure-infrastructure-monitoring.md)
- [Integralność](azure-infrastructure-integrity.md)
- [Ochrona danych](azure-protection-of-customer-data.md)

## <a name="shared-responsibility-model"></a>Wspólna odpowiedzialność modelu
Należy zrozumieć podział odpowiedzialności pomiędzy użytkownikiem a firmą Microsoft. W środowisku lokalnym, jesteś właścicielem całego stosu, ale po przeniesieniu do chmury, pewne obowiązki przeniesienia do firmy Microsoft. Poniższa ilustracja przedstawia zakresów odpowiedzialności, zależnie od typu wdrożenia stosu (oprogramowanie jako usługa [SaaS] platforma jako usługa [PaaS], infrastruktura jako usługa [IaaS] i lokalnych).

![Grafika przedstawiająca obowiązki][1]

Odpowiedzialność za zawsze następujące polecenie, niezależnie od tego typu wdrożenia:

- Dane
- Punkty końcowe
- Konto
- Zarządzanie dostępem

Pamiętaj, że rozumiesz podział odpowiedzialności pomiędzy użytkownikiem a firmą Microsoft w ramach wdrożenia rozwiązania SaaS, PaaS i IaaS. Aby uzyskać więcej informacji, zobacz [udostępnionego, przetwarzanie w chmurze obowiązki](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf).

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się, co firma Microsoft używa do zabezpieczania infrastruktury platformy Azure, zobacz:

- [Urządzenia platformy Azure, lokalnie i zabezpieczenia fizyczne](azure-physical-security.md)
- [Dostępność infrastruktury platformy Azure](azure-infrastructure-availability.md)
- [Usługi Azure information składników systemu i granice](azure-infrastructure-components.md)
- [Architektura sieci platformy Azure](azure-infrastructure-network.md)
- [Sieć platformy Azure środowiska produkcyjnego](azure-production-network.md)
- [Funkcje zabezpieczeń w usłudze Azure SQL Database](azure-infrastructure-sql.md)
- [Operacje platformy Azure środowiska produkcyjnego i zarządzanie](azure-infrastructure-operations.md)
- [Monitorowanie infrastruktury platformy Azure](azure-infrastructure-monitoring.md)
- [Integralność infrastruktury platformy Azure](azure-infrastructure-integrity.md)
- [Ochrona danych klientów platformy Azure](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-security-infrastructure/responsibility-zones.png
