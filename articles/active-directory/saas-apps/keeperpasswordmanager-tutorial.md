---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Menedżera haseł hodowca & magazynu cyfrowych | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Menedżera haseł hodowca cyfrowego magazynu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: e1a98f6a-2dae-4734-bdbf-4fba742a61d2
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: dee9b81b6830244dec6860da0618d20c7f062ac2
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39430359"
---
# <a name="tutorial-azure-active-directory-integration-with-keeper-password-manager--digital-vault"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Menedżera haseł hodowca & cyfrowego magazynu

W tym samouczku dowiesz się, jak zintegrować Menedżera haseł hodowca & cyfrowego magazynu z usługą Azure Active Directory (Azure AD).

Integracja Menedżera haseł hodowca & cyfrowego magazynu z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Menedżera haseł hodowca & cyfrowego magazynu
- Użytkowników, aby automatycznie uzyskać zalogowanych do Menedżera haseł hodowca & cyfrowego magazynu (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Do konfigurowania integracji z usługą Azure AD za pomocą Menedżera haseł hodowca & magazynu cyfrowych, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Menedżer haseł hodowca & cyfrowego magazynu logowania jednokrotnego włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie menedżera haseł hodowca & cyfrowego magazynu z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-keeper-password-manager--digital-vault-from-the-gallery"></a>Dodawanie menedżera haseł hodowca & cyfrowego magazynu z galerii
Aby skonfigurować integrację Menedżera haseł hodowca & cyfrowego magazynu w usłudze Azure AD, należy dodać Menedżera haseł hodowca & cyfrowego magazynu z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Menedżera haseł hodowca & cyfrowego magazynu z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **Menedżera haseł hodowca & cyfrowego magazynu**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/keeperpasswordmanager-tutorial/tutorial_keeper_search.png)

1. W panelu wyników wybierz **Menedżera haseł hodowca & cyfrowego magazynu**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/keeperpasswordmanager-tutorial/tutorial_keeper_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i przetestować usługi Azure AD logowanie jednokrotne za pomocą Menedżera haseł hodowca & cyfrowego magazynu, w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Menedżera haseł hodowca & cyfrowego magazynu do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika Menedżer haseł hodowca & cyfrowego magazynu w musi nawiązać.

W Menedżera haseł hodowca & cyfrowego magazynu, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Menedżera haseł hodowca & cyfrowego magazynu, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Menedżera haseł hodowca & cyfrowego magazynu](#creating-a-keeperpasswordmanager-test-user)**  — zapewnienie odpowiednikiem Britta Simon w Menedżera haseł hodowca & cyfrowego magazynu, w którym jest połączony z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji Włączanie usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Menedżera haseł hodowca & cyfrowego magazynu.

**Aby skonfigurować usługi Azure AD logowanie jednokrotne za pomocą Menedżera haseł hodowca & cyfrowego magazynu, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Menedżera haseł hodowca & cyfrowego magazynu** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/keeperpasswordmanager-tutorial/tutorial_keeper_samlbase.png)

1. Na **Menedżera haseł hodowca & cyfrowego domena magazynu i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/keeperpasswordmanager-tutorial/tutorial_keeper_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://{SSO CONNECT SERVER}/sso-connect/saml/login`

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://{SSO CONNECT SERVER}/sso-connect/saml/sso`

    c. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://{SSO CONNECT SERVER}/sso-connect`

    > [!NOTE] 
    > Te wartości są prawdziwe. Rzeczywisty adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Skontaktuj się z pomocą [Menedżera haseł hodowca & cyfrowego klienta magazynu zespołu pomocy technicznej](https://keepersecurity.com/contact.html) do uzyskania tych wartości. 

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/keeperpasswordmanager-tutorial/tutorial_keeper_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/keeperpasswordmanager-tutorial/tutorial_general_400.png)
    
1. Na **cyfrowego konfiguracja magazynu & Menedżera haseł hodowca** , kliknij przycisk **Konfigurowanie Menedżera haseł hodowca & cyfrowego magazynu** otworzyć **Konfigurowanie logowania jednokrotnego** okno. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/keeperpasswordmanager-tutorial/tutorial_keeper_configure.png) 

1. Aby skonfigurować logowanie jednokrotne na **cyfrowego konfiguracja magazynu & Menedżera haseł hodowca** po stronie, postępuj zgodnie z wytycznymi podanymi na [Opiekun pomocy technicznej przewodnik](https://keepersecurity.com/assets/pdf/KeeperSSOConnect_v11.pdf).

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/keeperpasswordmanager-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/keeperpasswordmanager-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/keeperpasswordmanager-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/keeperpasswordmanager-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-keeper-password-manager--digital-vault-test-user"></a>Tworzenie użytkownika testowego Menedżera haseł hodowca & cyfrowego magazynu

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do Menedżera haseł hodowca & cyfrowego magazynu, musi być obsługiwana do Menedżera haseł hodowca & cyfrowego magazynu. Aplikacja obsługuje tylko w czasie Inicjowanie obsługi użytkowników oraz uwierzytelniania użytkowników w aplikacji, automatycznie tworzony. Możesz skontaktować się ze [Opiekun pomocy technicznej](https://keepersecurity.com/contact.html), jeśli chcesz ręcznie skonfigurować użytkowników.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do Menedżera haseł hodowca & cyfrowego magazynu.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Menedżera haseł hodowca & magazynu cyfrowych, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Menedżera haseł hodowca & cyfrowego magazynu**.

    ![Konfigurowanie logowania jednokrotnego](./media/keeperpasswordmanager-tutorial/tutorial_keeper_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Menedżera haseł hodowca & cyfrowego magazynu, w panelu dostępu, należy uzyskać strony logowania Menedżera haseł hodowca & cyfrowego magazynu aplikacji. Po pomyślnym uwierzytelnieniu powinna pojawić się do aplikacji. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/keeperpasswordmanager-tutorial/tutorial_general_01.png
[2]: ./media/keeperpasswordmanager-tutorial/tutorial_general_02.png
[3]: ./media/keeperpasswordmanager-tutorial/tutorial_general_03.png
[4]: ./media/keeperpasswordmanager-tutorial/tutorial_general_04.png

[100]: ./media/keeperpasswordmanager-tutorial/tutorial_general_100.png

[200]: ./media/keeperpasswordmanager-tutorial/tutorial_general_200.png
[201]: ./media/keeperpasswordmanager-tutorial/tutorial_general_201.png
[202]: ./media/keeperpasswordmanager-tutorial/tutorial_general_202.png
[203]: ./media/keeperpasswordmanager-tutorial/tutorial_general_203.png

