---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą narzędzia Jamf Pro | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i narzędzia Jamf Pro.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 35e86d08-c29e-49ca-8545-b0ff559c5faf
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: jeedes
ms.openlocfilehash: d28e28a2c4f8144da16c4838f07c9b8bb5ce67f0
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2018
ms.locfileid: "48268161"
---
# <a name="tutorial-azure-active-directory-integration-with-jamf-pro"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą narzędzia Jamf Pro

W tym samouczku dowiesz się, jak integrowanie narzędzia Jamf Pro z usługą Azure Active Directory (Azure AD).

Integrowanie narzędzia Jamf Pro z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do narzędzia Jamf Pro.
- Użytkowników, aby automatycznie uzyskać zalogowanych do narzędzia Jamf Pro (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą narzędzia Jamf Pro, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Narzędzie Jamf Pro logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym.
Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie narzędzia Jamf Pro z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-jamf-pro-from-the-gallery"></a>Dodawanie narzędzia Jamf Pro z galerii

Aby skonfigurować integrację z narzędziem Jamf Pro w usłudze Azure AD, należy dodać narzędzia Jamf Pro z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać narzędzia Jamf Pro z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![image](./media/jamfprosamlconnector-tutorial/selectazuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![image](./media/jamfprosamlconnector-tutorial/a_select_app.png)
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![image](./media/jamfprosamlconnector-tutorial/a_new_app.png)

4. W polu wyszukiwania wpisz **narzędzia Jamf Pro**, wybierz opcję **narzędzia Jamf Pro** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![image](./media/jamfprosamlconnector-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji skonfigurujesz i test usługi Azure AD logowanie jednokrotne za pomocą narzędzia Jamf Pro w oparciu o nazwie "Britta Simon" użytkownika testowego.

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w narzędziu Jamf Pro do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w narzędziu Jamf Pro musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą narzędzia Jamf Pro, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego narzędzia Jamf Pro](#create-a-jamf-pro-test-user)**  — aby odpowiednikiem Britta Simon w narzędziu Jamf Pro, połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji narzędzia Jamf Pro.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne za pomocą narzędzia Jamf Pro, wykonaj następujące czynności:**

1. W [witryny Azure portal](https://portal.azure.com/)na **narzędzia Jamf Pro** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![image](./media/jamfprosamlconnector-tutorial/b1_b2_select_sso.png)

2. Kliknij przycisk **pojedynczej zmiany trybu logowania jednokrotnego** na górze ekranu, aby wybrać **SAML** trybu.

      ![image](./media/jamfprosamlconnector-tutorial/b1_b2_saml_ssso.png)

3. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, kliknij przycisk **wybierz** dla **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![image](./media/jamfprosamlconnector-tutorial/b1_b2_saml_sso.png)

4. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** przycisk, aby otworzyć **podstawową konfigurację protokołu SAML** okna dialogowego.

    ![image](./media/jamfprosamlconnector-tutorial/b1-domains_and_urlsedit.png)

5. Na **podstawową konfigurację protokołu SAML** sekcji, wykonaj następujące czynności:

    a. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<subdomain>.jamfcloud.com/saml/metadata`.

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<subdomain>.jamfcloud.com/saml/SSO`.

    ![image](./media/jamfprosamlconnector-tutorial//b2-domains_and_urls.png)

    c. Kliknij przycisk **Ustaw dodatkowe adresy URL**.

    d. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<subdomain>.jamfcloud.com`.

    ![image](./media/jamfprosamlconnector-tutorial//b4-domains_and_urls.png)

    > [!NOTE]
    > Te wartości są prawdziwe. Rzeczywisty identyfikator, adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Wystąpi rzeczywista wartość identyfikatora z **logowania jednokrotnego** sekcji w portalu narzędzia Jamf Pro, co zostało wyjaśnione w dalszej części tego samouczka. Można wyodrębnić rzeczywiste **poddomeny** z wartości identyfikatora i używać go **poddomeny** informacji adres URL logowania i adres URL odpowiedzi.

6. Na **Ustaw się logowania jednokrotnego przy użyciu protokołu SAML** strony w **certyfikat podpisywania SAML** sekcji, kliknij przycisk kopiowania, aby skopiować **adres Url metadanych Federacji aplikacji** i zapisz go na użytkownika komputer.

    ![image](./media/jamfprosamlconnector-tutorial/C2_certificate.png)

7. Aby zautomatyzować konfigurację w narzędziu Jamf Pro, musisz zainstalować **rozszerzenia przeglądarki do bezpiecznego Moje aplikacje logowania** , klikając **zainstalować rozszerzenie**.

    ![image](./media/jamfprosamlconnector-tutorial/install_extension.png)
 
8. Po dodaniu rozszerzenia do przeglądarki, kliknij pozycję **instalacji narzędzia Jamf Pro** nastąpi bezpośrednie przekierowanie do aplikacji w narzędziu Jamf Pro. W tym miejscu podaj poświadczenia administratora do logowania się do narzędzia Jamf Pro. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzowania czynności 9 – 12.

    ![image](./media/jamfprosamlconnector-tutorial/d1_saml.png)

9. Jeśli chcesz ręcznie skonfigurować narzędzia Jamf Pro, Otwórz nowe okno przeglądarki sieci web i dziennika do witryny firmy narzędzia Jamf Pro z uprawnieniami administratora i wykonaj następujące czynności:

10. Kliknij pozycję **ikonę ustawienia** w prawym górnym rogu strony.

    ![Konfiguracja narzędzia Jamf Pro](./media/jamfprosamlconnector-tutorial/configure1.png)

11. Kliknij pozycję **logowanie jednokrotne**.

    ![Konfiguracja narzędzia Jamf Pro](./media/jamfprosamlconnector-tutorial/configure2.png)

12. Na **logowania jednokrotnego** strony należy wykonać następujące czynności:

    ![Pojedynczy w narzędziu Jamf Pro](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_single.png)

    a. Wybierz **Jamf Pro serwera** włączyć logowanie jednokrotne dostępu.

    b. Wybierając **obejścia Zezwalaj wszystkim użytkownikom** użytkownicy nie nastąpi przekierowanie do strony logowania dostawcy tożsamości do uwierzytelniania, ale można zalogować się do narzędzia Jamf Pro bezpośrednio zamiast tego. Gdy użytkownik próbuje dostęp do narzędzia Jamf Pro za pośrednictwem dostawcy tożsamości, występuje logowanie Jednokrotne zainicjowane przez dostawcę tożsamości uwierzytelniania i autoryzacji.

    c. Wybierz **NameID** opcja dla **mapowania użytkownika: SAML**. Domyślnie to ustawienie ma wartość **NameID** , ale można zdefiniować atrybutów niestandardowych.

    d. Wybierz **E-mail** dla **mapowania użytkownika: narzędzia JAMF PRO**. Narzędzia Jamf Pro mapuje atrybutów SAML wysyłane przez dostawcę tożsamości na następujące sposoby: przez użytkowników i grup. Gdy użytkownik próbuje uzyskać dostęp do narzędzia Jamf Pro, domyślnie narzędzia Jamf Pro pobiera informacje o użytkowniku od dostawcy tożsamości i dopasowuje je względem kont użytkowników w narzędziu Jamf Pro. Jeśli przychodzące konto użytkownika nie istnieje w narzędziu Jamf Pro, to Dopasowywanie nazw grupy występuje.

    e. Wklej wartość `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` w **nazwa ATRYBUTU grupy** pola tekstowego.

13. Na tym samym przewiń stronę w dół maksymalnie **dostawcy tożsamości** w obszarze **logowania jednokrotnego** sekcji, a następnie wykonaj następujące czynności:

    ![Konfiguracja narzędzia Jamf Pro](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. Wybierz **innych** jako opcję z **dostawcy tożsamości** listy rozwijanej.

    b. W **inny DOSTAWCA** polu tekstowym wprowadź **usługi Azure AD**.

    c. Wybierz **adres URL metadanych** jako opcję z **źródła METADANYCH dostawcy tożsamości** listy rozwijanej i Wklej w poniższym polu tekstowym **adres Url metadanych Federacji aplikacji** wartość, która Skopiowano z witryny Azure portal.

    d. Kopiuj **identyfikator jednostki** wartość i wklej go w **identyfikator jednostki** polu tekstowym w **Jamf Pro domena i adresy URL** sekcji w witrynie Azure portal.

    >[!NOTE]
    > W tym miejscu wartość rozmyte jest częścią poddomeny. Ta wartość służy do ukończenia adres URL logowania i adres URL odpowiedzi w **Jamf Pro domena i adresy URL** sekcji w witrynie Azure portal.

    e. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

1. W witrynie Azure portal w okienku po lewej stronie wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.

    ![image](./media/jamfprosamlconnector-tutorial/d_users_and_groups.png)

2. Wybierz **nowego użytkownika** w górnej części ekranu.

    ![image](./media/jamfprosamlconnector-tutorial/d_adduser.png)

3. We właściwościach użytkownika wykonaj następujące czynności.

    ![image](./media/jamfprosamlconnector-tutorial/d_userproperties.png)

    a. W **nazwa** pola wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    c. Wybierz **właściwości**, wybierz opcję **hasło Show** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w polu hasło.

    d. Wybierz pozycję **Utwórz**.

### <a name="create-a-jamf-pro-test-user"></a>Tworzenie użytkownika testowego narzędzia Jamf Pro

Aby umożliwić użytkownikom usługi Azure AD zalogować się do narzędzia Jamf Pro, musi być obsługiwana w narzędziu Jamf Pro. W przypadku narzędzia Jamf Pro aprowizacji to zadanie ręczne.

**Aby udostępnić konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy narzędzia Jamf Pro z uprawnieniami administratora.

2. Kliknij pozycję **ikonę ustawienia** w prawym górnym rogu strony.

    ![Dodawanie pracownika](./media/jamfprosamlconnector-tutorial/configure1.png)

3. Kliknij pozycję **narzędzia Jamf Pro kont użytkowników i grup**.

    ![Dodawanie pracownika](./media/jamfprosamlconnector-tutorial/user1.png)

4. Kliknij przycisk **Nowy**.

    ![Dodawanie pracownika](./media/jamfprosamlconnector-tutorial/user2.png)

5. Wybierz **Utwórz standardowe konto**.

    ![Dodawanie pracownika](./media/jamfprosamlconnector-tutorial/user3.png)

6. Na **nowe konto** dailog, wykonaj następujące czynności:

    ![Dodawanie pracownika](./media/jamfprosamlconnector-tutorial/user4.png)

    a. W **USERNAME** polu tekstowym wpisz pełną nazwę BrittaSimon.

    b. Wybierz odpowiednie opcje zgodnie z Twoją organizację dla **poziom dostępu**, **zestawu uprawnień**oraz **stan dostępu**.

    c. W **imię i nazwisko** polu tekstowym wpisz pełną nazwę Britta Simon.

    d. W **adres E-mail** pole tekstowe, wpisz adres e-mail konta Britta Simon.

    e. W **hasło** pole tekstowe, wpisz hasło użytkownika.

    f. W **POTWIERDŹ hasło** pole tekstowe, wpisz hasło użytkownika.

    g. Kliknij pozycję **Zapisz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do narzędzia Jamf Pro.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **narzędzia Jamf Pro**.

    ![image](./media/jamfprosamlconnector-tutorial/d_all_applications.png)

2. Na liście aplikacji wybierz **narzędzia Jamf Pro**.

    ![image](./media/jamfprosamlconnector-tutorial/d_all_proapplications.png)

3. W menu po lewej stronie wybierz **użytkowników i grup**.

    ![image](./media/jamfprosamlconnector-tutorial/d_leftpaneusers.png)

4. Wybierz **Dodaj** przycisk, a następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

    ![image](./media/jamfprosamlconnector-tutorial/d_assign_user.png)

4. W **użytkowników i grup** okna dialogowego wybierz **Britta Simon** na liście użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

5. W **Dodaj przydziału** okna dialogowego wybierz **przypisać** przycisku.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka narzędzia Jamf Pro w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji narzędzia Jamf Pro.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
