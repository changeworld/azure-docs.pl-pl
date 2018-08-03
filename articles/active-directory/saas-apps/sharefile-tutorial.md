---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługa Citrix ShareFile | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i usługa Citrix ShareFile.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: e14fc310-bac4-4f09-99ef-87e5c77288b6
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2018
ms.author: jeedes
ms.openlocfilehash: 9919be128ae651b589a37f957cc59ce6d171143f
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431736"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Samouczek: Integracja usługi Azure Active Directory z usługa Citrix ShareFile

W tym samouczku dowiesz się, jak zintegrować rozwiązanie Citrix ShareFile za pomocą usługi Azure Active Directory (Azure AD).

Integrowanie usługa Citrix ShareFile z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do usługa Citrix ShareFile.
- Użytkowników, aby automatycznie uzyskać zalogowanych do usługa Citrix ShareFile (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą usługa Citrix ShareFile, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Usługa Citrix ShareFile logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodaj rozwiązanie Citrix ShareFile z galerii
1. Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

## <a name="add-citrix-sharefile-from-the-gallery"></a>Dodaj rozwiązanie Citrix ShareFile z galerii
Aby skonfigurować integrację z usługa Citrix ShareFile w usłudze Azure AD, należy dodać rozwiązanie Citrix ShareFile z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać rozwiązanie Citrix ShareFile z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

1. W polu wyszukiwania wpisz **usługa Citrix ShareFile**, wybierz opcję **usługa Citrix ShareFile** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Usługa Citrix ShareFile na liście wyników](./media/sharefile-tutorial/tutorial_sharefile_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji można skonfigurować, i test usługi Azure AD logowanie jednokrotne za pomocą usługa Citrix ShareFile oparte na użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w usługa Citrix ShareFile do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w usługa Citrix ShareFile musi nawiązać.

Usługa Citrix ShareFile przypisywanie wartości **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą usługa Citrix ShareFile, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego usługa Citrix ShareFile](#create-a-citrix-sharefile-test-user)**  — aby odpowiednikiem Britta Simon w usługa Citrix ShareFile połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji usługa Citrix ShareFile.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z usługa Citrix ShareFile, wykonaj następujące czynności:**

1. W witrynie Azure portal na **usługa Citrix ShareFile** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/sharefile-tutorial/tutorial_sharefile_samlbase.png)

1. Na **Citrix ShareFile domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Citrix ShareFile domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/sharefile-tutorial/tutorial_sharefile_url.png)
    
    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<tenant-name>.sharefile.com/saml/login`

    b. W **identyfikator jednostki** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca:

    | |
    |---|
    | `https://<tenant-name>.sharefile.com`|
    | `https://<tenant-name>.sharefile.com/saml/info`|
    | `https://<tenant-name>.sharefile1.com/saml/info`|
    | `https://<tenant-name>.sharefile1.eu/saml/info`|
    | `https://<tenant-name>.sharefile.eu/saml/info`|
    | |
    
    c. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca:
    | |
    |---|
    | `https://<tenant-name>.sharefile.com/saml/acs`|
    | `https://<tenant-name>.sharefile.eu/saml/<URL path>`|
    | `https://<tenant-name>.sharefile.com/saml/<URL path>`|
    | |

    > [!NOTE]
    > Te wartości są prawdziwe. Rzeczywisty adres URL logowania, identyfikator i adres URL odpowiedzi, należy zaktualizować te wartości. Skontaktuj się z pomocą [zespołem pomocy technicznej firmy Citrix ShareFile klienta](https://www.citrix.co.in/products/sharefile/support.html) do uzyskania tych wartości.

1. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/sharefile-tutorial/tutorial_sharefile_certificate.png)

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/sharefile-tutorial/tutorial_general_400.png)

1. Na **Citrix ShareFile konfiguracji** kliknij **skonfiguruj rozwiązanie Citrix ShareFile** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Usługa Citrix ShareFile konfiguracji](./media/sharefile-tutorial/tutorial_sharefile_configure.png)

1. W oknie przeglądarki internetowej innej, zaloguj się do Twojej **usługa Citrix ShareFile** witryny firmy jako administrator.

1. Na pasku narzędzi u góry kliknij **administratora**.

1. W okienku nawigacji po lewej stronie wybierz **skonfigurować logowanie jednokrotne**.
   
    ![Administracja konta](./media/sharefile-tutorial/ic773627.png "kont administracyjnych")

1. Na **jednokrotne logowanie / plik konfiguracji SAML 2.0** stronie okna dialogowego, w obszarze **podstawowych ustawień**, wykonaj następujące czynności:
   
    ![Logowanie jednokrotne](./media/sharefile-tutorial/ic773628.png "logowanie jednokrotne")
   
    a. Kliknij przycisk **Włącz SAML**.
    
    b. W **Your wystawca dostawcy tożsamości / identyfikator jednostki** pola tekstowego, Wklej wartość **identyfikator jednostki SAML** skopiowanej w witrynie Azure portal.

    c. Kliknij przycisk **zmiany** obok **certyfikat X.509** pola, a następnie przekaż certyfikat został pobrany z witryny Azure portal.
    
    d. W **adres URL logowania** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL** skopiowanej w witrynie Azure portal.
    
    e. W **adres URL wylogowania** pola tekstowego, Wklej wartość **adres URL wylogowania** skopiowanej w witrynie Azure portal.

1. Kliknij przycisk **Zapisz** w portalu zarządzania usługa Citrix ShareFile.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/sharefile-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/sharefile-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/sharefile-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/sharefile-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-citrix-sharefile-test-user"></a>Tworzenie użytkownika testowego usługa Citrix ShareFile

Aby umożliwić użytkownikom usługi Azure AD zalogować się do usługa Citrix ShareFile, musi być obsługiwana na rozwiązanie Citrix ShareFile. W przypadku usługa Citrix ShareFile aprowizacji to zadanie ręczne.

**Aby udostępnić konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do Twojej **usługa Citrix ShareFile** dzierżawy.

1. Kliknij przycisk **Zarządzanie użytkownikami \> Zarządzanie główną użytkowników \> + Utwórz pracowników**.
   
   ![Tworzenie pracownika](./media/sharefile-tutorial/IC781050.png "Tworzenie pracownika")

1. Na **podstawowe informacje** sekcji, wykonaj następujące czynności:
   
   ![Podstawowe informacje](./media/sharefile-tutorial/IC799951.png "podstawowe informacje")
   
   a. W **adres E-mail** polu tekstowym wpisz adres e-mail Britta Simon jako **brittasimon@contoso.com**.
   
   b. W **imię** polu tekstowym wpisz **imię** użytkownika jako **Britta**.
   
   c. W **nazwisko** polu tekstowym wpisz **nazwisko** użytkownika jako **Simon**.

1. Kliknij przycisk **Dodaj użytkownika**.
  
   >[!NOTE]
   >Właściciel konta usługi Azure AD będzie otrzymywać wiadomości e-mail i kliknij link, aby potwierdzić swoje konto, zanim stanie się aktywny. Aprowizuj konta użytkownika usługi Azure AD, można użyć innych narzędzi do tworzenia konta użytkownika usługa Citrix ShareFile lub interfejsów API dostarczonych przez rozwiązanie Citrix ShareFile.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielenie dostępu usługa Citrix ShareFile.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon usługa Citrix ShareFile, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **usługa Citrix ShareFile**.

    ![Usługa Citrix ShareFile łącze na liście aplikacji](./media/sharefile-tutorial/tutorial_sharefile_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka usługa Citrix ShareFile w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji usługa Citrix ShareFile.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/sharefile-tutorial/tutorial_general_01.png
[2]: ./media/sharefile-tutorial/tutorial_general_02.png
[3]: ./media/sharefile-tutorial/tutorial_general_03.png
[4]: ./media/sharefile-tutorial/tutorial_general_04.png

[100]: ./media/sharefile-tutorial/tutorial_general_100.png

[200]: ./media/sharefile-tutorial/tutorial_general_200.png
[201]: ./media/sharefile-tutorial/tutorial_general_201.png
[202]: ./media/sharefile-tutorial/tutorial_general_202.png
[203]: ./media/sharefile-tutorial/tutorial_general_203.png
