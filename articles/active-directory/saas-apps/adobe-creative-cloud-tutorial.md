---
title: 'Samouczek: Integracja usługi Azure Active Directory z Adobe Creative Cloud | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Adobe Creative Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: c199073f-02ce-45c2-b515-8285d4bbbca2
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2018
ms.author: jeedes
ms.openlocfilehash: e1788de7c2372797b2034eb1753ab435c1299889
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38548285"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-creative-cloud"></a>Samouczek: Integracja usługi Azure Active Directory z Adobe Creative Cloud

W tym samouczku dowiesz się, jak zintegrować Adobe Creative Cloud z usługą Azure Active Directory (Azure AD).

Integrowanie Adobe Creative Cloud z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do usługi Adobe Creative Cloud.
- Użytkowników, aby automatycznie uzyskać zalogowanych do Adobe Creative Cloud (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Adobe Creative Cloud, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Adobe Creative Cloud logowanie jednokrotne włączone subskrypcji
- Wymagana wersja programu Adobe Creative Cloud przedsiębiorstwa

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Adobe Creative Cloud z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>Dodawanie Adobe Creative Cloud z galerii

Aby skonfigurować integrację programu Adobe Creative Cloud, w usłudze Azure AD, należy dodać Adobe Creative Cloud z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Adobe Creative Cloud z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Adobe Creative Cloud**, wybierz opcję **Adobe Creative Cloud** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Adobe Creative Cloud, na liście wyników](./media/adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Adobe Creative Cloud, w oparciu o nazwie "Britta Simon" użytkownika testowego.

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Adobe Creative Cloud do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w programie Adobe Creative Cloud musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD jednokrotne logowanie w usłudze Adobe Creative Cloud, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego Adobe Creative Cloud](#create-an-adobe-creative-cloud-test-user)**  — aby odpowiednikiem Britta Simon w Adobe Creative Cloud, połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Adobe Creative Cloud.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Adobe Creative Cloud, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Adobe Creative Cloud** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.

    ![Okno dialogowe rejestracji jednokrotnej](./media/adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_samlbase.png)

3. Na **Adobe Creative Cloud domena i adresy URL** sekcji, jeśli chcesz skonfigurować aplikację w trybie zainicjował dostawcy tożsamości należy wykonać następujące czynności:

    ![Adobe Creative Cloud domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_url.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://www.okta.com/saml2/service-provider/<token>`

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<company name>.okta.com/auth/saml20/accauthlinktest`

    > [!NOTE]
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adres URL odpowiedzi. Skontaktuj się z pomocą [Adobe Creative Cloud Enterprise](https://www.adobe.com/au/creativecloud/business/teams/plans.html) do uzyskania tych wartości.

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![Adobe Creative Cloud domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_url2.png)

    W **adres URL logowania** polu tekstowym wpisz wartość jako: `https://adobe.com`

5. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_certificate.png)
     
6. Adobe Creative Cloud aplikacja oczekuje twierdzenia SAML, w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Możesz zarządzać wartości te atrybuty z **atrybutu użytkownika** kartę w aplikacji. Poniższy zrzut ekranu przedstawia przykład tego.

    ![Konfigurowanie logowania jednokrotnego](./media/adobe-creative-cloud-tutorial/tutorial_attribute.png)

7. W **atrybutów użytkownika** sekcji na **logowanie jednokrotne** okno dialogowe, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej i wykonaj następujące czynności:

    | Nazwa atrybutu | Wartość atrybutu |
    | ---------------| ----------------|
    | FirstName |user.givenname |
    | LastName |user.surname |
    | Email |User.mail |

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](./media/adobe-creative-cloud-tutorial/tutorial_attribute_04.png)

    ![Konfigurowanie logowania jednokrotnego](./media/adobe-creative-cloud-tutorial/tutorial_attribute_05.png)

    b. W **nazwa** polu tekstowym wpisz nazwę atrybutu, wyświetlanego dla tego wiersza.

    c. Z **wartość** wpisz wartość atrybutu wyświetlanego dla tego wiersza.

    d. Kliknij przycisk **OK**.

    > [!NOTE]
    > Użytkownicy muszą mieć ważną licencję pakietu Office 365 ekso wiadomości e-mail w wartości ma zostać wypełniony w odpowiedzi SAML oświadczenia.

8. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/adobe-creative-cloud-tutorial/tutorial_general_400.png)

9. Na **konfiguracji usług Adobe Creative Cloud** , kliknij przycisk **skonfigurować Adobe Creative Cloud** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego adres URL usługi** z **krótki przewodnik po sekcji**.

    ![Konfiguracji usług Adobe Creative Cloud](./media/adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_configure.png)

10. W oknie przeglądarki internetowej innej, zaloguj się do [konsoli administracyjnej programu Adobe](https://adminconsole.adobe.com) jako administrator.

11. Przejdź do **ustawienia** na górnym pasku nawigacyjnym paska, a następnie wybierz **tożsamości**. Zostanie otwarta lista domen. Kliknij przycisk **Konfiguruj** łącza względem Twojej domeny. Następnie wykonaj następujące czynności na **pojedynczy znak na wymagana konfiguracja** sekcji. Aby uzyskać więcej informacji, zobacz [konfiguracji domeny](https://helpx.adobe.com/enterprise/using/set-up-domain.html)

    ![Ustawienia](https://helpx.adobe.com/content/dam/help/en/enterprise/using/configure-microsoft-azure-with-adobe-sso/_jcr_content/main-pars/procedure_719391630/proc_par/step_3/step_par/image/edit-sso-configuration.png "ustawienia")

    a. Kliknij przycisk **Przeglądaj** można przekazać certyfikatu pobrany z usługi Azure AD do **certyfikatu tożsamości**.

    b. W **wystawca dostawcy tożsamości** pola tekstowego, umieść wartość **identyfikator jednostki SAML** który skopiowano z **Konfigurowanie logowania jednokrotnego** sekcji w witrynie Azure portal.

    c. W **adres URL logowania dostawcy tożsamości** pola tekstowego, umieść wartość **adres URL usługi logowania jednokrotnego SAML** który skopiowano z **Konfigurowanie logowania jednokrotnego** sekcji w witrynie Azure portal.

    d. Wybierz **HTTP - Redirect** jako **powiązania protokołu IDP**.

    e. Wybierz **adres E-mail** jako **ustawienia logowania użytkownika**.

    f. Kliknij przycisk **Zapisz** przycisku.

12. Pulpit nawigacyjny będzie teraz przedstawiać XML **"Pobierz metadane"** pliku. Zawiera on EntityDescriptor adresu URL i adresu URL AssertionConsumerService firmy Adobe. Otwórz plik i skonfigurować je w aplikacji usługi Azure AD.

    ![Konfigurowanie logowania jednokrotnego w aplikacji po stronie](./media/adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Użyj wartości EntityDescriptor Adobe dostarczonym dla **identyfikator** na **Konfigurowanie ustawień aplikacji** okna dialogowego.

    b. Użyj wartości AssertionConsumerService Adobe dostarczonym dla **adres URL odpowiedzi** na **Konfigurowanie ustawień aplikacji** okna dialogowego.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/adobe-creative-cloud-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/adobe-creative-cloud-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/adobe-creative-cloud-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/adobe-creative-cloud-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-an-adobe-creative-cloud-test-user"></a>Tworzenie użytkownika testowego Adobe Creative Cloud

Aby umożliwić użytkownikom usługi Azure AD zalogować się do Adobe Creative Cloud, musi być obsługiwana w programie Adobe Creative Cloud. W przypadku Adobe Creative Cloud Inicjowanie obsługi administracyjnej jest zadanie ręczne.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Aby udostępnić konta użytkowników, wykonaj następujące czynności:

1. Zaloguj się do [konsoli administracyjnej programu Adobe](https://adminconsole.adobe.com) lokacji jako administrator.

2. Dodaj użytkownika konsoli firmy Adobe jako identyfikator usługi federacyjnej i przypisać je do profilu produkcie. Aby uzyskać szczegółowe informacje na temat dodawania użytkowników, zobacz [Dodawanie użytkowników w konsoli administracyjnej programu Adobe](https://helpx.adobe.com/enterprise/using/users.html#Addusers) 

3. W tym momencie wpisz swoje wiadomości e-mail adres/nazwy upn do formularza logowania Adobe, naciśnij klawisz tab, a powinien zostać sfederowana powrót do usługi Azure AD:
    * Program Web access: www.adobe.com > Zaloguj się
    * W narzędziu aplikacji klasycznej > Zaloguj się
    * W ramach aplikacji > Pomoc > Zaloguj się

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do Adobe Creative Cloud.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon Adobe Creative Cloud, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **Adobe Creative Cloud**.

    ![Łącze Adobe Creative Cloud, na liście aplikacji](./media/adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Adobe Creative Cloud, w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Adobe Creative Cloud.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie domeny (adobe.com)](https://helpx.adobe.com/enterprise/using/set-up-domain.html)
* [Konfigurowanie platformy Azure do użycia z usługą Adobe logowania jednokrotnego (adobe.com)](https://helpx.adobe.com/enterprise/kb/configure-microsoft-azure-with-adobe-sso.html)

<!--Image references-->

[1]: ./media/adobe-creative-cloud-tutorial/tutorial_general_01.png
[2]: ./media/adobe-creative-cloud-tutorial/tutorial_general_02.png
[3]: ./media/adobe-creative-cloud-tutorial/tutorial_general_03.png
[4]: ./media/adobe-creative-cloud-tutorial/tutorial_general_04.png

[100]: ./media/adobe-creative-cloud-tutorial/tutorial_general_100.png

[200]: ./media/adobe-creative-cloud-tutorial/tutorial_general_200.png
[201]: ./media/adobe-creative-cloud-tutorial/tutorial_general_201.png
[202]: ./media/adobe-creative-cloud-tutorial/tutorial_general_202.png
[203]: ./media/adobe-creative-cloud-tutorial/tutorial_general_203.png
