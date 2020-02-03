---
title: Jak skonfigurować Logowanie jednokrotne do aplikacji serwera proxy aplikacji
description: Jak można skonfigurować logowanie jednokrotne do aplikacji serwera proxy aplikacji szybko
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: mimart
ms.reviewer: japere, asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 589b3e51f27147f0a0432b61c22a024c202e388b
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712021"
---
# <a name="how-to-configure-single-sign-on-to-an-application-proxy-application"></a>Jak skonfigurować logowanie jednokrotne do aplikacji serwera Proxy aplikacji

Logowanie jednokrotne (SSO) umożliwia użytkownikom dostęp do aplikacji bez uwierzytelniania wiele razy. Umożliwia pojedynczego uwierzytelniania w chmurze w usłudze Azure Active Directory oraz umożliwia usługi lub łącznik do personifikacji użytkownika, aby zakończyć wszelkie problemy dodatkowego uwierzytelniania z aplikacji.

## <a name="how-to-configure-single-sign-on"></a>Konfigurowanie logowania jednokrotnego na
Aby skonfigurować logowanie Jednokrotne, najpierw upewnij się, że aplikacja jest skonfigurowana do uwierzytelniania wstępnego za pomocą usługi Azure Active Directory. Aby przeprowadzić tę konfigurację, przejdź do pozycji **Azure Active Directory** -&gt; **aplikacji dla przedsiębiorstw** -&gt; **wszystkie aplikacje** -&gt; aplikacji-&gt; **serwerze proxy aplikacji**. Na tej stronie można znaleźć w polu "Uwierzytelniania Pre" i upewnij się, która jest ustawiona na "Azure Active Directory. 

Aby uzyskać więcej informacji na temat metod wstępnego uwierzytelniania, zobacz krok 4 [dokumentu publikowanie aplikacji](application-proxy-add-on-premises-application.md).

   ![Metoda uwierzytelniania wstępnego w witrynie Azure portal](./media/application-proxy-config-sso-how-to/app-proxy.png)

## <a name="configuring-single-sign-on-modes-for-application-proxy-applications"></a>Konfigurowanie trybów rejestracji jednokrotnej dla aplikacji serwera Proxy aplikacji
Skonfiguruj typ logowania jednokrotnego. Metody logowania są klasyfikowane, oparte na korzysta z rodzaju uwierzytelniania aplikacji zaplecza. Aplikacje serwera Proxy aplikacji obsługują trzy rodzaje logowania jednokrotnego:

-   **Logowanie oparte na haśle**: logowanie oparte na hasłach może być używane dla każdej aplikacji, która używa pól username i Password do logowania. Kroki konfiguracji znajdują się w sekcji Konfigurowanie logowania jednokrotnego [dla aplikacji w galerii usługi Azure AD](configure-password-single-sign-on-non-gallery-applications.md).

-   **Zintegrowane uwierzytelnianie systemu Windows**: w przypadku aplikacji korzystających ze zintegrowanego uwierzytelniania systemu Windows (IWA) Logowanie jednokrotne jest włączane przy użyciu ograniczonego delegowania protokołu Kerberos (KCD). Ta metoda zapewnia uprawnieniu łączników serwera Proxy aplikacji w usłudze Active Directory na personifikowanie użytkowników oraz do wysyłania i odbierania tokenów w ich imieniu. Szczegółowe informacje na temat konfigurowania KCD można znaleźć w temacie Logowanie jednokrotne [z dokumentacją KCD](application-proxy-configure-single-sign-on-with-kcd.md).

-   **Logowanie oparte na nagłówkach**: logowanie oparte na nagłówkach jest włączane za pośrednictwem partnerstwa i wymaga dodatkowej konfiguracji. Aby uzyskać szczegółowe informacje na temat partnerstwa i instrukcje krok po kroku dotyczące konfigurowania logowania jednokrotnego do aplikacji korzystającej z nagłówków do uwierzytelniania, zobacz [dokumentację usługi PingAccess for Azure AD](application-proxy-configure-single-sign-on-with-ping-access.md).

-   **Logowanie jednokrotne SAML**: z logowaniem jednokrotnym SAML usługa Azure AD jest uwierzytelniana w aplikacji przy użyciu konta usługi Azure AD użytkownika. Usługa Azure AD komunikuje się informacji logowania jednokrotnego do aplikacji za pośrednictwem protokołu połączenia. Korzystając z logowania jednokrotnego opartego na protokole SAML, można mapować użytkowników na określone role aplikacji na podstawie reguł zdefiniowanych w oświadczeniach SAML. Aby uzyskać informacje na temat konfigurowania logowania jednokrotnego w protokole SAML, zobacz [SAML dla logowania jednokrotnego przy użyciu serwera proxy aplikacji](application-proxy-configure-single-sign-on-on-premises-apps.md).

Każdą z tych opcji można znaleźć, przechodząc do aplikacji w "aplikacje dla przedsiębiorstw" i otwierając stronę **logowania** jednokrotnego w menu po lewej stronie. Należy pamiętać, że jeśli aplikacja została utworzona w starym portalu, mogą nie być wyświetlane wszystkie te opcje.

Na tej stronie zostanie również wyświetlony jedną dodatkową opcję logowania jednokrotnego: połączonego logowania jednokrotnego. Ta opcja jest również obsługiwana przez serwer Proxy aplikacji. Jednak ta opcja nie dodaje logowanie jednokrotne do aplikacji. Inaczej mówiąc, że aplikacja może już logowanie jednokrotne implementowane za pomocą innej usługi, takie jak Active Directory Federation Services. 

Ta opcja umożliwia administratora do tworzenia użytkowników gruntów pierwszy link do aplikacji na podczas uzyskiwania dostępu do aplikacji. Na przykład w przypadku aplikacji, która jest skonfigurowana do uwierzytelniania użytkowników za pomocą Active Directory Federation Services 2.0, administrator może użyć opcji "połączone logowanie" Aby utworzyć link do niego w panelu dostępu.

## <a name="next-steps"></a>Następne kroki
- [Przechowywanie haseł dla logowania jednokrotnego przy użyciu serwera proxy aplikacji](application-proxy-configure-single-sign-on-password-vaulting.md)
- [Ograniczone delegowanie protokołu Kerberos na potrzeby logowania jednokrotnego przy użyciu serwera proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md)
- [Uwierzytelnianie oparte na nagłówkach logowania jednokrotnego przy użyciu serwera proxy aplikacji](application-proxy-configure-single-sign-on-with-ping-access.md) 
- Protokół [SAML do logowania jednokrotnego przy użyciu serwera proxy aplikacji](application-proxy-configure-single-sign-on-on-premises-apps.md).
