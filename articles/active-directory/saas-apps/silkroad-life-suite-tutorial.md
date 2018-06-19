---
title: 'Samouczek: Azure Active Directory integracji z pakietem życia SilkRoad | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i SilkRoad życia pakietu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3cd92319-7964-41eb-8712-444f5c8b4d15
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: jeedes
ms.openlocfilehash: 58cfd858641263eb0c678203f191451345a0ded9
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35941244"
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>Samouczek: Azure Active Directory integracji z pakietem życia SilkRoad

Z tego samouczka dowiesz integrowanie SilkRoad Suite użytkowania z usługą Azure Active Directory (Azure AD).

Integrowanie SilkRoad Suite użytkowania z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do zestawu życia SilkRoad.
- Umożliwia użytkownikom automatycznie pobrać zalogowane SilkRoad życia pakietu (logowanie jednokrotne) z konta usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z pakietem życia SilkRoad, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Pakiet życia SilkRoad jednokrotnego włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie pakietu życia SilkRoad z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-silkroad-life-suite-from-the-gallery"></a>Dodawanie pakietu życia SilkRoad z galerii
Aby skonfigurować integrację usługi Azure AD SilkRoad życia pakietu, należy dodać pakiet życia SilkRoad z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać pakiet życia SilkRoad z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Suite życia SilkRoad**, wybierz pozycję **SilkRoad życia pakietu** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Pakiet życia SilkRoad na liście wyników](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z pakietem życia SilkRoad w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednikiem pakietu życia SilkRoad jest dla użytkownika, w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i danemu użytkownikowi pakietu życia SilkRoad musi określone.

SilkRoad życia pakietu, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z pakietem życia SilkRoad, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego zestawu życia SilkRoad](#create-a-silkroad-life-suite-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta SilkRoad życia pakiet, który jest połączony z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w SilkRoad życia pakietu aplikacji.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z pakietem życia SilkRoad, wykonaj następujące czynności:**

1. W portalu Azure na **Suite życia SilkRoad** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_samlbase.png)

3. Na **SilkRoad życia Suite domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Adresy URL i SilkRoad życia Suite domeny pojedynczy informacje logowania jednokrotnego](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_url1.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<subdomain>.silkroad-eng.com/Authentication/`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: 
    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/SP` |
    | `https://<subdomain>.silkroad.com/Authentication/SP` |

    c. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: 
    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/` |
    | `https://<subdomain>.silkroad.com/Authentication/` |
     
    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty identyfikator, adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej SilkRoad życia pakietu klienta](https://www.silkroad.com/locations/) uzyskać te wartości. 

4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/silkroad-life-suite-tutorial/tutorial_general_400.png)
    
6. Na **SilkRoad życia pakietu konfiguracji** , kliknij przycisk **skonfigurować pakiet życia SilkRoad** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![SilkRoad życia pakietu konfiguracji](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_configure.png) 

7. Logowanie do witryny firmy SilkRoad jako administrator. 
 
    >[!NOTE] 
    > Aby uzyskać dostęp do aplikacji uwierzytelniania Suite życia SilkRoad Konfigurowanie Federacji przy użyciu usługi Microsoft Azure AD, skontaktuj się z SilkRoad pomocy technicznej lub Twoim przedstawicielem SilkRoad usług.

8. Przejdź do **usługodawcy**, a następnie kliknij przycisk **szczegóły federacyjnego**. 
   
    ![Azure AD rejestracji jednokrotnej][10]

9. Kliknij przycisk **pobierania metadanych Federacji**, a następnie zapisz plik metadanych na tym komputerze.
   
    ![Azure AD rejestracji jednokrotnej][11] 

10. W Twojej **SilkRoad** aplikacji, kliknij przycisk **źródeł uwierzytelniania**.
   
    ![Azure AD rejestracji jednokrotnej][12] 

11. Kliknij przycisk **Dodawanie uwierzytelniania źródła**. 
   
    ![Azure AD rejestracji jednokrotnej][13] 

12. W **Dodaj źródło uwierzytelniania** sekcji, wykonaj następujące czynności: 
   
    ![Azure AD rejestracji jednokrotnej][14]
  
    a. W obszarze **opcja 2 — plik metadanych**, kliknij przycisk **Przeglądaj** można przekazać pliku metadanych pobranych z portalu Azure.
  
    b. Kliknij przycisk **dostawcy tożsamości utworzyć przy użyciu danych pliku**.

13. W **źródeł uwierzytelniania** kliknij **Edytuj**. 
    
     ![Azure AD rejestracji jednokrotnej][15] 

14. Na **Edytuj źródło uwierzytelniania** okna dialogowego, wykonaj następujące czynności: 
    
     ![Azure AD rejestracji jednokrotnej][16] 

    a. Jako **włączone**, wybierz pozycję **tak**.

    b. W **EntityId** pole tekstowe, Wklej wartość **identyfikator jednostki SAML** którego została skopiowana z portalu Azure.
   
    c. W **opis IdP** tekstowym, wpisz opis dla danej konfiguracji (na przykład: *logowania jednokrotnego programu Azure AD*).

    d. W **pliku metadanych** pole tekstowe, Przekaż **metadanych** pliku, który został pobrany z portalu Azure.
  
    e. W **nazwa IdP** tekstowym, wpisz nazwę, która jest specyficzna dla konfiguracji (na przykład: *Azure SP*).
  
    f. W **adres URL wylogowania usługi** pole tekstowe, Wklej wartość **Sign-Out URL** którego została skopiowana z portalu Azure.

    g. W **adres URL usługi logowania jednokrotnego w** pole tekstowe, Wklej wartość **SAML pojedynczy znak na adres URL usługi** którego została skopiowana z portalu Azure.

    h. Kliknij pozycję **Zapisz**.

15. Wyłącz wszystkie źródła uwierzytelniania. 
    
     ![Azure AD rejestracji jednokrotnej][17]

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/silkroad-life-suite-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/silkroad-life-suite-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/silkroad-life-suite-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/silkroad-life-suite-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-silkroad-life-suite-test-user"></a>Tworzenie użytkownika testowego SilkRoad życia pakietu

W tej sekcji należy utworzyć użytkownika o nazwie Simona Britta SilkRoad życia pakietu. Praca z [zespołem pomocy technicznej SilkRoad życia pakietu klienta](https://www.silkroad.com/locations/) Aby dodać użytkowników na platformie SilkRoad życia pakietu. Użytkownicy muszą utworzyć i aktywowana, aby użyć rejestracji jednokrotnej. 

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do zestawu życia SilkRoad.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta SilkRoad życia pakietu, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Suite życia SilkRoad**.

    ![Łącze SilkRoad życia pakietu na liście aplikacji](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka SilkRoad życia pakietu w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane SilkRoad życia pakietu aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/silkroad-life-suite-tutorial/tutorial_general_01.png
[2]: ./media/silkroad-life-suite-tutorial/tutorial_general_02.png
[3]: ./media/silkroad-life-suite-tutorial/tutorial_general_03.png
[4]: ./media/silkroad-life-suite-tutorial/tutorial_general_04.png

[100]: ./media/silkroad-life-suite-tutorial/tutorial_general_100.png

[200]: ./media/silkroad-life-suite-tutorial/tutorial_general_200.png
[201]: ./media/silkroad-life-suite-tutorial/tutorial_general_201.png
[202]: ./media/silkroad-life-suite-tutorial/tutorial_general_202.png
[203]: ./media/silkroad-life-suite-tutorial/tutorial_general_203.png
[10]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_06.png
[11]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_07.png
[12]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_08.png
[13]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_09.png
[14]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_10.png
[15]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_11.png
[16]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_12.png
[17]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_13.png
