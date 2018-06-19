---
title: 'Samouczek: Integracji Azure Active Directory z ThousandEyes | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i ThousandEyes.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 790e3f1e-1591-4dd6-87df-590b7bf8b4ba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: a08023e4853d999e5e339f6fd54fa9d968c4fec1
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35938900"
---
# <a name="tutorial-azure-active-directory-integration-with-thousandeyes"></a>Samouczek: Integracji Azure Active Directory z ThousandEyes

Z tego samouczka dowiesz się integrowanie ThousandEyes z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD ThousandEyes zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do ThousandEyes
- Umożliwia użytkownikom automatycznie pobrać zalogowane do ThousandEyes (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z ThousandEyes, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- ThousandEyes logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna tutaj: [oferta wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie ThousandEyes z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-thousandeyes-from-the-gallery"></a>Dodawanie ThousandEyes z galerii
Aby skonfigurować integrację usługi Azure AD ThousandEyes, należy dodać ThousandEyes z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać ThousandEyes z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **ThousandEyes**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/thousandeyes-tutorial/tutorial_thousandeyes_search.png)

5. W panelu wyników wybierz **ThousandEyes**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/thousandeyes-tutorial/tutorial_thousandeyes_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z ThousandEyes w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w ThousandEyes jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w ThousandEyes musi się.

W ThousandEyes, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z ThousandEyes, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego ThousandEyes](#creating-a-thousandeyes-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta ThousandEyes połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji ThousandEyes.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z ThousandEyes, wykonaj następujące czynności:**

1. W portalu Azure na **ThousandEyes** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.

    ![Konfigurowanie rejestracji jednokrotnej](./media/thousandeyes-tutorial/tutorial_thousandeyes_samlbase.png)

3. Na **ThousandEyes domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/thousandeyes-tutorial/tutorial_thousandeyes_url.png)

    W **adres URL logowania** tekstowym, wpisz adres URL jako: `https://app.thousandeyes.com/login/sso`

4. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/thousandeyes-tutorial/tutorial_thousandeyes_certificate.png)

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/thousandeyes-tutorial/tutorial_general_400.png)

6. Na **konfiguracji ThousandEyes** , kliknij przycisk **skonfigurować ThousandEyes** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/thousandeyes-tutorial/tutorial_thousandeyes_configure.png) 

7. W oknie przeglądarki innej witryny sieci web, zaloguj się na Twojej **ThousandEyes** witryny firmy jako administrator.

8. W menu u góry kliknij **ustawienia**.

    ![Ustawienia](./media/thousandeyes-tutorial/ic790066.png "ustawienia")

9. Kliknij przycisk **konta**

    ![Konto](./media/thousandeyes-tutorial/ic790067.png "konta")

10. Kliknij przycisk **zabezpieczeń i uwierzytelniania** kartę.

    ![Bezpieczeństwo i uwierzytelniania](./media/thousandeyes-tutorial/ic790068.png "zabezpieczeń i uwierzytelniania")

11. W **ustawienia logowania jednokrotnego** sekcji, wykonaj następujące czynności:

    ![Konfiguracja rejestracji jednokrotnej](./media/thousandeyes-tutorial/ic790069.png "skonfigurować logowanie jednokrotne")

    a. Wybierz **Włącz rejestrację jednokrotną**.

    b. W **adres URL strony logowania** pole tekstowe, Wklej **SAML pojedynczy znak na adres URL usługi**, które zostały skopiowane z portalu Azure.

    c. W **adres URL strony wylogowania** pole tekstowe, Wklej **Sign-Out URL**, które zostały skopiowane z portalu Azure.

    d. **Wystawca dostawcy tożsamości** pole tekstowe, Wklej **identyfikator jednostki SAML**, które zostały skopiowane z portalu Azure.

    e. W **certyfikatu weryfikacji**, kliknij przycisk **wybierz plik**, a następnie przekaż certyfikat został pobrany z portalu Azure.

    f. Kliknij pozycję **Zapisz**.

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/thousandeyes-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/thousandeyes-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/thousandeyes-tutorial/create_aaduser_03.png)

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/thousandeyes-tutorial/create_aaduser_04.png)

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.

### <a name="creating-a-thousandeyes-test-user"></a>Tworzenie użytkownika testowego ThousandEyes

Celem tej sekcji jest utworzenie użytkownika o nazwie Simona Britta w ThousandEyes. ThousandEyes obsługę automatycznego użytkownika, który jest domyślnie włączone. Więcej informacji można znaleźć [tutaj](thousandeyes-provisioning-tutorial.md) na temat konfigurowania użytkowników automatycznego inicjowania obsługi administracyjnej.

**Jeśli trzeba ręcznie utworzyć użytkownika, należy wykonać następujące kroki:**

1. Zaloguj się do witryny firmy ThousandEyes jako administrator.

2. Kliknij przycisk **ustawienia**.

    ![Ustawienia](./media/thousandeyes-tutorial/IC790066.png "ustawienia")

3. Kliknij przycisk **konta**.

    ![Konto](./media/thousandeyes-tutorial/IC790067.png "konta")

4. Kliknij przycisk **konta & użytkowników** kartę.

    ![Konta & użytkowników](./media/thousandeyes-tutorial/IC790073.png "konta & użytkowników")

5. W **Dodawanie użytkowników i kont** sekcji, wykonaj następujące czynności:

    ![Dodaj konta użytkowników](./media/thousandeyes-tutorial/IC790074.png "dodawania kont użytkowników")

    a. W **nazwa** tekstowym, wpisz nazwę użytkownika, takich jak **Simona Britta**.

    b. W **E-mail** tekstowym, wpisz adres e-mail użytkownika, takich jak **brittasimon@contoso.com**.

    b. Kliknij przycisk **Dodaj nowego użytkownika do konta**.

    > [!NOTE]
    > Właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail, łącznie z łączem do potwierdzenia i Aktywuj konta.

> [!NOTE]
> Możesz użyć innych ThousandEyes użytkownika konta tworzenia narzędzi lub interfejsów API dostarczonych przez ThousandEyes do świadczenia usługi Azure Active Directory kont użytkowników.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu ThousandEyes.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta ThousandEyes, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **ThousandEyes**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/thousandeyes-tutorial/tutorial_thousandeyes_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka ThousandEyes w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji ThousandEyes.

Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Skonfiguruj Inicjowanie obsługi użytkowników](thousandeyes-provisioning-tutorial.md)


<!--Image references-->

[1]: ./media/thousandeyes-tutorial/tutorial_general_01.png
[2]: ./media/thousandeyes-tutorial/tutorial_general_02.png
[3]: ./media/thousandeyes-tutorial/tutorial_general_03.png
[4]: ./media/thousandeyes-tutorial/tutorial_general_04.png

[100]: ./media/thousandeyes-tutorial/tutorial_general_100.png

[200]: ./media/thousandeyes-tutorial/tutorial_general_200.png
[201]: ./media/thousandeyes-tutorial/tutorial_general_201.png
[202]: ./media/thousandeyes-tutorial/tutorial_general_202.png
[203]: ./media/thousandeyes-tutorial/tutorial_general_203.png
