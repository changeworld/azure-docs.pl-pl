---
title: 'Tutorial: Azure Active Directory integration with Bonusly | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and Bonusly.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 29fea32a-fa20-47b2-9e24-26feb47b0ae6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea9c88f8eb8ac7b72f11ff286d2294df8cb70860
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232058"
---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Tutorial: Azure Active Directory integration with Bonusly

In this tutorial, you learn how to integrate Bonusly with Azure Active Directory (Azure AD).
Integrating Bonusly with Azure AD provides you with the following benefits:

* You can control in Azure AD who has access to Bonusly.
* You can enable your users to be automatically signed-in to Bonusly (Single Sign-On) with their Azure AD accounts.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

To configure Azure AD integration with Bonusly, you need the following items:

* Subskrypcja usługi Azure AD. If you don't have an Azure AD environment, you can get a [free account](https://azure.microsoft.com/free/)
* Bonusly single sign-on enabled subscription

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Bonusly supports **IDP** initiated SSO

## <a name="adding-bonusly-from-the-gallery"></a>Adding Bonusly from the gallery

To configure the integration of Bonusly into Azure AD, you need to add Bonusly from the gallery to your list of managed SaaS apps.

**To add Bonusly from the gallery, perform the following steps:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. In the search box, type **Bonusly**, select **Bonusly** from result panel then click **Add** button to add the application.

    ![Bonusly in the results list](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

In this section, you configure and test Azure AD single sign-on with Bonusly based on a test user called **Britta Simon**.
For single sign-on to work, a link relationship between an Azure AD user and the related user in Bonusly needs to be established.

To configure and test Azure AD single sign-on with Bonusly, you need to complete the following building blocks:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Configure Bonusly Single Sign-On](#configure-bonusly-single-sign-on)** - to configure the Single Sign-On settings on application side.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Create Bonusly test user](#create-bonusly-test-user)** - to have a counterpart of Britta Simon in Bonusly that is linked to the Azure AD representation of user.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

To configure Azure AD single sign-on with Bonusly, perform the following steps:

1. In the [Azure portal](https://portal.azure.com/), on the **Bonusly** application integration page, select **Single sign-on**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Bonusly Domain and URLs single sign-on information](common/idp-reply.png)

    W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://Bonus.ly/saml/<tenant-name>`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zaktualizuj ją, stosując rzeczywisty adres URL odpowiedzi. Contact [Bonusly Client support team](https://bonus.ly/contact) to get the value. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. W sekcji **Certyfikat podpisywania SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Certyfikat podpisywania SAML**.

    ![Edytowanie certyfikatu podpisywania SAML](common/edit-certificate.png)

6. W sekcji **Certyfikat podpisywania SAML** skopiuj wartość **ODCISK PALCA** i zapisz go na komputerze.

    ![Kopiowanie wartości Odcisk palca](common/copy-thumbprint.png)

7. On the **Set up Bonusly** section, copy the appropriate URL(s) as per your requirement.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-bonusly-single-sign-on"></a>Configure Bonusly Single Sign-On

1. In a different browser window, sign in to your **Bonusly** tenant.

1. In the toolbar on the top, click **Settings** and then select **Integrations and apps**.

    ![Bonusly Social Section](./media/bonus-tutorial/ic773686.png "Bonusly")
1. Under **Single Sign-On**, select **SAML**.

1. On the **SAML** dialog page, perform the following steps:

    ![Bonusly Saml Dialog page](./media/bonus-tutorial/ic773687.png "Bonusly")

    a. In the **IdP SSO target URL** textbox, paste the value of **Login URL**, which you have copied from Azure portal.

    b. In the **IdP Login URL** textbox, paste the value of **Login URL**, which you have copied from Azure portal.

    d. In the **IdP Issuer** textbox, paste the value of **Azure AD Identifier**, which you have copied from Azure portal.
    
    d. Paste the **Thumbprint** value copied from Azure portal into the **Cert Fingerprint** textbox.
    
    e. Kliknij przycisk **Save** (Zapisz).

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. In the **User name** field type `brittasimon@yourcompanydomain.extension`. Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

In this section, you enable Britta Simon to use Azure single sign-on by granting access to Bonusly.

1. In the Azure portal, select **Enterprise Applications**, select **All applications**, then select **Bonusly**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. In the applications list, select **Bonusly**.

    ![The Bonusly link in the Applications list](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-bonusly-test-user"></a>Create Bonusly test user

In order to enable Azure AD users to sign in to Bonusly, they must be provisioned into Bonusly. In the case of Bonusly, provisioning is a manual task.

> [!NOTE]
> You can use any other Bonusly user account creation tools or APIs provided by Bonusly to provision Azure AD user accounts. 

**Aby skonfigurować aprowizację użytkowników, wykonaj następujące czynności:**

1. In a web browser window, sign in to your Bonusly tenant.

1. Kliknij pozycję **Ustawienia**.

    ![Ustawienia](./media/bonus-tutorial/ic781041.png "Ustawienia")

1. Click the **Users and bonuses** tab.

    ![Users and bonuses](./media/bonus-tutorial/ic781042.png "Users and bonuses")

1. Click **Manage Users**.

    ![Manage Users](./media/bonus-tutorial/ic781043.png "Manage Users")

1. Kliknij pozycję **Add User** (Dodaj użytkownika).

    ![Add User](./media/bonus-tutorial/ic781044.png "Dodaj użytkownika")

1. W oknie dialogowym **Add User** (Dodawanie użytkownika) wykonaj następujące kroki:

    ![Add User](./media/bonus-tutorial/ic781045.png "Dodaj użytkownika")  

    a. W polu tekstowym **First name (Imię)** wprowadź imię użytkownika, na przykład **Britta**.

    b. W polu tekstowym **Last name (Nazwisko)** wprowadź nazwisko użytkownika, na przykład **Simon**.

    d. In the **Email** textbox, enter the email of user like `brittasimon\@contoso.com`.

    d. Kliknij przycisk **Save** (Zapisz).

    > [!NOTE]
    > The Azure AD account holder receives an email that includes a link to confirm the account before it becomes active.  

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

When you click the Bonusly tile in the Access Panel, you should be automatically signed in to the Bonusly for which you set up SSO. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [What is Conditional Access in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
