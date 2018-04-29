---
title: Technical aplikacji SaaS witrynę Azure Marketplace Podręcznik publikowania
description: Przewodnik krok po kroku i publikowanie list kontrolnych publikowanie aplikacji SaaS w portalu Azure Marketplace
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
documentationcenter: ''
author: BrentL-Collabera
manager: ''
editor: BrentL-Collabera
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/28/2018
ms.author: pabutler
ms.openlocfilehash: eb6db45ca0fcb6879aeaeaaf70715691cac438b0
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="saas-applications-technical-publishing-guide"></a>Podręcznik techniczny publikowania aplikacji SaaS

Witamy w portalu Azure Marketplace techniczne aplikacji SaaS Podręcznik publikowania. Ten przewodnik ułatwia candidate i istniejących wydawców, aby wyświetlić listę aplikacji i usług w portalu Azure Marketplace przy użyciu aplikacji SaaS oferty.  
Chcesz użyć oferty aplikacji SaaS, gdy rozwiązanie zostanie wdrożone w ramach własnej subskrypcji platformy Azure, a klienci powinni zalogować się za pośrednictwem interfejsu, który projektowania i zarządzania nimi przetestować aplikację. Robi to przy użyciu [usługi Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) wykorzystać istniejące środowisko wersji próbnej. Innymi słowy jest doprowadziły klienta, u partnera bezpłatnej wersji próbnej. Podstawowe znaczenie ma ich ujawniać rozwiązania w taki sposób, który daje możliwość wystąpić rozwiązaniu niezależnie nie dodatkowych opłat lub opłata kupujący chmury, a więc ten typ oferta zapewnia środowisko wersji próbnej, które odpowiadają jak klienci poszukać rozwiązań w chmurze.  

Przegląd wszystkich ofert witryny Marketplace, można znaleźć na stronie [Marketplace wydawca przewodnika](https://aka.ms/sellerguide).

## <a name="saas-application-technical-guidance"></a>Wskazówki techniczne dla aplikacji SaaS
Wymagania techniczne dla aplikacji SaaS są proste. Wydawcy tylko są wymagane do można zintegrować z usługą Azure AD do opublikowania.  Integracja usługi Azure AD z aplikacjami jest dobrze udokumentowane i firma Microsoft udostępnia wiele zestawów SDK i zasobów, w tym celu.  

Aby rozpocząć, firma Microsoft zaleca mających subskrypcję przeznaczonego do publikowania portalu Azure Marketplace, co umożliwia izolowanie pracy z innych inicjatyw. Ponadto, jeśli nie są już zainstalowane, zaleca się ma następujące narzędzia jako część środowiska programowania: 
- [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)  
- [Program Azure powerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-5.0.0)  
- [Narzędzia dla deweloperów platformy Azure (Sprawdź, co jest dostępne)](https://azure.microsoft.com/tools/)  
- [Visual Studio Code](https://code.visualstudio.com/)  

### <a name="resources"></a>Zasoby
Następujące listy zawierają łącza do najlepszych zasobów usługi Azure AD ułatwiających rozpoczęcie pracy: 

**Dokumentacja**

- [Przewodnik dewelopera usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)

- [Integracja z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate)

- [Integrowanie aplikacji z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

- [Mapa usługi Azure - bezpieczeństwo i tożsamość](https://azure.microsoft.com/roadmap/?category=security-identity)

**Wideo**

- [Uwierzytelniania usługi Azure Active Directory za pomocą Vittorio Bertocci](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration)

- [Usługa Azure Active Directory tożsamości techniczna widoków — część 1 z 2](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration)

- [Usługa Azure Active Directory tożsamości techniczna widoków — część 2 z 2](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration)

- [Tworzenie aplikacji z usługą Microsoft Azure Active Directory](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration)

- [Microsoft Azure wideo koncentruje się na usłudze Active Directory](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

**Szkolenia**  
- [Microsoft Azure dla serii zawartości specjalistom IT: Azure Active Directory](https://mva.microsoft.com/en-US/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

**Aktualizacje usługi Azure Active Directory**  
- [Aktualizacje usługi Azure AD](https://azure.microsoft.com/updates/?product=active-directory)

Aby uzyskać pomoc można użyć następujących zasobów:
- [Fora MSDN](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
- [StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="the-azure-active-directory-gallery"></a>Galerii Azure Active Directory
Oprócz wymienionych w Azure Marketplace/AppSource aplikacji, może także zawierać aplikacji wymienionych w galerii aplikacji usługi Azure AD, która jest częścią sklepu AppStore Azure Marketplace. Klienci, którzy korzystają z usługi Azure AD jako dostawcy tożsamości można znaleźć innego łączników aplikacji SaaS opublikowane w tym miejscu. Administratorzy IT dodać łączniki z galerii aplikacji, a następnie skonfigurować i używać łączników rejestracji jednokrotnej (SSO) i udostępniania. Usługi Azure AD obsługuje wszystkie protokoły głównych federacyjnego logowania jednokrotnego, w tym SAML 2.0, OpenID Connect, OAuth i WS-Fed.  

Po przetestowaniu czy integracją aplikacji współpracuje z usługą Azure AD, należy przesłać żądanie dostępu w portalu sieci aplikacji. Jeśli masz konto usługi Office 365, używać, aby się zalogować portalu. Jeśli nie masz konta usługi Office 365, można użyć Twojego konta Microsoft (takich jak Outlook lub usługi Hotmail) do logowania.

## <a name="program-benefits"></a>Korzyści z programu
- Zapewnia najlepsze możliwe pojedynczego logowania jednokrotnego dla klientów
- Proste i minimalnej konfiguracji aplikacji
- Klientów można wyszukać aplikacji i znajdź go w galerii
- Każdy klient można użyć tej integracji niezależnie od wersji produktu usługi Azure AD używają (wolne, podstawowa lub Premium)
- Samouczek krok po kroku konfiguracji dla naszych klientów wzajemne
- Umożliwia inicjowanie obsługi użytkowników dla tej samej aplikacji, jeśli są przy użyciu SCIM

## <a name="prerequisites"></a>Wymagania wstępne
Aby wyświetlić listę aplikacji w galerii Azure AD, aplikacja najpierw muszą implementować jeden z federacyjnego protokoły obsługiwane przez usługę Azure AD. Przeczytaj warunki i postanowienia znajdujący się w galerii aplikacji Azure AD [informacje prawne Microsoft Azure](https://azure.microsoft.com/support/legal/).  

Poniżej przedstawiono dodatkowe informacje o wymaganiach wstępnych, w zależności od tego, który protokół używasz:

**OpenID Connect** — tworzenie wielodostępnych aplikacji w usłudze Azure AD i zaimplementować framework zgody dla aplikacji. Wyślij żądanie logowania do wspólnego punktu końcowego tak, aby każdy klient może zapewnić zgody do aplikacji. Można kontrolować dostęp użytkownika klienta na podstawie Identyfikatora dzierżawy i nazwy UPN użytkownika odebrane w tokenie.  
**SAML 2.0 i WS-Fed** -aplikacji powinien mieć możliwość czy integrację SAML lub logowania jednokrotnego usługi WS-Fed w trybie SP lub dostawców tożsamości.
