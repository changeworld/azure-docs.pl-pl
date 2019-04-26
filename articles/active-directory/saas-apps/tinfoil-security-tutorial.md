---
title: 'Samouczek: Integracja usługi Azure Active Directory przy użyciu usługi TINFOIL SECURITY | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i usługi TINFOIL SECURITY.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: da02da92-e3b0-4c09-ad6c-180882b0f9f8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 135b5719422d1b28a82ac2eda06f76d6dd746800
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60339302"
---
# <a name="tutorial-azure-active-directory-integration-with-tinfoil-security"></a>Samouczek: Integracja usługi Azure Active Directory przy użyciu usługi TINFOIL SECURITY

W tym samouczku dowiesz się, jak zintegrować usługi TINFOIL SECURITY z usługą Azure Active Directory (Azure AD).
Integrowanie usługi TINFOIL SECURITY z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do usługi TINFOIL SECURITY.
* Użytkownikom można automatycznie zalogowany do usługi TINFOIL SECURITY (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu usługi TINFOIL SECURITY, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Usługi TINFOIL SECURITY logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje usługi TINFOIL SECURITY **tożsamości** jednokrotne logowanie inicjowane przez

## <a name="adding-tinfoil-security-from-the-gallery"></a>Dodawanie usługi TINFOIL SECURITY za pomocą galerii

Aby skonfigurować integrację usługi TINFOIL SECURITY w usłudze Azure AD, należy dodać usługi TINFOIL SECURITY z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać usługi TINFOIL SECURITY z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **usługi TINFOIL SECURITY**, wybierz opcję **usługi TINFOIL SECURITY** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![Usługi TINFOIL SECURITY na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji, konfigurowania i testowania usługi Azure AD logowania jednokrotnego przy użyciu usługi TINFOIL SECURITY, w oparciu o użytkownika testu o nazwie **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w usługi TINFOIL SECURITY musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu usługi TINFOIL SECURITY, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie usługi TINFOIL SECURITY logowania jednokrotnego](#configure-tinfoil-security-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego usługi TINFOIL SECURITY](#create-tinfoil-security-test-user)**  — aby odpowiednikiem Britta Simon w usługi TINFOIL SECURITY, połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowania jednokrotnego przy użyciu usługi TINFOIL SECURITY, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **usługi TINFOIL SECURITY** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** użytkownik nie musi wykonywać żadnych kroków, ponieważ aplikacja jest już wstępnie zintegrowana z platformą Azure.

    ![Usługi TINFOIL SECURITY domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/preintegrated.png)

5. Aplikacja usługi TINFOIL SECURITY oczekuje twierdzenia SAML w określonym formacie, który wymaga dodania mapowania atrybutów niestandardowych konfiguracji atrybuty tokenu języka SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij ikonę  **Edytuj** , aby otworzyć okno dialogowe  **Atrybuty użytkownika** .

        ![image](common/edit-attribute.png)

6. Ponadto do powyższej usługi TINFOIL SECURITY aplikacja oczekuje kilka więcej atrybutów, które mają być przekazywane w odpowiedzi SAML. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** wykonaj następujące czynności, aby dodać atrybut tokenu SAML, jak pokazano w poniższej tabeli:

    | Name (Nazwa) | Atrybut źródłowy |
    | ------------------- | -------------|
    | accountid | UXXXXXXXXXXXXX |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    d. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. W **atrybut źródłowy** polu tekstowym wartość Wklej identyfikator konta, która zostanie wyświetlony później w samouczku.

    f. Kliknij przycisk **OK**.

    g. Kliknij pozycję **Zapisz**.

7. W sekcji **Certyfikat podpisywania SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Certyfikat podpisywania SAML**.

    ![Edytowanie certyfikatu podpisywania SAML](common/edit-certificate.png)

8. W sekcji **Certyfikat podpisywania SAML** skopiuj wartość **Odcisk palca** i zapisz go na komputerze.

    ![Kopiowanie wartości Odcisk palca](common/copy-thumbprint.png)

9. Na **Konfigurowanie usługi TINFOIL SECURITY** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-tinfoil-security-single-sign-on"></a>Konfigurowanie usługi TINFOIL SECURITY logowania jednokrotnego

1. W oknie przeglądarki innej witryny sieci web należy zalogować się do witryny firmy usługi TINFOIL SECURITY jako administrator.

2. Na pasku narzędzi u góry kliknij **Moje konto**.
   
    ![Pulpit nawigacyjny](./media/tinfoil-security-tutorial/ic798971.png "Pulpit nawigacyjny")

3. Kliknij pozycję **Zabezpieczenia**.
   
    ![Zabezpieczenia](./media/tinfoil-security-tutorial/ic798972.png "Zabezpieczenia")

4. Na stronie konfiguracji **Single Sign On** (Logowanie jednokrotne) wykonaj następujące kroki:
   
    ![Logowanie jednokrotne](./media/tinfoil-security-tutorial/ic798973.png "Logowanie jednokrotne")
   
    a. Wybierz pozycję **Enable SAML** (Włącz SAML).
   
    b. Kliknij przycisk **ręcznej konfiguracji**.
   
    c. W **adresu URL przesyłania SAML** pola tekstowego, Wklej wartość **adres URL logowania** skopiowanej w witrynie Azure portal
   
    d. W **odcisk palca certyfikatu SAML** pola tekstowego, Wklej wartość **odcisk palca** skopiowanej z **certyfikat podpisywania SAML** sekcji.
  
    e. Kopiuj **Twój identyfikator konta** wartość i Wklej wartość w **wartość atrybutu** polu tekstowym w obszarze **Dodawanie atrybutu** sekcji w witrynie Azure portal.
   
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
  
    b. W **nazwa_użytkownika** typ pola brittasimon@yourcompanydomain.extension. Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do usługi TINFOIL SECURITY.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **usługi TINFOIL SECURITY**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **usługi TINFOIL SECURITY**.

    ![Link usługi TINFOIL SECURITY na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-tinfoil-security-test-user"></a>Tworzenie użytkownika testowego usługi TINFOIL SECURITY

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do usługi TINFOIL SECURITY, musi być przygotowana do usługi TINFOIL SECURITY. W przypadku usługi TINFOIL SECURITY aprowizacji to zadanie ręczne.

**Aby uzyskać użytkownika zainicjowano obsługę administracyjną, wykonaj następujące czynności:**

1. Jeśli użytkownik jest część konta przedsiębiorstwa, musisz [skontaktuj się z zespołem pomocy technicznej usługi TINFOIL SECURITY](https://www.tinfoilsecurity.com/contact) można pobrać z utworzonym kontem użytkownika.

1. Jeśli użytkownik jest zwykły użytkownik usługi TINFOIL SECURITY SaaS, użytkownik może dodawać współpracownika do żadnej lokacji przez użytkownika. Spowoduje to wyzwolenie procesu o wysłanie zaproszenia na określony adres e-mail, aby utworzyć nowe konto użytkownika usługi TINFOIL SECURITY.

> [!NOTE]
> Aprowizuj konta użytkownika usługi Azure AD, można użyć innych narzędzi do tworzenia konta usługi TINFOIL SECURITY użytkownika lub interfejsów API dostarczonych przez usługi TINFOIL SECURITY.
> 

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka usługi TINFOIL SECURITY w panelu dostępu, użytkownik powinien być automatycznie zalogowany do usługi TINFOIL SECURITY, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

