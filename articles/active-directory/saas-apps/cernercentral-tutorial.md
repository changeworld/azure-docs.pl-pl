---
title: 'Samouczek: Integracja usługi Azure Active Directory z centralnego Cerner | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między i Cerner Central, Azure Active Directory.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d2bc549d-d286-4679-854e-bb67c62b0475
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: jeedes
ms.openlocfilehash: 77cd9f90de1d50e91061a6a7222d01c72aadf3f3
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39047913"
---
# <a name="tutorial-azure-active-directory-integration-with-cerner-central"></a>Samouczek: Integracja usługi Azure Active Directory z centralnego Cerner

W tym samouczku dowiesz się, jak zintegrować Cerner centralnego przy użyciu usługi Azure Active Directory (Azure AD).

Integrowanie centralnego Cerner z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do centralnego Cerner
- Użytkowników, aby automatycznie uzyskać zalogowanych do centralnego Cerner (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z centralnego Cerner, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Zatwierdzone Cerner centralnej konto systemowe

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie centralnego Cerner z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-cerner-central-from-the-gallery"></a>Dodawanie centralnego Cerner z galerii
Aby skonfigurować integrację środkowej Cerner w usłudze Azure AD, należy dodać centralnego Cerner z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać centralnego Cerner z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisku na górze okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **centralnego Cerner**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/cernercentral-tutorial/tutorial_cernercentral_search.png)

5. W panelu wyników wybierz **centralnego Cerner**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/cernercentral-tutorial/tutorial_cernercentral_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurujesz, a test usługi Azure AD logowanie jednokrotne za pomocą centralnego Cerner oparte na użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika, Indie środkowe Cerner do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników, Indie środkowe Cerner musi zostać nawiązane.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z centralnego Cerner, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego centralnego Cerner](#creating-a-cerner-central-test-user)**  — aby odpowiednikiem Britta Simon Indie środkowe Cerner, połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji Włączanie usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Cerner centralnego.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z centralnego Cerner, wykonaj następujące czynności:**

1. W witrynie Azure portal na **centralnego Cerner** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.

    ![Konfigurowanie logowania jednokrotnego](./media/cernercentral-tutorial/tutorial_cernercentral_samlbase.png)

3. Na **Cerner centralnej domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/cernercentral-tutorial/tutorial_cernercentral_url.png)

    a. W **identyfikator** polu tekstowym wpisz wartość, przy użyciu następujących wzorców:

    | |
    |--|
    | `https://<instancename>.cernercentral.com/session-api/protocol/saml2/metadata` |
    | `https://<instancename>.sandboxcernercentral.com/session-api/protocol/saml2/metadata` |
    
    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następujących wzorców:
    | |
    |--|
    | `https://<instancename>.cernercentral.com/session-api/protocol/saml2/sso` |
    | `https://<instancename>.sandboxcernercentral.com/session-api/protocol/saml2/sso` |

    > [!NOTE]
    > Te wartości nie są rzeczywiste. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adres URL odpowiedzi. Skontaktuj się z pomocą [Cerner centralny zespół pomocy technicznej](https://wiki.ucern.com/display/CernerCentral/Contacting+Cloud+Operations) do uzyskania tych wartości.

4. Na **certyfikat podpisywania SAML** sekcji, kliknij przycisk kopiowania, aby skopiować **adres Url metadanych Federacji aplikacji** i wklej go w Notatniku.

    ![Konfigurowanie logowania jednokrotnego](./media/cernercentral-tutorial/tutorial_metadataurl.png)

5. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/cernercentral-tutorial/tutorial_general_400.png)

6. Aby skonfigurować logowanie jednokrotne na **centralnego Cerner** stronie, musisz wysłać **adres Url metadanych Federacji aplikacji** do [centralnego Cerner pomocy technicznej](https://wiki.ucern.com/display/CernerCentral/Contacting+Cloud+Operations). Na stronie aplikacji, aby ukończyć integracji skonfigurowana usługa rejestracji Jednokrotnej.

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/cernercentral-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/cernercentral-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/cernercentral-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/cernercentral-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** obiektu Britta Simon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.

### <a name="creating-a-cerner-central-test-user"></a>Tworzenie użytkownika testowego Cerner środkowa

**Środkowe Cerner** aplikacji umożliwia użycie uwierzytelniania od dowolnego dostawcy tożsamości federacyjnych. Jeśli użytkownik jest w stanie zalogować się do strony głównej aplikacji, są Sfederowane i nie ma potrzeby ręcznego aprowizacji. Więcej szczegółów można znaleźć [tutaj](cernercentral-provisioning-tutorial.md) dotyczące sposobu konfigurowania automatycznej aprowizacji użytkowników.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do centralnych Cerner.

![Przypisz użytkownika][200]

**Aby przypisać centralne Cerner Britta Simon, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji wybierz **centralnego Cerner**.

    ![Konfigurowanie logowania jednokrotnego](./media/cernercentral-tutorial/tutorial_cernercentral_app.png)

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.

### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Cerner centralnego w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikację Cerner Central. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie Aprowizowania użytkowników](cernercentral-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/cernercentral-tutorial/tutorial_general_01.png
[2]: ./media/cernercentral-tutorial/tutorial_general_02.png
[3]: ./media/cernercentral-tutorial/tutorial_general_03.png
[4]: ./media/cernercentral-tutorial/tutorial_general_04.png

[100]: ./media/cernercentral-tutorial/tutorial_general_100.png

[200]: ./media/cernercentral-tutorial/tutorial_general_200.png
[201]: ./media/cernercentral-tutorial/tutorial_general_201.png
[202]: ./media/cernercentral-tutorial/tutorial_general_202.png
[203]: ./media/cernercentral-tutorial/tutorial_general_203.png