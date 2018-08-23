---
title: 'Samouczek: Integracja usługi Azure Active Directory z Czat na żywo Comm100 | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Porozmawiaj na żywo Comm100.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0340d7f3-ab54-49ef-b77c-62a0efd5d49c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2018
ms.author: jeedes
ms.openlocfilehash: b85162c8392e8ecdb08a3ed04ff5b9de835808a1
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2018
ms.locfileid: "42057291"
---
# <a name="tutorial-azure-active-directory-integration-with-comm100-live-chat"></a>Samouczek: Integracja usługi Azure Active Directory rozmowę Comm100 na żywo

W tym samouczku dowiesz się, jak zintegrować Czat na żywo Comm100 w usłudze Azure Active Directory (Azure AD).

Integrowanie Czat na żywo Comm100 z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Czat na żywo Comm100.
- Użytkowników, aby automatycznie uzyskać zalogowane poczatować Comm100 na żywo (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD rozmowę Comm100 na żywo, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Czat na żywo Comm100 logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Czat na żywo Comm100 z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-comm100-live-chat-from-the-gallery"></a>Dodawanie Czat na żywo Comm100 z galerii
Aby skonfigurować integrację z Czat na żywo Comm100 w usłudze Azure AD, należy dodać Czat na żywo Comm100 z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Czat na żywo Comm100 z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Czat na żywo Comm100**, wybierz opcję **Czat na żywo Comm100** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Comm100 Live Czat na liście wyników](./media/comm100livechat-tutorial/tutorial_comm100livechat_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowania usługi Azure AD logowanie jednokrotne, w rozmowę Comm100 na żywo w oparciu o nazwie "Britta Simon" użytkownika testowego.

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Czat na żywo Comm100 do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w Czat na żywo Comm100 musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego rozmowę Comm100 na żywo, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego Czat na żywo Comm100](#create-a-comm100-live-chat-test-user)**  — aby odpowiednikiem Britta Simon w Comm100 Live Chat połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Czat na żywo Comm100.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne rozmowę Comm100 na żywo, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Czat na żywo Comm100** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/comm100livechat-tutorial/tutorial_comm100livechat_samlbase.png)

3. Na **adresy URL i domen Czat na żywo Comm100** sekcji, wykonaj następujące czynności:

    ![Adresy URL i domen Czat na żywo Comm100 pojedynczego logowania jednokrotnego informacji](./media/comm100livechat-tutorial/tutorial_comm100livechat_url.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<SUBDOMAIN>.comm100.com/AdminManage/LoginSSO.aspx?siteId=<SITEID>`
    
    > [!NOTE] 
    > Wartość adres URL logowania nie jest prawdziwe. Wartość adres URL logowania zostaną zaktualizowane o rzeczywisty logowania jednokrotnego adresu URL, który zostało wyjaśnione w dalszej części tego samouczka.

4. Czat na żywo Comm100 aplikacja oczekuje twierdzenia SAML w celu uwzględnienia określonych atrybutów. Skonfiguruj następujące atrybuty dla tej aplikacji. Możesz zarządzać wartości te atrybuty z **atrybutów użytkownika** sekcji na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład tego.

    ![Konfigurowanie logowania jednokrotnego](./media/comm100livechat-tutorial/tutorial_attribute_03.png)
    
5. W **atrybutów użytkownika** sekcji na **logowanie jednokrotne** okno dialogowe, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej i wykonaj następujące czynności:
    
    |  Nazwa atrybutu  | Wartość atrybutu |
    | --------------- | -------------------- |    
    |   e-mail    | User.mail |

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](./media/comm100livechat-tutorial/tutorial_attribute_04.png)

    ![Konfigurowanie logowania jednokrotnego](./media/comm100livechat-tutorial/tutorial_attribute_05.png)
    
    b. W **nazwa** polu tekstowym wpisz nazwę atrybutu, wyświetlanego dla tego wiersza.
    
    c. Z **wartość** wpisz wartość atrybutu wyświetlanego dla tego wiersza.

    d. Pozostaw **Namespace** puste.
    
    e. Kliknij przycisk **OK**.

6. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/comm100livechat-tutorial/tutorial_comm100livechat_certificate.png) 

7. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/comm100livechat-tutorial/tutorial_general_400.png)

8. Na **konfiguracji Czat na żywo Comm100** , kliknij przycisk **skonfigurować Comm100 Live Chat** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Comm100 Czat na żywo konfiguracji](./media/comm100livechat-tutorial/tutorial_comm100livechat_configure.png)

9. W oknie przeglądarki internetowej innej, zaloguj się do Comm100 Live Chat jako Administrator zabezpieczeń.

10. W prawej górnej części strony, kliknij polecenie **Moje konto**.

    ![Czat na żywo Comm100 myaccount](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

11. W lewej części menu, kliknij **zabezpieczeń** a następnie kliknij przycisk **agenta rejestracji jednokrotnej**.

    ![Czat na żywo Comm100 zabezpieczeń](./media/comm100livechat-tutorial/tutorial_comm100livechat_security.png)

12. Na **agenta rejestracji jednokrotnej** strony, wykonaj następujące czynności:

    ![Czat na żywo Comm100 zabezpieczeń](./media/comm100livechat-tutorial/tutorial_comm100livechat_singlesignon.png)

    a. Skopiuj pierwszą wyróżniony link i wklej ją w **adres URL logowania** polu tekstowym w **Comm100 Live Chat domena i adresy URL** sekcji w witrynie Azure portal.

    b. W **adres URL logowania jednokrotnego SAML** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL**, który skopiowano z witryny Azure portal.

    c. W **zdalny adres URL wylogowania** pola tekstowego, Wklej wartość **adres URL wylogowania**, które zostały skopiowane z witryny Azure portal.

    d. Kliknij przycisk **wybierz plik** do przekazania base-64 zakodowany certyfikat pobrany z witryny Azure portal do **certyfikatu**.

    e. Kliknij przycisk **Zapisz zmiany**

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/comm100livechat-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/comm100livechat-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/comm100livechat-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/comm100livechat-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-comm100-live-chat-test-user"></a>Tworzenie użytkownika testowego Czat na żywo Comm100

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do Czat na żywo Comm100, musi być obsługiwana na Czat na żywo Comm100. W ramach rozmowy na żywo Comm100 aprowizacji to zadanie ręczne.

**Aby udostępnić konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do Czat na żywo Comm100 jako Administrator zabezpieczeń.

2. W prawej górnej części strony, kliknij polecenie **Moje konto**.

    ![Czat na żywo Comm100 myaccount](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

3. W lewej części menu, kliknij **agentów** a następnie kliknij przycisk **nowy Agent**.

    ![Czat na żywo Comm100 agenta](./media/comm100livechat-tutorial/tutorial_comm100livechat_agent.png)

4. Na **nowy Agent** strony, wykonaj następujące czynności:

    ![Czat na żywo Comm100 nowego agenta](./media/comm100livechat-tutorial/tutorial_comm100livechat_newagent.png)

    a. a. W **E-mail** tekstu wprowadź adres e-mail użytkownika, takich jak **Brittasimon@contoso.com**.

    b. W **imię** tekstu Wprowadź imię użytkownika, takich jak **Britta**.

    c. W **nazwisko** tekstu wprowadź nazwisko użytkownika, takich jak **simon**.

    d. W **nazwy wyświetlanej** polu tekstowym wprowadź nazwę wyświetlaną użytkownika, takich jak **Britta simon**

    e. W **hasło** polu tekstowym wpisz swoje hasło.

    f. Kliknij pozycję **Zapisz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do Czat na żywo Comm100.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon Czat na żywo Comm100, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **Czat na żywo Comm100**.

    ![Czat na żywo Comm100 łącze na liście aplikacji](./media/comm100livechat-tutorial/tutorial_comm100livechat_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Czat na żywo Comm100 w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do Czat na żywo Comm100 aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/comm100livechat-tutorial/tutorial_general_01.png
[2]: ./media/comm100livechat-tutorial/tutorial_general_02.png
[3]: ./media/comm100livechat-tutorial/tutorial_general_03.png
[4]: ./media/comm100livechat-tutorial/tutorial_general_04.png

[100]: ./media/comm100livechat-tutorial/tutorial_general_100.png

[200]: ./media/comm100livechat-tutorial/tutorial_general_200.png
[201]: ./media/comm100livechat-tutorial/tutorial_general_201.png
[202]: ./media/comm100livechat-tutorial/tutorial_general_202.png
[203]: ./media/comm100livechat-tutorial/tutorial_general_203.png

