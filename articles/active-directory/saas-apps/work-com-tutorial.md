---
title: 'Samouczek: Integracji Azure Active Directory z Work.com | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Work.com.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 98e6739e-eb24-46bd-9dd3-20b489839076
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 33c491b5a7ce518427e9f10b1c00fa15191b6fa7
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35938473"
---
# <a name="tutorial-azure-active-directory-integration-with-workcom"></a>Samouczek: Integracji Azure Active Directory z Work.com

Z tego samouczka dowiesz się integrowanie Work.com z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Work.com zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Work.com
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Work.com (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Work.com, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Work.com logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodaj Work.com z galerii
2. Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

## <a name="add-workcom-from-the-gallery"></a>Dodaj Work.com z galerii
Aby skonfigurować integrację usługi Azure AD Work.com, należy dodać Work.com z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Work.com z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Work.com**, wybierz pozycję **Work.com** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Dodaj z galerii](./media/work-com-tutorial/tutorial_work-com_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Work.com w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Work.com jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Work.com musi się.

W Work.com, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Work.com, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Work.com](#create-a-workcom-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Work.com połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Work.com.

>[!NOTE]
>Aby skonfigurować logowanie jednokrotne, należy skonfigurować niestandardową nazwę domeny Work.com jeszcze. Należy zdefiniować co najmniej nazwę domeny, sprawdzenie nazwę domeny i wdrożyć ją w całej organizacji.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Work.com, wykonaj następujące czynności:**

1. W portalu Azure na **Work.com** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Logowanie na podstawie protokołu SAML](./media/work-com-tutorial/tutorial_work-com_samlbase.png)

3. Na **Work.com domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Sekcja Work.com domeny i adresy URL](./media/work-com-tutorial/tutorial_work-com_url.png)

    W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `http://<companyname>.my.salesforce.com`

    > [!NOTE] 
    > Ta wartość nie jest prawdziwe. Zaktualizuj tę wartość przy rzeczywisty adres URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta Work.com](https://help.salesforce.com/articleView?id=000159855&type=3) aby zyskać tę wartość. 

4. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Sekcja certyfikat podpisywania SAML](./media/work-com-tutorial/tutorial_work-com_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Przycisk Zapisz](./media/work-com-tutorial/tutorial_general_400.png)

6. Na **konfiguracji Work.com** , kliknij przycisk **skonfigurować Work.com** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Sekcja konfiguracji Work.com](./media/work-com-tutorial/tutorial_work-com_configure.png) 
7. Zaloguj się jako administrator dzierżawy Work.com.

8. Przejdź do **Instalatora**.
   
    ![Instalator](./media/work-com-tutorial/ic794108.png "Instalatora")

9. W okienku nawigacji po lewej stronie w **Administruj** , kliknij przycisk **Zarządzanie domenami** rozwiń sekcję powiązane, a następnie kliknij przycisk **Moje domeny** otworzyć **Moje domeny** strony. 
   
    ![Mojej domeny](./media/work-com-tutorial/ic767825.png "mojej domeny")

10. Aby sprawdzić, czy domeny nie został skonfigurowany prawidłowo, upewnij się, że jest on "**krok 4 wdrożone dla użytkowników**" i zapoznaj się z "**Moje ustawienia domeny**".
   
    ![Wdrożonego dla użytkownika domeny](./media/work-com-tutorial/ic784377.png "wdrożonego dla użytkownika domeny")

11. Zaloguj się do dzierżawy Work.com.

12. Przejdź do **Instalatora**.
    
    ![Instalator](./media/work-com-tutorial/ic794108.png "Instalatora")

13. Rozwiń węzeł **kontroli bezpieczeństwa** menu, a następnie kliknij przycisk **ustawień rejestracji jednokrotnej**.
    
    ![Single Sign-On ustawienia](./media/work-com-tutorial/ic794113.png "Single Sign-On ustawienia")

14. Na **ustawień rejestracji jednokrotnej** okna dialogowego strony, należy wykonać następujące czynności:
    
    ![Włączone SAML](./media/work-com-tutorial/ic781026.png "SAML włączone")
    
    a. Wybierz **SAML włączone**.
    
    b. Kliknij przycisk **Nowy**.

15. W **SAML pojedynczego logowania jednokrotnego ustawienia** sekcji, wykonaj następujące czynności:
    
    ![SAML pojedynczy znak na ustawienie](./media/work-com-tutorial/ic794114.png "SAML pojedynczy znak na ustawienie")
    
    a. W **nazwa** tekstowym, wpisz nazwę dla danej konfiguracji.  
       
    > [!NOTE]
    > Wartość dla **nazwa** automatycznie wypełnić **Nazwa interfejsu API** pola tekstowego.
    
    b. W **wystawcy** pole tekstowe, Wklej wartość **identyfikator jednostki SAML** którego została skopiowana z portalu Azure.
    
    c. Aby przekazać certyfikat pobrany z portalu Azure, kliknij przycisk **Przeglądaj**.
    
    d. W **identyfikator jednostki** pole tekstowe, typ `https://salesforce-work.com`.
    
    e. Jako **typ tożsamości SAML**, wybierz pozycję **potwierdzenia zawiera identyfikator federacji z obiektu użytkownika**.
    
    f. Jako **lokalizacji tożsamości SAML**, wybierz pozycję **jest tożsamość w elemencie NameIdentfier instrukcji podmiotu**.
    
    g. W **adresu URL logowania do dostawcy tożsamości** pole tekstowe, Wklej wartość **SAML pojedynczy znak na adres URL usługi** którego została skopiowana z portalu Azure.

    h. W **adres URL wylogowania dostawcy tożsamości** pole tekstowe, Wklej wartość **Sign-Out URL** którego została skopiowana z portalu Azure.
    
    i. Jako **dostawcy zainicjował żądanie powiązania usługi**, wybierz pozycję **HTTP Post**.
    
    j. Kliknij pozycję **Zapisz**.

16. W portalu klasycznym Work.com, w okienku nawigacji po lewej stronie kliknij **Zarządzanie domenami** rozwiń sekcję powiązane, a następnie kliknij przycisk **Moje domeny** otworzyć **Moje domeny** strony. 
    
    ![Mojej domeny](./media/work-com-tutorial/ic794115.png "mojej domeny")

17. Na **Moje domeny** strony w **znakowanie strony logowania** kliknij **Edytuj**.
    
    ![Znakowanie strony logowania](./media/work-com-tutorial/ic767826.png "znakowanie strony logowania")

14. Na **znakowanie strony logowania** strony w **usługi uwierzytelniania** sekcji Nazwa Twojej **ustawienia logowania jednokrotnego SAML** jest wyświetlany. Wybierz go, a następnie kliknij przycisk **zapisać**.
    
    ![Znakowanie strony logowania](./media/work-com-tutorial/ic784366.png "znakowanie strony logowania")

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/work-com-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Użytkownicy i grupy -> Wszyscy użytkownicy](./media/work-com-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Add](./media/work-com-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Strony okna dialogowego użytkownika](./media/work-com-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-workcom-test-user"></a>Tworzenie użytkownika testowego Work.com
Dla usługi Azure Active Directory aby użytkownicy mogli się zalogować muszą one być przygotowana do Work.com. W przypadku Work.com Inicjowanie obsługi to zadanie ręczne.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Aby skonfigurować, inicjowanie obsługi użytkowników, wykonaj następujące czynności:
1. Zalogować się do witryny firmy Work.com jako administrator.

2. Przejdź do **Instalatora**.
   
    ![Instalator](./media/work-com-tutorial/IC794108.png "Instalatora")
3. Przejdź do **Zarządzanie użytkownikami \> użytkowników**.
   
    ![Zarządzaj użytkownikami](./media/work-com-tutorial/IC784369.png "Zarządzanie użytkownikami")

4. Kliknij przycisk **nowego użytkownika**.
   
    ![Wszyscy użytkownicy](./media/work-com-tutorial/IC794117.png "wszyscy użytkownicy")

5. W sekcji Edycja użytkownika należy wykonać następujące czynności, w przypadku atrybutów elementów prawidłową Azure AD konta chcesz udostępnić do powiązanych pól tekstowych:
   
    ![Edycja użytkownika](./media/work-com-tutorial/ic794118.png "Edycja użytkownika")
   
    a. W **imię** pole tekstowe, typ **imię** użytkownika **Britta**.
    
    b. W **nazwisko** pole tekstowe, typ **nazwisko** użytkownika **Simona**.
    
    c. W **Alias** pole tekstowe, typ **nazwa** użytkownika **BrittaS**.
    
    d. W **E-mail** pole tekstowe, typ **adres e-mail** użytkownika **Brittasimon@contoso.com**.
    
    e. W **nazwy użytkownika** tekstowym, wpisz nazwę użytkownika użytkownika, takich jak **Brittasimon@contoso.com**.
    
    f. W **pseudonim** pola tekstowego, a typ **pseudonim** użytkownika **Simona**.
    
    g. Wybierz **roli**, **licencji użytkownika**, i **profilu**.
    
    h. Kliknij pozycję **Zapisz**.  
      
    > [!NOTE]
    > Właściciel konta usługi Azure AD otrzyma wiadomość e-mail, łącznie z łączem do potwierdzenia konta, zanim staje się aktywny.
    > 
    > 

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Work.com.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta Work.com, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Work.com**.

    ![Work.com na liście aplikacji](./media/work-com-tutorial/tutorial_work-com_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Work.com w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji Work.com.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/work-com-tutorial/tutorial_general_01.png
[2]: ./media/work-com-tutorial/tutorial_general_02.png
[3]: ./media/work-com-tutorial/tutorial_general_03.png
[4]: ./media/work-com-tutorial/tutorial_general_04.png

[100]: ./media/work-com-tutorial/tutorial_general_100.png

[200]: ./media/work-com-tutorial/tutorial_general_200.png
[201]: ./media/work-com-tutorial/tutorial_general_201.png
[202]: ./media/work-com-tutorial/tutorial_general_202.png
[203]: ./media/work-com-tutorial/tutorial_general_203.png

