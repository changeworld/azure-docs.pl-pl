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
ms.openlocfilehash: 45f5dc840f015793912e314ab3d47e54a409708e
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2018
ms.locfileid: "46126670"
---
# <a name="protecting-azure-sql-service-and-data-in-azure-security-center"></a>Ochrona usługi Azure SQL i danych w usłudze Azure Security Center
Usługa Azure Security Center analizuje stan zabezpieczeń zasobów platformy Azure. Gdy usługa Security Center zidentyfikuje potencjalnych luk w zabezpieczeniach, tworzy zaleceń, które przeprowadzą Cię przez proces konfigurowania wymaganych kontrolek.  Zalecenia odnoszą się do typów zasobów platformy Azure: maszyny wirtualne (VM), networking, SQL i dane i aplikacje.

W tym artykule opisano zaleceń, które mają zastosowanie do usługi Azure SQL i danych. Centrum zalecenia dotyczące włączania inspekcji dla serwerów SQL platformy Azure i bazy danych, włączania szyfrowania baz danych SQL i włączania szyfrowania konta magazynu platformy Azure.  Użyj poniższej tabeli jako odwołanie ułatwiające zrozumienie dostępnych zaleceń usługi i danych SQL i jak każdy z nich działa w przypadku zastosowania.
### <a name="monitor-data-security"></a>Monitorowanie bezpieczeństwa danych

Po kliknięciu pozycji **Bezpieczeństwo danych** w sekcji **Zapobieganie** zostanie otwarty blok **Zasoby danych** z zaleceniami dotyczącymi usług SQL i Storage. Przedstawia on także [zalecenia](security-center-sql-service-recommendations.md) dotyczące ogólnej kondycji bazy danych. Aby uzyskać więcej informacji dotyczących szyfrowania magazynu, przeczytaj artykuł [Enable encryption for Azure storage account in Azure Security Center](security-center-enable-encryption-for-storage-account.md) (Włączanie szyfrowania dla konta usługi Azure Storage w usłudze Azure Security Center).

![Zasoby danych](./media/security-center-monitoring/security-center-monitoring-fig13-newUI-2017.png)

W obszarze **Zalecenia SQL** można kliknąć dowolne zalecenie i uzyskać więcej szczegółów na temat dalszych działań w celu rozwiązania problemu. Poniższy przykład pokazuje rozszerzenie zalecenia **Inspekcja bazy danych i wykrywanie zagrożeń w bazach danych SQL**.

![Szczegółowe informacje o zaleceniach SQL](./media/security-center-monitoring/security-center-monitoring-fig14-ga-new.png)

Blok **Włączanie inspekcji i wykrywania zagrożeń dla baz danych SQL** zawiera następujące informacje:

* Lista baz danych SQL
* Serwer, na którym znajdują się bazy danych SQL
* Informacje o tym, czy to ustawienie jest dziedziczone z serwera lub czy jest unikatowe w tej bazie danych
* Bieżący stan
* Ważność problemu

Po kliknięciu bazy danych w celu zastosowania tego zalecenia zostanie otwarty blok **Inspekcja i wykrywanie zagrożeń**, jak pokazano na poniższym ekranie.

![Inspekcja i wykrywanie zagrożeń](./media/security-center-monitoring/security-center-monitoring-fig15-ga.png)

Aby włączyć funkcję inspekcji, zaznacz pole wyboru **WŁĄCZONE** poniżej opcji **Inspekcja**.

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
