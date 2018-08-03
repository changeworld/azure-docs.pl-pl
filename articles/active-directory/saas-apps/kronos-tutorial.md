---
title: 'Samouczek: Integracja usługi Azure Active Directory z Kronos | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Kronos.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: e28d6191-c375-43c6-b2df-22daa88d9939
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: 31e8c990e39b3dc99ccd4dcda0a8d00ecb83b440
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39435887"
---
# <a name="tutorial-azure-active-directory-integration-with-kronos"></a>Samouczek: Integracja usługi Azure Active Directory z Kronos

W tym samouczku dowiesz się, jak zintegrować Kronos w usłudze Azure Active Directory (Azure AD).

Integrowanie Kronos z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Kronos
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do Kronos (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Kronos, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- A **Kronos pracowników centralnej** logowanie Jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Kronos z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-kronos-from-the-gallery"></a>Dodawanie Kronos z galerii
Aby skonfigurować integrację Kronos w usłudze Azure AD, należy dodać Kronos z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Kronos z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **Kronos**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/kronos-tutorial/tutorial_kronos_search.png)

1. W panelu wyników wybierz **Kronos**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/kronos-tutorial/tutorial_kronos_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą Kronos w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Kronos do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Kronos musi można ustanowić.

W Kronos, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Kronos, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Kronos](#creating-a-kronos-test-user)**  — aby odpowiednikiem Britta Simon w Kronos połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Kronos.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Kronos, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Kronos** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/kronos-tutorial/tutorial_kronos_samlbase.png)

1. Na **Kronos domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/kronos-tutorial/tutorial_kronos_url.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<company name>.kronos.net/`

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<company name>.kronos.net/wfc/navigator/logonWithUID`

    > [!NOTE] 
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adres URL odpowiedzi. Skontaktuj się z pomocą [zespołu pomocy technicznej Kronos](https://www.kronos.in/contact/en-in/form) do uzyskania tych wartości.
 
1. Aplikacja Kronos oczekuje twierdzenia SAML w określonym formacie. Praca z [zespołu pomocy technicznej Kronos](https://www.kronos.in/contact/en-in/form) najpierw po to, aby zidentyfikować identyfikator właściwy użytkownik, który jest mapowany na aplikację. Ponadto prosimy o poświęcenie wskazówek dotyczących atrybutów, które mają być użyte dla tego mapowania.
 
     Firma Microsoft zaleca używanie **"NameIdentifier"** atrybut jako identyfikator użytkownika. Możesz zarządzać wartości te atrybuty z **"Atrybuty użytkownika"** sekcji na stronie integracji aplikacji.
     
     Poniższy zrzut ekranu przedstawia przykład tego. W tym miejscu możemy zmapowane **identyfikator użytkownika (nameid)** z **ExtractMailPrefix()** funkcji **user.userprincipalname**. Dzięki temu wartość prefiksu adres e-mail użytkownika, który jest unikatowy identyfikator użytkownika. Jest on wysyłany do aplikacji Kronos w każdym pomyślnej odpowiedzi. 
     
    ![Konfigurowanie logowania jednokrotnego](./media/kronos-tutorial/tutorial_kronos_attribute.png)

1. W **atrybutów użytkownika** sekcji na **logowanie jednokrotne** okno dialogowe:

    a. Na liście rozwijanej identyfikator użytkownika wybierz **ExtractMailPrefix**.

    b. W **poczty** listy rozwijanej wybierz **user.userprincipalname**.

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/kronos-tutorial/tutorial_kronos_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/kronos-tutorial/tutorial_general_400.png)

1. Aby skonfigurować logowanie jednokrotne na **Kronos** stronie, musisz wysłać pobrany **XML metadanych** do [zespołu pomocy technicznej Kronos](https://www.kronos.in/contact/en-in/form). 

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/kronos-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/kronos-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/kronos-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/kronos-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-kronos-test-user"></a>Tworzenie użytkownika testowego Kronos

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w Kronos. Aplikacja Kronos potrzebuje wszystkich użytkowników do udostępnienia w aplikacji przed wykonaniem logowania jednokrotnego. 

Praca z [zespołu pomocy technicznej Kronos](https://www.kronos.in/contact/en-in/form) do aprowizowania tych użytkowników do aplikacji. 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Kronos.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Kronos, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Kronos**.

    ![Konfigurowanie logowania jednokrotnego](./media/kronos-tutorial/tutorial_kronos_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji możesz przetestować konfigurację logowania jednokrotnego usługi Azure AD za pomocą panelu dostępu.

Po kliknięciu kafelka Kronos w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Kronos.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/kronos-tutorial/tutorial_general_01.png
[2]: ./media/kronos-tutorial/tutorial_general_02.png
[3]: ./media/kronos-tutorial/tutorial_general_03.png
[4]: ./media/kronos-tutorial/tutorial_general_04.png

[100]: ./media/kronos-tutorial/tutorial_general_100.png

[200]: ./media/kronos-tutorial/tutorial_general_200.png
[201]: ./media/kronos-tutorial/tutorial_general_201.png
[202]: ./media/kronos-tutorial/tutorial_general_202.png
[203]: ./media/kronos-tutorial/tutorial_general_203.png

