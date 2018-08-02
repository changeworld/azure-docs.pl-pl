---
title: Decyzje związane z wdrożeniem odłączonego platformy dla usługi Azure Stack zintegrowane systemy | Dokumentacja firmy Microsoft
description: Określić wdrażania planowania decyzji dotyczących wdrożeniach z wieloma węzłami podłączonej do platformy Azure, Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 4574b140e2e17462a5ff696b913bb4ef7bcb0ad0
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39412760"
---
# <a name="azure-disconnected-deployment-planning-decisions-for-azure-stack-integrated-systems"></a>Wdrożenie usługi Azure odłączonego planowanie decyzji dla usługi Azure Stack zintegrowane systemy
Po podjęciu [zostanie sposób Zintegruj usługę Azure Stack w środowisku chmury hybrydowej](azure-stack-connection-models.md), następnie można zakończyć swoje decyzje dotyczące wdrożenia usługi Azure Stack.

O odłączony z poziomu opcji wdrażania na platformie Azure, wdrażanie i używanie usługi Azure Stack bez połączenia z Internetem. Jednak z wdrożeniem odłączonego, są ograniczone do magazynu tożsamości usługi AD FS i model rozliczeń oparty na pojemności. 

Wybierz tę opcję, jeśli użytkownik:
- Mieć zabezpieczeń lub innych ograniczeń, które wymagają wdrożenia usługi Azure Stack w środowisku, w którym nie jest połączony z Internetem.
- Chcesz zablokować danych (w tym dane użycia) zostały wysłane do platformy Azure.
- Chcesz używać usługi Azure Stack wyłącznie jako rozwiązanie w chmurze prywatnej, jest wdrażana do firmowego intranetu, a nie chcesz w scenariuszach hybrydowych.

> [!TIP]
> Czasami środowisku tego typu jest również nazywane "podmorskich scenariusza".

Odłączony wdrożenia nie ściśle oznacza, że dla hybrydowe scenariusze maszyn wirtualnych dzierżawy nie może połączyć później swojego wystąpienia usługi Azure Stack na platformie Azure. Oznacza to, że nie masz łączności na platformie Azure podczas wdrażania lub nie chcesz używać usługi Azure Active Directory jako magazyn tożsamości.

## <a name="features-that-are-impaired-or-unavailable-in-disconnected-deployments"></a>Funkcje, które są naruszone lub jest niedostępna w odłączonej wdrożeń 
Usługa Azure Stack został zaprojektowany w celu działają najlepiej, jeśli połączone na platformie Azure, dlatego należy pamiętać, że istnieją pewne funkcje i możliwości, które są naruszone lub staną się całkowicie niedostępne w trybie rozłączonym. 

|Cecha|Wpływ w trybie rozłączonym|
|-----|-----|
|Wdrażanie maszyny Wirtualnej za pomocą rozszerzenia DSC, aby skonfigurować maszynę Wirtualną po wdrożeniu|Naruszone — rozszerzenie DSC szuka w Internecie najnowszej wersji platformy WMF.|
|Wdrażanie maszyny Wirtualnej za pomocą rozszerzenia platformy Docker, aby uruchamiać polecenia Docker|Naruszone — Docker sprawdzi Internetu dla najnowszej wersji i ten test zakończy się niepowodzeniem.|
|Linki do dokumentacji w portalu usługi Azure Stack|Niedostępna — łącza, takie jak uzyskać pomoc, Prześlij opinię Przewodnik Szybki Start, itp. używające internetowego adresu URL nie będzie działać.|
|Alert korygowania/ograniczenia, który odwołuje się przewodnik online korygowania|Niedostępne — wszystkie zaradcze alertu łącza używające internetowego adresu URL nie będzie działać.|
|Portal Marketplace — możliwości, aby wybrać i dodać pakiety galerii bezpośrednio w witrynie Azure Marketplace|Naruszone — podczas wdrażania usługi Azure Stack w trybie rozłączonym (bez żadnych łączności z Internetem), nie można pobrać elementów portalu marketplace, korzystając z portalu usługi Azure Stack. Można jednak użyć [narzędzie syndykacji marketplace](https://docs.microsoft.com/azure/azure-stack/azure-stack-download-azure-marketplace-item#download-marketplace-items-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity) pobieranie elementów portalu marketplace na komputerze, który ma łączność z Internetem i przenieść je do środowiska usługi Azure Stack.|
|Korzystanie z kont federacyjnych usługi Azure Active Directory do zarządzania do wdrożenia usługi Azure Stack|Niedostępne — ta funkcja wymaga łączności na platformie Azure. Zamiast tego należy użyć usług AD FS z lokalnym wystąpieniem usługi Active Directory.|
|App Services|Naruszone — aplikacji internetowych, mogą wymagać dostępu do Internetu na zaktualizowanej zawartości.|
|Interfejs wiersza polecenia (CLI)|Naruszone — interfejs wiersza polecenia obniżyła funkcji pod kątem uwierzytelniania i inicjowania obsługi zasad usługi.|
|Visual Studio — rozwiązania Cloud discovery|Naruszone — rozwiązania Cloud Discovery odnajdzie albo innej chmury lub nie będzie działać w ogóle.|
|Visual Studio – AD FS|Naruszone — tylko do programu Visual Studio Enterprise obsługuje usługi AD FS.
Telemetria|Niedostępna — dane telemetryczne dla usługi Azure Stack, jak również wszelkie pakiety galerii innych firm, które są zależne od danych telemetrycznych.|
|Certyfikaty|Niedostępne — łączność z Internetem jest wymagany dla usługi listy odwołania certyfikatów (CRL) i Online certyfikatu stan protokołu (OSCP) w kontekście protokołu HTTPS.|
|Key-Vault|Naruszone — typowy przypadek użycia usługi Key Vault jest odczyt kluczy tajnych w czasie wykonywania aplikacji. W tym aplikacja musi nazwy głównej usługi w katalogu. W usłudze Azure Active Directory zwykli użytkownicy (inni niż administratorzy) są domyślnie może dodawać jednostki usługi. W AD (przy użyciu usług AD FS) nie są one. Próg wykluczający te umieszcza w środowisku end-to-end, ponieważ jeden zawsze musi przechodzić przez administratora katalogu, aby dodać dowolną aplikację.| 

## <a name="learn-more"></a>Dowiedz się więcej
- Aby uzyskać informacji dotyczących przypadków użycia, zakupów, partnerami i dostawcami sprzętu OEM, zobacz [usługi Azure Stack](https://azure.microsoft.com/overview/azure-stack/) stronę produktu.
- Uzyskać informacje na temat planu i udostępnienia georegionu dla usługi Azure Stack zintegrowanych systemów, zobacz oficjalny dokument: [usługi Azure Stack: stanowi rozszerzenie platformy Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 
- Aby dowiedzieć się więcej o Microsoft Azure Stack, pakowania i ceny [Pobierz PDF](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf). 

## <a name="next-steps"></a>Kolejne kroki
[Integracja sieci centrum danych](azure-stack-network.md)