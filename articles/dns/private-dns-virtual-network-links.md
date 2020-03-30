---
title: Co to jest podźródło łącza sieci wirtualnej stref prywatnych usługi Azure DNS
description: Omówienie zasobu podrzędnego łącza sieci wirtualnej w strefie prywatnej usługi Azure DNS
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 9181ef93dfedbc28b297bef48a0bc37ba6d69798
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646765"
---
# <a name="what-is-a-virtual-network-link"></a>Co to jest łącze sieci wirtualnej?

Po utworzeniu prywatnej strefy DNS na platformie Azure nie jest ona natychmiast dostępna z dowolnej sieci wirtualnej. Aby maszyna wirtualna hostowana w tej sieci mogła uzyskać dostęp do prywatnej strefy DNS, należy połączyć ją z siecią wirtualną.
Aby połączyć prywatną strefę DNS z siecią wirtualną, należy utworzyć łącze sieci wirtualnej w prywatnej strefie DNS. Każda prywatna strefa DNS ma kolekcję zasobów podrzędnych łączy sieci wirtualnej. Każdy z tych zasobów reprezentuje połączenie z siecią wirtualną.

Sieć wirtualną można połączyć z prywatną strefą DNS jako sieć wirtualną rejestracji lub jako sieć wirtualna o rozdzielczości.

## <a name="registration-virtual-network"></a>Rejestracja sieci wirtualnej

Podczas [tworzenia łącza](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network) między prywatną strefą DNS a siecią wirtualną istnieje możliwość włączenia [autoregistracji](./private-dns-autoregistration.md) rekordów DNS dla maszyn wirtualnych. Jeśli wybierzesz tę opcję, sieć wirtualna stanie się siecią wirtualną rejestracji dla prywatnej strefy DNS. Rekord DNS jest tworzony automatycznie dla maszyn wirtualnych wdrażane w sieci. Rekordy DNS są tworzone dla maszyn wirtualnych, które zostały już wdrożone w sieci wirtualnej. Z punktu widzenia sieci wirtualnej prywatna strefa DNS staje się strefą rejestracji dla tej sieci wirtualnej.
Jedna prywatna strefa DNS może mieć wiele sieci wirtualnych rejestracji, jednak każda sieć wirtualna może mieć dokładnie jedną strefę rejestracji skojarzoną z nią.

## <a name="resolution-virtual-network"></a>Rozdzielczość sieci wirtualnej

Podczas tworzenia łącza sieci wirtualnej w prywatnej strefie DNS i nie włączania autoregistracji rekordów DNS, sieć wirtualna jest traktowana jako sieć wirtualna tylko w rozdzielczości. Rekordy DNS dla maszyn wirtualnych wdrożonych w takich sieciach nie będą tworzone automatycznie w połączonej prywatnej strefie DNS. Jednak maszyny wirtualne wdrożone w takiej sieci mogą pomyślnie wysyłać kwerendy do rekordów DNS z prywatnej strefy DNS. Rekordy te mogą być tworzone ręcznie przez użytkownika lub mogą być wypełniane z innych sieci wirtualnych, które zostały połączone jako sieci rejestracji z prywatną strefą DNS.
Jedna prywatna strefa DNS może mieć wiele sieci wirtualnych o rozdzielczości, a sieć wirtualna może mieć skojarzone z nią wiele stref rozpoznawania.

## <a name="limits"></a>Limity

Aby dowiedzieć się, ile sieci rejestracji i rozpoznawania, można połączyć z prywatnymi strefami DNS, zobacz [Limity DNS platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits)

## <a name="other-considerations"></a>Inne zagadnienia

* Sieci wirtualne wdrożone przy użyciu klasycznego modelu wdrażania nie są obsługiwane.

* Można utworzyć tylko jedno łącze między prywatną strefą DNS a siecią wirtualną.

* Każde łącze sieci wirtualnej w prywatnej strefie DNS musi mieć unikatową nazwę w kontekście prywatnej strefy DNS. Łącza o tej samej nazwie mogą mieć w różnych prywatnych strefach DNS.

* Po utworzeniu łącza sieci wirtualnej sprawdź pole "Stan łącza" zasobu łącza sieci wirtualnej. W zależności od rozmiaru sieci wirtualnej może upłynąć kilka minut, zanim połączenie zostanie wykonane, a stan łącza zmieni się na *Zakończone*.

* Po usunięciu sieci wirtualnej wszystkie łącza sieci wirtualnej i automatycznie zarejestrowane rekordy DNS skojarzone z nią w różnych prywatnych strefach DNS są automatycznie usuwane.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak połączyć sieć wirtualną z prywatną strefą DNS za pomocą [witryny Azure portal](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network)

* Dowiedz się, jak utworzyć strefę prywatną w usłudze Azure DNS przy użyciu [programu Azure PowerShell](./private-dns-getstarted-powershell.md) lub [interfejsu wiersza polecenia platformy Azure.](./private-dns-getstarted-cli.md)

* Przeczytaj o niektórych typowych [scenariuszach strefy prywatnej,](./private-dns-scenarios.md) które można zrealizować za pomocą stref prywatnych w usłudze Azure DNS.

* Aby uzyskać typowe pytania i odpowiedzi dotyczące stref prywatnych w usłudze Azure DNS, w tym określone zachowanie, jakich można oczekiwać w przypadku niektórych rodzajów operacji, zobacz [często zadawane pytania dotyczące prywatnego DNS](./dns-faq-private.md).
