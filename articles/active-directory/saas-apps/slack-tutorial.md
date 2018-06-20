---
title: 'Samouczek: Integracji Azure Active Directory z zapas czasu | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i zapas czasu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ffc5e73f-6c38-4bbb-876a-a7dd269d4e1c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 5d16e633f29ca635a9b32118e699cb59d3896353
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36221346"
---
# <a name="tutorial-azure-active-directory-integration-with-slack"></a>Samouczek: Integracji Azure Active Directory z zapas czasu

Z tego samouczka dowiesz się integrowanie zapas czasu w usłudze Azure Active Directory (Azure AD).

Integrowanie zapas czasu z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do zapas czasu
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Slack (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z zapas czasu, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Slack logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie zapas czasu z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-slack-from-the-gallery"></a>Dodawanie zapas czasu z galerii
Aby skonfigurować integrację zapas czasu w usłudze Azure Active Directory, należy dodać zapas czasu z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać zapas czasu z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Slack**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/slack-tutorial/tutorial_slack_search.png)

5. W panelu wyników wybierz **Slack**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/slack-tutorial/tutorial_slack_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z zapas czasu, w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w zapas czasu jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w zapas czasu musi się.

W zapas czasu, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z zapas czasu, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Slack](#creating-a-slack-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta zapas czasu połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Slack.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z zapas czasu, wykonaj następujące czynności:**

1. W portalu Azure na **Slack** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/slack-tutorial/tutorial_slack_samlbase.png)

3. Na **domeny zapas czasu i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/slack-tutorial/tutorial_slack_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<companyname>.slack.com`

    b. W **identyfikator** tekstowym, wpisz adres URL: `https://slack.com`

    > [!NOTE] 
    > Wartość nie jest prawdziwe. Należy zaktualizować wartości rzeczywistych logowania na adres URL. Skontaktuj się z [zespołem pomocy technicznej Slack](https://slack.com/help/contact) można uzyskać wartość.
     
4. Slack aplikacji oczekuje potwierdzenia języka SAML w określonym formacie. Skonfiguruj następujące oświadczeń dla tej aplikacji. Możesz zarządzać wartości tych atrybutów z "**atrybuty użytkownika**" sekcji na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład tego.
    
    ![Konfigurowanie rejestracji jednokrotnej](./media/slack-tutorial/tutorial_slack_attribute.png)

    > [!NOTE] 
    > Jeśli masz użytkowników, którzy są przypisani **adres e-mail** nie znajduje się na licencji usługi Office 365, **User.Email** oświadczenia nie będą widoczne w tokenu SAML. W takich przypadkach zaleca się przy użyciu **user.userprincipalname** jako **User.Email** atrybutu wartość do zamapowania jako **Unikatowy identyfikator** zamiast tego.

5. W **atrybuty użytkownika** sekcji na **logowanie jednokrotne** okno dialogowe, wybierz opcję **user.mail** jako **identyfikator użytkownika** i dla każdego wiersza w tabeli poniżej, wykonaj następujące czynności:
    
    | Nazwa atrybutu | Wartość atrybutu |
    | --- | --- |
    | first_name | user.givenname |
    | last_name | user.surname |
    | User.Email | User.mail |  
    | User.Username | user.userprincipalname |

    a. Polecenie **atrybutu** otworzyć **atrybutu Edytuj** okna dialogowego polu i wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/slack-tutorial/tutorial_slack_attribute1.png)

    a. W **nazwa** tekstowym, wpisz nazwę atrybut wyświetlany dla danego wiersza.

    b. Z **wartość** listy, wybierz wartość atrybutu wyświetlany dla danego wiersza.

    c. Pozostaw **Namespace** puste.

    d. Kliknij przycisk **OK**.

6. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/slack-tutorial/tutorial_slack_certificate.png)

7. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/slack-tutorial/tutorial_general_400.png)

8. Na **konfiguracji zapas czasu** kliknij **skonfigurować zapas czasu** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/slack-tutorial/tutorial_slack_configure.png)

9. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy Slack.

10. Przejdź do **usługi Microsoft Azure AD** następnie przejdź do **ustawień zespołu**.

     ![Konfigurowanie rejestracji jednokrotnej po stronie aplikacji](./media/slack-tutorial/tutorial_slack_001.png)

11. W **ustawień zespołu** kliknij **uwierzytelniania** , a następnie kliknij pozycję **Zmień ustawienia**.

    ![Konfigurowanie rejestracji jednokrotnej po stronie aplikacji](./media/slack-tutorial/tutorial_slack_002.png)

12. Na **ustawienia uwierzytelniania SAML** okna dialogowego, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej po stronie aplikacji](./media/slack-tutorial/tutorial_slack_003.png)

    a.  W **SAML 2.0 punktu końcowego (HTTP)** pole tekstowe, Wklej wartość **SAML pojedynczy znak na adres URL usługi**, które zostały skopiowane z portalu Azure.

    b.  W **wystawcy dostawcy tożsamości** pole tekstowe, Wklej wartość **identyfikator jednostki SAML**, które zostały skopiowane z portalu Azure.

    c.  Otwórz w Notatniku plik certyfikatu pobrane, skopiuj zawartość go do Schowka, a następnie wklej go do **certyfikatu publicznego** pola tekstowego.

    d. Skonfiguruj powyżej trzy ustawienia odpowiednie dla swojego zespołu Slack. Aby uzyskać więcej informacji na temat ustawień znaleźć **Przewodnik po konfiguracji logowania jednokrotnego zapas czasu jego** tutaj. `https://get.slack.help/hc/articles/220403548-Guide-to-single-sign-on-with-Slack%60`

    e.  Kliknij przycisk **Zapisz konfigurację**.

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/slack-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/slack-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/slack-tutorial/create_aaduser_03.png)

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/slack-tutorial/create_aaduser_04.png)

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.

### <a name="creating-a-slack-test-user"></a>Tworzenie użytkownika testowego Slack

Celem tej sekcji jest utworzenie użytkownika o nazwie Simona Britta w Slack. Zapas czasu obsługę w czasie, który jest domyślnie włączone. Nie ma elementu akcji można w tej sekcji. Nowy użytkownik został utworzony podczas próby dostępu zapas czasu, jeśli go jeszcze nie istnieje. Zapas obsługuje również Inicjowanie obsługi użytkowników, można znaleźć więcej szczegółów [tutaj](slack-provisioning-tutorial.md) na temat konfigurowania użytkowników automatycznego inicjowania obsługi administracyjnej.

> [!NOTE]
> Jeśli trzeba ręcznie utworzyć użytkownika, należy skontaktować się [zespołem pomocy technicznej Slack](https://slack.com/help/contact).

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu zapas czasu.

![Przypisz użytkownika][200]

**Aby przypisać Simona Britta zapas czasu, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Slack**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/slack-tutorial/tutorial_slack_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Slack w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane Slack aplikacji.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Skonfiguruj Inicjowanie obsługi użytkowników](slack-provisioning-tutorial.md)


<!--Image references-->

[1]: ./media/slack-tutorial/tutorial_general_01.png
[2]: ./media/slack-tutorial/tutorial_general_02.png
[3]: ./media/slack-tutorial/tutorial_general_03.png
[4]: ./media/slack-tutorial/tutorial_general_04.png

[100]: ./media/slack-tutorial/tutorial_general_100.png

[200]: ./media/slack-tutorial/tutorial_general_200.png
[201]: ./media/slack-tutorial/tutorial_general_201.png
[202]: ./media/slack-tutorial/tutorial_general_202.png
[203]: ./media/slack-tutorial/tutorial_general_203.png
