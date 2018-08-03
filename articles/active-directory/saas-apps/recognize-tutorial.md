---
title: 'Samouczek: Integracja usługi Azure Active Directory z rozpoznawaj | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Rozpoznaj.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: cfad939e-c8f4-45a0-bd25-c4eb9701acaa
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: jeedes
ms.openlocfilehash: b2d5acfcb722845d7f346668597c073319f273f0
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39448788"
---
# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>Samouczek: Integracja usługi Azure Active Directory z Rozpoznaj

W tym samouczku dowiesz się, jak zintegrować rozpoznawanie przy użyciu usługi Azure Active Directory (Azure AD).

Integrowanie rozpoznawaj z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do uznania
- Można włączyć użytkowników, aby automatycznie uzyskać zalogowanych do uznania (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą rozpoznawaj, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Rozpoznaj logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięcznej wersji próbnej tutaj: [oferta wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie rozpoznawaj z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-recognize-from-the-gallery"></a>Dodawanie rozpoznawaj z galerii
Aby skonfigurować integrację Rozpoznaj w usłudze Azure AD, należy dodać rozpoznawanie z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać rozpoznawanie z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **rozpoznawaj**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/recognize-tutorial/tutorial_recognize_search.png)

1. W panelu wyników wybierz **rozpoznawaj**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/recognize-tutorial/tutorial_recognize_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Rozpoznaj w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w rozpoznawaj do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w rozpoznawaj musi można ustanowić.

Rozpoznaj, przypisywanie wartości **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą rozpoznawaj, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego rozpoznawaj](#creating-a-recognize-test-user)**  — aby odpowiednikiem Britta Simon w rozpoznawaj połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji rozpoznawaj.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z rozpoznawaj, wykonaj następujące czynności:**

1. W witrynie Azure portal na **rozpoznawaj** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/recognize-tutorial/tutorial_recognize_samlbase.png)

1. Na **rozpoznaje domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/recognize-tutorial/tutorial_recognize_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://recognizeapp.com/<your-domain>/saml/sso`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://recognizeapp.com/<your-domain>`

    > [!NOTE] 
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania jednokrotnego i identyfikator. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta rozpoznaje](mailto:support@recognizeapp.com) można pobrać adresu URL logowania jednokrotnego i można uzyskać wartość identyfikatora, otwierając adres URL metadanych dostawcy usług w sekcji ustawień logowania jednokrotnego, które zostało wyjaśnione w dalszej części tego samouczka. . 
 
1. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/recognize-tutorial/tutorial_recognize_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/recognize-tutorial/tutorial_general_400.png)

1. Na **rozpoznać konfiguracji** kliknij **skonfiguruj rozpoznawanie** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/recognize-tutorial/tutorial_recognize_configure.png) 

1. W oknie przeglądarki internetowej innej logowanie jednokrotne do swojej dzierżawy rozpoznawaj jako administrator.

1. W prawym górnym rogu kliknij **Menu**. Przejdź do **firmy administratora**.
   
    ![Konfigurowanie pojedynczego logowania jednokrotnego w aplikacji po stronie](./media/recognize-tutorial/tutorial_recognize_000.png)

1. W okienku nawigacji po lewej stronie kliknij **ustawienia**.
   
    ![Konfigurowanie pojedynczego logowania jednokrotnego w aplikacji po stronie](./media/recognize-tutorial/tutorial_recognize_001.png)

1. Wykonaj następujące czynności na **ustawień logowania jednokrotnego** sekcji.
   
    ![Konfigurowanie pojedynczego logowania jednokrotnego w aplikacji po stronie](./media/recognize-tutorial/tutorial_recognize_002.png)
    
    a. Jako **włączyć logowanie Jednokrotne**, wybierz opcję **ON**.

    b. W **identyfikator jednostki tożsamości** pola tekstowego, Wklej wartość **identyfikator jednostki SAML** skopiowanej w witrynie Azure portal.
    
    c. W **logowania jednokrotnego, docelowy adres url** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL** skopiowanej w witrynie Azure portal.
    
    d. W **Slo docelowy adres url** pola tekstowego, Wklej wartość **adres URL wylogowania** skopiowanej w witrynie Azure portal. 
    
    e. Otwórz swoje pobrany **certyfikat (Base64)** w Notatniku plik, skopiuj jego zawartość do Schowka, a następnie wklej go do **certyfikatu** pola tekstowego.
    
    f. Kliknij przycisk **Zapisz ustawienia** przycisku. 

1. Obok pozycji **ustawień logowania jednokrotnego** sekcji, skopiuj adres URL, pod **adres url usługi dostawcy metadanych**.
   
    ![Konfigurowanie pojedynczego logowania jednokrotnego w aplikacji po stronie](./media/recognize-tutorial/tutorial_recognize_003.png)

1. Otwórz **link z adresem URL metadanych** w obszarze puste przeglądarki, aby pobrać dokumentu metadanych. Następnie skopiuj EntityDescriptor value(entityID) z pliku i wklej go w **identyfikator** polu tekstowym w **części rozpoznaje domena i adresy URL** w witrynie Azure portal.
    
    ![Konfigurowanie pojedynczego logowania jednokrotnego w aplikacji po stronie](./media/recognize-tutorial/tutorial_recognize_004.png)

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/recognize-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/recognize-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/recognize-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/recognize-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-recognize-test-user"></a>Tworzenie użytkownika testowego Rozpoznaj

Aby umożliwić użytkownikom usługi Azure AD zalogować się do uznania, musi być obsługiwana do uznania. W przypadku uznania Inicjowanie obsługi administracyjnej jest zadanie ręczne.

Ta aplikacja nie obsługuje standard SCIM inicjowania obsługi administracyjnej, ale ma synchronizacji alternatywne użytkownika, która aprowizuje użytkowników. 

**Aby udostępnić konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy rozpoznawaj jako administrator.

1. W prawym górnym rogu kliknij **Menu**. Przejdź do **firmy administratora**.

1. W okienku nawigacji po lewej stronie kliknij **ustawienia**.

1. Wykonaj następujące czynności na **synchronizacja użytkownika** sekcji.
   
   ![Nowy użytkownik](./media/recognize-tutorial/tutorial_recognize_005.png "nowego użytkownika")
   
   a. Jako **z włączoną funkcją synchronizacji**, wybierz opcję **ON**.
   
   b. Jako **dostawcy synchronizacji wybierz**, wybierz opcję **Microsoft / usługi Office 365**.
   
   c. Kliknij przycisk **Uruchom synchronizację użytkowników**.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do uznania.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon rozpoznawaj, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **rozpoznawaj**.

    ![Konfigurowanie logowania jednokrotnego](./media/recognize-tutorial/tutorial_recognize_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Rozpoznaj w panelu dostępu, możesz należy pobrać automatycznie zalogowanych do aplikacji rozpoznawaj. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/recognize-tutorial/tutorial_general_01.png
[2]: ./media/recognize-tutorial/tutorial_general_02.png
[3]: ./media/recognize-tutorial/tutorial_general_03.png
[4]: ./media/recognize-tutorial/tutorial_general_04.png

[100]: ./media/recognize-tutorial/tutorial_general_100.png

[200]: ./media/recognize-tutorial/tutorial_general_200.png
[201]: ./media/recognize-tutorial/tutorial_general_201.png
[202]: ./media/recognize-tutorial/tutorial_general_202.png
[203]: ./media/recognize-tutorial/tutorial_general_203.png

