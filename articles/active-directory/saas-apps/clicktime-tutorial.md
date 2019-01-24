---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą ClickTime | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i ClickTime.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: d437b5ab-4d71-4c13-96d0-79018cebbbd4
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: jeedes
ms.openlocfilehash: f1c7c3cf850ed48412c8a232e364f927248ed9bf
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54811132"
---
# <a name="tutorial-azure-active-directory-integration-with-clicktime"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą ClickTime

W tym samouczku dowiesz się, jak zintegrować ClickTime w usłudze Azure Active Directory (Azure AD).

Integrowanie ClickTime z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do ClickTime
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do ClickTime (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą ClickTime, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- ClickTime logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie ClickTime z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-clicktime-from-the-gallery"></a>Dodawanie ClickTime z galerii
Aby skonfigurować integrację ClickTime w usłudze Azure AD, należy dodać ClickTime z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać ClickTime z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

1. W polu wyszukiwania wpisz **ClickTime**, wybierz opcję **ClickTime** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![ClickTime na liście wyników](./media/clicktime-tutorial/tutorial_clicktime_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą ClickTime w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w ClickTime do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w ClickTime musi można ustanowić.

W ClickTime, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą ClickTime, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
1. **[Tworzenie użytkownika testowego ClickTime](#create-a-clicktime-test-user)**  — aby odpowiednikiem Britta Simon w ClickTime połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji ClickTime.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z ClickTime, wykonaj następujące czynności:**

1. W witrynie Azure portal na **ClickTime** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/clicktime-tutorial/tutorial_clicktime_samlbase.png)

1. Na **ClickTime domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![ClickTime domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/clicktime-tutorial/tutorial_clicktime_url.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL jako: `https://app.clicktime.com/sp/`
    
    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następujących wzorców: 

    | |
    |--|
    | `https://app.clicktime.com/Login/` |
    | `https://app.clicktime.com/App/Login/Consume.aspx` |

1. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link do pobierania certyfikatu](./media/clicktime-tutorial/tutorial_clicktime_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/clicktime-tutorial/tutorial_general_400.png)

1. Na **konfiguracji ClickTime** , kliknij przycisk **skonfigurować ClickTime** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfiguracja ClickTime](./media/clicktime-tutorial/tutorial_clicktime_configure.png) 

1. W oknie przeglądarki internetowej innej Zaloguj się do witryny firmy ClickTime, jako administrator.

1. Na pasku narzędzi u góry kliknij **preferencje**, a następnie kliknij przycisk **ustawienia zabezpieczeń**.

1. W **preferencje rejestracji jednokrotnej** konfiguracji sekcji, wykonaj następujące czynności:
   
    ![Ustawienia zabezpieczeń](./media/clicktime-tutorial/tic777280.png "ustawienia zabezpieczeń")
   
    a.  Wybierz **Zezwalaj** Zaloguj się przy użyciu pojedynczego logowania jednokrotnego (SSO) przy użyciu **usługi Azure AD**.
   
    b. W **punkt końcowy dostawcy tożsamości** pola tekstowego, Wklej **SAML pojedynczego logowania jednokrotnego usługi adresu URL** skopiowanej w witrynie Azure portal.
   
    c.  Otwórz **certyfikat szyfrowany algorytmem base-64** pobranego z witryny Azure portal w **Notatnik**, skopiuj zawartość, a następnie wklej go do **certyfikat X.509** pola tekstowego.
   
    d.  Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/clicktime-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.
    
    !["Użytkownicy i grupy" i "All users" linki](./media/clicktime-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.
 
    ![Przycisk Dodaj](./media/clicktime-tutorial/create_aaduser_03.png) 

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:
 
    ![Okno dialogowe użytkownika](./media/clicktime-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-clicktime-test-user"></a>Tworzenie użytkownika testowego ClickTime

Aby umożliwić użytkownikom usługi Azure AD zalogować się do ClickTime, musi być obsługiwana w ClickTime.  
W przypadku ClickTime Inicjowanie obsługi administracyjnej jest zadanie ręczne.

> [!NOTE]
> Można użyć jakichkolwiek innych ClickTime użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez ClickTime można uaktywniać ich konta usługi Azure AD.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**
1. Zaloguj się do Twojej **ClickTime** dzierżawy.
1. Na pasku narzędzi u góry kliknij **firmy**, a następnie kliknij przycisk **osób**.
   
    ![Osoby](./media/clicktime-tutorial/tic777282.png "Osoby")
1. Kliknij przycisk **osobę**.
   
    ![Dodaj osobę](./media/clicktime-tutorial/tic777283.png "osobę")
1. W sekcji nową osobę wykonaj następujące czynności:
   
    ![Osoby](./media/clicktime-tutorial/tic777284.png "Osoby")
   
    a.  W **Pełna nazwa** pola tekstowego, typ Pełna nazwa użytkownika, takich jak **Britta Simon**. 
  
    b.  W **adres e-mail** polu tekstowym wpisz adres e-mail użytkownika, takie jak **brittasimon@contoso.com**.
       
    > [!NOTE]
    > Jeśli chcesz, można ustawić dodatkowe właściwości nowego obiektu osoby.
   
    c.  Kliknij pozycję **Zapisz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do ClickTime.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon ClickTime, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **ClickTime**.

    ![Link ClickTimne na liście aplikacji](./media/clicktime-tutorial/tutorial_clicktime_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka ClickTime w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji ClickTime.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/clicktime-tutorial/tutorial_general_01.png
[2]: ./media/clicktime-tutorial/tutorial_general_02.png
[3]: ./media/clicktime-tutorial/tutorial_general_03.png
[4]: ./media/clicktime-tutorial/tutorial_general_04.png

[100]: ./media/clicktime-tutorial/tutorial_general_100.png

[200]: ./media/clicktime-tutorial/tutorial_general_200.png
[201]: ./media/clicktime-tutorial/tutorial_general_201.png
[202]: ./media/clicktime-tutorial/tutorial_general_202.png
[203]: ./media/clicktime-tutorial/tutorial_general_203.png

