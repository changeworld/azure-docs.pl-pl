---
title: Logowanie jednokrotne SAML dla aplikacji lokalnych za pomocą serwera proxy aplikacji usługi Azure AD
description: Dowiedz się, jak zapewnić logowanie jednokrotne dla aplikacji lokalnych, które są zabezpieczone za pomocą uwierzytelniania SAML. Zapewnij zdalny dostęp do aplikacji lokalnych za pomocą serwera proxy aplikacji.
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
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ccf34b52e06e369fe4dd459ff9dfa2880596fb35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481351"
---
# <a name="saml-single-sign-on-for-on-premises-applications-with-application-proxy"></a>Saml logowanie jednokrotne dla aplikacji lokalnych z pełnomocnikiem aplikacji

Można zapewnić logowanie jednokrotne (SSO) do aplikacji lokalnych, które są zabezpieczone za pomocą uwierzytelniania SAML i zapewnić zdalny dostęp do tych aplikacji za pośrednictwem serwera proxy aplikacji. Dzięki logowaniu jednokrotnemu SAML usługa Azure Active Directory (Azure AD) uwierzytelnia się w aplikacji przy użyciu konta usługi Azure AD użytkownika. Usługa Azure AD przekazuje informacje logowania do aplikacji za pośrednictwem protokołu połączenia. Można również mapować użytkowników do określonych ról aplikacji na podstawie reguł zdefiniowanych w oświadczeń SAML. Włączając serwer proxy aplikacji oprócz logowania jednokrotnego SAML, użytkownicy będą mieli zewnętrzny dostęp do aplikacji i bezproblemowe środowisko logowania jednokrotnego.

Aplikacje muszą mieć możliwość korzystania z tokenów SAML wystawionych przez **usługę Azure Active Directory.** Ta konfiguracja nie ma zastosowania do aplikacji korzystających z lokalnego dostawcy tożsamości. W tych scenariuszach zaleca się przeglądanie [zasobów do migracji aplikacji do usługi Azure AD.](migration-resources.md)

SAML SSO z serwerem proxy aplikacji współpracuje również z funkcją szyfrowania tokenu SAML. Aby uzyskać więcej informacji, zobacz [Konfigurowanie szyfrowania tokenów saml usługi Azure AD](howto-saml-token-encryption.md).

Poniższe diagramy protokołów opisują sekwencję logowania jednokrotnego dla przepływu inicjowanego przez dostawcę usług (inicjowanego przez sp. z o.o.), jak i przepływu inicjowanego przez dostawcę tożsamości (inicjowanego idP). Serwer proxy aplikacji współpracuje z samouczłówkiem SAML, buforując żądanie SAML i odpowiedź do i z aplikacji lokalnej.

  ![Przepływ SP SAML](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-sp-initiated-flow.png)

  ![Przepływ SP SAML](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-idp-initiated-flow.png)

## <a name="create-an-application-and-set-up-saml-sso"></a>Tworzenie aplikacji i konfigurowanie identyfikatora SSO SAML

1. W witrynie Azure portal wybierz pozycję **Azure Active Directory > aplikacje enterprise** i wybierz nową **aplikację**.

2. Wprowadź nazwę wyświetlaną dla nowej aplikacji, wybierz **pozycję Zintegruj dowolną inną aplikację, której nie znajdziesz w galerii,** a następnie wybierz pozycję **Utwórz**.

3. Na stronie **Przegląd** aplikacji wybierz pozycję **Logowanie jednokrotne**.

4. Wybierz **SAML** jako metodę logowania jednokrotnego.

5. Najpierw skonfiguruj saml sypki do pracy w sieci firmowej. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** przejdź do **nagłówka Konfiguracja podstawowego SAML** i wybierz jego ikonę **Edycji** (ołówek). Wykonaj kroki opisane w [programie Enter podstawowa konfiguracja SAML,](configure-single-sign-on-non-gallery-applications.md#step-1-edit-the-basic-saml-configuration) aby skonfigurować uwierzytelnianie oparte na saml dla aplikacji.

6. Dodaj co najmniej jednego użytkownika do aplikacji i upewnij się, że konto testowe ma dostęp do aplikacji. Po podłączeniu do sieci firmowej użyj konta testowego, aby sprawdzić, czy masz logowanie jednokrotne w aplikacji. 

   > [!NOTE]
   > Po skonfigurowaniu serwera proxy aplikacji powrócisz i zaktualizujesz **adres URL odpowiedzi**SAML .

## <a name="publish-the-on-premises-application-with-application-proxy"></a>Publikowanie aplikacji lokalnej za pomocą serwera proxy aplikacji

Aby można było podać opcję SSO dla aplikacji lokalnych, należy włączyć serwer proxy aplikacji i zainstalować łącznik. Zobacz samouczek [Dodawanie aplikacji lokalnej do zdalnego dostępu za pośrednictwem serwera proxy aplikacji w usłudze Azure AD,](application-proxy-add-on-premises-application.md) aby dowiedzieć się, jak przygotować środowisko lokalne, zainstalować i zarejestrować łącznik i przetestować łącznik. Następnie wykonaj następujące kroki, aby opublikować nową aplikację za pomocą serwera proxy aplikacji. Aby uzyskać inne ustawienia, o których nie wspomniano poniżej, zapoznaj się [z sekcją Dodawanie aplikacji lokalnej do usługi Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) w samouczku.

1. Gdy aplikacja jest nadal otwarta w witrynie Azure portal, wybierz pozycję **Serwer proxy aplikacji**. Podaj **wewnętrzny adres URL** aplikacji. Jeśli używasz domeny niestandardowej, musisz również przekazać certyfikat TLS/SSL dla aplikacji. 
   > [!NOTE]
   > Najlepszym rozwiązaniem jest używanie domen niestandardowych w miarę możliwości, aby uzyskać zoptymalizowane środowisko użytkownika. Dowiedz się więcej o [pracy z domenami niestandardowymi w usłudze Azure AD Application Proxy](application-proxy-configure-custom-domain.md).

2. Wybierz **usługę Azure Active Directory** jako metodę **uwierzytelniania wstępnego** dla aplikacji.

3. Skopiuj **zewnętrzny adres URL** aplikacji. Ten adres URL będzie potrzebny do ukończenia konfiguracji SAML.

4. Korzystając z konta testowego, spróbuj otworzyć aplikację z **zewnętrznym adresem URL,** aby sprawdzić poprawność, że serwer proxy aplikacji jest poprawnie skonfigurowany. Jeśli występują problemy, zobacz [Rozwiązywanie problemów z serwerem proxy aplikacji i komunikatów o błędach](application-proxy-troubleshoot.md).

## <a name="update-the-saml-configuration"></a>Aktualizowanie konfiguracji SAML

1. Gdy aplikacja jest nadal otwarta w witrynie Azure portal, wybierz opcję **Logowanie jednokrotne**. 

2. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** przejdź do **nagłówka Konfiguracja podstawowego SAML** i wybierz jego ikonę **Edycji** (ołówek). Upewnij się, że **zewnętrzny adres URL** skonfigurowany w serwerze proxy aplikacji jest wypełniany w polach **Identyfikator**, Adres **URL odpowiedzi**i Adres **URL wylogowywania.** Te adresy URL są wymagane do prawidłowego działania serwera proxy aplikacji. 

3. Edytuj adres **URL odpowiedzi** skonfigurowany wcześniej, tak aby jego domena była osiągalna przez serwer proxy aplikacji. Jeśli na przykład **zewnętrzny adres URL** jest `https://contosotravel.com/acs` `https://contosotravel-f128.msappproxy.net` i oryginalny **adres URL odpowiedzi** był , musisz zaktualizować oryginalny adres URL **odpowiedzi** do `https://contosotravel-f128.msappproxy.net/acs`. 

    ![Wprowadzanie podstawowych danych konfiguracyjnych SAML](./media/application-proxy-configure-single-sign-on-on-premises-apps/basic-saml-configuration.png)


4. Zaznacz pole wyboru obok zaktualizowanego **adresu URL odpowiedzi,** aby oznaczyć je jako domyślne.

   * Jeśli wymagany **adres URL odpowiedzi** jest już wyświetlany, oznacz ten **adres URL odpowiedzi** jako domyślny i usuń wcześniej skonfigurowany adres URL **odpowiedzi**.

   * W przypadku przepływu inicjowanego przez sp, upewnij się, że aplikacja zaplecza określa poprawny **adres URL odpowiedzi** lub adres URL usługi konsumenta oświadczeń do odbierania tokenu uwierzytelniania.

    > [!NOTE]
    > Jeśli aplikacja zaplecza **oczekuje,** że adres URL odpowiedzi będzie wewnętrznym adresem URL, musisz użyć [domen niestandardowych,](application-proxy-configure-custom-domain.md) aby mieć pasujące wewnętrzne i zewnętrzne adresy URL lub zainstalować bezpieczne rozszerzenie logowania Moje aplikacje na urządzeniach użytkowników. To rozszerzenie zostanie automatycznie przekierowane do odpowiedniej usługi proxy aplikacji. Aby zainstalować rozszerzenie, zobacz [Rozszerzenie logowania bezpieczne moje aplikacje](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).
    
## <a name="test-your-app"></a>Testowanie aplikacji

Po wykonaniu wszystkich tych kroków aplikacja powinna być uruchomiona. Aby przetestować aplikację:

1. Otwórz przeglądarkę i przejdź do **zewnętrznego adresu URL** utworzonego podczas publikowania aplikacji. 
1. Zaloguj się przy tym przy tym kontem testowym przypisanym do aplikacji. Powinno być możliwe załadowanie aplikacji i mieć wpis sytowy do aplikacji.

## <a name="next-steps"></a>Następne kroki

- [W jaki sposób serwer proxy aplikacji usługi Azure AD zapewnia logowanie jednokrotne?](application-proxy-single-sign-on.md)
- [Rozwiązywanie problemów z serwerem proxy aplikacji](application-proxy-troubleshoot.md)
