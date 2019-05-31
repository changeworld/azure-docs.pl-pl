---
title: Zapory adresów IP dla konta usługi Azure Cosmos
description: Dowiedz się, jak zabezpieczyć dane usługi Azure Cosmos DB przy użyciu zasad kontroli dostępu IP obsługę zapory.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: govindk
ms.openlocfilehash: 9398eb4038afcd17788e750fcb5c27c76e9f3f44
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241081"
---
# <a name="ip-firewall-in-azure-cosmos-db"></a>Zapory adresów IP w usłudze Azure Cosmos DB

Aby zabezpieczyć dane przechowywane na koncie, Azure Cosmos DB obsługuje modelu autoryzacji na podstawie klucza tajnego, który korzysta z silną bazujących na skrótach komunikatów uwierzytelniania kodu (HMAC). Ponadto usługa Azure Cosmos DB obsługuje kontroli dostępu opartych na protokole IP obsługę zapory dla ruchu przychodzącego. Ten model jest podobne do reguł zapory systemu tradycyjnych baz danych i zapewnia dodatkowy poziom zabezpieczeń do Twojego konta. Za pomocą zapory można skonfigurować swoje konto usługi Azure Cosmos, aby były dostępne tylko z zatwierdzonego zestawu maszyn i/lub usług w chmurze. Dostęp do danych przechowywanych w bazie danych Azure Cosmos z tych zestawów zatwierdzonych maszyn i usługi nadal będzie wymagać obiekt wywołujący, aby przedstawić prawidłowy token autoryzacji.

## <a id="ip-access-control-overview"></a>Omówienie kontroli dostępu IP

Domyślnie Twoje konto usługi Azure Cosmos jest dostępny z Internetu, tak długo, jak żądania dołączono prawidłowy token autoryzacji. Aby skonfigurować kontrola dostępu oparta na zasadach IP, użytkownik musi podać zestaw adresów IP lub zakres adresów IP w postaci CIDR (Classless Inter-Domain o routingu), które będą uwzględniane jako lista dozwolonych adresów IP, aby uzyskać dostęp do danego konta usługi Azure Cosmos klientów. Po zastosowaniu konfiguracji wszystkie żądania pochodzące z maszyn spoza tej listy dozwolonych otrzymują 403 (zabronione) odpowiedzi. Korzystając z zapory adresów IP, zalecane jest aby zezwolić na portalu Azure w celu uzyskania dostępu do konta. Dostęp jest wymagany, aby zezwolić na korzystanie z Eksploratora danych, jak również do pobrania metryki dla swojego konta, które są wyświetlane w witrynie Azure portal. Podczas używania Eksploratora danych, oprócz umożliwienia portalu Azure w celu uzyskania dostępu do konta, należy zaktualizować ustawienia zapory, aby dodać bieżący adres IP reguły zapory. Należy pamiętać, że zmian w zaporze może potrwać do 15 minut do propagowania. 

Możesz połączyć zapory oparte na adresie IP z podsiecią i kontrola dostępu do sieci Wirtualnej. Łącząc je, można ograniczyć dostęp do dowolnego źródła, która ma publiczny adres IP i/lub z określonej podsieci w sieci Wirtualnej. Aby dowiedzieć się więcej o korzystaniu z podsieci i kontrola dostępu oparta na sieci Wirtualnej, zobacz [zasobów dostępu do usługi Azure Cosmos DB z sieciami wirtualnymi](vnet-service-endpoint.md).

Aby podsumować, token autoryzacji jest zawsze wymagany dostęp do konta usługi Azure Cosmos. Jeśli zapory adresów IP i listy kontroli dostępu do sieci Wirtualnej (ACL) nie zostały skonfigurowane, konto usługi Cosmos Azure jest dostępna przy użyciu tokenu autoryzacji. Po zapory adresów IP listy ACL sieci Wirtualnej i/lub zostały skonfigurowane na konto usługi Cosmos Azure, tylko żądania pochodzące ze źródeł, które określono (i tokenem autoryzacji) uzyskać prawidłowe odpowiedzi. 

## <a name="next-steps"></a>Kolejne kroki

Następnie można skonfigurować zapory adresów IP lub punkt końcowy usługi sieci Wirtualnej dla swojego konta przy użyciu poniższej dokumentacji:

* [Jak skonfigurować zapory adresów IP dla konta usługi Azure Cosmos](how-to-configure-firewall.md)
* [Dostęp do zasobów usługi Azure Cosmos DB z sieciami wirtualnymi](vnet-service-endpoint.md)
* [Jak skonfigurować punkt końcowy usługi sieci wirtualnej dla konta usługi Azure Cosmos](how-to-configure-vnet-service-endpoint.md)




