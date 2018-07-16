---
title: 'Samouczek: Integracja usługi Azure Active Directory z CompetencyIQ | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i CompetencyIQ.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: e262bf7e-cc7d-4d0e-aea7-861f00d8837d
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2017
ms.author: jeedes
ms.openlocfilehash: da78c569d8a8df576f472c8b6dfb9185f0e65e5e
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39048025"
---
# <a name="tutorial-azure-active-directory-integration-with-competencyiq"></a>Samouczek: Integracja usługi Azure Active Directory z CompetencyIQ

W tym samouczku dowiesz się, jak zintegrować CompetencyIQ w usłudze Azure Active Directory (Azure AD).

Integrowanie CompetencyIQ z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do CompetencyIQ
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do CompetencyIQ (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą CompetencyIQ, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- CompetencyIQ logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie CompetencyIQ z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-competencyiq-from-the-gallery"></a>Dodawanie CompetencyIQ z galerii
Aby skonfigurować integrację CompetencyIQ w usłudze Azure AD, należy dodać CompetencyIQ z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać CompetencyIQ z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **CompetencyIQ**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/competencyiq-tutorial/tutorial_competencyiq_search.png)

5. W panelu wyników wybierz **CompetencyIQ**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/competencyiq-tutorial/tutorial_competencyiq_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą CompetencyIQ w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w CompetencyIQ do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w CompetencyIQ musi można ustanowić.

W CompetencyIQ, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą CompetencyIQ, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego CompetencyIQ](#creating-a-competencyiq-test-user)**  — aby odpowiednikiem Britta Simon w CompetencyIQ połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji CompetencyIQ.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z CompetencyIQ, wykonaj następujące czynności:**

1. W witrynie Azure portal na **CompetencyIQ** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/competencyiq-tutorial/tutorial_competencyiq_samlbase.png)

3. Na **CompetencyIQ domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/competencyiq-tutorial/tutorial_competencyiq_url1.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<customer>.competencyiq.com/`
    
    b. W **identyfikator** pole tekstowe, wpisz adres URL: `https://www.competencyiq.com/`

    > [!NOTE] 
    > Wartość adres URL logowania jest nie rzeczywiste tak zaktualizować to o rzeczywisty adres URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta CompetencyIQ](https://www.competencyiq.com/) celu rozwiązania tego. 
 
4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/competencyiq-tutorial/tutorial_competencyiq_certificate.png) 

5. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/competencyiq-tutorial/tutorial_general_400.png)

6. Na **konfiguracji CompetencyIQ** , kliknij przycisk **skonfigurować CompetencyIQ** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML identyfikator jednostki**, i **SAML pojedynczego logowania jednokrotnego adres URL usługi** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/competencyiq-tutorial/tutorial_competencyiq_configure.png) 

7. Aby skonfigurować logowanie jednokrotne na **CompetencyIQ** stronie, musisz wysłać pobrany **XML metadanych**, **identyfikator jednostki SAML** i **SAML pojedynczej usługi logowania jednokrotnego Adres URL** do [zespołem pomocy technicznej CompetencyIQ](https://www.competencyiq.com/). Ustawiają to ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/competencyiq-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/competencyiq-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/competencyiq-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/competencyiq-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-competencyiq-test-user"></a>Tworzenie użytkownika testowego CompetencyIQ

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do CompetencyIQ, musi być obsługiwana w CompetencyIQ. Skontaktuj się z pomocą [zespołem pomocy technicznej CompetencyIQ](https://www.competencyiq.com/) do tworzenia użytkowników w aplikacji.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do CompetencyIQ.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon CompetencyIQ, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **CompetencyIQ**.

    ![Konfigurowanie logowania jednokrotnego](./media/competencyiq-tutorial/tutorial_competencyiq_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka CompetencyIQ w panelu dostępu, należy pobrać automatycznie zalogowano się do aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/competencyiq-tutorial/tutorial_general_01.png
[2]: ./media/competencyiq-tutorial/tutorial_general_02.png
[3]: ./media/competencyiq-tutorial/tutorial_general_03.png
[4]: ./media/competencyiq-tutorial/tutorial_general_04.png

[100]: ./media/competencyiq-tutorial/tutorial_general_100.png

[200]: ./media/competencyiq-tutorial/tutorial_general_200.png
[201]: ./media/competencyiq-tutorial/tutorial_general_201.png
[202]: ./media/competencyiq-tutorial/tutorial_general_202.png
[203]: ./media/competencyiq-tutorial/tutorial_general_203.png

