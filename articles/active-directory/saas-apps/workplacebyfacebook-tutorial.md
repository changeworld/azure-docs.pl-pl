---
title: 'Samouczek: Integracja usługi Azure Active Directory z miejsca pracy, serwisu Facebook | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i obszaru roboczego w serwisie Facebook.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: jeedes
ms.openlocfilehash: 1f83dd64c7f6773ddb8956e6ebbc37b8c55aacec
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39423875"
---
# <a name="tutorial-azure-active-directory-integration-with-workplace-by-facebook"></a>Samouczek: Integracja usługi Azure Active Directory z miejsca pracy w serwisie Facebook

W tym samouczku dowiesz się, jak zintegrować miejsca pracy w serwisie Facebook z usługą Azure Active Directory (Azure AD).

Integrowanie miejsca pracy w serwisie Facebook z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do miejsca pracy w serwisie Facebook
- Można włączyć użytkowników, aby automatycznie uzyskać zalogowanych do miejsca pracy w serwisie Facebook (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z miejsca pracy, Facebook, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Pracy przez Facebook logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

> [!NOTE]
> Facebook zawiera dwa produkty, obszar roboczy Standard (wersja bezpłatna) i Premium obszar roboczy (płatność). Dzierżawami obszar roboczy w warstwie Premium można skonfigurować integracji Standard SCIM i logowania jednokrotnego z nie innych wpływ na koszt i licencje wymagane. Usługa rejestracji Jednokrotnej i użyciu SCIM nie są dostępne w miejscu pracy standardowych wystąpień.

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie obszaru roboczego w serwisie Facebook z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>Dodawanie obszaru roboczego w serwisie Facebook z galerii
Aby skonfigurować integrację z miejsca pracy w usłudze Facebook do usługi Azure AD, należy dodać miejsca pracy w serwisie Facebook z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać miejsce pracy w serwisie Facebook z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **miejsca pracy w serwisie Facebook**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_search.png)

1. W panelu wyników wybierz **miejsca pracy w serwisie Facebook**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i przetestować usługi Azure AD logowanie jednokrotne z miejsca pracy, Facebook, w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w miejscu pracy w usłudze Facebook do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w miejscu pracy, Facebook musi nawiązać.

Ustanowieniu tej relacji łączy, przypisując wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** w miejscu pracy w serwisie Facebook.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z miejsca pracy, Facebook, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Konfigurowanie częstotliwości ponowne uwierzytelnianie](#configuring-reauthentication-frequency)**  — Aby skonfigurować obszar roboczy, aby monitować o sprawdzenie protokołu SAML.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie pracy przez użytkownika testowego Facebook](#creating-a-workplace-by-facebook-test-user)**  — aby odpowiednikiem Britta Simon w miejscu pracy, Facebook, połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji Włączanie usługi Azure AD logowania jednokrotnego w witrynie Azure portal, a podczas konfigurowania logowania jednokrotnego w miejscu pracy aplikacji usługi Facebook.

**Aby skonfigurować usługi Azure AD logowanie jednokrotne z miejsca pracy, Facebook, wykonaj następujące czynności:**

1. W witrynie Azure portal na **miejsca pracy w serwisie Facebook** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_samlbase.png)

1. Na **obszar roboczy usługi Facebook, domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<instancename>.facebook.com`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://www.facebook.com/company/<instanceID>`

    > [!NOTE] 
    > Te wartości nie są rzeczywiste. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania jednokrotnego i identyfikator. Wyświetlić strony uwierzytelniania pulpitu nawigacyjnego firmy miejsca pracy dla poprawne wartości dla swojej społeczności miejsca pracy. 

1. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/workplacebyfacebook-tutorial/tutorial_general_400.png)

1. Na **miejsca pracy przez konfigurację usługi Facebook** , kliknij przycisk **skonfigurować obszar roboczy w usłudze Facebook** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/workplacebyfacebook-tutorial/config.png) 

1. W oknie przeglądarki internetowej innej, zaloguj się do miejsca pracy przez witrynę firmy Facebook jako administrator.
  
   > [!NOTE] 
   > W ramach procesu uwierzytelniania SAML miejsce pracy mogą używać ciągów zapytań do wersji 2.5 kilobajtów rozmiaru w celu przekazania parametrów do usługi Azure AD.

1. W **pulpitu nawigacyjnego firmy**, przejdź do **uwierzytelniania** kartę.

1. W obszarze **uwierzytelnianie SAML**, wybierz opcję **logowania jednokrotnego tylko** z listy rozwijanej.

1. Wprowadzanie wartości skopiowane z **miejsca pracy przez konfigurację usługi Facebook** części witryny Azure portal do odpowiednich pól:

    *   W **adres URL SAML** pola tekstowego, Wklej wartość **pojedynczy znak na adres URL usługi**, który skopiowano z witryny Azure portal.
    *   W **polu tekstowym adres URL wystawcy SAML**, Wklej wartość **SAML identyfikator jednostki**, które zostały skopiowane z witryny Azure portal.
    *   W **przekierowania wylogowania protokołu SAML** (opcjonalnie), Wklej wartość **adres URL wylogowania**, które zostały skopiowane z witryny Azure portal.
    *   Otwórz swoje **certyfikat szyfrowany algorytmem base-64** w Notatniku pobranego z witryny Azure portal, skopiuj zawartość go do Schowka, a następnie wklej go do **certyfikat SAML** pola tekstowego.

1. Może być konieczne wprowadzenie adresu URL odbiorców, adres URL odbiorcy, a adres URL usługi ACS (Assertion Consumer Service) na liście **plik konfiguracji SAML** sekcji.

1. Przewiń do końca sekcji, a następnie kliknij przycisk **Test rejestracji Jednokrotnej** przycisku. Przedstawione to wyniki w oknie podręcznym znajdujących się za pomocą strony logowania usługi Azure AD. Wprowadź swoje poświadczenia w zwykły do uwierzytelniania. 

    **Rozwiązywanie problemów:** upewnij się, adres e-mail, zwracane zwrotnego z usługi Azure AD jest taka sama jak użytkownik jest zalogowany przy użyciu konta firmowego.

1. Po testu została zakończona pomyślnie, przewiń w dół strony i kliknij **Zapisz** przycisku.

1. Wszyscy użytkownicy korzystający z miejsca pracy zostaną teraz wyświetlone strony logowania usługi Azure AD do uwierzytelniania.

1. **SAML wylogowania przekierowania (opcjonalnie)** - 

    Można opcjonalnie skonfigurować SAML adres Url wylogowania, którego można użyć, aby wskazywała na strony wylogowania w usłudze Azure AD. Gdy to ustawienie jest włączone i skonfigurowane, użytkownik nie jest już nastąpi przekierowanie do strony wylogowania w miejscu pracy. Zamiast tego użytkownik zostanie przekierowany do adresu url, który został dodany w ustawieniu przekierowania wylogowania protokołu SAML.

### <a name="configuring-reauthentication-frequency"></a>Konfigurowanie częstotliwości ponowne uwierzytelnianie

Można skonfigurować obszar roboczy, aby monitować o wyboru języka SAML, każdego dnia, trzech dni, tydzień, dwa tygodnie, miesiąc, czy nigdy.

> [!NOTE] 
>Minimalna wartość wyboru języka SAML na aplikacji dla urządzeń przenośnych jest ustawiany na jeden tydzień.

Możesz też wymusić SAML, zresetuj dla wszystkich użytkowników, korzystając z przycisku: SAML wymagają uwierzytelnianie dla wszystkich użytkowników.


### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/workplacebyfacebook-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/workplacebyfacebook-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/workplacebyfacebook-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/workplacebyfacebook-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-workplace-by-facebook-test-user"></a>Tworzenie z miejscem pracy przez użytkownika testowego w serwisie Facebook

W tej sekcji użytkownika o nazwie Britta Simon jest tworzony w miejscu pracy przez usługi Facebook. Obszar roboczy w usłudze Facebook obsługę just-in-time, który jest domyślnie włączona.

Nie ma akcji dla Ciebie w tej sekcji. Jeśli użytkownik nie istnieje w miejscu pracy, Facebook, nowy jest tworzona przy próbie uzyskania dostępu do miejsca pracy przez usługi Facebook.

>[!Note]
>Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [miejsca pracy przez zespół pomocy technicznej klienta usługi Facebook](https://workplace.fb.com/faq/)

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do miejsca pracy w serwisie Facebook.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon w serwisie Facebook w miejscu pracy, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **miejsca pracy w serwisie Facebook**.

    ![Konfigurowanie logowania jednokrotnego](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

Jeśli chcesz przetestować pojedynczego ustawienia logowania jednokrotnego, otwórz Panel dostępu.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie Aprowizowania użytkowników](workplacebyfacebook-provisioning-tutorial.md)



<!--Image references-->

[1]: ./media/workplacebyfacebook-tutorial/tutorial_general_01.png
[2]: ./media/workplacebyfacebook-tutorial/tutorial_general_02.png
[3]: ./media/workplacebyfacebook-tutorial/tutorial_general_03.png
[4]: ./media/workplacebyfacebook-tutorial/tutorial_general_04.png

[100]: ./media/workplacebyfacebook-tutorial/tutorial_general_100.png

[200]: ./media/workplacebyfacebook-tutorial/tutorial_general_200.png
[201]: ./media/workplacebyfacebook-tutorial/tutorial_general_201.png
[202]: ./media/workplacebyfacebook-tutorial/tutorial_general_202.png
[203]: ./media/workplacebyfacebook-tutorial/tutorial_general_203.png
