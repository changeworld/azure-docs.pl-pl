---
title: Podręcznik publikowania oferty aplikacji zarządzanej aplikacji platformy Azure
description: W tym artykule opisano wymagania aby opublikować aplikację zarządzaną w witrynie Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: qianw211
manager: evansma
ms.service: marketplace
ms.topic: article
ms.date: 06/14/2018
ms.author: v-qiwe
ms.openlocfilehash: 29546b0969751a43959a55860fc22e9f3c3e225b
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154950"
---
# <a name="azure-applications-managed-application-offer-publishing-guide"></a>Aplikacje platformy Azure: Oferty aplikacji zarządzanej Podręcznik publikowania

Aplikacji zarządzanej jest jednym z głównych sposobów publikowania rozwiązania w portalu Marketplace. Użyj tego przewodnika, aby poznać wymagania dla tej oferty. 

Są to oferty transakcji, które są wdrożone i rozliczane za pośrednictwem portalu Marketplace. Wywołanie akcji, które użytkownik będzie widział jest "Pobierz teraz."

Korzystanie z aplikacji platformy Azure: typ oferty aplikacji zarządzanych, gdy wymagane są następujące warunki:
- Możesz wdrożyć albo rozwiązanie oparte na subskrypcji dla klienta przy użyciu maszyny Wirtualnej lub całego rozwiązania opartego na usłudze IaaS.
- Użytkownik lub klient wymaga, że rozwiązanie jest zarządzana przez partnera.

>[!NOTE]
>Na przykład partnera może być SI lub dostawcą usługi zarządzanej (MSP).  

## <a name="managed-application-offer"></a>Oferty aplikacji zarządzanych

|Wymagania |Szczegóły  |
|---------|---------|
|Wdrożone do subskrypcji platformy Azure dla klientów | Aplikacje zarządzane musi zostać wdrożony w subskrypcji klienta i mogą być zarządzane przez inną firmę. | 
|Rozliczeń oraz metod pomiarów    |  Zasoby będą aprowizowane w subskrypcji platformy Azure przez klienta. Płatność za rzeczywiste użycie (PŁATNICZEGO) maszyn wirtualnych zostanie wykonany z klientem za pośrednictwem firmy Microsoft, które są rozliczane za pośrednictwem subskrypcji platformy Azure przez klienta (PŁATNICZEGO). <br> W przypadku bring-your-own-license gdy firma Microsoft rozlicza koszty związane z infrastrukturą w subskrypcji klienta będą transact oprogramowanie opłaty dla klientów bezpośrednio.        |
|Wirtualny dysk twardy (VHD) zgodnych z platformą Azure    |   Maszyny wirtualne muszą zostać skompilowane na systemie Windows lub Linux.<ul> <ul> <li>Aby uzyskać więcej informacji na temat tworzenia wirtualnego dysku twardego systemu Linux, zobacz [dystrybucje systemu Linux zalecanych dla na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Aby uzyskać więcej informacji na temat tworzenia wirtualnego dysku twardego Windows, zobacz [utworzyć wirtualny dysk twardy zgodnych z platformą Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md).</li> </ul> |

>[!NOTE]
> Aplikacje zarządzane musi być możliwy do wdrożenia w portalu Marketplace. Jeśli komunikacji z klientami jest istotna, możesz powinni skontaktować się zainteresowanych klientów po włączeniu udostępniania.  

>[!Note]
>Dostawcy rozwiązań (CSP) partnera kanału zoptymalizowany pod kątem w chmurze jest teraz dostępna.  Zobacz [dostawców rozwiązań w chmurze](./cloud-solution-providers.md) więcej informacji na temat marketingowych oferty za pośrednictwem programu Microsoft CSP partner kanałów.

## <a name="next-steps"></a>Kolejne kroki
Jeśli użytkownik jeszcze tego nie zrobiono, 

- [Zarejestruj](https://azuremarketplace.microsoft.com/sell) w portalu marketplace.

Jeśli jesteś zarejestrowanym i tworzysz nową ofertę czy działa na podstawie istniejącego

- [Zaloguj się do portalu Cloud Partner](https://cloudpartner.azure.com) do utworzenia lub zakończyć oferty.
