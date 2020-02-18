---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) z logowaniem jednokrotnym dla Skytap | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i logowanie jednokrotne dla Skytap.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d6cb7ab2-da1a-4015-8e6f-c0c47bb6210f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/13/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 847534ee28880cd2206c6abef605e695cc968f80
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2020
ms.locfileid: "77370695"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-single-sign-on-for-skytap"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) z logowaniem jednokrotnym dla Skytap

W tym samouczku dowiesz się, jak zintegrować Logowanie jednokrotne dla Skytap z usługą Azure Active Directory (Azure AD). Po zintegrowaniu logowania jednokrotnego w usłudze Skytap z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do logowania jednokrotnego dla Skytap.
* Zezwól użytkownikom na automatyczne logowanie do rejestracji Jednokrotnej dla Skytap przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Logowanie jednokrotne dla subskrypcji z włączonym logowaniem jednokrotnym (SSO) usługi Skytap.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Logowanie jednokrotne dla Skytap obsługuje usługę **SP i dostawcy tożsamości** zainicjowano Logowanie jednokrotne
* Po skonfigurowaniu logowania jednokrotnego dla usługi Skytap można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozszerzy od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-single-sign-on-for-skytap-from-the-gallery"></a>Dodawanie logowania jednokrotnego do Skytap z galerii

Aby skonfigurować integrację logowania jednokrotnego dla Skytap w usłudze Azure AD, musisz dodać Logowanie jednokrotne do Skytap z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz Logowanie jednokrotne **dla Skytap** w polu wyszukiwania.
1. Wybierz pozycję **Logowanie jednokrotne dla Skytap** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-single-sign-on-for-skytap"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD na potrzeby logowania jednokrotnego dla Skytap

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą logowania jednokrotnego dla Skytap przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w ramach rejestracji Jednokrotnej dla Skytap.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą logowania jednokrotnego dla Skytap, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne na potrzeby logowania JEDNOkrotnego](#configure-single-sign-on-for-skytap-sso)** w usłudze Skytap, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz Logowanie jednokrotne dla użytkownika testowego Skytap](#create-single-sign-on-for-skytap-test-user)** , aby dysponować odpowiednikiem B. Simon w przypadku rejestracji Jednokrotnej dla Skytap, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. Na [Azure Portal](https://portal.azure.com/)na stronie integracja z **logowaniem** jednokrotnym dla aplikacji Skytap Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę tożsamości**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    a. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `http://pingone.com/<custom EntityID>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://sso.connect.pingidentity.com/sso/sp/ACS.saml2`

1. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    d. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://sso.connect.pingidentity.com/sso/sp/initsso?saasid=<saasid>&idpid=<idpid>`

    
    e. W polu tekstowym **Stan przekaźnika** wpisz adres URL, korzystając z następującego wzorca: `https://pingone.com/1.0/<custom ID>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora, adresu URL odpowiedzi, adresu URL logowania i stanu przekazywania. Aby uzyskać te wartości, należy skontaktować się z logowaniem jednokrotnym [dla zespołu obsługi klienta Skytap](mailto:support@skytap.com) . Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać plik metadanych i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie logowania jednokrotnego dla Skytap** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź username@companydomain.extension. Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do logowania jednokrotnego dla Skytap.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Logowanie jednokrotne dla Skytap**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

## <a name="configure-single-sign-on-for-skytap-sso"></a>Konfigurowanie logowania jednokrotnego na potrzeby logowania jednokrotnego w usłudze Skytap

Aby skonfigurować Logowanie jednokrotne przy użyciu logowania jednokrotnego **dla usługi Skytap** , musisz wysłać pobrany **kod XML metadanych Federacji** i odpowiednie skopiowane adresy URL z Azure Portal na [Logowanie jednokrotne dla zespołu obsługi klienta Skytap](mailto:support@skytap.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.


### <a name="create-single-sign-on-for-skytap-test-user"></a>Utwórz Logowanie jednokrotne dla użytkownika testowego Skytap

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w przypadku logowania jednokrotnego dla Skytap. Pracuj z [logowaniem jednokrotnym dla zespołu obsługi klienta Skytap](mailto:support@skytap.com) , aby dodać użytkowników w ramach logowania jednokrotnego dla platformy Skytap. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Logowanie jednokrotne dla Skytap w panelu dostępu należy automatycznie zalogować się do logowania jednokrotnego dla Skytap, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj usługę Azure AD](https://aad.portal.azure.com/)

