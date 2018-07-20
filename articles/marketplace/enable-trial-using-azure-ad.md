---
title: Włączanie wersji próbnej w witrynie Azure Marketplace przy użyciu usługi Azure Active Directory | Azure
description: Włączanie wersji próbnej typ oferty przy użyciu usługi Azure Active Directory w portalu Azure Marketplace i AppSource dla wydawcy aplikacji i usługi.
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
ms.openlocfilehash: c5b7b4967c1acef733d366e651d50706db42aace
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2018
ms.locfileid: "39160471"
---
# <a name="enable-a-trial-listing-by-using-azure-active-directory"></a>Włączanie wersji próbnej listę przy użyciu usługi Azure Active Directory

Azure Active Directory (Azure AD) to chmurze Usługa zarządzania tożsamościami, która umożliwia uwierzytelnianie za pomocą programu Microsoft konta służbowego przy użyciu środowisk standardowych. Usługa Azure AD obsługuje uwierzytelnianie OAuth i OpenID Connect. [Portalu Azure Marketplace](https://azuremarketplace.microsoft.com) używa usługi Azure AD do uwierzytelniania Tobie i Twoim klientom.

Aby uzyskać więcej informacji na temat usługi Azure AD, zobacz [usługi Azure Active Directory](https://azure.microsoft.com/services/active-directory).

Po klient wybierze okres próbny w witrynie Marketplace, klient jest przekierowywane do środowiska próbnego. W środowisku wersji próbnej można skonfigurować klienta bezpośrednio, bez konieczności dodatkowe kroki logowania. Oferty lub aplikacja odbiera token z usługi Azure AD podczas uwierzytelniania. Token zawiera informacje przydatne użytkownika, które są używane do utworzenia konta użytkownika w aplikacji lub oferty. Można zautomatyzować instalację klienta i zwiększyć prawdopodobieństwo konwersji.

Aby uzyskać więcej informacji na temat token, który jest wysyłany z usługi Azure AD podczas uwierzytelniania, zobacz [przykładowy tokenów](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens).

Używaj usługi Azure AD, aby włączyć uwierzytelnianie jednym kliknięciem w aplikacji lub wersji próbnej. Usługa Azure AD zapewnia następujące korzyści: 
*   Usprawnij środowiska klienta w portalu Marketplace w wersji próbnej.
*   Obsługa działania środowiska w ramach produktu, nawet wtedy, gdy użytkownik jest przekierowywany z witryny Marketplace do środowiska domeny lub wersji próbnej.
*   Zmniejszyć prawdopodobieństwo porzucania na przekierowanie, ponieważ istnieją żadne dodatkowe kroki logowania.
*   Zmniejsz bariery wdrożenia dla dużych populacji użytkowników usługi Azure AD.

## <a name="verify-your-azure-ad-integration-in-the-marketplace-multitenant-apps"></a>Sprawdź integracji usługi Azure AD w portalu Marketplace: aplikacje wielodostępne
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

## <a name="verify-your-azure-ad-integration-in-the-marketplace-single-tenant-apps"></a>Sprawdź integracji usługi Azure AD w portalu Marketplace: aplikacje z jedną dzierżawą
Umożliwia obsługę jednej z następujących opcji rozwiązania do pojedynczej dzierżawy usługi Azure AD: 
*   Dodawanie użytkowników do katalogu jako użytkowników (gości) przy użyciu usługi Azure Active Directory B2B (Azure AD B2B).
    
    Aby uzyskać więcej informacji na temat usługi Azure AD B2B, zobacz [czym jest współpraca B2B w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
*   Ręcznie skonfigurować prób dla klientów przy użyciu kontaktu mnie opcji publikowania.
*   Tworzenie wersji testowej na klienta.
*   Tworzenie wielodostępnych przykładową aplikację wersji demonstracyjnej, która korzysta z logowania jednokrotnego.

## <a name="next-steps"></a>Kolejne kroki
*   Przegląd [Podręcznik publikowania w portalu Azure Marketplace i AppSource](./marketplace-publishers-guide.md).
