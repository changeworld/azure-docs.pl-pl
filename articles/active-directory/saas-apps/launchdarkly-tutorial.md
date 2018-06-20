---
title: 'Samouczek: Integracji Azure Active Directory z LaunchDarkly | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i LaunchDarkly.
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
ms.openlocfilehash: 8653fe2053b2a49967b5b0f98654c20f8999d7ff
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36217500"
---
# <a name="tutorial-azure-active-directory-integration-with-launchdarkly"></a>Samouczek: Integracji Azure Active Directory z LaunchDarkly

Z tego samouczka dowiesz się integrowanie LaunchDarkly z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD LaunchDarkly zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do LaunchDarkly.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do LaunchDarkly (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z LaunchDarkly, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- LaunchDarkly logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie LaunchDarkly z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-launchdarkly-from-the-gallery"></a>Dodawanie LaunchDarkly z galerii
Aby skonfigurować integrację usługi Azure AD LaunchDarkly, należy dodać LaunchDarkly z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać LaunchDarkly z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **LaunchDarkly**, wybierz pozycję **LaunchDarkly** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![LaunchDarkly na liście wyników](./media/launchdarkly-tutorial/tutorial_launchdarkly_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z LaunchDarkly w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w LaunchDarkly jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w LaunchDarkly musi się.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z LaunchDarkly, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego LaunchDarkly](#create-a-launchdarkly-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta LaunchDarkly połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji LaunchDarkly.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z LaunchDarkly, wykonaj następujące czynności:**

1. W portalu Azure na **LaunchDarkly** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.

    ![Okno dialogowe rejestracji jednokrotnej](./media/launchdarkly-tutorial/tutorial_launchdarkly_samlbase.png)

3. Na **LaunchDarkly domeny i adres URL** sekcji, wykonaj następujące kroki, aby skonfigurować aplikację w **IDP** inicjowane tryb:

    ![Adresy URL i domeny LaunchDarkly pojedynczy informacje logowania jednokrotnego](./media/launchdarkly-tutorial/tutorial_launchdarkly_url.png)

    a. W **identyfikator jednostki** tekstowym, wpisz adres URL: `app.launchdarkly.com`

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://app.launchdarkly.com/trust/saml2/acs/<customers-unique-id>`
    
    > [!NOTE]
    > Wartość adresu URL odpowiedzi nie jest prawdziwe. Wartość zaktualizuje rzeczywiste odpowiedzi adres URL, który znajduje się w dalszej części tego samouczka.

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** inicjowane tryb:

    ![Adresy URL i domeny LaunchDarkly pojedynczy informacje logowania jednokrotnego](./media/launchdarkly-tutorial/tutorial_launchdarkly_url1.png)

    W **adres URL logowania** tekstowym, wpisz adres URL: `https://app.launchdarkly.com`

5. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/launchdarkly-tutorial/tutorial_launchdarkly_certificate.png) 

6. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/launchdarkly-tutorial/tutorial_general_400.png)
    
7. Na **konfiguracji LaunchDarkly** , kliknij przycisk **skonfigurować LaunchDarkly** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfiguracja LaunchDarkly](./media/launchdarkly-tutorial/tutorial_launchdarkly_configure.png)

8. W oknie przeglądarki innej witryny sieci web Zaloguj się do witryny firmy LaunchDarkly jako administrator.

9. Wybierz **ustawienia konta** z panelu nawigacji po lewej stronie.

    ![Konfiguracja LaunchDarkly](./media/launchdarkly-tutorial/configure1.png)

10. Kliknij przycisk **zabezpieczeń** kartę.

    ![Konfiguracja LaunchDarkly](./media/launchdarkly-tutorial/configure2.png)

11. Kliknij przycisk **Włączanie logowania jednokrotnego** , a następnie **Konfiguracja SAML EDYCJI**.

    ![Konfiguracja LaunchDarkly](./media/launchdarkly-tutorial/configure3.png)

12. Na **Edytuj konfigurację SAML** sekcji, wykonaj następujące czynności:

    ![Konfiguracja LaunchDarkly](./media/launchdarkly-tutorial/configure4.png)

    a. Kopiuj **adres URL usługi klienta SAML** dla swojego wystąpienia i wklej go w polu tekstowym adres URL odpowiedzi w **LaunchDarkly domeny i adres URL** sekcji z portalu Azure.

    b. W **adres URL logowania** pole tekstowe, Wklej **pojedynczy znak na adres URL usługi** wartość, która została skopiowana z portalu Azure.

    c. Otwórz certyfikat pobrany z portalu Azure do Notatnika, skopiuj zawartość, a następnie wklej go do **certyfikatu X.509** pole lub bezpośrednio można przekazać certyfikatu klikając **przekazać jeden**.

    d. Kliknij pozycję **Zapisz**

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/launchdarkly-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/launchdarkly-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/launchdarkly-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/launchdarkly-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.

### <a name="create-a-launchdarkly-test-user"></a>Tworzenie użytkownika testowego LaunchDarkly

Celem tej sekcji jest utworzenie użytkownika o nazwie Simona Britta w LaunchDarkly. LaunchDarkly obsługę w czasie, który jest domyślnie włączone. Nie ma elementu akcji można w tej sekcji. Nowy użytkownik został utworzony podczas próby dostępu LaunchDarkly, jeśli go jeszcze nie istnieje.
>[!Note]
>Jeśli trzeba ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej klienta LaunchDarkly](mailto:support@launchdarkly.com).

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu LaunchDarkly.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta LaunchDarkly, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **LaunchDarkly**.

    ![Łącze LaunchDarkly na liście aplikacji](./media/launchdarkly-tutorial/tutorial_launchdarkly_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka LaunchDarkly w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji LaunchDarkly.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
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

