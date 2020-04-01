---
title: 'Samouczek: Integracja usługi Azure Active Directory z expensein | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a expensein.
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
ms.date: 06/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c09542013dff3a18965d1070216a938c26a144e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67102840"
---
# <a name="tutorial-integrate-expensein-with-azure-active-directory"></a>Samouczek: Integrowanie expensein z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować ExpenseIn z usługą Azure Active Directory (Azure AD). Po zintegrowaniu ExpenseIn z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do ExpenseIn.
* Włącz użytkownikom automatyczne logowanie do expensein za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* ExpenseIn subskrypcja z włączoną rejestracją jednokrotną (Logowanie jednokrotne).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym. ExpenseIn obsługuje **sp i idp** zainicjowane SSO.

## <a name="adding-expensein-from-the-gallery"></a>Dodawanie expensein z galerii

Aby skonfigurować integrację expensein do usługi Azure AD, należy dodać ExpenseIn z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **ExpenseIn** w polu wyszukiwania.
1. Wybierz **expenseIn** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą expenseIn przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik syt/r działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w usłudze ExpenseIn.

Aby skonfigurować i przetestować przychylić się do usługi Azure AD z pomocą usługi ExpenseIn, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj sytuasz usługi Azure AD,](#configure-azure-ad-sso)** aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj ExpenseIn,](#configure-expensein)** aby skonfigurować ustawienia logowania dodatkowego po stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD,](#create-an-azure-ad-test-user)** aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
4. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby włączyć B.Simon do korzystania z usługi Azure AD logowania jednokrotnego.
5. **[Utwórz expenseIn użytkownika testowego,](#create-expensein-test-user)** aby mieć odpowiednik B.Simon w expenseIn, który jest połączony z reprezentacji usługi Azure AD użytkownika.
6. **[Przetestuj sytą próbę sycącą,](#test-sso)** aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie Integracja aplikacji **ExpenseIn** znajdź sekcję **Zarządzaj** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wykonaj następujący krok:

    W polu tekstowym **Odpowiedz na adres URL** wpisz dowolny z adresów URL:

    | |
    |--|
    | `https://app.expensein.com/samlcallback` |
    | `https://mobileapi.expensein.com/identity/samlcallback` |

5. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    W polu **tekstowym "Podpisywanie adresu URL"** wpisz adres URL:`https://app.expensein.com/saml`

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** kliknij przycisk kopiuj przycisk **kopiowania adresów url metadanych federacji aplikacji,** a następnie kliknij przycisk **Pobierz,** aby pobrać **certyfikat (Base64)** i zapisać go na komputerze.

   ![Link do pobierania certyfikatu](./media/expensein-tutorial/copy-metdataurl-certificate.png)

1. W sekcji **Konfigurowanie wydatków** skopiuj odpowiednie adresy URL na podstawie wymagań.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-expensein"></a>Konfigurowanie expensein

1. Aby zautomatyzować konfigurację w ramach ExpenseIn, musisz zainstalować **rozszerzenie przeglądarki My Apps Secure Sign-in,** klikając pozycję Zainstaluj **rozszerzenie**.

    ![Rozszerzenie Moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, kliknij **koszty instalacjiIn** przekieruje Cię do aplikacji ExpenseIn. Stamtąd podaj poświadczenia administratora, aby zalogować się do ExpenseIn. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację dla Ciebie i zautomatyzuje kroki 3-5.

    ![Konfiguracja instalacji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować expenseIn, otwórz nowe okno przeglądarki sieci Web i zaloguj się do witryny firmy ExpenseIn jako administrator i wykonaj następujące czynności:

4. Kliknij **pozycję Administrator** w górnej części strony, a następnie przejdź do **logowania jednokrotnego** i kliknij pozycję **Dodaj dostawcę**.

     ![Konfiguracja ExpenseIn](./media/expenseIn-tutorial/config01.png)

5. W wyskakującym okienku **Nowy dostawca tożsamości** wykonaj następujące czynności:

    ![Konfiguracja ExpenseIn](./media/expenseIn-tutorial/config02.png)

    a. W polu **tekstowym Nazwa dostawcy** wpisz nazwę typu ex:Azure.

    b. Wybierz **pozycję Tak** jako **zezwalaj na logowanie do dostawcy**.

    d. W polu **tekstowym Docelowy adres URL** wklej wartość **adresu URL logowania,** który został skopiowany z witryny Azure portal.

    d. W polu tekstowym **Wystawca** wklej wartość **identyfikatora usługi Azure AD,** który został skopiowany z witryny Azure portal.

    e. Otwórz certyfikat (Base64) w Notatniku, skopiuj jego zawartość i wklej ją w polu tekstowym **Certyfikat.**

    f. Kliknij przycisk **Utwórz**.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do ExpenseIn.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **ExpenseIn**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

### <a name="create-expensein-test-user"></a>Utwórz expenseIn użytkownika testowego

Aby umożliwić użytkownikom usługi Azure AD zalogowanie się do ExpenseIn, muszą one być aprowizowana do ExpenseIn. W ExpenseIn inicjowania obsługi administracyjnej jest zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do ExpenseIn jako administrator.

2. Kliknij **na Admin** w górnej części strony, a następnie przejdź do **użytkowników** i kliknij pozycję **Nowy użytkownik**.

     ![Konfiguracja ExpenseIn](./media/expenseIn-tutorial/config03.png)

3. W wyskakującym okienku **Szczegóły** wykonaj następujące czynności:

    ![Konfiguracja ExpenseIn](./media/expenseIn-tutorial/config04.png)

    a. W polu **tekstowym Imię** wprowadź imię użytkownika, takiego jak **B**.

    b. W polu **tekstowym Nazwisko** wprowadź nazwisko użytkownika, takiego jak **Simon**.

    d. W polu tekstowym **Email** (Adres e-mail) wprowadź adres e-mail użytkownika, na przykład `B.Simon@contoso.com`.

    d. Kliknij przycisk **Utwórz**.

### <a name="test-sso"></a>Test SSO

Po wybraniu kafelka ExpenseIn w Panelu dostępu należy automatycznie zalogować się do expensein, dla którego skonfigurowano logującą się logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
