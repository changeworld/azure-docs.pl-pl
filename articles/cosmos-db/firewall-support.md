---
title: Zapora IP dla kont usługi Azure Cosmos
description: Dowiedz się, jak zabezpieczyć dane usługi Azure Cosmos DB przy użyciu zasad kontroli dostępu do adresów IP dla obsługi zapory.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: govindk
ms.openlocfilehash: 9398eb4038afcd17788e750fcb5c27c76e9f3f44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66241081"
---
# <a name="ip-firewall-in-azure-cosmos-db"></a>Zapora bazująca na adresach IP w usłudze Azure Cosmos DB

Aby zabezpieczyć dane przechowywane na koncie, usługa Azure Cosmos DB obsługuje model autoryzacji oparty na kluczu tajnym, który wykorzystuje silny kod uwierzytelniania wiadomości oparty na skrótach (HMAC). Ponadto usługa Azure Cosmos DB obsługuje formanty dostępu oparte na protokãołowych adresach IP dla obsługi zapory przychodzącej. Ten model jest podobny do reguł zapory tradycyjnego systemu baz danych i zapewnia dodatkowy poziom zabezpieczeń konta. Za pomocą zapór można skonfigurować konto usługi Azure Cosmos tak, aby było dostępne tylko z zatwierdzonego zestawu maszyn i/lub usług w chmurze. Dostęp do danych przechowywanych w bazie danych usługi Azure Cosmos z tych zatwierdzonych zestawów maszyn i usług będzie nadal wymagać od obiektu wywołującego przedstawienia prawidłowego tokenu autoryzacji.

## <a name="ip-access-control-overview"></a><a id="ip-access-control-overview"></a>Omówienie kontroli dostępu do adresu IP

Domyślnie twoje konto usługi Azure Cosmos jest dostępne z Internetu, o ile żądaniu towarzyszy prawidłowy token autoryzacji. Aby skonfigurować kontrolę dostępu opartą na zasadach IP, użytkownik musi podać zestaw adresów IP lub zakresów adresów IP w formularzu CIDR (Classless Inter-Domain Routing), które mają zostać uwzględnione jako dozwolona lista adresów IP klienta, aby uzyskać dostęp do danego konta usługi Azure Cosmos. Po zastosowaniu tej konfiguracji wszelkie żądania pochodzące z maszyn spoza tej listy dozwolonych otrzymują odpowiedź 403 (zabronione). Podczas korzystania z zapory IP zaleca się zezwolić witrynie Azure portal na dostęp do konta. Dostęp jest wymagany, aby umożliwić korzystanie z Eksploratora danych, a także do pobierania metryk dla konta, które są wyświetlane w witrynie Azure portal. Podczas korzystania z Eksploratora danych, oprócz umożliwienia witryny Azure Portal dostępu do konta, należy również zaktualizować ustawienia zapory, aby dodać bieżący adres IP do reguł zapory. Należy zauważyć, że propagowanie zmian w zaporze może potrwać do 15 minut. 

Zaporę opartą na protokoiach IP można połączyć z kontrolą dostępu do podsieci i sieci wirtualnej. Łącząc je, można ograniczyć dostęp do dowolnego źródła, które ma publiczny adres IP i/lub z określonej podsieci w sieci wirtualnej. Aby dowiedzieć się więcej na temat korzystania z podsieci i kontroli dostępu opartej na sieci wirtualnej, zobacz [Dostęp do zasobów usługi Azure Cosmos DB z sieci wirtualnych](vnet-service-endpoint.md).

Podsumowując token autoryzacji jest zawsze wymagany do uzyskania dostępu do konta usługi Azure Cosmos. Jeśli zapora IP i lista kontroli dostępu do sieci wirtualnej (ACL) nie są skonfigurowane, konto usługi Azure Cosmos można uzyskać za pomocą tokenu autoryzacji. Po zaporze IP lub listów ACL sieci wirtualnej lub oba są skonfigurowane na koncie usługi Azure Cosmos, tylko żądania pochodzące z określonych źródeł (i token autoryzacji) uzyskać prawidłowe odpowiedzi. 

## <a name="next-steps"></a>Następne kroki

Następnie można skonfigurować zaporę IP lub punkt końcowy usługi sieci wirtualnej dla swojego konta przy użyciu następujących dokumentów:

* [Jak skonfigurować zaporę IP dla konta usługi Azure Cosmos](how-to-configure-firewall.md)
* [Uzyskiwanie dostępu do zasobów usługi Azure Cosmos DB z sieci wirtualnych](vnet-service-endpoint.md)
* [Jak skonfigurować punkt końcowy usługi sieci wirtualnej dla konta usługi Azure Cosmos](how-to-configure-vnet-service-endpoint.md)




