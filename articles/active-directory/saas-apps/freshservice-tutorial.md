---
title: 'Tutorial: Azure Active Directory single sign-on (SSO) integration with Freshservice | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Freshservice.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3dd22b1f-445d-45c6-8eda-30207eb9a1a8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e08ef72dca09f873ad1cfcc91e132063b88406b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227537"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshservice"></a>Tutorial: Azure Active Directory single sign-on (SSO) integration with Freshservice

In this tutorial, you'll learn how to integrate Freshservice with Azure Active Directory (Azure AD). When you integrate Freshservice with Azure AD, you can:

* Control in Azure AD who has access to Freshservice.
* Enable your users to be automatically signed-in to Freshservice with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

To learn more about SaaS app integration with Azure AD, see [What is application access and single sign-on with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

To get started, you need the following items:

* Subskrypcja usługi Azure AD. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* Freshservice single sign-on (SSO) enabled subscription.

## <a name="scenario-description"></a>Opis scenariusza

In this tutorial, you configure and test Azure AD SSO in a test environment.

* Aplikacja Freshservice obsługuje logowanie jednokrotne inicjowane przez **dostawcę usługi**

## <a name="adding-freshservice-from-the-gallery"></a>Dodawanie aplikacji Freshservice z galerii

Aby skonfigurować integrację aplikacji Freshservice z usługą Azure AD, musisz dodać aplikację Freshservice z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **Freshservice** in the search box.
1. Select **Freshservice** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-freshservice"></a>Configure and test Azure AD single sign-on for Freshservice

Configure and test Azure AD SSO with Freshservice using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in Freshservice.

To configure and test Azure AD SSO with Freshservice, complete the following building blocks:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
    1. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
    1. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
1. **[Configure Freshservice SSO](#configure-freshservice-sso)** - to configure the single sign-on settings on application side.
    1. **[Create Freshservice test user](#create-freshservice-test-user)** - to have a counterpart of B.Simon in Freshservice that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** - to verify whether the configuration works.

## <a name="configure-azure-ad-sso"></a>Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the [Azure portal](https://portal.azure.com/), on the **Freshservice** application integration page, find the **Manage** section and select **single sign-on**.
1. On the **Select a single sign-on method** page, select **SAML**.
1. On the **Set up single sign-on with SAML** page, click the edit/pen icon for **Basic SAML Configuration** to edit the settings.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. On the **Basic SAML Configuration** section, enter the values for the following fields:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<democompany>.freshservice.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<democompany>.freshservice.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. W celu uzyskania tych wartości skontaktuj się z [zespołem pomocy technicznej klienta aplikacji Freshservice](https://support.freshservice.com/). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. On the **Set up Single Sign-On with SAML** page, in the **SAML Signing Certificate** section, find **Certificate (Base64)** and select **Download** to download the certificate and save it on your computer.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. Freshservice requires SHA-256 fingerprint to get SSO working. To get SHA-256 fingerprint, perform the following steps :

    ![FingerPrint](./media/freshservice-tutorial/ic790821.png "FingerPrint")

    1. Open the [link](https://www.samltool.com/fingerprint.php) in different web browser.

    1. Open downloaded certificate (Base64) file in the Notepad and paste content in the **X.509 cert** textbox.

    1. For the Algorithm, select **sha256** from the dropdown.

    1. Click **CALCULATE FINGERPRINT**.

    1. Click on the copy icon to copy the generated **FingerPrint** and save it on your computer.

1. On the **Set up Freshservice** section on the **Azure portal**, copy the appropriate URL(s) based on your requirement.

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

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to Freshservice.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. Na liście aplikacji wybierz pozycję **Freshservice**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.

    ![The Add User link](common/add-assign-user.png)

1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you're expecting any role value in the SAML assertion, in the **Select Role** dialog, select the appropriate role for the user from the list and then click the **Select** button at the bottom of the screen.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

## <a name="configure-freshservice-sso"></a>Configure Freshservice SSO

1. To automate the configuration within Freshservice, you need to install **My Apps Secure Sign-in browser extension** by clicking **Install the extension**.

    ![My apps extension](common/install-myappssecure-extension.png)

2. After adding extension to the browser, click on **Setup Freshservice** will direct you to the Freshservice application. From there, provide the admin credentials to sign into Freshservice. The browser extension will automatically configure the application for you and automate steps 3-6.

    ![Setup configuration](common/setup-sso.png)

3. If you want to setup Freshservice manually, open a new web browser window and sign into your Freshservice company site as an administrator and perform the following steps:

4. W menu u góry kliknij pozycję **Admin** (Administrator).

    ![Admin](./media/freshservice-tutorial/ic790814.png "Admin")

5. W obszarze **Customer Portal** (Portal klienta) kliknij pozycję **Security** (Zabezpieczenia).

    ![Bezpieczeństwo](./media/freshservice-tutorial/ic790815.png "Zabezpieczenia")

6. W sekcji **Security** (Zabezpieczenia) wykonaj następujące kroki:

    ![Logowanie jednokrotne](./media/freshservice-tutorial/ic790816.png "Logowanie jednokrotne")

    a. Włącz przełącznik **Single Sign On** (Logowanie jednokrotne).

    b. Wybierz pozycję **SAML SSO** (Logowanie jednokrotne SAML).

    d. W polu tekstowym **SAML Login URL** (Adres URL logowania SAML) wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.

    d. W polu tekstowym **Logout URL** (Adres URL wylogowywania) wklej wartość **adresu URL wylogowywania** skopiowaną z witryny Azure Portal.

    e. In **Security Certificate Fingerprint** textbox, paste the **FingerPrint** value, which you have generated earlier.

    f. Kliknij pozycję **Zapisz**

### <a name="create-freshservice-test-user"></a>Tworzenie użytkownika testowego aplikacji Freshservice

To enable Azure AD users to sign in to FreshService, they must be provisioned into FreshService. W przypadku aplikacji FreshService jest to zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Sign in to your **FreshService** company site as an administrator.

2. W menu u góry kliknij pozycję **Admin** (Administrator).

    ![Admin](./media/freshservice-tutorial/ic790814.png "Admin")

3. W sekcji **User Management** (Zarządzanie użytkownikami) kliknij pozycję **Requesters** (Inicjatorzy żądania).

    ![Requesters](./media/freshservice-tutorial/ic790818.png "Requesters")

4. Kliknij przycisk **New Requester** (Nowy inicjator żądania).

    ![New Requesters](./media/freshservice-tutorial/ic790819.png "New Requesters")

5. W sekcji **New Requester** (Nowy inicjator żądania) wykonaj następujące kroki:

    ![New Requester](./media/freshservice-tutorial/ic790820.png "New Requester")  

    a. W polach tekstowych **First Name** (Imię) i **Email** (Adres e-mail) wprowadź atrybuty imienia i adresu e-mail prawidłowego konta usługi Azure Active Directory, które chcesz aprowizować.

    b. Kliknij przycisk **Save** (Zapisz).

    > [!NOTE]
    > Właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail z linkiem umożliwiającym potwierdzenie konta, zanim stanie się ono aktywne.
    >  

> [!NOTE]
> You can use any other FreshService user account creation tools or APIs provided by FreshService to provision Azure AD user accounts.

## <a name="test-sso"></a>Test SSO

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka FreshService w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji FreshService, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Try Freshservice with Azure AD](https://aad.portal.azure.com/)