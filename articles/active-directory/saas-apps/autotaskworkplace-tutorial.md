---
title: 'Samouczek: Integracja usługi Azure Active Directory z miejsca pracy Autotask | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Autotask w miejscu pracy.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: a9a7ff71-c389-4169-aafd-d7a505244797
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: 8e83e6dc66ec6192db589a0964e235c395a26eba
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39048110"
---
# <a name="tutorial-azure-active-directory-integration-with-autotask-workplace"></a>Samouczek: Integracja usługi Azure Active Directory z miejsca pracy Autotask

W tym samouczku dowiesz się, jak zintegrować Autotask miejsca pracy z usługą Azure Active Directory (Azure AD).

Integrowanie Autotask miejsca pracy z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do miejsca pracy Autotask
- Użytkowników, aby automatycznie uzyskać zalogowane Autotask w miejscu pracy (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z miejsca pracy Autotask, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Obszar roboczy Autotask logowania jednokrotnego włączonych subskrypcji
- Musi być administratorem lub administratorem super w miejscu pracy.
- Musi mieć konto administratora w usłudze Azure AD.
- Użytkownicy, którzy będą korzystać z tej funkcji muszą mieć konta w miejscu pracy i Azure AD, a ich adresy e-mail, oba muszą być zgodne.

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Autotask w miejscu pracy za pomocą galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-autotask-workplace-from-the-gallery"></a>Dodawanie Autotask w miejscu pracy za pomocą galerii
Aby skonfigurować integrację Autotask roboczy w usłudze Azure AD, należy dodać Autotask w miejscu pracy za pomocą galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać miejsce pracy Autotask z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Autotask w miejscu pracy**, wybierz opcję **Autotask w miejscu pracy** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Autotask pracy na liście wyników](./media/autotaskworkplace-tutorial/tutorial_autotaskworkplace_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji skonfigurujesz a test usługi Azure AD logowanie jednokrotne z miejsca pracy Autotask oparte na użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w miejscu pracy Autotask do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w miejscu pracy Autotask musi zostać ustanowione.

W obszarze roboczym Autotask, przypisz wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Autotask w miejscu pracy, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego pracy Autotask](#create-an-autotask-workplace-test-user)**  — aby odpowiednikiem Britta Simon w miejscu pracy Autotask, połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Autotask w miejscu pracy.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Autotask w miejscu pracy, wykonaj następujące czynności:**

1. W witrynie Azure portal na **pracy Autotask** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/autotaskworkplace-tutorial/tutorial_autotaskworkplace_samlbase.png)

3. Jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb, wykonaj następujące czynności na **Autotask pracy domena i adresy URL** sekcji:

    ![Obszar roboczy Autotask domena i adresy URL pojedynczego logowania jednokrotnego informacji dla dostawcy tożsamości](./media/autotaskworkplace-tutorial/tutorial_autotaskworkplace_url.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<subdomain>.awp.autotask.net/singlesignon/saml/metadata`

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<subdomain>.awp.autotask.net/singlesignon/saml/SSO`

4. Jeśli chcesz skonfigurować aplikację w **SP** inicjowane trybu wyboru **Pokaż zaawansowane ustawienia adresu URL** i wykonaj następujące czynności:

    ![Obszar roboczy Autotask domena i adresy URL pojedynczego logowania jednokrotnego informacje dotyczące SP](./media/autotaskworkplace-tutorial/tutorial_autotaskworkplace_url1.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<subdomain>.awp.autotask.net/loginsso`
     
    > [!NOTE] 
    > Te wartości są prawdziwe. Rzeczywisty identyfikator, adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta do miejsca pracy Autotask](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) do uzyskania tych wartości. 

5. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/autotaskworkplace-tutorial/tutorial_autotaskworkplace_certificate.png) 

6. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/autotaskworkplace-tutorial/tutorial_general_400.png)

7. W oknie przeglądarki internetowej innej Zaloguj się do miejsca pracy w trybie Online przy użyciu poświadczeń administratora.

    >[!Note]
    >Podczas konfigurowania tożsamości, poddomeny należy określić. Aby potwierdzić poprawny poddomeny, zaloguj się do miejsca pracy w trybie Online. Po zalogowaniu, zwróć uwagę na domenę podrzędną w adresie URL.
    >Poddomeny jest częścią od "https://" i ".awp.autotask.net/" i powinna być USA, Europa, urzędu certyfikacji lub korzystania z jednostki analizy.

8. Przejdź do **konfiguracji** > **logowania jednokrotnego** i wykonaj następujące czynności:

    ![Jednym Autotask konfiguracji logowania jednokrotnego](./media/autotaskworkplace-tutorial/tutorial_autotaskssoconfig1.png)
 
    a. Wybierz **pliku metadanych XML** opcji, a następnie przekaż **XML metadanych** pobranego z witryny Azure portal.

    b. Kliknij przycisk **włączenia funkcji logowania jednokrotnego**.
    
    ![Autotask jednokrotnej Zatwierdzanie konfiguracji](./media/autotaskworkplace-tutorial/tutorial_autotaskssoconfig2.png)

    c. Wybierz **potwierdzam, że te informacje są poprawne mam nadzieję, tego dostawcy tożsamości** pole wyboru.

    d. Kliknij przycisk **zatwierdzić**.
     
>[!Note]
>Jeśli potrzebujesz pomocy w konfigurowaniu Autotask w miejscu pracy, zobacz [na tej stronie](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) Aby uzyskać pomoc przy użyciu konta firmowego.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/autotaskworkplace-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/autotaskworkplace-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/autotaskworkplace-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/autotaskworkplace-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.

### <a name="create-an-autotask-workplace-test-user"></a>Tworzenie użytkownika testowego Autotask w miejscu pracy

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w Autotask. Skontaktuj się z [zespołem pomocy technicznej w miejscu pracy Autotask](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) Aby dodać użytkowników na platformie Autotask w miejscu pracy.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do udzielania dostępu do miejsca pracy Autotask za pomocą platformy Azure logowania jednokrotnego.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon Autotask w miejscu pracy, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **pracy Autotask**.

    ![Łącze Autotask w miejscu pracy na liście aplikacji](./media/autotaskworkplace-tutorial/tutorial_autotaskworkplace_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Autotask w miejscu pracy, w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do Autotask obszar roboczy aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/autotaskworkplace-tutorial/tutorial_general_01.png
[2]: ./media/autotaskworkplace-tutorial/tutorial_general_02.png
[3]: ./media/autotaskworkplace-tutorial/tutorial_general_03.png
[4]: ./media/autotaskworkplace-tutorial/tutorial_general_04.png

[100]: ./media/autotaskworkplace-tutorial/tutorial_general_100.png

[200]: ./media/autotaskworkplace-tutorial/tutorial_general_200.png
[201]: ./media/autotaskworkplace-tutorial/tutorial_general_201.png
[202]: ./media/autotaskworkplace-tutorial/tutorial_general_202.png
[203]: ./media/autotaskworkplace-tutorial/tutorial_general_203.png

