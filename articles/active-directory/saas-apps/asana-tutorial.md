---
title: 'Samouczek: Integracji Azure Active Directory z Asana | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Asana.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 837e38fe-8f55-475c-87f4-6394dc1fee2b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: jeedes
ms.openlocfilehash: d625d6df45f75df2932c737a99333be0247c626f
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35953242"
---
# <a name="tutorial-azure-active-directory-integration-with-asana"></a>Samouczek: Integracji Azure Active Directory z Asana

Z tego samouczka dowiesz się integrowanie Asana z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Asana zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Asana
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Asana (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Asana, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Asana jednokrotnego włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym.
Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Asana z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-asana-from-the-gallery"></a>Dodawanie Asana z galerii
Aby skonfigurować integrację usługi Azure AD Asana, należy dodać Asana z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Asana z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]

3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Asana**, wybierz pozycję **Asana** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/asana-tutorial/tutorial_asana_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Asana na podstawie użytkownika testowego, nazywany "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Asana jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Asana musi się.

W Asana, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Asana, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Asana](#create-an-asana-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Asana połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Asana.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Asana, wykonaj następujące czynności:**

1. W portalu Azure na **Asana** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.

    ![Okno dialogowe rejestracji jednokrotnej](./media/asana-tutorial/tutorial_asana_samlbase.png)

3. Na **Asana domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Adresy URL i domeny Asana pojedynczy informacje logowania jednokrotnego](./media/asana-tutorial/tutorial_asana_url.png)

    a. W **adres URL logowania** pole tekstowe, wprowadź adres URL: `https://app.asana.com/`

    b. W **identyfikator** pole tekstowe, wartość typu: `https://app.asana.com/`

4. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/asana-tutorial/tutorial_asana_certificate.png)

5. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/asana-tutorial/tutorial_general_400.png)

6. Na **konfiguracji Asana** , kliknij przycisk **skonfigurować Asana** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfiguracja Asana](./media/asana-tutorial/tutorial_asana_configure.png)

7. W oknie innej przeglądarki logowanie do aplikacji Asana. Aby skonfigurować logowanie Jednokrotne w Asana, uzyskać dostęp do ustawień obszaru roboczego, klikając nazwę obszaru roboczego w prawym górnym rogu ekranu. Następnie kliknij  **\<nazwa obszaru roboczego\> ustawienia**.

    ![Ustawienia logowania jednokrotnego Asana](./media/asana-tutorial/tutorial_asana_09.png)

8. Na **ustawienia organizacji** okna, kliknij przycisk **administracji**. Następnie kliknij przycisk **elementów członkowskich należy zalogować się za pośrednictwem SAML** umożliwiające konfigurację logowania jednokrotnego. Wykonaj następujące kroki:

    ![Skonfiguruj ustawienia organizacji rejestracji jednokrotnej](./media/asana-tutorial/tutorial_asana_10.png)  

     a. W **adres URL logowania strony** pole tekstowe, Wklej **SAML pojedynczy znak na adres URL usługi**.

     b. Kliknij prawym przyciskiem myszy certyfikat pobrany z portalu Azure, a następnie otwórz plik certyfikatu w Notatniku lub w edytorze tekstów preferowany. Kopiowanie zawartości między rozpoczęcia i zakończenia tytuł certyfikatu, a następnie wklej je **certyfikatu X.509** pola tekstowego.

9. Kliknij pozycję **Zapisz**. Przejdź do [Asana przewodnik konfigurowania rejestracji Jednokrotnej](https://asana.com/guide/help/premium/authentication#gl-saml) Jeœli potrzebujesz dodatkowej pomocy.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Przycisk usługi Azure Active Directory](./media/asana-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/asana-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/asana-tutorial/create_aaduser_03.png)

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:

    ![Przycisk Dodaj](./media/asana-tutorial/create_aaduser_04.png)

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.

### <a name="create-an-asana-test-user"></a>Tworzenie użytkownika testowego Asana

Celem tej sekcji jest utworzenie użytkownika o nazwie Simona Britta w Asana. Asana obsługę automatycznego użytkownika, który jest domyślnie włączone. Więcej informacji można znaleźć [tutaj](asana-provisioning-tutorial.md) na temat konfigurowania użytkowników automatycznego inicjowania obsługi administracyjnej.

**Jeśli trzeba ręcznie utworzyć użytkownika, wykonaj następujące kroki:**

W tej sekcji należy utworzyć użytkownika o nazwie Simona Britta w Asana.

1. Na **Asana**, przejdź do **zespołów** sekcji w lewym panelu. Kliknij przycisk ze znakiem plus.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/asana-tutorial/tutorial_asana_12.png)

2. Wpisz adres e-mail britta.simon@contoso.com w polu tekstowym, a następnie wybierz **zaprosić**.

3. Kliknij przycisk **wysłać zaproszenie**. Nowy użytkownik otrzyma wiadomość e-mail na swoje konto e-mail. Użytkownik należy utworzyć i zweryfikować konto.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Asana.

![Przypisanie roli użytkownika][200]

**Aby przypisać Simona Britta Asana, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji zaznacz **Asana**.

    ![Łącze Asana na liście aplikacji](./media/asana-tutorial/tutorial_asana_app.png)

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji służy do testowania programu Azure AD rejestracji jednokrotnej.

Przejdź do strony logowania Asana. W polu tekstowym Adres E-mail, Wstaw adres e-mail britta.simon@contoso.com. Pozostaw pole tekstowe hasła w puste, a następnie kliknij przycisk **dziennika w**. Nastąpi przekierowanie do strony logowania usługi Azure AD. Ukończ poświadczeń usługi Azure AD. Teraz użytkownik jest zalogowany na Asana.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Skonfiguruj Inicjowanie obsługi użytkowników](asana-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/asana-tutorial/tutorial_general_01.png
[2]: ./media/asana-tutorial/tutorial_general_02.png
[3]: ./media/asana-tutorial/tutorial_general_03.png
[4]: ./media/asana-tutorial/tutorial_general_04.png

[100]: ./media/asana-tutorial/tutorial_general_100.png

[200]: ./media/asana-tutorial/tutorial_general_200.png
[201]: ./media/asana-tutorial/tutorial_general_201.png
[202]: ./media/asana-tutorial/tutorial_general_202.png
[203]: ./media/asana-tutorial/tutorial_general_203.png
[10]: ./media/asana-tutorial/tutorial_general_060.png
[11]: ./media/asana-tutorial/tutorial_general_070.png