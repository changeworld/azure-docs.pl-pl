---
title: 'Samouczek: Integracji Azure Active Directory z Boomi | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Boomi.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 40d034ff-7394-4713-923d-1f8f2ed8bf36
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: jeedes
ms.openlocfilehash: bd0b5cd66718ca0eeb9fa90366b90a899181e912
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34343865"
---
# <a name="tutorial-azure-active-directory-integration-with-boomi"></a>Samouczek: Integracji Azure Active Directory z Boomi

Z tego samouczka dowiesz się integrowanie Boomi z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Boomi zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Boomi.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Boomi (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Boomi, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Boomi logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Boomi z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-boomi-from-the-gallery"></a>Dodawanie Boomi z galerii
Aby skonfigurować integrację usługi Azure AD Boomi, należy dodać Boomi z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Boomi z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Boomi**, wybierz pozycję **Boomi** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Boomi na liście wyników](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Boomi w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Boomi jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Boomi musi się.

W Boomi, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Boomi, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Boomi](#create-a-boomi-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Boomi połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Boomi.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Boomi, wykonaj następujące czynności:**

1. W portalu Azure na **Boomi** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_samlbase.png)

3. Na **Boomi domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Adresy URL i domeny Boomi pojedynczy informacje logowania jednokrotnego](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_url.png)

    a. W **identyfikator** tekstowym, wpisz adres URL: `https://platform.boomi.com/`

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://platform.boomi.com/sso/<boomi-tenant>/saml`

    > [!NOTE] 
    > Wartość adresu URL odpowiedzi nie jest prawdziwe. Zaktualizuj tę wartość do rzeczywistego adresu URL odpowiedzi. Skontaktuj się z [zespołem pomocy technicznej Boomi](https://boomi.com/company/contact/) można uzyskać wartość.
 
4. Aplikacja Boomi oczekuje potwierdzenia języka SAML w określonym formacie. Skonfiguruj następujące oświadczeń dla tej aplikacji. Możesz zarządzać wartości tych atrybutów z "**atrybuty użytkownika**" sekcji na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład tego.
    
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-boomi-tutorial/tutorial_attribute.png)

5. W **atrybuty użytkownika** sekcji na **logowanie jednokrotne** okna dialogowego, dla każdego wiersza w tabeli poniżej, wykonaj następujące czynności:

    | Nazwa atrybutu | Wartość atrybutu |
    | -------------- | --------------- |
    | FEDERATION_ID | User.mail |
    
    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.
    
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-boomi-tutorial/tutorial_officespace_04.png)
    
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-boomi-tutorial/tutorial_attribute_05.png)
    
    b. W **nazwa** tekstowym, wpisz nazwę atrybut wyświetlany dla danego wiersza.
    
    c. Z **wartość** listy, wpisz wartość atrybutu wyświetlany dla danego wiersza.
    
    d. Kliknij przycisk **OK**.

6. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_certificate.png) 

7. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-boomi-tutorial/tutorial_general_400.png)

8. Na **konfiguracji Boomi** , kliknij przycisk **skonfigurować Boomi** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfiguracja Boomi](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_configure.png) 

9. W oknie przeglądarki innej witryny sieci web Zaloguj się do witryny firmy Boomi jako administrator. 

10. Przejdź do **nazwa firmy** i przejdź do **Konfigurowanie**.

11. Kliknij przycisk **opcje logowania jednokrotnego** karcie i wykonaj następujące czynności.

    ![Konfigurowanie rejestracji jednokrotnej po stronie aplikacji](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_11.png)

    a. Sprawdź **Włącz SAML logowania jednokrotnego** wyboru.

    b. Kliknij przycisk **importu** można przekazać certyfikatu pobrane z usługi Azure AD do **certyfikat dostawcy tożsamości**.
    
    c. W **adresu URL logowania do dostawcy tożsamości** pole tekstowe, umieścić wartość elementu **SAML pojedynczy znak na adres URL usługi** z okna konfiguracji aplikacji usługi Azure AD.

    d. Jako **federacyjnego identyfikator lokalizacji**, wybierz pozycję **identyfikator federacyjnej jest w elemencie atrybutu FEDERATION_ID** przycisk radiowy. 

    e. Kliknij przycisk **zapisać** przycisku.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/active-directory-saas-boomi-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/active-directory-saas-boomi-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/active-directory-saas-boomi-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/active-directory-saas-boomi-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
  
### <a name="create-a-boomi-test-user"></a>Tworzenie użytkownika testowego Boomi

Aby umożliwić użytkownikom zalogować się do Boomi usługi Azure AD, musi być przygotowana do Boomi. W przypadku Boomi Inicjowanie obsługi to zadanie ręczne.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Aby udostępnić konta użytkownika, wykonaj następujące czynności:

1. Zaloguj się do witryny firmy Boomi jako administrator.

2. Po zalogowaniu, przejdź do **Zarządzanie użytkownikami** i przejdź do **użytkowników**.

    ![Użytkownicy](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_001.png "użytkowników")

3. Kliknij przycisk **+** ikonę i **ról użytkownika Add/Zachowaj** zostanie otwarte okno dialogowe.

    ![Użytkownicy](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_002.png "użytkowników")

    ![Użytkownicy](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_003.png "użytkowników")

    a. W **adres e-mail użytkownika** tekstowym, wpisz adres e-mail użytkownika, takich jak BrittaSimon@contoso.com.
    
    b. W **imię** pole tekstowe, nazwę użytkownika, takich jak Britta typu pierwszy.

    c. W **nazwisko** tekstowym, wpisz nazwisko użytkownika, takich jak Simona.
    
    d. Wprowadź nazwę danego użytkownika **identyfikator federacyjnej**. Każdy użytkownik musi mieć identyfikator federacyjnego, który unikatowo identyfikuje użytkownika w ramach konta.
    
    e. Przypisz **użytkownika standardowego** rolę dla użytkownika. Nie należy przypisywać roli administratora, ponieważ który spowodowałoby to nadanie mu dostęp do atmosfery, a także dostępu rejestracji jednokrotnej.
    
    f. Kliknij przycisk **OK**.
    
    > [!NOTE]
    > Użytkownik nie otrzyma wiadomość e-mail z powiadomieniem powitalnej zawierającą hasła, który może służyć do logowania się w AtomSphere konta, ponieważ jego hasło jest zarządzane przez dostawcę tożsamości. Możesz użyć innych Boomi użytkownika konta tworzenia narzędzi lub interfejsów API dostarczonych przez Boomi do kont użytkowników usługi AAD.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Boomi.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta Boomi, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Boomi**.

    ![Łącze Boomi na liście aplikacji](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Boomi w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji Boomi.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_203.png

