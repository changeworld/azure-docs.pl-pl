---
title: 'Samouczek: Integracji Azure Active Directory z miejsca pracy przez Facebook | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i miejsca pracy przez usługi Facebook.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: jeedes
ms.openlocfilehash: 687320af3130a8fd3dbd6e680216c5f6f7f1c73f
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35941035"
---
# <a name="tutorial-azure-active-directory-integration-with-workplace-by-facebook"></a>Samouczek: Integracji Azure Active Directory z miejsca pracy przez usługi Facebook

Z tego samouczka dowiesz się sposobu integracji z usługą Azure Active Directory (Azure AD) miejsca pracy przez usługi Facebook.

Integrowanie miejsca pracy przez Facebook z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do miejsca pracy przez usługi Facebook
- Umożliwia użytkownikom automatycznie pobrać zalogowane do miejsca pracy przez Facebook (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z miejsca pracy przez usługi Facebook, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Dołączanie w serwisie Facebook logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

> [!NOTE]
> Facebook ma dwa produkty standardowe miejsca pracy (bezpłatnie) i Premium pracy (płatną). Dowolnej dzierżawy pracy Premium można skonfigurować nie innych skutków koszt lub licencjami wymaganymi integracji SCIM i logowania jednokrotnego. Logowania jednokrotnego i SCIM nie są dostępne w przypadkach standardowe miejsca pracy.

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie miejsca pracy przez Facebook z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>Dodawanie miejsca pracy przez Facebook z galerii
Aby skonfigurować integrację usługi Azure AD miejsca pracy przez usługi Facebook, należy dodać miejsca pracy przez Facebook z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać miejsce pracy przez Facebook z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **miejsca pracy przez Facebook**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_search.png)

5. W panelu wyników wybierz **miejsca pracy przez Facebook**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z miejsca pracy przez usługi Facebook na podstawie użytkownika testowego, nazywany "Britta Simona".

Do rejestracji jednokrotnej do pracy usługi Azure AD musi ustalić użytkownika odpowiednika w miejscu pracy przez Facebook dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i danemu użytkownikowi w miejscu pracy przez Facebook musi określone.

Ta relacja łącza zostanie nawiązane, przypisując wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** w miejscu pracy przez usługi Facebook.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z miejsca pracy przez usługi Facebook, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie częstotliwości ponowne uwierzytelnianie](#configuring-reauthentication-frequency)**  — Aby skonfigurować miejsca pracy z monitem o wyboru SAML.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
4. **[Tworzenie pracy przez użytkownika testowego Facebook](#creating-a-workplace-by-facebook-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta miejsca pracy przez Facebook połączonego z usługi Azure AD reprezentację użytkownika.
5. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
6. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji możesz włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w miejscu pracy przez aplikację usługi Facebook.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z miejsca pracy przez usługi Facebook, wykonaj następujące czynności:**

1. W portalu Azure na **miejsca pracy przez Facebook** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_samlbase.png)

3. Na **miejsca pracy przez domenę usługi Facebook i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<instancename>.facebook.com`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://www.facebook.com/company/<instanceID>`

    > [!NOTE] 
    > Wartości te nie są rzeczywistych. Rzeczywisty adres URL logowania i identyfikator, należy zaktualizować te wartości. Odwiedź stronę uwierzytelniania pulpitu nawigacyjnego firmy miejsca pracy dla poprawne wartości dla społeczności Twojego miejsca pracy. 

4. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/workplacebyfacebook-tutorial/tutorial_general_400.png)

6. Na **miejsca pracy przez konfigurację usługi Facebook** , kliknij przycisk **skonfigurować miejsca pracy przez Facebook** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/workplacebyfacebook-tutorial/config.png) 

7. W oknie przeglądarki innej witryny sieci web, zaloguj się do miejsca pracy przez Facebook witryny firmy jako administrator.
  
   > [!NOTE] 
   > W ramach procesu uwierzytelniania SAML miejsce pracy mogą używać ciągów zapytań do 2,5 kilobajtów rozmiaru w celu przekazania parametrów do usługi Azure AD.

8. W **pulpitu nawigacyjnego firmy**, przejdź do **uwierzytelniania** kartę.

9. W obszarze **uwierzytelnianie SAML**, wybierz pozycję **logowania jednokrotnego tylko** z listy rozwijanej.

10. Wprowadzanie wartości skopiowanych z **miejsca pracy przez konfigurację usługi Facebook** części portalu Azure do odpowiednich pól:

    *   W **adres URL SAML** pole tekstowe, Wklej wartość **pojedynczy znak na adres URL usługi**, które zostały skopiowane z portalu Azure.
    *   W **pole tekstowe adres URL wystawcy SAML**, Wklej wartość **identyfikator jednostki SAML**, które zostały skopiowane z portalu Azure.
    *   W **przekierowania wylogowania SAML** (opcjonalnie), Wklej wartość **Sign-Out URL**, które zostały skopiowane z portalu Azure.
    *   Otwórz z **certyfikatu algorytmem base-64** w Notatniku pobrany z portalu Azure, należy skopiować zawartość go do Schowka, a następnie wklej go do **certyfikatu SAML** pola tekstowego.

11. Musisz wprowadzić adres URL odbiorców, adres URL odbiorcy, i adres URL ACS (usługa konsumenta potwierdzenia) w folderze **Konfiguracja SAML** sekcji.

12. Przewiń w dół do sekcji, a następnie kliknij przycisk **Test rejestracji Jednokrotnej** przycisku. Ten wyniki w oknie podręcznym znajdujących się ze strony logowania usługi Azure AD przedstawione. Wprowadź swoje poświadczenia w normalnie w celu uwierzytelnienia. 

    **Rozwiązywanie problemów:** zapewnienia zwracanych wstecz z usługi Azure AD jest taka sama jak konto firmowe, użytkownik jest zalogowany przy użyciu adresu e-mail.

13. Po zakończeniu pomyślnie testu, przewiń w dół strony i kliknij przycisk **zapisać** przycisku.

14. Wszyscy użytkownicy korzystający z miejsca pracy zostanie teraz wyświetlone strony logowania usługi Azure AD do uwierzytelniania.

15. **SAML wylogowania przekierowania (opcjonalnie)** - 

    Można opcjonalnie skonfigurować SAML Url wylogowania, którego można użyć, aby wskazywały na strony wylogowania usługi Azure AD. Gdy to ustawienie jest włączone i skonfigurowane, użytkownik nie jest już będzie kierowany do strony wylogowania w miejscu pracy. Zamiast tego użytkownik zostanie przekierowany do adresu url, który został dodany w ustawieniu przekierowania wylogowania SAML.

### <a name="configuring-reauthentication-frequency"></a>Konfigurowanie częstotliwości ponowne uwierzytelnianie

Można skonfigurować miejsca pracy, aby monitować wyboru SAML co dzień, trzech dni, tygodni, dwóch tygodni, miesięcy lub nigdy.

> [!NOTE] 
>Minimalna wartość wyboru SAML na aplikacje mobilne wynosi tydzień.

Możesz też wymusić SAML zresetować dla wszystkich użytkowników za pomocą przycisku: Wymagaj SAML uwierzytelnianie dla wszystkich użytkowników.


### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/workplacebyfacebook-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/workplacebyfacebook-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/workplacebyfacebook-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/workplacebyfacebook-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-workplace-by-facebook-test-user"></a>Tworzenie pracy przez użytkownika testowego usługi Facebook

W tej sekcji o nazwie Simona Britta tworzenia użytkownika w miejscu pracy przez usługi Facebook. Obszar roboczy w serwisie Facebook obsługę w czasie, który jest domyślnie włączona.

Brak akcji można w tej sekcji. Jeśli użytkownik nie istnieje w miejscu pracy przez usługi Facebook, nowy jest tworzona przy próbie uzyskania dostępu do miejsca pracy przez usługi Facebook.

>[!Note]
>Jeśli trzeba ręcznie utworzyć użytkownika, skontaktuj się z [miejsca pracy przez zespół obsługi klienta usługi Facebook](https://workplace.fb.com/faq/)

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do miejsca pracy przez usługi Facebook.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta do miejsca pracy przez usługi Facebook, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **miejsca pracy przez Facebook**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

Jeśli chcesz przetestować jednego ustawienia logowania jednokrotnego, otwórz Panel dostępu.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Skonfiguruj Inicjowanie obsługi użytkowników](workplacebyfacebook-provisioning-tutorial.md)



<!--Image references-->

[1]: ./media/workplacebyfacebook-tutorial/tutorial_general_01.png
[2]: ./media/workplacebyfacebook-tutorial/tutorial_general_02.png
[3]: ./media/workplacebyfacebook-tutorial/tutorial_general_03.png
[4]: ./media/workplacebyfacebook-tutorial/tutorial_general_04.png

[100]: ./media/workplacebyfacebook-tutorial/tutorial_general_100.png

[200]: ./media/workplacebyfacebook-tutorial/tutorial_general_200.png
[201]: ./media/workplacebyfacebook-tutorial/tutorial_general_201.png
[202]: ./media/workplacebyfacebook-tutorial/tutorial_general_202.png
[203]: ./media/workplacebyfacebook-tutorial/tutorial_general_203.png
