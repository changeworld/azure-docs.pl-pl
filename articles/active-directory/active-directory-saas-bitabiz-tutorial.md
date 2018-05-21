---
title: 'Samouczek: Integracji Azure Active Directory z BitaBIZ | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i BitaBIZ.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 1a51e677-c62b-4aee-9c61-56926aaaa899
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: jeedes
ms.openlocfilehash: 031d7b11aea57b8bdd8b17e474db0c81b1bdbe76
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-bitabiz"></a>Samouczek: Integracji Azure Active Directory z BitaBIZ

Z tego samouczka dowiesz się integrowanie BitaBIZ z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD BitaBIZ zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do BitaBIZ.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do BitaBIZ (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z BitaBIZ, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- BitaBIZ logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie BitaBIZ z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-bitabiz-from-the-gallery"></a>Dodawanie BitaBIZ z galerii
Aby skonfigurować integrację usługi Azure AD BitaBIZ, należy dodać BitaBIZ z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać BitaBIZ z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **BitaBIZ**, wybierz pozycję **BitaBIZ** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![BitaBIZ na liście wyników](./media/active-directory-saas-bitabiz-tutorial/tutorial_bitabiz_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z BitaBIZ w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w BitaBIZ jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w BitaBIZ musi się.

W BitaBIZ, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z BitaBIZ, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego BitaBIZ](#create-a-bitabiz-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta BitaBIZ połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji BitaBIZ.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z BitaBIZ, wykonaj następujące czynności:**

1. W portalu Azure na **BitaBIZ** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/active-directory-saas-bitabiz-tutorial/tutorial_bitabiz_samlbase.png)

3. Na **BitaBIZ domeny i adres URL** sekcji, wykonaj następujące kroki, aby skonfigurować aplikację w trybie IDP inicjowane:

    ![Adresy URL i domeny BitaBIZ pojedynczy informacje logowania jednokrotnego](./media/active-directory-saas-bitabiz-tutorial/tutorial_bitabiz_url.png)

    W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://www.bitabiz.com/<instanceId>`

    > [!NOTE] 
    > Wartość w powyższy adres URL dotyczy tylko pokaz. Zaktualizuj tę wartość z rzeczywisty identyfikator, który znajduje się w dalszej części tego samouczka.

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** inicjowane tryb:

    ![Adresy URL i domeny BitaBIZ pojedynczy informacje logowania jednokrotnego](./media/active-directory-saas-bitabiz-tutorial/tutorial_bitabiz_url1.png)

    W **adres URL logowania** tekstowym, wpisz adres URL: `https://www.bitabiz.com/dashboard`

5. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/active-directory-saas-bitabiz-tutorial/tutorial_bitabiz_certificate.png) 

6. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-bitabiz-tutorial/tutorial_general_400.png)
    
7. Na **konfiguracji BitaBIZ** , kliknij przycisk **skonfigurować BitaBIZ** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfiguracja BitaBIZ](./media/active-directory-saas-bitabiz-tutorial/tutorial_bitabiz_configure.png) 

8. W oknie przeglądarki innej witryny sieci web logowanie do dzierżawy BitaBIZ jako administrator.

9. Polecenie **ADMIN Instalatora**.

    ![Konfiguracja BitaBIZ](./media/active-directory-saas-bitabiz-tutorial/settings1.png)

10. Polecenie **integracji Microsoft** w obszarze **Dodaj wartość** sekcji.

    ![Konfiguracja BitaBIZ](./media/active-directory-saas-bitabiz-tutorial/settings2.png)

11. Przewiń w dół do sekcji **usługi Microsoft Azure AD (Włącz logowanie jednokrotne)** i wykonać następujące kroki:

    ![Konfiguracja BitaBIZ](./media/active-directory-saas-bitabiz-tutorial/settings3.png)

    a. Skopiuj wartości z **identyfikator jednostki ("Identifier" w usłudze Azure AD)** pole tekstowe i wklej ją do **identyfikator** textbox w **BitaBIZ domeny i adres URL** sekcji w portalu Azure. 
    
    b. W **Azure AD pojedynczy znak na adres URL usługi** pole tekstowe, Wklej **SAML pojedynczy znak na adres URL usługi**, które zostały skopiowane z portalu Azure.
    
    c. W **identyfikator jednostki usługi Azure AD SAML** pole tekstowe, Wklej **identyfikator jednostki SAML**, które zostały skopiowane z portalu Azure.

    d. Otwórz z pobranego **Certificate(Base64)** plików w programie Notatnik, skopiuj zawartość go do Schowka, a następnie wklej go do **Azure AD certyfikatu podpisywania (kodowanie Base64)** pola tekstowego.

    e. Dodawanie domeny poczty e-mail biznesowej oznacza to, że nazwa mycompany.com w **nazwy domeny** pole tekstowe można przypisać logowania jednokrotnego dla użytkowników w firmie z tej domeny poczty e-mail (nie jest to konieczne).
    
    f. Oznacz **logowanie Jednokrotne włączone** BitaBIZ konta.
    
    g. Kliknij przycisk **Zapisz konfigurację usługi Azure AD** Aby zapisać i aktywować konfiguracji logowania jednokrotnego.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/active-directory-saas-bitabiz-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/active-directory-saas-bitabiz-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/active-directory-saas-bitabiz-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/active-directory-saas-bitabiz-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-bitabiz-test-user"></a>Tworzenie użytkownika testowego BitaBIZ

Aby umożliwić użytkownikom usługi Azure AD zalogować się do BitaBIZ, musi być przygotowana do BitaBIZ.  
W przypadku BitaBIZ Inicjowanie obsługi to zadanie ręczne.

**Aby udostępnić konta użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy BitaBIZ jako administrator.

2. Polecenie **ADMIN Instalatora**.

    ![BitaBIZ Dodaj użytkownika](./media/active-directory-saas-bitabiz-tutorial/settings1.png)

3. Polecenie **dodawania użytkowników** w obszarze **organizacji** sekcji.

    ![BitaBIZ Dodaj użytkownika](./media/active-directory-saas-bitabiz-tutorial/user1.png)

4. Kliknij przycisk **Dodaj nowych pracowników**.

    ![BitaBIZ Dodaj użytkownika](./media/active-directory-saas-bitabiz-tutorial/user2.png)

5. Na **"Dodaj nowy pracownik"** okna dialogowego strony, należy wykonać następujące czynności:

    ![BitaBIZ Dodaj użytkownika](./media/active-directory-saas-bitabiz-tutorial/user3.png)

    a. W **imię** pole tekstowe, nazwę użytkownika, takich jak Britta typu pierwszy.

    b. W **nazwisko** tekstowym, wpisz nazwisko użytkownika, takich jak Simona.

    c. W **E-mail** tekstowym, wpisz adres e-mail użytkownika, takich jak Brittasimon@contoso.com.

    d. Wybierz datę w **Data zatrudnienia**.

    e. Istnieją inne atrybuty nieobowiązkowych użytkownika, które można skonfigurować dla użytkownika. Zobacz [Doc Instalatora pracownika](https://help.bitabiz.dk/manage-or-set-up-your-account/on-boarding-employees/new-employee) więcej szczegółów.    
    
    f. Kliknij przycisk **pracownika Zapisz**.
    
    > [!NOTE]
    > Właściciel konta usługi Azure Active Directory otrzymuje wiadomość e-mail i następuje łącze, aby potwierdzić swoje konto, zanim staje się aktywny.
    
### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu BitaBIZ.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta BitaBIZ, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **BitaBIZ**.

    ![Łącze BitaBIZ na liście aplikacji](./media/active-directory-saas-bitabiz-tutorial/tutorial_bitabiz_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka BitaBIZ w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji BitaBIZ.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-bitabiz-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bitabiz-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bitabiz-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bitabiz-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bitabiz-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bitabiz-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bitabiz-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bitabiz-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bitabiz-tutorial/tutorial_general_203.png

