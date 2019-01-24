---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Teamphoria | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Teamphoria.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: d569c705-6f0f-4ec1-b485-ba82526b5d32
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2018
ms.author: jeedes
ms.openlocfilehash: 8232af95cf8414df67343f5004975b11df4488a0
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54826721"
---
# <a name="tutorial-azure-active-directory-integration-with-teamphoria"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Teamphoria

W tym samouczku dowiesz się, jak zintegrować Teamphoria w usłudze Azure Active Directory (Azure AD).

Integrowanie Teamphoria z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Teamphoria
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do Teamphoria (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Teamphoria, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Teamphoria logowania jednokrotnego włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym.
Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Teamphoria z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-teamphoria-from-the-gallery"></a>Dodawanie Teamphoria z galerii
Aby skonfigurować integrację Teamphoria w usłudze Azure AD, należy dodać Teamphoria z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Teamphoria z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure Portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Kliknij przycisk **Dodaj** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **Teamphoria**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/teamphoria-tutorial/tutorial_teamphoria_search.png)

1. W panelu wyników wybierz **Teamphoria**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/teamphoria-tutorial/tutorial_teamphoria_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Teamphoria w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Teamphoria do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Teamphoria musi można ustanowić.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Teamphoria, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Teamphoria](#creating-a-teamphoria-test-user)**  — aby odpowiednikiem Britta Simon w Teamphoria połączonego z jej reprezentacji usługi Azure AD.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Teamphoria.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Teamphoria, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Teamphoria** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.

    ![Konfigurowanie logowania jednokrotnego](./media/teamphoria-tutorial/tutorial_teamphoria_samlbase.png)

1. Na **Teamphoria domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/teamphoria-tutorial/tutorial_teamphoria_url.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL, za pomocą następującego wzorca: `https://<sub-domain>.teamphoria.com/login`   

    > [!NOTE] 
    > Wartość adres URL logowania nie jest prawdziwe. Musisz zaktualizować tę wartość za pomocą adresu URL logowania rzeczywistych. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Teamphoria](https://www.teamphoria.com/) można pobrać adresu URL logowania jednokrotnego.

1. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie Zapisz certyfikat na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/teamphoria-tutorial/tutorial_teamphoria_certificate.png)

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/teamphoria-tutorial/tutorial_general_400.png)

1. Na **konfiguracji Teamphoria** , kliknij przycisk **skonfigurować Teamphoria** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/teamphoria-tutorial/tutorial_teamphoria_configure.png)

1. Aby skonfigurować logowanie jednokrotne na **Teamphoria** strony logowania do aplikacji Teamphoria jako administrator.

1. Przejdź do **ustawienia administratora** kliknij opcję w pasku narzędzi po lewej stronie i na karcie Konfigurowanie **logowanie Jednokrotne** można otworzyć okna konfiguracji logowania jednokrotnego.

    ![Konfigurowanie logowania jednokrotnego](./media/teamphoria-tutorial/admin_sso_configure.png)

1. Kliknij pozycję **Dodaj nowego DOSTAWCĘ tożsamości** opcję w prawym górnym rogu, aby otworzyć formularz służący do dodawania ustawień logowania jednokrotnego.

    ![Konfigurowanie logowania jednokrotnego](./media/teamphoria-tutorial/add_new_identity_provider.png)

1. Wprowadź szczegółowe informacje w polach, zgodnie z opisem poniżej-

    ![Konfigurowanie logowania jednokrotnego](./media/teamphoria-tutorial/Teamphoria_sso_save.png)

    a. **NAZWA WYŚWIETLANA**: Na stronie administratora, wprowadź nazwę wyświetlaną wtyczki.

    b. **NAZWA PRZYCISKU**: Nazwa karty, która będzie wyświetlana na stronie logowania dla logowania za pomocą logowania jednokrotnego.

    c. **CERTYFIKAT**: Otwórz certyfikat pobrany wcześniej z witryny Azure portal w programie Notatnik, skopiuj zawartość tego samego i wklej go poniżej w polu.

    d. **PUNKT WEJŚCIA**: Wklej **SAML pojedynczego logowania jednokrotnego usługi adresu URL** skopiowany wcześniej z witryny Azure portal.

    e. Przełącz opcję **ON** i kliknij pozycję **ZAPISZ**.

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/teamphoria-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/teamphoria-tutorial/create_aaduser_02.png) 

1. W górnej części okna dialogowego kliknij **Dodaj** otworzyć **użytkownika** okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/teamphoria-tutorial/create_aaduser_03.png)

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/teamphoria-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.

### <a name="creating-a-teamphoria-test-user"></a>Tworzenie użytkownika testowego Teamphoria

Aby umożliwić użytkownikom usługi Azure AD zalogować się do Teamphoria, musi być obsługiwana w Teamphoria. W przypadku Teamphoria Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do witryny firmy Teamphoria jako administrator.

1. Kliknij pozycję **administratora** ustawień na pasku narzędzi po lewej stronie i w obszarze **ZARZĄDZAJ** karcie kliknij **użytkowników** o otwarcie strony administratora dla użytkowników.

    ![Dodawanie pracownika](./media/teamphoria-tutorial/admin_manage_users.png)

1. Kliknij pozycję **ZAPROSIĆ ręczne** opcji.

    ![Zaproś inne osoby](./media/teamphoria-tutorial/admin_manage_add_users.png)

1. Na tej stronie wykonaj następujące działania.
    
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

1. Na liście aplikacji wybierz **Teamphoria**.

    ![Konfigurowanie logowania jednokrotnego](./media/teamphoria-tutorial/tutorial_teamphoria_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.

### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Jeśli chcesz przetestować pojedynczego ustawienia logowania jednokrotnego, otwórz Panel dostępu. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

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
