---
title: 'Tutorial: Azure Active Directory Single sign-on (SSO) integration with Cornerstone OnDemand | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Cornerstone OnDemand.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f57c5fef-49b0-4591-91ef-fc0de6d654ab
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 363b5b2dc2891166f779c98c6de1487bc45047a3
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227632"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cornerstone-ondemand"></a>Tutorial: Azure Active Directory Single sign-on (SSO) integration with Cornerstone OnDemand

In this tutorial, you'll learn how to integrate Cornerstone OnDemand with Azure Active Directory (Azure AD). When you integrate Cornerstone OnDemand with Azure AD, you can:

* Control in Azure AD who has access to Cornerstone OnDemand.
* Enable your users to be automatically signed-in to Cornerstone OnDemand with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

To learn more about SaaS app integration with Azure AD, see [What is application access and single sign-on with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

To get started, you need the following items:

* Subskrypcja usługi Azure AD. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* Cornerstone OnDemand single sign-on (SSO) enabled subscription.

## <a name="scenario-description"></a>Opis scenariusza

In this tutorial, you configure and test Azure AD SSO in a test environment.

* Aplikacja Cornerstone OnDemand obsługuje logowanie jednokrotne inicjowane przez **SP**
* Aplikacja Cornerstone OnDemand obsługuje [zautomatyzowane inicjowanie obsługi użytkowników](cornerstone-ondemand-provisioning-tutorial.md)

## <a name="adding-cornerstone-ondemand-from-the-gallery"></a>Dodawanie aplikacji Cornerstone OnDemand z galerii

Aby skonfigurować integrację aplikacji Cornerstone OnDemand z usługą Azure AD, należy dodać aplikację Cornerstone OnDemand z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **Cornerstone OnDemand** in the search box.
1. Select **Cornerstone OnDemand** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cornerstone-ondemand"></a>Configure and test Azure AD single sign-on for Cornerstone OnDemand

Configure and test Azure AD SSO with Cornerstone OnDemand using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in Cornerstone OnDemand.

To configure and test Azure AD SSO with Cornerstone OnDemand, complete the following building blocks:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
    1. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
    1. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
2. **[Configure Cornerstone OnDemand SSO](#configure-cornerstone-ondemand-sso)** - to configure the Single Sign-On settings on application side.
    1. **[Create Cornerstone OnDemand test user](#create-cornerstone-ondemand-test-user)** - to have a counterpart of B.Simon in Cornerstone OnDemand that is linked to the Azure AD representation of user.
3. **[Test SSO](#test-sso)** - to verify whether the configuration works.

## <a name="configure-azure-ad-sso"></a>Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the [Azure portal](https://portal.azure.com/), on the **Cornerstone OnDemand** application integration page, find the **Manage** section and select **Single sign-on**.
1. On the **Select a Single sign-on method** page, select **SAML**.
1. On the **Set up Single Sign-On with SAML** page, click the edit/pen icon for **Basic SAML Configuration** to edit the settings.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<company>.csod.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<company>.csod.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej aplikacji Cornerstone OnDemand Client](mailto:moreinfo@csod.com), aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

4. On the **Set up Single Sign-On with SAML** page, in the **SAML Signing Certificate** section,  find **Certificate (Base64)** and select **Download** to download the certificate and save it on your computer.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. On the **Set up Cornerstone OnDemand** section, copy the appropriate URL(s) based on your requirement.

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

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to Cornerstone OnDemand.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **Cornerstone OnDemand**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.

    ![The Add User link](common/add-assign-user.png)

1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you're expecting any role value in the SAML assertion, in the **Select Role** dialog, select the appropriate role for the user from the list and then click the **Select** button at the bottom of the screen.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

## <a name="configure-cornerstone-ondemand-sso"></a>Configure Cornerstone OnDemand SSO

Aby skonfigurować logowanie jednokrotne po stronie aplikacji **Cornerstone OnDemand**, należy wysłać pobrany **certyfikat (Base64)** i odpowiednie adresy URL skopiowane z witryny Azure Portal [zespołowi pomocy technicznej aplikacji Cornerstone OnDemand](mailto:moreinfo@csod.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-cornerstone-ondemand-test-user"></a>Tworzenie użytkownika testowego aplikacji Cornerstone OnDemand

The objective of this section is to create a user called B.Simon in Cornerstone OnDemand. Aplikacja Cornerstone OnDemand obsługuje automatyczne inicjowanie obsługi użytkowników, które jest domyślnie włączone. Więcej szczegółów dotyczących konfigurowania automatycznego inicjowania obsługi użytkowników można znaleźć [tutaj](https://docs.microsoft.com/azure/active-directory/saas-apps/cornerstone-ondemand-provisioning-tutorial).

**Jeśli potrzebujesz utworzyć użytkownika ręcznie, wykonaj następujące czynności:**

To configure user provisioning, send the information (e.g.: Name, Email) about the Azure AD user you want to provision to the [Cornerstone OnDemand support team](mailto:moreinfo@csod.com).

>[!NOTE]
>You can use any other Cornerstone OnDemand user account creation tools or APIs provided by Cornerstone OnDemand to provision Azure AD user accounts.

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Cornerstone OnDemand w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Cornerstone OnDemand, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Try Cornerstone OnDemand with Azure AD](https://aad.portal.azure.com)
