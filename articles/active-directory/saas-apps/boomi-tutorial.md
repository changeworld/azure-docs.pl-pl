---
title: 'Tutorial: Azure Active Directory single sign-on (SSO) integration with Boomi | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i platformą Boomi.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 40d034ff-7394-4713-923d-1f8f2ed8bf36
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2469745edb5b8b3696478603cfe874bcabc8c1ff
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231955"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-boomi"></a>Tutorial: Azure Active Directory single sign-on (SSO) integration with Boomi

In this tutorial, you'll learn how to integrate Boomi with Azure Active Directory (Azure AD). When you integrate Boomi with Azure AD, you can:

* Control in Azure AD who has access to Boomi.
* Enable your users to be automatically signed-in to Boomi with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

To learn more about SaaS app integration with Azure AD, see [What is application access and single sign-on with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

To get started, you need the following items:

* Subskrypcja usługi Azure AD. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* Boomi single sign-on (SSO) enabled subscription.

## <a name="scenario-description"></a>Opis scenariusza

In this tutorial, you configure and test Azure AD SSO in a test environment.

* Platforma Boomi obsługuje logowanie jednokrotne inicjowane przez **dostawcę tożsamości**

## <a name="adding-boomi-from-the-gallery"></a>Dodawanie platformy Boomi z galerii

Aby skonfigurować integrację platformy Boomi z usługą Azure AD, musisz dodać platformę Boomi z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **Boomi** in the search box.
1. Select **Boomi** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-boomi"></a>Configure and test Azure AD single sign-on for Boomi

Configure and test Azure AD SSO with Boomi using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in Boomi.

To configure and test Azure AD SSO with Boomi, complete the following building blocks:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
    * **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
    * **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
1. **[Configure Boomi SSO](#configure-boomi-sso)** - to configure the single sign-on settings on application side.
    * **[Create Boomi test user](#create-boomi-test-user)** - to have a counterpart of B.Simon in Boomi that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** - to verify whether the configuration works.

## <a name="configure-azure-ad-sso"></a>Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the [Azure portal](https://portal.azure.com/), on the **Boomi** application integration page, find the **Manage** section and select **single sign-on**.
1. On the **Select a single sign-on method** page, select **SAML**.
1. On the **Set up single sign-on with SAML** page, click the edit/pen icon for **Basic SAML Configuration** to edit the settings.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. On the **Set up single sign-on with SAML** page, enter the values for the following fields:

    a. W polu tekstowym **Identyfikator** wpisz adres URL: `https://platform.boomi.com/`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://platform.boomi.com/sso/<boomi-tenant>/saml`

    > [!NOTE]
    > Wartość adresu URL odpowiedzi nie jest prawdziwa. Zaktualizuj ją, stosując rzeczywisty adres URL odpowiedzi. Aby uzyskać tę wartość, skontaktuj się z [zespołem pomocy technicznej klienta Boomi](https://boomi.com/company/contact/). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Boomi application expects the SAML assertions in a specific format, which requires you to add custom attribute mappings to your SAML token attributes configuration. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image](common/default-attributes.png)

1. In addition to above, Boomi application expects few more attributes to be passed back in SAML response which are shown below. These attributes are also pre populated but you can review them as per your requirements.

    | Nazwa |  Atrybut źródłowy|
    | ---------------|  --------- |
    | FEDERATION_ID | user.mail |

1. On the **Set up single sign-on with SAML** page, in the **SAML Signing Certificate** section,  find **Certificate (Base64)** and select **Download** to download the certificate and save it on your computer.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. On the **Set up Boomi** section, copy the appropriate URL(s) based on your requirement.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

In this section, you'll create a test user in the Azure portal called B.Simon.

1. From the left pane in the Azure portal, select **Azure Active Directory**, select **Users**, and then select **All users**.
1. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.
1. In the **User** properties, follow these steps:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. In the **User name** field, enter the username@companydomain.extension. Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to Boomi.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. Na liście aplikacji wybierz pozycję **Boomi**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.

    ![The Add User link](common/add-assign-user.png)

1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you're expecting any role value in the SAML assertion, in the **Select Role** dialog, select the appropriate role for the user from the list and then click the **Select** button at the bottom of the screen.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

## <a name="configure-boomi-sso"></a>Configure Boomi SSO

1. In a different web browser window, sign in to your Boomi company site as an administrator.

1. Przejdź do pozycji **Nazwa firmy** i przejdź do pozycji **Set up** (Konfigurowanie).

1. Kliknij kartę **SSO Options** (Opcje logowania jednokrotnego), a następnie wykonaj poniższe kroki.

    ![Konfigurowanie logowania jednokrotnego po stronie aplikacji](./media/boomi-tutorial/tutorial_boomi_11.png)

    a. Zaznacz pole wyboru **Enable SAML Single Sign-On** (Włącz logowanie jednokrotne SAML).

    b. Kliknij przycisk **Import** (Importuj), aby przekazać pobrany certyfikat z usługi Azure AD do pola **Identity Provider Certificate** (Certyfikat dostawcy tożsamości).

    d. W polu tekstowym **Identity Provider Login URL** (Adres URL logowania dostawcy tożsamości) wstaw wartość**adresu URL logowania** z okna konfiguracji aplikacji usługi Azure AD.

    d. W obszarze **Federation Id Location** (Lokalizacja identyfikatora federacyjnego) wybierz przycisk radiowy **Federation Id is in FEDERATION_ID Attribute element** (Identyfikator federacyjny znajduje się w elemencie atrybutu FEDERATION_ID).

    e. Kliknij przycisk **Save** (Zapisz).

### <a name="create-boomi-test-user"></a>Tworzenie użytkownika testowego platformy Boomi

In order to enable Azure AD users to sign in to Boomi, they must be provisioned into Boomi. W przypadku platformy Boomi aprowizowanie jest zadaniem ręcznym.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Aby aprowizować konto użytkownika, wykonaj następujące czynności:

1. Sign in to your Boomi company site as an administrator.

1. Po zalogowaniu się przejdź do pozycji **User Management** (Zarządzanie użytkownikami) i wybierz pozycję **Users** (Użytkownicy).

    ![Użytkownicy](./media/boomi-tutorial/tutorial_boomi_001.png "Użytkownicy")

1. Kliknij ikonę **+** , aby otworzyć okno dialogowe **Add/Maintain User Roles** (Dodawanie/obsługa ról użytkowników).

    ![Użytkownicy](./media/boomi-tutorial/tutorial_boomi_002.png "Użytkownicy")

    ![Użytkownicy](./media/boomi-tutorial/tutorial_boomi_003.png "Użytkownicy")

    a. W polu tekstowym **User e-mail address** (Adres e-mail użytkownika) wpisz adres e-mail użytkownika, taki jak B.Simon@contoso.com.

    b. In the **First name** textbox, type the First name of user like B.

    d. W polu tekstowym **Last Name** (Nazwisko) wpisz nazwisko użytkownika, takie jak Simon.

    d. Wprowadź **identyfikator federacyjny** użytkownika. Każdy użytkownik musi mieć identyfikator federacyjny, który w sposób unikatowy identyfikuje użytkownika w ramach konta.

    e. Przypisz do użytkownika rolę **Standard User** (Użytkownik standardowy). Do not assign the Administrator role because that would give them normal Atmosphere access as well as single sign-on access.

    f. Kliknij przycisk **OK**.

    > [!NOTE]
    > The user will not receive a welcome notification email containing a password that can be used to log in to the AtomSphere account because their password is managed through the identity provider. You may use any other Boomi user account creation tools or APIs provided by Boomi to provision Azure AD user accounts.

## <a name="test-sso"></a>Test SSO

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Boomi w panelu dostępu powinno nastąpić automatyczne zalogowanie do platformy Boomi, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Try Boomi with Azure AD](https://aad.portal.azure.com/)