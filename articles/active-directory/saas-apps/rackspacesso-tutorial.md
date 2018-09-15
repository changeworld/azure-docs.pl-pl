---
title: 'Samouczek: Integracja usługi Azure Active Directory przy użyciu logowania jednokrotnego Rackspace | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i logowania jednokrotnego Rackspace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 36b398be-2f7e-4ce8-9031-53587299bc4a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2018
ms.author: jeedes
ms.openlocfilehash: e7a4dd57c7da3350e610b5f005aa59ea9d51dd56
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2018
ms.locfileid: "45607787"
---
# <a name="tutorial-azure-active-directory-integration-with-rackspace-sso"></a>Samouczek: Integracja usługi Azure Active Directory przy użyciu logowania jednokrotnego Rackspace

W tym samouczku dowiesz się, jak zintegrować Rackspace logowanie Jednokrotne z usługą Azure Active Directory (Azure AD).

Integrowanie Rackspace logowanie Jednokrotne z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do logowania jednokrotnego Rackspace.
- Użytkowników, aby automatycznie uzyskać zalogowanych do Rackspace SSO (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu logowania jednokrotnego Rackspace, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Usługa rejestracji Jednokrotnej Rackspace logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie rejestracji Jednokrotnej Rackspace z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-rackspace-sso-from-the-gallery"></a>Dodawanie rejestracji Jednokrotnej Rackspace z galerii
Aby skonfigurować integrację Rackspace logowania jednokrotnego w usłudze Azure AD, należy dodać Rackspace logowania jednokrotnego z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Rackspace logowania jednokrotnego z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Rackspace logowania jednokrotnego**, wybierz opcję **Rackspace logowania jednokrotnego** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Usługa rejestracji Jednokrotnej Rackspace, na liście wyników](./media/rackspacesso-tutorial/tutorial_rackspacesso_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji można skonfigurować, i test usługi Azure AD logowania jednokrotnego przy użyciu logowania jednokrotnego Rackspace oparte na użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Rackspace logowania jednokrotnego do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Rackspace logowania jednokrotnego musi nawiązać.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu logowania jednokrotnego Rackspace, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego logowania jednokrotnego Rackspace](#create-a-rackspace-sso-test-user)**  — aby odpowiednikiem Britta Simon w Rackspace Usługa rejestracji Jednokrotnej, połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji rejestracji Jednokrotnej Rackspace.

**Aby skonfigurować usługę Azure AD logowania jednokrotnego przy użyciu logowania jednokrotnego Rackspace, wykonaj następujące czynności:**

1. W witrynie Azure portal na **logowania jednokrotnego Rackspace** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/rackspacesso-tutorial/tutorial_rackspacesso_samlbase.png)

3. Na **Rackspace logowania jednokrotnego domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Domena logowania jednokrotnego Rackspace i adresy URL pojedynczego logowania jednokrotnego informacji](./media/rackspacesso-tutorial/tutorial_rackspacesso_url.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL: `https://login.rackspace.com/federate/`

4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/rackspacesso-tutorial/tutorial_rackspacesso_certificate.png) 

5. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/rackspacesso-tutorial/tutorial_general_400.png)

6. Aby skonfigurować logowanie jednokrotne na **Rackspace logowania jednokrotnego** stronie, musisz wysłać pobrany **XML metadanych** do [zespołem pomocy technicznej Rackspace logowania jednokrotnego](https://support.rackspace.com/). Ustawiają to ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/rackspacesso-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/rackspacesso-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/rackspacesso-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/rackspacesso-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-rackspace-sso-test-user"></a>Tworzenie użytkownika testowego Rackspace logowania jednokrotnego

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w Rackspace logowania jednokrotnego. Praca z [zespołem pomocy technicznej logowania jednokrotnego Rackspace](https://support.rackspace.com/) Aby dodać użytkowników na platformie Rackspace logowania jednokrotnego. Użytkownicy muszą być tworzone i aktywowana, aby używać logowania jednokrotnego.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do logowania jednokrotnego Rackspace.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon Rackspace logowania jednokrotnego, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **logowania jednokrotnego Rackspace**.

    ![Link Rackspace logowania jednokrotnego na liście aplikacji](./media/rackspacesso-tutorial/tutorial_rackspacesso_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Rackspace logowania jednokrotnego w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do Rackspace logowania jednokrotnego aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/rackspacesso-tutorial/tutorial_general_01.png
[2]: ./media/rackspacesso-tutorial/tutorial_general_02.png
[3]: ./media/rackspacesso-tutorial/tutorial_general_03.png
[4]: ./media/rackspacesso-tutorial/tutorial_general_04.png

[100]: ./media/rackspacesso-tutorial/tutorial_general_100.png

[200]: ./media/rackspacesso-tutorial/tutorial_general_200.png
[201]: ./media/rackspacesso-tutorial/tutorial_general_201.png
[202]: ./media/rackspacesso-tutorial/tutorial_general_202.png
[203]: ./media/rackspacesso-tutorial/tutorial_general_203.png

