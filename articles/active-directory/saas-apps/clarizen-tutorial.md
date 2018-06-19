---
title: 'Samouczek: Integracji Azure Active Directory z Clarizen | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Clarizen.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: jeedes
ms.openlocfilehash: 881f9398c1f6de4d3cc083dee34b6417a22eab29
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35942625"
---
# <a name="tutorial-azure-active-directory-integration-with-clarizen"></a>Samouczek: Integracji Azure Active Directory z Clarizen

Z tego samouczka dowiesz Integrowanie usługi Azure Active Directory (Azure AD) z Clarizen. Integracja ta zapewnia następujące korzyści:

- Można kontrolować, w usłudze Azure AD, który ma dostęp do Clarizen.
- Można umożliwić użytkownikom można zostanie automatycznie zalogowany do Clarizen (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji, portalu Azure.

Scenariusz, w tym samouczku składa się z dwóch głównych zadań:

1. Dodaj Clarizen z galerii.
2. Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej.

Więcej informacji na temat oprogramowania jako usługa (SaaS) integracji aplikacji z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne
Aby skonfigurować integrację usługi Azure AD z Clarizen, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Clarizen subskrypcji, która jest włączone dla rejestracji jednokrotnej

Aby przetestować kroki opisane w tym samouczku, wykonaj te zalecenia:

- Testowanie usługi Azure AD rejestracji jednokrotnej w środowisku testowym. Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz usługi Azure AD w środowisku testowym, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="add-clarizen-from-the-gallery"></a>Dodaj Clarizen z galerii
Aby skonfigurować integrację usługi Azure AD Clarizen, należy dodać Clarizen z galerii do listy zarządzanych aplikacji SaaS.

1. W [portalu Azure](https://portal.azure.com), w okienku po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Ikona usługi Azure Active Directory][1]

2. Kliknij przycisk **aplikacje dla przedsiębiorstw**. Następnie kliknij przycisk **wszystkie aplikacje**.

    ![Kliknięcie przycisku "aplikacje przedsiębiorstwa" i "Wszystkie aplikacje"][2]

3. Kliknij przycisk **Dodaj** przycisk w górnej części okna dialogowego.

    ![Przycisk "Dodaj"][3]

4. W polu wyszukiwania wpisz **Clarizen**.

    ![Wpisz "Clarizen" w polu wyszukiwania](./media/clarizen-tutorial/tutorial_clarizen_000.png)

5. W okienku wyników wybierz **Clarizen**, a następnie kliknij przycisk **Dodaj** można dodać aplikację.

    ![Wybieranie Clarizen w okienku wyników](./media/clarizen-tutorial/tutorial_clarizen_0001.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej
W poniższych sekcjach skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Clarizen na podstawie użytkownika testu Simona Britta.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Clarizen jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Clarizen musi się. Ustanowić tę relację łącza, przypisując wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** w Clarizen.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Clarizen, wykonaj poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  umożliwienie użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  do testowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Clarizen](#create-a-clarizen-test-user)**  w celu zapewnienia odpowiednikiem Simona Britta Clarizen połączonego z jej reprezentacji usługi Azure AD.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  umożliwiające Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej
Włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Clarizen.

1. W portalu Azure na **Clarizen** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Kliknięcie przycisku "Logowanie jednokrotne"][4]

2. W **logowanie jednokrotne** okno dialogowe dla **tryb**, wybierz pozycję **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.

    ![Wybieranie "na podstawie SAML logowania"](./media/clarizen-tutorial/tutorial_clarizen_01.png)

3. W **Clarizen domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Pola Adres URL identyfikatora i odpowiedzi](./media/clarizen-tutorial/tutorial_clarizen_02.png)

    a. W **identyfikator** wpisz wartość, jak: **Clarizen**

    b. W **adres URL odpowiedzi** wpisz adres URL przy użyciu następującego wzorca: **https://<company name>.clarizen.com/Clarizen/Pages/Integrations/SAML/SamlResponse.aspx**

    > [!NOTE]
    > Nie są to rzeczywiste wartości. Należy użyć rzeczywisty identyfikator i adres URL odpowiedzi. W tym miejscu zaleca się, że używasz unikatowej wartości ciągu jako identyfikator. Aby uzyskać rzeczywiste wartości, skontaktuj się z [zespołem pomocy technicznej Clarizen](https://success.clarizen.com/hc/en-us/requests/new).

4. Na **certyfikat podpisywania SAML** kliknij **Utwórz nowy certyfikat**.

    ![Klikając przycisk "Utwórz nowy certyfikat"](./media/clarizen-tutorial/tutorial_clarizen_03.png)    

5. W **Tworzenie nowego świadectwa** okno dialogowe pola, kliknij ikonę kalendarza i wybierz datę wygaśnięcia. Następnie kliknij przycisk **Save** (Zapisz).

    ![Wybranie i zapisanie Data wygaśnięcia](./media/clarizen-tutorial/tutorial_general_300.png)

6. W **certyfikat podpisywania SAML** wybierz opcję **uaktywnić nowy certyfikat**, a następnie kliknij przycisk **zapisać**.

    ![Po zaznaczeniu pola wyboru dla uaktywnienie nowego certyfikatu](./media/clarizen-tutorial/tutorial_clarizen_04.png)

7. W **certyfikat przerzucania** okno dialogowe, kliknij przycisk **OK**.

    ![Klikając przycisk "OK", aby potwierdzić uaktywnić certyfikatu](./media/clarizen-tutorial/tutorial_general_400.png)

8. W **certyfikat podpisywania SAML** kliknij **certyfikatu (Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Klikając przycisk "Certyfikat (Base64)", aby rozpocząć pobieranie](./media/clarizen-tutorial/tutorial_clarizen_05.png)

9. W **konfiguracji Clarizen** , kliknij przycisk **skonfigurować Clarizen** otworzyć **Konfigurowanie logowania jednokrotnego** okna.

    ![Klikając przycisk "Konfigurowanie Clarizen"](./media/clarizen-tutorial/tutorial_clarizen_06.png)

    ![Okno "Konfigurowanie logowania jednokrotnego", w tym adresy URL i pliki](./media/clarizen-tutorial/tutorial_clarizen_07.png)

10. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy Clarizen.

11. Kliknij nazwy użytkownika, a następnie kliknij przycisk **ustawienia**.

    ![Klikając pozycję "Ustawienia" w obszarze nazwy użytkownika](./media/clarizen-tutorial/tutorial_clarizen_001.png "ustawienia")

12. Kliknij przycisk **ustawienia globalne** kartę. A następnie obok pozycji **Federated Authentication**, kliknij przycisk **Edytuj**.

    ![Kartę "Ustawienia globalne"](./media/clarizen-tutorial/tutorial_clarizen_002.png "ustawienia globalne")

13. W **Federated Authentication** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe "Uwierzytelnianie federacyjne"](./media/clarizen-tutorial/tutorial_clarizen_003.png "Federated Authentication")

    a. Wybierz **uwierzytelnianie federacyjne Włącz**.

    b. Kliknij przycisk **przekazać** przekazać pobranego certyfikatu.

    c. W **adres URL logowania** wprowadź wartość **SAML pojedynczy znak na adres URL usługi** w oknie Konfiguracja aplikacji usługi Azure AD.

    d. W **Sign-out URL** wprowadź wartość **Sign-Out URL** w oknie Konfiguracja aplikacji usługi Azure AD.

    e. Wybierz **Użyj POST**.

    f. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W portalu Azure Tworzenie użytkownika testowego o nazwie Simona Britta.

![Nazwa i adres e-mail użytkownika usługi Azure AD][100]

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Ikona usługi Azure Active Directory](./media/clarizen-tutorial/create_aaduser_01.png)

2. Kliknij przycisk **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy** do wyświetlenia na liście Użytkownicy.

    ![Klikając pozycję "Użytkownicy i grupy" i "Wszyscy użytkownicy"](./media/clarizen-tutorial/create_aaduser_02.png)

3. W górnej części okna dialogowego, kliknij przycisk **Dodaj** otworzyć **użytkownika** okno dialogowe.

    ![Przycisk "Dodaj"](./media/clarizen-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe "Użytkownika" z nazwę, adres e-mail i hasło wypełnione](./media/clarizen-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail konta Simona Britta.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.

### <a name="create-a-clarizen-test-user"></a>Tworzenie użytkownika testowego Clarizen

Celem tej sekcji jest utworzenie użytkownika o nazwie Simona Britta w Clarizen. Clarizen obsługę automatycznego użytkownika, który jest domyślnie włączone. Więcej informacji można znaleźć [tutaj](clarizen-provisioning-tutorial.md) na temat konfigurowania użytkowników automatycznego inicjowania obsługi administracyjnej.

**Jeśli trzeba ręcznie utworzyć użytkownika, wykonaj następujące kroki:**

Aby umożliwić użytkownikom usługi Azure AD do logowania się na Clarizen, należy udostępnić kont użytkowników. W przypadku Clarizen Inicjowanie obsługi to zadanie ręczne.

1. Zaloguj się do witryny firmy Clarizen jako administrator.

2. Kliknij przycisk **osób**.

    ![Klikając przycisk "Osoby"](./media/clarizen-tutorial/create_aaduser_001.png "osób")

3. Kliknij przycisk **zaprosić użytkowników**.

    ![Przycisk "Zaprosić użytkowników"](./media/clarizen-tutorial/create_aaduser_002.png "zaprosić użytkowników")

4. W **zaprosić użytkowników** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe "Zaproś inne osoby"](./media/clarizen-tutorial/create_aaduser_003.png "zaprosić użytkowników")

    a. W **E-mail** wpisz adres e-mail konta Simona Britta.

    b. Kliknij przycisk **zaprosić**.

    > [!NOTE]
    > Właścicielem konta usługi Azure Active Directory otrzymasz wiadomość e-mail, a następnie kliknij łącze, aby potwierdzić swoje konto, zanim staje się aktywny.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD
Włącz Simona Britta do udostępnienia jej Clarizen za pomocą usługi Azure rejestracji jednokrotnej.

![Test przypisany użytkownik][200]

1. Na platformie Azure kliknij przycisk Otwórz portalu, wyświetlać aplikacje, przejdź do widoku katalogu **aplikacje dla przedsiębiorstw**, a następnie kliknij przycisk **wszystkie aplikacje**.

    ![Kliknięcie przycisku "aplikacje przedsiębiorstwa" i "Wszystkie aplikacje"][201]

2. Na liście aplikacji zaznacz **Clarizen**.

    ![Wybieranie Clarizen na liście](./media/clarizen-tutorial/tutorial_clarizen_50.png)

3. W okienku po lewej stronie kliknij **użytkowników i grup**.

    ![Klikając pozycję "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj**. Następnie w **Dodaj przydziału** okno dialogowe, wybierz opcję **użytkowników i grup**.

    ![Przycisk "Dodaj" i "Dodaj przydziału" — okno dialogowe][203]

5. W **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. W **użytkowników i grup** okno dialogowe, kliknij przycisk **wybierz** przycisku.

7. W **Dodaj przydziału** okno dialogowe, kliknij przycisk **przypisać** przycisku.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego
Test konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Clarizen w panelu dostępu należy powinny być automatycznie zalogowany do aplikacji Clarizen.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Skonfiguruj Inicjowanie obsługi użytkowników](clarizen-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/clarizen-tutorial/tutorial_general_01.png
[2]: ./media/clarizen-tutorial/tutorial_general_02.png
[3]: ./media/clarizen-tutorial/tutorial_general_03.png
[4]: ./media/clarizen-tutorial/tutorial_general_04.png

[100]: ./media/clarizen-tutorial/tutorial_general_100.png

[200]: ./media/clarizen-tutorial/tutorial_general_200.png
[201]: ./media/clarizen-tutorial/tutorial_general_201.png
[202]: ./media/clarizen-tutorial/tutorial_general_202.png
[203]: ./media/clarizen-tutorial/tutorial_general_203.png