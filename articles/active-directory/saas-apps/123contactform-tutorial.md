---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą 123ContactForm | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i 123ContactForm.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 5211910a-ab96-4709-959a-524c4d57c43e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 450fc8df69eb5ffb99cb6548e80cd4d3628e7419
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54819445"
---
# <a name="tutorial-azure-active-directory-integration-with-123contactform"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą 123ContactForm

W tym samouczku dowiesz się, jak zintegrować 123ContactForm w usłudze Azure Active Directory (Azure AD).

Integrowanie 123ContactForm z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do 123ContactForm
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do 123ContactForm (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą 123ContactForm, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- 123ContactForm logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie 123ContactForm z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-123contactform-from-the-gallery"></a>Dodawanie 123ContactForm z galerii
Aby skonfigurować integrację 123ContactForm w usłudze Azure AD, należy dodać 123ContactForm z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać 123ContactForm z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **123ContactForm**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/123contactform-tutorial/tutorial_123contactform_search.png)

5. W panelu wyników wybierz **123ContactForm**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/123contactform-tutorial/tutorial_123contactform_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą 123ContactForm w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w 123ContactForm do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w 123ContactForm musi można ustanowić.

W 123ContactForm, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą 123ContactForm, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego 123ContactForm](#creating-a-123contactform-test-user)**  — aby mają odpowiednika w pozycji Britta simon w 123ContactForm połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji 123ContactForm.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z 123ContactForm, wykonaj następujące czynności:**

1. W witrynie Azure portal na **123ContactForm** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/123contactform-tutorial/tutorial_123contactform_samlbase.png)

3. Na **123ContactForm domena i adresy URL** sekcji, jeśli chcesz skonfigurować aplikację w **tryb inicjowane przez dostawcę tożsamości**, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/123contactform-tutorial/url1.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://www.123contactform.com/saml/azure_ad/<tenant_id>/metadata`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://www.123contactform.com/saml/azure_ad/<tenant_id>/acs`

4. Jeśli chcesz skonfigurować aplikację w **SP zainicjowano tryb**, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/123contactform-tutorial/url2.png)

    a. Kliknij przycisk **Pokaż zaawansowane ustawienia adresu URL** opcji

    b. W **na adres URL logowania** pole tekstowe, wpisz adres URL jako: `https://www.123contactform.com/saml/azure_ad/<tenant_id>/sso`

    > [!NOTE] 
    > Te wartości nie są prawdziwe. Należy zaktualizować te wartości z rzeczywistych adresów URL i identyfikator, który zostało wyjaśnione w dalszej części tego samouczka.
    
5. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/123contactform-tutorial/tutorial_123contactform_certificate.png) 

6. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/123contactform-tutorial/tutorial_general_400.png)

7. Aby skonfigurować logowanie jednokrotne na **123ContactForm** strony, przejdź do [ https://www.123contactform.com/form-2709121/ ](https://www.123contactform.com/form-2709121/) i wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/123contactform-tutorial/submit.png) 

    a. W **E-mail** pole tekstowe, wpisz adres e-mail tj użytkownika **BrittaSimon@Contoso.com**.

    b. Kliknij przycisk **przekazywanie** i przejdź do pliku XML metadanych, który został pobrany z witryny Azure portal.

    c. Kliknij przycisk **Prześlij formularz**.

8. Na **Konfigurowanie ustawień aplikacji programu Microsoft Azure AD — logowanie jednokrotne -** wykonaj następujące czynności:
    
    ![Konfigurowanie logowania jednokrotnego](./media/123contactform-tutorial/url3.png)

    a. Jeśli chcesz skonfigurować aplikację w **tryb inicjowane przez dostawcę tożsamości**, kopia **identyfikator** wartości dla swojego wystąpienia, a następnie wklej je **identyfikator** polu tekstowym w  **123ContactForm domena i adresy URL** sekcji w witrynie Azure portal.
    
    b. Jeśli chcesz skonfigurować aplikację w **tryb inicjowane przez dostawcę tożsamości**, kopia **adres URL odpowiedzi** wartości dla swojego wystąpienia, a następnie wklej je **adres URL odpowiedzi** polu tekstowym w  **123ContactForm domena i adresy URL** sekcji w witrynie Azure portal.

    c. Jeśli chcesz skonfigurować aplikację w **SP zainicjowano tryb**, kopia **adres URL logowania na** wartości dla swojego wystąpienia, a następnie wklej je **na adres URL logowania** polu tekstowym w  **123ContactForm domena i adresy URL** sekcji w witrynie Azure portal.

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/123contactform-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/123contactform-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/123contactform-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/123contactform-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-123contactform-test-user"></a>Tworzenie użytkownika testowego 123ContactForm

Aplikacja obsługuje aprowizowanie użytkowników typu Just In Time. Po uwierzytelnieniu użytkownicy zostaną automatycznie utworzeni w aplikacji.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do 123ContactForm.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon 123ContactForm, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **123ContactForm**.

    ![Konfigurowanie logowania jednokrotnego](./media/123contactform-tutorial/tutorial_123contactform_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka 123ContactForm w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji 123ContactForm.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/123contactform-tutorial/tutorial_general_01.png
[2]: ./media/123contactform-tutorial/tutorial_general_02.png
[3]: ./media/123contactform-tutorial/tutorial_general_03.png
[4]: ./media/123contactform-tutorial/tutorial_general_04.png

[100]: ./media/123contactform-tutorial/tutorial_general_100.png

[200]: ./media/123contactform-tutorial/tutorial_general_200.png
[201]: ./media/123contactform-tutorial/tutorial_general_201.png
[202]: ./media/123contactform-tutorial/tutorial_general_202.png
[203]: ./media/123contactform-tutorial/tutorial_general_203.png

