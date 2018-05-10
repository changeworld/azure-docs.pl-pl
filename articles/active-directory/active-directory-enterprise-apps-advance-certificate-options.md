---
title: Zaawansowane certyfikatu podpisywania opcje w tokenie SAML dla wstępnie zintegrowanych aplikacji w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak użyć zaawansowanych certyfikatów podpisywania opcje w tokenie SAML dla wstępnie zintegrowanych aplikacji w usłudze Azure Active Directory
services: active-directory
documentationcenter: ''
author: jeevansd
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2017
ms.author: jeedes
ms.custom: aaddev
ms.openlocfilehash: 05eb175e3d8306308998780ac230d4f44e611294
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Zaawansowane opcje w tokenie SAML dla galerii aplikacji w usłudze Azure Active Directory podpisania certyfikatu
Obecnie usługa Azure Active Directory (Azure AD) obsługuje tysięcy wstępnie zintegrowanych aplikacji w galerii aplikacji Azure Active Directory. Liczba ta obejmuje więcej niż 500 aplikacje, które obsługują logowanie jednokrotne za pomocą protokołu SAML 2.0. Podczas uwierzytelniania użytkownika do aplikacji za pomocą usługi Azure AD przy użyciu SAML usługi Azure AD wysyła token do aplikacji (za pośrednictwem protokołu HTTP POST). Następnie aplikacji sprawdza poprawność tokenu i używa go zalogowania użytkownika zamiast monitowania o nazwę użytkownika i hasło. Te tokeny SAML są podpisane za pomocą unikatowy certyfikat, który jest generowany w usłudze Azure AD i określonych algorytmów standardowych.

Usługi Azure AD używa niektóre domyślne ustawienia dla aplikacji w galerii. Wartości domyślne ustawiono zależności od wymagań aplikacji.

Usługi Azure AD obsługuje zaawansowane certyfikatu podpisywania ustawienia. Aby wybrać te opcje, najpierw wybierz **Pokaż zaawansowane ustawienia podpisywania certyfikatu** pola wyboru:

![Pokaż zaawansowane ustawienia podpisywania certyfikatów ][1]

Po zaznaczeniu tego pola wyboru, można skonfigurować opcje podpisywania certyfikatu i certyfikat podpisywania algorytmu.

## <a name="certificate-signing-options"></a>Opcje podpisywania certyfikatu

Usługi Azure AD obsługuje trzy opcje podpisywania certyfikatu:

* **Zaloguj się potwierdzenia języka SAML**. Opcja domyślna jest ustawiona dla większości aplikacji galerii. Jeśli ta opcja jest zaznaczona, usługi Azure AD jako dostawca tożsamości podpisuje potwierdzenia języka SAML i certyfikatu z X509 certyfikatu aplikacji. Ponadto używa algorytmu podpisywania, który wybrano w **algorytm podpisywania** listy rozwijanej.

* **Zaloguj się odpowiedzi SAML**. Jeśli ta opcja jest zaznaczona, usługi Azure AD jako dostawca tożsamości podpisuje odpowiedzi SAML z X509 certyfikatu aplikacji. Ponadto używa algorytmu podpisywania, który wybrano w **algorytm podpisywania** listy rozwijanej.

* **Podpisywanie odpowiedzi SAML i potwierdzenia**. Jeśli ta opcja jest zaznaczona, usługi Azure AD jako dostawca tożsamości podpisuje cały tokenu SAML z X509 certyfikatu aplikacji. Ponadto używa algorytmu podpisywania, który wybrano w **algorytm podpisywania** listy rozwijanej.

    ![Opcje podpisywania certyfikatu][4]

## <a name="certificate-signing-algorithms"></a>Algorytmy podpisywania certyfikatu

Usługi Azure AD obsługuje dwa algorytmy podpisywania odpowiedzi SAML logowania:

* **ALGORYTM SHA-256**. Usługi Azure AD używa tego domyślny algorytm do podpisywania odpowiedzi SAML. Najnowsze algorytmu i jest traktowany jako więcej bezpieczna niż SHA-1. Większość aplikacji obsługuje algorytm SHA-256. Jeśli aplikacja obsługuje tylko algorytm SHA-1 jako algorytm podpisywania, można ją zmienić. W przeciwnym razie zaleca się używać algorytmu SHA-256 do podpisywania odpowiedzi SAML.

    ![Algorytm podpisywania certyfikatu algorytmu SHA-256][3]

* **ALGORYTM SHA-1**. To starsze algorytmu, więc będzie traktowane jako mniej bezpieczna niż SH-256. Jeśli aplikacja obsługuje tylko ten algorytm podpisywania, możesz wybrać tę opcję w **algorytm podpisywania** listy rozwijanej. Następnie usługi Azure AD podpisuje odpowiedzi SAML przy użyciu algorytmu SHA-1.

    ![Algorytm podpisywania certyfikatu SHA-1][2]

## <a name="next-steps"></a>Kolejne kroki
* [Indeks artykułów dotyczących zarządzania aplikacjami w usłudze Azure Active Directory](active-directory-apps-index.md)
* [Konfigurowanie rejestracji jednokrotnej do aplikacji, które nie znajdują się w galerii aplikacji Azure Active Directory](application-config-sso-how-to-configure-federated-sso-non-gallery.md)
* [Rozwiązywanie problemów z systemem SAML logowania jednokrotnego](develop/active-directory-saml-debugging.md)

<!--Image references-->

[1]: ./media/active-directory-enterprise-apps-advance-certificate-options/saml-advance-certificate.png
[2]: ./media/active-directory-enterprise-apps-advance-certificate-options/saml-signing-algo-sha1.png
[3]: ./media/active-directory-enterprise-apps-advance-certificate-options/saml-signing-algo-sha256.png
[4]: ./media/active-directory-enterprise-apps-advance-certificate-options/saml-signing-options.png
