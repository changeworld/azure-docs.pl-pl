---
title: Zabezpieczanie PaaS w sieci web i aplikacji dla urządzeń przenośnych przy użyciu usługi Azure App Service | Dokumentacja firmy Microsoft
description: " Dowiedz się więcej o usłudze Azure App Service zabezpieczeń najlepsze rozwiązania dotyczące zabezpieczania PaaS w sieci web i aplikacji dla urządzeń przenośnych. "
services: security
documentationcenter: na
author: techlake
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: d2e606fe23a3a6eb9d2310b0932ccec8fcfc518c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="securing-paas-web-and-mobile-applications-using-azure-app-service"></a>Zabezpieczanie PaaS w sieci web i aplikacji dla urządzeń przenośnych przy użyciu usługi Azure App Service

W tym artykule omówiono Kolekcja [usłudze Azure App Service](https://azure.microsoft.com/services/app-service/) najlepsze rozwiązania dotyczące zabezpieczania PaaS w sieci web i aplikacji dla urządzeń przenośnych. Następujące najlepsze rozwiązania są uzyskiwane z wiemy z doświadczenia z platformy Azure i doświadczenia klientów, takie jak samodzielnie.

## <a name="azure-app-service"></a>Azure App Service
[Usługa aplikacji Azure](../app-service/app-service-web-overview.md) jest PaaS, oferty, która umożliwia tworzenie aplikacji sieci web i aplikacji mobilnych dla dowolnej platformy lub urządzenia i połącz się z danymi w dowolnym miejscu, w chmurze lub lokalnie. Usługi aplikacji obejmuje sieci web i mobilnych możliwości, które były wcześniej dostępne oddzielnie jako witryny sieci Web Azure oraz Azure Mobile Services. Obejmuje ona także nowe funkcje automatyzacji procesów biznesowych i hostowania interfejsów API w chmurze. Jako pojedyncza zintegrowana usługa App Service oferuje bogaty zestaw funkcji do sieci web, mobilnych i scenariuszy integracji.

## <a name="best-practices"></a>Najlepsze praktyki

Korzystając z usługi aplikacji, wykonaj następujące najlepsze rozwiązania:

- [Uwierzytelnianie za pomocą usługi Azure Active Directory (AD)](../app-service/app-service-authentication-overview.md). App Service udostępnia usługę OAuth 2.0 dla dostawcy tożsamości. OAuth 2.0 koncentruje się na prostotę developer klienta zapewniając przepływów określonych autoryzacji dla aplikacji sieci Web, aplikacje i telefony komórkowe. Usługi Azure AD używa protokołu OAuth 2.0 do autoryzowania dostępu dla urządzeń przenośnych i aplikacji sieci web.
- Ograniczanie dostępu na podstawie musi znać i co najmniej uprawnień zabezpieczeń zasad. Ograniczanie dostępu jest dla organizacji, które mają zostać wymuszone zasady zabezpieczeń dla dostępu do danych. Kontroli dostępu opartej na rolach (RBAC) może służyć do przypisywania uprawnień do użytkowników, grup i aplikacji w określonego zakresu. Aby dowiedzieć się więcej o udzielenie użytkownikom dostęp do aplikacji, zobacz [wprowadzenie do zarządzania dostępem](../role-based-access-control/overview.md).
- Ochrona kluczy. Jaka jest zabezpieczeń nie ma znaczenia w przypadku utraty klucze subskrypcji. Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Za pomocą usługi Key Vault możesz szyfrować klucze i klucze tajne (takie jak klucze uwierzytelniania, klucze konta magazynu, klucze szyfrowania danych, pliki PFX oraz hasła) przy użyciu kluczy chronionych przez sprzętowe moduły zabezpieczeń (HSM, hardware security module). W celu zapewnienia dodatkowego bezpieczeństwa możesz zaimportować lub wygenerować klucze w modułach HSM. Zobacz [usługi Azure Key Vault](../key-vault/key-vault-whatis.md) Aby dowiedzieć się więcej. Magazyn kluczy umożliwia także zarządzanie certyfikatami TLS z automatycznego odnawiania.
- Ogranicz przychodzące źródłowych adresów IP. [Środowiska usługi aplikacji](../app-service/environment/intro.md) funkcją integracji sieci wirtualnej, która pomaga ograniczyć przychodzące źródłowych adresów IP za pomocą grup zabezpieczeń sieci (NSG). Jeśli znasz sieci wirtualnych Azure (sieci wirtualne), jest możliwość, która pozwala na umieszczenie wielu zasobów platformy Azure w kontroli dostępu do sieci z systemem innym niż internet, wzajemnie obsługiwać Routing. Aby dowiedzieć się więcej, zobacz [integracji aplikacji z sieci wirtualnej platformy Azure](../app-service/web-sites-integrate-with-vnet.md).

## <a name="next-steps"></a>Kolejne kroki
W tym artykule wprowadzona w kolekcji usługi aplikacji zabezpieczeń najlepsze rozwiązania dotyczące zabezpieczania PaaS w sieci web i aplikacji dla urządzeń przenośnych. Aby dowiedzieć się więcej na temat zabezpieczania wdrożeń typu PaaS, zobacz:

- [Zabezpieczanie wdrożeń typu PaaS](security-paas-deployments.md)
- [Zabezpieczanie PaaS w sieci web i aplikacji dla urządzeń przenośnych przy użyciu usługi Azure SQL Database i SQL Data Warehouse](security-paas-applications-using-sql.md)
