---
title: Co to jest funkcja autoregistration stref prywatnych usługi Azure DNS
description: Omówienie funkcji autoregistration stref prywatnych usługi Azure DNS
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 9d1854b459e799d5cbb401de9ac717dd7d0fde1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71961172"
---
# <a name="what-is-the-autoregistration-feature-of-azure-dns-private-zones"></a>Jaka jest funkcja autoregistration stref prywatnych usługi Azure DNS

Funkcja automatycznej rejestracji stref prywatnych dns platformy Azure usuwa ból związany z zarządzaniem rekordami DNS dla maszyn wirtualnych wdrożonych w sieci wirtualnej. Po [połączeniu sieci wirtualnej](./private-dns-virtual-network-links.md) z prywatną strefą DNS i włączeniu automatycznej rejestracji dla wszystkich maszyn wirtualnych rekordy DNS dla maszyn wirtualnych wdrożonych w sieci wirtualnej są automatycznie tworzone w prywatnej strefie DNS. Oprócz rekordów wybiegu w przyszłość (rekordy A), rekordy wyszukiwania wstecznego (rekordy PTR) są również automatycznie tworzone dla maszyn wirtualnych.
Jeśli dodasz więcej maszyn wirtualnych do sieci wirtualnej, rekordy DNS dla tych maszyn wirtualnych są również automatycznie tworzone w połączonej prywatnej strefie DNS.

Po usunięciu maszyny wirtualnej rekordy DNS maszyny wirtualnej są automatycznie usuwane z prywatnej strefy DNS.

Autoregistration można włączyć, wybierając opcję "Włącz automatyczną rejestrację" podczas tworzenia łącza sieci wirtualnej.

![Włącz automatyczną rejestrację](./media/privatedns-concepts/enable-autoregistration.png)

## <a name="restrictions"></a>Ograniczenia

* Autoregistration działa tylko dla maszyn wirtualnych. W przypadku wszystkich innych zasobów, takich jak wewnętrzne moduły równoważenia obciążenia itp., można ręcznie tworzyć rekordy DNS w prywatnej strefie DNS połączonej z siecią wirtualną.
* Rekordy DNS są tworzone automatycznie tylko dla podstawowej karty sieciowej maszyny wirtualnej . Jeśli maszyny wirtualne mają więcej niż jedną kartę sieciową, można ręcznie utworzyć rekordy DNS dla innych interfejsów sieciowych.
* autoregistration dla IPv6 (rekordy AAAA) nie jest obsługiwany.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak utworzyć strefę prywatną w usłudze Azure DNS przy użyciu [programu Azure PowerShell](./private-dns-getstarted-powershell.md) lub [interfejsu wiersza polecenia platformy Azure.](./private-dns-getstarted-cli.md)

* Przeczytaj o niektórych typowych [scenariuszach strefy prywatnej,](./private-dns-scenarios.md) które można zrealizować za pomocą stref prywatnych w usłudze Azure DNS.

* Aby uzyskać typowe pytania i odpowiedzi dotyczące stref prywatnych w usłudze Azure DNS, w tym określone zachowanie, jakich można oczekiwać w przypadku niektórych rodzajów operacji, zobacz [często zadawane pytania dotyczące prywatnego DNS](./dns-faq-private.md).
