---
title: 'Samouczek: Integracja usługi Azure Active Directory z dryfu | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i odejściem od tego stanu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 39dcbb95-c192-448c-86a1-cedede1c0972
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2018
ms.author: jeedes
ms.openlocfilehash: c08b57c41cc43bfa47dee69a75663e7a58ef4c21
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2018
ms.locfileid: "52632844"
---
# <a name="tutorial-azure-active-directory-integration-with-drift"></a>Samouczek: Integracja usługi Azure Active Directory z odejściem od tego stanu

W tym samouczku dowiesz się, jak zintegrować odejściem od tego stanu za pomocą usługi Azure Active Directory (Azure AD).

Integrowanie odejściem od tego stanu za pomocą usługi Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do odejściem od tego stanu.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do dryfu (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z odejściem od tego stanu, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Dryfu logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie odejściem od tego stanu za pomocą galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-drift-from-the-gallery"></a>Dodawanie odejściem od tego stanu za pomocą galerii

Aby skonfigurować integrację odejściem od tego stanu w usłudze Azure AD, należy dodać kilka z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać kilka z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **dryfu**, wybierz opcję **dryfu** z panelu wynik następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Odstępstw na liście wyników](./media/drift-tutorial/tutorial_drift_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą odchylenie w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w odejściem od tego stanu do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w kilka musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą odejściem od tego stanu, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego dryfu](#creating-a-drift-test-user)**  — aby odpowiednikiem Britta Simon w dryfu połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji odejściem od tego stanu.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z odejściem od tego stanu, wykonaj następujące czynności:**

1. W witrynie Azure portal na **dryfu** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, kliknij przycisk **wybierz** dla **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![Konfigurowanie logowania jednokrotnego](common/tutorial_general_301.png)

3. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** ikonę, aby otworzyć **podstawową konfigurację protokołu SAML** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](common/editconfigure.png)

4. Na **podstawową konfigurację protokołu SAML** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![Domena i adresy URL pojedynczego logowania jednokrotnego informacji odstępstw](./media/drift-tutorial/tutorial_drift_url.png)

    a. Kliknij przycisk **Ustaw dodatkowe adresy URL**.

    b. W **tan przekaźnika** pole tekstowe, wpisz adres URL: `https://app.drift.com`

    c. Jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb, w **adres URL logowania** pole tekstowe, wpisz adres URL: `https://start.drift.com`

5. Aplikacja dryfu oczekuje twierdzenia SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Możesz zarządzać wartości te atrybuty z **atrybutów użytkowników i oświadczeń** sekcji na stronie integracji aplikacji. Kliknij przycisk **Edytuj** przycisk, aby otworzyć **atrybutów użytkowników i oświadczeń** okna dialogowego.

    ![image](./media/drift-tutorial/tutorial_drift_attribute.png)

6. W **oświadczenia użytkownika** sekcji na **atrybutów użytkowników i oświadczeń** okno dialogowe, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej i wykonaj następujące czynności:
    
    | Nazwa atrybutu | Wartość atrybutu |
    | ---------------| --------------- |    
    | Name (Nazwa) | user.displayname |

    a. Kliknij pozycję `name` oświadczenia (wyróżnione oświadczeń), aby otworzyć **Zarządzanie oświadczenia użytkownika** okna dialogowego.

    ![image](./media/drift-tutorial/tutorial_drift_attribute1.png)

    ![image](./media/drift-tutorial/tutorial_drift_attribute3.png)

    b. Wybierz źródło jako **atrybutu**.

    c. Wprowadź **Namespace** puste.

    d. Z **atrybut źródłowy** listy wybierz **user.displayname**.

    e. Kliknij pozycję **Zapisz**.

7. Na **certyfikat podpisywania SAML** strony w **certyfikat podpisywania SAML** kliknij **Pobierz** można pobrać **XML metadanych Federacji** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/drift-tutorial/tutorial_drift_certificate.png) 

8. W oknie przeglądarki internetowej innej, zaloguj się do dryfu jako Administrator.

9. Z lewej strony paska menu, kliknij pozycję **ikonę ustawienia** > **ustawienia aplikacji** > **uwierzytelniania** i wykonaj następujące czynności:

    ![Link administratora](./media/drift-tutorial/tutorial_drift_admin.png)

    a. Przekaż **XML metadanych Federacji** pobrany z witryny Azure portal do **dostawcy tożsamości Przekaż plik metadanych** pola tekstowego.

    b. Po przekazaniu pliku metadanych, pozostałe wartości Uzyskaj automatycznie wypełniane na stronie automatycznie.

    c. Kliknij przycisk **Włącz SAML**.

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

### <a name="creating-a-drift-test-user"></a>Tworzenie użytkownika testowego odejściem od tego stanu

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon w odejściem od tego stanu. Dryfu obsługę just-in-time, który jest domyślnie włączona. Brak elementu akcji dla Ciebie w tej sekcji. Nowy użytkownik jest tworzony podczas próby dostępu dryfu, jeśli go jeszcze nie istnieje.
>[!Note]
>Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej dryfu](mailto:integrations@drift.com).

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon korzystać z platformy Azure logowania jednokrotnego przez udzielenie dostępu po odejściem od tego stanu.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji wybierz **dryfu**.

    ![Konfigurowanie logowania jednokrotnego](./media/drift-tutorial/tutorial_drift_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. W **użytkowników i grup** okna dialogowego wybierz **Britta Simon** na liście użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

6. W **Dodaj przydziału**, wybierz okno dialogowe **przypisać** przycisku.

### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka odejściem od tego stanu w panelu dostępu, możesz należy pobrać automatycznie zalogowanych do aplikacji odejściem od tego stanu.
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
