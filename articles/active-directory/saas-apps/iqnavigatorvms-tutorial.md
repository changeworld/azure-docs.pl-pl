---
title: 'Samouczek: Integracja usługi Azure Active Directory z maszynami Wirtualnymi IQNavigator | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i IQNavigator maszyn wirtualnych.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: a8a09b25-dfa5-4c31-aea2-53bf1853b365
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: jeedes
ms.openlocfilehash: 062c83c7e1d621a80a5e81cdaf6b070eb446453c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55180538"
---
# <a name="tutorial-azure-active-directory-integration-with-iqnavigator-vms"></a>Samouczek: Integracja usługi Azure Active Directory z maszynami Wirtualnymi IQNavigator

W tym samouczku dowiesz się, jak zintegrować IQNavigator maszyn wirtualnych za pomocą usługi Azure Active Directory (Azure AD).

Integrowanie IQNavigator maszyn wirtualnych z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do maszyn wirtualnych IQNavigator
- Użytkowników, aby automatycznie uzyskać zalogowanych do maszyn wirtualnych z IQNavigator (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z maszynami Wirtualnymi IQNavigator, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Maszyny Wirtualne IQNavigator logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięcznej wersji próbnej tutaj [oferta wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie IQNavigator maszyn wirtualnych z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-iqnavigator-vms-from-the-gallery"></a>Dodawanie IQNavigator maszyn wirtualnych z galerii
Aby skonfigurować integrację IQNavigator maszyn wirtualnych w usłudze Azure AD, należy dodać IQNavigator maszyn wirtualnych z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać IQNavigator maszyn wirtualnych z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **maszyn wirtualnych IQNavigator**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_search.png)

1. W panelu wyników wybierz **maszyn wirtualnych IQNavigator**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji można skonfigurować, i test usługi Azure AD logowanie jednokrotne za pomocą IQNavigator maszyny Wirtualne oparte na użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika na maszynach wirtualnych IQNavigator jest dla użytkownika, w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników na maszynach wirtualnych IQNavigator musi zostać ustanowione.

Na maszynach wirtualnych IQNavigator, przypisz wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z maszynami Wirtualnymi IQNavigator, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego maszyn wirtualnych IQNavigator](#creating-a-iqnavigator-vms-test-user)**  — aby odpowiednikiem Britta Simon w IQNavigator maszyn wirtualnych, które jest połączone z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji IQNavigator maszyn wirtualnych.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z maszynami Wirtualnymi IQNavigator, wykonaj następujące czynności:**

1. W witrynie Azure portal na **maszyn wirtualnych IQNavigator** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.

    ![Konfigurowanie logowania jednokrotnego](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_samlbase.png)

1. Na **IQNavigator maszyn wirtualnych domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_url.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL:`iqn.com`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.iqnavigator.com/security/login?client_name=https://sts.window.net/<instance name>`

1. Sprawdź **Pokaż zaawansowane ustawienia adresu URL**, wykonaj następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_url1.png)

    W **przekazywania stanu** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca:`https://<subdomain>.iqnavigator.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości z faktycznym stanem adres URL odpowiedzi i przekazywania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta maszyn wirtualnych IQNavigator](https://www.beeline.com/iqn-product-support/) do uzyskania tych wartości.

1. Na **certyfikat podpisywania SAML** sekcji, kliknij przycisk kopiowania, aby skopiować **adres Url metadanych Federacji aplikacji** i wklej go w Notatniku.
    
    ![Konfigurowanie logowania jednokrotnego](./media/iqnavigatorvms-tutorial/tutorial_metadataurl.png)

1. Aplikacja IQNavigator oczekiwać, że wartość identyfikatora unikatowego użytkownika oświadczenia identyfikator nazwy. Klienta można mapować poprawną wartość oświadczenia identyfikator nazwy. W takim przypadku firma Microsoft zmapowane użytkownika. UserPrincipalName w celach demonstracyjnych. Jednak zgodnie z ustawieniami organizacji należy zamapować prawidłową wartość dla niego.

    ![Konfigurowanie logowania jednokrotnego](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_attribute.png)

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/iqnavigatorvms-tutorial/tutorial_general_400.png)

1. Na **konfiguracji maszyn wirtualnych IQNavigator** kliknij **konfigurowania maszyn wirtualnych IQNavigator** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_configure.png)

1. Aby skonfigurować logowanie jednokrotne na **IQNavigator maszyn wirtualnych** stronie, musisz wysłać **adres Url metadanych Federacji aplikacji**, **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL**do [zespołem pomocy technicznej maszyn wirtualnych IQNavigator](https://www.beeline.com/iqn-product-support/). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/iqnavigatorvms-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/iqnavigatorvms-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/iqnavigatorvms-tutorial/create_aaduser_03.png)

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/iqnavigatorvms-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.

### <a name="creating-a-iqnavigator-vms-test-user"></a>Tworzenie użytkownika testowego IQNavigator maszyn wirtualnych

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon na maszynach wirtualnych IQNavigator. Praca z [zespołem pomocy technicznej maszyn wirtualnych IQNavigator](https://www.beeline.com/iqn-product-support/) Aby dodać użytkowników w ramach konta IQNavigator maszyn wirtualnych.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do udzielania dostępu do maszyn wirtualnych IQNavigator za pomocą platformy Azure logowania jednokrotnego.

![Przypisz użytkownika][200]

**Aby przypisać Britta Simon IQNavigator maszyn wirtualnych, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

1. Na liście aplikacji wybierz **maszyn wirtualnych IQNavigator**.

    ![Konfigurowanie logowania jednokrotnego](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_app.png)

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka IQNavigator maszyn wirtualnych w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji IQNavigator maszyn wirtualnych.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/iqnavigatorvms-tutorial/tutorial_general_01.png
[2]: ./media/iqnavigatorvms-tutorial/tutorial_general_02.png
[3]: ./media/iqnavigatorvms-tutorial/tutorial_general_03.png
[4]: ./media/iqnavigatorvms-tutorial/tutorial_general_04.png

[100]: ./media/iqnavigatorvms-tutorial/tutorial_general_100.png

[200]: ./media/iqnavigatorvms-tutorial/tutorial_general_200.png
[201]: ./media/iqnavigatorvms-tutorial/tutorial_general_201.png
[202]: ./media/iqnavigatorvms-tutorial/tutorial_general_202.png
[203]: ./media/iqnavigatorvms-tutorial/tutorial_general_203.png

