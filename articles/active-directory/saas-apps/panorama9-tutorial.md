---
title: 'Samouczek: Integracja usługi Azure Active Directory z Panorama9 | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Panorama9.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 5e28d7fa-03be-49f3-96c8-b567f1257d44
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.openlocfilehash: ad2a9dba54df3ae7a3e2604437c8a65c88dcca94
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52850894"
---
# <a name="tutorial-azure-active-directory-integration-with-panorama9"></a>Samouczek: Integracja usługi Azure Active Directory z Panorama9

W tym samouczku dowiesz się, jak zintegrować Panorama9 w usłudze Azure Active Directory (Azure AD).

Integrowanie Panorama9 z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Panorama9
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do Panorama9 (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Panorama9, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Panorama9 logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Panorama9 z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-panorama9-from-the-gallery"></a>Dodawanie Panorama9 z galerii
Aby skonfigurować integrację Panorama9 w usłudze Azure AD, należy dodać Panorama9 z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Panorama9 z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **Panorama9**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/panorama9-tutorial/tutorial_panorama9_search.png)

1. W panelu wyników wybierz **Panorama9**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/panorama9-tutorial/tutorial_panorama9_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

W tej sekcji Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą Panorama9 w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Panorama9 do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Panorama9 musi można ustanowić.

W Panorama9, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Panorama9, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Panorama9](#creating-a-panorama9-test-user)**  — aby odpowiednikiem Britta Simon w Panorama9 połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Panorama9.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Panorama9, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Panorama9** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/panorama9-tutorial/tutorial_panorama9_samlbase.png)

1. Na **Panorama9 domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/panorama9-tutorial/tutorial_panorama9_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL jako: `https://dashboard.panorama9.com/saml/access/3262`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://www.panorama9.com/saml20/<tenant-name>`

    > [!NOTE] 
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania jednokrotnego i identyfikator. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Panorama9](https://support.panorama9.com) do uzyskania tych wartości. 
 
1. Na **certyfikat podpisywania SAML** sekcji, skopiuj **odcisk PALCA** wartość certyfikatu.

    ![Konfigurowanie logowania jednokrotnego](./media/panorama9-tutorial/tutorial_panorama9_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/panorama9-tutorial/tutorial_general_400.png)

1. Na **konfiguracji Panorama9** , kliknij przycisk **skonfigurować Panorama9** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/panorama9-tutorial/tutorial_panorama9_configure.png) 

1. W oknie przeglądarki internetowej innej Zaloguj się do witryny firmy Panorama9, jako administrator.

1. Na pasku narzędzi u góry kliknij **Zarządzaj**, a następnie kliknij przycisk **rozszerzenia**.
   
   ![Rozszerzenia](./media/panorama9-tutorial/ic790023.png "rozszerzenia")
1. Na **rozszerzenia** okno dialogowe, kliknij przycisk **logowania jednokrotnego**.
   
   ![Logowanie jednokrotne](./media/panorama9-tutorial/ic790024.png "logowanie jednokrotne")
1. W **ustawienia** sekcji, wykonaj następujące czynności:
   
   ![Ustawienia](./media/panorama9-tutorial/ic790025.png "ustawienia")
   
    a. W **adres URL dostawcy tożsamości** pola tekstowego, Wklej wartość **pojedynczy znak na adres URL usługi**, które zostały skopiowane z witryny Azure portal.
   
    b. W **odcisk palca certyfikatu** pola tekstowego, Wklej **odcisk palca** wartość certyfikatu, który skopiowano z witryny Azure portal.    
         
1. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/panorama9-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/panorama9-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/panorama9-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/panorama9-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-panorama9-test-user"></a>Tworzenie użytkownika testowego Panorama9

Aby umożliwić użytkownikom usługi Azure AD zalogować się do Panorama9, musi być obsługiwana w Panorama9.  

W przypadku Panorama9 Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby skonfigurować aprowizację użytkowników, wykonaj następujące czynności:**

1. Zaloguj się do Twojej **Panorama9** witryny firmy jako administrator.

1. W menu u góry kliknij **Zarządzaj**, a następnie kliknij przycisk **użytkowników**.
   
  ![Użytkownicy](./media/panorama9-tutorial/ic790027.png "użytkowników")

1. W sekcji Użytkownicy kliknij **+** można dodać nowego użytkownika.

 ![Użytkownicy](./media/panorama9-tutorial/ic790028.png "użytkowników")

1. Przejdź do sekcji danych użytkownika, wpisz adres e-mail, z którym chcesz udostępnić w prawidłowym użytkownikiem usługi Azure Active Directory **E-mail** pola tekstowego.

1. Się w sekcji Użytkownicy kliknij **Zapisz**.
   
> [!NOTE]
    > Właściciel konta usługi Azure Active Directory otrzymuje wiadomość e-mail, a także następujące łącze, aby potwierdzić swoje konto, zanim stanie się aktywny.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Panorama9.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Panorama9, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Panorama9**.

    ![Konfigurowanie logowania jednokrotnego](./media/panorama9-tutorial/tutorial_panorama9_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Panorama9 w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do Panorama9 aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/panorama9-tutorial/tutorial_general_01.png
[2]: ./media/panorama9-tutorial/tutorial_general_02.png
[3]: ./media/panorama9-tutorial/tutorial_general_03.png
[4]: ./media/panorama9-tutorial/tutorial_general_04.png

[100]: ./media/panorama9-tutorial/tutorial_general_100.png

[200]: ./media/panorama9-tutorial/tutorial_general_200.png
[201]: ./media/panorama9-tutorial/tutorial_general_201.png
[202]: ./media/panorama9-tutorial/tutorial_general_202.png
[203]: ./media/panorama9-tutorial/tutorial_general_203.png

