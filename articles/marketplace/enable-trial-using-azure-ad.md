---
title: Włącz używanie programu Azure AD w wersji próbnej | Azure
description: Włącz typ oferty wersji próbnej przy użyciu usługi Azure Active Directory (Azure AD) w portalu Azure Marketplace i AppSource wydawcy aplikacji i usług
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: jm-aditi-ms
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/04/2018
ms.author: ellacroi
ms.openlocfilehash: 4140ba98c0c65c22674c61dc7266818af904e777
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2018
ms.locfileid: "34825232"
---
# <a name="enable-trial-using-azure-ad"></a>Włącz używanie programu Azure AD w wersji próbnej  
Azure Active Directory (Azure AD) to usługa tożsamości chmury, która umożliwia uwierzytelnianie z programu Microsoft konto służbowe przy użyciu standardowych ramy: OAuth i OpenID Connect.  
*   Aby uzyskać więcej informacji na temat usługi Azure AD, odwiedź stronę usługi Azure Active Directory strony znajduje się w [azure.microsoft.com/services/active-directory](https://azure.microsoft.com/services/active-directory).  

Klientom są uwierzytelniane w witrynie marketplace, za pomocą usługi Azure AD. Po klient wybiera okresu próbnego wyświetlania w portalu marketplace, klient jest przekierowywany do środowiska wersji próbnej.  W środowisku wersji próbnej można skonfigurować klienta bezpośrednio, bez konieczności dodatkowe kroki logowania. Oferty lub aplikacja odbiera token z usługi Azure AD podczas uwierzytelniania, która zawiera użytkownika cenne informacje dotyczące tworzenia konta użytkownika w aplikacji lub oferty. Można zautomatyzować konfigurację sieci i zwiększa prawdopodobieństwo konwersji.  
*   Aby uzyskać więcej informacji o tokenie wysyłane z usługi Azure AD podczas uwierzytelniania, odwiedź stronę tokeny próbki znajdującej się na [docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens)

Użyj usługi Azure AD, aby włączyć uwierzytelnianie jednym kliknięciem w aplikacji lub w wersji próbnej.  
*   Upraszczanie wrażenia z witryny marketplace do wersji próbnej.  
*   Obsługa działania obsługi produktu, nawet wtedy, gdy użytkownik zostaje przekierowany z witryny Marketplace do środowiska domeny lub wersji próbnej.  
*   Zmniejszają prawdopodobieństwo występowania porzucania na przekierowanie, ponieważ nie ma żadnych dodatkowych czynności logowania.  
*   Zmniejsz bariery wdrożenia dla dużej liczby użytkowników usługi Azure AD.  

## <a name="verify-your-azure-ad-integration-on-the-marketplace-multitenant-apps"></a>Sprawdź Your Azure integracji z usługą AD w witrynie marketplace: wielodostępnej aplikacji  
Następujące opcje pomocy technicznej dla rozwiązania przy użyciu usługi Azure AD.  
*   Zarejestrować aplikację w sklepy w witrynie marketplace.  
*   Włącz funkcję wielodostępności w usłudze Azure AD, aby uzyskać środowisko wersji próbnej jednym kliknięciem.  
    *   Aby uzyskać więcej informacji na temat rejestracji aplikacji można znaleźć aplikacji integrowanie w usłudze Azure Active Directory strony znajduje się w [docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  

Jeśli dopiero zaczynasz przy użyciu usługi Azure AD federacyjne logowanie jednokrotne (SSO), a następnie wykonaj następujące kroki.  
1.  Zarejestrować aplikację w witrynie marketplace. 
2.  Tworzenie rejestracji Jednokrotnej z usługą Azure AD przy użyciu OAuth 2.0 lub OpenID Connect.  
    *   Aby uzyskać więcej informacji na temat protokołu OAuth 2.0, odwiedź stronę OAuth 2.0 strony znajduje się w [docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code).  
    *   Aby uzyskać więcej informacji na temat Open ID Connect, odwiedź stronę OpenID Connect strony znajduje się w [docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).  
3.  Włącz funkcję wielodostępności w usłudze Azure AD, aby zapewnić środowisko wersji próbnej jednym kliknięciem.  
    *   Aby uzyskać więcej informacji na temat AppSource certyfikacji, odwiedź stronę AppSource strony certyfikacji znajdujący się w [docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified). 

## <a name="verify-your-azure-ad-integration-on-the-marketplace-single-tenant-apps"></a>Sprawdź Your Azure integracji z usługą AD w witrynie marketplace: aplikacje pojedynczej dzierżawy  
Obsługuje jedną z następujących opcji rozwiązania do pojedynczej dzierżawy.  
*   Dodawanie użytkowników do katalogu jako goście przy użyciu B2B usługi Azure AD.  
    *   Aby uzyskać więcej informacji na temat B2B usługi Azure AD, odwiedź stronę co to jest B2B usługi Azure AD, współpraca strony znajduje się w [docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
*   Ręcznie skonfiguruj prób dla klientów korzystających z mnie kontaktu.  
*   Tworzenie dysku testu na klienta.  
*   Tworzenie wielodostępnych przykładową aplikację pokaz logowania jednokrotnego.  

## <a name="next-steps"></a>Kolejne kroki
*   Odwiedź stronę [AppSource wydawcy przewodnik i portalu Azure Marketplace](./marketplace-publishers-guide.md) strony.  
 
---  

