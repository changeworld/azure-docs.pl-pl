---
title: 'Samouczek: Integracja usługi Azure Active Directory z Silverback | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Silverback.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 32cfc96f-2137-49ff-818b-67feadcd73b7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: jeedes
ms.openlocfilehash: e7cb3049f680f81026e09388066001413922600a
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2018
ms.locfileid: "46123859"
---
# <a name="tutorial-azure-active-directory-integration-with-silverback"></a>Samouczek: Integracja usługi Azure Active Directory z Silverback

W tym samouczku dowiesz się, jak zintegrować Silverback w usłudze Azure Active Directory (Azure AD).

Integrowanie Silverback z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Silverback.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do Silverback (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Silverback, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Aktywna subskrypcja Silverback

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Silverback z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-silverback-from-the-gallery"></a>Dodawanie Silverback z galerii
Aby skonfigurować integrację Silverback w usłudze Azure AD, należy dodać Silverback z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Silverback z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Silverback**, wybierz opcję **Silverback** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Silverback na liście wyników](./media/silverback-tutorial/tutorial_silverback_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Silverback w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Silverback do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Silverback musi można ustanowić.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Silverback, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego Silverback](#create-a-silverback-test-user)**  — aby odpowiednikiem Britta Simon w Silverback połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Silverback.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Silverback, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Silverback** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/silverback-tutorial/tutorial_silverback_samlbase.png)

3. Na **Silverback domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Silverback domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/silverback-tutorial/tutorial_silverback_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<YOURSILVERBACKURL>.com/ssp`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `<YOURSILVERBACKURL>.com`

    c. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<YOURSILVERBACKURL>.com/sts/authorize/login`

    > [!NOTE] 
    > Te wartości są prawdziwe. Rzeczywisty adres URL logowania, identyfikator i adres URL odpowiedzi, należy zaktualizować te wartości. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Silverback](mailto:helpdesk@matrix42.com) do uzyskania tych wartości. 

4. Na **certyfikat podpisywania SAML** sekcji, kliknij przycisk kopiowania, aby skopiować **adres Url metadanych Federacji aplikacji** i wklej go w Notatniku.

    ![Link pobierania certyfikatu](./media/silverback-tutorial/tutorial_silverback_certificate.png) 

5. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/silverback-tutorial/tutorial_general_400.png)

6.  Zaloguj się do serwera Silverback z uprawnieniami administratora i wykonaj następujące czynności:

    a.  Przejdź do **administratora** > **dostawcy uwierzytelniania**.

    b. Na **ustawień dostawcy uwierzytelniania** strony, wykonaj następujące czynności:

    ![Administrator ](./media/silverback-tutorial/tutorial_silverback_admin.png)

    c.  Kliknij pozycję **Importuj z adresu URL**.
    
    d.  Wklej skopiowany adres URL metadanych, a następnie kliknij przycisk **OK**.
    
    e.  Potwierdzenia otrzymania pracy **OK** , a następnie wartości zostaną wypełnione automatycznie.
    
    f.  Włącz **wyświetlane na stronie logowania**.
    
    g.  Włącz **dynamiczne tworzenie użytkownika** Aby automatycznie dodać przez użytkowników usługi Azure AD autoryzacji (opcjonalnie).
    
    h.  Tworzenie **tytuł** dla przycisku na portalu samoobsługowego.

    i.  Przekaż **ikonę** , klikając **wybierz plik**.
    
    j.  Wybierz tło **kolor** dla przycisku.
    
    k.  Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/silverback-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/silverback-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/silverback-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/silverback-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-silverback-test-user"></a>Tworzenie użytkownika testowego Silverback

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do Silverback, musi być obsługiwana w Silverback. W Silverback Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby udostępnić konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do serwera Silverback jako Administrator.

2. Przejdź do **użytkowników** i **Dodaj nowego użytkownika urządzenia**.

3. Na **podstawowe** strony, wykonaj następujące czynności:

    ![Użytkownik ](./media/silverback-tutorial/tutorial_silverback_user.png)

    a. W **Username** tekstu wprowadź nazwę użytkownika, takich jak **Britta**.

    b. W **imię** tekstu Wprowadź imię użytkownika, takich jak **Britta**.

    c. W **nazwisko** tekstu wprowadź nazwisko użytkownika, takich jak **Simon**.

    d. W **adres E-mail** tekstu wprowadź adres e-mail użytkownika, takich jak **Brittasimon@contoso.com**.

    e. W **hasło** tekstu wprowadź hasło.
    
    f. W **Potwierdź hasło** polu tekstowym Wprowadź ponownie hasło i potwierdź.

    g. Kliknij pozycję **Zapisz**.

>[!NOTE]
>Jeśli nie chcesz ręcznie utworzyć każdy użytkownik należy włączyć **dynamiczne tworzenie użytkownika** pole wyboru w obszarze **administratora** > **dostawcy uwierzytelniania**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Silverback.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon Silverback, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **Silverback**.

    ![Link Silverback na liście aplikacji](./media/silverback-tutorial/tutorial_silverback_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Silverback w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Silverback.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/silverback-tutorial/tutorial_general_01.png
[2]: ./media/silverback-tutorial/tutorial_general_02.png
[3]: ./media/silverback-tutorial/tutorial_general_03.png
[4]: ./media/silverback-tutorial/tutorial_general_04.png

[100]: ./media/silverback-tutorial/tutorial_general_100.png

[200]: ./media/silverback-tutorial/tutorial_general_200.png
[201]: ./media/silverback-tutorial/tutorial_general_201.png
[202]: ./media/silverback-tutorial/tutorial_general_202.png
[203]: ./media/silverback-tutorial/tutorial_general_203.png

