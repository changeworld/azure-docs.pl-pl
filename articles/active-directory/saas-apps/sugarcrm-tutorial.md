---
title: 'Tutorial: Azure Active Directory single sign-on (SSO) integration with Sugar CRM | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and Sugar CRM.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3331b9fc-ebc0-4a3a-9f7b-bf20ee35d180
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fae7b80fd4d2fcec32bbef5e4cdf18e576412a86
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231976"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sugar-crm"></a>Tutorial: Azure Active Directory single sign-on (SSO) integration with Sugar CRM

In this tutorial, you'll learn how to integrate Sugar CRM with Azure Active Directory (Azure AD). When you integrate Sugar CRM with Azure AD, you can:

* Control in Azure AD who has access to Sugar CRM.
* Enable your users to be automatically signed-in to Sugar CRM with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

To learn more about SaaS app integration with Azure AD, see [What is application access and single sign-on with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

To get started, you need the following items:

* Subskrypcja usługi Azure AD. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* Sugar CRM single sign-on (SSO) enabled subscription.

## <a name="scenario-description"></a>Opis scenariusza

In this tutorial, you configure and test Azure AD SSO in a test environment.

* Sugar CRM supports **SP** initiated SSO

> [!NOTE]
> Identifier of this application is a fixed string value so only one instance can be configured in one tenant.

## <a name="adding-sugar-crm-from-the-gallery"></a>Adding Sugar CRM from the gallery

To configure the integration of Sugar CRM into Azure AD, you need to add Sugar CRM from the gallery to your list of managed SaaS apps.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **Sugar CRM** in the search box.
1. Select **Sugar CRM** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sugar-crm"></a>Configure and test Azure AD single sign-on for Sugar CRM

Configure and test Azure AD SSO with Sugar CRM using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in Sugar CRM.

To configure and test Azure AD SSO with Sugar CRM, complete the following building blocks:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
    1. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
    1. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
1. **[Configure Sugar CRM SSO](#configure-sugar-crm-sso)** - to configure the single sign-on settings on application side.
    1. **[Create Sugar CRM test user](#create-sugar-crm-test-user)** - to have a counterpart of B.Simon in Sugar CRM that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** - to verify whether the configuration works.

## <a name="configure-azure-ad-sso"></a>Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the [Azure portal](https://portal.azure.com/), on the **Sugar CRM** application integration page, find the **Manage** section and select **single sign-on**.
1. On the **Select a single sign-on method** page, select **SAML**.
1. On the **Set up single sign-on with SAML** page, click the edit/pen icon for **Basic SAML Configuration** to edit the settings.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. On the **Basic SAML Configuration** section, enter the values for the following fields:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca:

    | |
    |--|
    | `https://<companyname>.sugarondemand.com`|
    | `https://<companyname>.trial.sugarcrm`|

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca:

    | |
    |--|
    | `https://<companyname>.sugarondemand.com/<companyname>`|
    | `https://<companyname>.trial.sugarcrm.com/<companyname>`|
    | `https://<companyname>.trial.sugarcrm.eu/<companyname>`|

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając faktycznego adresu URL odpowiedzi i adresu URL logowania. Contact [Sugar CRM Client support team](https://support.sugarcrm.com/) to get these values. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. On the **Set up single sign-on with SAML** page, in the **SAML Signing Certificate** section,  find **Certificate (Base64)** and select **Download** to download the certificate and save it on your computer.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. On the **Set up Sugar CRM** section, copy the appropriate URL(s) based on your requirement.

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

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to Sugar CRM.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **Sugar CRM**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.

    ![The Add User link](common/add-assign-user.png)

1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you're expecting any role value in the SAML assertion, in the **Select Role** dialog, select the appropriate role for the user from the list and then click the **Select** button at the bottom of the screen.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

## <a name="configure-sugar-crm-sso"></a>Configure Sugar CRM SSO

1. In a different web browser window, sign in to your Sugar CRM company site as an administrator.

1. Go to **Admin**.

    ![Admin](./media/sugarcrm-tutorial/ic795888.png "Admin")

1. In the **Administration** section, click **Password Management**.

    ![Administration](./media/sugarcrm-tutorial/ic795889.png "Administracja")

1. Select **Enable SAML Authentication**.

    ![Administration](./media/sugarcrm-tutorial/ic795890.png "Administracja")

1. W sekcji **SAML Authentication** (Uwierzytelnianie SAML) wykonaj następujące kroki:

    ![SAML Authentication](./media/sugarcrm-tutorial/ic795891.png "SAML Authentication")  

    a. In the **Login URL** textbox, paste the value of **Login URL**, which you have copied from Azure portal.
  
    b. In the **SLO URL** textbox, paste the value of **Logout URL**, which you have copied from Azure portal.
  
    d. Open your base-64 encoded certificate in notepad, copy the content of it into your clipboard, and then paste the entire Certificate into **X.509 Certificate** textbox.
  
    d. Kliknij przycisk **Save** (Zapisz).

### <a name="create-sugar-crm-test-user"></a>Create Sugar CRM test user

In order to enable Azure AD users to sign in to Sugar CRM, they must be provisioned to Sugar CRM. In the case of Sugar CRM, provisioning is a manual task.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Sign in to your **Sugar CRM** company site as administrator.

1. Go to **Admin**.

    ![Admin](./media/sugarcrm-tutorial/ic795888.png "Admin")

1. In the **Administration** section, click **User Management**.

    ![Administration](./media/sugarcrm-tutorial/ic795893.png "Administracja")

1. Go to **Users \> Create New User**.

    ![Create New User](./media/sugarcrm-tutorial/ic795894.png "Create New User")

1. On the **User Profile** tab, perform the following steps:

    ![New User](./media/sugarcrm-tutorial/ic795895.png "New User")

    * Type the **user name**, **last name**, and **email address** of a valid Azure Active Directory user into the related textboxes.
  
1. As **Status**, select **Active**.

1. On the Password tab, perform the following steps:

    ![New User](./media/sugarcrm-tutorial/ic795896.png "New User")

    a. Type the password into the related textbox.

    b. Kliknij przycisk **Save** (Zapisz).

> [!NOTE]
> You can use any other Sugar CRM user account creation tools or APIs provided by Sugar CRM to provision Azure AD user accounts.

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

When you click the Sugar CRM tile in the Access Panel, you should be automatically signed in to the Sugar CRM for which you set up SSO. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Try Sugar CRM with Azure AD](https://aad.portal.azure.com/)

