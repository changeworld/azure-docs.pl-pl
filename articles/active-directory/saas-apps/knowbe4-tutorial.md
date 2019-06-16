---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługą KnowBe4 Security Awareness Training | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a usługą KnowBe4 Security Awareness Training.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: b80d2212-cc5f-4adb-836c-570640810c39
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fa4badb1183170bd7265e95c90775675eccfc34
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67098608"
---
# <a name="tutorial-azure-active-directory-integration-with-knowbe4-security-awareness-training"></a>Samouczek: Integracja usługi Azure Active Directory z usługą KnowBe4 Security Awareness Training

Z tego samouczka dowiesz się, jak zintegrować usługę KnowBe4 Security Awareness Training z usługą Azure Active Directory (Azure AD).
Zintegrowanie usługi KnowBe4 Security Awareness Training z usługą Azure AD daje następujące korzyści:

* Możliwość kontrolowania dostępu do usługi KnowBe4 Security Awareness Training za pomocą usługi Azure AD.
* Możliwość skonfigurowania automatycznego logowania użytkowników do usługi KnowBe4 Security Awareness Training (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą KnowBe4 Security Awareness Training, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Usługa KnowBe4 Security Awareness Training z subskrypcją obsługującą logowanie jednokrotne

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa KnowBe4 Security Awareness Training obsługuje logowanie jednokrotne inicjowane przez **dostawcę usługi**

* Usługa KnowBe4 Security Awareness Training obsługuje aprowizowanie użytkowników typu **just in time**

## <a name="adding-knowbe4-security-awareness-training-from-the-gallery"></a>Dodawanie usługi KnowBe4 Security Awareness Training z galerii

Aby skonfigurować integrację usługi KnowBe4 Security Awareness Training z usługą Azure AD, należy dodać usługę KnowBe4 Security Awareness Training z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać usługę KnowBe4 Security Awareness Training z galerii, wykonaj następujące kroki:**

1. W **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **KnowBe4 Security Awareness Training**, wybierz pozycję **KnowBe4 Security Awareness Training** na panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Usługa KnowBe4 Security Awareness Training na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z usługą KnowBe4 Security Awareness Training, korzystając z danych użytkownika testowego **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem usługi KnowBe4 Security Awareness Training.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi KnowBe4 Security Awareness Training, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w usłudze KnowBe4 Security Awareness Training](#configure-knowbe4-security-awareness-training-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego usługi KnowBe4 Security Awareness Training](#create-knowbe4-security-awareness-training-test-user)** — aby mieć w usłudze KnowBe4 Security Awareness Training odpowiednik użytkownika Britta Simon, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą usługi KnowBe4 Security Awareness Training, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **KnowBe4 Security Awareness Training** wybierz pozycję **Logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Domena i adresy URL logowania jednokrotnego usługi KnowBe4 Security Awareness Training](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<companyname>.KnowBe4.com/auth/saml/<instancename>`

    > [!NOTE]
    > Wartość adresu URL logowania nie jest prawdziwa. Zaktualizuj tę wartość za pomocą rzeczywistego adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta usługi KnowBe4 Security Awareness Training](mailto:support@KnowBe4.com), aby uzyskać tę wartość. Przydatne mogą być również wzorce znajdujące się w sekcji **Podstawowa konfiguracja protokołu SAML** witryny Azure Portal.

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz ciąg `KnowBe4`

    > [!NOTE]
    > Wielkość liter nie jest rozróżniana.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (nieprzetworzony)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/certificateraw.png)

6. W sekcji **Konfigurowanie usługi KnowBe4 Security Awareness Training** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-knowbe4-security-awareness-training-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi KnowBe4 Security Awareness Training

Aby skonfigurować logowanie jednokrotne po stronie usługi **KnowBe4 Security Awareness Training**, musisz wysłać pobrany **certyfikat (nieprzetworzony)** i odpowiednie adresy URL skopiowane z witryny Azure Portal [zespołowi pomocy technicznej usługi KnowBe4 Security Awareness Training](mailto:support@KnowBe4.com). Ustawiają to ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola **brittasimon\@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do usługi KnowBe4 Security Awareness Training.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, pozycję **Wszystkie aplikacje**, a następnie pozycję **KnowBe4 Security Awareness Training**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **KnowBe4 Security Awareness Training**.

    ![Link usługi KnowBe4 Security Awareness Training na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-knowbe4-security-awareness-training-test-user"></a>Tworzenie użytkownika testowego usługi KnowBe4 Security Awareness Training

Celem tej sekcji jest utworzenie użytkownika o nazwie Britta Simon w usłudze KnowBe4 Security Awareness Training. Usługa KnowBe4 Security Awareness Training obsługuje aprowizowanie typu just in time, które jest domyślnie włączone.

W tej sekcji nie musisz niczego robić. Nowy użytkownik jest tworzony podczas próby dostępu do usługi KnowBe4 Security Awareness Training, jeśli jeszcze nie istnieje.

> [!NOTE]
> Jeśli potrzebujesz utworzyć użytkownika ręcznie, musisz skontaktować się z [zespołem pomocy technicznej usługi KnowBe4 Security Awareness Training](mailto:support@KnowBe4.com).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka KnowBe4 Security Awareness Training na panelu dostępu powinno nastąpić automatyczne zalogowanie do usługi KnowBe4 Security Awareness Training, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
