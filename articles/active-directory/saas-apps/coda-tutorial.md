---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) z użyciem formatu CODA | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Coda.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: acaf2012-ef2e-4ce0-8467-ceece3bae50e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/23/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f015b1568098b506abc847608a1fca91ef72b6e9
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2020
ms.locfileid: "76761224"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-coda"></a>Samouczek: integracja z logowaniem jednokrotnym (SSO) Azure Active Directory z użyciem formatu CODA

W tym samouczku dowiesz się, jak zintegrować format CODA z Azure Active Directory (Azure AD). Po zintegrowaniu z usługą Azure AD formacie Coda można:

* Kontrolka w usłudze Azure AD, która ma dostęp do formatu CODA.
* Zezwól użytkownikom na automatyczne logowanie do formatu CODA przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) w formacie Coda.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Format Coda obsługuje **dostawcy tożsamości** zainicjowane przez logowanie jednokrotne

* Format Coda obsługuje funkcję aprowizacji użytkowników **just in Time**

* Po skonfigurowaniu formatu CODA można wymusić kontrolki sesji, które chronią eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolki sesji wykraczają poza dostęp warunkowy. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-coda-from-the-gallery"></a>Dodawanie formatu CODA z galerii

Aby skonfigurować integrację formatu CODA z usługą Azure AD, musisz dodać CODA z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Format Coda** w polu wyszukiwania.
1. Wybierz pozycję **Coda** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-coda"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla formatu CODA

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą formatu CODA przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w formacie Coda.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu formatu CODA, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-coda-sso)** w formacie Coda — w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz użytkownika testowego Coda](#create-coda-test-user)** — Aby uzyskać odpowiednik elementu B. Simon w formacie Coda, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji w formacie **Coda** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** wprowadź wartości dla następujących pól:

    a. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://coda.io/samlId/<CUSTOMID>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://coda.io/samlId/<CUSTOMID>/consume`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Skontaktuj się z [zespołem obsługi klienta Coda](mailto:support@coda.io) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie formatu CODA** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź username@companydomain.extension. Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do formatu CODA.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Coda**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

## <a name="configure-coda-sso"></a>Konfigurowanie logowania jednokrotnego w formacie Coda

Aby skonfigurować Logowanie jednokrotne po stronie **Coda** , należy wysłać pobrany **certyfikat (base64)** i odpowiednie skopiowane adresy URL z Azure Portal do [zespołu pomocy technicznej Coda](mailto:support@coda.io). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-coda-test-user"></a>Utwórz użytkownika testowego Coda

W tej sekcji użytkownik o nazwie Britta Simon jest tworzony w formacie Coda. Format Coda obsługuje Inicjowanie obsługi użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w formacie Coda, po uwierzytelnieniu zostanie utworzony nowy.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Coda w panelu dostępu należy automatycznie zalogować się do formatu CODA, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj format CODA z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chronić Coda przy użyciu zaawansowanej widoczności i kontrolek](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)