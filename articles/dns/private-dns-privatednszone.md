---
title: Co to jest strefa prywatna usługi Azure DNS
description: Omówienie prywatnej strefy DNS
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: a951bc07c4a8ed42b1c116332d13674656bbaafd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646799"
---
# <a name="what-is-a-private-azure-dns-zone"></a>Co to jest prywatna strefa DNS platformy Azure

Usługa Azure Private DNS zapewnia niezawodną, bezpieczną usługę DNS do zarządzania i rozpoznawania nazw domen w sieci wirtualnej bez konieczności dodawania niestandardowego rozwiązania DNS. Korzystając z prywatnych stref DNS, można użyć własnych niestandardowych nazw domen, a nie nazw dostarczonych przez platformę Azure dostępnych obecnie. 

Rekordy zawarte w prywatnej strefie DNS nie można rozwiązać z Internetu. Rozpoznawanie DNS względem prywatnej strefy DNS działa tylko z sieci wirtualnych, które są z nią połączone.

Prywatną strefę DNS można połączyć z jedną lub kilkoma sieciami wirtualnymi, tworząc [łącza sieci wirtualnej](./private-dns-virtual-network-links.md).
Można również włączyć funkcję [automatycznej rejestracji,](./private-dns-autoregistration.md) aby automatycznie zarządzać cyklem życia rekordów DNS dla maszyn wirtualnych wdrożonych w sieci wirtualnej.

## <a name="limits"></a>Limity

Aby dowiedzieć się, ile prywatnych stref DNS można utworzyć w ramach subskrypcji i ile zestawów rekordów jest obsługiwanych w prywatnej strefie DNS, zobacz [limity dns platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits)

## <a name="restrictions"></a>Ograniczenia

* Pojedyncze prywatne strefy DNS z etykietami nie są obsługiwane. Prywatna strefa DNS musi mieć co najmniej dwie etykiety. Na przykład contoso.com ma dwie etykiety oddzielone kropką. Prywatna strefa DNS może mieć maksymalnie 34 etykiety.
* Nie można tworzyć delegacji stref (rekordów NS) w prywatnej strefie DNS. Jeśli zamierzasz użyć domeny podrzędnej, możesz bezpośrednio utworzyć domenę jako prywatną strefę DNS i połączyć ją z siecią wirtualną bez konfigurowania delegacji serwera nazw ze strefy nadrzędnej.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak utworzyć strefę prywatną w usłudze Azure DNS przy użyciu [programu Azure PowerShell](./private-dns-getstarted-powershell.md) lub [interfejsu wiersza polecenia platformy Azure.](./private-dns-getstarted-cli.md)

* Przeczytaj o niektórych typowych [scenariuszach strefy prywatnej,](./private-dns-scenarios.md) które można zrealizować za pomocą stref prywatnych w usłudze Azure DNS.

* Aby uzyskać typowe pytania i odpowiedzi dotyczące stref prywatnych w usłudze Azure DNS, w tym określone zachowanie, jakich można oczekiwać w przypadku niektórych rodzajów operacji, zobacz [często zadawane pytania dotyczące prywatnego DNS](./dns-faq-private.md).
