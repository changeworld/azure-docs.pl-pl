---
title: Ochrona usługi Azure SQL i danych w Centrum zabezpieczeń Azure | Dokumentacja firmy Microsoft
description: Ten adres dokumentu zalecenia w Centrum zabezpieczeń Azure, które ułatwiają ochronę danych i usługą SQL platformy Azure i są aktualizowane zgodnie z zasadami zabezpieczeń.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2017
ms.author: terrylan
ms.openlocfilehash: 0c3a11e9a86767641533b16de1b96b4c59bfdf51
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23866275"
---
# <a name="protecting-azure-sql-service-and-data-in-azure-security-center"></a>Ochrona usługi Azure SQL i danych w Centrum zabezpieczeń Azure
Centrum zabezpieczeń Azure analizuje stan zabezpieczeń zasobów platformy Azure. Jeśli Centrum zabezpieczeń zostanie zidentyfikowana potencjalnych luk w zabezpieczeniach, tworzy zaleceń, które przeprowadzają użytkownika przez proces konfigurowania wymaganych elementów sterujących.  Zalecenia dotyczą typów zasobów platformy Azure: maszynach wirtualnych (VM), sieci, SQL i danych i aplikacji.

W tym artykule opisano zaleceń, które są stosowane do danych i usługą SQL platformy Azure. Zalecenia Centrum wokół Włączanie inspekcji baz danych, włączenie szyfrowania dla baz danych i włączenie szyfrowania konta magazynu Azure i serwerów SQL platformy Azure.  Użyj poniższej tabeli jako odwołanie, aby lepiej zrozumieć dostępne zalecenia usługi i danych SQL i jak każdą z nich działa w przypadku zastosowania.

## <a name="available-sql-service-and-data-recommendations"></a>Dostępne zalecenia usługi i danych SQL
| Zalecenie | Opis |
| --- | --- |
| [Włączanie inspekcji i wykrywania zagrożeń na serwerach SQL](security-center-enable-auditing-on-sql-servers.md) |Zaleca się włączenie inspekcji i wykrywania zagrożeń dla serwerów SQL platformy Azure (usługą SQL platformy Azure; nie zawiera SQL uruchomionych na maszynach wirtualnych). |
| [Włączanie inspekcji i wykrywania zagrożeń w bazach danych SQL](security-center-enable-auditing-on-sql-databases.md) |Zaleca się włączenie inspekcji i wykrywania zagrożeń dla baz danych Azure SQL (usługą SQL platformy Azure; nie zawiera SQL uruchomionych na maszynach wirtualnych). |
| [Funkcja przezroczystego szyfrowania danych, Włącz bazy danych SQL](security-center-enable-transparent-data-encryption.md) |Zaleca się, aby włączyć szyfrowanie dla bazy danych SQL (tylko usługa Azure SQL). |

## <a name="see-also"></a>Zobacz też
Aby dowiedzieć się więcej na temat zaleceń, które są stosowane do innych typów zasobów platformy Azure, zobacz następujące tematy:

* [Ochrona maszyn wirtualnych w Centrum zabezpieczeń Azure](security-center-virtual-machine-recommendations.md)
* [Ochrona aplikacji w Centrum zabezpieczeń Azure](security-center-application-recommendations.md)
* [Ochrona sieci w Centrum zabezpieczeń Azure](security-center-network-recommendations.md)

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](security-center-policies.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
