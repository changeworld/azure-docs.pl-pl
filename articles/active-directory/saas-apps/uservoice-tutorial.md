---
title: 'Samouczek: Integracja usługi Azure Active Directory z programem UserVoice | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i usługi UserVoice.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 684a405b-8932-46f6-b43a-4d97a42b6b87
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: jeedes
ms.openlocfilehash: a2b8faaf3ee7d8f5dfca35f860dbd003f9e6072b
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55194342"
---
# <a name="tutorial-azure-active-directory-integration-with-uservoice"></a>Samouczek: Integracja usługi Azure Active Directory z programem UserVoice

W tym samouczku dowiesz się, jak zintegrować UserVoice w usłudze Azure Active Directory (Azure AD).

Integrowanie usługi UserVoice z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do usługi UserVoice.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do UserVoice (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą usługi UserVoice, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- UserVoice logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie usługi UserVoice z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-uservoice-from-the-gallery"></a>Dodawanie usługi UserVoice z galerii
Aby skonfigurować integrację usługi UserVoice w usłudze Azure AD, należy dodać UserVoice z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać usługi UserVoice w galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

1. W polu wyszukiwania wpisz **UserVoice**, wybierz opcję **UserVoice** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![UserVoice na liście wyników](./media/uservoice-tutorial/tutorial_uservoice_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą usługi UserVoice w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w usłudze UserVoice dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w usłudze UserVoice musi zostać ustanowione.

W usłudze UserVoice, przypisz wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą usługi UserVoice, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
1. **[Tworzenie użytkownika testowego UserVoice](#create-a-uservoice-test-user)**  — aby odpowiednikiem Britta Simon w usłudze UserVoice, połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji usługi UserVoice.

**Aby skonfigurować usługę Azure AD logowania jednokrotnego z usługi UserVoice, wykonaj następujące czynności:**

1. W witrynie Azure portal na **UserVoice** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/uservoice-tutorial/tutorial_uservoice_samlbase.png)

1. Na **UserVoice domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Domena usługi UserVoice i adresy URL pojedynczego logowania jednokrotnego informacji](./media/uservoice-tutorial/tutorial_uservoice_url.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<tenantname>.UserVoice.com`

    b. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://<tenantname>.UserVoice.com`

    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając faktycznego adresu URL i identyfikatora logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta UserVoice](https://www.uservoice.com/) do uzyskania tych wartości.

1. Na **certyfikat podpisywania SAML** sekcji, skopiuj **odcisk PALCA** wartość certyfikatu.

    ![Link do pobierania certyfikatu](./media/uservoice-tutorial/tutorial_uservoice_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/uservoice-tutorial/tutorial_general_400.png)

1. Na **konfiguracji usługi UserVoice** , kliknij przycisk **skonfigurować UserVoice** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfiguracja usługi UserVoice](./media/uservoice-tutorial/tutorial_uservoice_configure.png) 

1. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny UserVoice firmy.

1. Na pasku narzędzi u góry kliknij **ustawienia**, a następnie wybierz pozycję **portalu sieci Web** z menu.
   
    ![W sekcji Ustawienia na stronie aplikacji](./media/uservoice-tutorial/ic777519.png "ustawienia")

1. Na **portalu sieci Web** na karcie **uwierzytelnianie użytkownika** , kliknij przycisk **Edytuj** otworzyć **Edytuj uwierzytelnianie użytkownika** strony okna dialogowego.
   
    ![Portal sieci Web kartę](./media/uservoice-tutorial/ic777520.png "portalu sieci Web")

1. Na **Edytuj uwierzytelnianie użytkownika** okna dialogowego strony, wykonaj następujące czynności:
   
    ![Edytuj uwierzytelnianie użytkownika](./media/uservoice-tutorial/ic777521.png "Edytuj uwierzytelnianie użytkownika")
   
    a. Kliknij przycisk **logowanie jednokrotne (SSO)**.
 
    b. Wklej **SAML pojedynczego logowania jednokrotnego usługi adresu URL** wartości, które zostały skopiowane z witryny Azure portal do **logowania jednokrotnego zdalnego logowania** pola tekstowego.

    c. Wklej **adres URL wylogowania** wartości, które zostały skopiowane z witryny Azure portal do **wylogowania zdalnego logowania jednokrotnego w polu tekstowym**.
 
    d. Wklej **odcisk palca** wartości, które zostały skopiowane z witryny Azure portal do **bieżącego odcisk palca certyfikatu SHA1** pola tekstowego.
    
    e. Kliknij przycisk **zapisywanie ustawień uwierzytelniania**.

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/uservoice-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/uservoice-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/uservoice-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/uservoice-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-uservoice-test-user"></a>Tworzenie użytkownika testowego usługi UserVoice

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do usługi UserVoice, musi być obsługiwana w usłudze UserVoice. W przypadku usługi UserVoice Inicjowanie obsługi administracyjnej jest zadanie ręczne.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Aby aprowizować konto użytkownika, wykonaj następujące czynności:
1. Zaloguj się do Twojej **UserVoice** dzierżawy.

1. Przejdź do **ustawienia**.
   
    ![Ustawienia](./media/uservoice-tutorial/ic777811.png "Ustawienia")

1. Kliknij przycisk **ogólne**.

1. Kliknij przycisk **agentów i uprawnienia**.
   
    ![Uprawnienia i agenci](./media/uservoice-tutorial/ic777812.png "agentów i uprawnienia")

1. Kliknij przycisk **dodać administratorów**.
   
    ![Dodaj administratorów](./media/uservoice-tutorial/ic777813.png "dodać administratorów")

1. Na **zaprosić Administratorzy** okno dialogowe, należy wykonać następujące czynności:
   
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

1. Na liście aplikacji wybierz **UserVoice**.

    ![Link usługi UserVoice, na liście aplikacji](./media/uservoice-tutorial/tutorial_uservoice_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka usługi UserVoice w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikację usługi UserVoice.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md) (Wprowadzenie do panelu dostępu). 

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



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

