---
title: 'Samouczek: Azure Active Directory integracji z logowaniem Jednokrotnym Kantega dla FishEye/tygiel | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i logowania jednokrotnego Kantega dla FishEye/tygiel.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 9fe951fd-1530-4d33-a1a4-390385b99ce9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: d436b986d06c3ac5a3a2f6e328bb226bbf02edaf
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-fisheyecrucible"></a>Samouczek: Azure Active Directory integracji z logowaniem Jednokrotnym Kantega dla FishEye/tygiel

Z tego samouczka dowiesz integrowanie logowania jednokrotnego Kantega dla FishEye/tygiel z usługi Azure Active Directory (Azure AD).

Integrowanie logowania jednokrotnego Kantega dla FishEye/tygiel z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do logowania jednokrotnego Kantega dla FishEye/tygiel
- Umożliwia użytkownikom automatycznie pobrać zalogowane do logowania jednokrotnego Kantega dla FishEye/tygiel (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z logowania jednokrotnego Kantega dla FishEye/tygiel, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Kantega Usługa rejestracji Jednokrotnej dla FishEye/tygiel logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie logowania jednokrotnego Kantega dla FishEye/tygiel z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-kantega-sso-for-fisheyecrucible-from-the-gallery"></a>Dodawanie logowania jednokrotnego Kantega dla FishEye/tygiel z galerii
Aby skonfigurować integrację Kantega sesji rejestracji jednokrotnej dla FishEye/tygiel do usługi Azure AD, należy dodać logowania jednokrotnego Kantega dla FishEye/tygiel z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać logowania jednokrotnego Kantega dla FishEye/tygiel z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Kantega Usługa rejestracji Jednokrotnej dla FishEye/tygiel**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_kantegassoforfisheyecrucible_search.png)

5. W panelu wyników wybierz **Kantega Usługa rejestracji Jednokrotnej dla FishEye/tygiel**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_kantegassoforfisheyecrucible_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z logowania jednokrotnego Kantega dla FishEye/tygiel w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w rejestracji Jednokrotnej Kantega dla FishEye/tygiel jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w rejestracji Jednokrotnej Kantega dla FishEye/tygiel musi się.

W Kantega logowania jednokrotnego dla FishEye/tygiel, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z logowania jednokrotnego Kantega dla FishEye/tygiel, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie Kantega Usługa rejestracji Jednokrotnej dla użytkownika testowego FishEye/tygiel](#creating-a-kantega-sso-for-fisheyecrucible-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Kantega Usługa rejestracji Jednokrotnej dla FishEye/tygiel połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w Twojej rejestracji Jednokrotnej Kantega FishEye/tygiel aplikacji.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z logowania jednokrotnego Kantega dla FishEye/tygiel, wykonaj następujące czynności:**

1. W portalu Azure na **Kantega Usługa rejestracji Jednokrotnej dla FishEye/tygiel** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_kantegassoforfisheyecrucible_samlbase.png)

3. W **IDP** inicjowane w trybie **logowania jednokrotnego Kantega FishEye/tygiel domeny i adresów URL** sekcji wykonaj następujące kroki:

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_kantegassoforfisheyecrucible_url1.png)

    a. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

4. W **SP** inicjowane trybie wyboru **Pokaż zaawansowane ustawienia adresu URL** i wykonaj następujące kroki:

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_kantegassoforfisheyecrucible_url2.png)

    W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`
     
    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty identyfikator, adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Te wartości są odbierane podczas konfigurowania FishEye/tygiel dodatek, który znajduje się w dalszej części tego samouczka.

5. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_kantegassoforfisheyecrucible_certificate.png) 

6. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_general_400.png)
    
7. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do Twojej FishEye/tygiel na serwerze lokalnym.

8. Umieść kursor na koło zębate, a następnie kliknij przycisk **dodatki**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/addon1.png)

9. W sekcji Ustawienia systemu, kliknij przycisk **znaleźć nowe dodatki**. 

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/add-on2.png)

10. Wyszukiwanie **Kantega Usługa rejestracji Jednokrotnej dla tygiel** i kliknij przycisk **zainstalować** przycisk, aby zainstalować nowy wtyczki SAML.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/addon2.png)

11. Uruchamia instalację dodatku. 

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/addon33.png)

12. Po zakończeniu instalacji. Kliknij przycisk **Zamknij**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/addon34.png)

13. Kliknij pozycję **Zarządzaj**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/addon35.png)

14. Kliknij przycisk **Konfiguruj** do skonfigurowania nowej wtyczki.    

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/addon3.png)

15. W **SAML** sekcji. Wybierz **usługi Azure Active Directory (Azure AD)** z **dostawcy tożsamości Dodaj** listy rozwijanej.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/addon4.png)

16. Wybierz poziom subskrypcji jako **podstawowe**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/addon5.png)

17. Na **właściwości aplikacji** sekcji, wykonaj następujące kroki:

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/addon6.png)

    a. Kopiuj **identyfikator URI aplikacji** wartości i używać go jako **identyfikator, adres URL odpowiedzi i adres URL logowania** na **logowania jednokrotnego Kantega FishEye/tygiel domeny i adresów URL** sekcji w portalu Azure.

    b. Kliknij przycisk **Dalej**.

18. Na **importu metadanych** sekcji, wykonaj następujące kroki:

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/addon7.png)

    a. Wybierz **pliku metadanych na tym komputerze**i przekazywanie pliku metadanych, który został pobrany z portalu Azure.

    b. Kliknij przycisk **Dalej**.

19. Na **nazwę i logowania jednokrotnego lokalizację** sekcji, wykonaj następujące kroki:

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/addon8.png)

    a. Dodaj nazwę dostawcy tożsamości w **Nazwa dostawcy tożsamości** pola tekstowego (np. usługi Azure AD).

    b. Kliknij przycisk **Dalej**.

20. Sprawdź certyfikatu podpisywania, a następnie kliknij przycisk **dalej**.  

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/addon9.png)

21. Na **kont użytkowników typu rybie oko** sekcji, wykonaj następujące kroki:

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/addon10.png)

    a. Wybierz **tworzenie użytkowników w katalogu wewnętrzny FishEye firmy, w razie potrzeby** , a następnie wprowadź odpowiednią nazwę grupy użytkowników (może być wiele nie. grup rozdzielone przecinkami).

    b. Kliknij przycisk **Dalej**.

22. Kliknij przycisk **Zakończ**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/addon11.png)

23. Na **znanych domeny dla usługi Azure AD** sekcji, wykonaj następujące kroki: 

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/addon12.png)

    a. Wybierz **znane domen** z lewego panelu strony.

    b. Wprowadź nazwę domeny w **znane domen** pola tekstowego.

    c. Kliknij pozycję **Zapisz**.  

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-kantega-sso-for-fisheyecrucible-test-user"></a>Tworzenie Kantega Usługa rejestracji Jednokrotnej dla użytkownika testowego FishEye/tygiel

Aby umożliwić użytkownikom usługi Azure AD zalogować się do FishEye/tygiel, musi być przygotowana do FishEye/tygla. W Kantega logowania jednokrotnego dla FishEye/tygiel Inicjowanie obsługi to zadanie ręczne.

**Aby udostępnić konta użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do Twojego tygiel na lokalnym serwerze jako administrator.

2. Umieść kursor na koło zębate, a następnie kliknij przycisk **użytkowników**.

    ![Dodawanie pracownika](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/user1.png) 

3. W obszarze **użytkowników** sekcji, kliknij pozycję **Dodaj użytkownika**.

    ![Dodawanie pracownika](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/user2.png)

4. Na **Dodaj nowego użytkownika** okna dialogowego strony, należy wykonać następujące czynności:

    ![Dodawanie pracownika](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/user3.png) 

    a. W **Username** tekstowym, wpisz adres e-mail użytkownika, takich jak Brittasimon@contoso.com.
    
    b. W **nazwy wyświetlanej** pole tekstowe, nazwa wyświetlana typu użytkownika, takich jak Simona Britta.
    
    c. W **adres E-mail** tekstowym, wpisz adres e-mail użytkownika, takich jak Brittasimon@contoso.com.

    d. W **hasło** tekstowym, wpisz hasło użytkownika.  

    e. W **Potwierdź hasło** pole tekstowe, wprowadź ponownie hasło użytkownika.

    f. Kliknij pozycję **Add** (Dodaj).   

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do logowania jednokrotnego Kantega dla FishEye/tygiel.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta do logowania jednokrotnego Kantega dla FishEye/tygiel, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Kantega Usługa rejestracji Jednokrotnej dla FishEye/tygiel**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_kantegassoforfisheyecrucible_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu logowania jednokrotnego Kantega FishEye/tygiel kafelka w panelu dostępu należy należy pobrać automatycznie zalogowane do rejestracji Jednokrotnej z Kantega FishEye/tygiel aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_general_203.png

