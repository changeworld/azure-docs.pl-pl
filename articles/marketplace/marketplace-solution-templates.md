---
title: Szablon oferty rozwiązań aplikacji platformy Azure, Podręcznik publikowania
description: W tym artykule opisano wymagania, aby opublikować szablon rozwiązania w portalu Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: ellacroi
manager: nunoc
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 07/09/2018
ms.author: ellacroi
ms.openlocfilehash: 44d081a0666aa37ec0bf8eeac540b7a7f4f4f904
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39059634"
---
# <a name="azure-applications-solution-template-offer-publishing-guide"></a>Aplikacje platformy Azure: Rozwiązanie szablon oferty, Podręcznik publikowania

Szablony rozwiązań są jednym z głównych sposobów publikowania rozwiązania w portalu Marketplace. Użyj tego przewodnika, aby poznać wymagania dla tej oferty. 

Są to oferty transakcji, które są wdrożone i rozliczane za pośrednictwem portalu Marketplace. Wywołanie akcji, które użytkownik będzie widział jest "Pobierz teraz."

Korzystanie z aplikacji platformy Azure: typ oferty szablonu rozwiązania podczas rozwiązanie wymaga dodatkowej automatyzacji wdrażania i konfiguracji poza prostą maszynę Wirtualną. Możesz zautomatyzować inicjowania obsługi administracyjnej przynajmniej jednej maszyny wirtualnej za pomocą aplikacji platformy Azure: szablony rozwiązań. Może również udostępniać zasoby sieciowe oraz magazynu. Aplikacje platformy Azure: szablony rozwiązań oferują typu zalety automatyzacji dla pojedynczej maszyny wirtualnej i całego rozwiązania opartego na usłudze IaaS.

## <a name="requirements-for-solution-templates"></a>Wymagania dotyczące szablony rozwiązań

|Wymagania |Szczegóły  |
|---------|---------|
|Rozliczeń oraz metod pomiarów    |  Zasoby będą aprowizowane w subskrypcji platformy Azure przez klienta. Płatność za rzeczywiste użycie (PŁATNICZEGO) maszyn wirtualnych zostanie wykonany w z klientem za pośrednictwem firmy Microsoft, które są rozliczane za pośrednictwem subskrypcji platformy Azure przez klienta (PŁATNICZEGO) 
W przypadku bring-your-own-license gdy firma Microsoft rozlicza koszty związane z infrastrukturą w subskrypcji klienta możesz będzie transact oprogramowanie opłaty dla klientów bezpośrednio        |
|Wirtualny dysk twardy (VHD) zgodnych z platformą Azure    |   Maszyny wirtualne muszą zostać skompilowane na systemie Windows lub Linux.<ul> <li>Aby uzyskać więcej informacji na temat tworzenia wirtualnego dysku twardego systemu Linux, odwiedź stronę tworzenia dysku VHD zgodnych z platformą Azure (opartych na systemie Linux) sekcji znajdujący się w [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2- Create-an-Azure-Compatible-VHD-Linux-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based).</li> <li>Aby uzyskać więcej informacji na temat tworzenia wirtualnego dysku twardego Windows można znaleźć tworzenia dysku VHD zgodnych z platformą Azure (z systemem Windows) sekcji znajdujący się w [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3- Create-an-Azure-Compatible-VHD-Windows-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based).</li> </ul>      |



## <a name="next-steps"></a>Następne kroki
Jeśli użytkownik jeszcze tego nie zrobiono, 

- [Zarejestruj](https://azuremarketplace.microsoft.com/sell) w portalu marketplace

Jeśli jesteś zarejestrowanym i tworzysz nową ofertę czy działa na podstawie istniejącego

- [Zaloguj się do portalu Cloud Partner](https://cloudpartner.azure.com) do utworzenia lub zakończyć oferty
