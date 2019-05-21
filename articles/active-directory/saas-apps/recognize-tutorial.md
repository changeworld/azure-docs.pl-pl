---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą rozpoznawaj | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Rozpoznaj.
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
ms.openlocfilehash: 94e0b365d159ef18d7c0e6216ac9f5babb0d6231
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65890479"
---
# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Rozpoznaj

W tym samouczku dowiesz się, jak zintegrować rozpoznawanie przy użyciu usługi Azure Active Directory (Azure AD).
Integrowanie rozpoznawaj z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do uznania.
* Aby umożliwić użytkownikom można automatycznie zalogowany rozpoznawaj (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą rozpoznawaj, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Rozpoznaje logowanie jednokrotne włączone subskrypcja pojedyncza

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Rozpoznaje obsługuje **SP** jednokrotne logowanie inicjowane przez

## <a name="adding-recognize-from-the-gallery"></a>Dodawanie rozpoznawaj z galerii

Aby skonfigurować integrację Rozpoznaj w usłudze Azure AD, należy dodać rozpoznawanie z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać rozpoznawanie z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **rozpoznawaj**, wybierz opcję **rozpoznawaj** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![Rozpoznaje na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji, konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą Rozpoznaj w oparciu o użytkownika testu o nazwie **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w rozpoznawaj musi zostać nawiązane.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą rozpoznawaj, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Skonfiguruj rozpoznawanie logowania jednokrotnego](#configure-recognize-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego rozpoznawaj](#create-recognize-test-user)**  — aby odpowiednikiem Britta Simon w rozpoznawaj połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z rozpoznawaj, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **rozpoznawaj** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Na **podstawową konfigurację protokołu SAML** sekcji, jeśli masz **plik metadanych usługodawcy**, wykonaj następujące czynności:

    >[!NOTE]
    >Zostanie wyświetlony **plik metadanych usługodawcy** z **skonfiguruj rozpoznawanie logowania jednokrotnego** części samouczka.

    a. Kliknij pozycję **Przekaż plik metadanych**.

    ![Przekazywanie pliku metadanych](common/upload-metadata.png)

    b. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.

    ![wybierz plik metadanych](common/browse-upload-metadata.png)

    c. Po pomyślnym przekazaniu pliku metadanych **identyfikator** wartość Uzyskaj automatycznie wypełnione w sekcji podstawową konfigurację protokołu SAML.

    ![Rozpoznawanie domena i adresy URL pojedynczego logowania jednokrotnego](common/sp-identifier.png)

     W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://recognizeapp.com/<your-domain>/saml/sso`

    > [!Note]
    > Jeśli **identyfikator** wartości nie są automatycznie wypełniane, otrzymasz wartość identyfikatora, otwierając adres URL metadanych dostawcy usług w sekcji ustawień logowania jednokrotnego, która została wyjaśniona później w **skonfiguruj rozpoznawanie pojedynczego Logowanie jednokrotne** części samouczka. Wartość adresu URL logowania nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta rozpoznaje](mailto:support@recognizeapp.com) można uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/certificatebase64.png)

6. Na **skonfiguruj rozpoznawanie** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-recognize-single-sign-on"></a>Skonfiguruj rozpoznawanie logowania jednokrotnego

1. W oknie przeglądarki internetowej innej Zaloguj się do dzierżawy rozpoznawaj jako administrator.

2. W prawym górnym rogu kliknij **Menu**. Przejdź do **firmy administratora**.
   
    ![Konfigurowanie logowania jednokrotnego po stronie aplikacji](./media/recognize-tutorial/tutorial_recognize_000.png)

3. W okienku nawigacji po lewej stronie kliknij pozycję **Settings** (Ustawienia).
   
    ![Konfigurowanie logowania jednokrotnego po stronie aplikacji](./media/recognize-tutorial/tutorial_recognize_001.png)

4. Wykonaj następujące czynności na **ustawień logowania jednokrotnego** sekcji.
   
    ![Konfigurowanie logowania jednokrotnego po stronie aplikacji](./media/recognize-tutorial/tutorial_recognize_002.png)
    
    a. Jako **włączyć logowanie Jednokrotne**, wybierz opcję **ON**.

    b. W **identyfikator jednostki tożsamości** pola tekstowego, Wklej wartość **usługi Azure AD identyfikator** skopiowanej w witrynie Azure portal.
    
    c. W **logowania jednokrotnego, docelowy adres url** pola tekstowego, Wklej wartość **adres URL logowania** skopiowanej w witrynie Azure portal.
    
    d. W **Slo docelowy adres url** pola tekstowego, Wklej wartość **adres URL wylogowania** skopiowanej w witrynie Azure portal. 
    
    e. Otwórz swoje pobrany **certyfikat (Base64)** w Notatniku plik, skopiuj jego zawartość do Schowka, a następnie wklej go do **certyfikatu** pola tekstowego.
    
    f. Kliknij przycisk **Zapisz ustawienia** przycisku. 

5. Obok pozycji **ustawień logowania jednokrotnego** sekcji, skopiuj adres URL, pod **adres url usługi dostawcy metadanych**.
   
    ![Konfigurowanie logowania jednokrotnego po stronie aplikacji](./media/recognize-tutorial/tutorial_recognize_003.png)

6. Otwórz **link z adresem URL metadanych** w obszarze puste przeglądarki, aby pobrać dokumentu metadanych. Następnie skopiuj EntityDescriptor value(entityID) z pliku i wklej go w **identyfikator** polu tekstowym w **podstawową konfigurację protokołu SAML** w witrynie Azure portal.
    
    ![Konfigurowanie logowania jednokrotnego po stronie aplikacji](./media/recognize-tutorial/tutorial_recognize_004.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola brittasimon@yourcompanydomain.extension. Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do uznania.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **rozpoznawaj**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **rozpoznawaj**.

    ![Rozpoznaj łącze na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-recognize-test-user"></a>Tworzenie użytkownika testowego Rozpoznaj

Aby umożliwić użytkownikom usługi Azure AD zalogować się do uznania, musi być obsługiwana do uznania. W przypadku uznania Inicjowanie obsługi administracyjnej jest zadanie ręczne.

Ta aplikacja nie obsługuje standard SCIM inicjowania obsługi administracyjnej, ale ma synchronizacji alternatywne użytkownika, która aprowizuje użytkowników. 

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do witryny firmy rozpoznawaj jako administrator.

2. W prawym górnym rogu kliknij **Menu**. Przejdź do **firmy administratora**.

3. W okienku nawigacji po lewej stronie kliknij pozycję **Settings** (Ustawienia).

4. Wykonaj następujące czynności na **synchronizacja użytkownika** sekcji.
   
    ![Nowy użytkownik](./media/recognize-tutorial/tutorial_recognize_005.png "Nowy użytkownik")
   
    a. Jako **z włączoną funkcją synchronizacji**, wybierz opcję **ON**.
   
    b. Jako **dostawcy synchronizacji wybierz**, wybierz opcję **Microsoft / usługi Office 365**.
   
    c. Kliknij przycisk **Uruchom synchronizację użytkowników**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Rozpoznaj w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze rozpoznawaj, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

