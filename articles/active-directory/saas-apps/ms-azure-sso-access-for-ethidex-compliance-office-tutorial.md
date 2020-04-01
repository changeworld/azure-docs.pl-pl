---
title: 'Samouczek: integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z ms azure sso access for Ethidex Compliance Office™ | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a ms azure sso access dla biura zgodności Ethidex™.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 58b2e70f-d1dd-47b6-b91f-f77581df01c6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00cbb5ff4b9354d0d1702161460b2646669f34c2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "70844369"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ms-azure-sso-access-for-ethidex-compliance-office"></a>Samouczek: integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z ms azure sso access for Ethidex Compliance Office™

W tym samouczku dowiesz się, jak zintegrować ms Azure SSO Access for Ethidex Compliance Office™ z usługą Azure Active Directory (Azure AD). Po zintegrowaniu ms azure sso access for Ethidex Compliance Office™ z usługą Azure AD, można:

* Kontrola w usłudze Azure AD, który ma dostęp do ms Azure SSO Access dla Ethidex Compliance Office™.
* Włącz użytkownikom automatyczne logowanie do usługi MS Azure SSO Access for Ethidex Compliance Office™ za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* MS Azure SSO Access for Ethidex Compliance Office™ subskrypcja z włączoną funkcją logowania jednokrotnego (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* MS Azure SSO Access for Ethidex Compliance Office™ obsługuje zainicjowane przez protokół SSO inicjowane przez protokół **IDP**

## <a name="adding-ms-azure-sso-access-for-ethidex-compliance-office-from-the-gallery"></a>Dodawanie pakietu MS Azure SSO Access for Ethidex Compliance Office™ z galerii

Aby skonfigurować integrację pakietu MS Azure SSO Access for Ethidex Compliance Office™ do usługi Azure AD, należy dodać ms azure sso access dla biura zgodności Ethidex™ z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **MS Azure SSO Access for Ethidex Compliance Office™** w polu wyszukiwania.
1. Wybierz **ms azure sso access for Ethidex Compliance Office™** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ms-azure-sso-access-for-ethidex-compliance-office"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla usługi MS Azure SSO Access for Ethidex Compliance Office™

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą ms Azure SSO Access for Ethidex Compliance Office™ przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuował działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w usłudze MS Azure SSO Access for Ethidex Compliance Office™.

Aby skonfigurować i przetestować usługę Azure AD SSO przy pomocą pakietu MS Azure SSO Access for Ethidex Compliance Office™ wykonaj następujące elementy konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj ms azure logowanie jednokrotne dla ethidex compliance office logowania](#configure-ms-azure-sso-access-for-ethidex-compliance-office-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz ms azure sso dostęp do ethidex zgodności office test użytkownika](#create-ms-azure-sso-access-for-ethidex-compliance-office-test-user)** — mieć odpowiednik B.Simon w MS Azure SSO Access dla Ethidex Compliance Office™ który jest połączony z reprezentacji usługi Azure AD użytkownika.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)— na stronie **MS Azure SSO Access for Ethidex Compliance Office™** sekcji **Zarządzanie** i wybierz opcję **logowania jednokrotnego**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL przy użyciu następującego wzorca:`com.ethidex.prod.<CLIENTID>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://www.ethidex.com/saml2/sp/acs/<CLIENTID>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Skontaktuj się z [ms azure sso access for Ethidex Compliance Office™ zespół pomocy technicznej,](mailto:support@ethidex.com) aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. MS Azure SSO Access for Ethidex Compliance Office™ application application expects the SAML assertions in a specific format, which requires you to add custom attribute mappings to your SAML token attributes configuration. Na poniższym zrzucie ekranu przedstawiono listę atrybutów domyślnych, gdzie atrybut **nameidentifier** jest mapowany na atrybut **user.userprincipalname**. MS Azure SSO Access for Ethidex Compliance Office™ aplikacja oczekuje **nameidentifier** być mapowane z **user.mail**, więc trzeba edytować mapowanie atrybutów, klikając na **ikonę edycji** i zmienić mapowanie atrybutów.

    ![image](common/edit-attribute.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (raw)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificateraw.png)

1. W sekcji **Konfigurowanie pakietu SSO ms azure dla biura zgodności Ethidex™,** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do ms azure sso dostęp do ethidex compliance office™.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **MS Azure SSO Access for Ethidex Compliance Office™**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-ms-azure-sso-access-for-ethidex-compliance-office-sso"></a>Konfigurowanie dostępu sytego ms azure dla pakietu SSO ethidex compliance office

Aby skonfigurować logowanie jednokrotne w **usłudze MS Azure SSO Access for Ethidex Compliance Office™** stronie, należy wysłać pobrany **certyfikat (raw)** i odpowiednie skopiowane adresy URL z witryny Azure portal do [ms Azure SSO Access dla ethidex Compliance Office™ zespół pomocy technicznej](mailto:support@ethidex.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-ms-azure-sso-access-for-ethidex-compliance-office-test-user"></a>Tworzenie programu MS Azure SSO Access dla użytkownika testowego ethidex compliance office

W tej sekcji utworzysz użytkownika o nazwie B.Simon w MS Azure SSO Access for Ethidex Compliance Office™. Praca z [ms Azure SSO Access for Ethidex Compliance Office™ zespół pomocy technicznej,](mailto:support@ethidex.com) aby dodać użytkowników w MS Azure SSO Access for Ethidex Compliance Office™ platformie. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka MS Azure SSO Access for Ethidex Compliance Office™ w panelu dostępu, powinieneś zostać automatycznie zalogowany do ms Azure SSO Access for Ethidex Compliance Office™ dla którego skonfigurowano logowanie jednośliżane. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj usługę MS Azure SSO Access for Ethidex Compliance Office™ z usługą Azure AD](https://aad.portal.azure.com/)

