---
title: 'Samouczek: Integracji Azure Active Directory z informacjami dotyczącymi adaptacyjną | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i adaptacyjną szczegółowych informacji.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/14/2018
ms.author: jeedes
ms.openlocfilehash: 545255affa61fc84d3f1ceb9830396e8d843e411
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34589981"
---
# <a name="tutorial-azure-active-directory-integration-with-adaptive-insights"></a>Samouczek: Integracji Azure Active Directory z informacjami dotyczącymi adaptacyjną

Z tego samouczka dowiesz się integrowanie adaptacyjną szczegółowych informacji z usługi Azure Active Directory (Azure AD).

Integrowanie adaptacyjną szczegółowych informacji z usługi Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do szczegółowych danych adaptacyjną
- Umożliwia użytkownikom automatycznie pobrać zalogowane adaptacyjną insights (logowanie jednokrotne) z konta usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z adaptacyjną szczegółowe informacje, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Adaptacyjną Insights logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie adaptacyjną Insights z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-adaptive-insights-from-the-gallery"></a>Dodawanie adaptacyjną Insights z galerii
Aby skonfigurować integrację z adaptacyjną wgląd w usłudze Azure AD, należy dodać adaptacyjną Insights z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać adaptacyjną Insights z poziomu galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]

3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **adaptacyjną Insights**, wybierz pozycję **adaptacyjną Insights** z panelu wyników następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z adaptacyjną szczegółowe informacje, na podstawie użytkownika testowego, nazywany "Britta Simona".

Do rejestracji jednokrotnej do pracy usługi Azure AD musi ustalić użytkownika odpowiednika w adaptacyjną wgląd do użytkownika w usłudze Azure AD. Innymi słowy musi można ustanowić łącze relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w adaptacyjną szczegółowych informacji.

Do konfigurowania i testowania usługi Azure AD rejestracji jednokrotnej z adaptacyjną szczegółowe informacje, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego adaptacyjną Insights](#creating-an-adaptive-insights-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta adaptacyjną wgląd, połączonej z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji adaptacyjną szczegółowych informacji.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z informacjami dotyczącymi adaptacyjną, wykonaj następujące czynności:**

1. W portalu Azure na **adaptacyjną Insights** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_samlbase.png)

3. Na **adaptacyjną Insights domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_url.png)

    a. W **identyfikator jednostki** tekstowym, wpisz adres URL, używając następującego wzorca: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    >[!NOTE]
    > Identyfikator jednostki i adres URL odpowiedzi wartości można uzyskać z adaptacyjną szczegółowych danych **ustawienia logowania jednokrotnego SAML** strony.

4. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_certificate.png)

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_400.png)

6. Na **adaptacyjną konfiguracji Insights** , kliknij przycisk **skonfigurować adaptacyjną Insights** można otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_configure.png) 

7. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy adaptacyjną szczegółowych informacji.

8. Przejdź do **Admin**.

    ![Administrator](./media/active-directory-saas-adaptivesuite-tutorial/IC805644.png "administratora")

9. W **użytkownikami i rolami** kliknij **Zarządzaj ustawieniami logowania jednokrotnego SAML**.

    ![Zarządzanie ustawieniami logowania jednokrotnego SAML](./media/active-directory-saas-adaptivesuite-tutorial/IC805645.png "Zarządzanie ustawieniami logowania jednokrotnego SAML")

10. Na **ustawienia logowania jednokrotnego SAML** wykonaj następujące czynności:

    ![Ustawienia logowania jednokrotnego SAML](./media/active-directory-saas-adaptivesuite-tutorial/IC805646.png "ustawienia logowania jednokrotnego SAML")

    a. W **Nazwa dostawcy tożsamości** tekstowym, wpisz nazwę dla danej konfiguracji.

    b. Wklej **identyfikator jednostki SAML** wartość skopiowany z portalu Azure do **dostawcy tożsamości identyfikator jednostki** pola tekstowego.

    c. Wklej **SAML pojedynczy znak na adres URL usługi** wartość skopiowany z portalu Azure do **dostawcy tożsamości adresu URL logowania jednokrotnego** pola tekstowego.

    d. Wklej **SAML pojedynczy znak na adres URL usługi** wartość skopiowany z portalu Azure do **niestandardowy adres URL wylogowania** pola tekstowego.

    e. Aby przekazać certyfikat pobrany, kliknij przycisk **wybierz plik**.

    f. Wykonaj następujące czynności, aby uzyskać:

    * **Identyfikator użytkownika SAML**, wybierz pozycję **nazwy użytkownika adaptacyjną Insights**.

    * **Lokalizacja identyfikator użytkownika SAML**, wybierz pozycję **identyfikatora użytkownika w NameID podmiotu**.

    * **Format SAML NameID**, wybierz pozycję **adres E-mail**.

    * **Włącz SAML**, wybierz pozycję **Zezwalaj logowania jednokrotnego SAML i bezpośrednie logowanie adaptacyjną Insights**.

    g. Kopiowania **adaptacyjną adres URL logowania jednokrotnego Insights** i Wklej do **identyfikator jednostki** i **adres URL odpowiedzi służący** pól tekstowych w **adaptacyjną Insights domeny i adres URL** sekcji w portalu Azure.

    h. Kliknij pozycję **Zapisz**.

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-adaptivesuite-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-adaptivesuite-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-adaptivesuite-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-adaptivesuite-tutorial/create_aaduser_04.png)

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.

### <a name="creating-an-adaptive-insights-test-user"></a>Tworzenie użytkownika testowego adaptacyjną Insights

Aby umożliwić użytkownikom usługi Azure AD zalogować się do adaptacyjnego wgląd, muszą mieć przydzielone do adaptacyjnego szczegółowych informacji. W przypadku adaptacyjną wgląd Inicjowanie obsługi to zadanie ręczne.

**Aby skonfigurować, inicjowanie obsługi użytkowników, wykonaj następujące czynności:** 

1. Zaloguj się do Twojego **Insights adaptacyjną** witryny firmy jako administrator.
2. Przejdź do **Admin**.

   ![Administrator](./media/active-directory-saas-adaptivesuite-tutorial/IC805644.png "administratora")
3. W **użytkownikami i rolami** kliknij **Dodaj użytkownika**.

   ![Dodaj użytkownika](./media/active-directory-saas-adaptivesuite-tutorial/IC805648.png "Dodaj użytkownika")
4. W **nowego użytkownika** sekcji, wykonaj następujące czynności:

   ![Przedstawia](./media/active-directory-saas-adaptivesuite-tutorial/IC805649.png "przesyłania")

   a. Typ **nazwa**, **logowania**, **E-mail**, **hasło** z prawidłowym użytkownikiem usługi Azure Active Directory chcesz udostępnić w odnośnych pola tekstowe.

   b. Wybierz **roli**.

   c. Kliknij przycisk **przesłać**.

>[!NOTE]
>Możesz użyć innych adaptacyjną Insights użytkownika konta tworzenia narzędzi lub interfejsów API dostarczonych przez adaptacyjną wgląd do kont użytkowników usługi AAD.
>

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do adaptacyjnego szczegółowych informacji.

![Przypisz użytkownika][200]

**Aby przypisać Simona Britta do adaptacyjnego szczegółowych danych, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji zaznacz **Insights adaptacyjną**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_app.png)

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.

### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

Celem tej sekcji służy do testowania usługi Microsoft Azure AD rejestracji jednokrotnej konfigurację za pomocą panelu dostępu.

Po kliknięciu kafelka adaptacyjną wgląd w panelu dostępu należy należy pobrać automatycznie zalogowane do aplikacji adaptacyjną szczegółowych informacji.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_203.png
