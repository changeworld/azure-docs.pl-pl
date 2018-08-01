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
ms.date: 07/27/2018
ms.author: jeedes
ms.openlocfilehash: b003f29db699d89f0d3cec76ee3562ffad08b40f
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2018
ms.locfileid: "39346338"
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

- Subskrypcję usługi Azure AD
- Narzędzie Jamf Pro logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie narzędzia Jamf Pro z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-jamf-pro-from-the-gallery"></a>Dodawanie narzędzia Jamf Pro z galerii
Aby skonfigurować integrację z narzędziem Jamf Pro w usłudze Azure AD, należy dodać narzędzia Jamf Pro z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać narzędzia Jamf Pro z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **narzędzia Jamf Pro**, wybierz opcję **narzędzia Jamf Pro** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Narzędzie Jamf Pro na liście wyników](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_addfromgallery.png)

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

1. W witrynie Azure portal na **narzędzia Jamf Pro** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_samlbase.png)

3. Na **Jamf Pro domena i adresy URL** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![Narzędzie Jamf Pro domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_url.png)

    a. W **identyfikator jednostki** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<subdomain>.jamfcloud.com/saml/metadata`

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<subdomain>.jamfcloud.com/saml/SSO`

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![Narzędzie Jamf Pro domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_url1.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<subdomain>.jamfcloud.com`
     
    > [!NOTE]
    > Te wartości są prawdziwe. Rzeczywisty identyfikator, adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Wystąpi rzeczywista wartość identyfikatora z **logowania jednokrotnego** sekcji w portalu narzędzia Jamf Pro, co zostało wyjaśnione w dalszej części tego samouczka. Można wyodrębnić rzeczywiste **poddomeny** z wartości identyfikatora i używać go **poddomeny** informacji adres URL logowania i adres URL odpowiedzi.

5. Na **certyfikat podpisywania SAML** sekcji, kliknij przycisk kopiowania, aby skopiować **adres Url metadanych Federacji aplikacji** i wklej go w Notatniku.

    ![Link pobierania certyfikatu](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_certificate.png) 

6. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/jamfprosamlconnector-tutorial/tutorial_general_400.png)
    
7. W oknie przeglądarki internetowej innej Zaloguj się do witryny firmy narzędzia Jamf Pro, jako administrator.

8. Kliknij pozycję **ikonę ustawienia** w prawym górnym rogu strony.

    ![Konfiguracja narzędzia Jamf Pro](./media/jamfprosamlconnector-tutorial/configure1.png)

9. Kliknij pozycję **logowanie jednokrotne**.

    ![Konfiguracja narzędzia Jamf Pro](./media/jamfprosamlconnector-tutorial/configure2.png)

10. Na **logowania jednokrotnego** strony należy wykonać następujące czynności:

    ![Pojedynczy w narzędziu Jamf Pro](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_single.png)

    a. Wybierz **Jamf Pro serwera** włączyć logowanie jednokrotne dostępu.

    b. Wybierając **obejścia Zezwalaj wszystkim użytkownikom** użytkownicy nie nastąpi przekierowanie do strony logowania dostawcy tożsamości do uwierzytelniania, ale można zalogować się do narzędzia Jamf Pro bezpośrednio zamiast tego. Gdy użytkownik próbuje dostęp do narzędzia Jamf Pro za pośrednictwem dostawcy tożsamości, występuje logowanie Jednokrotne zainicjowane przez dostawcę tożsamości uwierzytelniania i autoryzacji.

    c. Wybierz **NameID** opcja dla **mapowania użytkownika: SAML**. Domyślnie to ustawienie ma wartość **NameID** , ale można zdefiniować atrybutów niestandardowych.

    d. Wybierz **E-mail** dla **mapowania użytkownika: narzędzia JAMF PRO**. Narzędzia Jamf Pro mapuje atrybutów SAML wysyłane przez dostawcę tożsamości na następujące sposoby: przez użytkowników i grup. Gdy użytkownik próbuje uzyskać dostęp do narzędzia Jamf Pro, domyślnie narzędzia Jamf Pro pobiera informacje o użytkowniku od dostawcy tożsamości i dopasowuje je względem kont użytkowników w narzędziu Jamf Pro. Jeśli przychodzące konto użytkownika nie istnieje w narzędziu Jamf Pro, to Dopasowywanie nazw grupy występuje.

    e. Wklej wartość `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` w **nazwa ATRYBUTU grupy** pola tekstowego.
 
11. Na tym samym przewiń stronę w dół maksymalnie **dostawcy tożsamości** w obszarze **logowania jednokrotnego** sekcji, a następnie wykonaj następujące czynności:

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

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/jamfprosamlconnector-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/jamfprosamlconnector-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/jamfprosamlconnector-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/jamfprosamlconnector-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
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

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon narzędzia Jamf Pro, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **narzędzia Jamf Pro**.

    ![Link narzędzia Jamf Pro, na liście aplikacji](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka narzędzia Jamf Pro w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji narzędzia Jamf Pro.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/jamfprosamlconnector-tutorial/tutorial_general_01.png
[2]: ./media/jamfprosamlconnector-tutorial/tutorial_general_02.png
[3]: ./media/jamfprosamlconnector-tutorial/tutorial_general_03.png
[4]: ./media/jamfprosamlconnector-tutorial/tutorial_general_04.png

[100]: ./media/jamfprosamlconnector-tutorial/tutorial_general_100.png

[200]: ./media/jamfprosamlconnector-tutorial/tutorial_general_200.png
[201]: ./media/jamfprosamlconnector-tutorial/tutorial_general_201.png
[202]: ./media/jamfprosamlconnector-tutorial/tutorial_general_202.png
[203]: ./media/jamfprosamlconnector-tutorial/tutorial_general_203.png

