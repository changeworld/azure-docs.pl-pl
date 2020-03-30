---
title: Zaawansowane opcje podpisywania certyfikatów tokenu SAML dla aplikacji usługi Azure AD
description: Dowiedz się, jak korzystać z zaawansowanych opcji podpisywania certyfikatów w tokenie SAML dla wstępnie zintegrowanych aplikacji w usłudze Azure Active Directory
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
ms.openlocfilehash: dc911ff06208b1fd0af7651c8274a45c958bf0cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159203"
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Zaawansowane opcje podpisywania certyfikatów w tokenie SAML dla aplikacji galerii w usłudze Azure Active Directory

Obecnie usługa Azure Active Directory (Azure AD) obsługuje tysiące wstępnie zintegrowanych aplikacji w Galerii aplikacji usługi Azure Active Directory. Ponad 500 aplikacji obsługuje logowanie jednokrotne przy użyciu protokołu [Języka znaczników oświadczeń zabezpieczeń](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML) 2.0, takiego jak aplikacja [NetSuite.](https://azuremarketplace.microsoft.com/marketplace/apps/aad.netsuite) Gdy klient uwierzytelnia się w aplikacji za pośrednictwem usługi Azure AD przy użyciu saml, usługa Azure AD wysyła token do aplikacji (za pośrednictwem protokołu HTTP POST). Następnie aplikacja sprawdza poprawność i używa tokenu do logowania się do klienta zamiast monitowania o nazwę użytkownika i hasło. Te tokeny SAML są podpisane przy pomocy unikatowego certyfikatu, który jest generowany w usłudze Azure AD i przez określone standardowe algorytmy.

Usługa Azure AD używa niektórych ustawień domyślnych dla aplikacji galerii. Wartości domyślne są konfiguruj na podstawie wymagań aplikacji.

W usłudze Azure AD można skonfigurować opcje podpisywania certyfikatów i algorytm podpisywania certyfikatów.

## <a name="certificate-signing-options"></a>Opcje podpisywania certyfikatów

Usługa Azure AD obsługuje trzy opcje podpisywania certyfikatów:

* **Podpisz twierdzenie SAML**. Ta opcja domyślna jest ustawiona dla większości aplikacji galerii. Jeśli wybierzesz tę opcję, usługa Azure AD jako dostawca tożsamości (IdP) podpisze asercja SAML i certyfikat z certyfikatem [X.509](https://wikipedia.org/wiki/X.509) aplikacji.

* **Podpisz odpowiedź SAML**. Jeśli wybierzesz tę opcję, usługa Azure AD jako idp podpisuje odpowiedź SAML z certyfikatem X.509 aplikacji.

* **Podpisz odpowiedź i twierdzenie SAML**. Jeśli wybierzesz tę opcję, usługa Azure AD jako idp podpisuje cały token SAML z certyfikatem X.509 aplikacji.

## <a name="certificate-signing-algorithms"></a>Algorytmy podpisywania certyfikatów

Usługa Azure AD obsługuje dwa algorytmy podpisywania lub algorytmy bezpiecznego mieszania (SHA), aby podpisać odpowiedź SAML:

* **SHA-256**. Usługa Azure AD używa tego domyślnego algorytmu do podpisywania odpowiedzi SAML. Jest to najnowszy algorytm i jest bezpieczniejszy niż SHA-1. Większość aplikacji obsługuje algorytm SHA-256. Jeśli aplikacja obsługuje tylko SHA-1 jako algorytm podpisywania, można go zmienić. W przeciwnym razie zaleca się użycie algorytmu SHA-256 do podpisywania odpowiedzi SAML.

* **SHA-1**. Ten algorytm jest starszy i jest traktowany jako mniej bezpieczny niż SHA-256. Jeśli aplikacja obsługuje tylko ten algorytm podpisywania, można wybrać tę opcję na liście rozwijanej **Algorytm podpisywania.** Usługa Azure AD następnie podpisuje odpowiedź SAML za pomocą algorytmu SHA-1.

## <a name="change-certificate-signing-options-and-signing-algorithm"></a>Zmienianie opcji podpisywania certyfikatów i algorytmu podpisywania

Aby zmienić opcje podpisywania certyfikatów SAML aplikacji i algorytm podpisywania certyfikatów, wybierz daną aplikację:

1. W [portalu usługi Azure Active Directory](https://aad.portal.azure.com/)zaloguj się na swoje konto. Zostanie wyświetlona strona **centrum administracyjnego usługi Azure Active Directory.**
1. W lewym okienku wybierz pozycję **Aplikacje dla przedsiębiorstw**. Pojawi się lista aplikacji dla przedsiębiorstw na twoim koncie.
1. Wybierz aplikację. Zostanie wyświetlona strona przeglądu aplikacji.

   ![Przykład: Strona przeglądu aplikacji](./media/certificate-signing-options/application-overview-page.png)

Następnie zmień opcje podpisywania certyfikatów w tokenie SAML dla tej aplikacji:

1. W lewym okienku strony przeglądu aplikacji wybierz pozycję **Logowanie jednokrotne**.
1. Jeśli zostanie wyświetlona strona **Konfigurowanie logowania jednokrotnego z saml - podgląd,** przejdź do kroku 5.
1. Jeśli strona **Wybierz metodę logowania jednokrotnego** nie jest wyświetlana, wybierz pozycję **Zmień tryby logowania jednokrotnego,** aby wyświetlić tę stronę.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz **saml,** jeśli jest dostępny. (Jeśli **SAML** nie jest dostępny, aplikacja nie obsługuje SAML, a resztę tej procedury i artykułu można zignorować).
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml — podgląd** znajdź nagłówek **Certyfikat podpisywania SAML** i wybierz ikonę **Edytuj** (ołówek). Zostanie wyświetlona strona **Certyfikat podpisywania SAML.**

   ![Przykład: strona certyfikatu podpisywania SAML](./media/certificate-signing-options/saml-signing-page.png)

1. Na liście rozwijanej **Opcja podpisywania** wybierz pozycję **Podpisz odpowiedź SAML**, **Podpisz twierdzenie SAML**lub **Podpisz odpowiedź i asercja SAML**. Opisy tych opcji są wyświetlane wcześniej w tym artykule w [opcjach podpisywania certyfikatów](#certificate-signing-options).
1. Z listy rozwijanej **Algorytm podpisywania** wybierz pozycję **SHA-1** lub **SHA-256**. Opisy tych opcji są wyświetlane wcześniej w tym artykule w sekcji [Algorytmy podpisywania certyfikatów.](#certificate-signing-algorithms)
1. Jeśli wybory są zadowalające, wybierz pozycję **Zapisz,** aby zastosować nowe ustawienia certyfikatu podpisywania SAML. W przeciwnym razie wybierz **X,** aby odrzucić zmiany.

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie logowania jednokrotnego na aplikacje, które nie znajdują się w Galerii aplikacji usługi Azure Active Directory](configure-federated-single-sign-on-non-gallery-applications.md)
* [Rozwiązywanie problemów z logowaniem jednokrotnym oparte na saml](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
