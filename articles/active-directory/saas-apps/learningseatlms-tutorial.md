---
title: 'Samouczek: Integracja usługi Azure Active Directory z LMS stanowisko Learning | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i edukacją LMS, Learning stanowisk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: bb056fcf-4135-478e-85b1-5015d1f07b85
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: jeedes
ms.openlocfilehash: 1043c8f7468fd7775ff1e38d12a3dce0b379c915
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39443278"
---
# <a name="tutorial-azure-active-directory-integration-with-learning-seat-lms"></a>Samouczek: Integracja usługi Azure Active Directory z LMS stanowisko Learning

W tym samouczku dowiesz się, jak zintegrować LMS stanowisko Learning z usługą Azure Active Directory (Azure AD).

Integrowanie LMS stanowisko Learning z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do LMS stanowisko Learning
- Użytkowników, aby automatycznie uzyskać zalogowanych do nauki stanowisko LMS (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz. [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Do konfigurowania integracji z usługą Azure AD z edukacją LMS, Learning stanowisko, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- LMS stanowisko Learning logowania jednokrotnego włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie LMS stanowisko Learning w galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-learning-seat-lms-from-the-gallery"></a>Dodawanie LMS stanowisko Learning w galerii
Aby skonfigurować integrację LMS stanowisko Learning w usłudze Azure AD, należy dodać LMS stanowisko Learning w galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać LMS stanowisko Learning w galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **LMS stanowisko Learning**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/learningseatlms-tutorial/tutorial_learnconnect_search.png)

1. W panelu wyników wybierz **LMS stanowisko Learning**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą LMS stanowisko Learning oparte na użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w LMS stanowisko Learning do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w LMS stanowisko Learning musi nawiązać.

Ustanowieniu tej relacji łączy, przypisując wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** w LMS stanowisko Learning.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą LMS stanowisko Learning, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego LMS stanowisko Learning](#creating-a-learnconnect-test-user)**  — aby odpowiednikiem Britta Simon w stanowisko edukacją LMS, Learning połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji LMS stanowisko Learning.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z LMS stanowisko Learning, wykonaj następujące czynności:**

1. W witrynie Azure portal na **LMS stanowisko Learning** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/learningseatlms-tutorial/tutorial_learnconnect_samlbase.png)

1. Na **Learning stanowisko LMS domena i adresy URL** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![Konfigurowanie logowania jednokrotnego](./media/learningseatlms-tutorial/tutorial_learnconnect_url.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<subdomain>.learningseatlms.com`

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<subdomain>.learningseatlms.com/Account/AssertionConsumerService`

1. Sprawdź **Pokaż zaawansowane ustawienia adresu URL**, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![Konfigurowanie logowania jednokrotnego](./media/learningseatlms-tutorial/tutorial_learnconnect_url2.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<subdomain>.learningseatlms.com`
     
    > [!NOTE] 
    > Te wartości nie są rzeczywiste wartości. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora, adres URL odpowiedzi i adres URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej stanowisko Learning](http://help.learningseatlms.com/help) do uzyskania tych wartości. 

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/learningseatlms-tutorial/tutorial_learnconnect_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/learningseatlms-tutorial/tutorial_general_400.png)

1. Aby skonfigurować logowanie jednokrotne na **LMS stanowisko Learning** stronie, musisz wysłać pobrany **XML metadanych** do [zespołem pomocy technicznej stanowisko Learning](http://help.learningseatlms.com/help).

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD](https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/learningseatlms-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/learningseatlms-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/learningseatlms-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/learningseatlms-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-learning-seat-lms-test-user"></a>Tworzenie użytkownika testowego LMS stanowisko Learning

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w LMS stanowisko Learning. Skontaktuj się z pomocą [zespołem pomocy technicznej stanowisko Learning](http://help.learningseatlms.com/help) wszystkie informacje użytkownika, aby dodać użytkowników w aplikacji LMS stanowisko Learning.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do uczenia stanowisko LMS.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon LMS stanowisko Learning, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **LMS stanowisko Learning**.

    ![Konfigurowanie logowania jednokrotnego](./media/learningseatlms-tutorial/tutorial_learnconnect_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu. 

Kliknij Kafelek edukacją LMS, Learning stanowisko w warstwie w panelu dostępu, można będzie można automatycznie zalogowanych do edukacją LMS, Learning stanowisko w warstwie aplikacji. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/learnconnect-tutorial/tutorial_general_01.png
[2]: ./media/learnconnect-tutorial/tutorial_general_02.png
[3]: ./media/learnconnect-tutorial/tutorial_general_03.png
[4]: ./media/learnconnect-tutorial/tutorial_general_04.png

[100]: ./media/learnconnect-tutorial/tutorial_general_100.png

[200]: ./media/learnconnect-tutorial/tutorial_general_200.png
[201]: ./media/learnconnect-tutorial/tutorial_general_201.png
[202]: ./media/learnconnect-tutorial/tutorial_general_202.png
[203]: ./media/learnconnect-tutorial/tutorial_general_203.png

