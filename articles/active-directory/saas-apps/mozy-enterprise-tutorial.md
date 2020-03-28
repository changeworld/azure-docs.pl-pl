---
title: 'Samouczek: Integracja usługi Azure Active Directory z mozy enterprise | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a mozy enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 489b5e62-85c2-45c9-8766-326632d48114
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: 7d3b7f8982a24856818cbb6ce86060beac969fc2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74233509"
---
# <a name="tutorial-azure-active-directory-integration-with-mozy-enterprise"></a>Samouczek: Integracja usługi Azure Active Directory z mozy enterprise

W tym samouczku dowiesz się, jak zintegrować mozy enterprise z usługą Azure Active Directory (Azure AD).
Integracja mozy enterprise z usługą Azure AD zapewnia następujące korzyści:

* Można kontrolować w usłudze Azure AD, który ma dostęp do Mozy Enterprise.
* Można włączyć użytkowników, aby automatycznie zalogować się do Mozy Enterprise (logowanie jednokrotne) z ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z mozy enterprise, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja z obsługą logowania jednokrotnego Mozy Enterprise

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Mozy Enterprise obsługuje sso inicjowane przez **SP**

## <a name="adding-mozy-enterprise-from-the-gallery"></a>Dodawanie Mozy Enterprise z galerii

Aby skonfigurować integrację mozy enterprise z usługą Azure AD, należy dodać mozy enterprise z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Mozy Enterprise z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Mozy Enterprise**, wybierz **Mozy Enterprise** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

     ![Mozy Enterprise na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego z Mozy Enterprise na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w mozy enterprise.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą mozy enterprise, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie jednokrotne Mozy Enterprise](#configure-mozy-enterprise-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego Mozy Enterprise](#create-mozy-enterprise-test-user)** — aby mieć odpowiednik Britta Simon w Mozy Enterprise, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą programu Mozy Enterprise, wykonaj następujące czynności:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie Integracja aplikacji **Mozy Enterprise** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Mozy Enterprise Domain i adresy URL — informacje o loguchu jednokrotnym](common/sp-signonurl.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<tenantname>.Mozyenterprise.com`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta firmy Mozy Enterprise,](https://support.mozy.com/) aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie mozy enterprise** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-mozy-enterprise-single-sign-on"></a>Konfigurowanie logowania jednokrotnego mozy enterprise

1. W innym oknie przeglądarki internetowej zaloguj się do witryny firmy Mozy Enterprise jako administrator.

2. W sekcji **Konfiguracja** kliknij pozycję **Zasady uwierzytelniania**.
   
    ![Zasady uwierzytelniania](./media/mozy-enterprise-tutorial/ic777314.png "Zasady uwierzytelniania")

3. W sekcji **Zasady uwierzytelniania** wykonaj następujące czynności:
   
    ![Zasady uwierzytelniania](./media/mozy-enterprise-tutorial/ic777315.png "Zasady uwierzytelniania")
   
    a. Wybierz **pozycję Usługa katalogowa** jako **dostawca**.
   
    b. Wybierz **pozycję Użyj przycisku LDAP**.
   
    d. Kliknij kartę **SAML Authentication** (Uwierzytelnianie SAML).
   
    d. Wklej **adres URL logowania,** który został skopiowany z witryny Azure portal do pola tekstowego **url uwierzytelniania.**
   
    e. Wklej **identyfikator usługi Azure AD**, który został skopiowany z witryny Azure portal do pola tekstowego punktu **końcowego SAML.**
   
    f. Otwórz pobrany certyfikat zakodowany base-64 w notatniku, skopiuj jego zawartość do schowka, a następnie wklej cały certyfikat do pola **tekstowego certyfikatu SAML.**
   
    g. Wybierz **włącz logowanie logowania przy logowaniach logowania do logowania za pomocą poświadczeń sieciowych**.
   
    h. Kliknij **pozycję Zapisz zmiany**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz **pozycję Nowy użytkownik** u góry ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **\@brittasimon yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Britta Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do Mozy Enterprise.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz **pozycję Wszystkie aplikacje**, a następnie wybierz pozycję **Mozy Enterprise**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **mozy enterprise**.

    ![Łącze Mozy Enterprise na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-mozy-enterprise-test-user"></a>Utwórz użytkownika testowego Mozy Enterprise

Aby umożliwić użytkownikom usługi Azure AD zalogować się do Mozy Enterprise, muszą one być aprowied do Mozy Enterprise. W przypadku Mozy Enterprise inicjowania obsługi administracyjnej jest zadanie ręczne.

>[!NOTE]
>Do aprowizowania kont użytkowników usługi Azure AD można użyć dowolnych innych narzędzi do tworzenia kont użytkowników programu Mozy Enterprise lub interfejsów API dostarczonych przez firmę Mozy Enterprise.

**Aby aprowizować konta użytkowników, wykonaj następujące kroki:**

1. Zaloguj się do **dzierżawy Mozy Enterprise.**

2. Kliknij **pozycję Użytkownicy**, a następnie kliknij pozycję **Dodaj nowego użytkownika**.
   
    ![Użytkownicy](./media/mozy-enterprise-tutorial/ic777317.png "Użytkownicy")
   
    >[!NOTE]
    >Opcja **Dodaj nowego użytkownika** jest wyświetlana tylko wtedy, gdy **mozy** jest zaznaczone jako dostawca w obszarze **Zasady uwierzytelniania**. Jeśli uwierzytelnianie SAML jest skonfigurowany, a następnie użytkownicy są dodawane automatycznie przy pierwszym logowaniu za pomocą logowania jednokrotnego na.
    
3. W oknie dialogowym nowego użytkownika wykonaj następujące czynności:
   
    ![Dodaj użytkowników](./media/mozy-enterprise-tutorial/ic777318.png "Dodawanie użytkowników")
   
    a. Z listy **Wybierz grupę** wybierz grupę.
   
    b. Z listy **Jaki typ użytkownika** wybierz typ.
   
    d. W polach **tekstowych Nazwa użytkownika** wpisz nazwę użytkownika usługi Azure AD.
   
    d. W **polach tekstowych Poczta e-mail** wpisz adres e-mail użytkownika usługi Azure AD.
   
    e. Wybierz **pozycję Wyślij wiadomość e-mail z instrukcją obsługi**użytkownika .
   
    f. Kliknij **pozycję Dodaj użytkowników**.

     >[!NOTE]
     > Po utworzeniu użytkownika zostanie wysłana wiadomość e-mail do użytkownika usługi Azure AD zawierająca łącze potwierdzające konto, zanim stanie się aktywne.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Mozy Enterprise w Panelu dostępu należy automatycznie zalogować się do mozy enterprise, dla którego skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

