---
title: Jak skonfigurować logowanie jednokrotne w aplikacji serwera proxy aplikacji
description: Jak szybko skonfigurować logowanie jednokrotne w aplikacji proxy aplikacji
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76712021"
---
# <a name="how-to-configure-single-sign-on-to-an-application-proxy-application"></a>Jak skonfigurować logowanie jednokrotne w aplikacji serwera proxy aplikacji

Logowanie jednokrotne umożliwia użytkownikom dostęp do aplikacji bez wielokrotnego uwierzytelniania. Umożliwia pojedyncze uwierzytelnianie występuje w chmurze, w usłudze Azure Active Directory i umożliwia usługi lub łącznika personifikacji użytkownika, aby zakończyć wszelkie dodatkowe wyzwania uwierzytelniania z aplikacji.

## <a name="how-to-configure-single-sign-on"></a>Jak skonfigurować logowanie jednokrotne
Aby skonfigurować sytuaj, najpierw upewnij się, że aplikacja jest skonfigurowana do uwierzytelniania wstępnego za pośrednictwem usługi Azure Active Directory. Aby wykonać tę konfigurację, przejdź do **usługi Azure Active Directory**  - &gt; **Enterprise Applications**  - &gt; **Wszystkie aplikacje**  - &gt; Serwer proxy ** - &gt; aplikacji**. Na tej stronie zobaczysz pole "Uwierzytelnianie wstępne" i upewnij się, że jest ustawione na "Usługa Azure Active Directory.On this page, you see the "Pre Authentication" field, and sure that is set to "Azure Active Directory. 

Aby uzyskać więcej informacji na temat metod uwierzytelniania wstępnego, zobacz krok 4 [dokumentu publikowania aplikacji](application-proxy-add-on-premises-application.md).

   ![Metoda wstępnego uwierzytelniania w witrynie Azure portal](./media/application-proxy-config-sso-how-to/app-proxy.png)

## <a name="configuring-single-sign-on-modes-for-application-proxy-applications"></a>Konfigurowanie trybów logowania jednokrotnego dla aplikacji proxy aplikacji
Skonfiguruj określony typ logowania jednokrotnego. Metody logowania są klasyfikowane na podstawie typu uwierzytelniania używa aplikacja wewnętrznej bazy danych. Aplikacje proxy aplikacji obsługują trzy typy logowania:

-   **Logowanie oparte na hasłach:** Logowanie oparte na hasłach może być używane dla dowolnej aplikacji, która do logowania się używa pól nazwy użytkownika i hasła. Kroki konfiguracji znajdują się w [temacie Konfigurowanie hasła Logowanie jednokrotne dla aplikacji galerii usługi Azure AD](configure-password-single-sign-on-non-gallery-applications.md).

-   **Zintegrowane uwierzytelnianie systemu Windows**: W przypadku aplikacji korzystających ze zintegrowanego uwierzytelniania systemu Windows (IWA) logowanie jednokrotne jest włączone za pośrednictwem delegowania ograniczonego protokołu Kerberos (KCD). Ta metoda daje łącznikom serwera proxy aplikacji uprawnienia w usłudze Active Directory do personifikacji użytkowników oraz do wysyłania i odbierania tokenów w ich imieniu. Szczegółowe informacje na temat konfigurowania KCD można znaleźć w [logowaniu jednokrotnym z dokumentacją KCD.](application-proxy-configure-single-sign-on-with-kcd.md)

-   **Logowanie oparte na nagłówku:** Logowanie oparte na nagłówku jest włączone za pośrednictwem partnerstwa i wymaga dodatkowej konfiguracji. Aby uzyskać szczegółowe informacje na temat współpracy i instrukcje krok po kroku dotyczące konfigurowania logowania jednokrotnego do aplikacji, która używa nagłówków do uwierzytelniania, zobacz [dokumentację usługi PingAccess dla usługi Azure AD.](application-proxy-configure-single-sign-on-with-ping-access.md)

-   **Saml logowanie jednokrotne:** Za pomocą saml logowania jednokrotnego, usługa Azure AD uwierzytelnia się do aplikacji przy użyciu konta usługi Azure AD użytkownika. Usługa Azure AD przekazuje informacje logowania do aplikacji za pośrednictwem protokołu połączenia. Za pomocą logowania jednokrotnego opartego na SAML można mapować użytkowników do określonych ról aplikacji na podstawie reguł zdefiniowanych w oświadczeń SAML. Aby uzyskać informacje na temat konfigurowania logowania jednokrotnego SAML, zobacz [SAML dla logowania jednokrotnego za pomocą serwera proxy aplikacji](application-proxy-configure-single-sign-on-on-premises-apps.md).

Każdą z tych opcji można znaleźć, przechodząc do aplikacji w "Aplikacje dla przedsiębiorstw" i otwierając stronę **logowania jednokrotnego w** menu po lewej stronie. Należy zauważyć, że jeśli aplikacja została utworzona w starym portalu, może nie być widoczne wszystkie te opcje.

Na tej stronie zobaczysz również jedną dodatkową opcję logowania: Połączone logowanie. Ta opcja jest również obsługiwana przez serwer proxy aplikacji. Jednak ta opcja nie dodaje logowania jednokrotnego do aplikacji. Powiedział, że aplikacja może już mieć logowanie jednokrotne zaimplementowane przy użyciu innej usługi, takiej jak Usługi federacyjne Active Directory. 

Ta opcja umożliwia administratorowi utworzenie łącza do aplikacji, na którą użytkownicy po raz pierwszy wylądują podczas uzyskiwania dostępu do aplikacji. Jeśli na przykład istnieje aplikacja skonfigurowana do uwierzytelniania użytkowników przy użyciu usług federacyjnych Active Directory 2.0, administrator może użyć opcji "Połączone logowanie", aby utworzyć do niej łącze w panelu dostępu.

## <a name="next-steps"></a>Następne kroki
- [Przechwalanie haseł do logowania jednokrotnego za pomocą serwera proxy aplikacji](application-proxy-configure-single-sign-on-password-vaulting.md)
- [Delegowanie ograniczone protokołu Kerberos do logowania jednokrotnego za pomocą serwera proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md)
- [Uwierzytelnianie oparte na nagłówku dla logowania jednokrotnego za pomocą serwera proxy aplikacji](application-proxy-configure-single-sign-on-with-ping-access.md) 
- [SAML do logowania jednokrotnego za pomocą serwera proxy aplikacji](application-proxy-configure-single-sign-on-on-premises-apps.md).
