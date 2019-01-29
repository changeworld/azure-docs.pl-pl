---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą aplikacji DocuSign | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i DocuSign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: jeedes
ms.openlocfilehash: 8838cb2479078a8b6b4f031128501f09284b6bdd
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55159278"
---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą aplikacji DocuSign

W tym samouczku dowiesz się, jak zintegrować DocuSign w usłudze Azure Active Directory (Azure AD).

Integrowanie aplikacji DocuSign z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do aplikacji DocuSign.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do DocuSign (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD aplikacji docusign, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- DocuSign logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie aplikacji DocuSign z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-docusign-from-the-gallery"></a>Dodawanie aplikacji DocuSign z galerii

Aby skonfigurować integrację DocuSign w usłudze Azure AD, należy dodać DocuSign z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać DocuSign w galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

4. W polu wyszukiwania wpisz **DocuSign**, wybierz opcję **DocuSign** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![DocuSign na liście wyników](./media/docusign-tutorial/tutorial_docusign_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą aplikacji DocuSign w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w usłudze DocuSign do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w usłudze DocuSign musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego aplikacji docusign, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego DocuSign](#creating-a-docusign-test-user)**  — aby odpowiednikiem Britta Simon w aplikacji DocuSign, połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji DocuSign.

**Aby skonfigurować usługę Azure AD logowania jednokrotnego aplikacji docusign, wykonaj następujące czynności:**

1. W witrynie Azure portal na **DocuSign** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

2. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, kliknij przycisk **wybierz** dla **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![Konfigurowanie logowania jednokrotnego](common/tutorial_general_301.png)

3. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** ikonę, aby otworzyć **podstawową konfigurację protokołu SAML** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](common/editconfigure.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Domena aplikacji DocuSign i adresy URL pojedynczego logowania jednokrotnego informacji](./media/docusign-tutorial/tutorial_docusign_url.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania jednokrotnego i identyfikator, który zostało wyjaśnione w dalszej części **Wyświetl SAML 2.0 punkty końcowe** sekcji, w tym samouczku.

5. Na **certyfikat podpisywania SAML** strony w **certyfikat podpisywania SAML** kliknij **Pobierz** można pobrać **certyfikat (Base64)**, a następnie zapisz plik certyfikatu na komputerze.

    ![Link do pobierania certyfikatu](./media/docusign-tutorial/tutorial_docusign_certificate.png) 

6. Na **Konfigurowanie DocuSign** sekcji, skopiuj odpowiedni adres URL, zgodnie z wymaganiami.

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

    ![Konfiguracja aplikacji DocuSign](common/configuresection.png)

7. W oknie przeglądarki internetowej innej, zaloguj się do Twojej **portalu administracyjnego DocuSign** jako administrator.

8. W górnym prawym rogu strony kliknij profil **logo** a następnie kliknij polecenie **przejdź do administratora**.
  
    ![Konfigurowanie logowania jednokrotnego][51]

9. Na stronie rozwiązania domeny, kliknij na **domen**

    ![Konfigurowanie logowania jednokrotnego][50]

10. W obszarze **domen** kliknij **oświadczenia domeny**.

    ![Konfigurowanie logowania jednokrotnego][52]

11. Na **oświadczenia domeny** okna dialogowego w **nazwy domeny** polu tekstowym wpisz domenę firmy, a następnie kliknij przycisk **oświadczenia**. Upewnij się, że zweryfikować domenę, a stan jest aktywny.

    ![Konfigurowanie logowania jednokrotnego][53]

12. Na stronie rozwiązania domeny, kliknij **dostawców tożsamości**.
  
    ![Konfigurowanie logowania jednokrotnego][54]

13. W obszarze **dostawców tożsamości** kliknij **Dodawanie dostawcy tożsamości**. 

    ![Konfigurowanie logowania jednokrotnego][55]

14. Na **ustawień dostawcy tożsamości** strony, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego][56]

    a. W **nazwa** pole tekstowe, wpisz unikatową nazwę dla danej konfiguracji. Nie należy używać miejsc do magazynowania.

    b. W **wystawca dostawcy tożsamości w polu tekstowym**, Wklej wartość **usługi Azure AD identyfikator**, które zostały skopiowane z witryny Azure portal.

    c. W **adres URL logowania dostawcy tożsamości** pola tekstowego, Wklej wartość **adres URL logowania**, które zostały skopiowane z witryny Azure portal.

    d. W **adres URL wylogowania dostawcy tożsamości** pola tekstowego, Wklej wartość **adres URL wylogowania**, które zostały skopiowane z witryny Azure portal.

    e. Wybierz **żądania uwierzytelniania logowania**.

    f. Jako **żądania wysyłania AuthN**, wybierz opcję **WPIS**.

    g. Jako **wysyłania żądania wylogowania**, wybierz opcję **UZYSKAĆ**.

    h. W **mapowanie atrybutu niestandardowego** sekcji, kliknij pozycję **Dodaj nowe MAPOWANIE**.

    ![Konfigurowanie logowania jednokrotnego][62]

    i. Wybierz pole, które ma być mapowany na podstawie usługi Azure AD oświadczenia. W tym przykładzie **emailaddress** oświadczeń jest zamapowana z wartością **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**. Jest domyślna nazwa oświadczeń z usługi Azure AD oświadczenie adresu e-mail, a następnie kliknij przycisk **ZAPISZ**.

    ![Konfigurowanie logowania jednokrotnego][57]

    > [!NOTE]
    > To zrobić w odpowiednim **identyfikator użytkownika** do mapowania użytkowników z usługi Azure AD do mapowania użytkowników DocuSign. Zaznacz odpowiednie pola, a następnie wprowadź odpowiednią wartość na podstawie własnych ustawień organizacji.

    j. W **certyfikatów dostawcy tożsamości** , kliknij przycisk **Dodaj certyfikat**, a następnie przekaż certyfikat został pobrany z portalu usługi Azure AD i kliknij przycisk **ZAPISZ**.

    ![Konfigurowanie logowania jednokrotnego][58]

    k. W **dostawców tożsamości** kliknij **akcje**, a następnie kliknij przycisk **punktów końcowych**.

    ![Konfigurowanie logowania jednokrotnego][59]

    l. W **Wyświetl SAML 2.0 punkty końcowe** sekcji na **portalu administracyjnego DocuSign**, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego][60]

    * Kopia **adres URL wystawcy dostawcy usługi**, a następnie wklej go do **identyfikator** polu tekstowym w **DocuSign domena i adresy URL** sekcji w witrynie Azure portal.

    * Kopiowanie **adres URL logowania dostawcy usługi**, a następnie wklej go do **na adres URL logowania** polu tekstowym w **DocuSign domena i adresy URL** sekcji w witrynie Azure portal.

    * Kliknij przycisk **Zamknij**

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

### <a name="creating-a-docusign-test-user"></a>Tworzenie użytkownika testowego DocuSign

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon w usłudze DocuSign. DocuSign obsługę just-in-time, który jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Nowy użytkownik jest tworzony podczas próby dostępu aplikacji DocuSign, jeśli go jeszcze nie istnieje.
>[!Note]
>Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej DocuSign](https://support.docusign.com/).

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do aplikacji DocuSign.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji wybierz **DocuSign**.

    ![Konfigurowanie logowania jednokrotnego](./media/docusign-tutorial/tutorial_docusign_app.png)

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. W **użytkowników i grup** okna dialogowego wybierz **Britta Simon** na liście użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

6. W **Dodaj przydziału** okna dialogowego wybierz **przypisać** przycisku.

### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka DocuSign w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji DocuSign.
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
[50]: ./media/docusign-tutorial/tutorial_docusign_18.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[62]: ./media/docusign-tutorial/tutorial_docusign_30.png
