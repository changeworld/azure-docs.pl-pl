---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą ExpenseIn | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i ExpenseIn.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 6ac8053b-a216-45d8-bf5e-ecd37d808e57
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/31/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b849e63b758dca777df2be682b28bc4e10e58330
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481131"
---
# <a name="tutorial-integrate-expensein-with-azure-active-directory"></a>Samouczek: Integracja ExpenseIn za pomocą usługi Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować ExpenseIn w usłudze Azure Active Directory (Azure AD). W ramach ExpenseIn integracji z usługą Azure AD, możesz wykonywać następujące czynności:

* Kontrolowanie w usłudze Azure AD, kto ma dostęp do ExpenseIn.
* Umożliwianie użytkownikom można automatycznie zalogowany do ExpenseIn za pomocą kont usługi Azure AD.
* Zarządzanie Twoimi kontami w jednej centralnej lokalizacji — witryny Azure portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/).
* ExpenseIn logowania jednokrotnego (SSO) włączone subskrypcji.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku, skonfiguruj i przetestuj logowania jednokrotnego usługi Azure AD w środowisku testowym. Obsługuje ExpenseIn **dodatkiem SP oraz dostawców tożsamości** jednokrotne logowanie inicjowane przez.

## <a name="adding-expensein-from-the-gallery"></a>Dodawanie ExpenseIn z galerii

Aby skonfigurować integrację ExpenseIn w usłudze Azure AD, należy dodać ExpenseIn z galerii z listą zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory** usługi.
1. Przejdź do **aplikacje dla przedsiębiorstw** , a następnie wybierz **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz **nową aplikację**.
1. W **Dodaj z galerii** sekcji, wpisz **ExpenseIn** w polu wyszukiwania.
1. Wybierz **ExpenseIn** z wyników panelu, a następnie dodać aplikację. Odczekaj kilka sekund, podczas gdy aplikacja zostanie dodany do Twojej dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

Konfiguracja i testowanie logowania jednokrotnego usługi Azure AD za pomocą ExpenseIn za pomocą użytkownika testu o nazwie **B. Simon**. Logowanie Jednokrotne do pracy musisz ustanowić relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w ExpenseIn.

Aby skonfigurować i przetestować logowania jednokrotnego usługi Azure AD za pomocą ExpenseIn, wykonaj poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-sso)**  aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie ExpenseIn](#configure-expensein)**  do konfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  do testowania usługi Azure AD logowanie jednokrotne za pomocą B. Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  umożliwiające Simon B. korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego ExpenseIn](#create-expensein-test-user)**  mieć odpowiednikiem B. Simon ExpenseIn połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-sso)**  Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

Wykonaj następujące kroki, aby włączyć logowania jednokrotnego usługi Azure AD w witrynie Azure portal.

1. W [witryny Azure portal](https://portal.azure.com/)na **ExpenseIn** strona integracji aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **logowanie jednokrotne**.
1. Na **wybierz jedną metodę logowania jednokrotnego** wybierz **SAML**.
1. Na **Ustaw się logowanie jednokrotne z SAML** kliknij ikonę edycji/pióra **podstawową konfigurację protokołu SAML** edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Na **podstawową konfigurację protokołu SAML** sekcji, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb, wykonaj następujące kroki:

    W **adres URL odpowiedzi** tekstu wpisz dowolny adres URL:

    | |
    |--|
    | `https://app.expensein.com/samlcallback` |
    | `https://mobileapi.expensein.com/identity/samlcallback` |

5. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    W polu tekstowym **Adres URL logowania** wpisz adres URL: `https://app.expensein.com/saml`

1. Na **Ustaw się logowanie jednokrotne z SAML** strony w **certyfikat podpisywania SAML** sekcji, kliknij przycisk kopiowania, aby skopiować **adres Url metadanych Federacji aplikacji** i kliknij przycisk **Pobierz** można pobrać **certyfikat (Base64)** i zapisz go na komputerze.

   ![Link pobierania certyfikatu](./media/expensein-tutorial/copy-metdataurl-certificate.png)

1. Na **Konfigurowanie ExpenseIn** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-expensein"></a>Konfigurowanie ExpenseIn

1. W oknie przeglądarki internetowej innej Zaloguj się w ExpenseIn jako Administrator.

2. Kliknij pozycję **administratora** górnej części strony a następnie przejdź do **logowania jednokrotnego** i kliknij przycisk **dostawcy Dodaj**.

     ![Konfiguracja ExpenseIn](./media/expenseIn-tutorial/config01.png)

3. Na **nowego dostawcę tożsamości** wyskakujące, wykonaj następujące czynności:

    ![Konfiguracja ExpenseIn](./media/expenseIn-tutorial/config02.png)

    a. W **Nazwa dostawcy** tekstu wpisz nazwę, takich jak na przykład: Azure.

    b. W **docelowy adres Url** tekstu pole, Wklej wartość **adres URL logowania**, które zostały skopiowane z witryny Azure portal.

    c. W **wystawcy** tekstu pole, Wklej wartość **usługi Azure AD identyfikator**, które zostały skopiowane z witryny Azure portal.

    d. Otwórz certyfikat (Base64) w programie Notatnik, skopiuj jego zawartość i wklej ją w **certyfikatu** pola tekstowego.

    e. Kliknij pozycję **Utwórz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzymy użytkownika testowego w witrynie Azure portal, o nazwie B. Simon.

1. W okienku po lewej stronie w witrynie Azure portal wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.
1. Wybierz **nowego użytkownika** w górnej części ekranu.
1. W **użytkownika** właściwości, wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B. Simon`.  
   1. W **nazwa_użytkownika** wprowadź username@companydomain.extension. Na przykład `BrittaSimon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można udostępnić Simon B. do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do ExpenseIn.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz **ExpenseIn**.
1. Na stronie Przegląd usługi aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **użytkowników i grup**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz **Dodaj użytkownika**, a następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W **użytkowników i grup** okno dialogowe, wybierz opcję **B. Simon** z listy użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. Jeśli oczekujesz wszelkie wartości roli dla asercji SAML w **wybierz rolę** okno dialogowe, wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-expensein-test-user"></a>Tworzenie użytkownika testowego ExpenseIn

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do ExpenseIn, musi być obsługiwana w ExpenseIn. W ExpenseIn Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do ExpenseIn jako Administrator.

2. Kliknij pozycję **administratora** górnej części strony a następnie przejdź do **użytkowników** i kliknij przycisk **nowego użytkownika**.

     ![Konfiguracja ExpenseIn](./media/expenseIn-tutorial/config03.png)

3. Na **szczegóły** wyskakujące, wykonaj następujące czynności:

    ![Konfiguracja ExpenseIn](./media/expenseIn-tutorial/config04.png)

    a. W **imię** tekstu Wprowadź imię użytkownika, takich jak **B**.

    b. W polu tekstowym **Last name** (Nazwisko) wprowadź nazwisko użytkownika, na przykład **Simon**.

    c. W **E-mail** tekstu wprowadź adres e-mail użytkownika, takich jak `B. Simon@contoso.com`.

    d. Kliknij pozycję **Utwórz**.

### <a name="test-sso"></a>Test SSO

Po wybraniu kafelka ExpenseIn w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze ExpenseIn, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
