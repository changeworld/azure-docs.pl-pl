---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Figma | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Figma.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8569cae1-87dd-4c40-9bbb-527ac80d6a96
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/22/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3885f579e4543f25c409010c0aae970c26d2b9c4
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56183982"
---
# <a name="tutorial-azure-active-directory-integration-with-figma"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Figma

W tym samouczku dowiesz się, jak zintegrować Figma w usłudze Azure Active Directory (Azure AD).

Integrowanie Figma z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Figma.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do Figma (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Figma, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Figma [logowanie jednokrotne włączone subskrypcji](https://www.figma.com/pricing/)

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego. Nowych klientów i aktywni subskrybenci Figma Professional zespół może kontaktować się z Figma do [uaktualnić swoją subskrypcję](https://www.figma.com/pricing/) Figma warstwę organizacji.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Figma z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-figma-from-the-gallery"></a>Dodawanie Figma z galerii

Aby skonfigurować integrację Figma w usłudze Azure AD, należy dodać Figma z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Figma z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Figma**. Wybierz **Figma** z panel wyników, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Figma na liście wyników](./media/figma-tutorial/tutorial_figma_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Figma w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi być połączone z Figma.  Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Figma, wykonaj następujące czynności:

1. [**Skontaktuj się z zespołem pomocy technicznej Figma** ](mailto:support@figma.com?subject=SAML+Config) do inicjowania konfiguracji SAML dla Twojej organizacji i Uzyskaj ORG_SAML_CONFIG_ID.
2. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Tworzenie użytkownika testowego Figma](#creating-a-figma-test-user)**  — aby odpowiednikiem Britta Simon w Figma połączonego z usługi Azure AD reprezentacja użytkownika.
5. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
6. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Figma.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Figma, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Figma** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

2. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, kliknij przycisk **wybierz** dla **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![Konfigurowanie logowania jednokrotnego](./media/figma-tutorial/tutorial_general_301.png)

3. Jeśli musisz zmienić **SAML** trybu z dowolnego innego trybu, kliknij przycisk **zmiana jednego tryb logowania jednokrotnego** na górze ekranu.

    ![Konfigurowanie logowania jednokrotnego](./media/figma-tutorial/tutorial_general_300.png)

4. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** ikonę, aby otworzyć **podstawową konfigurację protokołu SAML** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](./media/figma-tutorial/tutorial_general_302.png)

5. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę tożsamości**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    ![Figma domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/figma-tutorial/tutorial_figma_url1.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://www.figma.com/saml/<ORG_SAML_CONFIG_ID>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://www.figma.com/saml/<ORG_SAML_CONFIG_ID>/consume`

6. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    ![Figma domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/figma-tutorial/tutorial_figma_url2.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://www.figma.com/saml/<ORG_SAML_CONFIG_ID>/start`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej Figma](mailto:support@figma.com?subject=SAML+Config) do uzyskania tych wartości.

7. Aplikacja Figma oczekuje twierdzenia SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Atrybuty użytkownika**.

    ![Sekcja atrybutu](./media/figma-tutorial/edit_attribute.png)

8. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej, i wykonaj następujące czynności:

    | Nazwa | Atrybut źródłowy|
    | ---------------| --------- |
    | `externalId` | `user.mailnickname` |
    | `displayName` | `user.displayname` |
    | `title` | `user.jobtitle` |
    | `emailaddress` | `user.mail` |
    | `familyName` | `user.surname` |
    | `givenName` | `givenName` |
    | `userName` | `user.userprincipalname` |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![Nowy atrybut](./media/figma-tutorial/new_save_attribute.png)

    ![Dodaj atrybut](./media/figma-tutorial/new_attribute_details.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    d. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij przycisk **OK**.

    g. Kliknij pozycję **Zapisz**.

9. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij przycisk kopiowania, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](./media/figma-tutorial/tutorial_figma_certificate.png)

10. Aby skonfigurować logowanie jednokrotne na stronie Figma, Wypełnij ten formularz: [ https://goo.gl/forms/XkRB1z5ed4eVUzXn2 ](https://goo.gl/forms/XkRB1z5ed4eVUzXn2). Akceptował swoje **adres Url metadanych Federacji aplikacji** z kroku nr 9.

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Utwórz użytkownika usługi Azure AD][100]

2. Wybierz **nowego użytkownika** w górnej części ekranu.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/figma-tutorial/create_aaduser_01.png) 

3. We właściwościach użytkownika wykonaj następujące czynności.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/figma-tutorial/create_aaduser_02.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    c. Wybierz **właściwości**, wybierz opcję **hasło Show** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w polu hasło.

    d. Wybierz pozycję **Utwórz**.

### <a name="creating-a-figma-test-user"></a>Tworzenie użytkownika testowego Figma

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon w Figma. Figma obsługę just-in-time, który jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Nowy użytkownik jest tworzony podczas próby dostępu Figma, jeśli go jeszcze nie istnieje.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do HubSpot SAML.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji wybierz **HubSpot SAML**.

    ![Konfigurowanie logowania jednokrotnego](./media/figma-tutorial/tutorial_figma_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. W **użytkowników i grup** okna dialogowego wybierz **Britta Simon** na liście użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

6. W **Dodaj przydziału** okna dialogowego wybierz **przypisać** przycisku.

### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Figma w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Figma.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/figma-tutorial/tutorial_general_01.png
[2]: ./media/figma-tutorial/tutorial_general_02.png
[3]: ./media/figma-tutorial/tutorial_general_03.png
[4]: ./media/figma-tutorial/tutorial_general_04.png

[100]: ./media/figma-tutorial/tutorial_general_100.png

[200]: ./media/figma-tutorial/tutorial_general_200.png
[201]: ./media/figma-tutorial/tutorial_general_201.png
[202]: ./media/figma-tutorial/tutorial_general_202.png
[203]: ./media/figma-tutorial/tutorial_general_203.png
