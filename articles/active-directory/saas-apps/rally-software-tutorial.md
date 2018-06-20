---
title: 'Samouczek: Azure Active Directory integracji z oprogramowaniem Rally | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między oprogramowaniem Rally i Azure Active Directory.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: ba25fade-e152-42dd-8377-a30bbc48c3ed
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: jeedes
ms.openlocfilehash: d6cd215651dddf9ddd6d0bce17114ab0c0900da8
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36224219"
---
# <a name="tutorial-azure-active-directory-integration-with-rally-software"></a>Samouczek: Azure Active Directory integracji z oprogramowaniem Rally

Z tego samouczka dowiesz się integrowanie Rally oprogramowania z usługi Azure Active Directory (Azure AD).

Integrowanie Rally oprogramowania z usługi Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do oprogramowania Rally.
- Umożliwia użytkownikom automatycznie pobrać zalogowane Rally oprogramowania (logowanie jednokrotne) z konta usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z oprogramowaniem Rally, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Oprogramowanie Rally logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie oprogramowania Rally z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-rally-software-from-the-gallery"></a>Dodawanie oprogramowania Rally z galerii
Aby skonfigurować integrację usługi Azure AD Rally oprogramowania, należy dodać Rally oprogramowania z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Rally oprogramowanie z poziomu galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Rally oprogramowania**, wybierz pozycję **Rally oprogramowania** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Rally oprogramowania z listy wyników](./media/rally-software-tutorial/tutorial_rallysoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z oprogramowaniem Rally w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednikiem Rally oprogramowanie jest dla użytkownika, w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i danemu użytkownikowi w oprogramowaniu Rally musi określone.

Rally oprogramowania, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z oprogramowaniem Rally, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Rally oprogramowania](#create-a-rally-software-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Rally oprogramowania połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne do aplikacji oprogramowania Rally.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Rally oprogramowania, wykonaj następujące czynności:**

1. W portalu Azure na **Rally oprogramowania** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/rally-software-tutorial/tutorial_rallysoftware_samlbase.png)

3. Na **Rally domeny oprogramowania i adres URL** sekcji, wykonaj następujące czynności:

    ![Rally domeny oprogramowania i adres URL pojedynczego logowania jednokrotnego informacji](./media/rally-software-tutorial/tutorial_rallysoftware_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<tenant-name>.rally.com`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<tenant-name>.rally.com`

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL logowania i identyfikator, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej klienta oprogramowania Rally](https://help.rallydev.com/) uzyskać te wartości. 
 


4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/rally-software-tutorial/tutorial_rallysoftware_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/rally-software-tutorial/tutorial_general_400.png)

6. Na **Rally konfiguracji oprogramowania** kliknij **Konfigurowanie oprogramowania Rally** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adresu URL i identyfikator jednostki SAML** z **sekcji krótkimi opisami.**

    ![Rally konfiguracji oprogramowania](./media/rally-software-tutorial/tutorial_rallysoftware_configure.png) 

7. Zaloguj się do Twojego **Rally oprogramowania** dzierżawy.

8. Na pasku narzędzi u góry kliknij **Instalator**, a następnie wybierz **subskrypcji**.
   
    ![Subskrypcja](./media/rally-software-tutorial/ic769531.png "subskrypcji")

9. Kliknij przycisk **akcji** przycisku. Wybierz **edytować subskrypcji** w górnym rogu paska narzędzi.

10. Na **subskrypcji** strony okna dialogowego, wykonaj następujące czynności, a następnie kliknij **Zapisz i Zamknij**:
   
    ![Uwierzytelnianie](./media/rally-software-tutorial/ic769542.png "uwierzytelniania")
   
    a. Wybierz **uwierzytelniania Rally lub logowania jednokrotnego** z listy rozwijanej uwierzytelniania.

    b. W **adres URL dostawcy tożsamości** pole tekstowe, Wklej wartość **identyfikator jednostki SAML**, które zostały skopiowane z portalu Azure. 

    c. W **wylogowania logowania jednokrotnego** pole tekstowe, Wklej wartość **Sign-Out URL**, które zostały skopiowane z portalu Azure.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/rally-software-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/rally-software-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/rally-software-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/rally-software-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-rally-software-test-user"></a>Tworzenie użytkownika testowego Rally oprogramowania

Dla użytkowników usługi Azure AD można było się zalogować musi być przygotowana do aplikacji Rally oprogramowania przy użyciu nazwy użytkowników usługi Azure Active Directory.

**Aby skonfigurować, inicjowanie obsługi użytkowników, wykonaj następujące czynności:**

1. Zaloguj się do dzierżawy Rally oprogramowania.

2. Przejdź do **Instalator \> użytkowników**, a następnie kliknij przycisk **+ Dodaj nowy**.
   
    ![Użytkownicy](./media/rally-software-tutorial/ic781039.png "użytkowników")

3. Wpisz nazwę w polu tekstowym nowego użytkownika, a następnie kliknij przycisk **Dodaj szczegóły**.

4. W **Tworzenie użytkownika** sekcji, wykonaj następujące czynności:
   
    ![Utwórz użytkownika](./media/rally-software-tutorial/ic781040.png "Tworzenie użytkownika")

    a. W **nazwy użytkownika** tekstowym, wpisz nazwę użytkownika, takich jak **Brittsimon**.
   
    b. W **adres E-mail** pole tekstowe, wprowadź adres e-mail użytkownika, takich jak **brittasimon@contoso.com**.

    c. W **imię** tekst Wprowadź imię użytkownika, takich jak **Britta**.

    d. W **nazwisko** tekst Wprowadź nazwisko użytkownika, takich jak **Simona**.

    e. Kliknij przycisk **Zapisz i Zamknij**.

   >[!NOTE]
   >Inne narzędzia do tworzenia konta użytkownika Rally oprogramowania lub interfejsów API dostarczonych przez Rally oprogramowanie służy do obsługi administracyjnej kont użytkowników usługi Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do oprogramowania Rally.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta Rally oprogramowania, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Rally oprogramowania**.

    ![Łącze Rally oprogramowania na liście aplikacji](./media/rally-software-tutorial/tutorial_rallysoftware_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Rally oprogramowania w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji oprogramowania Rally.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/rally-software-tutorial/tutorial_general_01.png
[2]: ./media/rally-software-tutorial/tutorial_general_02.png
[3]: ./media/rally-software-tutorial/tutorial_general_03.png
[4]: ./media/rally-software-tutorial/tutorial_general_04.png

[100]: ./media/rally-software-tutorial/tutorial_general_100.png

[200]: ./media/rally-software-tutorial/tutorial_general_200.png
[201]: ./media/rally-software-tutorial/tutorial_general_201.png
[202]: ./media/rally-software-tutorial/tutorial_general_202.png
[203]: ./media/rally-software-tutorial/tutorial_general_203.png

