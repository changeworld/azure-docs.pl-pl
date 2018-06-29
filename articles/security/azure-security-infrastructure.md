---
title: Bezpieczeństwo infrastruktury platformy Azure | Dokumentacja firmy Microsoft
description: W artykule opisano, jak Microsoft zapewnia zabezpieczeń naszych centrach danych platformy Azure.
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
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 313fbc0fea317e8888bf64e7f7817ab0e5c9f049
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102533"
---
# <a name="security-of-azure-infrastructure"></a>Bezpieczeństwo infrastruktury platformy Azure
Microsoft Azure działa w centrach danych zarządzana i obsługiwana przez firmę Microsoft. Te geograficznie centrów danych są zgodne ze standardami branżowymi klucza, takie jak ISO/IEC 27001: 2013 i NIST SP 800-53 dla bezpieczeństwa i niezawodności. Centrach danych są zarządzane, monitorowane i zarządzane przez pracowników firmy Microsoft. Operatorzy ma latach doświadczeń w dostarczaniu usług online największa na świecie z ciągłości 24 x 7.

Ta seria artykułów zawiera informacje dotyczące firmy Microsoft jest zapewnienie infrastruktury platformy Azure. Adres artykuły:

- [Zabezpieczenia fizyczne](azure-physical-security.md)
- [Dostępność](azure-infrastructure-availability.md)
- [Składniki i granice](azure-infrastructure-components.md)
- [Architektura sieci](azure-infrastructure-network.md)
- [Środowiska produkcyjnego](azure-production-network.md)
- [SQL Database](azure-infrastructure-sql.md)
- [Operacje](azure-infrastructure-operations.md)
- [Monitorowanie](azure-infrastructure-monitoring.md)
- [Integralność](azure-infrastructure-integrity.md)
- [Ochrona danych](azure-protection-of-customer-data.md)

## <a name="shared-responsibility-model"></a>Model wspólnej odpowiedzialności
Należy zrozumieć podział obowiązków między Tobą a firmą Microsoft. W infrastrukturze lokalnej, właścicielem cały stos, ale przy przenoszeniu do chmury pewne obowiązki transferu do firmy Microsoft. Następujące macierzy odpowiedzialność przedstawiono obszary stosu w oprogramowania jako usługa (SaaS), platformy jako usługa (PaaS), a infrastruktura jako usługa (IaaS) wdrożenia, który jest odpowiedzialny za i firmy Microsoft jest odpowiedzialny za.

![Wspólna odpowiedzialność][1]

Obowiązki, które są zawsze zachowywane przez administratora, niezależnie od tego typu wdrożenia, są następujące:

- Dane
- Punkty końcowe
- Konto
- Zarządzanie dostępem

Pamiętaj, że rozumiesz podział obowiązków między Tobą a firmą Microsoft w ramach wdrożenia SaaS, PaaS i IaaS. Zobacz [obowiązki udostępnionych chmury obliczeniowej](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf) uzyskać więcej szczegółowych informacji.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat firmy Microsoft jest zapewnienie infrastruktury platformy Azure, zobacz:

- [Urządzenia platformy Azure, lokalne i zabezpieczenia fizyczne](azure-physical-security.md)
- [Dostępność infrastruktury platformy Azure](azure-infrastructure-availability.md)
- [Składniki systemu Azure informacji i granice](azure-infrastructure-components.md)
- [Architektura sieci platformy Azure](azure-infrastructure-network.md)
- [Sieci Azure środowiska produkcyjnego](azure-production-network.md)
- [Funkcje zabezpieczeń bazy danych SQL Azure firmy Microsoft](azure-infrastructure-sql.md)
- [Operacje Azure środowiska produkcyjnego i zarządzania](azure-infrastructure-operations.md)
- [Monitorowanie infrastruktury platformy Azure](azure-infrastructure-monitoring.md)
- [Integralność infrastruktury platformy Azure](azure-infrastructure-integrity.md)
- [Ochrona danych klienta na platformie Azure](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-security-infrastructure/responsibility-zones.png
