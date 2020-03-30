---
title: Przewodnik po publikowaniu ofert maszyn wirtualnych dla portalu Azure Marketplace
description: W tym artykule opisano wymagania dotyczące publikowania maszyny wirtualnej i wersji próbnej bez oprogramowania, które mają zostać wdrożone w portalu Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: dsindona
ms.openlocfilehash: 34de5f59e96a37282063741a1664f512697c167b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288720"
---
# <a name="virtual-machine-offer-publishing-guide"></a>Przewodnik po publikowaniu ofert maszyn wirtualnych

Obrazy maszyny wirtualnej są jednym z głównych sposobów publikowania rozwiązania w portalu Azure Marketplace. Skorzystaj z tego przewodnika, aby zrozumieć wymagania dotyczące tej oferty. 

Są to oferty transakcji, które są wdrażane i rozliczane za pośrednictwem portalu Marketplace. Wywołanie działania, które użytkownik widzi jest "Get It Now".

## <a name="free-trial"></a>Bezpłatna wersja próbna 

Użytkownik może zorganizować dla użytkowników możliwość przetestowania oferty, uzyskując dostęp do licencji na oprogramowanie o ograniczonym czasie korzystania z modelu rozliczeniowego Bring Your Own License (BYOL). 

## <a name="test-drive"></a>Wersja testowa

Można wdrożyć co najmniej jedną maszynę wirtualną za pośrednictwem aplikacji infrastruktury jako usługi (IaaS) lub oprogramowania jako usługi (SaaS). Zaletą opcji publikowania dysku testowego jest automatyczne inicjowanie obsługi administracyjnej maszyny wirtualnej lub całego rozwiązania prowadzonego przez przewodnik z przewodnikiem hostowanym przez partnera. Testowa jazda zapewnia ocenę bez dodatkowych kosztów dla klienta. Klient nie musi być istniejącym klientem platformy Azure, aby nawiązać udział w wersji próbnej. 

Skontaktuj się z nami w [amp-testdrive,](mailto:amp-testdrive@microsoft.com) aby rozpocząć. 

|Wymagania  |Szczegóły |
|---------|---------|
| Masz aplikację Marketplace   |    Co najmniej jedna maszyna wirtualna za pośrednictwem usługi IaaS lub SaaS.      |

## <a name="interactive-demo"></a>Interaktywne demo

Zapewniasz klientom doświadczenie z przewodnikiem dzięki interaktywnej demonstracji. Zaletą opcji publikowania wersji demonstracyjnej jest zapewnienie środowiska próbnego bez skomplikowanego inicjowania obsługi administracyjnej złożonego rozwiązania. 

## <a name="virtual-machine-offer"></a>Oferta maszyny wirtualnej

Użyj typu oferty maszyny wirtualnej podczas wdrażania urządzenia wirtualnego w subskrypcji skojarzonej z klientem. Maszyny wirtualne są w pełni commerce włączone przy użyciu pay-as-you-go lub bring-your-own-license (BYOL) modeli licencjonowania. Firma Microsoft obsługuje transakcję handlową i rozlicza klienta w Twoim imieniu. Użytkownik otrzymuje korzyści z korzystania z preferowanej relacji płatności między klientem a firmą Microsoft, w tym wszelkich umów Enterprise Agreement.

> [!NOTE]
> W tej chwili zobowiązania pieniężne skojarzone z umową Enterprise Agreement mogą być używane przeciwko użyciu platformy Azure maszyny wirtualnej, ale nie w odniesieniu do opłat licencyjnych za oprogramowanie.  
> 
> [!NOTE]
> Można ograniczyć odnajdowanie i wdrażanie maszyny Wirtualnej do określonego zestawu klientów, publikując obraz i ceny jako ofertę prywatną. Prywatne oferty odblokują możliwość tworzenia ekskluzywnych ofert dla najbliższych klientów i oferowania dostosowanego oprogramowania i warunków. Dostosowane warunki umożliwiają wyróżnienie różnych scenariuszy, w tym umów kierowanych w terenie z wyspecjalizowanymi cenami i warunkami, a także wczesnego dostępu do oprogramowania o ograniczonej wersji. Oferty prywatne umożliwiają podanie określonych cen lub produktów ograniczonej ofercie klientów, tworząc nową jednostkę SKU z tymi szczegółami.  
> *   Aby uzyskać więcej informacji na temat ofert prywatnych, odwiedź stronę Oferty prywatne w portalu Azure Marketplace, znajdującą się pod [adresem azure.microsoft.com/blog/private-offers-on-azure-marketplace](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace).  

| Wymaganie | Szczegóły |  
|:--- |:--- | 
| Rozliczenia i pomiary | Maszyna wirtualna musi obsługiwać miesięczne rozliczenia według systemu BYOL lub płatności zgodnie z rzeczywistym i wyjazdem. |  
| Wirtualny dysk twardy zgodny z platformą Azure (VHD) | Maszyny wirtualne muszą być zbudowane w systemie Windows lub Linux. <ul> <li>Aby uzyskać więcej informacji na temat tworzenia dysku VHD systemu Linux, zobacz [Dystrybucje systemu Linux zatwierdzone na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Aby uzyskać więcej informacji na temat tworzenia dysku VHD systemu Windows, zobacz [Tworzenie dysku VHD zgodnego z platformą Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md).</li> </ul> |  

>[!Note]
>Usługa wyboru kanału partnerskiego dostawców rozwiązań w chmurze (CSP) jest już dostępna.  Zobacz [dostawców rozwiązań w chmurze, aby](./cloud-solution-providers.md) uzyskać więcej informacji na temat marketingu oferty za pośrednictwem kanałów partnerów CSP firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

Jeśli jeszcze tego nie zrobiłeś, 

- [Zarejestruj się](https://azuremarketplace.microsoft.com/sell) na rynku.

Jeśli jesteś zarejestrowany i tworzysz nową ofertę lub pracujesz nad istniejącą,

- [Zaloguj się do portalu cloud partner,](https://cloudpartner.azure.com) aby utworzyć lub uzupełnić ofertę.
- Aby uzyskać więcej informacji, zobacz [Oferta maszyny wirtualnej.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-virtual-machine-offer)
