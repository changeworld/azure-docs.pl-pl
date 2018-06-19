---
title: 'Samouczek: Azure Active Directory integracji z oprogramowaniem chlorowców | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między oprogramowaniem halogenowe i Azure Active Directory.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 2ca2298d-9a0c-4f14-925c-fa23f2659d28
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 6342395aad48efce08b382bd546e746c7fa31c02
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35942617"
---
# <a name="tutorial-azure-active-directory-integration-with-halogen-software"></a>Samouczek: Azure Active Directory integracji z oprogramowaniem chlorowców

Z tego samouczka dowiesz się integrowanie chlorowców oprogramowania z usługi Azure Active Directory (Azure AD).

Integrowanie chlorowców oprogramowania z usługi Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do oprogramowania chlorowców
- Umożliwia użytkownikom automatycznie pobrać zalogowane oprogramowania chlorowców (logowanie jednokrotne) z konta usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z oprogramowaniem halogenowe, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Oprogramowanie chlorowców logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie oprogramowania chlorowców z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-halogen-software-from-the-gallery"></a>Dodawanie oprogramowania chlorowców z galerii

Aby skonfigurować integrację usługi Azure AD chlorowców oprogramowania, należy dodać chlorowców oprogramowania z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać chlorowców oprogramowanie z poziomu galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **oprogramowania chlorowców**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/halogen-software-tutorial/tutorial_halogensoftware_search.png)

5. W panelu wyników wybierz **oprogramowania chlorowców**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/halogen-software-tutorial/tutorial_halogensoftware_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z oprogramowaniem chlorowców w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w oprogramowaniu halogenowe jest dla użytkownika, w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i danemu użytkownikowi w oprogramowaniu chlorowców musi określone.

W oprogramowaniu chlorowców przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z oprogramowaniem chlorowców, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego oprogramowania chlorowców](#creating-a-halogen-software-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta chlorowców oprogramowania, które jest połączone z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w używanej aplikacji halogenowe.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z oprogramowaniem halogenowe, wykonaj następujące czynności:**

1. W portalu Azure na **oprogramowania chlorowców** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/halogen-software-tutorial/tutorial_halogensoftware_samlbase.png)

3. Na **chlorowców oprogramowania domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/halogen-software-tutorial/tutorial_halogensoftware_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://global.hgncloud.com/<companyname>`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://global.halogensoftware.com/<companyname>`, `https://global.hgncloud.com/<companyname>`

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL logowania i identyfikator, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej klienta oprogramowania chlorowców](https://support.halogensoftware.com/) uzyskać te wartości. 
 


4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/halogen-software-tutorial/tutorial_halogensoftware_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/halogen-software-tutorial/tutorial_general_400.png)

6. W oknie innej przeglądarki, logowanie do Twojej **oprogramowania chlorowców** aplikacji jako administrator.

7. Kliknij przycisk **opcje** kartę. 
   
    ![Co to jest program Azure AD Connect][12]

8. W okienku nawigacji po lewej stronie kliknij **Konfiguracja SAML**. 
   
    ![Co to jest program Azure AD Connect][13]

9. Na **Konfiguracja SAML** wykonaj następujące czynności: 

    ![Co to jest program Azure AD Connect][14]

     a. Jako **Unikatowy identyfikator**, wybierz pozycję **NameID**.

     b. Jako **mapy Unikatowy identyfikator do**, wybierz pozycję **Username**.
  
     c. Aby przekazać plik metadanych pobranych, kliknij przycisk **Przeglądaj** i wybierz plik, a następnie **Przekaż plik**.
 
     d. Aby przetestować konfigurację, kliknij przycisk **Uruchom Test**. 
    
    >[!NOTE]
    >Należy oczekiwać na komunikat "*zakończeniu testu SAML. Zamknij to okno*". Zamknięcie okna przeglądarki otwarty. **Włącz SAML** pole wyboru jest włączone, tylko jeśli test została ukończona. 
     
     e. Wybierz **Włącz SAML**.
    
     f. Kliknij przycisk **zapisać zmiany**. 

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/halogen-software-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/halogen-software-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/halogen-software-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/halogen-software-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, nazwa typu jako **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-halogen-software-test-user"></a>Tworzenie użytkownika testowego chlorowców oprogramowania

Celem tej sekcji jest utworzenie użytkownika o nazwie Simona Britta chlorowców oprogramowania.

**Aby utworzyć użytkownika o nazwie Simona Britta chlorowców oprogramowania, wykonaj następujące kroki:**

1. Zaloguj się na Twojej **oprogramowania chlorowców** aplikacji jako administrator.

2. Kliknij przycisk **użytkownika Centrum** , a następnie kliknij pozycję **Tworzenie użytkownika**.
   
    ![Co to jest program Azure AD Connect][300]  

3. Na **nowego użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
   
    ![Co to jest program Azure AD Connect][301]

    a. W **imię** tekstowym, wpisz imię użytkownika, takich jak **Britta**.
    
    b. W **nazwisko** tekstowym, wpisz nazwisko użytkownika, takich jak **Simona**. 

    c. W **Username** pole tekstowe, typ **Simona Britta**, nazwę użytkownika w portalu Azure.

    d. W **hasło** tekstowym, wpisz hasło dla Britta.
    
    e. Kliknij pozycję **Zapisz**.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do oprogramowania halogenowe.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta chlorowców oprogramowania, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **oprogramowania chlorowców**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/halogen-software-tutorial/tutorial_halogensoftware_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

Celem tej sekcji służy do testowania konfigurację usługi Azure AD z logowania jednokrotnego za pomocą panelu dostępu.

Po kliknięciu kafelka oprogramowania chlorowców w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji oprogramowania halogenowe.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/halogen-software-tutorial/tutorial_general_01.png
[2]: ./media/halogen-software-tutorial/tutorial_general_02.png
[3]: ./media/halogen-software-tutorial/tutorial_general_03.png
[4]: ./media/halogen-software-tutorial/tutorial_general_04.png

[12]: ./media/halogen-software-tutorial/tutorial_halogen_12.png

[13]: ./media/halogen-software-tutorial/tutorial_halogen_13.png

[14]: ./media/halogen-software-tutorial/tutorial_halogen_14.png

[100]: ./media/halogen-software-tutorial/tutorial_general_100.png

[200]: ./media/halogen-software-tutorial/tutorial_general_200.png
[201]: ./media/halogen-software-tutorial/tutorial_general_201.png
[202]: ./media/halogen-software-tutorial/tutorial_general_202.png
[203]: ./media/halogen-software-tutorial/tutorial_general_203.png

[300]: ./media/halogen-software-tutorial/tutorial_halogen_300.png

[301]: ./media/halogen-software-tutorial/tutorial_halogen_301.png
