---
title: Zabezpieczanie PaaS w sieci web i aplikacji mobilnych za pomocą usługi Azure App Service | Dokumentacja firmy Microsoft
description: 'Informacje na temat zabezpieczeń w usłudze Azure App Service najlepsze rozwiązania dotyczące zabezpieczania usługi PaaS w sieci web i aplikacji mobilnych. '
services: security
documentationcenter: na
author: techlake
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: terrylan
ms.openlocfilehash: 6e5034d0ff8f14a9fc381f6fd1a214a91ad4d1ed
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60444405"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-app-service"></a>Najlepsze rozwiązania dotyczące zabezpieczania PaaS w sieci web i mobilnych przy użyciu usługi Azure App Service

W tym artykule omawiane jest kolekcją [usługi Azure App Service](../app-service/overview.md) najlepsze rozwiązania dotyczące zabezpieczania PaaS w sieci web i aplikacji mobilnych. Następujące najlepsze rozwiązania są uzyskiwane z naszych doświadczeń z platformą Azure i procesy, przez klientów, takich jak samodzielnie.

Usługa Azure App Service to oferta typu platform-as-a-service (PaaS), która umożliwia tworzenie aplikacji internetowych i mobilnych dla dowolnej platformy lub urządzenia i nawiązać połączenie z danymi przechowywanymi w chmurze lub lokalnie. Usługa App Service obejmuje sieci web i mobilnych możliwości, które były wcześniej dostępne oddzielnie jako usługi Azure Websites i Azure Mobile Services. Obejmuje ona także nowe funkcje automatyzacji procesów biznesowych i hostowania interfejsów API w chmurze. Jako pojedyncza zintegrowana usługa App Service oferuje bogaty zestaw funkcji do sieci web, mobilnych i scenariuszy integracji.

## <a name="authenticate-through-azure-active-directory-ad"></a>Uwierzytelnianie za pomocą usługi Azure Active Directory (AD)
Usługa App Service udostępnia usługę OAuth 2.0 dla dostawcy tożsamości. OAuth 2.0 koncentruje się na prostotę dewelopera klienta przy jednoczesnym zapewnieniu przepływów określonych autoryzacji dla aplikacji sieci web, aplikacji klasycznych i telefony komórkowe. Usługa Azure AD używa protokołu OAuth 2.0 umożliwia autoryzowanie dostępu dla urządzeń przenośnych i aplikacji sieci web. Aby dowiedzieć się więcej, zobacz [uwierzytelnianie i autoryzacja w usłudze Azure App Service](../app-service/overview-authentication-authorization.md).

## <a name="restrict-access-based-on-role"></a>Ograniczanie dostępu na podstawie roli 
Ograniczanie dostępu jest dla organizacji, które chcesz wymuszać zasady zabezpieczeń dostępu do danych. Kontroli dostępu opartej na rolach (RBAC) można użyć do przypisywania uprawnień do użytkowników, grup i aplikacji w określonym zakresie, takie jak konieczność znajomości i zasad zabezpieczeń w usłudze najniższych uprawnień. Aby dowiedzieć się więcej o udzielenie użytkownikom dostępu do aplikacji, zobacz [co to jest kontrola dostępu oparta na rolach](../role-based-access-control/overview.md).

## <a name="protect-your-keys"></a>Ochrona kluczy
Nie ma znaczenia, jak dobre jest bezpieczeństwa w przypadku utraty klucze subskrypcji. Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Usługa Key Vault można szyfrować klucze i wpisy tajne (takie jak klucze uwierzytelniania, klucze konta magazynu, klucze szyfrowania danych. Pliki PFX oraz hasła) przy użyciu kluczy chronionych przez sprzętowe moduły zabezpieczeń (HSM). W celu zapewnienia dodatkowego bezpieczeństwa możesz zaimportować lub wygenerować klucze w modułach HSM. Usługi Key Vault umożliwia także zarządzanie certyfikaty protokołu TLS z automatycznego odnawiania. Zobacz [co to jest usługa Azure Key Vault](../key-vault/key-vault-whatis.md) Aby dowiedzieć się więcej. 

## <a name="restrict-incoming-source-ip-addresses"></a>Ogranicz przychodzące źródłowych adresów IP
[Środowiska usługi App Service](../app-service/environment/intro.md) ma funkcji integracji sieci wirtualnej, która pomaga ograniczyć przychodzące źródłowych adresów IP za pomocą sieciowych grup zabezpieczeń (NSG). Jeśli jesteś zaznajomiony z sieciami wirtualnymi platformy Azure (Vnet), to funkcja, która pozwala na umieszczenie wielu zasobów platformy Azure w sieci-internet, podlegającego routingowi, która umożliwia kontrolę dostępu do. Aby dowiedzieć się więcej, zobacz [Integrowanie aplikacji z usługą Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md).

## <a name="next-steps"></a>Kolejne kroki
Ten artykuł zawiera wprowadzenie do kolekcji usługi App Service najlepsze rozwiązania dotyczące zabezpieczania PaaS w sieci web i aplikacji mobilnych. Aby dowiedzieć się więcej na temat zabezpieczanie wdrożeń PaaS, zobacz:

- [Zabezpieczanie wdrożeń typu PaaS](security-paas-deployments.md)
- [Zabezpieczanie PaaS baz danych na platformie Azure](security-paas-applications-using-sql.md)
