---
title: 'Samouczek: Integracja usługi Azure Active Directory z Getabstract | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Getabstract.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 2b63d048-b529-4fad-9e90-f244323409dd
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: jeedes
ms.openlocfilehash: 78c622dca89789c123e714d6101c9b1397182602
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39047131"
---
# <a name="tutorial-azure-active-directory-integration-with-getabstract"></a>Samouczek: Integracja usługi Azure Active Directory z Getabstract

W tym samouczku dowiesz się, jak zintegrować Getabstract w usłudze Azure Active Directory (Azure AD).

Integrowanie Getabstract z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Getabstract.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do Getabstract (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Getabstract, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Getabstract logowania jednokrotnego włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Getabstract z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-getabstract-from-the-gallery"></a>Dodawanie Getabstract z galerii
Aby skonfigurować integrację Getabstract w usłudze Azure AD, należy dodać Getabstract z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Getabstract z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Getabstract**, wybierz opcję **Getabstract** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Getabstract na liście wyników](./media/getabstract-tutorial/tutorial_getabstract_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Getabstract w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Getabstract do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Getabstract musi można ustanowić.

W Getabstract, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Getabstract, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego Getabstract](#create-a-getabstract-test-user)**  — aby odpowiednikiem Britta Simon w Getabstract połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Getabstract.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Getabstract, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Getabstract** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/getabstract-tutorial/tutorial_getabstract_samlbase.png)

3. Na **Getabstract domena i adresy URL** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![Getabstract domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/getabstract-tutorial/tutorial_getabstract_url.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL:

    Na etapie/pre_production: `https://int.getabstract.com`

    W środowisku produkcyjnym: `https://www.getabstract.com`

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL:
    
    Na etapie/pre_production: `https://int.getabstract.com/ACS.do`
    
    W środowisku produkcyjnym: `https://www.getabstract.com/ACS.do`

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![Getabstract domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/getabstract-tutorial/tutorial_getabstract_url1.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca:
    
    Na etapie/pre_production: `https://int.getabstract.com/portal/<org_username>`
    
    W środowisku produkcyjnym: `https://www.getabstract.com/portal/<org_username>`

    > [!NOTE] 
    > Ta wartość nie jest prawdziwe. Zaktualizuj tę wartość przy użyciu rzeczywisty adres URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Getabstract](https://www.getabstract.com/en/contact) aby zyskać tę wartość.

5. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/getabstract-tutorial/tutorial_getabstract_certificate.png) 

6. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/getabstract-tutorial/tutorial_general_400.png)
    
7. Aby skonfigurować logowanie jednokrotne na **Getabstract** stronie, musisz wysłać pobrany **XML metadanych** do [zespołem pomocy technicznej Getabstract](https://www.getabstract.com/en/contact). Ustawiają to ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/getabstract-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/getabstract-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/getabstract-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/getabstract-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-getabstract-test-user"></a>Tworzenie użytkownika testowego Getabstract

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon w Getabstract. Getabstract obsługę just-in-time, który jest domyślnie włączona. Brak elementu akcji dla Ciebie w tej sekcji. Nowy użytkownik jest tworzony podczas próby dostępu Getabstract, jeśli go jeszcze nie istnieje.
>[!Note]
>Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [Getabstract zespołem pomocy technicznej](https://www.getabstract.com/en/contact)


### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Getabstract.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon Getabstract, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **Getabstract**.

    ![Link Getabstract na liście aplikacji](./media/getabstract-tutorial/tutorial_getabstract_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Getabstract w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Getabstract.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/getabstract-tutorial/tutorial_general_01.png
[2]: ./media/getabstract-tutorial/tutorial_general_02.png
[3]: ./media/getabstract-tutorial/tutorial_general_03.png
[4]: ./media/getabstract-tutorial/tutorial_general_04.png

[100]: ./media/getabstract-tutorial/tutorial_general_100.png

[200]: ./media/getabstract-tutorial/tutorial_general_200.png
[201]: ./media/getabstract-tutorial/tutorial_general_201.png
[202]: ./media/getabstract-tutorial/tutorial_general_202.png
[203]: ./media/getabstract-tutorial/tutorial_general_203.png

