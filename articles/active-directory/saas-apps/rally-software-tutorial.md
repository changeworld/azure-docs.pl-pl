---
title: 'Samouczek: Integracja usługi Azure Active Directory z oprogramowaniem Rally | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a oprogramowaniem Rally.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ba25fade-e152-42dd-8377-a30bbc48c3ed
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: de84d03c3e0e433dbe7bc24c47b1766b32ad7bc4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67093177"
---
# <a name="tutorial-azure-active-directory-integration-with-rally-software"></a>Samouczek: Integracja usługi Azure Active Directory z oprogramowaniem Rally

W tym samouczku dowiesz się, jak zintegrować oprogramowanie Rajdu z usługą Azure Active Directory (Azure AD).
Integracja oprogramowania Rally z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD można kontrolować, kto ma dostęp do oprogramowania Rally.
* Można włączyć użytkowników do automatycznego logowania się do oprogramowania Rally Software (logowanie jednokrotne) za pomocą swoich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z oprogramowaniem Rally, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja z obsługą logowania jednokrotnego Rally Software

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Oprogramowanie Rally obsługuje zainicjowane przez **SP** SSO

## <a name="adding-rally-software-from-the-gallery"></a>Dodawanie oprogramowania Rally z galerii

Aby skonfigurować integrację oprogramowania Rally z usługą Azure AD, należy dodać oprogramowanie Rajdowe z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać oprogramowanie Rajdowe z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Oprogramowanie rajdowe**, wybierz **oprogramowanie rajdowe** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

     ![Oprogramowanie Rajdowe na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego z Rally Software na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w oprogramowaniu Rajdu.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą oprogramowania Rally, należy wykonać następujące elementy konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie jednokrotne oprogramowania Rally](#configure-rally-software-single-sign-on)** Software — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego oprogramowania Rajdowego](#create-rally-software-test-user)** — aby mieć odpowiednik Britta Simon w oprogramowaniu Rally, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą oprogramowania Rally, wykonaj następujące czynności:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Rally (Rejestracja** w aplikacji Rally) wybierz pozycję **Logowanie jednokrotne.**

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o domenie oprogramowania rajdowego i adresach URL z logami jednokrotnymi](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<tenant-name>.rally.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<tenant-name>.rally.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej rally software client,](https://help.rallydev.com/) aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Konfigurowanie oprogramowania rajdowego** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-rally-software-single-sign-on"></a>Konfigurowanie logowania jednokrotnego oprogramowania Rally

1. Zaloguj się do **dzierżawy oprogramowania Rally.**

2. Na pasku narzędzi u góry kliknij pozycję **Ustawienia**, a następnie wybierz pozycję **Subskrypcja**.
   
    ![Subskrypcja](./media/rally-software-tutorial/ic769531.png "Subskrypcja")

3. Kliknij przycisk **Akcja.** Wybierz **pozycję Edytuj subskrypcję** w prawym górnym rogu paska narzędzi.

4. Na stronie Okno dialogowe **Subskrypcja** wykonaj następujące czynności, a następnie kliknij pozycję **Zapisz & Zamknij:**
   
    ![Uwierzytelnianie](./media/rally-software-tutorial/ic769542.png "Uwierzytelnianie")
   
    a. Wybierz **pozycję Uwierzytelnianie rajdowe lub jedno i jednocześnie** z listy rozwijanej Uwierzytelnianie.

    b. W polu tekstowym **adresu URL dostawcy tożsamości** wklej wartość **identyfikatora usługi Azure AD,** który został skopiowany z witryny Azure portal. 

    d. W polu tekstowym **logowania logowania** logowania wklej wartość adresu **URL wylogowania,** który został skopiowany z witryny Azure portal.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz **pozycję Nowy użytkownik** u góry ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa** brittasimon@yourcompanydomain.extensionużytkownika wpisz . Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Britta Simon do korzystania z usługi Azure logowania jednokrotnego, przyznając dostęp do oprogramowania Rally.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz **pozycję Wszystkie aplikacje,** a następnie wybierz pozycję **Oprogramowanie rajdowe**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Oprogramowanie rajdowe**.

    ![Łącze Oprogramowanie Rajdowe na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-rally-software-test-user"></a>Utwórz użytkownika testowego oprogramowania Rajdowego

Aby użytkownicy usługi Azure AD mogli się zalogować, muszą być aprowizowani aplikacji Oprogramowania Rajdu przy użyciu ich nazw użytkowników usługi Azure Active Directory.

**Aby skonfigurować aprowizację użytkowników, wykonaj następujące kroki:**

1. Zaloguj się do dzierżawy oprogramowania Rally.

2. Przejdź do **strony Ustawienia \> użytkowników**, a następnie kliknij przycisk + Dodaj **nowy**.
   
    ![Użytkownicy](./media/rally-software-tutorial/ic781039.png "Użytkownicy")

3. Wpisz nazwę w polach tekstowych Nowy użytkownik, a następnie kliknij przycisk **Dodaj z szczegółami**.

4. W sekcji **Create User** (Tworzenie użytkownika) wykonaj następujące kroki:
   
    ![Tworzenie użytkownika](./media/rally-software-tutorial/ic781040.png "Utwórz użytkownika")

    a. W polu tekstowym **Nazwa użytkownika** wpisz nazwę użytkownika, taką jak **Brittsimon**.
   
    b. W polu tekstowym **Adres e-mail** wprowadź brittasimon@contoso.comadres e-mail użytkownika w stylu .

    d. W polu tekstowym **First Name** (Imię) wprowadź imię użytkownika, na przykład **Britta**.

    d. W polu **tekstowym Nazwisko** wprowadź nazwisko użytkownika, takiego jak **Simon**.

    e. Kliknij pozycję **Zapisz i zamknij**.

   >[!NOTE]
   >Do udostępniania kont użytkowników usługi Azure AD można użyć dowolnych innych narzędzi do tworzenia kont użytkownika oprogramowania Rally lub interfejsów API udostępnianych przez oprogramowanie Rally.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Oprogramowanie Rajdu w Panelu dostępu należy automatycznie zalogować się do oprogramowania Rajdowego, dla którego skonfigurowano logującą się do rejestru jednośmiętne. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

