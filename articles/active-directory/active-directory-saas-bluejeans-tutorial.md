---
title: 'Samouczek: Integracji Azure Active Directory z BlueJeans | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i BlueJeans.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: jeedes
ms.openlocfilehash: da76313abbb4d65916fedf1b541fc948cb0e97cb
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34590566"
---
# <a name="tutorial-azure-active-directory-integration-with-bluejeans"></a>Samouczek: Integracji Azure Active Directory z BlueJeans

Z tego samouczka dowiesz się integrowanie BlueJeans z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD BlueJeans zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do BlueJeans
- Umożliwia użytkownikom automatycznie pobrać zalogowane do BlueJeans (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z BlueJeans, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- BlueJeans jednokrotnego włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie BlueJeans z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-bluejeans-from-the-gallery"></a>Dodawanie BlueJeans z galerii
Aby skonfigurować integrację usługi Azure AD BlueJeans, należy dodać BlueJeans z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać BlueJeans z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]

3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **BlueJeans**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-bluejeans-tutorial/tutorial_bluejeans_search.png)

5. W panelu wyników wybierz **BlueJeans**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-bluejeans-tutorial/tutorial_bluejeans_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z BlueJeans na podstawie użytkownika testowego, nazywany "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w BlueJeans jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w BlueJeans musi się.

W BlueJeans, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z BlueJeans, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego BlueJeans](#creating-a-bluejeans-test-user)**  — aby odpowiednikiem Britta Simona w BlueJeans jest połączony z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji BlueJeans.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z BlueJeans, wykonaj następujące czynności:**

1. W portalu Azure na **BlueJeans** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-bluejeans-tutorial/tutorial_bluejeans_samlbase.png)

3. Na **BlueJeans domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-bluejeans-tutorial/tutorial_bluejeans_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<companyname>.BlueJeans.com`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<companyname>.BlueJeans.com`

    > [!NOTE]
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL logowania i identyfikator, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej klienta BlueJeans](https://support.bluejeans.com/contact) uzyskać te wartości.

4. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-bluejeans-tutorial/tutorial_bluejeans_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-bluejeans-tutorial/tutorial_general_400.png)

6. Na **konfiguracji BlueJeans** , kliknij przycisk **skonfigurować BlueJeans** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adres URL, Zmień adres URL hasła i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-bluejeans-tutorial/tutorial_bluejeans_configure.png) 

7. W oknie przeglądarki innej witryny sieci web, zaloguj się do Twojego **BlueJeans** witryny firmy jako administrator.

8. Przejdź do **ADMIN \> ustawienia grupy \> zabezpieczeń**.

   ![Administrator](./media/active-directory-saas-bluejeans-tutorial/IC785868.png "administratora")

9. W **zabezpieczeń** sekcji, wykonaj następujące czynności:

   ![SAML logowania jednokrotnego](./media/active-directory-saas-bluejeans-tutorial/IC785869.png "SAML logowania jednokrotnego")

   a. Wybierz **SAML logowania jednokrotnego**.

   b. Wybierz **Włącz automatyczne udostępnianie**.

10. Przenieś następujące czynności:

    ![Ścieżka certyfikatu](./media/active-directory-saas-bluejeans-tutorial/IC785870.png "ścieżka certyfikatu")

    a. Kliknij przycisk **wybierz plik**, a następnie przekaż pobranego certyfikatu.

    b. Wklej **SAML pojedynczy znak na adres URL usługi** do **adres URL logowania** pola tekstowego.

    c. Wklej **Zmień adres URL hasła** do **hasła, Zmień adres URL** pola tekstowego.

    d. Wklej **Sign-Out URL** do **adresu URL wylogowania** pola tekstowego.

11. Przenieś następujące czynności:

    ![Zapisz zmiany](./media/active-directory-saas-bluejeans-tutorial/IC785874.png "zapisać zmiany")

    a. W **identyfikator użytkownika** pole tekstowe, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    b. W **E-mail** pole tekstowe, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    c. Kliknij przycisk **zapisać zmiany**.

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-bluejeans-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-bluejeans-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-bluejeans-tutorial/create_aaduser_03.png)

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-bluejeans-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.

### <a name="creating-a-bluejeans-test-user"></a>Tworzenie użytkownika testowego BlueJeans

Celem tej sekcji jest utworzenie użytkownika o nazwie Simona Britta w BlueJeans. BlueJeans obsługę automatycznego użytkownika, który jest domyślnie włączone. Więcej informacji można znaleźć [tutaj](active-directory-saas-bluejeans-provisioning-tutorial.md) na temat konfigurowania użytkowników automatycznego inicjowania obsługi administracyjnej.

**Jeśli trzeba ręcznie utworzyć użytkownika, należy wykonać następujące kroki:**

1. Zaloguj się do Twojego **BlueJeans** witryny firmy jako administrator.

2. Przejdź do **ADMIN \> Zarządzanie użytkownikami \> dodać użytkownika**.

   ![Administrator](./media/active-directory-saas-bluejeans-tutorial/IC785877.png "administratora")

   >[!IMPORTANT]
   >**Dodaj użytkownika** karta jest dostępna tylko, jeśli w **kartę Zabezpieczenia**, **Włącz automatyczne udostępnianie** nie jest zaznaczone. 

3. W **Dodaj użytkownika** sekcji, wykonaj następujące czynności:

    ![Dodaj użytkownika](./media/active-directory-saas-bluejeans-tutorial/IC785886.png "Dodaj użytkownika")

    a. Wpisz **BlueJeans Username**, **adres E-mail**, **identyfikator spotkania BlueJeans**, **moderatora kod dostępu**, **imię i nazwisko**, **firmy** poprawnego konta usługi AAD ustanawiane do powiązanych pól tekstowych.

    b. Kliknij przycisk **dodać użytkownika**.

>[!NOTE]
>Możesz użyć innych BlueJeans użytkownika konta tworzenia narzędzi lub interfejsów API dostarczonych przez BlueJeans do kont użytkowników usługi AAD.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu BlueJeans.

![Przypisz użytkownika][200]

**Aby przypisać Simona Britta BlueJeans, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji zaznacz **BlueJeans**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-bluejeans-tutorial/tutorial_bluejeans_app.png)

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.

### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka BlueJeans w panelu dostępu, należy pobrać strony logowania BlueJeans aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)
* [Skonfiguruj Inicjowanie obsługi użytkowników](active-directory-saas-bluejeans-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_203.png
