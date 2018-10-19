---
title: 'Samouczek: Integracja usługi Azure Active Directory z Consent2Go | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Consent2Go.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ea93bc02-58ca-4468-84ff-359888fc6183
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2018
ms.author: jeedes
ms.openlocfilehash: 89fc0b69a3a7ca3f795b4ae0e79b11a4bcd9c9fb
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2018
ms.locfileid: "49431630"
---
# <a name="tutorial-azure-active-directory-integration-with-consent2go"></a>Samouczek: Integracja usługi Azure Active Directory z Consent2Go

W tym samouczku dowiesz się, jak zintegrować Consent2Go w usłudze Azure Active Directory (Azure AD).

Integrowanie Consent2Go z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Consent2Go.
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do Consent2Go (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Consent2Go, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Consent2Go logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Consent2Go z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-consent2go-from-the-gallery"></a>Dodawanie Consent2Go z galerii
Aby skonfigurować integrację Consent2Go w usłudze Azure AD, należy dodać Consent2Go z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Consent2Go z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![image](./media/consent2go-tutorial/selectazuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![image](./media/consent2go-tutorial/a_select_app.png)
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![image](./media/consent2go-tutorial/a_new_app.png)

4. W polu wyszukiwania wpisz **Consent2Go**, wybierz opcję **Consent2Go** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![image](./media/consent2go-tutorial/tutorial_consent2go_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Consent2Go w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Consent2Go do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Consent2Go musi można ustanowić.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Consent2Go, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego Consent2Go](#create-a-consent2go-test-user)**  — aby odpowiednikiem Britta Simon w Consent2Go połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Consent2Go.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Consent2Go, wykonaj następujące czynności:**

1. W [witryny Azure portal](https://portal.azure.com/)na **Consent2Go** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![image](./media/consent2go-tutorial/b1_b2_select_sso.png)

2. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, kliknij przycisk **wybierz** dla **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![image](./media/consent2go-tutorial/b1_b2_saml_sso.png)

3. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** przycisk, aby otworzyć **podstawową konfigurację protokołu SAML** okna dialogowego.

    ![image](./media/consent2go-tutorial/b1-domains_and_urlsedit.png)

4. Na **podstawową konfigurację protokołu SAML** sekcji, wykonaj następujące czynności:

    W **adres URL logowania** pole tekstowe, wpisz adres URL jako:  `   https://www.mcbschools.com/Login`

    ![image](./media/consent2go-tutorial/tutorial_consent2go_url.png)

5. Na **Ustaw się logowania jednokrotnego przy użyciu protokołu SAML** stronie **certyfikat podpisywania SAML** kliknij ikonę kopiowania, aby skopiować **adres Url metadanych Federacji aplikacji** i zapisz go na komputerze.

    ![image](./media/consent2go-tutorial/tutorial_consent2go_certificate.png) 

6. Aby skonfigurować logowanie jednokrotne na **Consent2Go** stronie, musisz wysłać skopiowany **adres Url metadanych Federacji aplikacji** do [Consent2Go zespołem pomocy technicznej](mailto:support@consent2go.com). Ustawiają to ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

1. W witrynie Azure portal w okienku po lewej stronie wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.

    ![image](./media/consent2go-tutorial/d_users_and_groups.png)

2. Wybierz **nowego użytkownika** w górnej części ekranu.

    ![image](./media/consent2go-tutorial/d_adduser.png)

3. We właściwościach użytkownika wykonaj następujące czynności.

    ![image](./media/consent2go-tutorial/d_userproperties.png)

    a. W **nazwa** pola wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    c. Wybierz **właściwości**, wybierz opcję **hasło Show** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w polu hasło.

    d. Wybierz pozycję **Utwórz**.
 
### <a name="create-a-consent2go-test-user"></a>Tworzenie użytkownika testowego Consent2Go

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w Consent2Go. Praca z [zespołem pomocy technicznej Consent2Go](mailto:support@consent2go.com) Aby dodać użytkowników na platformie Consent2Go. Użytkownicy muszą być tworzone i aktywowana, aby używać logowania jednokrotnego.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Consent2Go.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**.

    ![image](./media/consent2go-tutorial/d_all_applications.png)

2. Na liście aplikacji wybierz **Consent2Go**.

    ![image](./media/consent2go-tutorial/tutorial_consent2go_app.png)

3. W menu po lewej stronie wybierz **użytkowników i grup**.

    ![image](./media/consent2go-tutorial/d_leftpaneusers.png)

4. Wybierz **Dodaj** przycisk, a następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

    ![image](./media/consent2go-tutorial/d_assign_user.png)

4. W **użytkowników i grup** okna dialogowego wybierz **Britta Simon** na liście użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

5. W **Dodaj przydziału** okna dialogowego wybierz **przypisać** przycisku.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Consent2Go w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Consent2Go.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


