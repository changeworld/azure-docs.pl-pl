---
title: 'Samouczek: Integracji Azure Active Directory z PerformanceCentre | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i PerformanceCentre.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 65288c32-f7e6-4eb3-a6dc-523c3d748d1c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: afee73a18502c48a6ff933f269f2572333d14196
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34349230"
---
# <a name="tutorial-azure-active-directory-integration-with-performancecentre"></a>Samouczek: Integracji Azure Active Directory z PerformanceCentre

Z tego samouczka dowiesz się integrowanie PerformanceCentre z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD PerformanceCentre zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do PerformanceCentre
- Umożliwia użytkownikom automatycznie pobrać zalogowane do PerformanceCentre (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z PerformanceCentre, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- PerformanceCentre logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie PerformanceCentre z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-performancecentre-from-the-gallery"></a>Dodawanie PerformanceCentre z galerii
Aby skonfigurować integrację usługi Azure AD PerformanceCentre, należy dodać PerformanceCentre z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać PerformanceCentre z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **PerformanceCentre**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_search.png)

5. W panelu wyników wybierz **PerformanceCentre**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z PerformanceCentre w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w PerformanceCentre jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w PerformanceCentre musi się.

W PerformanceCentre, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z PerformanceCentre, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego PerformanceCentre](#creating-a-performancecentre-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta PerformanceCentre połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji PerformanceCentre.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z PerformanceCentre, wykonaj następujące czynności:**

1. W portalu Azure na **PerformanceCentre** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_samlbase.png)

3. Na **PerformanceCentre domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `http://companyname.performancecentre.com/saml/SSO`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `http://companyname.performancecentre.com`

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL logowania i identyfikator, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej klienta PerformanceCentre](https://www.performancecentre.com/contact-us/) uzyskać te wartości. 

4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-performancecentre-tutorial/tutorial_general_400.png)

6. Na **konfiguracji PerformanceCentre** , kliknij przycisk **skonfigurować PerformanceCentre** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_configure.png) 

7. Logowanie do Twojej **PerformanceCentre** witryny firmy jako administrator.

8. Na karcie po lewej stronie kliknij **Konfiguruj**.
   
    ![Azure AD rejestracji jednokrotnej][10]

9. Na karcie po lewej stronie kliknij **różne**, a następnie kliknij przycisk **rejestracji jednokrotnej**.
   
    ![Azure AD rejestracji jednokrotnej][11]

10. Jako **protokołu**, wybierz pozycję **SAML**.
   
    ![Azure AD rejestracji jednokrotnej][12]

11. Otwórz w Notatniku plik metadanych pobranych, skopiuj zawartość, wklej ją do **metadanych dostawcy tożsamości** pola tekstowego, a następnie kliknij przycisk **zapisać**.
   
    ![Azure AD rejestracji jednokrotnej][13]

12. Sprawdź, czy wartości **jednostki podstawowego adresu URL** i **adres URL Identyfikatora jednostki** są poprawne.
    
     ![Azure AD rejestracji jednokrotnej][14]

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-performancecentre-test-user"></a>Tworzenie użytkownika testowego PerformanceCentre

Celem tej sekcji jest utworzenie użytkownika o nazwie Simona Britta w PerformanceCentre.

**Aby utworzyć użytkownika o nazwie Simona Britta w PerformanceCentre, wykonaj następujące czynności:**

1. Logowanie się do witryny firmy PerformanceCentre jako administrator.

2. W menu po lewej stronie kliknij **Interrelate**, a następnie kliknij przycisk **utworzyć uczestnika**.
   
    ![Utwórz użytkownika][400]

3. Na **Interrelate — tworzenie uczestnika** okna dialogowego, wykonaj następujące czynności:
   
    ![Utwórz użytkownika][401]
    
    a. Wpisz wymagane atrybuty Simona Britta do powiązanych pól tekstowych.
    
    >[!IMPORTANT]
    >Atrybut nazwy użytkownika w Britta w PerformanceCentre musi być taka sama jak nazwa użytkownika w usłudze Azure AD.
    
    b. Wybierz **Administrator klienta** jako **wybierz rolę**.
    
    c. Kliknij pozycję **Zapisz**. 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu PerformanceCentre.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta PerformanceCentre, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **PerformanceCentre**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

Celem tej sekcji służy do testowania konfigurację usługi Azure AD z logowania jednokrotnego za pomocą panelu dostępu.  

Po kliknięciu kafelka PerformanceCentre w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji PerformanceCentre.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_04.png
[10]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_06.png
[11]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_07.png
[12]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_08.png
[13]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_09.png
[14]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_10.png

[100]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_203.png
[400]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_11.png
[401]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_12.png

