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
ms.date: 11/30/2018
ms.author: jeedes
ms.openlocfilehash: bcea7848c7331ecd326f0ccb6ab9f543ce972205
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52834693"
---
# <a name="tutorial-azure-active-directory-integration-with-g-suite"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą usługi G Suite

W tym samouczku dowiesz się, jak zintegrować usługi G Suite z usługą Azure Active Directory (Azure AD).

Integrowanie usługi G Suite z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do usługi G Suite.
- Użytkowników, aby automatycznie uzyskać zalogowanych do usługi G Suite (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą usługi G Suite, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- G Suite logowanie jednokrotne włączone subskrypcji
- Google Apps subskrypcję lub Google Cloud Platform.

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego. Ten dokument został utworzony przy użyciu nowego użytkownika środowisko logowanie jednokrotne. Jeśli nadal używasz starego, instalacji będzie wyglądać inaczej. Można włączyć nowe środowisko w ustawieniach logowania jednokrotnego aplikacji usługi G Suite. Przejdź do **usługi Azure AD, aplikacje dla przedsiębiorstw**, wybierz opcję **usługi G Suite**, wybierz opcję **logowania jednokrotnego** a następnie kliknij polecenie **wypróbować nasze nowe środowisko**.

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

6. **Błąd: Nieprawidłowy adres E-mail**

    Dla tej konfiguracji atrybut poczty e-mail jest wymagany w przypadku użytkowników można było zalogować się. Ten atrybut nie można ustawić ręcznie.

    Atrybut poczty e-mail jest wypełnianie automatycznie uzyskać każdy użytkownik mający ważną licencję programu Exchange. Jeśli użytkownik nie jest włączona obsługa poczty e-mail, zgodnie z jej serwer musi pobrać tego atrybutu w celu udzielenia dostępu będą odbierane ten błąd.

    Możesz przejść do portal.office.com przy użyciu konta administratora, kliknij przycisk w Centrum administracyjnym, rozliczeń i subskrypcji, wybieranie subskrypcji usługi Office 365, a następnie kliknij pozycję Przypisz do użytkowników, wybierz użytkowników, dla których chcesz sprawdzić swoją subskrypcję, a następnie w okienku po prawej stronie kliknij pozycję Edytuj licencje.

    Po przypisaniu licencji usługi O365, może upłynąć kilka minut, które mają być stosowane. Po utworzeniu tego atrybutu user.mail zostanie automatycznie wypełnione i problem należy rozwiązać.

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie usługi G Suite z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-g-suite-from-the-gallery"></a>Dodawanie usługi G Suite z galerii

Aby skonfigurować integrację usługi G Suite w usłudze Azure AD, należy dodać usługi G Suite z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać usługi G Suite z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **usługi G Suite**, wybierz opcję **usługi G Suite** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![G Suite na liście wyników](./media/google-apps-tutorial/tutorial_gsuite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą usługi G Suite w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w usłudze G Suite dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w usłudze G Suite musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą usługi G Suite, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego usługi G Suite](#creating-a-g-suite-test-user)**  — aby odpowiednikiem Britta Simon w usłudze G Suite, połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji usługi G Suite.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne za pomocą usługi G Suite, wykonaj następujące czynności:**

1. W witrynie Azure portal na **usługi G Suite** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, kliknij przycisk **wybierz** dla **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![Konfigurowanie logowania jednokrotnego](common/tutorial_general_301.png)

3. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** ikonę, aby otworzyć **podstawową konfigurację protokołu SAML** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](common/editconfigure.png)

4. Na **podstawową konfigurację protokołu SAML** sekcji, wykonaj następujące czynności:

    ![G Suite domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/google-apps-tutorial/tutorial_gsuite_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca:
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |

    > [!NOTE]
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania jednokrotnego i identyfikator. Skontaktuj się z pomocą [zespołem pomocy technicznej G Suite klienta](https://www.google.com/contact/) do uzyskania tych wartości.

5. Aplikacja usługi G Suite oczekuje twierdzenia SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Możesz zarządzać wartości te atrybuty z **atrybutów użytkownika** sekcji na stronie integracji aplikacji. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** przycisk, aby otworzyć **atrybutów użytkownika** okna dialogowego.

    ![image](./media/google-apps-tutorial/i3-attribute.png)

6. W **oświadczenia użytkownika** sekcji na **atrybutów użytkownika** okno dialogowe, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej i wykonaj następujące czynności:

    a. Kliknij przycisk **Edytuj** przycisk, aby otworzyć **Zarządzanie oświadczenia użytkownika** okna dialogowego.

    ![image](./media/google-apps-tutorial/i2-attribute.png)

    ![image](./media/google-apps-tutorial/i4-attribute.png)

    b. Z **atrybut źródłowy** listy selelct wartość atrybutu.

    c. Kliknij pozycję **Zapisz**.

5. Na **certyfikat podpisywania SAML** strony w **certyfikat podpisywania SAML** kliknij **Pobierz** można pobrać **certyfikat (Base64)**, a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/google-apps-tutorial/tutorial_gsuite_certificate.png) 

6. Na **Konfigurowanie usługi G Suite** sekcji, skopiuj odpowiedni adres URL, zgodnie z wymaganiami.

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

    ![Konfiguracja usługi G Suite](common/configuresection.png)

9. Otwórz nową kartę w przeglądarce i zaloguj się do [konsoli administracyjnej usługi G Suite](https://admin.google.com/) przy użyciu konta administratora.

10. Kliknij przycisk **zabezpieczeń**. Jeśli nie widzisz łącza może być ukryty pod przyciskiem **więcej formantów** menu wyświetlanym u dołu ekranu.

    ![Kliknij pozycję Zabezpieczenia.][10]

11. Na **zabezpieczeń** kliknij **Konfigurowanie logowania jednokrotnego (SSO).**

    ![Kliknij przycisk logowania jednokrotnego.][11]

12. Wykonaj następujące zmiany w konfiguracji:

    ![Konfigurowanie logowania jednokrotnego][12]

    a. Wybierz **konfiguracji logowania jednokrotnego, za pomocą dostawcy tożsamości innych firm**.

    b. W **adres URL logowania strony** pola w usłudze G Suite, Wklej wartość **adres URL logowania** skopiowanej w witrynie Azure portal.

    c. W **adres URL strony wylogowania** pola w usłudze G Suite, Wklej wartość **adres URL wylogowania** skopiowanej w witrynie Azure portal.

    d. W **zmienić adres URL hasła** pola w usłudze G Suite, Wklej wartość **zmienić adres URL hasła** skopiowanej w witrynie Azure portal.

    e. W usłudze G Suite dla **certyfikatu weryfikacji**, Przekaż certyfikat, który został pobrany z witryny Azure portal.

    f. Wybierz **Użyj wystawcy do określonej domeny**.

    g. Kliknij przycisk **Zapisz zmiany**.

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

1. W witrynie Azure portal w okienku po lewej stronie wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.

    ![Utwórz użytkownika usługi Azure AD][100]

2. Wybierz **nowego użytkownika** w górnej części ekranu.

    ![Tworzenie użytkownika testowego usługi Azure AD](common/create_aaduser_01.png) 

3. We właściwościach użytkownika wykonaj następujące czynności.

    ![Tworzenie użytkownika testowego usługi Azure AD](common/create_aaduser_02.png)

    a. W **nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** wpisz **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    c. Wybierz **właściwości**, wybierz opcję **hasło Show** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w polu hasło.

    d. Wybierz pozycję **Utwórz**.

### <a name="creating-a-g-suite-test-user"></a>Tworzenie użytkownika testowego usługi G Suite

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon G Suite oprogramowania. G Suite obsługuje automatycznej aprowizacji, który jest domyślnie włączona. Nie ma akcji dla Ciebie w tej sekcji. Jeśli użytkownik jeszcze nie istnieje w G Suite Software, nowy katalog jest tworzony przy próbie uzyskania dostępu do pakietu G Suite Software.

> [!NOTE]
> Upewnij się, że użytkownik będzie już istnieje w usłudze G Suite, jeśli Inicjowanie obsługi administracyjnej w usłudze Azure AD nie został włączony przed testowaniem logowania jednokrotnego.

> [!NOTE]
> Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej firmy Google](https://www.google.com/contact/).

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do usługi G Suite.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji wybierz **usługi G Suite**.

    ![Konfigurowanie logowania jednokrotnego](./media/google-apps-tutorial/tutorial_gsuite_app.png)

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. W **użytkowników i grup** okna dialogowego wybierz **Britta Simon** na liście użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

6. W **Dodaj przydziału** okna dialogowego wybierz **przypisać** przycisku.

### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka usługi G Suite w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji usługi G Suite.
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
<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png