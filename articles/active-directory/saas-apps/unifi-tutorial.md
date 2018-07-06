---
title: 'Samouczek: Integracja usługi Azure Active Directory z UNIFI | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i UNIFI.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: e1f49ee4-d2d4-4a82-9baf-0587ca1f20f6
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 0aa3ec013e93ddb89cd6982c01a38411f67b34c4
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2018
ms.locfileid: "37866352"
---
# <a name="tutorial-azure-active-directory-integration-with-unifi"></a>Samouczek: Integracja usługi Azure Active Directory z UNIFI

W tym samouczku dowiesz się, jak zintegrować UNIFI w usłudze Azure Active Directory (Azure AD).

Integrowanie UNIFI z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do UNIFI
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do UNIFI (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą UNIFI, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- UNIFI logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie UNIFI z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-unifi-from-the-gallery"></a>Dodawanie UNIFI z galerii
Aby skonfigurować integrację UNIFI w usłudze Azure AD, należy dodać UNIFI z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać UNIFI z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **UNIFI**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/unifi-tutorial/tutorial_unifi_search.png)

5. W panelu wyników wybierz **UNIFI**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/unifi-tutorial/tutorial_unifi_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą UNIFI w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w UNIFI do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w UNIFI musi można ustanowić.

W UNIFI, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą UNIFI, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie UNIFI testowe użytkownika](#creating-a-unifi-test-user)**  — aby odpowiednikiem Britta Simon w UNIFI połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji UNIFI.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z UNIFI, wykonaj następujące czynności:**

1. W witrynie Azure portal na **UNIFI** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/unifi-tutorial/tutorial_unifi_samlbase.png)

3. Na **UNIFI domena i adresy URL** sekcji, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![Konfigurowanie logowania jednokrotnego](./media/unifi-tutorial/tutorial_unifi_url1.png)

    W **identyfikator** polu tekstowym wpisz wartość: `INVIEWlabs` 

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL**, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![Konfigurowanie logowania jednokrotnego](./media/unifi-tutorial/tutorial_unifi_url2.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL: `https://app.discoverunifi.com/login`

5. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/unifi-tutorial/tutorial_unifi_certificate.png) 

6. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/unifi-tutorial/tutorial_general_400.png)
    
7. Na **konfiguracji UNIFI** , kliknij przycisk **skonfigurować UNIFI** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/unifi-tutorial/tutorial_unifi_configure.png)

8. W oknie przeglądarki internetowej innej, zaloguj się na swoje **UNIFI** witryny firmy jako administrator.

9. Kliknij pozycję **użytkowników**.

    ![Konfigurowanie logowania jednokrotnego](./media/unifi-tutorial/app1.png) 

10. Kliknij pozycję **Dodaj nowego dostawcę tożsamości**.

    ![Konfigurowanie logowania jednokrotnego](./media/unifi-tutorial/app2.png)

11. W **Dodawanie dostawcy tożsamości** sekcji, wykonaj następujące czynności:   

    ![Konfigurowanie logowania jednokrotnego](./media/unifi-tutorial/app3.png) 

    a. W **Nazwa dostawcy** polu tekstowym wpisz nazwę dostawcy tożsamości...

    b. W **adres URL dostawcy** Wklej w pole tekstowe **SAML pojedynczego logowania jednokrotnego usługi adresu URL** wartości, które zostały skopiowane z witryny Azure portal.

    c. Usuń certyfikat został już pobrany z witryny Azure portal w programie Notatnik, Open **---BEGIN CERTIFICATE---** i **---END CERTIFICATE---** tagu, a następnie wklej zawartość pozostałych **Certyfikatu** pola tekstowego.

    d. Wybierz **jest domyślny dostawca** pola wyboru.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/unifi-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/unifi-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/unifi-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/unifi-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-unifi-test-user"></a>Tworzenie użytkownika testowego UNIFI

W tej sekcji utworzysz użytkownika o nazwie Britta Simon. **UNIFI** obsługuje automatyczna aprowizacja użytkowników dzięki czynności ręcznej, nie są wymagane. Użytkownicy są tworzone automatycznie po pomyślnym uwierzytelnieniu z usługi Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do UNIFI.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon UNIFI, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **UNIFI**.

    ![Konfigurowanie logowania jednokrotnego](./media/unifi-tutorial/tutorial_unifi_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka UNIFI w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji UNIFI.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/unifi-tutorial/tutorial_general_01.png
[2]: ./media/unifi-tutorial/tutorial_general_02.png
[3]: ./media/unifi-tutorial/tutorial_general_03.png
[4]: ./media/unifi-tutorial/tutorial_general_04.png

[100]: ./media/unifi-tutorial/tutorial_general_100.png

[200]: ./media/unifi-tutorial/tutorial_general_200.png
[201]: ./media/unifi-tutorial/tutorial_general_201.png
[202]: ./media/unifi-tutorial/tutorial_general_202.png
[203]: ./media/unifi-tutorial/tutorial_general_203.png

