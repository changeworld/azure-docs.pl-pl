---
title: Przewodnik publikowania oferty zarządzanych aplikacji usługi Azure Applications
description: W tym artykule opisano wymagania dotyczące publikowania aplikacji zarządzanej w portalu Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/14/2018
ms.author: dsindona
ms.openlocfilehash: 38323ecfee69460b16542a3e004513f6a7389c41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288414"
---
# <a name="azure-applications-managed-application-offer-publishing-guide"></a>Usługi Azure Applications: Przewodnik publikowania oferty zarządzanych aplikacji

Aplikacja zarządzana jest jednym z głównych sposobów publikowania rozwiązania w portalu Marketplace. Skorzystaj z tego przewodnika, aby zrozumieć wymagania dotyczące tej oferty. 

Są to oferty transakcji, które są wdrażane i rozliczane za pośrednictwem portalu Marketplace. Wywołanie działania, które użytkownik widzi jest "Get It Now".

Użyj aplikacji platformy Azure: typ oferty aplikacji zarządzanej, gdy wymagane są następujące warunki:
- Można wdrożyć rozwiązanie oparte na subskrypcji dla klienta przy użyciu maszyny Wirtualnej lub całego rozwiązania opartego na IaaS.
- Ty lub Twój klient wymagasz, aby rozwiązanie było zarządzane przez partnera.

>[!NOTE]
>Na przykład partnerem może być SI lub zarządzanego dostawcy usług (MSP).  

## <a name="managed-application-offer"></a>Oferta aplikacji zarządzanych

|Wymagania |Szczegóły  |
|---------|---------|
|Wdrożony w ramach subskrypcji platformy Azure klienta | Aplikacje zarządzane muszą być wdrażane w ramach subskrypcji klienta i mogą być zarządzane przez stronę trzecią. | 
|Rozliczenia i pomiary    |  Zasoby zostaną aprowied w subskrypcji platformy Azure klienta. Maszyny wirtualne pay-as-you-go (PAYGO) będą transagowane z klientem za pośrednictwem firmy Microsoft, rozliczane za pośrednictwem subskrypcji platformy Azure klienta (PAYGO). <br> W przypadku licencji bring-your-own, podczas gdy firma Microsoft rozlicza koszty infrastruktury poniesione w związku z subskrypcją klienta, użytkownik bezpośrednio dokonuje płatności za licencjonowanie oprogramowania na rzecz klienta.        |
|Wirtualny dysk twardy zgodny z platformą Azure (VHD)    |   Maszyny wirtualne muszą być zbudowane w systemie Windows lub Linux.<ul> <ul> <li>Aby uzyskać więcej informacji na temat tworzenia dysku VHD systemu Linux, zobacz [Dystrybucje systemu Linux zatwierdzone na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Aby uzyskać więcej informacji na temat tworzenia dysku VHD systemu Windows, zobacz [Tworzenie dysku VHD zgodnego z platformą Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md).</li> </ul> |

>[!NOTE]
> Aplikacje zarządzane muszą być wdrażane za pośrednictwem portalu Marketplace. Jeśli komunikacja z klientem jest problemem, powinieneś skontaktować się z zainteresowanymi klientami po włączeniu udostępniania potencjalnych klientów.  

>[!Note]
>Usługa wyboru kanału partnerskiego dostawców rozwiązań w chmurze (CSP) jest już dostępna.  Zobacz [dostawców rozwiązań w chmurze, aby](./cloud-solution-providers.md) uzyskać więcej informacji na temat marketingu oferty za pośrednictwem kanałów partnerów CSP firmy Microsoft.

## <a name="next-steps"></a>Następne kroki
Jeśli jeszcze tego nie zrobiłeś, 

- [Zarejestruj się](https://azuremarketplace.microsoft.com/sell) na rynku.

Jeśli jesteś zarejestrowany i tworzysz nową ofertę lub pracujesz nad istniejącą,

- [Zaloguj się do portalu cloud partner,](https://cloudpartner.azure.com) aby utworzyć lub uzupełnić ofertę.
