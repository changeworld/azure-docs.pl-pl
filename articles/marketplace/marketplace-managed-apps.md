---
title: Podręcznik publikowania oferty aplikacji zarządzanej aplikacji platformy Azure
description: W tym artykule opisano wymagania aby opublikować aplikację zarządzaną w witrynie Marketplace
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
ms.openlocfilehash: 674e91a7c1de026a26cf9a3bf1eaead7f5e86144
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39060551"
---
# <a name="azure-applications-managed-application-offer-publishing-guide"></a>Aplikacji platformy Azure: Podręcznik publikowania oferty aplikacji zarządzanej

Szablony rozwiązań są jednym z głównych sposobów publikowania rozwiązania w portalu Marketplace. Użyj tego przewodnika, aby poznać wymagania dla tej oferty. 

Są to oferty transakcji, które są wdrożone i rozliczane za pośrednictwem portalu Marketplace. Wywołanie akcji, które użytkownik będzie widział jest "Pobierz teraz."

Korzystanie z aplikacji platformy Azure: typ oferty aplikacji zarządzanych, gdy wymagane są następujące warunki:
- Możesz wdrożyć albo rozwiązanie oparte na subskrypcji dla klienta przy użyciu maszyny Wirtualnej lub całego rozwiązania opartego na usłudze IaaS.
- Użytkownik lub klient wymaga, że rozwiązanie jest zarządzana przez partnera.

>[!NOTE]
>Na przykład partnera może być SI lub dostawcą usługi zarządzanej (MSP).  

## <a name="managed-application-offer"></a>Oferty aplikacji zarządzanych

|Wymagania |Szczegóły  |
|---------|---------|
|Wdrożone do subskrypcji platformy Azure dla klientów | Aplikacje zarządzane musi zostać wdrożony w subskrypcji klienta i mogą być zarządzane przez stronę 3 | 
|Rozliczeń oraz metod pomiarów    |  Zasoby będą aprowizowane w subskrypcji platformy Azure przez klienta. Płatność za rzeczywiste użycie (PŁATNICZEGO) maszyn wirtualnych zostanie wykonany w z klientem za pośrednictwem firmy Microsoft, które są rozliczane za pośrednictwem subskrypcji platformy Azure przez klienta (PŁATNICZEGO) 
W przypadku bring-your-own-license gdy firma Microsoft rozlicza koszty związane z infrastrukturą w subskrypcji klienta możesz będzie transact oprogramowanie opłaty dla klientów bezpośrednio        |
|Wirtualny dysk twardy (VHD) zgodnych z platformą Azure    |   Maszyny wirtualne muszą zostać skompilowane na systemie Windows lub Linux.<ul> <li>Aby uzyskać więcej informacji na temat tworzenia wirtualnego dysku twardego systemu Linux, odwiedź stronę tworzenia dysku VHD zgodnych z platformą Azure (opartych na systemie Linux) sekcji znajdujący się w [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2- Create-an-Azure-Compatible-VHD-Linux-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based).</li> <li>Aby uzyskać więcej informacji na temat tworzenia wirtualnego dysku twardego Windows można znaleźć tworzenia dysku VHD zgodnych z platformą Azure (z systemem Windows) sekcji znajdujący się w [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3- Create-an-Azure-Compatible-VHD-Windows-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based).</li> </ul>      |

>[!NOTE]
> Aplikacje zarządzane musi być możliwy do wdrożenia w portalu Marketplace. Jeśli komunikacji z klientami jest istotna, możesz powinni skontaktować się zainteresowanych klientów po włączeniu udostępniania.  


## <a name="next-steps"></a>Następne kroki
Jeśli użytkownik jeszcze tego nie zrobiono, 

- [Zarejestruj](https://azuremarketplace.microsoft.com/sell) w portalu marketplace

Jeśli jesteś zarejestrowanym i tworzysz nową ofertę czy działa na podstawie istniejącego

- [Zaloguj się do portalu Cloud Partner](https://cloudpartner.azure.com) do utworzenia lub zakończyć oferty
