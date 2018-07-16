---
title: 'Samouczek: Integracja usługi Azure Active Directory z programem UserVoice | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i usługi UserVoice.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 684a405b-8932-46f6-b43a-4d97a42b6b87
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: jeedes
ms.openlocfilehash: 8ead383ef0967fcaf67f3157f0a51104126ad618
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39045414"
---
# <a name="tutorial-azure-active-directory-integration-with-uservoice"></a>Samouczek: Integracja usługi Azure Active Directory z programem UserVoice

W tym samouczku dowiesz się, jak zintegrować UserVoice w usłudze Azure Active Directory (Azure AD).

Integrowanie usługi UserVoice z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do usługi UserVoice.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do UserVoice (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą usługi UserVoice, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- UserVoice logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie usługi UserVoice z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-uservoice-from-the-gallery"></a>Dodawanie usługi UserVoice z galerii
Aby skonfigurować integrację usługi UserVoice w usłudze Azure AD, należy dodać UserVoice z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać usługi UserVoice w galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **UserVoice**, wybierz opcję **UserVoice** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![UserVoice na liście wyników](./media/uservoice-tutorial/tutorial_uservoice_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą usługi UserVoice w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w usłudze UserVoice dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w usłudze UserVoice musi zostać ustanowione.

W usłudze UserVoice, przypisz wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą usługi UserVoice, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego UserVoice](#create-a-uservoice-test-user)**  — aby odpowiednikiem Britta Simon w usłudze UserVoice, połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji usługi UserVoice.

**Aby skonfigurować usługę Azure AD logowania jednokrotnego z usługi UserVoice, wykonaj następujące czynności:**

1. W witrynie Azure portal na **UserVoice** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/uservoice-tutorial/tutorial_uservoice_samlbase.png)

3. Na **UserVoice domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Domena usługi UserVoice i adresy URL pojedynczego logowania jednokrotnego informacji](./media/uservoice-tutorial/tutorial_uservoice_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<tenantname>.UserVoice.com`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<tenantname>.UserVoice.com`

    > [!NOTE] 
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania jednokrotnego i identyfikator. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta UserVoice](https://www.uservoice.com/) do uzyskania tych wartości.

4. Na **certyfikat podpisywania SAML** sekcji, skopiuj **odcisk PALCA** wartość certyfikatu.

    ![Link pobierania certyfikatu](./media/uservoice-tutorial/tutorial_uservoice_certificate.png) 

5. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/uservoice-tutorial/tutorial_general_400.png)

6. Na **konfiguracji usługi UserVoice** , kliknij przycisk **skonfigurować UserVoice** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfiguracja usługi UserVoice](./media/uservoice-tutorial/tutorial_uservoice_configure.png) 

7. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny UserVoice firmy.

8. Na pasku narzędzi u góry kliknij **ustawienia**, a następnie wybierz pozycję **portalu sieci Web** z menu.
   
    ![W sekcji Ustawienia na stronie aplikacji](./media/uservoice-tutorial/ic777519.png "ustawienia")

9. Na **portalu sieci Web** na karcie **uwierzytelnianie użytkownika** , kliknij przycisk **Edytuj** otworzyć **Edytuj uwierzytelnianie użytkownika** strony okna dialogowego.
   
    ![Portal sieci Web kartę](./media/uservoice-tutorial/ic777520.png "portalu sieci Web")

10. Na **Edytuj uwierzytelnianie użytkownika** okna dialogowego strony, wykonaj następujące czynności:
   
    ![Edytuj uwierzytelnianie użytkownika](./media/uservoice-tutorial/ic777521.png "Edytuj uwierzytelnianie użytkownika")
   
    a. Kliknij przycisk **logowanie jednokrotne (SSO)**.
 
    b. Wklej **SAML pojedynczego logowania jednokrotnego usługi adresu URL** wartości, które zostały skopiowane z witryny Azure portal do **logowania jednokrotnego zdalnego logowania** pola tekstowego.

    c. Wklej **adres URL wylogowania** wartości, które zostały skopiowane z witryny Azure portal do **wylogowania zdalnego logowania jednokrotnego w polu tekstowym**.
 
    d. Wklej **odcisk palca** wartości, które zostały skopiowane z witryny Azure portal do **bieżącego odcisk palca certyfikatu SHA1** pola tekstowego.
    
    e. Kliknij przycisk **zapisywanie ustawień uwierzytelniania**.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/uservoice-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/uservoice-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/uservoice-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/uservoice-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-uservoice-test-user"></a>Tworzenie użytkownika testowego usługi UserVoice

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do usługi UserVoice, musi być obsługiwana w usłudze UserVoice. W przypadku usługi UserVoice Inicjowanie obsługi administracyjnej jest zadanie ręczne.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Aby udostępnić konto użytkownika, wykonaj następujące czynności:
1. Zaloguj się do Twojej **UserVoice** dzierżawy.

2. Przejdź do **ustawienia**.
   
    ![Ustawienia](./media/uservoice-tutorial/ic777811.png "ustawienia")

3. Kliknij przycisk **ogólne**.

4. Kliknij przycisk **agentów i uprawnienia**.
   
    ![Uprawnienia i agenci](./media/uservoice-tutorial/ic777812.png "agentów i uprawnienia")

5. Kliknij przycisk **dodać administratorów**.
   
    ![Dodaj administratorów](./media/uservoice-tutorial/ic777813.png "dodać administratorów")

6. Na **zaprosić Administratorzy** okno dialogowe, należy wykonać następujące czynności:
   
    ![Zaproś Administratorzy](./media/uservoice-tutorial/ic777814.png "zaproszenie administratorów")
   
    a. W polu tekstowym wiadomości E-mail, wpisz adres e-mail konta, do aprowizowania, a następnie kliknij przycisk **Dodaj**.
   
    b. Kliknij przycisk **zaprosić**.

> [!NOTE]
> Można użyć jakichkolwiek innych UserVoice użytkownika konta tworzenie narzędzi lub interfejsów API udostępniane przez usługę UserVoice aprowizacji kont użytkowników usługi AAD.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do usługi UserVoice.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon UserVoice, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **UserVoice**.

    ![Link usługi UserVoice, na liście aplikacji](./media/uservoice-tutorial/tutorial_uservoice_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka usługi UserVoice w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikację usługi UserVoice.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/uservoice-tutorial/tutorial_general_01.png
[2]: ./media/uservoice-tutorial/tutorial_general_02.png
[3]: ./media/uservoice-tutorial/tutorial_general_03.png
[4]: ./media/uservoice-tutorial/tutorial_general_04.png

[100]: ./media/uservoice-tutorial/tutorial_general_100.png

[200]: ./media/uservoice-tutorial/tutorial_general_200.png
[201]: ./media/uservoice-tutorial/tutorial_general_201.png
[202]: ./media/uservoice-tutorial/tutorial_general_202.png
[203]: ./media/uservoice-tutorial/tutorial_general_203.png

