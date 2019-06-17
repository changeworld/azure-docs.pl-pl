---
title: Szablon rozwiązania aplikacji systemu Azure oferują Podręcznik publikowania | Portal Azure Marketplace
description: W tym artykule opisano wymagania, aby opublikować szablon rozwiązania w witrynie Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: ellacroi
manager: nunoc
ms.service: marketplace
ms.topic: article
ms.date: 11/15/2018
ms.author: ellacroi
ms.openlocfilehash: c2393b6ea9f1a2c2b35be63272743e081f4ae240
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64937746"
---
# <a name="azure-applications-solution-template-offer-publishing-guide"></a>Aplikacje platformy Azure: Oferta szablonu rozwiązania Podręcznik publikowania

Szablony rozwiązań są jednym z głównych sposobów publikowania rozwiązania w portalu Marketplace. Użyj tego przewodnika, aby poznać wymagania dla tej oferty. 

Korzystanie z aplikacji platformy Azure: typ oferty szablonu rozwiązania podczas rozwiązanie wymaga dodatkowej automatyzacji wdrażania i konfiguracji poza pojedynczej maszyny Wirtualnej. Możesz zautomatyzować inicjowania obsługi administracyjnej przynajmniej jednej maszyny wirtualnej za pomocą aplikacji platformy Azure: szablony rozwiązań. Może również udostępniać zasoby sieciowe oraz magazynu. Aplikacje platformy Azure: szablony rozwiązań oferują typu zalety automatyzacji dla pojedynczej maszyny wirtualnej i całego rozwiązania opartego na usłudze IaaS.

Szablony te rozwiązania są oferuje transakcji, które są wdrożone i rozliczane za pośrednictwem portalu Marketplace. Wywołanie akcji, które użytkownik będzie widział jest "Pobierz teraz."


## <a name="requirements-for-solution-templates"></a>Wymagania dotyczące szablony rozwiązań

| **Wymagania** | **Szczegóły**  |
| ---------------  | -----------  |
|Rozliczeń oraz metod pomiarów    |  Zasoby będą aprowizowane w subskrypcji platformy Azure przez klienta. Płatność za rzeczywiste użycie (PŁATNICZEGO) maszyn wirtualnych zostanie wykonany z klientem za pośrednictwem firmy Microsoft, które są rozliczane za pośrednictwem subskrypcji platformy Azure przez klienta (PŁATNICZEGO).  <br/> W przypadku bring-your-own-license (BYOL) gdy firma Microsoft rozlicza koszty związane z infrastrukturą w subskrypcji klienta będą transact oprogramowanie opłaty dla klientów bezpośrednio.   |
|Wirtualny dysk twardy (VHD) zgodnych z platformą Azure  |   Maszyny wirtualne muszą zostać skompilowane na systemie Windows lub Linux.  Aby uzyskać więcej informacji [Zobacz Tworzenie wirtualnego dysku twardego zgodnych z platformą Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md). |
| Uznanie autorstwa użycia klienta | Włączanie autorstwa użycia klienta jest wymagany na wszystkie szablony rozwiązań opublikowanych w portalu Azure Marketplace. Aby uzyskać więcej informacji na temat użycia klienta: uznanie autorstwa i jak go włączyć, zobacz [partnerów platformy Azure klienta użycia: uznanie autorstwa](./azure-partner-customer-usage-attribution.md).  |
|  |  |

## <a name="next-steps"></a>Kolejne kroki
Jeśli użytkownik jeszcze tego nie zrobiono, [zarejestrować](https://azuremarketplace.microsoft.com/sell) w portalu marketplace.

Jeśli jesteś zarejestrowanym i tworzysz nową ofertę lub Praca w istniejącym, zaloguj się do [portalu Cloud Partner](https://cloudpartner.azure.com) do utworzenia lub zakończyć oferty.
