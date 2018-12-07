---
title: 'Samouczek: Integracja usługi Azure Active Directory z małych ulepszenia | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między małych ulepszenia i usługi Azure Active Directory.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 59c8a112-41e1-4337-9ef3-3d7029780d61
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 588e56c9ae22578c08dbca07c7c576fe8b577b58
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2018
ms.locfileid: "53012338"
---
# <a name="tutorial-azure-active-directory-integration-with-small-improvements"></a>Samouczek: Integracja usługi Azure Active Directory z małych ulepszenia

W tym samouczku dowiesz się, jak zintegrować małych ulepszenia za pomocą usługi Azure Active Directory (Azure AD).

Integrowanie małych ulepszenia z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do małych ulepszenia
- Użytkowników, aby automatycznie uzyskać zalogowanych do ulepszenia mały (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z ulepszeniami małych, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Ulepszenia małych logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięcznej wersji próbnej tutaj [oferta wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie małych ulepszenia z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-small-improvements-from-the-gallery"></a>Dodawanie małych ulepszenia z galerii
Aby skonfigurować integrację małych ulepszeń w usłudze Azure AD, należy dodać małych ulepszenia z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać małych ulepszenia z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **małych ulepszenia**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_search.png)

1. W panelu wyników wybierz **małych ulepszenia**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą małych usprawnieniach użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w małych ulepszenia do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika poprawę małych musi nawiązać.

W małych ulepszenia, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z małych ulepszeniami, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego małych ulepszenia](#creating-a-small-improvements-test-user)**  — aby odpowiednikiem Britta Simon w małych ulepszenia, połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji małych ulepszenia.

**Aby skonfigurować usługi Azure AD logowanie jednokrotne z małych ulepszeniami, wykonaj następujące czynności:**

1. W witrynie Azure portal na **małych ulepszenia** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/smallimprovements-tutorial/tutorial_smallimprovements_samlbase.png)

1. Na **małych ulepszenia domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/smallimprovements-tutorial/tutorial_smallimprovements_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<subdomain>.small-improvements.com`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<subdomain>.small-improvements.com`

    > [!NOTE] 
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania jednokrotnego i identyfikator. Skontaktuj się z pomocą [zespołem pomocy technicznej małych klienta ulepszenia](mailto:support@small-improvements.com) do uzyskania tych wartości. 
 
1. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/smallimprovements-tutorial/tutorial_smallimprovements_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/smallimprovements-tutorial/tutorial_general_400.png)

1. Na **małych konfiguracji ulepszenia** , kliknij przycisk **skonfigurować mały ulepszenia** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/smallimprovements-tutorial/tutorial_smallimprovements_configure.png) 

1. W innym oknie przeglądarki Zaloguj się do witryny firmy małych ulepszenia jako administrator.

1. Na stronie głównego pulpitu nawigacyjnego kliknij **administracji** przycisku po lewej stronie.
   
    ![Konfigurowanie logowania jednokrotnego](./media/smallimprovements-tutorial/tutorial_smallimprovements_06.png) 

1. Kliknij przycisk **logowania jednokrotnego SAML** przycisk **integracje** sekcji.
   
    ![Konfigurowanie logowania jednokrotnego](./media/smallimprovements-tutorial/tutorial_smallimprovements_07.png) 

1. Na stronie ustawień logowania jednokrotnego wykonaj następujące czynności:
   
    ![Konfigurowanie logowania jednokrotnego](./media/smallimprovements-tutorial/tutorial_smallimprovements_08.png)  

    a. W **punkt końcowy HTTP** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL**, który skopiowano z witryny Azure portal.

    b. Otwórz pobranego certyfikatu w programie Notatnik, skopiuj zawartość, a następnie wklej go do **x509 certyfikatu** pola tekstowego. 

    c. Jeśli chcesz mieć logowania jednokrotnego i nazwy logowania formularza opcję uwierzytelniania dostępne dla użytkowników, sprawdź **włączyć dostęp za pośrednictwem logowania i hasła zbyt** opcji.  

    d. Wprowadź odpowiednią wartość nazwę przycisku logowania jednokrotnego logowania w **SAML monitu** pola tekstowego.  

    e. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/smallimprovements-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/smallimprovements-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/smallimprovements-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/smallimprovements-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-small-improvements-test-user"></a>Tworzenie użytkownika testowego małych ulepszenia

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do ulepszenia małych, musi być obsługiwana w małych ulepszenia. W przypadku małych ulepszenia aprowizacji to zadanie ręczne.

**Aby udostępnić konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy małych ulepszenia jako administrator.

1. Na stronie głównej, przejdź do menu po lewej stronie, kliknij pozycję **administracji**.

1. Kliknij przycisk **katalogu użytkownika** przycisku w sekcji Zarządzanie użytkownikami. 
   
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_10.png) 

1. Kliknij przycisk **dodawania użytkowników**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_11.png) 

1. Na **Add Users** okno dialogowe, należy wykonać następujące czynności: 

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_12.png)
    
    a. Wprowadź **imię** użytkownika, takich jak **Britta**.

    b. Wprowadź **nazwisko** użytkownika, takich jak **Simon**.

    c. Wprowadź **E-mail** użytkownika, takich jak <strong>brittasimon@contoso.com</strong>. 

    d. Możesz również wprowadzić osobistą wiadomość w **Wyślij wiadomość e-mail z powiadomieniem** pole. Jeśli nie chcesz wysłać powiadomienie, usuń zaznaczenie tego pola wyboru.

    e. Kliknij przycisk **tworzenia użytkowników**.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do małych ulepszenia.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon ulepszenia małe, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **małych ulepszenia**.

    ![Konfigurowanie logowania jednokrotnego](./media/smallimprovements-tutorial/tutorial_smallimprovements_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest test konfiguracji logowania jednokrotnego usługi Azure AD za pomocą panelu dostępu.  

Po kliknięciu kafelka małych ulepszenia w panelu dostępu, możesz należy pobrać automatycznie zalogowanych do aplikacji małych ulepszenia.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/smallimprovements-tutorial/tutorial_general_01.png
[2]: ./media/smallimprovements-tutorial/tutorial_general_02.png
[3]: ./media/smallimprovements-tutorial/tutorial_general_03.png
[4]: ./media/smallimprovements-tutorial/tutorial_general_04.png

[100]: ./media/smallimprovements-tutorial/tutorial_general_100.png

[200]: ./media/smallimprovements-tutorial/tutorial_general_200.png
[201]: ./media/smallimprovements-tutorial/tutorial_general_201.png
[202]: ./media/smallimprovements-tutorial/tutorial_general_202.png
[203]: ./media/smallimprovements-tutorial/tutorial_general_203.png

