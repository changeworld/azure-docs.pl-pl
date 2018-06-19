---
title: 'Samouczek: Integracji Azure Active Directory z Vidyard | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Vidyard.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bed7df23-6e13-4e7c-b4cc-53ed4804664d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2018
ms.author: jeedes
ms.openlocfilehash: 1ad4259ef0e0ca948d677944855ce732b3de98fd
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35938881"
---
# <a name="tutorial-azure-active-directory-integration-with-vidyard"></a>Samouczek: Integracji Azure Active Directory z Vidyard

Z tego samouczka dowiesz się integrowanie Vidyard z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Vidyard zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Vidyard.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Vidyard (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Vidyard, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Vidyard logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Vidyard z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-vidyard-from-the-gallery"></a>Dodawanie Vidyard z galerii
Aby skonfigurować integrację usługi Azure AD Vidyard, należy dodać Vidyard z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Vidyard z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Vidyard**, wybierz pozycję **Vidyard** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Vidyard na liście wyników](./media/vidyard-tutorial/tutorial_vidyard_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Vidyard w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Vidyard jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Vidyard musi się.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Vidyard, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Vidyard](#create-a-vidyard-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Vidyard połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Vidyard.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Vidyard, wykonaj następujące czynności:**

1. W portalu Azure na **Vidyard** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/vidyard-tutorial/tutorial_vidyard_samlbase.png)

3. Na **Vidyard domeny i adres URL** sekcji, wykonaj następujące kroki, aby skonfigurować aplikację w **IDP** inicjowane tryb:

    ![Adresy URL i domeny Vidyard pojedynczy informacje logowania jednokrotnego](./media/vidyard-tutorial/tutorial_vidyard_url2.png)

    a. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://secure.vidyard.com/sso/saml/<unique id>/metadata`

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://secure.vidyard.com/sso/saml/<unique id>/consume`

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** inicjowane tryb:

    ![Adresy URL i domeny Vidyard pojedynczy informacje logowania jednokrotnego](./media/vidyard-tutorial/tutorial_vidyard_url1.png)

    W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://secure.vidyard.com/sso/saml/<unique id>/login`

    > [!NOTE]
    > Wartości te nie są prawdziwe. Te wartości spowoduje zaktualizowanie o rzeczywisty identyfikator, adres URL odpowiedzi i logowania jednokrotnego adresu URL, który znajduje się w dalszej części samouczka

1. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/vidyard-tutorial/tutorial_vidyard_certificate.png) 

6. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/vidyard-tutorial/tutorial_general_400.png)

7. Na **konfiguracji Vidyard** , kliknij przycisk **skonfigurować Vidyard** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfiguracja Vidyard](./media/vidyard-tutorial/tutorial_vidyard_configure.png)

8. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy Vidyard oprogramowania.

9. Na pulpicie nawigacyjnym Vidyard wybierz **grupy** > **zabezpieczeń**

    ![Konfiguracja Vidyard](./media/vidyard-tutorial/configure1.png)

1. Kliknij przycisk **nowy profil** kartę.

    ![Konfiguracja Vidyard](./media/vidyard-tutorial/configure2.png)

11. W **Konfiguracja SAML** sekcji, wykonaj następujące czynności:

    ![Konfiguracja Vidyard](./media/vidyard-tutorial/configure3.png)

    a. Wprowadź nazwę profilu ogólnego w **nazwa profilu** pola tekstowego.

    b. Kopiuj **strony logowania użytkownika logowania jednokrotnego** i wklej go do **Zaloguj się na adres URL** textbox w **sekcji Vidyard domeny i adres URL** w portalu Azure.

    c. Kopia **adres URL usługi ACS** i wklej go do **adres URL odpowiedzi służący** textbox w **sekcji Vidyard domeny i adresy URL** w portalu Azure.

    d. Kopiuj **adres URL wystawcy/metadanych** i wklej go do **identyfikator** textbox w **sekcji Vidyard domeny i adresy URL** w portalu Azure.

    e. Otwórz plik certyfikatu pobrany z portalu Azure w programie Notatnik i wklej go do **certyfikatu X.509** pola tekstowego.

    f. W **adres URL punktu końcowego SAML** pole tekstowe, Wklej wartość **SAML pojedynczy znak na adres URL usługi** skopiowany z portalu Azure.

    g. Kliknij przycisk **potwierdzić**.

12. Na karcie rejestrację jednokrotną, wybierz **przypisać** obok istniejącego profilu

    ![Konfiguracja Vidyard](./media/vidyard-tutorial/configure4.png)

    > [!NOTE]
    > Po utworzeniu profilu rejestracji Jednokrotnej, przypisz je do żadnych grup, dla którego użytkownicy będą potrzebowali dostępu za pośrednictwem platformy Azure. Jeśli użytkownik nie istnieje w grupie, do którego zostały przypisane, Vidyard automatycznie utworzyć konto użytkownika i przypisać ich roli w czasie rzeczywistym.

13. Wybierz grupę organizacji, która jest widoczna w **grup dostępne do przypisania**.

    ![Konfiguracja Vidyard](./media/vidyard-tutorial/configure5.png)

14. Można wyświetlić przypisanej grupy w obszarze **grupy aktualnie przypisane**. Wybierz rolę do grupy, zgodnie z harmonogramem organizacji i kliknij przycisk **Potwierdź**.

    ![Konfiguracja Vidyard](./media/vidyard-tutorial/configure6.png)

    > [!NOTE]
    > Aby uzyskać więcej informacji, zobacz [tego dokumentu](https://knowledge.vidyard.com/saml-single-sign-on-authentication/saml-based-single-sign-on-sso-in-vidyard).

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/vidyard-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/vidyard-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/vidyard-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/vidyard-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-vidyard-test-user"></a>Tworzenie użytkownika testowego Vidyard

Celem tej sekcji jest utworzenie użytkownika o nazwie Simona Britta w Vidyard. Vidyard obsługę w czasie, który jest domyślnie włączone. Nie ma elementu akcji można w tej sekcji. Nowy użytkownik został utworzony podczas próby dostępu Vidyard, jeśli go jeszcze nie istnieje.
>[!Note]
>Jeśli trzeba ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej Vidyard](mailto:support@vidyard.com).

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Vidyard.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta Vidyard, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Vidyard**.

    ![Łącze Vidyard na liście aplikacji](./media/vidyard-tutorial/tutorial_vidyard_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Vidyard w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji Vidyard.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/vidyard-tutorial/tutorial_general_01.png
[2]: ./media/vidyard-tutorial/tutorial_general_02.png
[3]: ./media/vidyard-tutorial/tutorial_general_03.png
[4]: ./media/vidyard-tutorial/tutorial_general_04.png

[100]: ./media/vidyard-tutorial/tutorial_general_100.png

[200]: ./media/vidyard-tutorial/tutorial_general_200.png
[201]: ./media/vidyard-tutorial/tutorial_general_201.png
[202]: ./media/vidyard-tutorial/tutorial_general_202.png
[203]: ./media/vidyard-tutorial/tutorial_general_203.png

