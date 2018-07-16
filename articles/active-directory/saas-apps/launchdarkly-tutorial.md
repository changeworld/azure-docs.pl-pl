---
title: 'Samouczek: Integracja usługi Azure Active Directory z funkcją LaunchDarkly | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i LaunchDarkly.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0e9cb37e-16bf-493b-bfc8-8d9840545a1e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.openlocfilehash: 6d0024adfde82480d46f876021a25bb8f42ba6ff
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39041300"
---
# <a name="tutorial-azure-active-directory-integration-with-launchdarkly"></a>Samouczek: Integracja usługi Azure Active Directory z funkcją LaunchDarkly

W tym samouczku dowiesz się, jak zintegrować LaunchDarkly w usłudze Azure Active Directory (Azure AD).

Integracja LaunchDarkly z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do LaunchDarkly.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do LaunchDarkly (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z funkcją LaunchDarkly, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- LaunchDarkly logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie LaunchDarkly z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-launchdarkly-from-the-gallery"></a>Dodawanie LaunchDarkly z galerii
Aby skonfigurować integrację launchdarkly w usłudze Azure AD, należy dodać LaunchDarkly z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać LaunchDarkly z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **LaunchDarkly**, wybierz opcję **LaunchDarkly** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![LaunchDarkly na liście wyników](./media/launchdarkly-tutorial/tutorial_launchdarkly_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą LaunchDarkly, w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w LaunchDarkly do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w LaunchDarkly musi można ustanowić.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z funkcją LaunchDarkly, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego LaunchDarkly](#create-a-launchdarkly-test-user)**  — aby odpowiednikiem Britta Simon w LaunchDarkly, połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji LaunchDarkly.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z funkcją LaunchDarkly, wykonaj następujące czynności:**

1. W witrynie Azure portal na **LaunchDarkly** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.

    ![Okno dialogowe rejestracji jednokrotnej](./media/launchdarkly-tutorial/tutorial_launchdarkly_samlbase.png)

3. Na **LaunchDarkly, domena i adresy URL** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![LaunchDarkly, domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/launchdarkly-tutorial/tutorial_launchdarkly_url.png)

    a. W **identyfikator jednostki** pole tekstowe, wpisz adres URL: `app.launchdarkly.com`

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://app.launchdarkly.com/trust/saml2/acs/<customers-unique-id>`
    
    > [!NOTE]
    > Wartość adresu URL odpowiedzi nie jest prawdziwe. Wartość zostanie zaktualizowana o rzeczywistego adresu URL odpowiedzi, które wyjaśniono w dalszej części tego samouczka.

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![LaunchDarkly, domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/launchdarkly-tutorial/tutorial_launchdarkly_url1.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL: `https://app.launchdarkly.com`

5. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/launchdarkly-tutorial/tutorial_launchdarkly_certificate.png) 

6. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/launchdarkly-tutorial/tutorial_general_400.png)
    
7. Na **konfiguracji LaunchDarkly** , kliknij przycisk **skonfigurować LaunchDarkly** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **pojedynczy znak na adres URL usługi** z **krótki przewodnik po sekcji.**

    ![Konfiguracja LaunchDarkly](./media/launchdarkly-tutorial/tutorial_launchdarkly_configure.png)

8. W oknie przeglądarki internetowej innej Zaloguj się do witryny firmy LaunchDarkly, jako administrator.

9. Wybierz **ustawienia konta** w panelu nawigacyjnym po lewej stronie.

    ![Konfiguracja LaunchDarkly](./media/launchdarkly-tutorial/configure1.png)

10. Kliknij przycisk **zabezpieczeń** kartę.

    ![Konfiguracja LaunchDarkly](./media/launchdarkly-tutorial/configure2.png)

11. Kliknij przycisk **Włącz logowanie Jednokrotne** i następnie **Edytuj plik konfiguracji SAML**.

    ![Konfiguracja LaunchDarkly](./media/launchdarkly-tutorial/configure3.png)

12. Na **Edytuj konfigurację SAML** sekcji, wykonaj następujące czynności:

    ![Konfiguracja LaunchDarkly](./media/launchdarkly-tutorial/configure4.png)

    a. Kopiuj **adres URL usługi konsumenta SAML** wystąpienia i wklej go w polu tekstowym adres URL odpowiedzi, w **LaunchDarkly, domena i adresy URL** sekcji w witrynie Azure portal.

    b. W **adres URL logowania** pola tekstowego, Wklej **pojedynczy znak na adres URL usługi** wartości, które zostały skopiowane z witryny Azure portal.

    c. Otwórz certyfikat pobrany z witryny Azure portal do Notatnika, skopiuj zawartość, a następnie wklej go do **certyfikat X.509** pola lub możesz bezpośrednio przekazać certyfikat, klikając **przekazać jeden**.

    d. Kliknij pozycję **Zapisz**

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/launchdarkly-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/launchdarkly-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/launchdarkly-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/launchdarkly-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.

### <a name="create-a-launchdarkly-test-user"></a>Tworzenie użytkownika testowego LaunchDarkly

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon w LaunchDarkly. LaunchDarkly obsługę just-in-time, który jest domyślnie włączona. Brak elementu akcji dla Ciebie w tej sekcji. Nowy użytkownik jest tworzony podczas próby dostępu LaunchDarkly, jeśli go jeszcze nie istnieje.
>[!Note]
>Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej klienta LaunchDarkly](mailto:support@launchdarkly.com).

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do LaunchDarkly.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon LaunchDarkly, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **LaunchDarkly**.

    ![Link LaunchDarkly, na liście aplikacji](./media/launchdarkly-tutorial/tutorial_launchdarkly_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka LaunchDarkly w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji LaunchDarkly.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/launchdarkly-tutorial/tutorial_general_01.png
[2]: ./media/launchdarkly-tutorial/tutorial_general_02.png
[3]: ./media/launchdarkly-tutorial/tutorial_general_03.png
[4]: ./media/launchdarkly-tutorial/tutorial_general_04.png

[100]: ./media/launchdarkly-tutorial/tutorial_general_100.png

[200]: ./media/launchdarkly-tutorial/tutorial_general_200.png
[201]: ./media/launchdarkly-tutorial/tutorial_general_201.png
[202]: ./media/launchdarkly-tutorial/tutorial_general_202.png
[203]: ./media/launchdarkly-tutorial/tutorial_general_203.png

