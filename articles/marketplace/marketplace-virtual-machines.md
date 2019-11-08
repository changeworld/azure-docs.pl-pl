---
title: Przewodnik publikowania oferty maszyny wirtualnej dla witryny Azure Marketplace
description: W tym artykule opisano wymagania dotyczące publikowania maszyny wirtualnej i bezpłatnej wersji próbnej oprogramowania do wdrożenia z portalu Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: ellacroi
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 07/09/2018
ms.author: ellacroi
ms.openlocfilehash: 0f2047501b6c57b2bb98ff7e3c56498417644324
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818991"
---
# <a name="virtual-machine-offer-publishing-guide"></a>Przewodnik publikowania oferty maszyny wirtualnej

Obrazy maszyn wirtualnych to jeden z głównych sposobów publikowania rozwiązania w portalu Azure Marketplace. Skorzystaj z tego przewodnika, aby poznać wymagania dotyczące tej oferty. 

Są to oferty transakcji, które są wdrażane i rozliczane za pomocą witryny Marketplace. Wywołanie akcji, którą widzi użytkownik, to "Pobierz teraz".

## <a name="free-trial"></a>Bezpłatna wersja próbna 

W celu przetestowania oferty przez użytkowników z ograniczonymi licencjami na oprogramowanie należy skorzystać w przypadku korzystania z modelu rozliczeń własnych licencji (BYOL). Poniżej przedstawiono wymagania dotyczące wdrażania tej oferty. 

|Wymagania  |Szczegóły  |
|---------|---------|
|Bezpłatny okres próbny i środowisko próbne     |   Klienci mogą bezpłatnie próbować korzystać z aplikacji przez ograniczony czas. Klient musi zwrócić uwagę na opłaty za licencję lub subskrypcję oferty. Klienci nie muszą zapłacenia za dany produkt lub usługę pierwszej firmy Microsoft. Wszystkie opcje wersji próbnej są wdrażane w ramach subskrypcji platformy Azure. Użytkownik ma wyłączną kontrolę nad optymalizacją kosztów i zarządzaniem. Możesz wybrać bezpłatną wersję próbną lub pokaz interaktywny. Niezależnie od tego, co zostało wybrane, bezpłatna wersja próbna musi zapewniać klientom wstępnie ustalony czas na wypróbowanie oferty bez dodatkowych kosztów.|
|Łatwe do skonfigurowania rozwiązanie gotowe do użycia    |  Twoja aplikacja musi być prosta i szybka konfiguracja i konfiguracja.       |
|Dostępność/czas pracy    |    Twoja aplikacja SaaS lub platforma musi mieć czas przestoju wynoszącą co najmniej 99,9% czasu.     |
|Usługa Azure Active Directory     |    Twoja oferta musi zezwalać na Azure Active Directory (Azure AD) federacyjne Logowanie jednokrotne (SSO) (Logowanie jednokrotne usługi Azure AD) z włączoną opcją zgody.     |

## <a name="test-drive"></a>Wersja testowa

Wdrażasz co najmniej jedną maszynę wirtualną za pomocą aplikacji typu "infrastruktura jako usługa" (IaaS) lub "oprogramowanie jako usługa" (SaaS). Zaletą opcji publikowania wersji testowej jest automatyczne Inicjowanie obsługi maszyny wirtualnej lub całego rozwiązania z przewodnikiem obsługiwanym przez partnera. Na dysku testowym jest dostępna Ocena bez dodatkowych kosztów dla klienta. Klient nie musi być istniejącym klientem platformy Azure, aby móc współpracować z wersją próbną. 

Skontaktuj się z nami pod adresem [amp-testdrive](mailto:amp-testdrive@microsoft.com) , aby rozpocząć pracę. 

|Wymagania  |Szczegóły |
|---------|---------|
| Masz aplikację Marketplace   |    Co najmniej jedna maszyna wirtualna za poorednictwem IaaS lub SaaS.      |

## <a name="interactive-demo"></a>Interaktywna demonstracja

W przypadku korzystania z rozwiązania z przewodnikiem dla klientów możesz skorzystać z interaktywnej prezentacji. Zaletą opcji publikowania interaktywnej demonstracyjnej jest udostępnienie wersji próbnej bez skomplikowanej aprowizacji złożonego rozwiązania. 

## <a name="virtual-machine-offer"></a>Oferta maszyny wirtualnej

Podczas wdrażania urządzenia wirtualnego w ramach subskrypcji skojarzonej z klientem Użyj typu oferty maszyny wirtualnej. Maszyny wirtualne są w pełni włączane, korzystając z modeli licencjonowania płatność zgodnie z rzeczywistym użyciem lub BYOL. Firma Microsoft hostuje transakcję handlową i rozlicza klienta w Twoim imieniu. Korzyści wynikające z korzystania z preferowanej relacji płatności między klientem i firmą Microsoft, w tym dowolnymi umowami Enterprise Agreement.

> [!NOTE]
> W tym momencie zobowiązania pieniężne związane z Umowa Enterprise mogą być używane w odniesieniu do użycia platformy Azure przez maszynę wirtualną, ale nie do opłat za Licencjonowanie oprogramowania.  
> 
> [!NOTE]
> Możesz ograniczyć odnajdywanie i wdrażanie maszyny wirtualnej do określonego zestawu klientów, publikując obraz i ceny jako ofertę prywatną. W ofertach prywatnych można odblokować możliwość tworzenia ofert wyłącznych dla najbliższych klientów i oferowania niestandardowych programów oraz warunków. Dostosowane warunki umożliwiają wyróżnianie różnorodnych scenariuszy, w tym w przypadku, gdy użytkownik korzysta z wyspecjalizowanych cen i warunków oraz wczesnego dostępu do ograniczonej wersji oprogramowania. Oferty prywatne umożliwiają podanie określonych cen lub produktów do ograniczonego zestawu klientów przez utworzenie nowej jednostki SKU z tymi szczegółami.  
> *   Aby uzyskać więcej informacji o ofertach prywatnych, odwiedź stronę oferty prywatne w witrynie Azure Marketplace w witrynie [Azure.Microsoft.com/blog/Private-offers-on-Azure-Marketplace](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace).  

| Wymaganie | Szczegóły |  
|:--- |:--- | 
| Rozliczenia i pomiary | Maszyna wirtualna musi obsługiwać miesięczne rozliczenie BYOL lub płatność zgodnie z rzeczywistym użyciem. |  
| Wirtualny dysk twardy (VHD) zgodny z platformą Azure | Maszyny wirtualne muszą być wbudowane w system Windows lub Linux. <ul> <li>Aby uzyskać więcej informacji na temat tworzenia wirtualnego dysku twardego z systemem Linux, zobacz [dystrybucje systemu Linux zatwierdzone na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Aby uzyskać więcej informacji na temat tworzenia wirtualnego dysku twardego systemu Windows, zobacz [Tworzenie dysku VHD zgodnego z platformą Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md).</li> </ul> |  

>[!Note]
>Niedostępność kanału partnera dostawcy rozwiązań w chmurze (CSP) jest teraz dostępna.  Zobacz [dostawcy rozwiązań w chmurze](./cloud-solution-providers.md) , aby uzyskać więcej informacji na temat marketingu oferty przez kanały partnerskie programu Microsoft CSP.

## <a name="next-steps"></a>Następne kroki

Jeśli jeszcze tego nie zrobiono, 

- [Zarejestruj](https://azuremarketplace.microsoft.com/sell) się w portalu Marketplace.

Jeśli jesteś zarejestrowanym użytkownikiem i tworzysz nową ofertę lub pracujesz nad istniejącym,

- [Zaloguj się do Portal Cloud partner](https://cloudpartner.azure.com) , aby utworzyć lub zakończyć swoją ofertę.
- Aby uzyskać więcej informacji, zobacz temat [Oferta maszyny wirtualnej](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-virtual-machine-offer) .
