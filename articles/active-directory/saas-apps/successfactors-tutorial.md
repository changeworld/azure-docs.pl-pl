---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą SuccessFactors | Dokumentacja firmy Microsoft'
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
ms.date: 11/13/2018
ms.author: jeedes
ms.openlocfilehash: 467db4046c0600142338dcfa39e136f45255caba
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/02/2019
ms.locfileid: "53976753"
---
# <a name="tutorial-azure-active-directory-integration-with-successfactors"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą SuccessFactors

W tym samouczku dowiesz się, jak zintegrować SuccessFactors w usłudze Azure Active Directory (Azure AD).

Integrowanie SuccessFactors z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do SuccessFactors.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do SuccessFactors (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą SuccessFactors, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- SuccessFactors logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie SuccessFactors z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-successfactors-from-the-gallery"></a>Dodawanie SuccessFactors z galerii

Aby skonfigurować integrację SuccessFactors w usłudze Azure AD, należy dodać SuccessFactors z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać SuccessFactors z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

4. W polu wyszukiwania wpisz **SuccessFactors**, wybierz opcję **SuccessFactors** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![SuccessFactors na liście wyników](./media/successfactors-tutorial/tutorial_successfactors_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą SuccessFactors w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w SuccessFactors do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w SuccessFactors musi można ustanowić.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą SuccessFactors, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego SuccessFactors](#creating-a-successfactors-test-user)**  — aby odpowiednikiem Britta Simon w SuccessFactors połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji SuccessFactors.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z SuccessFactors, wykonaj następujące czynności:**

1. W witrynie Azure portal na **SuccessFactors** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

2. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, kliknij przycisk **wybierz** dla **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![Konfigurowanie logowania jednokrotnego](common/tutorial_general_301.png)

3. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** ikonę, aby otworzyć **podstawową konfigurację protokołu SAML** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](common/editconfigure.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

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
    > Te wartości nie są prawdziwe. Rzeczywisty adres URL logowania, identyfikator i adres URL odpowiedzi, należy zaktualizować te wartości. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta SuccessFactors](https://www.successfactors.com/content/ssf-site/en/support.html) do uzyskania tych wartości. 

5. Na **certyfikat podpisywania SAML** strony w **certyfikat podpisywania SAML** kliknij **Pobierz** można pobrać **certyfikat (Base64)**, a następnie zapisz plik certyfikatu na komputerze.

    ![Link do pobierania certyfikatu](./media/successfactors-tutorial/tutorial_successfactors_certificate.png) 

6. Na **Konfigurowanie SuccessFactors** sekcji, skopiuj odpowiedni adres URL, zgodnie z wymaganiami.

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

    ![Konfiguracja SuccessFactors](common/configuresection.png)

7. W oknie przeglądarki internetowej innej, zaloguj się do Twojej **portalu administracyjnego SuccessFactors** jako administrator.
    
8. Odwiedź stronę **zabezpieczeń aplikacji** z natywną obsługą **pojedynczy znak w funkcji**. 

9. Umieść dowolną wartość w **tokenu resetowania** i kliknij przycisk **zapisywanie tokenu** do włączenia funkcji logowania jednokrotnego SAML.
   
    ![Konfigurowanie logowania jednokrotnego w aplikacji po stronie][11]

    > [!NOTE] 
    > Ta wartość jest używana jako przełącznika wł. / wył. Jeśli dowolna wartość jest zapisywany, logowania jednokrotnego SAML ma wartość ON. Jeśli pustej wartości zostanie zapisany logowania jednokrotnego SAML został WYŁĄCZONY.

10. Natywne Poniższy zrzut ekranu i wykonaj następujące czynności:
   
    ![Konfigurowanie logowania jednokrotnego w aplikacji po stronie][12]
   
    a. Wybierz **logowania jednokrotnego SAML w wersji 2** przycisk radiowy
   
    b. Ustaw **nazwa jednostki potwierdzające SAML**(na przykład wystawcy SAML + nazwa firmy).
   
    c. W **adres URL wystawcy** pola tekstowego, Wklej **usługi Azure AD identyfikator** wartości, które zostały skopiowane z witryny Azure portal.
   
    d. Wybierz **asercji** jako **wymagają podpisu obowiązkowe**.
   
    e. Wybierz **włączone** jako **Włącz flagę SAML**.
   
    f. Wybierz **nie** jako **podpisu żądania logowania (SF wygenerowanych/SP/RP)**.
   
    g. Wybierz **profilu przeglądarki lub używanego po nim** jako **profilu SAML**.
   
    h. Wybierz **nie** jako **wymusić nieprawidłowy okres certyfikatu**.
   
    i. Skopiuj zawartość pliku certyfikatu pobrany z witryny Azure portal, a następnie wklej go do **certyfikatu weryfikacji SAML** pola tekstowego.

    > [!NOTE] 
    > Zawartość certyfikatu musi mieć rozpoczynać się certyfikatu i na końcu tagi certyfikatu.

11. Przejdź do języka SAML w wersji 2, a następnie wykonaj następujące czynności:
   
    ![Konfigurowanie logowania jednokrotnego w aplikacji po stronie][13]
   
    a. Wybierz **tak** jako **obsługuje zainicjowanego przez dostawcę usług globalnych wylogowania**.
   
    b. W **globalny adres URL usługi wylogowania (miejsce docelowe LogoutRequest)** pola tekstowego, Wklej **adres URL wylogowania** wartość, która została skopiowana tworzą witryny Azure portal.
   
    c. Wybierz **nie** jako **wymagają sp należy zaszyfrować wszystkie elementu NameID**.
   
    d. Wybierz **nieokreślony** jako **Format identyfikatora NameID**.
   
    e. Wybierz **tak** jako **Włącz sp zainicjowane logowania (AuthnRequest)**.
   
    f. W **żądań wysłania jako wystawca firmie** pola tekstowego, Wklej **SAML pojedynczego logowania jednokrotnego usługi adresu URL** wartości, które zostały skopiowane z witryny Azure portal.

12. Wykonaj następujące kroki, aby wprowadzić nazwy logowania użytkowników bez uwzględniania wielkości liter.
   
    ![Konfigurowanie logowania jednokrotnego][29]
    
    a. Odwiedź stronę **ustawienia firmowe**(w pobliżu dolnej części).
   
    b. Zaznacz pole wyboru obok **włączyć nazwy użytkownika inne niż wielkości liter**.
   
    c.Click **Zapisz**.
   
    > [!NOTE] 
    > Jeśli spróbujesz włączyć tę opcję, system sprawdza, jeśli tworzy zduplikowane nazwy logowania języka SAML. Na przykład, jeśli klient ma nazw użytkowników Użytkownik1 i użytkownik user1. Zdjęciu rozróżnianie wielkości liter sprawia, że te duplikaty. System oferuje komunikat o błędzie i nie włączać tej funkcji. Klient musi zmienić jedną z nazw użytkowników, aby została wpisana różne.

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Utwórz użytkownika usługi Azure AD][100]

2. Wybierz **nowego użytkownika** w górnej części ekranu.

    ![Tworzenie użytkownika testowego usługi Azure AD](common/create_aaduser_01.png) 

3. We właściwościach użytkownika wykonaj następujące czynności.

    ![Tworzenie użytkownika testowego usługi Azure AD](common/create_aaduser_02.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    c. Wybierz **właściwości**, wybierz opcję **hasło Show** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w polu hasło.

    d. Wybierz pozycję **Utwórz**.

### <a name="creating-a-successfactors-test-user"></a>Tworzenie użytkownika testowego SuccessFactors

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do SuccessFactors, musi być obsługiwana w SuccessFactors.  
W przypadku SuccessFactors Inicjowanie obsługi administracyjnej jest zadanie ręczne.

Aby uzyskać użytkownicy utworzeni w SuccessFactors, musisz skontaktować się z [zespołu pomocy technicznej SuccessFactors](https://www.successfactors.com/content/ssf-site/en/support.html).

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do SuccessFactors.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji wybierz **SuccessFactors**.

    ![Konfigurowanie logowania jednokrotnego](./media/successfactors-tutorial/tutorial_successfactors_app.png)

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. W **użytkowników i grup** okna dialogowego wybierz **Britta Simon** na liście użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

6. W **Dodaj przydziału** okna dialogowego wybierz **przypisać** przycisku.

### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka SuccessFactors w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji SuccessFactors.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
[11]: ./media/successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/successfactors-tutorial/tutorial_successfactors_09.png
[29]: ./media/successfactors-tutorial/tutorial_successfactors_10.png
