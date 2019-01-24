---
title: 'Samouczek: Integracja usługi Azure Active Directory z miejsca pracy Soonr | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Soonr w miejscu pracy.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: b75f5f00-ea8b-4850-ae2e-134e5d678d97
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: jeedes
ms.openlocfilehash: 7dd02aee83d42421cdda57f4feb54dde790db68c
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54809160"
---
# <a name="tutorial-azure-active-directory-integration-with-soonr-workplace"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Soonr w miejscu pracy

W tym samouczku dowiesz się, jak zintegrować Soonr miejsca pracy z usługą Azure Active Directory (Azure AD).

Integrowanie Soonr miejsca pracy z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do miejsca pracy Soonr
- Użytkowników, aby automatycznie uzyskać zalogowane Soonr w miejscu pracy (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z miejsca pracy Soonr, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Obszar roboczy Soonr logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Soonr w miejscu pracy za pomocą galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-soonr-workplace-from-the-gallery"></a>Dodawanie Soonr w miejscu pracy za pomocą galerii
Aby skonfigurować integrację Soonr roboczy w usłudze Azure AD, należy dodać Soonr w miejscu pracy za pomocą galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać miejsce pracy Soonr z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **pracy Soonr**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/soonr-tutorial/tutorial_soonr_search.png)

1. W panelu wyników wybierz **pracy Soonr**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/soonr-tutorial/tutorial_soonr_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji można skonfigurować, i test usługi Azure AD logowanie jednokrotne z miejsca pracy Soonr oparte na użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w miejscu pracy Soonr do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w miejscu pracy Soonr musi zostać ustanowione.

W obszarze roboczym Soonr, przypisz wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Soonr w miejscu pracy, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego pracy Soonr](#creating-a-soonr-workplace-test-user)**  — aby odpowiednikiem Britta Simon w miejscu pracy Soonr, połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Soonr w miejscu pracy.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Soonr w miejscu pracy, wykonaj następujące czynności:**

1. W witrynie Azure portal na **pracy Soonr** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/soonr-tutorial/tutorial_soonr_samlbase.png)

1. Na **Soonr pracy domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/soonr-tutorial/tutorial_soonr_url.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://<servername>.soonr.com/singlesignon/saml/metadata`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<servername>.soonr.com/singlesignon/saml/SSO`

1. Na **Soonr pracy domena i adresy URL** sekcji, jeśli chcesz skonfigurować aplikację w **SP zainicjowano tryb**, wykonaj następujące czynności:
    
    ![Konfigurowanie logowania jednokrotnego](./media/soonr-tutorial/tutorial_soonr_url1.png)

    a. Kliknij pozycję **Pokaż zaawansowane ustawienia adresu URL**.

    b. W **na adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<servername>.soonr.com/singlesignon/saml/SSO`

    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywisty identyfikator logowania adresu URL i adresu URL odpowiedzi. Skontaktuj się z pomocą [zespołem pomocy technicznej w miejscu pracy Soonr](https://awp.autotask.net/help/) do uzyskania tych wartości.
 
1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/soonr-tutorial/tutorial_soonr_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/soonr-tutorial/tutorial_general_400.png)

1. Na **konfiguracji obszaru roboczego Soonr** , kliknij przycisk **skonfigurować obszar roboczy Soonr** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/soonr-tutorial/tutorial_soonr_configure.png) 

1. Aby skonfigurować logowanie jednokrotne na **Soonr w miejscu pracy** stronie, musisz wysłać pobrany **XML metadanych**, **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** Aby [zespołem pomocy technicznej w miejscu pracy Soonr](https://awp.autotask.net/help/). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

    >[!Note]
    >Jeśli potrzebujesz pomocy w konfigurowaniu Autotask w miejscu pracy, zobacz [na tej stronie](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) Aby uzyskać pomoc przy użyciu konta firmowego.

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/soonr-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/soonr-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/soonr-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/soonr-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-soonr-workplace-test-user"></a>Tworzenie użytkownika testowego Soonr w miejscu pracy

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon w miejscu pracy Soonr. Praca z [zespołem pomocy technicznej w miejscu pracy Soonr](https://awp.autotask.net/help/) do tworzenia użytkownika na platformie. Może zgłosić bilet pomocy technicznej za pomocą Soonr z <a href="https://na01.safelinks.protection.outlook.com/?url=http%3A%2F%2Fsoonr.com%2FAWPHelp%2FContent%2F0_HOME%2FSupport_for_End_Clients.htm&data=01%7C01%7Cv-saikra%40microsoft.com%7Ccbb4367ab09b4dacaac408d3eebe3f42%7C72f988bf86f141af91ab2d7cd011db47%7C1&sdata=FB92qtE6m%2Fd8yox7AnL2f1h%2FGXwSkma9x9H8Pz0955M%3D&reserved=0/">tutaj</a>.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do udzielania dostępu do miejsca pracy Soonr za pomocą platformy Azure logowania jednokrotnego.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Soonr w miejscu pracy, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **pracy Soonr**.

    ![Konfigurowanie logowania jednokrotnego](./media/soonr-tutorial/tutorial_soonr_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.  

Po kliknięciu kafelka Soonr w miejscu pracy, w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do Soonr obszar roboczy aplikacji.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/soonr-tutorial/tutorial_general_01.png
[2]: ./media/soonr-tutorial/tutorial_general_02.png
[3]: ./media/soonr-tutorial/tutorial_general_03.png
[4]: ./media/soonr-tutorial/tutorial_general_04.png

[100]: ./media/soonr-tutorial/tutorial_general_100.png

[200]: ./media/soonr-tutorial/tutorial_general_200.png
[201]: ./media/soonr-tutorial/tutorial_general_201.png
[202]: ./media/soonr-tutorial/tutorial_general_202.png
[203]: ./media/soonr-tutorial/tutorial_general_203.png

