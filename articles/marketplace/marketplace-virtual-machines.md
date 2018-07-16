---
title: Oferty maszyny wirtualnej publikowania Przewodnik po portalu Azure Marketplace
description: W tym artykule opisano wymagania aby opublikować maszynę wirtualną i bezpłatnej wersji próbnej oprogramowania do wdrożenia w portalu Marketplace.
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
ms.openlocfilehash: 5508b5943e116545297d91e85621d2a11a635299
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39059767"
---
# <a name="virtual-machine-offer-publishing-guide"></a>Oferty maszyny wirtualnej Podręcznik publikowania

Obrazy maszyn wirtualnych są jednym z głównych sposobów publikowania rozwiązania w portalu Azure Marketplace. Użyj tego przewodnika, aby poznać wymagania dla tej oferty. 

Są to oferty transakcji, które są wdrożone i rozliczane za pośrednictwem portalu Marketplace. Wywołanie akcji, które użytkownik będzie widział jest "Pobierz teraz."

## <a name="free-trial"></a>Bezpłatna wersja próbna 

Można rozmieścić dla użytkowników przetestować ofertę, uzyskując dostęp do licencji na oprogramowanie ograniczony czas, korzystając z modelu blling Bring Your Own License (BYOL). Poniżej przedstawiono wymagania dotyczące wdrożenia tej oferty. 

|Wymagania  |Szczegóły  |
|---------|---------|
|Okres bezpłatnej wersji próbnej i wersja próbna     |   Klient może bezpłatnie wypróbować aplikację przez ograniczony czas. Klient nie jest wymagany do zapłaty wszelkich opłat licencji lub subskrypcji oferty. Klienci nie są wymagane do zapłacenia za podstawowych produktów firmy Microsoft lub usługi. Wszystkie opcje wersji próbnej są wdrażane do subskrypcji platformy Azure. Masz wyłączną kontrolę nad optymalizację kosztów i zarządzania. Możesz wybrać bezpłatnej wersji próbnej lub interaktywne pokaz. Niezależnie od tego, możesz wybrać Twoja bezpłatna wersja próbna należy podać klienta przez wstępnie określony czas, aby wypróbować ofertę bez ponoszenia dodatkowych kosztów.|
|Można je łatwo konfigurować, gotowych do użycia rozwiązanie    |  Aplikacja musi być łatwo i szybko skonfigurować i skonfigurować.       |
|Dostępność / czas pracy    |    Twoja aplikacja SaaS lub platformy musi mieć czas działania co najmniej 99,9%.     |
|Usługa Azure Active Directory     |    Oferty muszą zezwalać na, że usługi Azure Active Directory (Azure AD) federacyjnego logowania jednokrotnego (SSO) (Azure AD Federacyjna usługa rejestracji Jednokrotnej), za zgodą włączone.     |

## <a name="test-drive"></a>Wersja testowa

Możesz wdrożyć jeden lub więcej maszyn wirtualnych za pośrednictwem infrastructure-as-a-service(IaaS) lub aplikacji SaaS. Przewodnik po zaletą wersji testowej, opcja publikowania jest automatyczne Inicjowanie obsługi maszyny wirtualnej lub całe rozwiązanie, które są prowadzone przez u partnera. Wersji testowej umożliwia oprogramowania ewaluacyjnego bez ponoszenia dodatkowych kosztów do klienta. Klient nie musi być istniejącym klientem platformy Azure zapoznawanie się z wersji próbnej środowiska. 

Adres e-mail amp-testdrive@microsoft.com na rozpoczęcie pracy. 

|Wymagania  |Szczegóły |
|---------|---------|
| Aplikacja portalu Marketplace   |    Co najmniej jednej maszyny wirtualnej za pomocą modelu IaaS lub infrastrukturą SaaS.      |

## <a name="interactive-demo"></a>Interaktywna demonstracja

Możesz podać środowisko z przewodnikiem rozwiązania klientom za pomocą interaktywny pokaz. Zaletą interaktywna demonstracja opcja publikowania jest, podaj wersję próbną interfejsu bez skomplikowane inicjowania obsługi administracyjnej złożone rozwiązania. 

## <a name="virtual-machine-offer"></a>Oferty maszyny wirtualnej

Podczas wdrażania urządzenia wirtualnego subskrypcji skojarzonych z klientem, należy użyć typu oferty maszyny wirtualnej. Maszyny wirtualne są w pełni commerce włączone za pomocą zgodnie z rzeczywistym użyciem lub bring-your-own-license (BYOL) Modele licencjonowania. Firma Microsoft obsługuje transakcji handlowych i rozlicza klienta w Twoim imieniu. Możesz skorzystać z zalet przy użyciu relacji instrument preferowanego między klienta i firmy Microsoft, w tym wszelkich umów Enterprise Agreement.

>[!NOTE]
>W tej chwili zobowiązań pieniężnych związane z umową Enterprise Agreement mogą służyć względem użycia platformy Azure, maszyny wirtualnej, ale nie w odniesieniu do opłat za oprogramowanie.  

>[!NOTE]
>Jesteś w stanie ograniczyć odnajdywanie i wdrażanie maszyny wirtualnej do określonej grupy klientów przez publikowanie obrazu i cennik oferty prywatne. Oferty prywatne odblokowywanie możliwości tworzenia ekskluzywne oferty dla najbliższej klientów i oferuje dostosowane oprogramowanie i warunki. Warunki niestandardowe umożliwiają wyróżnienia różnych scenariuszy, w tym prowadzone przez pole zajmuje specjalne ceny i warunki oraz wczesny dostęp do ograniczonej wersji oprogramowania. Oferty prywatne Włącz Cię jednak do wypróbowania konkretne informacje o cenach lub produktów, które mają ograniczony zestaw klientów przez utworzenie nowej jednostki SKU o te szczegóły.  
*   Aby uzyskać więcej informacji na temat oferty prywatne, odwiedź stronę oferty prywatne w portalu Azure Marketplace strony znajduje się w [azure.microsoft.com/blog/private-offers-on-azure-marketplace](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace).  

| Wymaganie | Szczegóły |  
|:--- |:--- | 
| Rozliczeń oraz metod pomiarów | Maszyna wirtualna musi obsługiwać comiesięcznym rozliczeniem BYOL i płatność za rzeczywiste użycie. |  
| Wirtualny dysk twardy (VHD) zgodnych z platformą Azure | Maszyny wirtualne muszą zostać skompilowane na systemie Windows lub Linux.<ul> <li>Aby uzyskać więcej informacji na temat tworzenia wirtualnego dysku twardego systemu Linux, odwiedź stronę tworzenia dysku VHD zgodnych z platformą Azure (opartych na systemie Linux) sekcji znajdujący się w [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2- Create-an-Azure-Compatible-VHD-Linux-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based).</li> <li>Aby uzyskać więcej informacji na temat tworzenia wirtualnego dysku twardego Windows można znaleźć tworzenia dysku VHD zgodnych z platformą Azure (z systemem Windows) sekcji znajdujący się w [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3- Create-an-Azure-Compatible-VHD-Windows-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based).</li> </ul> |  

## <a name="next-steps"></a>Następne kroki

Jeśli użytkownik jeszcze tego nie zrobiono, 

- [Zarejestruj](https://azuremarketplace.microsoft.com/sell) w portalu marketplace

Jeśli jesteś zarejestrowanym i tworzysz nową ofertę czy działa na podstawie istniejącego

- [Zaloguj się do portalu Cloud Partner](https://cloudpartner.azure.com) do utworzenia lub zakończyć oferty
