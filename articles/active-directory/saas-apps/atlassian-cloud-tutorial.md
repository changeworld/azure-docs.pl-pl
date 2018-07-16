---
title: 'Samouczek: Integracja usługi Azure Active Directory z chmurą Atlassian | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między środowiskiem usługi Azure Active Directory i Atlassian chmurą.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: jeedes
ms.openlocfilehash: f0ad879bb084a8d3a50a0934557eae64621c0160
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39054261"
---
# <a name="tutorial-azure-active-directory-integration-with-atlassian-cloud"></a>Samouczek: Integracja usługi Azure Active Directory z chmurą Atlassian

W tym samouczku dowiesz się, jak zintegrować Atlassian chmury z usługi Azure Active Directory (Azure AD).

Integrowanie Atlassian chmury z usługi Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do chmury Atlassian.
- Można włączyć użytkowników, aby zalogować się automatycznie (logowanie jednokrotne) do chmury Atlassian przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji, witryny Azure portal.

Aby uzyskać więcej informacji na temat oprogramowania jako usługi (SaaS) integracji aplikacji z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu chmury Atlassian, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD.
- Aby włączyć zabezpieczeń Assertion Markup Language (SAML) logowanie jednokrotne dla produktów w chmurze Atlassian, musisz skonfigurować Identity Manager. Dowiedz się więcej o [programu Identity Manager]( https://www.atlassian.com/enterprise/cloud/identity-manager).

> [!NOTE]
> Podczas testowania kroki opisane w tym samouczku, zaleca się nie używać do środowiska produkcyjnego.

Aby przetestować czynności w ramach tego samouczka, wykonaj te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym.
Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

* Dodawanie Atlassian chmury z galerii
* Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="add-atlassian-cloud-from-the-gallery"></a>Dodaj Atlassian chmury z galerii
Aby skonfigurować integrację Atlassian chmury z usługą Azure AD, Dodaj Atlassian chmury z galerii z listą zarządzanych aplikacji SaaS, wykonując następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com), w okienku po lewej stronie wybierz **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory][1]

2. Wybierz **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.

    ![W okienku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać aplikację, wybierz pozycję **nową aplikację**.

    !["Nowa aplikacja" przycisk][3]

4. W polu wyszukiwania wpisz **chmury Atlassian**, na liście wyników wybierz **chmury Atlassian**, a następnie wybierz pozycję **Dodaj**.

    ![Chmura Atlassian na liście wyników](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz skonfigurować i przetestować usługi Azure AD logowanie jednokrotne za pomocą Atlassian chmury opartego na nazwie użytkownika testowego *Britta Simon*.

Dla logowania jednokrotnego do pracy usługa Azure AD wymaga do identyfikowania użytkownika Atlassian chmury i jego odpowiednika w usłudze Azure AD. Innymi słowy należy ustanowić relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w chmurze Atlassian.

Do ustanawiania relacji łączy, przypisz jako chmura Atlassian *Username* tę samą wartość, która jest przypisana do usługi Azure AD *nazwa_użytkownika*.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu chmury Atlassian, należy wykonać bloki konstrukcyjne, w poniższych sekcjach.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i skonfigurować logowanie jednokrotne w aplikacji w chmurze Atlassian.

Aby skonfigurować usługi Azure AD logowania jednokrotnego przy użyciu chmury Atlassian, wykonaj następujące czynności:

1. W witrynie Azure portal w **chmury Atlassian** okienko integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. W **logowanie jednokrotne** okna w **tryb rejestracji jednokrotnej** wybierz opcję **opartej na SAML logowania jednokrotnego**.

    ![Okno rejestracji jednokrotnej](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_samlbase.png)

3. Aby skonfigurować aplikację w trybie inicjowane przez dostawcę tożsamości, w obszarze **Atlassian chmury domena i adresy URL**, wykonaj następujące czynności:

    ![Adresy URL i domena chmury Atlassian pojedynczy informacje logowania jednokrotnego](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_url.png)
    
    a. W **identyfikator** wpisz **`https://auth.atlassian.com/saml/<unique ID>`**.
    
    b. W **adres URL odpowiedzi** wpisz **`https://auth.atlassian.com/login/callback?connection=saml-<unique ID>`**.

    c. W **tan przekaźnika** wpisz adres URL z następującą składnią: **`https://<instancename>.atlassian.net`**.

4. Aby skonfigurować aplikację w trybie zainicjowanego przez dostawcę usług, zaznacz **Pokaż zaawansowane ustawienia adresu URL** a następnie w **adres URL logowania** wpisz adres URL z następującą składnią: **`https://<instancename>.atlassian.net`** .

    ![Adresy URL i domena chmury Atlassian pojedynczy informacje logowania jednokrotnego](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_url1.png)

    > [!NOTE]
    > Powyższe wartości nie są prawdziwe. Zaktualizuj je z rzeczywistego identyfikatora, adres URL odpowiedzi i logowania jednokrotnego wartości adresu URL. Rzeczywiste wartości można uzyskać z ekranu konfiguracji SAML Atlassian w chmurze. Wyjaśnijmy, wartości w dalszej części tego samouczka.

5. W obszarze **certyfikat podpisywania SAML**, wybierz opcję **Certificate(Base64)**, a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_certificate.png)

6. Aplikacji w chmurze Atlassian spodziewa się znaleźć twierdzenia SAML w określonym formacie, który wymaga dodania mapowania atrybutów niestandardowych konfiguracji atrybuty tokenu języka SAML. 

    Domyślnie **identyfikator użytkownika** wartość jest mapowany na user.userprincipalname. Zmień tę wartość do mapowania user.mail. Możesz również innych odpowiednią wartość zgodnie z ustawieniami w Twojej organizacji, ale w większości przypadków, powinny działać wiadomości e-mail.

    ![Link pobierania certyfikatu](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_attribute.png)

7. Wybierz pozycję **Zapisz**.

    ![Konfigurowanie logowania jednokrotnego przycisk zapisywania](./media/atlassian-cloud-tutorial/tutorial_general_400.png)

8. Aby otworzyć **Konfigurowanie logowania jednokrotnego** okna w **Konfiguracja chmury Atlassian** zaznacz **Konfigurowanie chmury Atlassian**.

9. W **krótki** sekcji, skopiuj **identyfikator jednostki SAML** i **SAML pojedynczego logowania jednokrotnego usługi adresu URL**.

    ![Konfiguracja chmury Atlassian](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_configure.png)

10. Aby uzyskać logowanie Jednokrotne skonfigurowane pod kątem swojej aplikacji, zaloguj się do portalu Atlassian przy użyciu poświadczeń administratora.

11. Należy sprawdzić domeny przed przejściem do skonfigurowania logowania jednokrotnego. Aby uzyskać więcej informacji, zobacz [weryfikacji domeny Atlassian](https://confluence.atlassian.com/cloud/domain-verification-873871234.html) dokumentu.

12. W okienku po lewej stronie wybierz **SAML logowania jednokrotnego**. Jeśli nie zostało to jeszcze zrobione, należy uzyskać subskrypcję do Atlassian Identity Manager.

    ![Konfigurowanie logowania jednokrotnego](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_11.png)

13. W **plik konfiguracji SAML Dodaj** okna, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_12.png)

    a. W **dostawcy tożsamości identyfikator jednostki** pole, wklej identyfikator jednostki SAML, który został skopiowany z witryny Azure portal.

    b. W **dostawcy tożsamości adres URL logowania jednokrotnego** pole, wklej adres URL Usługa rejestracji jednokrotnej SAML, który został skopiowany z witryny Azure portal.

    c. Otwórz pobranego certyfikatu w witrynie Azure portal w pliku txt, skopiuj wartość (bez *rozpocząć certyfikatu* i *End Certificate* wierszy), a następnie wklej go w **X509 publiczne certyfikat** pole.
    
    d. Wybierz **Zapisz konfigurację**.
     
14. Aby upewnić się, że po skonfigurowaniu poprawne adresy URL, należy zaktualizować ustawienia usługi Azure AD, wykonując następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_13.png)

    a. W oknie SAML kopiować **identyfikator tożsamości SP** a następnie w witrynie Azure portal w obszarze chmura Atlassian **domena i adresy URL**, wklej ją w **identyfikator** pole.
    
    b. W oknie SAML kopiować **adres URL programu SP potwierdzenia klienta usługi** a następnie w witrynie Azure portal w obszarze chmura Atlassian **domena i adresy URL**, wklej ją w **adres URL odpowiedzi** pole. Adres URL logowania jest adres URL dzierżawy Atlassian chmury.

    > [!NOTE]
    > Jeśli jesteś istniejącym klientem, po zaktualizowaniu **identyfikator tożsamości SP** i **URL usługi konsumenta potwierdzenie SP** wartości w witrynie Azure portal wybierz **tak, zaktualizuj konfigurację**. W przypadku nowego klienta, możesz pominąć ten krok.
    
15. W witrynie Azure portal wybierz **Zapisz**.

    ![Konfigurowanie logowania jednokrotnego](./media/atlassian-cloud-tutorial/tutorial_general_400.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego Britta Simon w witrynie Azure portal, wykonując następujące czynności:

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

1. W witrynie Azure portal w okienku po lewej stronie wybierz **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/atlassian-cloud-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, wybierz **użytkowników i grup** > **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/atlassian-cloud-tutorial/create_aaduser_02.png)

3. W **wszyscy użytkownicy** wybierz **Dodaj**.

    ![Przycisk Dodaj](./media/atlassian-cloud-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna, wykonaj następujące czynności:

    ![W oknie użytkownika](./media/atlassian-cloud-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Wybierz pozycję **Utwórz**.

### <a name="create-an-atlassian-cloud-test-user"></a>Tworzenie użytkownika testowego w chmurze Atlassian

Aby umożliwić użytkownikom usługi Azure AD zarejestrować się w chmurze Atlassian, uaktywniać ich konta ręcznie w chmurze Atlassian, wykonując następujące czynności:

1. W **administracji** okienku wybierz **użytkowników**.

    ![Link Atlassian użytkowników w chmurze](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_14.png)

2. Aby utworzyć użytkownika w chmurze Atlassian, wybierz pozycję **zaproszenie użytkownika**.

    ![Utworzenie użytkownika usługi Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_15.png)

3. W **adres E-mail** wprowadź adres e-mail użytkownika, a następnie przypisz dostęp do aplikacji.

    ![Utworzenie użytkownika usługi Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_16.png)

4. Aby wysłać wiadomość e-mail z zaproszeniem do użytkownika, zaznacz **zaprosić użytkowników**. Wiadomość e-mail z zaproszeniem są wysyłane do użytkownika, a następnie po zaakceptowaniu zaproszenia, użytkownik jest aktywny w systemie.

>[!NOTE]
>Możesz również zbiorczo — tworzenie użytkowników, wybierając **zbiorczo utworzyć** znajdujący się w **użytkowników** sekcji.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć użytkownika Britta Simon do udzielania dostępu do chmury Atlassian za pomocą platformy Azure logowania jednokrotnego. Aby to zrobić, wykonaj następujące czynności:

![Przypisanie roli użytkownika][200]

1. W witrynie Azure portal Otwórz **aplikacje** wyświetlić, przejdź do widoku katalogu, a następnie wybierz **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. W **aplikacje** listy wybierz **chmury Atlassian**.

    ![Link chmury Atlassian na liście aplikacji](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_app.png)

3. W okienku po lewej stronie wybierz **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Wybierz **Dodaj** a następnie w **Dodaj przydziału** okienku wybierz **użytkowników i grup**.

    ![Okienko Dodawanie przypisania][203]

5. W **użytkowników i grup** okna w **użytkowników** listy wybierz **Britta Simon**.

6. W **użytkowników i grup** wybierz **wybierz**.

7. W **Dodaj przydziału** wybierz **przypisać**.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po wybraniu **chmury Atlassian** kafelka w panelu dostępu użytkownik należy zalogować się automatycznie do aplikacji w chmurze Atlassian.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/atlassian-cloud-tutorial/tutorial_general_01.png
[2]: ./media/atlassian-cloud-tutorial/tutorial_general_02.png
[3]: ./media/atlassian-cloud-tutorial/tutorial_general_03.png
[4]: ./media/atlassian-cloud-tutorial/tutorial_general_04.png

[100]: ./media/atlassian-cloud-tutorial/tutorial_general_100.png

[200]: ./media/atlassian-cloud-tutorial/tutorial_general_200.png
[201]: ./media/atlassian-cloud-tutorial/tutorial_general_201.png
[202]: ./media/atlassian-cloud-tutorial/tutorial_general_202.png
[203]: ./media/atlassian-cloud-tutorial/tutorial_general_203.png
