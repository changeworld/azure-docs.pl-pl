---
title: 'Samouczek: Integracja usługi Azure Active Directory z Teamphoria | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Teamphoria.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d569c705-6f0f-4ec1-b485-ba82526b5d32
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2018
ms.author: jeedes
ms.openlocfilehash: 794945caeea113dc6f1cc2ab5e11a76c3e88c83e
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39041912"
---
# <a name="tutorial-azure-active-directory-integration-with-teamphoria"></a>Samouczek: Integracja usługi Azure Active Directory z Teamphoria

W tym samouczku dowiesz się, jak zintegrować Teamphoria w usłudze Azure Active Directory (Azure AD).

Integrowanie Teamphoria z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Teamphoria
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do Teamphoria (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Teamphoria, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Teamphoria logowania jednokrotnego włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym.
Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Teamphoria z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-teamphoria-from-the-gallery"></a>Dodawanie Teamphoria z galerii
Aby skonfigurować integrację Teamphoria w usłudze Azure AD, należy dodać Teamphoria z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Teamphoria z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure Portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Kliknij przycisk **Dodaj** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Teamphoria**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/teamphoria-tutorial/tutorial_teamphoria_search.png)

5. W panelu wyników wybierz **Teamphoria**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/teamphoria-tutorial/tutorial_teamphoria_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Teamphoria w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Teamphoria do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Teamphoria musi można ustanowić.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Teamphoria, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego Teamphoria](#creating-a-teamphoria-test-user)**  — aby odpowiednikiem Britta Simon w Teamphoria połączonego z jej reprezentacji usługi Azure AD.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Teamphoria.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Teamphoria, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Teamphoria** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.

    ![Konfigurowanie logowania jednokrotnego](./media/teamphoria-tutorial/tutorial_teamphoria_samlbase.png)

3. Na **Teamphoria domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/teamphoria-tutorial/tutorial_teamphoria_url.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL, za pomocą następującego wzorca: `https://<sub-domain>.teamphoria.com/login`   

    > [!NOTE] 
    > Wartość adres URL logowania nie jest prawdziwe. Musisz zaktualizować tę wartość za pomocą adresu URL logowania rzeczywistych. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Teamphoria](https://www.teamphoria.com/) można pobrać adresu URL logowania jednokrotnego.

4. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie Zapisz certyfikat na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/teamphoria-tutorial/tutorial_teamphoria_certificate.png)

5. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/teamphoria-tutorial/tutorial_general_400.png)

6. Na **konfiguracji Teamphoria** , kliknij przycisk **skonfigurować Teamphoria** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/teamphoria-tutorial/tutorial_teamphoria_configure.png)

7. Aby skonfigurować logowanie jednokrotne na **Teamphoria** strony logowania do aplikacji Teamphoria jako administrator.

8. Przejdź do **ustawienia administratora** kliknij opcję w pasku narzędzi po lewej stronie i na karcie Konfigurowanie **logowanie Jednokrotne** można otworzyć okna konfiguracji logowania jednokrotnego.

    ![Konfigurowanie logowania jednokrotnego](./media/teamphoria-tutorial/admin_sso_configure.png)

9. Kliknij pozycję **Dodaj nowego DOSTAWCĘ tożsamości** opcję w prawym górnym rogu, aby otworzyć formularz służący do dodawania ustawień logowania jednokrotnego.

    ![Konfigurowanie logowania jednokrotnego](./media/teamphoria-tutorial/add_new_identity_provider.png)

10. Wprowadź szczegółowe informacje w polach, zgodnie z opisem poniżej-

    ![Konfigurowanie logowania jednokrotnego](./media/teamphoria-tutorial/Teamphoria_sso_save.png)

    a. **Nazwa WYŚWIETLANA**: Wprowadź nazwę wyświetlaną wtyczki na stronę administratora.

    b. **Nazwa przycisku**: Nazwa kartę która będzie wyświetlana na stronie logowania dla logowania za pomocą logowania jednokrotnego.

    c. **CERTYFIKAT**: Certyfikat pobrany wcześniej z witryny Azure portal w programie Notatnik, Open skopiuj zawartość tego samego i wklej go w tym miejscu w polu.

    d. **PUNKT wejścia**: Wklej **SAML pojedynczego logowania jednokrotnego usługi adresu URL** skopiowany wcześniej z witryny Azure portal.

    e. Przełącz opcję **ON** i kliknij pozycję **ZAPISZ**.

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/teamphoria-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/teamphoria-tutorial/create_aaduser_02.png) 

3. W górnej części okna dialogowego kliknij **Dodaj** otworzyć **użytkownika** okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/teamphoria-tutorial/create_aaduser_03.png)

4. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/teamphoria-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.

### <a name="creating-a-teamphoria-test-user"></a>Tworzenie użytkownika testowego Teamphoria

Aby umożliwić użytkownikom usługi Azure AD zalogować się do Teamphoria, musi być obsługiwana w Teamphoria. W przypadku Teamphoria Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby udostępnić konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy Teamphoria jako administrator.

2. Kliknij pozycję **administratora** ustawień na pasku narzędzi po lewej stronie i w obszarze **ZARZĄDZAJ** karcie kliknij **użytkowników** o otwarcie strony administratora dla użytkowników.

    ![Dodawanie pracownika](./media/teamphoria-tutorial/admin_manage_users.png)

3. Kliknij pozycję **ZAPROSIĆ ręczne** opcji.

    ![Zaproś inne osoby](./media/teamphoria-tutorial/admin_manage_add_users.png)

4. Na tej stronie wykonaj następujące działania.
    
    ![Zaproś inne osoby](./media/teamphoria-tutorial/manual_user_invite.png)

    a. W **adres E-mail** polu tekstowym **adres e-mail** z BrittaSimon.

    b. W **imię** polu tekstowym wpisz **Britta**.

    c. W **nazwisko** polu tekstowym wpisz **Simon**.

    d. Kliknij przycisk **zaproszenia 1 użytkownika**. Użytkownik musi zaakceptować zaproszenie tworzone w systemie.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Teamphoria.

![Przypisz użytkownika][200]

**Aby przypisać Britta Simon Teamphoria, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji wybierz **Teamphoria**.

    ![Konfigurowanie logowania jednokrotnego](./media/teamphoria-tutorial/tutorial_teamphoria_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.

### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Jeśli chcesz przetestować pojedynczego ustawienia logowania jednokrotnego, otwórz Panel dostępu. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/teamphoria-tutorial/tutorial_general_01.png
[2]: ./media/teamphoria-tutorial/tutorial_general_02.png
[3]: ./media/teamphoria-tutorial/tutorial_general_03.png
[4]: ./media/teamphoria-tutorial/tutorial_general_04.png

[100]: ./media/teamphoria-tutorial/tutorial_general_100.png

[200]: ./media/teamphoria-tutorial/tutorial_general_200.png
[201]: ./media/teamphoria-tutorial/tutorial_general_201.png
[202]: ./media/teamphoria-tutorial/tutorial_general_202.png
[203]: ./media/teamphoria-tutorial/tutorial_general_203.png
