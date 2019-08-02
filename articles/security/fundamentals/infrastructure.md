---
title: Zabezpieczenia infrastruktury platformy Azure | Microsoft Docs
description: W tym artykule opisano, jak firma Microsoft może zabezpieczyć nasze centra danych platformy Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: 3da9fef8c7de248f30100f94724cd5dcdb7db8e3
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727071"
---
# <a name="azure-infrastructure-security"></a>Zabezpieczenia infrastruktury platformy Azure
Microsoft Azure działa w centrach danych zarządzanych i obsługiwanych przez firmę Microsoft. Te geograficznie rozproszone centra danych są zgodne z najważniejszymi standardami branżowymi, takimi jak ISO/IEC 27001:2013 i NIST SP 800-53, w celu zapewnienia bezpieczeństwa i niezawodności. Centra danych są zarządzane, monitorowane i administrowane przez pracowników operacyjnych firmy Microsoft. Personel operacyjny ma lata doświadczenia w dostarczaniu największych Usługi online na świecie z ciągłością 24 x 7.

Ta seria artykułów zawiera informacje o tym, co firma Microsoft chroni infrastruktury platformy Azure. Adresy artykułów:

- [Zabezpieczenia fizyczne](physical-security.md)
- [Dostępność](infrastructure-availability.md)
- [Składniki i granice](infrastructure-components.md)
- [Architektura sieci](infrastructure-network.md)
- [Sieć produkcyjna](production-network.md)
- [SQL Database](infrastructure-sql.md)
- [Operacje](infrastructure-operations.md)
- [Monitorowanie](infrastructure-monitoring.md)
- [Spójn](infrastructure-integrity.md)
- [Ochrona danych](protection-customer-data.md)

## <a name="shared-responsibility-model"></a>Współużytkowany model odpowiedzialności
Ważne jest zrozumienie działu odpowiedzialności między ty i firmą Microsoft. W środowisku lokalnym jest własnością całego stosu, ale podczas przechodzenia do chmury niektóre odpowiedzialności są przekazywane do firmy Microsoft. Poniższa ilustracja przedstawia obszary odpowiedzialności, zgodnie z typem wdrożenia stosu (oprogramowanie jako usługa [SaaS], platformą jako usługą [PaaS], infrastrukturą jako usługą [IaaS] i lokalną).

![Grafika przedstawiająca obowiązki](./media/infrastructure/responsibility-zones.png)

Użytkownik zawsze odpowiada za następujące kwestie, niezależnie od typu wdrożenia:

- Data
- Punkty końcowe
- Konto
- Zarządzanie dostępem

Upewnij się, że rozumiesz dział odpowiedzialności między ty i firmą Microsoft w ramach wdrożenia SaaS, PaaS i IaaS. Aby uzyskać więcej informacji, zobacz [udostępnianie obowiązków w chmurze obliczeniowej](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf).

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat tego, co firma Microsoft pomaga w zabezpieczeniu infrastruktury platformy Azure, zobacz:

- [Funkcje platformy Azure, lokalne i zabezpieczenia fizyczne](physical-security.md)
- [Dostępność infrastruktury platformy Azure](infrastructure-availability.md)
- [Składniki i granice systemu informacji platformy Azure](infrastructure-components.md)
- [Architektura sieci platformy Azure](infrastructure-network.md)
- [Sieć produkcyjna platformy Azure](production-network.md)
- [Azure SQL Database funkcje zabezpieczeń](infrastructure-sql.md)
- [Operacje produkcyjne platformy Azure i zarządzanie nimi](infrastructure-operations.md)
- [Monitorowanie infrastruktury platformy Azure](infrastructure-monitoring.md)
- [Integralność infrastruktury platformy Azure](infrastructure-integrity.md)
- [Ochrona danych klienta platformy Azure](protection-customer-data.md)


