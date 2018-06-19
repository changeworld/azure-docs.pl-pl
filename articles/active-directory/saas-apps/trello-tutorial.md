---
title: 'Samouczek: Integracji Azure Active Directory z Trello | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Trello.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: cd5ae365-9ed6-43a6-920b-f7814b993949
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: jeedes
ms.openlocfilehash: da5ee5720487dabbf7e612ce6ec2b19f5eff6b37
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35941214"
---
# <a name="tutorial-azure-active-directory-integration-with-trello"></a>Samouczek: Integracji Azure Active Directory z Trello

Z tego samouczka dowiesz się integrowanie Trello z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Trello zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Trello.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Trello (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Trello, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Trello jednokrotnego włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Trello z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-trello-from-the-gallery"></a>Dodawanie Trello z galerii
Aby skonfigurować integrację usługi Azure AD Trello, należy dodać Trello z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Trello z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Trello**, wybierz pozycję **Trello** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Trello na liście wyników](./media/trello-tutorial/tutorial_trello_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Trello w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Trello jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Trello musi się.

W Trello, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Trello, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Trello](#create-a-trello-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Trello połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Trello.

>[!NOTE]
>Należy pobrać **\<enterprise\>** informacji o pracy z Trello. Jeśli nie masz wartość informacji o pracy, skontaktuj się z [zespołem pomocy technicznej Trello](mailto:support@trello.com) można uzyskać informacji o pracy należy przedsiębiorstwa.
    > 

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Trello, wykonaj następujące czynności:**

1. W portalu Azure na **Trello** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/trello-tutorial/tutorial_trello_samlbase.png)

3. Na **Trello domeny i adres URL** sekcji, jeśli chcesz skonfigurować aplikację w **IDP zainicjował tryb**, wykonaj następujące czynności:

    ![Adresy URL i domeny Trello pojedynczy informacje logowania jednokrotnego](./media/trello-tutorial/tutorial_trello_url.png)
    
    a. W **identyfikator** tekstowym, wpisz następujący adres URL: `https://trello.com/auth/saml/metadata`
    
    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://trello.com/auth/saml/consume/<enterprise>`

4. Jeśli chcesz skonfigurować aplikację w **SP zainicjował tryb**, wykonaj następujące czynności:

    ![Adresy URL i domeny Trello pojedynczy informacje logowania jednokrotnego](./media/trello-tutorial/tutorial_trello_url1.png)

    a. Sprawdź **Pokaż zaawansowane ustawienia adresu URL**.

    b. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://trello.com/auth/saml/login/<enterprise>` 

5. Aplikacja Trello oczekuje potwierdzenia SAML w celu uwzględnienia określonych atrybutów. Skonfiguruj następujące atrybuty dla tej aplikacji. Możesz zarządzać wartości tych atrybutów z **"Atrybuty użytkownika"** aplikacji. Poniższy zrzut ekranu przedstawia przykład tego.

    ![Konfigurowanie rejestracji jednokrotnej](./media/trello-tutorial/tutorial_trello_attribute.png)

6. Na **atrybuty tokenu SAML** okna dialogowego, dla każdego wiersza w tabeli poniżej, wykonaj następujące czynności:
 
    | Nazwa atrybutu | Wartość atrybutu |
    | --- | --- |
    | User.Email | User.mail |
    | User.FirstName | user.givenname |
    | User.LastName | user.surname |

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie rejestracji jednokrotnej](./media/trello-tutorial/tutorial_officespace_04.png)

    ![Konfigurowanie rejestracji jednokrotnej](./media/trello-tutorial/tutorial_attribute_05.png)

    b. W **nazwa** tekstowym, wpisz nazwę atrybut wyświetlany dla danego wiersza. 

    c. Z **wartość** listy, wpisz wartość atrybutu wyświetlany dla danego wiersza.
    
    d. Kliknij przycisk **OK**. 

7. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base64)** , a następnie zapisz plik metadanych na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/trello-tutorial/tutorial_trello_certificate.png) 

8. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/trello-tutorial/tutorial_general_400.png)
    
9. Na **konfiguracji Trello** , kliknij przycisk **skonfigurować Trello** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfiguracja Trello](./media/trello-tutorial/tutorial_trello_configure.png) 

10. Skonfigurować logowanie jednokrotne w **Trello** stronie, musisz przejść do [konfiguracji logowania jednokrotnego przedsiębiorstwa Trello](https://trello.com/sso-configuration) stronę, aby wysłać pobrany **certyfikatu (Base64)** i  **SAML pojedynczy znak na adres URL usługi** do [zespołem pomocy technicznej Trello](mailto:support@trello.com). To ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML one wartość.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/trello-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/trello-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/trello-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/trello-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-trello-test-user"></a>Tworzenie użytkownika testowego Trello

Celem tej sekcji jest utworzenie użytkownika o nazwie Simona Britta w Trello. Trello obsługę w czasie, który jest domyślnie włączone. Nie ma elementu akcji można w tej sekcji. Nowy użytkownik został utworzony podczas próby dostępu Trello, jeśli go jeszcze nie istnieje.

>[!Note]
>Jeśli trzeba ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej Trello](mailto:support@trello.com).


### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Trello.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta Trello, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Trello**.

    ![Łącze Trello na liście aplikacji](./media/trello-tutorial/tutorial_trello_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Trello w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji Trello.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/trello-tutorial/tutorial_general_01.png
[2]: ./media/trello-tutorial/tutorial_general_02.png
[3]: ./media/trello-tutorial/tutorial_general_03.png
[4]: ./media/trello-tutorial/tutorial_general_04.png

[100]: ./media/trello-tutorial/tutorial_general_100.png

[200]: ./media/trello-tutorial/tutorial_general_200.png
[201]: ./media/trello-tutorial/tutorial_general_201.png
[202]: ./media/trello-tutorial/tutorial_general_202.png
[203]: ./media/trello-tutorial/tutorial_general_203.png

