---
title: 'Samouczek: Integracja usługi Azure Active Directory z oprogramowaniem SAP NetWeaver | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i oprogramowania SAP NetWeaver.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 1b9e59e3-e7ae-4e74-b16c-8c1a7ccfdef3
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: jeedes
ms.openlocfilehash: b773380a21e0c47a1e1519e592aa0ddd5e6388fa
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39421954"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-netweaver"></a>Samouczek: integracja usługi Azure Active Directory z oprogramowaniem SAP NetWeaver

W tym samouczku dowiesz się, jak zintegrować oprogramowanie SAP NetWeaver przy użyciu usługi Azure Active Directory (Azure AD).

Integracja oprogramowania SAP NetWeaver z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do oprogramowania SAP NetWeaver
- Użytkowników, aby automatycznie uzyskać zalogowanych do oprogramowania SAP NetWeaver (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).


## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z oprogramowaniem SAP NetWeaver, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Oprogramowanie SAP NetWeaver logowania jednokrotnego włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie oprogramowania SAP NetWeaver z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-sap-netweaver-from-the-gallery"></a>Dodawanie oprogramowania SAP NetWeaver z galerii
Aby skonfigurować integrację z oprogramowaniem SAP NetWeaver w usłudze Azure AD, należy dodać oprogramowanie SAP NetWeaver z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać oprogramowanie SAP NetWeaver w galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure Portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **oprogramowanie SAP NetWeaver**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sap-netweaver-tutorial/tutorial_sapnetweaver_search.png)

1. W panelu wyników wybierz **oprogramowanie SAP NetWeaver**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sap-netweaver-tutorial/tutorial_sapnetweaver_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i przetestować usługi Azure AD logowanie jednokrotne z oprogramowaniem SAP NetWeaver w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w oprogramowanie SAP NetWeaver do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkownika na oprogramowanie SAP NetWeaver musi zostać ustanowione.

Ustanowieniu tej relacji łączy, przypisując wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** w oprogramowanie SAP NetWeaver.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z oprogramowaniem SAP NetWeaver, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego środowiska SAP NetWeaver](#creating-an-sap-netweaver-test-user)**  — aby odpowiednikiem Britta Simon w oprogramowanie SAP NetWeaver, połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji SAP NetWeaver.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z oprogramowaniem SAP NetWeaver, wykonaj następujące czynności:**

1. W witrynie Azure portal na **oprogramowanie SAP NetWeaver** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/sap-netweaver-tutorial/tutorial_sapnetweaver_samlbase.png)

1. Na **SAP NetWeaver domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/sap-netweaver-tutorial/tutorial_sapnetweaver_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<your company instance of SAP NetWeaver>`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<your company instance of SAP NetWeaver>`

    c. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<your company instance of SAP NetWeaver>/sap/saml2/sp/acs/100`
     
    > [!NOTE] 
    > Te wartości nie są rzeczywiste. Rzeczywisty identyfikator i adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. W tym miejscu zalecamy przy użyciu unikatowej wartości ciągu w identyfikatorze. Skontaktuj się z pomocą [zespołem pomocy technicznej SAP NetWeaver klienta](https://www.sap.com/support.html) do uzyskania tych wartości. 

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik XML na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/sap-netweaver-tutorial/tutorial_sapnetweaver_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/sap-netweaver-tutorial/tutorial_general_400.png)
    
1. Na **SAP NetWeaver konfiguracji** kliknij **skonfigurować oprogramowanie SAP NetWeaver** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki SAML** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/sap-netweaver-tutorial/tutorial_sapnetweaver_configure.png) 

1. Aby skonfigurować logowanie jednokrotne na **oprogramowanie SAP NetWeaver** stronie, musisz wysłać pobrany **XML metadanych** i **identyfikator jednostki SAML** do [pomocy technicznej SAP NetWeaver](https://www.sap.com/support.html). 

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sap-netweaver-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sap-netweaver-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sap-netweaver-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sap-netweaver-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **Britta Simon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** obiektu Britta Simon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-an-sap-netweaver-test-user"></a>Tworzenie użytkownika testowego środowiska SAP NetWeaver

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w oprogramowanie SAP NetWeaver. Praca z Twojej [pomocy technicznej SAP NetWeaver](https://www.sap.com/support.html) Aby dodać użytkowników na platformie SAP NetWeaver.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do oprogramowania SAP NetWeaver.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon oprogramowanie SAP NetWeaver, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **oprogramowanie SAP NetWeaver**.

    ![Konfigurowanie logowania jednokrotnego](./media/sap-netweaver-tutorial/tutorial_sapnetweaver_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka oprogramowanie SAP NetWeaver w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji SAP NetWeaver.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/sap-netweaver-tutorial/tutorial_general_01.png
[2]: ./media/sap-netweaver-tutorial/tutorial_general_02.png
[3]: ./media/sap-netweaver-tutorial/tutorial_general_03.png
[4]: ./media/sap-netweaver-tutorial/tutorial_general_04.png

[100]: ./media/sap-netweaver-tutorial/tutorial_general_100.png

[200]: ./media/sap-netweaver-tutorial/tutorial_general_200.png
[201]: ./media/sap-netweaver-tutorial/tutorial_general_201.png
[202]: ./media/sap-netweaver-tutorial/tutorial_general_202.png
[203]: ./media/sap-netweaver-tutorial/tutorial_general_203.png

