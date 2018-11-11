---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą usługi G Suite | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i usługi G Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/06/2018
ms.author: jeedes
ms.openlocfilehash: 4ed571d34e5df67f556f39b898e7ae5efc06a3e1
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2018
ms.locfileid: "51288938"
---
# <a name="tutorial-azure-active-directory-integration-with-g-suite"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą usługi G Suite

W tym samouczku dowiesz się, jak zintegrować usługi G Suite z usługą Azure Active Directory (Azure AD).

Integrowanie usługi G Suite z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do usługi G Suite.
- Użytkowników, aby automatycznie uzyskać zalogowanych do usługi G Suite (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą usługi G Suite, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- G Suite logowanie jednokrotne włączone subskrypcji
- Google Apps subskrypcję lub Google Cloud Platform.

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="frequently-asked-questions"></a>Często zadawane pytania

1. **Pytanie: czy ta Obsługa integracji Google Cloud Platform Usługa rejestracji Jednokrotnej integracji z usługą Azure AD?**

    Odp. Tak. Google Cloud Platform i Google Apps współdzielą tę samą platformę uwierzytelniania. Dlatego w przypadku wykonywania integracji GCP, należy skonfigurować logowanie Jednokrotne za pomocą usługi Google Apps.

2. **P: czy Chromebooks i innych urządzeń dla programu Chrome zgodny z usługi Azure AD logowania jednokrotnego?**
  
    Odp. tak, użytkownicy będą mogli logować się do swoich urządzeń Chromebook przy użyciu swoich poświadczeń usługi Azure AD. Zobacz ten [artykuł pomocy technicznej usługi G Suite](https://support.google.com/chrome/a/answer/6060880) informacji o tym, dlaczego użytkownicy mogą się monit o poświadczenia dwa razy.

3. **Pyt. Czy włączyć logowanie jednokrotne, użytkownicy będą mogli korzystać z poświadczeń usługi Azure AD do logowania się do dowolnego produktu Google, takich jak Google Classroom, GMail, dysk Google, YouTube i tak dalej?**

    Odp. tak, w zależności od [które usługi G Suite](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) chcesz włączyć lub wyłączyć dla Twojej organizacji.

4. **P: czy można włączyć logowanie jednokrotne dla tylko podzbiór Moi użytkownicy usługi G Suite?**

    Odp.: nie Włączanie logowania jednokrotnego natychmiast wymaga od wszystkich użytkowników usługi G Suite do uwierzytelniania przy użyciu poświadczeń usługi Azure AD. Ponieważ usługi G Suite nie obsługuje wielu dostawców tożsamości, dostawcy tożsamości w danym środowisku usługi G Suite może być usługa Azure AD lub Google — ale nie obu jednocześnie.

5. **Pyt.: Jeśli użytkownik jest zalogowany przy użyciu Windows, czy automatycznie uwierzytelniają do usługi G Suite bez pobierania zostanie wyświetlony monit o podanie hasła?**

    Odp.: istnieją dwie opcje dotyczące włączania tego scenariusza. Po pierwsze, użytkownicy mogą logować się do urządzeń z systemem Windows 10 za pośrednictwem [usługi Azure Active Directory Join](../device-management-introduction.md). Alternatywnie użytkownicy mogą logować się do urządzenia Windows, które są przyłączone do domeny do usługi Active Directory w środowisku lokalnym, który został włączony dla logowania jednokrotnego do usługi Azure AD za pomocą [Active Directory Federation Services (AD FS)](../hybrid/plan-connect-user-signin.md) wdrożenia. Obie opcje wymagają, należy wykonać czynności opisane w poniższego samouczka, aby włączyć logowanie jednokrotne między usługą Azure AD i usługi G Suite.

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym.
Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie usługi G Suite z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-g-suite-from-the-gallery"></a>Dodawanie usługi G Suite z galerii

Aby skonfigurować integrację usługi G Suite w usłudze Azure AD, należy dodać usługi G Suite z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać usługi G Suite z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![image](./media/google-apps-tutorial/selectazuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![image](./media/google-apps-tutorial/a_select_app.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![image](./media/google-apps-tutorial/a_new_app.png)

4. W polu wyszukiwania wpisz **usługi G Suite**, wybierz opcję **usługi G Suite** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![image](./media/google-apps-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą usługi G Suite w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w usłudze G Suite dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w usłudze G Suite musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą usługi G Suite, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego usługi G Suite](#create-a-g-suite-test-user)**  — aby odpowiednikiem Britta Simon w usłudze G Suite, połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji usługi G Suite.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne za pomocą usługi G Suite, wykonaj następujące czynności:**

1. W [witryny Azure portal](https://portal.azure.com/)na **usługi G Suite** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![image](./media/google-apps-tutorial/b1_b2_select_sso.png)

2. Kliknij przycisk **pojedynczej zmiany trybu logowania jednokrotnego** na górze ekranu, aby wybrać **SAML** trybu.

      ![image](./media/google-apps-tutorial/b1_b2_saml_ssso.png)

3. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, kliknij przycisk **wybierz** dla **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![image](./media/google-apps-tutorial/b1_b2_saml_sso.png)

4. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** przycisk, aby otworzyć **podstawową konfigurację protokołu SAML** okna dialogowego.

    ![image](./media/google-apps-tutorial/b1-domains_and_urlsedit.png)

5. Na **podstawową konfigurację protokołu SAML** sekcji, wykonaj następujące czynności:

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca:
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `http://google.com` |
    | `http://google.com/a/<yourdomain.com>` |

    ![image](./media/google-apps-tutorial/b1-domains_and_urls.png)

    > [!NOTE]
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania jednokrotnego i identyfikator. Skontaktuj się z pomocą [zespołem pomocy technicznej G Suite klienta](https://www.google.com/contact/) do uzyskania tych wartości.

6. Aplikacja usługi G Suite oczekuje twierdzenia SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Możesz zarządzać wartości te atrybuty z **atrybutów użytkownika** sekcji na stronie integracji aplikacji. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** przycisk, aby otworzyć **atrybutów użytkownika** okna dialogowego.

    ![image](./media/google-apps-tutorial/i3-attribute.png)

7. W **oświadczenia użytkownika** sekcji na **atrybutów użytkownika** okno dialogowe, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej i wykonaj następujące czynności:

    a. Kliknij przycisk **Edytuj** przycisk, aby otworzyć **Zarządzanie oświadczenia użytkownika** okna dialogowego.

    ![image](./media/google-apps-tutorial/i2-attribute.png)

    ![image](./media/google-apps-tutorial/i4-attribute.png)

    b. Z **atrybut źródłowy** listy selelct wartość atrybutu.

    c. Kliknij pozycję **Zapisz**.

8. Na **Ustaw się logowania jednokrotnego przy użyciu protokołu SAML** strony w **certyfikat podpisywania SAML** , kliknij przycisk **Pobierz** można pobrać odpowiedni certyfikat zgodnie z wymagania i zapisz go na komputerze.

    ![image](./media/google-apps-tutorial/certificatebase64.png)

9. Na **Konfigurowanie usługi G Suite** sekcji, skopiuj odpowiedni adres URL, zgodnie z wymaganiami.

    Należy zwrócić uwagę na to, czy adres URL może wskazywać następujące czynności:

    a. Adres URL logowania

    b. Identyfikator usługi Azure Ad

    c. Adres URL wylogowywania

    ![image](./media/google-apps-tutorial/d1_saml.png) 

10. Otwórz nową kartę w przeglądarce i zaloguj się do [konsoli administracyjnej usługi G Suite](http://admin.google.com/) przy użyciu konta administratora.

11. Kliknij przycisk **zabezpieczeń**. Jeśli nie widzisz łącza może być ukryty pod przyciskiem **więcej formantów** menu wyświetlanym u dołu ekranu.

    ![Kliknij pozycję Zabezpieczenia.][10]

12. Na **zabezpieczeń** kliknij **Konfigurowanie logowania jednokrotnego (SSO).**

    ![Kliknij przycisk logowania jednokrotnego.][11]

13. Wykonaj następujące zmiany w konfiguracji:

    ![Konfigurowanie logowania jednokrotnego][12]

    a. Wybierz **konfiguracji logowania jednokrotnego, za pomocą dostawcy tożsamości innych firm**.

    b. W **adres URL logowania strony** pola w usłudze G Suite, Wklej wartość **adres URL logowania** skopiowanej w witrynie Azure portal.

    c. W **adres URL strony wylogowania** pola w usłudze G Suite, Wklej wartość **adres URL wylogowania** skopiowanej w witrynie Azure portal.

    d. W **zmienić adres URL hasła** pola w usłudze G Suite, Wklej wartość **zmienić adres URL hasła** skopiowanej w witrynie Azure portal.

    e. W usłudze G Suite dla **certyfikatu weryfikacji**, Przekaż certyfikat, który został pobrany z witryny Azure portal.

    f. Wybierz **Użyj wystawcy do określonej domeny**.

    g. Kliknij przycisk **Zapisz zmiany**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

1. W witrynie Azure portal w okienku po lewej stronie wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.

    ![image](./media/google-apps-tutorial/d_users_and_groups.png)

2. Wybierz **nowego użytkownika** w górnej części ekranu.

    ![image](./media/google-apps-tutorial/d_adduser.png)

3. We właściwościach użytkownika wykonaj następujące czynności.

    ![image](./media/google-apps-tutorial/d_userproperties.png)

    a. W **nazwa** pola wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    c. Wybierz **właściwości**, wybierz opcję **hasło Show** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w polu hasło.

    d. Wybierz pozycję **Utwórz**.

### <a name="create-a-g-suite-test-user"></a>Tworzenie użytkownika testowego usługi G Suite

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon G Suite oprogramowania. G Suite obsługuje automatycznej aprowizacji, który jest domyślnie włączona. Nie ma akcji dla Ciebie w tej sekcji. Jeśli użytkownik jeszcze nie istnieje w G Suite Software, nowy katalog jest tworzony przy próbie uzyskania dostępu do pakietu G Suite Software.

> [!NOTE]
> Upewnij się, że użytkownik będzie już istnieje w usłudze G Suite, jeśli Inicjowanie obsługi administracyjnej w usłudze Azure AD nie został włączony przed testowaniem logowania jednokrotnego.

> [!NOTE]
> Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej firmy Google](https://www.google.com/contact/).

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do usługi G Suite.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**.

    ![image](./media/google-apps-tutorial/d_all_applications.png)

2. Na liście aplikacji wybierz **usługi G Suite**.

    ![image](./media/google-apps-tutorial/d_all_proapplications.png)

3. W menu po lewej stronie wybierz **użytkowników i grup**.

    ![image](./media/google-apps-tutorial/d_leftpaneusers.png)

4. Wybierz **Dodaj** przycisk, a następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

    ![image](./media/google-apps-tutorial/d_assign_user.png)

5. W **użytkowników i grup** okna dialogowego wybierz **Britta Simon** na liście użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

6. W **Dodaj przydziału** okna dialogowego wybierz **przypisać** przycisku.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka usługi G Suite w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji usługi G Suite.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png