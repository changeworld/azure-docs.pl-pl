---
title: 'Samouczek: Integracji Azure Active Directory z obrazu przekazywania | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i przekazywania obrazu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 65bb5990-07ef-4244-9f41-cd28fc2cb5a2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: b4ed73ca669ede9c206abd653a0b991edc2b1063
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-image-relay"></a>Samouczek: Integracji Azure Active Directory z przekaźnika obrazu

Z tego samouczka dowiesz integrowanie przekazywania obrazu z usługi Azure Active Directory (Azure AD).

Integrowanie przekazywania obrazu z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do przekazywania obrazu
- Umożliwia użytkownikom automatycznie pobrać zalogowane do przekazywania obrazu (logowanie jednokrotne) z konta usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z obrazu przekazywania, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Obraz przekazywania logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie obrazu przekazywania z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-image-relay-from-the-gallery"></a>Dodawanie obrazu przekazywania z galerii
Aby skonfigurować integrację usługi Azure AD przekazywania obrazu, należy dodać przekazywania obraz w galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać przekazywania obraz w galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **przekazywania obrazu**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_search.png)

5. W panelu wyników wybierz **przekazywania obrazu**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z przekaźnika obraz w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w przekazywania obrazu jest dla użytkownika, w usłudze Azure AD. Innymi słowy musi można ustanowić łącze relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w przekazywania obrazu.

W przekazywania obrazu, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Do konfigurowania i testowania usługi Azure AD rejestracji jednokrotnej z przekaźnika obrazu, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego przekazywania obrazu](#creating-an-image-relay-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Relay obrazu jest połączony z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji przekaźnika obrazu.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z obrazu przekazywania, wykonaj następujące czynności:**

1. W portalu Azure na **przekazywania obrazu** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_samlbase.png)

3. Na **obrazu przekazywania domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<companyname>.imagerelay.com/`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<companyname>.imagerelay.com/sso/metadata`

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL logowania i identyfikator, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej klienta przekazywania obrazu](http://support.imagerelay.com/) uzyskać te wartości. 
 


4. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-imagerelay-tutorial/tutorial_general_400.png)

6. Na **Konfiguracja przekazywania obrazu** , kliknij przycisk **Konfigurowanie przekazywania obrazu** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adres URL usługi i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_configure.png) 

7. W innym oknie przeglądarki należy zalogować się jako administrator do witryny firmy przekazywania obrazu.

8. Na pasku narzędzi u góry kliknij **użytkowników i uprawnienia** obciążenia.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_06.png) 

9. Kliknij przycisk **utworzyć nowe uprawnienie**.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_08.png)

10. W **pojedynczy znak na ustawienia** obciążenie, wybierz opcję **tej grupie można tylko logowania za pomocą rejestracji jednokrotnej** pole wyboru, a następnie kliknij przycisk **zapisać**.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_09.png) 

11. Przejdź do **ustawienia konta**.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_10.png) 

12. Przejdź do **pojedynczy znak na ustawienia** obciążenia.
    
     ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_11.png)

13. Na **ustawienia SAML** okna dialogowego, wykonaj następujące czynności:
    
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_12.png)
    
    a. W **adres URL logowania** pole tekstowe, Wklej wartość **pojedynczy znak na adres URL usługi** którego została skopiowana z portalu Azure.

    b. W **adresu URL wylogowania** pole tekstowe, Wklej wartość **pojedynczy adres URL usługi Sign-Out** którego została skopiowana z portalu Azure.

    c. Jako **Format identyfikatora nazwy**, wybierz pozycję **urn: oasis: nazwy: tc: SAML:1.1:nameid-format: emailAddress**.

    d. Jako **powiązanie opcje żądania od dostawcy usług (obraz przekazywania)**, wybierz pozycję **powiązanie POST**.

    e. W obszarze **certyfikatu x.509**, kliknij przycisk **certyfikatu aktualizacji**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_17.png)

    f. Otwórz w Notatniku pobranego certyfikatu, skopiuj zawartość, a następnie wklej go w polu tekstowym certyfikatu x.509.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_18.png)

    g. W **Inicjowanie obsługi użytkowników just in Time** zaznacz **włączyć just in Time Inicjowanie obsługi użytkowników**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_19.png)

    h. Wybierz grupę uprawnienia (na przykład **logowania jednokrotnego podstawowe**) który jest zezwolenia na logowanie tylko za pośrednictwem rejestracji jednokrotnej.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_20.png)

    i. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-an-image-relay-test-user"></a>Tworzenie użytkownika testowego przekazywania obrazu

Celem tej sekcji jest utworzenie użytkownika o nazwie Simona Britta w przekazywania obrazu.

**Aby utworzyć użytkownika o nazwie Simona Britta w przekazywania obrazu, wykonaj następujące czynności:**

1. Logowanie do przekazywania obrazu witryny firmy jako administrator.

2. Przejdź do **użytkowników i uprawnienia** i wybierz **Tworzenie użytkownika logowania jednokrotnego**.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_21.png) 

3. Wprowadź **E-mail**, **imię**, **nazwisko**, i **firmy** użytkownika chcesz udostępnić, a następnie wybierz grupę uprawnień, (na przykład logowania jednokrotnego podstawowe) jest grupa, który można zalogować się tylko za pośrednictwem rejestracji jednokrotnej.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_22.png) 

4. Kliknij przycisk **Utwórz**.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do obrazu przekaźnika.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta do przekazywania obraz, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **przekazywania obrazu**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

Celem tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.    

Po kliknięciu kafelka przekazywania obrazu w panelu dostępu należy należy pobrać automatycznie zalogowane do aplikacji przekaźnika obrazu.


## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_04.png


[100]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_203.png

