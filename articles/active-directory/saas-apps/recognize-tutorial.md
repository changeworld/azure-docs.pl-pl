---
title: 'Samouczek: Integracja usługi Azure Active Directory z programem Rozpoznaj | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a programem Recognize.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: cfad939e-c8f4-45a0-bd25-c4eb9701acaa
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: c0a513ad8fe772839813615d065616f2c911a031
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "68943341"
---
# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>Samouczek: Integracja usługi Azure Active Directory z rozpoznawaniem

W tym samouczku dowiesz się, jak zintegrować rozpoznawanie z usługą Azure Active Directory (Azure AD).
Integracja rozpoznawania z usługą Azure AD zapewnia następujące korzyści:

* Można kontrolować w usłudze Azure AD, który ma dostęp do rozpoznawania.
* Można włączyć użytkowników, aby automatycznie zalogować się do rozpoznawania (logowanie jednokrotne) z ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z programem Rozpoznaj, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Rozpoznawanie subskrypcji z włączoną rejestracją jednokrotną

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Rozpoznaj obsługuje syticywnie inicjowane przez **usługę SP**

## <a name="adding-recognize-from-the-gallery"></a>Dodawanie rozpoznawania z galerii

Aby skonfigurować integrację rozpoznawania z usługą Azure AD, należy dodać rozpoznać z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać rozpoznać z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz polecenie **Rozpoznaj**, wybierz pozycję **Rozpoznaj** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

     ![Rozpoznawanie na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego z rozpoznać na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w programie Recognize.

Aby skonfigurować i przetestować logowanie jednookrotne usługi Azure AD za pomocą programu Rozpoznaj, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj rozpoznawanie logowania jednokrotnego](#configure-recognize-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego rozpoznawania](#create-recognize-test-user)** — aby mieć odpowiednik Britta Simon w rozpoznać, który jest połączony z reprezentacji usługi Azure AD użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednookrotne usługi Azure AD za pomocą programu Rozpoznaj, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie **Rozpoznawanie** integracji aplikacji wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML**, jeśli masz **plik metadanych dostawcy usługi**, wykonaj następujące kroki:

    >[!NOTE]
    >**Plik metadanych dostawcy usług** otrzymasz z sekcji **Configure Recognize Single Sign-On** samouczka.

    a. Kliknij pozycję **Przekaż plik metadanych**.

    ![Przekazywanie pliku metadanych](common/upload-metadata.png)

    b. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.

    ![wybierz plik metadanych](common/browse-upload-metadata.png)

    d. Po pomyślnym przekazaniu pliku metadanych wartość **identyfikatora** zostanie automatycznie wypełniona w sekcji Podstawowa konfiguracja SAML.

    ![Rozpoznawanie informacji o logowanie do domeny i adresów URL](common/sp-identifier.png)

     W polu tekstowym **Podpisz adres URL** wpisz adres URL, używając następującego wzorca:`https://recognizeapp.com/<your-domain>/saml/sso`

    > [!Note]
    > Jeśli wartość **identyfikatora** nie zostanie automatycznie wypełniona, wartość identyfikatora zostanie odebrana przez otwarcie adresu URL metadanych dostawcy usług w sekcji Ustawienia logowania jednokrotnego, która została wyjaśniona w dalszej części sekcji **Konfigurowanie rozpoznawania logowania jednokrotnego** samouczka. Wartość adresu URL logowania nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z [zespołem pomocy technicznej rozpoznawania klienta,](mailto:support@recognizeapp.com) aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie rozpoznawania** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-recognize-single-sign-on"></a>Konfigurowanie rozpoznawania logowania jednokrotnego

1. W innym oknie przeglądarki sieci Web zaloguj się do dzierżawy rozpoznawania jako administrator.

2. W prawym górnym rogu kliknij pozycję **Menu**. Przejdź do **administratora firmy**.
   
    ![Konfigurowanie logowania jednokrotnego po stronie aplikacji](./media/recognize-tutorial/tutorial_recognize_000.png)

3. W okienku nawigacji po lewej stronie kliknij pozycję **Settings** (Ustawienia).
   
    ![Konfigurowanie logowania jednokrotnego po stronie aplikacji](./media/recognize-tutorial/tutorial_recognize_001.png)

4. Wykonaj następujące kroki w sekcji **Ustawienia logowania przyuszeń.**
   
    ![Konfigurowanie logowania jednokrotnego po stronie aplikacji](./media/recognize-tutorial/tutorial_recognize_002.png)
    
    a. Po **włączeniu funkcji SSO**wybierz opcję **ON**.

    b. W polu tekstowym **identyfikator jednostki IDP** wklej wartość **identyfikatora usługi Azure AD,** który został skopiowany z witryny Azure portal.
    
    d. W polu tekstowym **docelowego adresu URL logowania** wklej wartość **adresu URL logowania** skopiowanego z witryny Azure portal.
    
    d. W polu tekstowym **docelowego adresu URL Slo** wklej wartość **adresu URL wylogowania** skopiowanego z witryny Azure portal. 
    
    e. Otwórz pobrany plik **certyfikatu (Base64)** w notatniku, skopiuj jego zawartość do schowka, a następnie wklej go do pola tekstowego **Certyfikat.**
    
    f. Kliknij przycisk **Zapisz ustawienia.** 

5. Obok sekcji **Ustawienia logowania przyuszeń** skopiuj adres URL w obszarze **Adres URL metadanych dostawcy usług**.
   
    ![Konfigurowanie logowania jednokrotnego po stronie aplikacji](./media/recognize-tutorial/tutorial_recognize_003.png)

6. Otwórz **łącze Adresu URL metadanych** w pustej przeglądarce, aby pobrać dokument metadanych. Następnie skopiuj wartość (entityID) encji encji encji encji i wklej ją w polu tekstowym **identyfikator** w **podstawowej konfiguracji SAML** w witrynie Azure portal.
    
    ![Konfigurowanie logowania jednokrotnego po stronie aplikacji](./media/recognize-tutorial/tutorial_recognize_004.png)

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

W tej sekcji można włączyć Britta Simon do korzystania z azure logowania jednokrotnego, przyznając dostęp do rozpoznawania.

1. W portalu Azure wybierz pozycję **Aplikacje przedsiębiorstwa**, wybierz **pozycję Wszystkie aplikacje,** a następnie wybierz pozycję **Rozpoznaj**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Rozpoznaj**.

    ![Łącze Rozpoznaj na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-recognize-test-user"></a>Utwórz użytkownika testowego rozpoznawania

Aby umożliwić użytkownikom usługi Azure AD zalogować się do rozpoznawania, muszą one być aprowizować do rozpoznawania. W przypadku rozpoznawania inicjowania obsługi administracyjnej jest zadanie ręczne.

Ta aplikacja nie obsługuje obsługi administracyjnej scim, ale ma alternatywną synchronizację użytkownika, która aprowizuje użytkowników. 

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy Rozpoznaj jako administrator.

2. W prawym górnym rogu kliknij pozycję **Menu**. Przejdź do **administratora firmy**.

3. W okienku nawigacji po lewej stronie kliknij pozycję **Settings** (Ustawienia).

4. Wykonaj następujące kroki w sekcji **Synchronizacja użytkownika.**
   
    ![Nowy użytkownik](./media/recognize-tutorial/tutorial_recognize_005.png "Nowy użytkownik")
   
    a. Jako **włączone synchronizacji**wybierz **wł.**
   
    b. Jako **Wybierz dostawcę synchronizacji**, wybierz **Microsoft / Office 365**.
   
    d. Kliknij **pozycję Uruchom synchronizację z użytkownikiem**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Rozpoznaj w Panelu dostępu należy automatycznie zalogować się do programu Rozpoznaj, dla którego skonfigurowano logującą się jednoślik. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

