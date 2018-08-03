---
title: 'Samouczek: Integracja usługi Azure Active Directory z SuccessFactors | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i SuccessFactors.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 870a753a8f10255a602616ab54234b295f4d6e13
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431487"
---
# <a name="tutorial-azure-active-directory-integration-with-successfactors"></a>Samouczek: Integracja usługi Azure Active Directory z SuccessFactors

W tym samouczku dowiesz się, jak zintegrować SuccessFactors w usłudze Azure Active Directory (Azure AD).

Integrowanie SuccessFactors z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do SuccessFactors.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do SuccessFactors (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą SuccessFactors, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- SuccessFactors logowania jednokrotnego włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie SuccessFactors z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-successfactors-from-the-gallery"></a>Dodawanie SuccessFactors z galerii
Aby skonfigurować integrację SuccessFactors w usłudze Azure AD, należy dodać SuccessFactors z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać SuccessFactors z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

1. W polu wyszukiwania wpisz **SuccessFactors**, wybierz opcję **SuccessFactors** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![SuccessFactors na liście wyników](./media/successfactors-tutorial/tutorial_successfactors_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą SuccessFactors w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w SuccessFactors do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w SuccessFactors musi można ustanowić.

W SuccessFactors, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą SuccessFactors, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego SuccessFactors](#create-a-successfactors-test-user)**  — aby odpowiednikiem Britta Simon w SuccessFactors połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji SuccessFactors.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z SuccessFactors, wykonaj następujące czynności:**

1. W witrynie Azure portal na **SuccessFactors** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/successfactors-tutorial/tutorial_successfactors_samlbase.png)

1. Na **SuccessFactors domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![SuccessFactors domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/successfactors-tutorial/tutorial_successfactors_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca:
    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.eu`|

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca:
    | |
    |--|
    | `https://www.successfactors.com/<companyname>`|
    | `https://www.successfactors.com`|
    | `https://<companyname>.successfactors.eu`|
    | `https://www.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://hcm4preview.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.eu`|
    | `https://www.successfactors.cn`|
    | `https://www.successfactors.cn/<companyname>`|

    c. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca:
    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.successfactors.com`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.successfactors.eu`|
    | `https://<companyname>.sapsf.eu`|
    | `https://<companyname>.sapsf.eu/<companyname>`|
    | `https://<companyname>.sapsf.cn`|
    | `https://<companyname>.sapsf.cn/<companyname>`|
         
    > [!NOTE] 
    > Te wartości są prawdziwe. Rzeczywisty identyfikator, adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta SuccessFactors](https://www.successfactors.com/en_us/support.html) do uzyskania tych wartości. 

1. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/successfactors-tutorial/tutorial_successfactors_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/successfactors-tutorial/tutorial_general_400.png)
    
1. Na **konfiguracji SuccessFactors** , kliknij przycisk **skonfigurować SuccessFactors** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/successfactors-tutorial/tutorial_successfactors_configure.png) 

1. W oknie przeglądarki internetowej innej, zaloguj się do Twojej **portalu administracyjnego SuccessFactors** jako administrator.
    
1. Odwiedź stronę **zabezpieczeń aplikacji** z natywną obsługą **pojedynczy znak w funkcji**. 

1. Umieść dowolną wartość w **tokenu resetowania** i kliknij przycisk **zapisywanie tokenu** do włączenia funkcji logowania jednokrotnego SAML.
   
    ![Konfigurowanie logowania jednokrotnego w aplikacji po stronie][11]

    > [!NOTE] 
    > Ta wartość jest używana jako przełącznika wł. / wył. Jeśli dowolna wartość jest zapisywany, logowania jednokrotnego SAML ma wartość ON. Jeśli pustej wartości zostanie zapisany logowania jednokrotnego SAML został WYŁĄCZONY.

1. Natywne Poniższy zrzut ekranu i wykonaj następujące czynności:
   
    ![Konfigurowanie logowania jednokrotnego w aplikacji po stronie][12]
   
    a. Wybierz **logowania jednokrotnego SAML w wersji 2** przycisk radiowy
   
    b. Ustaw **nazwa jednostki potwierdzające SAML**(na przykład wystawcy SAML + nazwa firmy).
   
    c. W **adres URL wystawcy** pola tekstowego, Wklej **identyfikator jednostki SAML** wartości, które zostały skopiowane z witryny Azure portal.
   
    d. Wybierz **odpowiedzi (klienta wygenerowanych/protokołu IdP/AP)** jako **wymagają podpisu obowiązkowe**.
   
    e. Wybierz **włączone** jako **Włącz flagę SAML**.
   
    f. Wybierz **nie** jako **podpisu żądania logowania (SF wygenerowanych/SP/RP)**.
   
    g. Wybierz **profilu przeglądarki lub używanego po nim** jako **profilu SAML**.
   
    h. Wybierz **nie** jako **wymusić nieprawidłowy okres certyfikatu**.
   
    i. Skopiuj zawartość pliku certyfikatu pobrany z witryny Azure portal, a następnie wklej go do **certyfikatu weryfikacji SAML** pola tekstowego.

    > [!NOTE] 
    > Zawartość certyfikatu musi mieć rozpoczynać się certyfikatu i na końcu tagi certyfikatu.

1. Przejdź do języka SAML w wersji 2, a następnie wykonaj następujące czynności:
   
    ![Konfigurowanie logowania jednokrotnego w aplikacji po stronie][13]
   
    a. Wybierz **tak** jako **obsługuje zainicjowanego przez dostawcę usług globalnych wylogowania**.
   
    b. W **globalny adres URL usługi wylogowania (miejsce docelowe LogoutRequest)** pola tekstowego, Wklej **adres URL wylogowania** wartość, która została skopiowana tworzą witryny Azure portal.
   
    c. Wybierz **nie** jako **wymagają sp należy zaszyfrować wszystkie elementu NameID**.
   
    d. Wybierz **nieokreślony** jako **Format identyfikatora NameID**.
   
    e. Wybierz **tak** jako **Włącz sp zainicjowane logowania (AuthnRequest)**.
   
    f. W **żądań wysłania jako wystawca firmie** pola tekstowego, Wklej **SAML pojedynczego logowania jednokrotnego usługi adresu URL** wartości, które zostały skopiowane z witryny Azure portal.

1. Wykonaj następujące kroki, aby wprowadzić nazwy logowania użytkowników bez uwzględniania wielkości liter.
   
    ![Konfigurowanie logowania jednokrotnego][29]
    
    a. Odwiedź stronę **ustawienia firmowe**(w pobliżu dolnej części).
   
    b. Zaznacz pole wyboru obok **włączyć nazwy użytkownika inne niż wielkości liter**.
   
    c.Click **Zapisz**.
   
    > [!NOTE] 
    > Jeśli spróbujesz włączyć tę opcję, system sprawdza, jeśli tworzy zduplikowane nazwy logowania języka SAML. Na przykład, jeśli klient ma nazw użytkowników Użytkownik1 i użytkownik user1. Zdjęciu rozróżnianie wielkości liter sprawia, że te duplikaty. System oferuje komunikat o błędzie i nie włączać tej funkcji. Klient musi zmienić jedną z nazw użytkowników, aby została wpisana różne.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/successfactors-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/successfactors-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/successfactors-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/successfactors-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-successfactors-test-user"></a>Tworzenie użytkownika testowego SuccessFactors

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do SuccessFactors, musi być obsługiwana w SuccessFactors.  
W przypadku SuccessFactors Inicjowanie obsługi administracyjnej jest zadanie ręczne.

Aby uzyskać użytkownicy utworzeni w SuccessFactors, musisz skontaktować się z [zespołu pomocy technicznej SuccessFactors](https://www.successfactors.com/en_us/support.html).

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do SuccessFactors.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon SuccessFactors, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **SuccessFactors**.

    ![Link SuccessFactors na liście aplikacji](./media/successfactors-tutorial/tutorial_successfactors_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka SuccessFactors w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji SuccessFactors.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/successfactors-tutorial/tutorial_general_01.png
[2]: ./media/successfactors-tutorial/tutorial_general_02.png
[3]: ./media/successfactors-tutorial/tutorial_general_03.png
[4]: ./media/successfactors-tutorial/tutorial_general_04.png

[11]: ./media/successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/successfactors-tutorial/tutorial_successfactors_09.png
[14]: ./media/successfactors-tutorial/tutorial_general_05.png
[15]: ./media/successfactors-tutorial/tutorial_general_06.png
[29]: ./media/successfactors-tutorial/tutorial_successfactors_10.png

[100]: ./media/successfactors-tutorial/tutorial_general_100.png

[200]: ./media/successfactors-tutorial/tutorial_general_200.png
[201]: ./media/successfactors-tutorial/tutorial_general_201.png
[202]: ./media/successfactors-tutorial/tutorial_general_202.png
[203]: ./media/successfactors-tutorial/tutorial_general_203.png

