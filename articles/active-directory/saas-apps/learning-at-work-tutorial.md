---
title: 'Samouczek: Integracja usługi Azure Active Directory przy użyciu uczenia w miejscu pracy | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i szkolenia w miejscu pracy.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 1d607174-bea1-4f40-8233-54cabe02c66a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/02/2017
ms.author: jeedes
ms.openlocfilehash: b7a097107f68c692c960efc465d00b05e81f7c81
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39046859"
---
# <a name="tutorial-azure-active-directory-integration-with-learning-at-work"></a>Samouczek: Integracja usługi Azure Active Directory przy użyciu uczenia w miejscu pracy

W tym samouczku dowiesz się, jak zintegrować uczenia w pracy z usługą Azure Active Directory (Azure AD).

Integrowanie uczenia w pracy z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do uczenia w miejscu pracy
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do uczenia w miejscu pracy (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu uczenia w miejscu pracy, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Nauka w pracy logowania jednokrotnego włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie uczenia w miejscu pracy za pomocą galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-learning-at-work-from-the-gallery"></a>Dodawanie uczenia w miejscu pracy za pomocą galerii
Aby skonfigurować integrację uczenia w miejscu pracy w usłudze Azure AD, należy dodać uczenia w pracy z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać uczenia w pracy z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **uczenia w miejscu pracy**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/learning-at-work-tutorial/tutorial_learningatwork_search.png)

5. W panelu wyników wybierz **uczenia w miejscu pracy**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/learning-at-work-tutorial/tutorial_learningatwork_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i przetestować usługi Azure AD logowanie jednokrotne za pomocą uczenia w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w uczenia w miejscu pracy jest dla użytkownika, w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w usłudze Learning w miejscu pracy musi zostać nawiązane.

Ustanowieniu tej relacji łączy, przypisując wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** Learning w miejscu pracy.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu uczenia w miejscu pracy, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie uczenie na pracę użytkownika testowego](#creating-a-learning-at-work-test-user)**  — aby odpowiednikiem Britta Simon w uczenia w pracy, która jest połączona z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i skonfigurować logowanie jednokrotne w zdobytą wiedzę w pracy aplikacji.

**Aby skonfigurować usługę Azure AD logowania jednokrotnego przy użyciu uczenia w miejscu pracy, wykonaj następujące czynności:**

1. W witrynie Azure portal na **uczenia w miejscu pracy** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/learning-at-work-tutorial/tutorial_learningatwork_samlbase.png)

3. Na **uczenie się na pracy, domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/learning-at-work-tutorial/tutorial_learningatwork_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<subdomain>.sabacloud.com/Saba/Web/<company code>`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<subdomain>.sabacloud.com/Saba/saml/SSO/alias/<company name>`

    > [!NOTE] 
    > Te wartości nie są rzeczywiste. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania jednokrotnego i identyfikator. Skontaktuj się z pomocą [uczenia się u klienta pracy zespołu pomocy technicznej](https://www.learninga-z.com/site/contact/support) do uzyskania tych wartości. 
 
4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/learning-at-work-tutorial/tutorial_learningatwork_certificate.png) 

5. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/learning-at-work-tutorial/tutorial_general_400.png)

6. Na **uczenia w konfiguracji roboczego** , kliknij przycisk **skonfigurować uczenia w miejscu pracy** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/learning-at-work-tutorial/tutorial_learningatwork_configure.png) 

7. Aby skonfigurować logowanie jednokrotne na **uczenia w miejscu pracy** stronie, musisz wysłać pobrany **XML metadanych**, **identyfikator jednostki SAML**, **SAML pojedynczej usługi logowania jednokrotnego Adres URL**, i **adres URL wylogowania** do [uczenia w witrynie pomocy technicznej pracy](https://www.learninga-z.com/site/contact/support).

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/learning-at-work-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/learning-at-work-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/learning-at-work-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/learning-at-work-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-learning-at-work-test-user"></a>Tworzenie uczenie na pracę użytkownika testowego

W tej sekcji utworzysz użytkownika o nazwie Britta Simon Learning w miejscu pracy. Praca z [uczenia w witrynie pomocy technicznej pracy](https://www.learninga-z.com/site/contact/support) Aby dodać użytkowników w usłudze Learning roboczych platformy.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do uczenia w miejscu pracy.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon uczenia w miejscu pracy, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **uczenia w miejscu pracy**.

    ![Konfigurowanie logowania jednokrotnego](./media/learning-at-work-tutorial/tutorial_learningatwork_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu uczenia w kafelku pracy w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do zdobytą wiedzę w pracy aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/learning-at-work-tutorial/tutorial_general_01.png
[2]: ./media/learning-at-work-tutorial/tutorial_general_02.png
[3]: ./media/learning-at-work-tutorial/tutorial_general_03.png
[4]: ./media/learning-at-work-tutorial/tutorial_general_04.png

[100]: ./media/learning-at-work-tutorial/tutorial_general_100.png

[200]: ./media/learning-at-work-tutorial/tutorial_general_200.png
[201]: ./media/learning-at-work-tutorial/tutorial_general_201.png
[202]: ./media/learning-at-work-tutorial/tutorial_general_202.png
[203]: ./media/learning-at-work-tutorial/tutorial_general_203.png

