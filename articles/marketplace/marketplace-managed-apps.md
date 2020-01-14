---
title: Przewodnik publikowania oferty aplikacji zarządzanych przez aplikacje platformy Azure
description: W tym artykule opisano wymagania dotyczące publikowania aplikacji zarządzanej w portalu Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: MaggiePucciEvans
manager: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 06/14/2018
ms.author: evansma
ms.openlocfilehash: 764212ac148b336b07d29c29a72314c5d889d47c
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934657"
---
# <a name="azure-applications-managed-application-offer-publishing-guide"></a>Aplikacje platformy Azure: Przewodnik publikowania oferty aplikacji zarządzanej

Aplikacja zarządzana to jeden z głównych sposobów publikowania rozwiązania w portalu Marketplace. Skorzystaj z tego przewodnika, aby poznać wymagania dotyczące tej oferty. 

Są to oferty transakcji, które są wdrażane i rozliczane za pomocą witryny Marketplace. Wywołanie akcji, którą widzi użytkownik, to "Pobierz teraz".

Użyj usługi Azure App: Managed App — typ oferty, gdy wymagane są następujące warunki:
- Rozwiązanie oparte na subskrypcji można wdrożyć dla klienta przy użyciu maszyny wirtualnej lub całego rozwiązania opartego na IaaS.
- Ty lub Twój klient wymaga, aby rozwiązanie było zarządzane przez partnera.

>[!NOTE]
>Na przykład partner może być dostawcą usług (MSP) lub usługą zarządzaną.  

## <a name="managed-application-offer"></a>Oferta aplikacji zarządzanej

|Wymagania |Szczegóły  |
|---------|---------|
|Wdrożone w subskrypcji platformy Azure klienta | Zarządzane aplikacje muszą być wdrożone w subskrypcji klienta i mogą być zarządzane przez inną firmę. | 
|Rozliczenia i pomiary    |  Zasoby będą obsługiwane w ramach subskrypcji platformy Azure klienta. Maszyny wirtualne z płatnością zgodnie z rzeczywistym użyciem (PAYGO) będą w firmie Microsoft rozliczane za pośrednictwem subskrypcji platformy Azure (PAYGO) klienta. <br> W przypadku dołączenia do własnej licencji, podczas gdy firma Microsoft będzie rozliczać koszty infrastruktury związane z subskrypcją klienta, należy bezpośrednio wprowadzić opłaty za licencję na oprogramowanie klienta.        |
|Wirtualny dysk twardy (VHD) zgodny z platformą Azure    |   Maszyny wirtualne muszą być wbudowane w system Windows lub Linux.<ul> <ul> <li>Aby uzyskać więcej informacji na temat tworzenia wirtualnego dysku twardego z systemem Linux, zobacz [dystrybucje systemu Linux zatwierdzone na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Aby uzyskać więcej informacji na temat tworzenia wirtualnego dysku twardego systemu Windows, zobacz [Tworzenie dysku VHD zgodnego z platformą Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md).</li> </ul> |

>[!NOTE]
> Zarządzane aplikacje muszą być wdrażane za pomocą portalu Marketplace. Jeśli komunikacja z klientem jest istotna, należy skontaktować się z zainteresowanymi klientami po włączeniu udostępniania potencjalnego klienta.  

>[!Note]
>Niedostępność kanału partnera dostawcy rozwiązań w chmurze (CSP) jest teraz dostępna.  Zobacz [dostawcy rozwiązań w chmurze](./cloud-solution-providers.md) , aby uzyskać więcej informacji na temat marketingu oferty przez kanały partnerskie programu Microsoft CSP.

## <a name="next-steps"></a>Następne kroki
Jeśli jeszcze tego nie zrobiono, 

- [Zarejestruj](https://azuremarketplace.microsoft.com/sell) się w portalu Marketplace.

Jeśli jesteś zarejestrowanym użytkownikiem i tworzysz nową ofertę lub pracujesz nad istniejącym,

- [Zaloguj się do Portal Cloud partner,](https://cloudpartner.azure.com) aby utworzyć lub zakończyć swoją ofertę.
