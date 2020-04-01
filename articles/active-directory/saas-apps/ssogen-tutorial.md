---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z ssogenem — brama logowania jednokrotnego usługi Azure AD dla pakietu Oracle E-Business Suite — EBS, PeopleSoft i JDE | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a SSOGEN — bramą logowania jednokrotnego usługi Azure AD dla pakietu Oracle E-Business Suite — EBS, PeopleSoft i JDE.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f4311031-5a4b-468e-be58-324d06220869
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74b56e30eb8851913f91cbee377204a575311375
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76293678"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z ssogenem — brama logowania jednokrotnego usługi Azure AD dla pakietu Oracle E-Business Suite — EBS, PeopleSoft i JDE

W tym samouczku dowiesz się, jak zintegrować SSOGEN — Brama SSO usługi Azure AD dla pakietu Oracle E-Business Suite — EBS, PeopleSoft i JDE z usługą Azure Active Directory (Azure AD). Po zintegrowaniu bramy SSOGEN — Azure AD SSO dla pakietu Oracle E-Business Suite — EBS, PeopleSoft i JDE z usługą Azure AD można:

* Kontrola w usłudze Azure AD, która ma dostęp do SSOGEN — brama SSO usługi Azure AD dla pakietu Oracle E-Business Suite — EBS, PeopleSoft i JDE.
* Włącz automatyczne logowanie użytkowników do SSOGEN — brama SSO usługi Azure AD dla pakietu Oracle E-Business Suite — EBS, PeopleSoft i JDE za pomocą kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* SSOGEN — brama logowania jednokrotnego usługi Azure AD dla pakietu Oracle E-Business Suite — subskrypcja z włączoną funkcją logowania jednokrotnego EBS, PeopleSoft i JDE .SSOGEN — Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, and JDE single sign-on (SSO) enabled subscription.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* SSOGEN — brama SSO usługi Azure AD dla pakietu Oracle E-Business Suite — EBS, PeopleSoft i JDE obsługuje jednostkę SSO inicjowane przez **sp i idp.**
* Po skonfigurowaniu bramy SSOGEN — Azure AD SSO gateway dla pakietu Oracle E-Business Suite — EBS, PeopleSoft i JDE można wymusić kontrole sesji, które chronią eksfiltrację i infiltrację poufnych danych organizacji w czasie rzeczywistym. Formanty sesji rozciągają się od dostępu warunkowego. [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

> [!NOTE]
> Identyfikator tej aplikacji jest stałą wartością ciągu, więc tylko jedno wystąpienie można skonfigurować w jednej dzierżawie.

## <a name="adding-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-from-the-gallery"></a>Dodawanie SSOGEN - Brama SSO usługi Azure AD dla pakietu Oracle E-Business Suite - EBS, PeopleSoft i JDE z galerii

Aby skonfigurować integrację SSOGEN — Brama SSO usługi Azure AD dla pakietu Oracle E-Business Suite — EBS, PeopleSoft i JDE w usłudze Azure AD, należy dodać SSOGEN — bramę SSO usługi Azure AD dla pakietu Oracle E-Business Suite — EBS, PeopleSoft i JDE z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **SSOGEN — Brama SSO usługi Azure AD dla pakietu Oracle E-Business Suite — EBS, PeopleSoft i JDE** w polu wyszukiwania.
1. Wybierz **SSOGEN — bramę SSO usługi Azure AD dla pakietu Oracle E-Business Suite — EBS, PeopleSoft i JDE** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla ssogenów — brama logowania jednokrotnego usługi Azure AD dla pakietu Oracle E-Business Suite — EBS, PeopleSoft i JDE

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą ssogenu SSOGEN — brama SSO usługi Azure AD dla pakietu Oracle E-Business Suite — EBS, PeopleSoft i JDE przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik samouszeńców łącze działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w programie SSOGEN — brama SSO usługi Azure AD dla pakietu Oracle E-Business Suite — EBS, PeopleSoft i JDE.

Aby skonfigurować i przetestować samoustawę samouszeńców usługi Azure AD za pomocą sytuatora SSOGEN — brama SSO usługi Azure AD dla pakietu Oracle E-Business Suite — EBS, PeopleSoft i JDE, wykonaj następujące elementy konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj bramę logowania jednokrotnego usługi SSOGEN Azure AD dla oracle e business suite EBS, PeopleSoft i JDE SSO](#configure-ssogen-azure-ad-sso-gateway-for-oracle-e-business-suite-ebs-peoplesoft-and-jde-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    * **[Utwórz bramę SSOGEN Azure AD SSO dla użytkownika testowego Oracle E Business Suite EBS, PeopleSoft i JDE](#create-ssogen-azure-ad-sso-gateway-for-oracle-e-business-suite-ebs-peoplesoft-and-jde-test-user)** — aby mieć odpowiednik B.Simon w SSOGEN — Brama SSO usługi Azure AD dla pakietu Oracle E-Business Suite — EBS, PeopleSoft i JDE, która jest połączona z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)— na stronie **SSOGEN — Azure AD SSO Gateway for Oracle E-Business Suite — EBS, PeopleSoft i JDE** , znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wprowadź wartości dla następujących pól:

    W polu tekstowym **Odpowiedz na adres URL** wpisz adres URL, używając następującego wzorca:`https://<customer_name>.ssogen.com/ssogen/login?client_name=<customer_name>`

1. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<customer_name>.ssogen.com/ssogen/login?client_name=<customer_name>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając faktycznego adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej SSOGEN — Azure AD SSO Gateway for Oracle E-Business Suite — EBS, PeopleSoft i JDE Client support,](mailto:support@ssogen.com) aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja SSOGEN — Azure AD SSO Gateway for Oracle E-Business Suite — APLIKACJA EBS, PeopleSoft i JDE oczekuje potwierdzeń SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Na poniższym zrzucie ekranu przedstawiono listę atrybutów domyślnych, gdzie atrybut **nameidentifier** jest mapowany na atrybut **user.userprincipalname**. SSOGEN — brama SSO usługi Azure AD dla pakietu Oracle E-Business Suite — aplikacja EBS, PeopleSoft i JDE oczekuje, że **identyfikator nazw** zostanie zamapowany **na user.onpremisessamaccountname**, więc musisz edytować mapowanie atrybutów, klikając ikonę **Edytuj** i zmienić mapowanie atrybutów.

    ![image](common/edit-attribute.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** kliknij przycisk kopiuj, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w witrynie Azure portal o nazwie B.Simon.

1. Z lewego okienka w witrynie Azure portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **pozycję Nowy użytkownik** u góry ekranu.
1. We właściwościach **Użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** username@companydomain.extensionwprowadź pole . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do SSOGEN — Azure AD SSO Gateway dla Oracle E-Business Suite — EBS, PeopleSoft i JDE.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz **pozycję SSOGEN — Brama SSO usługi Azure AD dla pakietu Oracle E-Business Suite — EBS, PeopleSoft i JDE**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-ssogen-azure-ad-sso-gateway-for-oracle-e-business-suite-ebs-peoplesoft-and-jde-sso"></a>Konfigurowanie bramy SSOGEN Azure AD SSO dla oracle e business suite EBS, PeopleSoft i JDE SSO

Aby skonfigurować logowanie jednokrotne na **SSOGEN - Brama logowania usługi Azure AD dla pakietu Oracle E-Business Suite — strona EBS, PeopleSoft i JDE,** poniżej znajdziesz dokumentację rejestracji logowania jednokrotnego specyficzne dla aplikacji:

* Oracle EBS — integracja samouszeńców usługi Azure AD:[https://www.ssogen.com/oracle-ebs-sso-ldap/](https://www.ssogen.com/oracle-ebs-sso-ldap/)
* PeopleSoft — integracja samouszeńców usługi Azure AD:[https://www.ssogen.com/peoplesoft-sso/](https://www.ssogen.com/peoplesoft-sso/)
* JD Edwards — integracja sso usługi Azure AD:[https://www.ssogen.com/oracle-jde-sso/](https://www.ssogen.com/oracle-jde-sso/)
* Apache — integracja z przyłączem samouszeńców usługi Azure AD:[https://www.ssogen.com/apache-sso-authentication/](https://www.ssogen.com/apache-sso-authentication/)

### <a name="create-ssogen-azure-ad-sso-gateway-for-oracle-e-business-suite-ebs-peoplesoft-and-jde-test-user"></a>Tworzenie bramy SSOGEN Azure AD SSO dla użytkownika testowego Oracle E Business Suite EBS, PeopleSoft i JDE

Usługa Azure AD wysyła unikatowy identyfikator użytkownika (name id) do aplikacji użytkownika, po pomyślnym uwierzytelnieniu.  Upewnij się, że unikatowy identyfikator użytkownika (name id) jest zgodny z rekordem użytkownika w aplikacji, FND_USER. USER_NAME na przykład w Oracle EBS.

Prosimy [info@ssogen.com](mailto:info@ssogen.com) o [support@ssogen.com](mailto:support@ssogen.com) kontakt i wsparcie.

## <a name="test-sso"></a>Test SSO

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu ssogen - Azure AD SSO Gateway for Oracle E-Business Suite — EBS, PeopleSoft i JDE w panelu dostępu, należy automatycznie zalogować się do SSOGEN — Azure AD SSO Gateway dla Oracle E-Business Suite - EBS, PeopleSoft i JDE, dla którego skonfigurowano logującą się do usługi SSO. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj SSOGEN — bramę SSO usługi Azure AD dla pakietu Oracle E-Business Suite — EBS, PeopleSoft i JDE z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w usłudze Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chronić SSOGEN - Brama SSO usługi Azure AD dla pakietu Oracle E-Business Suite - EBS, PeopleSoft i JDE z zaawansowaną widocznością i kontrolą](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
