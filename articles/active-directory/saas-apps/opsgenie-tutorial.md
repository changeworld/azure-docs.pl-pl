---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą OpsGenie | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i OpsGenie.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 41b59b22-a61d-4fe6-ab0d-6c3991d1375f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: 52d976761ad5962f09f40565cfa04ae78386d1ca
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55192396"
---
# <a name="tutorial-azure-active-directory-integration-with-opsgenie"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą OpsGenie

W tym samouczku dowiesz się, jak zintegrować OpsGenie w usłudze Azure Active Directory (Azure AD).

Integrowanie OpsGenie z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do OpsGenie
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do OpsGenie (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą OpsGenie, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- OpsGenie logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie OpsGenie z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-opsgenie-from-the-gallery"></a>Dodawanie OpsGenie z galerii
Aby skonfigurować integrację OpsGenie w usłudze Azure AD, należy dodać OpsGenie z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać OpsGenie z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **OpsGenie**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/opsgenie-tutorial/tutorial_opsgenie_search.png)

1. W panelu wyników wybierz **OpsGenie**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/opsgenie-tutorial/tutorial_opsgenie_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą OpsGenie w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w OpsGenie do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w OpsGenie musi można ustanowić.

W OpsGenie, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą OpsGenie, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego OpsGenie](#creating-a-opsgenie-test-user)**  — aby odpowiednikiem Britta Simon w OpsGenie połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji OpsGenie.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z OpsGenie, wykonaj następujące czynności:**

1. W witrynie Azure portal na **OpsGenie** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/opsgenie-tutorial/tutorial_opsgenie_samlbase.png)

1. Na **OpsGenie domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/opsgenie-tutorial/tutorial_opsgenie_url.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL: `https://app.opsgenie.com/auth/login`

1. Na **certyfikat podpisywania SAML** sekcji, kliknij przycisk kopiowania, aby skopiować **adres Url metadanych Federacji aplikacji** i wklej go w Notatniku.

    ![Link do pobierania certyfikatu](./media/opsgenie-tutorial/tutorial_opsgenie_certificate.png)

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/opsgenie-tutorial/tutorial_general_400.png)

1. Na **konfiguracji OpsGenie** , kliknij przycisk **skonfigurować OpsGenie** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczego logowania jednokrotnego usługi adresu URL** z sekcji krótki.

    ![Konfigurowanie logowania jednokrotnego](./media/opsgenie-tutorial/tutorial_opsgenie_configure.png)

1. Otwórz inne wystąpienie przeglądarki, a następnie zaloguj się do OpsGenie jako administrator.

1. Kliknij przycisk **ustawienia**, a następnie kliknij przycisk **Single Sign On** kartę.
   
    ![OpsGenie logowanie jednokrotne](./media/opsgenie-tutorial/tutorial_opsgenie_06.png)

1. Aby włączyć logowanie Jednokrotne, zaznacz **włączone**.
   
    ![OpsGenie Settings](./media/opsgenie-tutorial/tutorial_opsgenie_07.png) 

1. W **dostawcy** kliknij **usługi Azure Active Directory** kartę.
   
    ![OpsGenie Settings](./media/opsgenie-tutorial/tutorial_opsgenie_08.png) 

1. Na stronie okna dialogowego usługi Azure Active Directory wykonaj następujące czynności:
   
    ![OpsGenie Settings](./media/opsgenie-tutorial/tutorial_opsgenie_09.png)
    
    a. W **punktem końcowym SAML 2.0** pola tekstowego, Wklej **pojedynczy znak na adres URL usługi**wartości, które zostały skopiowane z witryny Azure portal.
    
    b. W **adres Url metadanych:** pola tekstowego, Wklej **adres Url metadanych Federacji aplikacji** wartości, które zostały skopiowane z witryny Azure portal.
    
    c. Kliknij przycisk **Save Changes** (Zapisz zmiany).

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/opsgenie-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/opsgenie-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/opsgenie-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/opsgenie-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-opsgenie-test-user"></a>Tworzenie użytkownika testowego OpsGenie

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon w OpsGenie. 

1. W oknie przeglądarki sieci web Zaloguj się do dzierżawy OpsGenie jako administrator.

1. Przejdź do listy użytkowników, klikając **użytkownika** w panelu po lewej stronie.
   
   ![OpsGenie Settings](./media/opsgenie-tutorial/tutorial_opsgenie_10.png) 

1. Kliknij pozycję **Add User** (Dodaj użytkownika).

1. Na **Dodaj użytkownika** okno dialogowe, należy wykonać następujące czynności:
   
   ![OpsGenie Settings](./media/opsgenie-tutorial/tutorial_opsgenie_11.png)
   
   a. W **E-mail** pole tekstowe, wpisz adres e-mail BrittaSimon zostały omówione w usłudze Azure Active Directory.
   
   b. W **imię i nazwisko** polu tekstowym wpisz **Britta Simon**.
   
   c. Kliknij pozycję **Zapisz**. 

>[!NOTE]
>Britta otrzymuje wiadomość e-mail z instrukcjami konfigurowania jej profilu.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do OpsGenie.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon OpsGenie, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **OpsGenie**.

    ![Konfigurowanie logowania jednokrotnego](./media/opsgenie-tutorial/tutorial_opsgenie_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest test konfiguracji logowania jednokrotnego usługi Azure AD za pomocą panelu dostępu.

Po kliknięciu kafelka OpsGenie w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji OpsGenie.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/opsgenie-tutorial/tutorial_general_01.png
[2]: ./media/opsgenie-tutorial/tutorial_general_02.png
[3]: ./media/opsgenie-tutorial/tutorial_general_03.png
[4]: ./media/opsgenie-tutorial/tutorial_general_04.png

[100]: ./media/opsgenie-tutorial/tutorial_general_100.png

[200]: ./media/opsgenie-tutorial/tutorial_general_200.png
[201]: ./media/opsgenie-tutorial/tutorial_general_201.png
[202]: ./media/opsgenie-tutorial/tutorial_general_202.png
[203]: ./media/opsgenie-tutorial/tutorial_general_203.png

