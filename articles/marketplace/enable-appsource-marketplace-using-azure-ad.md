---
title: Włącz listę Microsoft AppSource i portalu Azure Marketplace przy użyciu usługi Azure Active Directory | Azure
description: Włącz typ oferty przy użyciu usługi Azure Active Directory w portalu Azure Marketplace i AppSource dla wydawcy aplikacji i usługi.
services: Azure, AppSource, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: qianw211
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 09/12/2018
ms.author: qianw211
ms.openlocfilehash: d7fd09928c0a687755d216e7f10f7eac23677c63
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2018
ms.locfileid: "45986425"
---
# <a name="enable-a-microsoft-appsource-and-azure-marketplace-listing-by-using-azure-active-directory"></a>Włącz listę Microsoft AppSource i portalu Azure Marketplace przy użyciu usługi Azure Active Directory

Microsoft Azure Active Directory (Azure AD) to usługa tożsamości w chmurze umożliwia uwierzytelnianie za pomocą konta Microsoft przy użyciu środowisk standardowych.  Aby uzyskać więcej informacji na temat usługi Azure AD, zobacz [usługi Azure Active Directory](https://azure.microsoft.com/services/active-directory).

## <a name="benefits-of-using-azure-active-directory"></a>Korzyści z używania usługi Azure Active Directory

Microsoft AppSource i portalu Azure Marketplace klienci Użyj środowiska w ramach produktu, aby wyszukać katalogach listy, które będą musieli zalogować się do produktu.  Integrowanie aplikacji z usługi Azure AD, możesz przyspieszyć zaangażowania i optymalizować środowisko klienta. Usługa Azure AD:

- Umożliwia logowanie jednokrotne (SSO) dla milionów użytkowników w przedsiębiorstwach.
- Umożliwia użytkownikom spójne środowisko logowania jednokrotnego w opublikowanych przez partnerów w różnych aplikacjach.
- Zapewnia skalowalne, Międzyplatformowe uwierzytelnianie dla aplikacji mobilnych i chmury aplikacji.

Zgodnie z opisem w dalszej części tego artykułu niektóre oferty są wymagane do wdrożenia usługi Azure AD, aby opublikować w portalu Marketplace.

## <a name="azure-active-directory-requirements"></a>Wymagania dotyczące usługi Azure Active Directory

Istnieją różne [opcje listowania ilości pamięci i stanowią typy](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type) Microsoft AppSource i portalu Azure Marketplace.  Poniżej przedstawiono wymagania platformy Azure AD, te opcje wyświetlania i typów ofert:

| **Typ oferty**    | **Wymagane logowanie Jednokrotne usługi AAD?**  |  |   |  |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
|  | Kontakt ze mną | Wersja próbna | Wersja testowa | Transakcja |
| Maszyna wirtualna | ND | Nie | Nie | Nie |
| Aplikacje platformy Azure (szablon rozwiązania)  | ND | ND | ND | ND |
| Aplikacje zarządzane  | ND | ND | ND | Nie |
| SaaS  | Nie | Yes | Yes | Yes |
| Containers  | ND | ND | ND | Nie |
| Usługi doradcze  | Nie | ND | ND | ND |

Aby uzyskać więcej informacji na temat wymagań technicznych SaaS, zobacz [aplikacji SaaS oferują Podręcznik publikowania](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide).

## <a name="integration-with-azure-active-directory"></a>Integracja z usługą Azure Active Directory

Aby uzyskać informacje na temat integracji z usługą Azure AD, aby włączyć logowanie Jednokrotne, odwiedź stronę https://aka.ms/aaddev.

Aby uzyskać więcej informacji na temat logowania jednokrotnego usługi Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)?

## <a name="enable-a-trial-listing-by-using-azure-active-directory"></a>Włączanie wersji próbnej listę przy użyciu usługi Azure Active Directory

Po klient wybierze okres próbny w witrynie Marketplace, klient jest przekierowywane do środowiska próbnego. W środowisku wersji próbnej można skonfigurować klienta bezpośrednio, bez konieczności dodatkowe kroki logowania. Oferty lub aplikacja odbiera token z usługi Azure AD podczas uwierzytelniania. Token zawiera informacje przydatne użytkownika, które są używane do utworzenia konta użytkownika w aplikacji lub oferty. Można zautomatyzować instalację klienta i zwiększyć prawdopodobieństwo konwersji.

Aby uzyskać więcej informacji na temat token, który jest wysyłany z usługi Azure AD podczas uwierzytelniania, zobacz [przykładowy tokenów](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens).

Używaj usługi Azure AD, aby włączyć uwierzytelnianie jednym kliknięciem w aplikacji lub wersji próbnej. Usługa Azure AD zapewnia następujące korzyści: 
*   Usprawnij środowiska klienta w portalu Marketplace w wersji próbnej.
*   Obsługa działania środowiska w ramach produktu, nawet wtedy, gdy użytkownik jest przekierowywany z witryny Marketplace do środowiska domeny lub wersji próbnej.
*   Zmniejszyć prawdopodobieństwo porzucania na przekierowanie, ponieważ istnieją żadne dodatkowe kroki logowania.
*   Zmniejsz bariery wdrożenia dla dużych populacji użytkowników usługi Azure AD.

### <a name="verify-your-azure-ad-integration-in-the-marketplace-multitenant-apps"></a>Sprawdź integracji usługi Azure AD w portalu Marketplace: aplikacje wielodostępne
Używaj usługi Azure AD, aby obsługiwać następujące opcje dla Twojego rozwiązania:
*   Zarejestruj swoją aplikację w sklepów w portalu Marketplace.
*   Włącz funkcję obsługi wielodostępności w usłudze Azure AD, aby korzystać z jednym kliknięciem wersji próbnej.

Aby uzyskać więcej informacji na temat rejestracji aplikacji, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).

Jeśli dopiero zaczynasz za pomocą usługi Azure AD federacyjnego pojedynczego logowania jednokrotnego (SSO), wykonaj następujące kroki:
1.  Zarejestruj swoją aplikację w portalu Marketplace. 
2.  Twórz logowanie Jednokrotne z usługą Azure AD przy użyciu protokołu OAuth 2.0 lub OpenID Connect.
    *   Aby uzyskać więcej informacji na temat protokołu OAuth 2.0, zobacz [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code).
    *   Aby uzyskać więcej informacji na temat Open ID Connect zobacz [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).
3.  Włącz funkcję obsługi wielodostępności w usłudze Azure AD, aby zapewnić środowisko próbne jednym kliknięciem.
    
    Aby uzyskać więcej informacji na temat certyfikacja usługi AppSource, zobacz [certyfikacja usługi AppSource](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified). 

### <a name="verify-your-azure-ad-integration-in-the-marketplace-single-tenant-apps"></a>Sprawdź integracji usługi Azure AD w portalu Marketplace: aplikacje z jedną dzierżawą
Umożliwia obsługę jednej z następujących opcji rozwiązania do pojedynczej dzierżawy usługi Azure AD: 
*   Dodawanie użytkowników do katalogu jako użytkowników (gości) przy użyciu usługi Azure Active Directory B2B (Azure AD B2B). Aby uzyskać więcej informacji na temat usługi Azure AD B2B, zobacz [czym jest współpraca B2B w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
*   Ręcznie skonfigurować prób dla klientów przy użyciu kontaktu mnie opcji publikowania.
*   Tworzenie wersji testowej na klienta.
*   Tworzenie wielodostępnych przykładową aplikację wersji demonstracyjnej, która korzysta z logowania jednokrotnego.

## <a name="next-steps"></a>Kolejne kroki

Jeśli użytkownik jeszcze tego nie zrobiono, 
- [Zarejestruj](https://azuremarketplace.microsoft.com/sell) w portalu marketplace.

Jeśli jesteś zarejestrowanym i tworzysz nową ofertę czy działa na podstawie istniejącego
- [Zaloguj się do portalu Cloud Partner](https://cloudpartner.azure.com/) do utworzenia lub zakończyć oferty.

