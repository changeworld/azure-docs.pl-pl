---
title: 'Samouczek: Integracja usługi Azure Active Directory z Velpic SAML | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Velpic SAML.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc8431e2c45cc3bfdfa08dd0078220ab2d290309
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2019
ms.locfileid: "59565720"
---
# <a name="tutorial-azure-active-directory-integration-with-velpic-saml"></a>Samouczek: Integracja usługi Azure Active Directory z Velpic SAML

W tym samouczku dowiesz się, jak zintegrować Velpic SAML w usłudze Azure Active Directory (Azure AD).
Integrowanie Velpic SAML z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Velpic SAML.
* Użytkownikom można automatycznie zalogowany do Velpic SAML (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Velpic SAML, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/)
* Velpic SAML logowania jednokrotnego włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje Velpic SAML **SP** jednokrotne logowanie inicjowane przez

## <a name="adding-velpic-saml-from-the-gallery"></a>Dodawanie Velpic SAML z galerii

Aby skonfigurować integrację Velpic protokołu SAML w usłudze Azure AD, należy dodać Velpic SAML z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Velpic SAML z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Velpic SAML**, wybierz opcję **Velpic SAML** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![SAML Velpic na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i test usługi Azure AD logowanie jednokrotne za pomocą Velpic SAML w oparciu o nazwie użytkownika testowego **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Velpic SAML musi zostać nawiązane.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z Velpic SAML, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie Velpic SAML logowania jednokrotnego](#configure-velpic-saml-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego Velpic SAML](#create-velpic-saml-test-user)**  — aby odpowiednikiem Britta Simon w Velpic SAML, połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z Velpic SAML, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **Velpic SAML** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Velpic SAML domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<sub-domain>.velpicsaml.net`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://auth.velpic.com/saml/v2/<entity-id>/login`

    > [!NOTE]
    > Należy pamiętać, że adres URL logowania będzie świadczona przez zespół Velpic SAML i wartość identyfikatora będzie dostępny podczas konfigurowania wtyczkę logowania jednokrotnego Velpic SAML stronie. Musisz skopiować tę wartość ze strony aplikacji Velpic SAML i wklej go tutaj.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. Na **skonfigurować Velpic SAML** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-velpic-saml-single-sign-on"></a>Konfigurowanie Velpic SAML logowania jednokrotnego

1. W oknie przeglądarki innej witryny sieci web należy zalogować się do witryny firmy Velpic SAML jako administrator.

2. Kliknij pozycję **Zarządzaj** kartę, a następnie przejdź do **integracji** sekcji, w których trzeba kliknąć **wtyczek** przycisk, aby utworzyć nowy dodatek do logowania.

    ![Wtyczka](./media/velpicsaml-tutorial/velpic_1.png)

3. Kliknij pozycję **"Dodawanie wtyczki"** przycisku.
    
    ![Wtyczka](./media/velpicsaml-tutorial/velpic_2.png)

4. Kliknij pozycję **SAML** kafelka na stronie Dodaj wtyczkę.
    
    ![Wtyczka](./media/velpicsaml-tutorial/velpic_3.png)

5. Wprowadź nazwę nowej wtyczki SAML, a następnie kliknij przycisk **"Dodaj"** przycisku.

    ![Wtyczka](./media/velpicsaml-tutorial/velpic_4.png)

6. Wprowadź szczegółowe informacje w następujący sposób:

    ![Wtyczka](./media/velpicsaml-tutorial/velpic_5.png)

    a. W **nazwa** polu tekstowym wpisz nazwę wtyczki SAML.

    b. W **adres URL wystawcy** pola tekstowego, Wklej **usługi Azure AD identyfikator** skopiowane z **Konfigurowanie logowania jednokrotnego** okna witryny Azure portal.

    c. W **dostawcy metadanych konfiguracji** przekazywanie pliku XML metadanych, który został pobrany z witryny Azure portal.

    d. Możesz również włączyć SAML dokładnie na czas inicjowania obsługi administracyjnej, należy włączyć **"Automatyczne tworzenie nowych użytkowników"** pola wyboru. Jeśli użytkownik nie istnieje w Velpic ta flaga nie jest włączona, logowanie z platformy Azure nie powiedzie się. Po włączeniu flagi użytkownika będą automatycznie udostępniane w Velpic podczas logowania. 

    e. Kopiuj **pojedynczy adres URL logowania** z tekstu i wklej go w witrynie Azure portal.
    
    f. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola `brittasimon@yourcompanydomain.extension`. Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do Velpic SAML.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **Velpic SAML**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Velpic SAML**.

    ![Link Velpic SAML na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-velpic-saml-test-user"></a>Tworzenie użytkownika testowego Velpic SAML

Ten krok zazwyczaj nie jest wymagane, ponieważ aplikacja obsługuje dokładnie na czas Inicjowanie obsługi użytkowników. Jeśli nie włączono automatycznej aprowizacji użytkowników Tworzenie ręcznego użytkownika można zrobić zgodnie z poniższym opisem.

Zaloguj się do witryny firmy Velpic SAML jako administrator i wykonać następujące kroki:
    
1. Kliknij kartę Zarządzaj i przejdź do sekcji użytkowników, a następnie kliknij przycisk Nowa, aby dodać użytkowników.

    ![Dodaj użytkownika](./media/velpicsaml-tutorial/velpic_7.png)

2. Na **"Tworzenie nowego użytkownika"** okna dialogowego strony, wykonaj następujące kroki.

    ![Użytkownik](./media/velpicsaml-tutorial/velpic_8.png)
    
    a. W **imię** polu tekstowym Nazwa typu pierwszy Britta.

    b. W **nazwisko** polu tekstowym wpisz nazwisko Simon.

    c. W **nazwa_użytkownika** polu tekstowym wpisz nazwę żądanego użytkownika w pozycji Britta simon.

    d. W **E-mail** polu tekstowym wpisz adres e-mail Brittasimon@contoso.com konta.

    e. Pozostała część informacji jest opcjonalne, można go wypełnić, jeśli to konieczne.
    
    f. Kliknij przycisk **SAVE** (Zapisz).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

1. Po kliknięciu kafelka Velpic SAML, w panelu dostępu, należy uzyskać strony logowania Velpic SAML aplikacji. Powinien zostać wyświetlony **"Zaloguj się za pomocą usługi Azure AD"** przycisk na stronie logowania.

    ![Wtyczka](./media/velpicsaml-tutorial/velpic_6.png)

1. Kliknij pozycję **"Zaloguj się za pomocą usługi Azure AD"** przycisk, aby zalogować się do Velpic przy użyciu swojego konta usługi Azure AD.

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

