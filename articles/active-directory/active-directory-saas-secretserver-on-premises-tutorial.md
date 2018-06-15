---
title: 'Samouczek: Azure Active Directory integrację z serwerem klucz tajny (lokalnego) | Dokumenty Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między Azure Active Directory i klucz tajny serwera (lokalnego).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: be4ba84a-275d-4f71-afce-cb064edc713f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2018
ms.author: jeedes
ms.openlocfilehash: 8aaf31022d54ebbd8c7c3f0bc72763f8c5139cbc
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34348516"
---
# <a name="tutorial-azure-active-directory-integration-with-secret-server-on-premises"></a>Samouczek: Azure Active Directory integrację z serwerem klucz tajny (lokalnego)

Z tego samouczka dowiesz się integrowanie klucz tajny serwera (lokalnego) w usłudze Azure Active Directory (Azure AD).

Integracja z usługą Azure AD klucz tajny serwera (lokalnego) zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, kto ma dostęp do serwera klucz tajny (lokalnego).
- Umożliwia użytkownikom automatycznie pobrać zalogowane serwerowi hasło (lokalnego) (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z serwerem klucz tajny (lokalnego), potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Klucz tajny serwera (lokalnego) logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodanie serwera klucz tajny (lokalnego) w galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-secret-server-on-premises-from-the-gallery"></a>Dodanie serwera klucz tajny (lokalnego) w galerii
Aby skonfigurować integrację serwera klucz tajny (lokalnego) w usłudze Azure Active Directory, należy dodać klucz tajny serwera (lokalnego) w galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać klucz tajny serwera (lokalnego) w galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **klucz tajny serwera (lokalnego)**, wybierz pozycję **klucz tajny serwera (lokalnego)** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Tajny serwer (lokalnego) na liście wyników](./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_secretserver_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z serwerem klucz tajny (lokalnego) w oparciu o nazwie "Britta Simona" użytkownika testowego.

Do rejestracji jednokrotnej do pracy usługi Azure AD musi ustalić użytkownika odpowiednika w klucz tajny serwera (lokalnego) na użytkownika w usłudze Azure AD. Innymi słowy musi można ustanowić łącze relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w klucz tajny serwera (lokalnego).

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z serwerem klucz tajny (lokalnego), należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego klucz tajny serwera (lokalnego)](#create-a-secret-server-on-premises-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta serwera klucz tajny (lokalnego), który jest połączony z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji klucz tajny serwera (lokalnego).

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z serwerem klucz tajny (lokalnego), wykonaj następujące czynności:**

1. W portalu Azure na **klucz tajny serwera (lokalnego)** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.

    ![Okno dialogowe rejestracji jednokrotnej](./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_secretserver_samlbase.png)

3. Na **adresy URL i klucz tajny serwera (lokalnej) domeny** sekcji, wykonaj następujące kroki, aby skonfigurować aplikację w **IDP** inicjowane tryb:

    ![Tajny domeny serwera (lokalnego) i adres URL z jednym informacje logowania jednokrotnego](./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_secretserver_url.png)

    a. W **identyfikator** pole tekstowe, wprowadź nazwę użytkownika, wybranych wartości, na przykład: `https://secretserveronpremises.azure`

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<SecretServerURL>/SAML/AssertionConsumerService.aspx `

    > [!NOTE]
    > Powyższy identyfikator jednostki jest tylko przykładem i jest swobodę unikatową wartość, który identyfikuje wystąpienia klucz tajny serwera w usłudze Azure AD. Aby wysłać ten identyfikator jednostki [zespołem pomocy technicznej klienta klucz tajny serwera (lokalnego)](https://thycotic.force.com/support/s/) i ich konfigurowanie na bok. Aby uzyskać więcej informacji, przeczytaj [w tym artykule](https://thycotic.force.com/support/s/article/Configuring-SAML-in-Secret-Server).

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** inicjowane tryb:

    ![Tajny domeny serwera (lokalnego) i adres URL z jednym informacje logowania jednokrotnego](./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_secretserver_url1.png)

    W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<SecretServerURL>/login.aspx`
     
    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej klienta klucz tajny serwera (lokalnego)](https://thycotic.force.com/support/s/) uzyskać te wartości.

5. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_secretserver_certificate.png)

6. Sprawdź **Pokaż zaawansowane ustawienia podpisywania certyfikatu** i wybierz **opcja podpisywania** jako **odpowiedzi SAML logowania i potwierdzenia**.

    ![Opcje podpisywania](./media/active-directory-saas-secretserver-on-premises-tutorial/signing.png)

7. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_general_400.png)
    
8. Na **konfiguracji klucz tajny serwera (lokalnego)** , kliknij przycisk **Konfiguracja serwera klucz tajny (lokalnego)** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfiguracja serwera tajne (lokalnego)](./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_secretserver_configure.png)

9. Do konfigurowania rejestracji jednokrotnej na **klucz tajny serwera (lokalnego)** stronie, musisz wysłać pobrany **Certificate(Base64), Sign-Out URL SAML pojedynczego logowania jednokrotnego adres URL usługi**, i **jednostki SAML Identyfikator** do [zespołem pomocy technicznej klucz tajny serwera (lokalnego)](https://thycotic.force.com/support/s/). To ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML one wartość.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/active-directory-saas-secretserver-on-premises-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/active-directory-saas-secretserver-on-premises-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/active-directory-saas-secretserver-on-premises-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/active-directory-saas-secretserver-on-premises-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-secret-server-on-premises-test-user"></a>Tworzenie użytkownika testowego klucz tajny serwera (lokalnego)

W tej sekcji należy utworzyć użytkownika o nazwie Simona Britta w klucz tajny serwera (lokalnego). Praca z [zespołem pomocy technicznej klucz tajny serwera (lokalnego)](https://thycotic.force.com/support/s/) Aby dodać użytkowników na platformie klucz tajny serwera (lokalnego). Użytkownicy muszą utworzyć i aktywowana, aby użyć rejestracji jednokrotnej.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji musisz włączyć Simona Britta do użycia usługi Azure logowania jednokrotnego za udzielanie dostępu do klucza tajnego serwera (lokalnego).

![Przypisanie roli użytkownika][200]

**Aby przypisać Simona Britta klucz tajny serwera (lokalnego), wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji zaznacz **klucz tajny serwera (lokalnego)**.

    ![Łączy klucz tajny serwera (lokalnego) na liście aplikacji](./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_secretserver_app.png)

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka klucz tajny serwera (lokalnego) w panelu dostępu należy należy pobrać automatycznie podpisany w przypadku aplikacji klucz tajny serwera (lokalnego).
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_general_203.png

