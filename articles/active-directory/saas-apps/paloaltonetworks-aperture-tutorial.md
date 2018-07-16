---
title: 'Samouczek: Integracja usługi Azure Active Directory z Palo Alto Networks - przysłony | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Palo Alto Networks - przysłony.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a5ea18d3-3aaf-4bc6-957c-783e9371d0f1
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: jeedes
ms.openlocfilehash: 20292c06cf336a0245b5f5db6b1cb4894df0f1ae
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39051585"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---aperture"></a>Samouczek: Integracja usługi Azure Active Directory z Palo Alto Networks - przysłony

W tym samouczku dowiesz się, jak zintegrować Palo Alto Networks - przysłony w usłudze Azure Active Directory (Azure AD).

Integrowanie Palo Alto Networks — przysłony z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Palo Alto Networks - przysłony.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do Palo Alto Networks - przysłony (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Do konfigurowania integracji z usługą Azure AD z Palo Alto Networks - przysłony, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Palo Alto Networks — przysłony logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Palo Alto Networks - przysłony z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-palo-alto-networks---aperture-from-the-gallery"></a>Dodawanie Palo Alto Networks - przysłony z galerii
Aby skonfigurować integrację Palo Alto Networks - przysłony w usłudze Azure AD, należy dodać Palo Alto Networks - przysłony z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Palo Alto Networks - przysłony z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Palo Alto Networks - przysłony**, wybierz opcję **Palo Alto Networks - przysłony** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Palo Alto Networks - przysłony na liście wyników](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą Palo Alto Networks — przysłony oparte na użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Palo Alto Networks - przysłony do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w Palo Alto Networks — przysłony musi zostać ustanowione.

Do konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą Palo Alto Networks - przysłony, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Utwórz Palo Alto Networks - użytkownika testowego przysłony](#create-a-palo-alto-networks---aperture-test-user)**  — aby odpowiednikiem Britta Simon w Palo Alto Networks - przysłony połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i skonfigurować logowanie jednokrotne w sieci Palo Alto - przysłony aplikacji.

**Aby skonfigurować usługę Azure AD w logowanie jednokrotne za pomocą Palo Alto Networks - przysłony, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Palo Alto Networks - przysłony** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_samlbase.png)

3. Na **Palo Alto Networks - przysłony domena i adresy URL** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![Palo Alto Networks - przysłony domena i adresy URL pojedynczy informacje logowania jednokrotnego](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_url.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/metadata`

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/auth`

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![Palo Alto Networks - przysłony domena i adresy URL pojedynczy informacje logowania jednokrotnego](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_url1.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/sign_in`
     
    > [!NOTE] 
    > Te wartości są prawdziwe. Rzeczywisty identyfikator, adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Skontaktuj się z pomocą [Palo Alto Networks - zespół obsługi klienta przysłony](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) do uzyskania tych wartości. 

5. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_certificate.png) 

6. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/paloaltonetworks-aperture-tutorial/tutorial_general_400.png)


7. Na **Palo Alto Networks — Konfiguracja przysłony** , kliknij przycisk **skonfigurować Palo Alto Networks - przysłony** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego adres URL usługi** z **krótki przewodnik po sekcji.**

    ![Łącze Konfiguruj](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_configure.png)

8. W oknie przeglądarki internetowej innej, zaloguj się do Palo Alto Networks - przysłony jako Administrator.

9. Na pasku menu u góry kliknij **ustawienia**.

    ![Karta Ustawienia](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_settings.png)

10. Przejdź do **aplikacji** kliknij sekcję **uwierzytelniania** tworzą menu po lewej stronie.

    ![Na karcie uwierzytelniania](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_auth.png)
    
11. Na **uwierzytelniania** strony należy wykonać następujące czynności:
    
    ![Karta uwierzytelnianie](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_singlesignon.png)

    a. Sprawdź **Włącz pojedynczy znak-On(Supported SSP Providers are Okta, Onelogin)** z **logowania jednokrotnego** pola.

    b. W **identyfikator dostawcy tożsamości** pola tekstowego, Wklej wartość **SAML identyfikator jednostki**, które zostały skopiowane z witryny Azure portal.

    c. Kliknij przycisk **wybierz plik** można przekazać certyfikatu pobrany z usługi Azure AD w **certyfikatu dostawcy tożsamości** pola.

    d. W **adres URL logowania jednokrotnego dostawcy tożsamości** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego adres URL usługi**, które zostały skopiowane z witryny Azure portal.

    e. Zapoznaj się z informacjami o tożsamości z **informacje przysłony** sekcji i Pobierz certyfikat z **klucz przysłony** pola.

    f. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/paloaltonetworks-aperture-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/paloaltonetworks-aperture-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/paloaltonetworks-aperture-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/paloaltonetworks-aperture-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-palo-alto-networks---aperture-test-user"></a>Utwórz Palo Alto Networks - przysłony użytkownika testowego

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w Palo Alto Networks - przysłony. Praca z [Palo Alto Networks - zespół obsługi klienta przysłony](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) Aby dodać użytkowników w Palo Alto Networks - przysłony platformy. Użytkownicy muszą być tworzone i aktywowana, aby używać logowania jednokrotnego. 

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do Palo Alto Networks - przysłony.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon Palo Alto Networks - przysłony, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **Palo Alto Networks - przysłony**.

    ![Sieci Palo Alto - przysłony łącze na liście aplikacji](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu Palo Alto Networks - przysłony kafelka w panelu dostępu, możesz powinien pobrać automatycznie zalogowanych do Twojej Palo Alto Networks - przysłony aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_01.png
[2]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_02.png
[3]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_03.png
[4]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_04.png

[100]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_100.png

[200]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_200.png
[201]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_201.png
[202]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_202.png
[203]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_203.png

