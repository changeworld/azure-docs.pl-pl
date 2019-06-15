---
title: Zaawansowane opcje w tokenie SAML wstępnie zintegrowanych aplikacji w usłudze Azure Active Directory podpisywanie certyfikatów | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać zaawansowanych certyfikatów podpisywania opcje w tokenie SAML wstępnie zintegrowanych aplikacji w usłudze Azure Active Directory
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: mimart
ms.reviewer: jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1baf4af41fd5dcb6723b6ee2827ae91b43b072d9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65780966"
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Zaawansowane opcje w tokenie SAML dla aplikacji z galerii usługi Azure Active Directory podpisywania certyfikatu

Już dzisiaj usługi Azure Active Directory (Azure AD) obsługuje tysiące wstępnie zintegrowanych aplikacji w galerii aplikacji usługi Azure Active Directory. Ponad 500 aplikacje obsługują logowanie jednokrotne przy użyciu [Security Assertion Markup Language](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML) 2.0 protokół, taki jak [NetSuite](https://azuremarketplace.microsoft.com/marketplace/apps/aad.netsuite) aplikacji. Gdy klient uwierzytelnia się do aplikacji za pomocą usługi Azure AD przy użyciu protokołu SAML, usługi Azure AD wysyła token do aplikacji (za pośrednictwem metody POST protokołu HTTP). Aplikacja następnie sprawdza poprawność tokenu i używa go do logowania klienta zamiast monitowania o nazwę użytkownika i hasło. Te tokeny SAML są podpisane za pomocą unikatowy certyfikat, który jest generowany w usłudze Azure AD i określonych standardowych algorytmów.

Usługa Azure AD używa niektóre z domyślnych ustawień dla aplikacji w galerii. Wartości domyślne są konfigurowane w zależności od wymagań aplikacji.

W usłudze Azure AD możesz skonfigurować opcje podpisywania certyfikatu i certyfikatu algorytm podpisywania.

## <a name="certificate-signing-options"></a>Opcje podpisywania certyfikatów

Usługa Azure AD obsługuje trzy opcje certyfikatów do podpisywania:

* **Zaloguj się potwierdzenie SAML**. Ta opcja domyślna jest ustawiona dla większości aplikacji galerii. Jeśli wybierzesz tę opcję, usługa Azure AD jako dostawcy tożsamości (IdP) podpisuje potwierdzenie SAML i certyfikatu z [X.509](https://wikipedia.org/wiki/X.509) certyfikatu aplikacji.

* **Zaloguj się odpowiedzi SAML**. Jeśli wybierzesz tę opcję, usługi Azure AD jako dostawcy tożsamości podpisuje odpowiedzi SAML przy użyciu certyfikatu X.509 aplikacji.

* **Zaloguj się odpowiedź i potwierdzenie SAML**. Jeśli wybierzesz tę opcję, usługi Azure AD jako dostawcy tożsamości podpisuje całego tokenu SAML przy użyciu certyfikatu X.509 aplikacji.

## <a name="certificate-signing-algorithms"></a>Algorytmy podpisywania certyfikatu

Usługa Azure AD obsługuje dwa algorytmy podpisywania lub bezpiecznego skrótu (SHA) do podpisywania SAML odpowiedzi:

* **SHA-256**. Usługa Azure AD używa to domyślny algorytm używany do podpisywania odpowiedzi protokołu SAML. Jest algorytm najnowsze i bezpieczniejsze niż SHA-1. Większość aplikacji obsługuje algorytm SHA-256. Jeśli aplikacja obsługuje tylko algorytm SHA-1 jako algorytm podpisywania, możesz go zmienić. W przeciwnym razie firma Microsoft zaleca używanie algorytmu SHA-256 do podpisywania odpowiedzi protokołu SAML.

* **SHA-1**. Ten algorytm jest starszy i jest ona traktowana jak mniej bezpieczne niż algorytm SHA-256. Jeśli aplikacja obsługuje tylko ten algorytm podpisywania, możesz wybrać tę opcję w **algorytmu podpisywania** listy rozwijanej. Następnie usługa Azure AD podpisuje odpowiedzi SAML przy użyciu algorytmu SHA-1.

## <a name="change-certificate-signing-options-and-signing-algorithm"></a>Zmienianie certyfikatu opcje podpisywania i algorytm podpisywania

Aby zmienić opcje podpisywania certyfikatu SAML aplikacji i certyfikatu algorytm podpisywania, wybierz aplikację, która jest zagrożona:

1. W [portalu Azure Active Directory](https://aad.portal.azure.com/), zaloguj się do swojego konta. **Centrum administracyjne usługi Azure Active Directory** zostanie wyświetlona strona.
1. W lewym okienku wybierz pozycję **Aplikacje dla przedsiębiorstw**. Zostanie wyświetlona lista aplikacji przeznaczonych dla przedsiębiorstw na Twoim koncie.
1. Wybierz aplikację. Zostanie wyświetlona strona Omówienie aplikacji.

   ![Strona przeglądu aplikacji](./media/certificate-signing-options/application-overview-page.png)

Następnie można zmienić certyfikatu podpisywania opcje w tokenie SAML dla tej aplikacji:

1. W okienku po lewej stronie strony przeglądu aplikacji wybierz **logowanie jednokrotne**.

2. Jeśli **Ustaw się logowania jednokrotnego przy użyciu protokołu SAML - Preview** zostanie wyświetlona strona, przejdź do kroku 5.

3. Jeśli **wybierz jedną metodę logowania jednokrotnego** strona nie jest wyświetlane, zaznacz **zmianę trybów rejestracji jednokrotnej** do wyświetlania tej strony.

4. W **wybierz jedną metodę logowania jednokrotnego** wybierz **SAML** Jeśli jest dostępny. (Jeśli **SAML** nie jest dostępna, aplikacja nie obsługuje SAML i można zignorować pozostałą część tej procedury i artykułu.)

5. W **Ustaw się logowania jednokrotnego przy użyciu protokołu SAML - Preview** strony, Znajdź **certyfikat podpisywania SAML** nagłówek i wybierz **Edytuj** ikonę (ołówka). **Certyfikat podpisywania SAML** zostanie wyświetlona strona.

   ![Strona podpisywania protokołu SAML](./media/certificate-signing-options/saml-signing-page.png)

6. W **opcja podpisywania** listy rozwijanej wybierz **odpowiedzi SAML logowania**, **potwierdzenie SAML logowania**, lub **odpowiedź i potwierdzenie SAML logowania**. Opisy te opcje są wyświetlane we wcześniejszej części tego artykułu w [opcje podpisywania certyfikatu](#certificate-signing-options).

7. W **algorytmu podpisywania** listy rozwijanej wybierz **SHA-1** lub **algorytmu SHA-256**. Opisy te opcje są wyświetlane we wcześniejszej części tego artykułu w [certyfikatu podpisywania algorytmy](#certificate-signing-algorithms) sekcji.

8. Jeśli jesteś zadowolony z wybranych opcji, wybierz **Zapisz** Aby zastosować nowe ustawienia certyfikatu podpisywania protokołu SAML. W przeciwnym razie wybierz **X** aby odrzucić zmiany.

## <a name="next-steps"></a>Kolejne kroki

* [Konfigurowanie logowania jednokrotnego do aplikacji, które nie znajdują się w galerii aplikacji usługi Azure Active Directory](configure-federated-single-sign-on-non-gallery-applications.md)
* [Rozwiązywanie problemów z opartej na SAML logowania jednokrotnego](../develop/howto-v1-debug-saml-sso-issues.md)
