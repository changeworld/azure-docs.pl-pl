---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą szkoleń świadomości bezpieczeństwa KnowBe4 | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i szkolenia w zakresie rozpoznawania KnowBe4 zabezpieczeń.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: b80d2212-cc5f-4adb-836c-570640810c39
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: jeedes
ms.openlocfilehash: c27af4e7bc88f24b76310336859740d8795f7cbe
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449277"
---
# <a name="tutorial-azure-active-directory-integration-with-knowbe4-security-awareness-training"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą szkolenia w zakresie rozpoznawania KnowBe4 zabezpieczeń

W tym samouczku dowiesz się, jak zintegrować szkolenia w zakresie rozpoznawania KnowBe4 zabezpieczeń z usługą Azure Active Directory (Azure AD).

Integrowanie szkolenia w zakresie rozpoznawania KnowBe4 zabezpieczeń z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do szkolenia w zakresie rozpoznawania KnowBe4 zabezpieczeń.
- Użytkowników, aby automatycznie uzyskać zalogowanych do KnowBe4 świadomości szkolenie z zakresu zabezpieczeń (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integracji z usługą Azure AD za pomocą szkolenia w zakresie rozpoznawania KnowBe4 zabezpieczeń, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Rozpoznawanie szkolenia w zakresie zabezpieczeń KnowBe4 logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie KnowBe4 świadomości szkolenia w zakresie zabezpieczeń z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-knowbe4-security-awareness-training-from-the-gallery"></a>Dodawanie KnowBe4 świadomości szkolenia w zakresie zabezpieczeń z galerii
Aby skonfigurować integrację szkolenia w zakresie rozpoznawania KnowBe4 zabezpieczeń w usłudze Azure AD, należy dodać rozpoznawanie szkolenia w zakresie zabezpieczeń KnowBe4 z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać rozpoznawanie szkolenia w zakresie zabezpieczeń KnowBe4 z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

1. W polu wyszukiwania wpisz **świadomości szkolenia w zakresie zabezpieczeń KnowBe4**, wybierz pozycję **świadomości szkolenia w zakresie zabezpieczeń KnowBe4** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikacja.

    ![KnowBe4 świadomości szkolenie z zakresu zabezpieczeń na liście wyników](./media/knowbe4-tutorial/tutorial_knowbe4_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą KnowBe4 świadomości szkolenia w zakresie zabezpieczeń oparte na użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w szkolenia w zakresie rozpoznawania KnowBe4 zabezpieczeń do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w szkolenia w zakresie rozpoznawania KnowBe4 zabezpieczeń musi nawiązać.

W KnowBe4 świadomości szkolenie z zakresu zabezpieczeń, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą szkolenia w zakresie rozpoznawania KnowBe4 zabezpieczeń, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego świadomości szkolenia w zakresie zabezpieczeń KnowBe4](#create-a-knowbe4-security-awareness-training-test-user)**  — aby odpowiednikiem Britta Simon w KnowBe4 świadomości szkolenia w zakresie zabezpieczeń połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji szkolenia w zakresie rozpoznawania KnowBe4 zabezpieczeń.

**Aby skonfigurować usługi Azure AD logowanie jednokrotne za pomocą szkolenia w zakresie rozpoznawania KnowBe4 zabezpieczeń, wykonaj następujące czynności:**

1. W witrynie Azure portal na **świadomości szkolenia w zakresie zabezpieczeń KnowBe4** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/knowbe4-tutorial/tutorial_knowbe4_samlbase.png)

1. Na **KnowBe4 zabezpieczeń świadomości szkolenia domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![KnowBe4 zabezpieczeń świadomości szkolenia domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/knowbe4-tutorial/tutorial_knowbe4_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<companyname>.KnowBe4.com/auth/saml/<instancename>`

    > [!NOTE] 
    > Wartość adresu URL logowania nie jest prawdziwe. Zaktualizuj tę wartość przy użyciu rzeczywisty adres URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta szkolenia świadomości zabezpieczeń KnowBe4](mailto:support@KnowBe4.com) aby zyskać tę wartość. 

    b. W **identyfikator** polu tekstowym wpisz wartość ciągu: `KnowBe4`

    > [!NOTE]
    >To jest uwzględniana wielkość liter

1. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Raw)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/knowbe4-tutorial/tutorial_knowbe4_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/knowbe4-tutorial/tutorial_general_400.png)

1. Na **konfiguracji szkolenia świadomości zabezpieczeń KnowBe4** , kliknij przycisk **skonfigurować KnowBe4 świadomości szkolenia w zakresie zabezpieczeń** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfiguracja szkolenia świadomości zabezpieczeń KnowBe4](./media/knowbe4-tutorial/tutorial_knowbe4_configure.png) 

1. Aby skonfigurować logowanie jednokrotne na **świadomości szkolenia w zakresie zabezpieczeń KnowBe4** stronie, musisz wysłać pobrany **certyfikatu (Raw)**, **adres URL wylogowania, identyfikator jednostki języka SAML i SAML logowania jednokrotnego Adres URL usługi** do [zespołem pomocy technicznej klienta szkolenia świadomości zabezpieczeń KnowBe4](mailto:support@KnowBe4.com).

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/knowbe4-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/knowbe4-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/knowbe4-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/knowbe4-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-knowbe4-security-awareness-training-test-user"></a>Tworzenie użytkownika testowego szkolenia w zakresie rozpoznawania KnowBe4 zabezpieczeń

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon KnowBe4 szkolenia świadomości i zabezpieczeń. Rozpoznawanie szkolenia w zakresie zabezpieczeń KnowBe4 obsługę just-in-time, który jest domyślnie włączona.

Brak elementu akcji dla Ciebie w tej sekcji. Nowy użytkownik jest tworzony podczas próby dostępu szkolenia w zakresie rozpoznawania KnowBe4 zabezpieczeń, jeśli go jeszcze nie istnieje. 

>[!NOTE]
>Jeśli potrzebujesz ręcznie utworzyć użytkownika, musisz skontaktować się z [zespołem pomocy technicznej świadomości szkolenia w zakresie zabezpieczeń KnowBe4](mailto:support@KnowBe4.com).

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do szkolenia w zakresie rozpoznawania KnowBe4 zabezpieczeń.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon do szkolenia w zakresie rozpoznawania KnowBe4 zabezpieczeń, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **świadomości szkolenia w zakresie zabezpieczeń KnowBe4**.

    ![Link szkolenia w zakresie rozpoznawania KnowBe4 zabezpieczeń na liście aplikacji](./media/knowbe4-tutorial/tutorial_knowbe4_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.
  
Po kliknięciu kafelka szkolenia w zakresie rozpoznawania KnowBe4 zabezpieczeń w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do szkolenia w zakresie rozpoznawania KnowBe4 zabezpieczeń aplikacji. 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/knowbe4-tutorial/tutorial_general_01.png
[2]: ./media/knowbe4-tutorial/tutorial_general_02.png
[3]: ./media/knowbe4-tutorial/tutorial_general_03.png
[4]: ./media/knowbe4-tutorial/tutorial_general_04.png

[100]: ./media/knowbe4-tutorial/tutorial_general_100.png

[200]: ./media/knowbe4-tutorial/tutorial_general_200.png
[201]: ./media/knowbe4-tutorial/tutorial_general_201.png
[202]: ./media/knowbe4-tutorial/tutorial_general_202.png
[203]: ./media/knowbe4-tutorial/tutorial_general_203.png

