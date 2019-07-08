---
title: 'Samouczek: Integracja usługi Azure Active Directory z menedżerem certyfikatów Sectigo | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Sectigo Menedżera certyfikatów.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 62cd6987-3373-4b58-b1ff-589f4a3d70a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0447a8dd464363ae7e076dde2520565005d7c0a5
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67588235"
---
# <a name="tutorial-azure-active-directory-integration-with-sectigo-certificate-manager"></a>Samouczek: Integracja usługi Azure Active Directory z menedżerem certyfikatów Sectigo

W tym samouczku dowiesz się, jak zintegrować Menedżera certyfikatów Sectigo za pomocą usługi Azure Active Directory (Azure AD).

Integracja Menedżera certyfikatów Sectigo z usługą Azure AD zapewnia następujące korzyści:

* Możesz użyć usługi Azure AD w celu kontrolowania, kto ma dostęp do Menedżera certyfikatów Sectigo.
* Użytkownicy mogą automatycznie zalogować się do Menedżera certyfikatów Sectigo przy użyciu konta usługi Azure AD (logowanie jednokrotne).
* Możesz zarządzać kontami z jednej centralnej lokalizacji — witryny Azure Portal.

Aby uzyskać więcej informacji na temat oprogramowania jako usługi (SaaS) integracji aplikacji z usługą Azure AD, zobacz [logowanie jednokrotne do aplikacji w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z menedżerem certyfikatów Sectigo, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcję usługi Azure AD, Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed przystąpieniem do wykonywania.
* Menedżer certyfikatów Sectigo subskrypcji przy użyciu logowania jednokrotnego włączone.

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka konfigurowania i testowania usługi Azure AD logowanie jednokrotne w środowisku testowym, a integracja Menedżera certyfikatów Sectigo z usługą Azure AD.

Menedżer certyfikatów Sectigo obsługuje następujące funkcje:

* **Zainicjowane przez Dostawcę logowania jednokrotnego**
* **Logowanie jednokrotne inicjowane przez dostawcę tożsamości**

## <a name="add-sectigo-certificate-manager-in-the-azure-portal"></a>Dodaj Menedżera certyfikatów Sectigo w witrynie Azure portal

Aby zintegrować Menedżera certyfikatów Sectigo z usługą Azure AD, należy dodać Menedżera certyfikatów Sectigo z listą zarządzanych aplikacji SaaS.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W menu po lewej stronie wybierz **usługi Azure Active Directory**.

    ![Opcja usługi Azure Active Directory](common/select-azuread.png)

1. Wybierz pozycję **Aplikacje dla przedsiębiorstw** > **Wszystkie aplikacje**.

    ![Okienko Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Aby dodać aplikację, wybierz pozycję **nową aplikację**.

    ![Nowa opcja aplikacji](common/add-new-app.png)

1. W polu wyszukiwania wprowadź **Menedżera certyfikatów Sectigo**. W wynikach wyszukiwania wybierz **Menedżera certyfikatów Sectigo**, a następnie wybierz pozycję **Dodaj**.

    ![Menedżer certyfikatów Sectigo na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji, konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą Sectigo Menedżera certyfikatów na podstawie użytkownika testowego, o nazwie **Britta Simon**. Dla logowania jednokrotnego do pracy należy ustanowić w Menedżerze certyfikatów Sectigo połączone relację między użytkownikiem usługi Azure AD i powiązanych użytkowników.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z menedżerem certyfikatów Sectigo, należy wykonać poniższe bloki konstrukcyjne:

| Zadanie | Opis |
| --- | --- |
| **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)** | Umożliwia użytkownikom korzystać z tej funkcji. |
| **[Konfigurowanie Menedżera certyfikatów Sectigo logowania jednokrotnego](#configure-sectigo-certificate-manager-single-sign-on)** | Konfiguruje pojedynczy ustawień logowania jednokrotnego w aplikacji. |
| **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** | Testy usługi Azure AD logowanie jednokrotne dla użytkownika o nazwie Britta Simon. |
| **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** | Umożliwia Britta Simon korzystać z usługi Azure AD logowania jednokrotnego. |
| **[Tworzenie użytkownika testowego Sectigo Menedżer certyfikatów](#create-a-sectigo-certificate-manager-test-user)** | Tworzy odpowiednikiem Britta Simon w Sectigo Menedżera certyfikatów połączonym z usługi Azure AD reprezentacja użytkownika. |
| **[Testowanie logowania jednokrotnego](#test-single-sign-on)** | Sprawdza, czy konfiguracja działa. |

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji skonfigurujesz usługi Azure AD logowanie jednokrotne z menedżerem certyfikatów Sectigo w witrynie Azure portal.

1. W [witryny Azure portal](https://portal.azure.com/)w **Menedżera certyfikatów Sectigo** okienko integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Konfigurowanie opcji rejestracji jednokrotnej](common/select-sso.png)

1. W **wybierz jedną metodę logowania jednokrotnego** okienku wybierz **SAML** lub **SAML/WS-Fed** trybu, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

1. W **Ustaw się logowania jednokrotnego przy użyciu protokołu SAML** okienku wybierz **Edytuj** (ikonę ołówka) aby otworzyć **podstawową konfigurację protokołu SAML** okienka.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W **podstawową konfigurację protokołu SAML** okienko, aby skonfigurować *inicjowane przez dostawcę tożsamości tryb*, wykonaj następujące czynności:

    1. W **identyfikator** wprowadź jedną z następujących adresów URL:
       * protokół https:\//cert-manager.com/shibboleth
       * protokół https:\//hard.cert-manager.com/shibboleth

    1. W **adres URL odpowiedzi** wprowadź jedną z następujących adresów URL:
        * https:\//cert-manager.com/Shibboleth.sso/SAML2/POST
        * https:\//hard.cert-manager.com/Shibboleth.sso/SAML2/POST

    1. Wybierz **Ustaw dodatkowe adresy URL**.

    1. W **tan przekaźnika** wprowadź jedną z następujących adresów URL:
       * protokół https:\//cert-manager.com/customer/SSLSupport/idp
       * protokół https:\//hard.cert-manager.com/customer/SSLSupport/idp

    ![Menedżer certyfikatów Sectigo domena i adresy URL pojedynczy informacje logowania jednokrotnego](common/idp-relay.png)

1.  Aby skonfigurować aplikację w *zainicjowanego przez dostawcę usług trybu*, wykonaj następujące czynności:

    * W **adres URL logowania** wprowadź jedną z następujących adresów URL:
      * https:\//cert-manager.com/Shibboleth.sso/Login
      * https:\//hard.cert-manager.com/Shibboleth.sso/Login

      ![Menedżer certyfikatów Sectigo domena i adresy URL pojedynczy informacje logowania jednokrotnego](common/both-signonurl.png)

1. W **Ustaw się logowanie jednokrotne z SAML** okienko w **certyfikat podpisywania SAML** zaznacz **Pobierz** obok **certyfikat (Base64)** . Wybierz opcję pobierania, w zależności od wymagań. Zapisz certyfikat na komputerze.

    ![Opcja pobierania certyfikat (Base64)](common/certificatebase64.png)

1. W **Ustaw Menedżera certyfikatów Sectigo** sekcji, skopiuj następujące adresy URL, zgodnie z wymaganiami:

    * Adres URL logowania
    * Identyfikator usługi Azure AD
    * Adres URL wylogowywania

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-sectigo-certificate-manager-single-sign-on"></a>Konfigurowanie Menedżera certyfikatów Sectigo logowania jednokrotnego

Aby skonfigurować logowanie jednokrotne stronie Sectigo Menedżera certyfikatów, wysyłanie pobrany plik certyfikatu (kodowanie Base64) i odpowiednie adresy URL, które zostały skopiowane z portalu Azure w celu [zespołem pomocy technicznej w Menedżerze certyfikatów Sectigo](https://sectigo.com/support). Menedżer certyfikatów Sectigo zespołem pomocy technicznej używa tych informacji, wysyłanych przez nich, aby upewnić się, że SAML logowania jednokrotnego połączenie jest prawidłowo po obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji utworzysz użytkownika testowego o nazwie Britta Simon w witrynie Azure Portal.

1. W witrynie Azure Portal wybierz kolejno pozycje **Azure Active Directory** > **Użytkownicy** > **Wszyscy użytkownicy**.

    ![Użytkownicy i wszystkie opcje użytkowników](common/users.png)

1. Wybierz przycisk **Nowy użytkownik**.

    ![Nowa opcja użytkownika](common/new-user.png)

1. W okienku **Użytkownik** wykonaj następujące czynności:

    1. W polu **Nazwa** wpisz **BrittaSimon**.
  
    1. W **nazwa_użytkownika** wprowadź **brittasimon\@\<Twojej domeny firmy >.\< rozszerzenie\>** . Na przykład **brittasimon\@contoso.com**.

    1. Wybierz **hasło Show** pole wyboru. Zanotuj wartość, która jest wyświetlana w **hasło** pole.

    1. Wybierz pozycję **Utwórz**.

    ![W okienku użytkownika](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można przyznać Britta Simon dostęp do Menedżera certyfikatów Sectigo tak korzystaniem Azure logowania jednokrotnego.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje** > **Menedżera certyfikatów Sectigo**.

    ![Okienko Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Na liście aplikacji wybierz **Menedżera certyfikatów Sectigo**.

    ![Menedżer certyfikatów Sectigo na liście aplikacji](common/all-applications.png)

1. W menu wybierz pozycję **Użytkownicy i grupy**.

    ![Opcja użytkowników i grup](common/users-groups-blade.png)

1. Wybierz przycisk **Dodaj użytkownika**. Następnie w okienku **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

1. W **użytkowników i grup** okienku wybierz **Britta Simon** na liście Użytkownicy. Wybierz pozycję **Wybierz**.

1. Jeśli są oczekiwane wartości roli dla asercji SAML w **wybierz rolę** okienku zaznacz odpowiednie rolę dla użytkownika z listy. Wybierz pozycję **Wybierz**.

1. W **Dodaj przydziału** okienku wybierz **przypisać**.

### <a name="create-a-sectigo-certificate-manager-test-user"></a>Tworzenie użytkownika testowego Sectigo Menedżer certyfikatów

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w Menedżerze certyfikatów Sectigo. Praca z [zespołem pomocy technicznej w Menedżerze certyfikatów Sectigo](https://sectigo.com/support) Dodaj użytkownika na platformie Sectigo Menedżera certyfikatów. Użytkownicy muszą być tworzone i aktywowana, aby używać logowania jednokrotnego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji możesz przetestować konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu portalu Moje aplikacje.

Po skonfigurowaniu rejestracji jednokrotnej, po wybraniu **Menedżera certyfikatów Sectigo** w portalu Moje aplikacje automatycznie zalogowaniu się do Menedżera certyfikatów Sectigo. Aby uzyskać więcej informacji na temat portalu Moje aplikacje, zobacz [dostępu i użycia aplikacji w portalu Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej, przejrzyj następujące artykuły:

- [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


