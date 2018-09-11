---
title: Ochrona usługi Azure SQL i danych w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: Adresy tego dokumentu, zalecenia w usłudze Azure Security Center, które ułatwiają ochronę danych i usługi Azure SQL i pozostają zgodne z zasadami zabezpieczeń.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2017
ms.author: terrylan
ms.openlocfilehash: 0b3b8082412b12a0fffbaea04409a8bbb3f4ac15
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2018
ms.locfileid: "44295381"
---
# <a name="protecting-azure-sql-service-and-data-in-azure-security-center"></a>Ochrona usługi Azure SQL i danych w usłudze Azure Security Center
Usługa Azure Security Center analizuje stan zabezpieczeń zasobów platformy Azure. Gdy usługa Security Center zidentyfikuje potencjalnych luk w zabezpieczeniach, tworzy zaleceń, które przeprowadzą Cię przez proces konfigurowania wymaganych kontrolek.  Zalecenia odnoszą się do typów zasobów platformy Azure: maszyny wirtualne (VM), networking, SQL i dane i aplikacje.

W tym artykule opisano zaleceń, które mają zastosowanie do usługi Azure SQL i danych. Centrum zalecenia dotyczące włączania inspekcji dla serwerów SQL platformy Azure i bazy danych, włączania szyfrowania baz danych SQL i włączania szyfrowania konta magazynu platformy Azure.  Użyj poniższej tabeli jako odwołanie ułatwiające zrozumienie dostępnych zaleceń usługi i danych SQL i jak każdy z nich działa w przypadku zastosowania.

## <a name="available-sql-service-and-data-recommendations"></a>Dostępne usługi i dane zalecenia dotyczące SQL
| Zalecenie | Opis |
| --- | --- |
| [Włączanie inspekcji i wykrywania zagrożeń na serwerach SQL](security-center-enable-auditing-on-sql-servers.md) |Zaleca się włączenie inspekcji i wykrywania zagrożeń dla serwerów SQL platformy Azure (usługi Azure SQL nie obejmuje środowisko SQL uruchomionych na maszynach wirtualnych). |
| [Włączanie inspekcji i wykrywania zagrożeń w bazach danych SQL](security-center-enable-auditing-on-sql-databases.md) |Zaleca się włączenie inspekcji i wykrywania zagrożeń dla baz danych Azure SQL (usługi Azure SQL nie obejmuje środowisko SQL uruchomionych na maszynach wirtualnych). |
| [Włączanie funkcji Transparent Data Encryption w bazach danych SQL](security-center-enable-transparent-data-encryption.md) |Zaleca włączenie szyfrowania baz danych SQL (tylko w przypadku usługi Azure SQL). |

## <a name="see-also"></a>Zobacz także
Aby dowiedzieć się więcej na temat zalecenia, które mają zastosowanie do innych typów zasobów platformy Azure, zobacz następujące tematy:

* [Ochrona maszyn wirtualnych w usłudze Azure Security Center](security-center-virtual-machine-recommendations.md)
* [Ochrona aplikacji w usłudze Azure Security Center](security-center-application-recommendations.md)
* [Ochrona sieci w usłudze Azure Security Center](security-center-network-recommendations.md)

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](security-center-policies.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
