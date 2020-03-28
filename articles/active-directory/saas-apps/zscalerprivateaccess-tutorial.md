---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługą Zscaler Private Access (ZPA) | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a usługą Zscaler Private Access (ZPA).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 83711115-1c4f-4dd7-907b-3da24b37c89e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0a1538f640bb4722eca1d4f3a80125837593bab
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67085714"
---
# <a name="tutorial-integrate-zscaler-private-access-zpa-with-azure-active-directory"></a>Samouczek: Integracja programu Zscaler Private Access (ZPA) z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować program Zscaler Private Access (ZPA) z usługą Azure Active Directory (Azure AD). Po zintegrowaniu programu Zscaler Private Access (ZPA) z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do Zscaler Private Access (ZPA).
* Włącz użytkownikom automatyczne logowanie do programu Zscaler Private Access (ZPA) za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z obsługą logowania jednokrotnego (ZSCALER Private Access) (ZPA).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym. Zscaler Private Access (ZPA) **obsługuje** sp zainicjowane SSO.

## <a name="adding-zscaler-private-access-zpa-from-the-gallery"></a>Dodawanie zscalera prywatnego dostępu (ZPA) z galerii

Aby skonfigurować integrację programu Zscaler Private Access (ZPA) z usługą Azure AD, należy dodać program Zscaler Private Access (ZPA) z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Zscaler Private Access (ZPA)** w polu wyszukiwania.
1. Wybierz **Zscaler Private Access (ZPA)** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą programu Zscaler Private Access (ZPA) przy użyciu użytkownika testowego o nazwie **Britta Simon**. Aby użytkownik łączony sygnowania, musi działać, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w programie Zscaler Private Access (ZPA).

Aby skonfigurować i przetestować usługę Azure AD SSO przy za pomocą programu Zscaler Private Access (ZPA), wykonaj następujące elementy konstrukcyjne:

1. **[Skonfiguruj sytuasz usługi Azure AD,](#configure-azure-ad-sso)** aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj program Zscaler Private Access (ZPA),](#configure-zscaler-private-access-zpa)** aby skonfigurować ustawienia logowania przystępowego po stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD,](#create-an-azure-ad-test-user)** aby przetestować logowanie jednokrotne usługi Azure AD za pomocą Britta Simon.
4. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby umożliwić Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego Zscaler Private Access (ZPA),](#create-zscaler-private-access-zpa-test-user)** aby mieć odpowiednik Britta Simon w Zscaler Private Access (ZPA), który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Przetestuj sytą próbę sycącą,](#test-sso)** aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Zscaler (ZPA)** znajdź sekcję **Zarządzaj** i wybierz opcję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na stronie **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    1. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://samlsp.private.zscaler.com/auth/login?domain=<your-domain-name>`

    1. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL: `https://samlsp.private.zscaler.com/auth/metadata`

    > [!NOTE]
    > Wartość **sign on URL** nie jest prawdziwa. Zaktualizuj wartość za pomocą rzeczywistego adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta zscaler (ZPA),](https://help.zscaler.com/zpa-submit-ticket) aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** znajdź kod **XML metadanych federacji** i wybierz pozycję **Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

   ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie programu Zscaler Private Access (ZPA)** skopiuj odpowiednie adresy URL na podstawie wymagań.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-zscaler-private-access-zpa"></a>Konfigurowanie programu Zscaler Private Access (ZPA)

1. Aby zautomatyzować konfigurację w ramach Zscaler Private Access (ZPA), musisz zainstalować **rozszerzenie przeglądarki My Apps Secure Sign-in,** klikając pozycję Zainstaluj **rozszerzenie**.

    ![Rozszerzenie Moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, kliknij **Instalator Zscaler Private Access (ZPA)** przekieruje Cię do aplikacji Zscaler Private Access (ZPA). Stamtąd podaj poświadczenia administratora, aby zalogować się do Zscaler Private Access (ZPA). Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację dla Ciebie i zautomatyzuje kroki 3-6.

    ![Konfiguracja instalacji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować program Zscaler Private Access (ZPA), otwórz nowe okno przeglądarki internetowej i zaloguj się do witryny firmy Zscaler Private Access (ZPA) jako administrator i wykonaj następujące czynności:

4. Po lewej stronie menu kliknij pozycję **Administracja** i przejdź do sekcji **UWIERZYTELNIANIE,** kliknij pozycję **Konfiguracja idP**.

    ![Administracja administratora dostępu prywatnego zscaler](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-administration.png)

5. W prawym górnym rogu kliknij pozycję **Dodaj konfigurację idp**. 

    ![Zscaler Administrator dostępu prywatnego idp](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-idp.png)

6. Na stronie **Dodaj konfigurację IdP** wykonaj następujące czynności:
 
    ![Zscaler Administrator dostępu prywatnego wybierz](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-select.png)

    a. Kliknij **pozycję Wybierz plik,** aby przekazać pobrany plik metadanych z usługi Azure AD w polu **Przekazywanie pliku metadanych IdP.**

    b. Odczytuje **metadane IdP** z usługi Azure AD i wypełnia wszystkie informacje o polach, jak pokazano poniżej.

    ![Zscaler Private Access Administrator config](./media/zscalerprivateaccess-tutorial/config.png)

    d. Wybierz domenę z pola **Domeny.**
    
    d. Kliknij przycisk **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w witrynie Azure portal o nazwie Britta Simon.

1. Z lewego okienka w witrynie Azure portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **pozycję Nowy użytkownik** u góry ekranu.
1. We właściwościach **Użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `Britta Simon`.  
   1. W polu **Nazwa użytkownika** username@companydomain.extensionwprowadź pole . Na przykład `BrittaSimon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz Britta Simon do korzystania z usługi Azure logowania jednokrotnego, udzielając dostępu do Zscaler Private Access (ZPA).

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Zscaler Private Access (ZPA)**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz **pozycję Britta Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

### <a name="create-zscaler-private-access-zpa-test-user"></a>Utwórz użytkownika testowego Zscaler Private Access (ZPA)

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w Zscaler Private Access (ZPA). Aby dodać użytkowników na platformie Zscaler Private Access (ZPA), należy współpracować z [zespołem pomocy technicznej Zscaler Private Access (ZPA).](https://help.zscaler.com/zpa-submit-ticket)

### <a name="test-sso"></a>Test SSO

Po wybraniu kafelka Zscaler Private Access (ZPA) w Panelu dostępu należy zalogować się automatycznie do zwężonego programu Zscaler Private Access (ZPA), dla którego skonfigurowano logującą się logującą logującą log. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)