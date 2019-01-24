---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Optimizely | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Optimizely.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 28ef03e1-9aad-4301-af97-d94e853edc74
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2018
ms.author: jeedes
ms.openlocfilehash: 72e0f19a665b1e8cc91939ae24cc71341b5f1674
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54819020"
---
# <a name="tutorial-azure-active-directory-integration-with-optimizely"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Optimizely

W tym samouczku dowiesz się, jak zintegrować Optimizely w usłudze Azure Active Directory (Azure AD).

Integrowanie Optimizely z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Optimizely
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do Optimizely (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Optimizely, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Optimizely logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym.
Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Optimizely z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-optimizely-from-the-gallery"></a>Dodawanie Optimizely z galerii

Aby skonfigurować integrację Optimizely w usłudze Azure AD, należy dodać Optimizely z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Optimizely z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Optimizely**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/optimizely-tutorial/tutorial_optimizely_search.png)

5. W panelu wyników wybierz **Optimizely**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/optimizely-tutorial/tutorial_optimizely_addfromgallery.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

W tej sekcji Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą Optimizely w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Optimizely do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Optimizely musi można ustanowić.

Ustanowieniu tej relacji łączy, przypisując wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** w Optimizely.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Optimizely, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego Optimizely](#creating-an-optimizely-test-user)**  — aby odpowiednikiem Britta Simon w Optimizely połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Optimizely.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Optimizely, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Optimizely** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.

    ![Konfigurowanie logowania jednokrotnego](./media/optimizely-tutorial/tutorial_optimizely_samlbase.png)

3. Na **Optimizely domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/optimizely-tutorial/tutorial_optimizely_url.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://app.optimizely.net/<instance name>`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca:  `urn:auth0:optimizely:contoso`

    > [!NOTE]
    > Te wartości nie są rzeczywiste. Wartość zostanie zaktualizowana o rzeczywisty adres URL logowania i identyfikator, który zostało wyjaśnione w dalszej części tego samouczka.

4. Aplikacja Optimizely oczekuje twierdzenia SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Możesz zarządzać wartości te atrybuty z "**atrybutów użytkownika**" sekcji na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład tego działania.
    
    ![Konfigurowanie logowania jednokrotnego](./media/optimizely-tutorial/tutorial_optimizely_attribute.png)
    
5. Kliknij przycisk **Wyświetl i Edytuj wszystkie inne atrybuty użytkownika** pola wyboru w **atrybutów użytkownika** sekcji, aby rozwinąć atrybutów. Wykonaj następujące czynności na każdym z atrybutów wyświetlanych-

    | Nazwa atrybutu | Wartość atrybutu |
    | ---------------| --------------- |
    | email | user.mail |

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](./media/optimizely-tutorial/tutorial_attribute_04.png)

    ![Konfigurowanie logowania jednokrotnego](./media/optimizely-tutorial/tutorial_attribute_05.png)

    b. W **nazwa** polu tekstowym wpisz **nazwa atrybutu** wyświetlanego dla tego wiersza.

    c. Z **wartość** wpisz wartość atrybutu wyświetlanego dla tego wiersza.

    d. Kliknij przycisk **OK**.

6. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/optimizely-tutorial/tutorial_optimizely_certificate.png)

7. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/optimizely-tutorial/tutorial_general_400.png)

8. Na **konfiguracji Optimizely** , kliknij przycisk **skonfigurować Optimizely** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/optimizely-tutorial/tutorial_optimizely_configure.png)

9. Aby skonfigurować logowanie jednokrotne na **Optimizely** po stronie, skontaktuj się z menedżerem Optimizely i zapewnić pobrany **certyfikat (Base64)**, i **SAML pojedynczego logowania jednokrotnego usługi adresu URL**.

10. W odpowiedzi na wiadomości e-mail Optimizely pozwala na adres URL logowania (SSO zainicjowanego przez dostawcę usług) i wartości Identyfikator usługi dostawcy jednostki.

    a. Kopiuj **zainicjowanego przez dostawcę usług adres URL logowania jednokrotnego** podana przez Optimizely i Wklej w **na adres URL logowania** polu tekstowym w **Optimizely domena i adresy URL** sekcji w witrynie Azure portal.

    b. Kopiuj **identyfikator jednostki dostawcy usługi** podana przez Optimizely i Wklej w **identyfikator** polu tekstowym w **Optimizely domena i adresy URL** sekcji w witrynie Azure portal.

11. W oknie innej przeglądarki logowanie jednokrotne do aplikacji Optimizely.

12. Możesz nazwa konta w prawym górnym rogu kliknij pozycję i następnie **ustawienia konta**.

    ![Usługa Azure AD logowanie jednokrotne](./media/optimizely-tutorial/tutorial_optimizely_09.png)

13. Zaznacz pole wyboru na karcie konto **włączyć logowanie Jednokrotne** w ramach rejestracji jednokrotnej w **Przegląd** sekcji.
  
    ![Usługa Azure AD logowanie jednokrotne](./media/optimizely-tutorial/tutorial_optimizely_10.png)

14. Kliknij pozycję **Zapisz**

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/optimizely-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/optimizely-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/optimizely-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/optimizely-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** obiektu Britta Simon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.

### <a name="creating-an-optimizely-test-user"></a>Tworzenie użytkownika testowego Optimizely

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w Optimizely.

1. Na stronie głównej wybierz **współpracowników** kartę.

2. Aby dodać nowe współpracownika do projektu, kliknij **nowe współpracownika**.
   
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/optimizely-tutorial/create_aaduser_10.png)

3. Podaj adres e-mail i przypisać im rolę. Kliknij przycisk **zaprosić**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/optimizely-tutorial/create_aaduser_11.png)

4. Otrzyma wiadomość e-mail z zaproszeniem. Przy użyciu adresu e-mail, muszą oni zalogować się do Optimizely.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Optimizely.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Optimizely, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji wybierz **Optimizely**.

    ![Konfigurowanie logowania jednokrotnego](./media/optimizely-tutorial/tutorial_optimizely_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.

### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Optimizely w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Optimizely.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/optimizely-tutorial/tutorial_general_01.png
[2]: ./media/optimizely-tutorial/tutorial_general_02.png
[3]: ./media/optimizely-tutorial/tutorial_general_03.png
[4]: ./media/optimizely-tutorial/tutorial_general_04.png

[100]: ./media/optimizely-tutorial/tutorial_general_100.png

[200]: ./media/optimizely-tutorial/tutorial_general_200.png
[201]: ./media/optimizely-tutorial/tutorial_general_201.png
[202]: ./media/optimizely-tutorial/tutorial_general_202.png
[203]: ./media/optimizely-tutorial/tutorial_general_203.png