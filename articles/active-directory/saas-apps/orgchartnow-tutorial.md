---
title: 'Samouczek: Integracji Azure Active Directory z teraz schemat organizacyjny | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i teraz schematu organizacyjnego.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 50a1522f-81de-4d14-9b6b-dd27bb1338a4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2018
ms.author: jeedes
ms.openlocfilehash: 41bc53e5618130eff9fabe54e345c1712453bdbc
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35941226"
---
# <a name="tutorial-azure-active-directory-integration-with-orgchart-now"></a>Samouczek: Integracji Azure Active Directory z teraz schematu organizacyjnego

Z tego samouczka dowiesz się integrowanie teraz schematu organizacyjnego w usłudze Azure Active Directory (Azure AD).

Integracja z usługą Azure AD teraz schemat organizacyjny zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do schematu organizacyjnego teraz.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do chwili schemat organizacyjny (logowanie jednokrotne) z konta usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z teraz schemat organizacyjny, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Schemat organizacyjny teraz jednokrotnego włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie teraz schemat organizacyjny z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-orgchart-now-from-the-gallery"></a>Dodawanie teraz schemat organizacyjny z galerii
Aby skonfigurować integrację teraz schemat organizacyjny do usługi Azure AD, należy dodać teraz schemat organizacyjny z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać teraz schemat organizacyjny z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **teraz schemat organizacyjny**, wybierz pozycję **teraz schemat organizacyjny** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Schemat organizacyjny teraz na liście wyników](./media/orgchartnow-tutorial/tutorial_orgchartnow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji możesz skonfigurować i test usługi Azure AD rejestracji jednokrotnej z teraz schemat organizacyjny w oparciu o nazwie "Britta Simona" użytkownika testowego.

Do rejestracji jednokrotnej do pracy usługi Azure AD musi ustalić użytkownika odpowiednika w chwili schemat organizacyjny do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i danemu użytkownikowi w chwili schematu organizacyjnego musi określone.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z teraz schemat organizacyjny, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego teraz schemat organizacyjny](#create-an-orgchart-now-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta teraz schemat organizacyjny, połączonej z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji teraz schematu organizacyjnego.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z teraz schemat organizacyjny, wykonaj następujące czynności:**

1. W portalu Azure na **teraz schemat organizacyjny** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/orgchartnow-tutorial/tutorial_orgchartnow_samlbase.png)

3. Na **schemat organizacyjny teraz domeny i adres URL** sekcji, jeśli chcesz skonfigurować aplikację w **IDP** inicjowane tryb:

    ![Schemat organizacyjny teraz domeny i adresów URL jednym informacje logowania jednokrotnego](./media/orgchartnow-tutorial/tutorial_orgchartnow_url.png)

    W **identyfikator** tekstowym, wpisz adres URL: `https://sso2.orgchartnow.com`

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** inicjowane tryb:

    ![Schemat organizacyjny teraz domeny i adresów URL jednym informacje logowania jednokrotnego](./media/orgchartnow-tutorial/tutorial_orgchartnow_url1.png)

    W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://sso2.orgchartnow.com/Shibboleth.sso/Login?entityID=<YourEntityID>&target=https://sso2.orgchartnow.com`
     
    > [!NOTE]
    > `<YourEntityID>` Identyfikator jednostki SAML jest kopiowana z sekcji krótkimi opisami opisane w dalszej części samouczka.

5. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/orgchartnow-tutorial/tutorial_orgchartnow_certificate.png) 

6. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/orgchartnow-tutorial/tutorial_general_400.png)
    
7. Na **schemat organizacyjny teraz konfiguracji** kliknij **Konfiguruj teraz schemat organizacyjny** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML identyfikator jednostki** z **sekcji krótkimi opisami** i użyć jej do ukończenia **adres URL logowania** w **schemat organizacyjny teraz domeny i adres URL w sekcji**.

    ![Konfiguracja teraz schematu organizacyjnego](./media/orgchartnow-tutorial/tutorial_orgchartnow_configure.png) 

8. Skonfigurować logowanie jednokrotne w **teraz schemat organizacyjny** stronie, musisz wysłać pobrany **XML metadanych** do [zespołem pomocy technicznej teraz schemat organizacyjny](mailto:ocnsupport@officeworksoftware.com). To ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML one wartość.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/orgchartnow-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/orgchartnow-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/orgchartnow-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/orgchartnow-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-an-orgchart-now-test-user"></a>Tworzenie użytkownika testowego teraz schematu organizacyjnego

Aby umożliwić użytkownikom usługi Azure AD Zaloguj się teraz schemat organizacyjny, muszą mieć przydzielone do schematu organizacyjnego teraz. 

1. Schemat organizacyjny teraz obsługę w czasie, który jest domyślnie włączone. Nowy użytkownik został utworzony podczas próby dostępu teraz schemat organizacyjny, jeśli go jeszcze nie istnieje. Tylko utworzy użytkownika w czasie inicjowania obsługi funkcji **tylko do odczytu** użytkownika, gdy żądanie logowania jednokrotnego pochodzi z rozpoznanym IDP i poczty e-mail w potwierdzenia języka SAML nie znajduje się na liście użytkowników. Inicjowanie obsługi funkcji automatycznie należy utworzyć grupę dostępu zatytułowany **ogólne** w chwili schematu organizacyjnego. Wykonaj następujące czynności, aby utworzyć grupę dostępu:

    a. Przejdź do **Zarządzaj grupami** opcji po kliknięciu przycisku **narzędzi** w prawym górnym rogu interfejsu użytkownika.

    ![Grupy teraz schematu organizacyjnego](./media/orgchartnow-tutorial/tutorial_orgchartnow_manage.png)    

    b. Wybierz **Dodaj** ikony, jak i nazwę grupy **ogólne** kliknięcie **OK**. 

    ![Dodaj teraz schematu organizacyjnego](./media/orgchartnow-tutorial/tutorial_orgchartnow_add.png)

    c. Wybierz foldery, które mają ogólne lub w trybie tylko do odczytu użytkownikom można uzyskać dostępu do:

    ![Foldery teraz schematu organizacyjnego](./media/orgchartnow-tutorial/tutorial_orgchartnow_chart.png)

    d. **Zablokuj** foldery tak, aby można je modyfikować tylko Administrator użytkowników. Następnie naciśnij klawisz **OK**.

    ![Schemat organizacyjny teraz blokady](./media/orgchartnow-tutorial/tutorial_orgchartnow_lock.png)

2. Aby utworzyć **Admin** użytkowników i **odczytu/zapisu** użytkowników, należy ręcznie utworzyć użytkownika w celu uzyskania dostępu do ich poziom uprawnień za pomocą logowania jednokrotnego. Aby udostępnić konta użytkownika, wykonaj następujące czynności:

    a. Zaloguj się teraz schematu organizacyjnego jako Administrator zabezpieczeń.

    b.  Polecenie **ustawienia** w prawym górnym rogu, a następnie przejdź do **Zarządzanie użytkownikami**.

    ![Ustawienia teraz schematu organizacyjnego](./media/orgchartnow-tutorial/tutorial_orgchartnow_settings.png)

    c. Polecenie **Dodaj** i wykonaj następujące czynności:

    ![Zarządzanie teraz schematu organizacyjnego](./media/orgchartnow-tutorial/tutorial_orgchartnow_manageusers.png)

    * W **identyfikator użytkownika** pole tekstowe, wprowadź identyfikator użytkownika, takich jak **brittasimon@contoso.com**.

    * W **adres E-mail** tekstowym wprowadź adres e-mail użytkownika, takich jak **brittasimon@contoso.com**.

    * Kliknij pozycję **Add** (Dodaj).
    
### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu teraz schematu organizacyjnego.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta teraz schemat organizacyjny, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **teraz schemat organizacyjny**.

    ![Łącze schemat organizacyjny teraz na liście aplikacji](./media/orgchartnow-tutorial/tutorial_orgchartnow_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka teraz schemat organizacyjny w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do tej aplikacji teraz schematu organizacyjnego.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/orgchartnow-tutorial/tutorial_general_01.png
[2]: ./media/orgchartnow-tutorial/tutorial_general_02.png
[3]: ./media/orgchartnow-tutorial/tutorial_general_03.png
[4]: ./media/orgchartnow-tutorial/tutorial_general_04.png

[100]: ./media/orgchartnow-tutorial/tutorial_general_100.png

[200]: ./media/orgchartnow-tutorial/tutorial_general_200.png
[201]: ./media/orgchartnow-tutorial/tutorial_general_201.png
[202]: ./media/orgchartnow-tutorial/tutorial_general_202.png
[203]: ./media/orgchartnow-tutorial/tutorial_general_203.png

