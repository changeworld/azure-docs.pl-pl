---
title: 'Samouczek: Integracji Azure Active Directory z mianowicie | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory, a mianowicie.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 9541d5c4-4c82-4b5b-b01a-6a3f75a2b7a1
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: 93e0467314329ab783017489c385007d30f9076b
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36219391"
---
# <a name="tutorial-azure-active-directory-integration-with-namely"></a>Samouczek: Integracji Azure Active Directory z mianowicie

Z tego samouczka dowiesz się mianowicie Integracja z usługą Azure Active Directory (Azure AD).

To znaczy integracji z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do:
- Można umożliwić użytkownikom automatycznie pobrać zalogowane do mianowicie (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować usługi Azure AD integracji z mianowicie, możesz potrzebne są następujące zasoby:

- Subskrypcję usługi Azure AD
- To znaczy logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie mianowicie z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-namely-from-the-gallery"></a>Dodawanie mianowicie z galerii
Aby skonfigurować integrację mianowicie do usługi Azure AD, należy dodać mianowicie z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać mianowicie z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **mianowicie**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/namely-tutorial/tutorial_namely_search.png)

5. W panelu wyników wybierz **mianowicie**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/namely-tutorial/tutorial_namely_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z, a mianowicie w oparciu o nazwie "Britta Simona" użytkownika testowego.

Do rejestracji jednokrotnej do pracy usługi Azure AD musi ustalić użytkownika odpowiednika w mianowicie do użytkownika w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w musi mianowicie określone.

To znaczy, przypisywanie wartości **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącze.

Do konfigurowania i testowania usługi Azure AD rejestracji jednokrotnej z mianowicie, możesz, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie mianowicie użytkownik testowy](#creating-a-namely-test-user)**  — aby odpowiednikiem Simona Britta w mianowicie połączonego użytkownika reprezentacja usługi Azure AD.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w mianowicie aplikacji.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z mianowicie, wykonaj następujące czynności:**

1. W portalu Azure na **mianowicie** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/namely-tutorial/tutorial_namely_samlbase.png)

3. Na **mianowicie domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/namely-tutorial/tutorial_namely_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<subdomain>.namely.com`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<subdomain>.namely.com/saml/metadata`

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL logowania i identyfikator, należy zaktualizować te wartości. Skontaktuj się z [zespół obsługi klienta a mianowicie](https://www.namely.com/contact/) uzyskać te wartości. 
 
4. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/namely-tutorial/tutorial_namely_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/namely-tutorial/tutorial_general_400.png)

6. Na **mianowicie konfiguracji** kliknij **skonfigurować mianowicie** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/namely-tutorial/tutorial_namely_configure.png) 

7. W innym oknie przeglądarki, zaloguj się na Twoje mianowicie witryny firmy jako administrator.

8. Na pasku narzędzi u góry kliknij **firmy**.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/namely-tutorial/tutorial_namely_06.png) 

9. Kliknij kartę **Ustawienia**.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/namely-tutorial/tutorial_namely_07.png) 

10. Kliknij przycisk **SAML**.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/namely-tutorial/tutorial_namely_08.png) 

11. Na **ustawienia SAML** wykonaj następujące czynności:
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/namely-tutorial/tutorial_namely_09.png)
 
    a. Kliknij przycisk **Włącz SAML**. 

    b. W **adres url logowania jednokrotnego dostawcy tożsamości** pole tekstowe, Wklej wartość **SAML pojedynczy znak na adres URL usługi**, które zostały skopiowane z portalu Azure.
    
    c. Otwórz w Notatniku pobranego certyfikatu, skopiuj zawartość, a następnie wklej go do **certyfikat dostawcy tożsamości** pola tekstowego.
     
    d. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/namely-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/namely-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/namely-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/namely-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-namely-test-user"></a>Tworzenie mianowicie użytkownik testowy

Celem tej sekcji jest utworzenie użytkownika o nazwie Simona Britta w mianowicie.

**Aby utworzyć użytkownika o nazwie Simona Britta w mianowicie, wykonaj następujące czynności:**

1. Logowanie do Twojej mianowicie witryny firmy jako administrator.

2. Na pasku narzędzi u góry kliknij **osób**.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/namely-tutorial/tutorial_namely_10.png) 

3. Kliknij przycisk **katalogu** kartę.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/namely-tutorial/tutorial_namely_11.png) 

4. Kliknij przycisk **Dodawanie nowej osoby**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/namely-tutorial/tutorial_namely_12.png)

5. Na **Dodawanie nowej osoby** okna dialogowego, wykonaj następujące czynności:

    a. W **imię** pole tekstowe, typ **Britta**.

    b. W **nazwisko** pole tekstowe, typ **Simona**.

    c. W **E-mail** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    d. Kliknij pozycję **Zapisz**.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do użycia Azure logowania jednokrotnego za udzielanie dostępu do:.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta do mianowicie, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **mianowicie**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/namely-tutorial/tutorial_namely_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

Celem tej sekcji służy do testowania konfigurację usługi Azure AD z logowania jednokrotnego za pomocą panelu dostępu.

Po kliknięciu kafelka mianowicie w panelu dostępu, użytkownik powinien pobrać automatycznie podpisany w przypadku mianowicie aplikacji

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/namely-tutorial/tutorial_general_01.png
[2]: ./media/namely-tutorial/tutorial_general_02.png
[3]: ./media/namely-tutorial/tutorial_general_03.png
[4]: ./media/namely-tutorial/tutorial_general_04.png

[100]: ./media/namely-tutorial/tutorial_general_100.png

[200]: ./media/namely-tutorial/tutorial_general_200.png
[201]: ./media/namely-tutorial/tutorial_general_201.png
[202]: ./media/namely-tutorial/tutorial_general_202.png
[203]: ./media/namely-tutorial/tutorial_general_203.png

