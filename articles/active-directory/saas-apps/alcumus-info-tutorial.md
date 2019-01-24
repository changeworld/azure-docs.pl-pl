---
title: 'Samouczek: Integracja usługi Azure Active Directory z programem Exchange informacje Alcumus | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Alcumus informacji z programu Exchange.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: d26034b8-f0d5-4f65-aa56-0fc168ceec8c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2017
ms.author: jeedes
ms.openlocfilehash: d55aa873d0ad410b91dc678d1fe4784e00ed5487
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54812475"
---
# <a name="tutorial-azure-active-directory-integration-with-alcumus-info-exchange"></a>Samouczek: Integracja usługi Azure Active Directory z programem Exchange informacje Alcumus

W tym samouczku dowiesz się, jak zintegrować Alcumus informacji z programu Exchange z usługą Azure Active Directory (Azure AD).

Integrowanie Alcumus informacji z programu Exchange z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do wymiany informacji o Alcumus
- Użytkowników, aby automatycznie uzyskać zalogowanych do wymiany informacji Alcumus (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z programem Exchange informacje Alcumus, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Logowania jednokrotnego wymiany informacji Alcumus włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie wymiany informacji Alcumus z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-alcumus-info-exchange-from-the-gallery"></a>Dodawanie wymiany informacji Alcumus z galerii
Aby skonfigurować integrację programu Exchange informacje Alcumus w usłudze Azure AD, należy dodać wymiany informacji Alcumus z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać wymiany informacji Alcumus z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **wymiany informacji Alcumus**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/alcumus-info-tutorial/tutorial_alcumusinfoexchange_search.png)

5. W panelu wyników wybierz **wymiany informacji Alcumus**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/alcumus-info-tutorial/tutorial_alcumusinfoexchange_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą wymiany informacji Alcumus oparte na użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w programie Exchange informacje Alcumus do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w programie Exchange informacje Alcumus musi nawiązać.

W programie Exchange informacje Alcumus, przypisz wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z programem Exchange informacje Alcumus, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego wymiany informacji Alcumus](#creating-an-alcumus-info-exchange-test-user)**  — aby odpowiednikiem Britta Simon w wymiany informacji Alcumus, połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Exchange informacje Alcumus.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z programem Exchange informacje Alcumus, wykonaj następujące czynności:**

1. W witrynie Azure portal na **wymiany informacji Alcumus** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/alcumus-info-tutorial/tutorial_alcumusinfoexchange_samlbase.png)

3. Na **Alcumus informacji wymiany domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/alcumus-info-tutorial/tutorial_alcumusinfoexchange_url.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.info-exchange.com`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.info-exchange.com/Auth/`

    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Skontaktuj się z pomocą [zespół pomocy technicznej wymiany informacji Alcumus](mailto:helpdesk@alcumusgroup.com) do uzyskania tych wartości.
 
4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/alcumus-info-tutorial/tutorial_alcumusinfoexchange_certificate.png) 

5. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/alcumus-info-tutorial/tutorial_general_400.png)

6. Aby skonfigurować logowanie jednokrotne na **wymiany informacji Alcumus** stronie, musisz wysłać pobrany **XML metadanych** do [wymiany informacji Alcumus zespołem pomocy technicznej](mailto:helpdesk@alcumusgroup.com).

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/alcumus-info-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/alcumus-info-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/alcumus-info-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/alcumus-info-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-an-alcumus-info-exchange-test-user"></a>Tworzenie użytkownika testowego Alcumus informacji z programu Exchange

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon Alcumus informacje o wymianie.

Aby utworzyć użytkownika o nazwie Britta Simon w programie Exchange informacje Alcumus, skontaktuj się z pomocą [zespół pomocy technicznej wymiany informacji Alcumus](mailto:helpdesk@alcumusgroup.com).

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do wymiany informacji o Alcumus.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon do wymiany informacji o Alcumus, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **wymiany informacji Alcumus**.

    ![Konfigurowanie logowania jednokrotnego](./media/alcumus-info-tutorial/tutorial_alcumusinfoexchange_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.  
Po kliknięciu kafelka wymiany informacji Alcumus w panelu dostępu, możesz powinien pobrać automatycznie zalogowanych do wymiany informacji Alcumus aplikacji.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/alcumus-info-tutorial/tutorial_general_01.png
[2]: ./media/alcumus-info-tutorial/tutorial_general_02.png
[3]: ./media/alcumus-info-tutorial/tutorial_general_03.png
[4]: ./media/alcumus-info-tutorial/tutorial_general_04.png

[100]: ./media/alcumus-info-tutorial/tutorial_general_100.png

[200]: ./media/alcumus-info-tutorial/tutorial_general_200.png
[201]: ./media/alcumus-info-tutorial/tutorial_general_201.png
[202]: ./media/alcumus-info-tutorial/tutorial_general_202.png
[203]: ./media/alcumus-info-tutorial/tutorial_general_203.png

