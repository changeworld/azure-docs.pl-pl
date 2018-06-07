---
title: 'Samouczek: Integracji Azure Active Directory z podniesienia uprawnień LinkedIn | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i podnieść LinkedIn.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 2ad9941b-c574-42c3-bd0f-5d6ec68537ef
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 5b940ac896f95ec796179f44e0dd11dcf6184464
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34589393"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-elevate"></a>Samouczek: Integracji Azure Active Directory z LinkedIn podniesienia uprawnień

Z tego samouczka dowiesz się integrowanie LinkedIn podniesienia uprawnień w usłudze Azure Active Directory (Azure AD).

Integrowanie LinkedIn podniesienia uprawnień w usłudze Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do LinkedIn podniesienia uprawnień
- Umożliwia użytkownikom automatycznie pobrać zalogowane LinkedIn podniesienie poziomu (logowanie jednokrotne) z konta usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu zarządzania Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z LinkedIn podniesienia uprawnień, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Podniesienie poziomu LinkedIn jednokrotnego włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym.
Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie podniesienia uprawnień LinkedIn z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-linkedin-elevate-from-the-gallery"></a>Dodawanie podniesienia uprawnień LinkedIn z galerii
Aby skonfigurować integrację LinkedIn podniesienia uprawnień w usłudze Azure Active Directory, należy dodać podniesienia uprawnień LinkedIn z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać podniesienia uprawnień LinkedIn z galerii, wykonaj następujące czynności:**

1. W  **[portalu zarządzania Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]

3. Kliknij przycisk **Dodaj** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **LinkedIn podniesienia uprawnień**. Z poziomu panelu wyników, kliknij przycisk **LinkedIn podniesienia uprawnień** można dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-linkedinElevate-tutorial/tutorial-linkedinElevate_000.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z LinkedIn podnoszenia uprawnień w oparciu o użytkownika testowego o nazwie "Britta Simona".

Do rejestracji jednokrotnej do pracy usługi Azure AD musi ustalić użytkownika odpowiednika w LinkedIn podniesienia uprawnień do użytkownika w usłudze Azure AD. Innymi słowy musi można ustanowić łącze relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w LinkedIn podniesienia uprawnień.

Ta relacja łącza zostanie nawiązane, przypisując wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** w LinkedIn podniesienia uprawnień.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z LinkedIn podniesienia uprawnień, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego podniesienia uprawnień LinkedIn](#creating-a-linkedin-elevate-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu zarządzania Azure i skonfigurować logowanie jednokrotne w aplikacji LinkedIn podniesienia uprawnień.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z LinkedIn podniesienia uprawnień, wykonaj następujące czynności:**

1. W portalu zarządzania Azure na **podniesienia uprawnień LinkedIn** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okna dialogowego, jako **tryb** wybierz **na języku SAML logowania jednokrotnego** Włącz funkcji logowania jednokrotnego.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-linkedinElevate-tutorial/tutorial-linkedin_01.png)

3. W oknie przeglądarki innej witryny sieci web logowanie do podniesienia uprawnień LinkedIn dzierżawy z uprawnieniami administratora.

4. W **Centrum konta**, kliknij przycisk **ustawienia globalne** w obszarze **ustawienia**. Zaznacz również **podniesienie uprawnień — podniesienie poziomu testu AAD** z listy rozwijanej.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_admin_01.png)

5. Polecenie **lub kliknij tutaj, aby załadować i skopiuj poszczególnych pól w formularzu** i skopiuj **identyfikator jednostki** i **adresu Url potwierdzenia konsumenta dostępu (ACS)**

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_admin_03.png)

6. W portalu Azure w obszarze **LinkedIn podniesienie poziomu domeny i adres URL**, wykonaj następujące kroki, aby skonfigurować logowanie Jednokrotne w **inicjowane IdP** tryb

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_signon_01.png)

    a. W **identyfikator** pole tekstowe, wprowadź **identyfikator jednostki** skopiowany z portalu LinkedIn 

    b. W **adres URL odpowiedzi** pole tekstowe, wprowadź **potwierdzenia konsumenta dostępu (ACS) adres Url** skopiowany z portalu LinkedIn

7. Jeśli chcesz skonfigurować logowanie Jednokrotne w **inicjowane SP**, kliknij opcję Ustawienia Pokaż zaawansowane adres URL w sekcji konfiguracji oraz konfigurowania logowania na adres URL jest następujący wzór:

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=elevate&applicationInstanceId=<InstanceId>` 

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_signon_02.png) 

8. Podniesienie poziomu LinkedIn aplikacji oczekuje potwierdzenia języka SAML w określonym formacie, musisz dodać mapowania atrybutu niestandardowego do konfiguracji atrybuty tokenu SAML. Poniższy zrzut ekranu przedstawia przykład tego. Wartość domyślna **identyfikator użytkownika** jest **user.userprincipalname** , ale oczekuje LinkedIn podniesienia uprawnień, to można mapować z adresu e-mail użytkownika. W przypadku którego można użyć **user.mail** atrybutu z listy lub użyj wartości atrybutu odpowiednie na podstawie konfiguracji organizacji.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-linkedinElevate-tutorial/updateusermail.png)

9. W **atrybuty użytkownika** kliknij **widoku i edytować wszystkie atrybuty użytkowników** i ustawić atrybutów. Należy dodać inny oświadczeń o nazwie **działu** i wartość ta musi być zamapowany na **user.department**.

    | Nazwa atrybutu | Wartość atrybutu |
    | --- | --- |
    | dział| User.Department |

      ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-linkedinElevate-tutorial/userattribute.png)

      a. Atrybutu Dodaj, aby otworzyć stronę szczegółów atrybutu, kliknij polecenie Dodaj atrybut działu, jak pokazano poniżej-

      ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-linkedinElevate-tutorial/adduserattribute.png)

      b. Polecenie **Ok** można zapisać atrybutu.

      c. Zmień nazwę atrybutu **emailaddress** do **e-mail**.

10. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik XML na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-linkedinElevate-tutorial/tutorial-linkedinElevate_certificate.png) 

11. Kliknij pozycję **Zapisz**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_400.png)

12. Przejdź do **ustawienia administratora LinkedIn** sekcji. Przekaż plik XML, który pobrany z portalu Azure, klikając opcję pliku XML przekazać.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_metadata_03.png)

13. Kliknij przycisk **na** do włączenia funkcji logowania jednokrotnego. Stan rejestracji Jednokrotnej ulegnie zmianie z **niepołączone** do **połączony**

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_admin_05.png)

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu zarządzania Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu zarządzania Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-linkedinElevate-tutorial/create_aaduser_01.png) 

2. Przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy** do wyświetlenia na liście Użytkownicy.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-linkedinElevate-tutorial/create_aaduser_02.png) 

3. W górnej części okna dialogowego kliknij **Dodaj** otworzyć **użytkownika** okna dialogowego.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-linkedinElevate-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-linkedinElevate-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.

### <a name="creating-a-linkedin-elevate-test-user"></a>Tworzenie użytkownika testowego LinkedIn podniesienia uprawnień

LinkedIn podniesienia uprawnień aplikacji obsługuje tylko w czasie Inicjowanie obsługi użytkowników i uwierzytelnianie użytkowników zostaną utworzone w aplikacji automatycznie. Na administrator ustawienia strony na Przerzucanie portalu LinkedIn podniesienia uprawnień przełącznika **automatycznie przypisywać licencje** na active bezpośrednio w czasie inicjowania obsługi administracyjnej i to będzie również przypisać licencję do użytkownika. Podniesienie poziomu LinkedIn obsługuje również Inicjowanie obsługi użytkowników, można znaleźć więcej szczegółów [tutaj](active-directory-saas-linkedinelevate-provisioning-tutorial.md) na temat konfigurowania użytkowników automatycznego inicjowania obsługi administracyjnej.

   ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-linkedinElevate-tutorial/LinkedinUserprovswitch.png)

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udostępnienie jej do podniesienia uprawnień LinkedIn.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta LinkedIn podniesienia uprawnień, wykonaj następujące czynności:**

1. Otwórz widok aplikacji w portalu zarządzania Azure, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji zaznacz **LinkedIn podniesienia uprawnień**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-linkedinElevate-tutorial/tutorial-linkedinElevate_0001.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.

### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka LinkedIn podniesienia uprawnień w panelu dostępu, należy pobrać strony Azure logowania i na po pomyślnym logowania, należy pobrać w aplikacji, LinkedIn podniesienia uprawnień.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Samouczek: Konfigurowanie LinkedIn podniesienia uprawnień dla użytkownika automatycznego inicjowania obsługi administracyjnej w usłudze Azure Active Directory](active-directory-saas-linkedinelevate-provisioning-tutorial.md)
* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)
* [Skonfiguruj Inicjowanie obsługi użytkowników](active-directory-saas-linkedinelevate-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_203.png