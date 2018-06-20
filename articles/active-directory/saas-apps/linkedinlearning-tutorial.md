---
title: 'Samouczek: Integracji Azure Active Directory z LinkedIn Learning | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i uczenie się LinkedIn.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d5857070-bf79-4bd3-9a2a-4c1919a74946
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: ffa689e9556e57560138d9629c616bd3a284f9b6
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36222311"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-learning"></a>Samouczek: Integracji Azure Active Directory z LinkedIn Learning

Z tego samouczka dowiesz się integrowanie LinkedIn Learning z usługą Azure Active Directory (Azure AD).

Integrowanie LinkedIn Learning z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do uczenia LinkedIn
- Umożliwia użytkownikom automatycznie pobrać zalogowane do uczenia LinkedIn (logowanie jednokrotne) z konta usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu LinkedIn Learning, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- LinkedIn Learning jednokrotnego włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie LinkedIn Learning z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-linkedin-learning-from-the-gallery"></a>Dodawanie LinkedIn Learning z galerii
Aby skonfigurować integrację LinkedIn Learning z usługą Azure AD, należy dodać LinkedIn Learning z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać LinkedIn Learning z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Kliknij przycisk **Dodaj** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **LinkedIn Learning**. Z poziomu panelu wyników, kliknij przycisk **LinkedIn Learning** można dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/linkedinlearning-tutorial/tutorial-linkedinlearning_000.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurować i przetestować usługi Azure AD logowania jednokrotnego przy użyciu Learning LinkedIn w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w uczeniu LinkedIn jest dla użytkownika, w usłudze Azure AD. Innymi słowy musi można ustanowić łącze relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w uczeniu LinkedIn.

Ta relacja łącza zostanie nawiązane, przypisując wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** w uczeniu LinkedIn.

Aby skonfigurować i przetestować usługi Azure AD logowania jednokrotnego przy użyciu LinkedIn Learning, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego LinkedIn Learning](#creating-a-linkedin-learning-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji LinkedIn Learning.

**Aby skonfigurować usługi Azure AD logowania jednokrotnego przy użyciu LinkedIn Learning, wykonaj następujące czynności:**

1. W portalu Azure na **LinkedIn Learning** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/linkedinlearning-tutorial/tutorial-linkedin_01.png)

3. W oknie przeglądarki innej witryny sieci web logowanie do dzierżawy LinkedIn Learning jako administrator.

4. W **Centrum konta**, kliknij przycisk **ustawienia globalne** w obszarze **ustawienia**. Zaznacz również **Learning - domyślne** z listy rozwijanej.

    ![Konfigurowanie rejestracji jednokrotnej](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_01.png)

5. Kliknij przycisk **lub kliknij tutaj, aby załadować i skopiuj poszczególnych pól w formularzu** i skopiuj **identyfikator jednostki** i **adresu Url potwierdzenia konsumenta dostępu (ACS)**

    ![Konfigurowanie rejestracji jednokrotnej](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_03.png)

6. W portalu Azure w obszarze **domeny Learning LinkedIn i adres URL**, wykonaj następujące kroki, aby skonfigurować logowanie Jednokrotne w **inicjowane IdP** tryb

    ![Konfigurowanie rejestracji jednokrotnej](./media/linkedinlearning-tutorial/tutorial_linkedin_signon_01.png)

    a. W **identyfikator** pole tekstowe, wprowadź **identyfikator jednostki** skopiowany z portalu LinkedIn 

    b. W **adres URL odpowiedzi** pole tekstowe, wprowadź **potwierdzenia konsumenta dostępu (ACS) adres Url** skopiowany z portalu LinkedIn

7. Jeśli chcesz skonfigurować logowanie Jednokrotne w **inicjowane SP**, następnie kliknij opcję Ustawienia Pokaż zaawansowane adres URL w sekcji konfiguracji i skonfigurować adres URL logowania przy użyciu następującego wzorca:

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=learning&applicationInstanceId=<InstanceId>`

    ![Konfigurowanie rejestracji jednokrotnej](./media/linkedinlearning-tutorial/tutorial_linkedin_signon_02.png)   
    
8. Aplikacja LinkedIn Learning oczekuje potwierdzenia języka SAML w określonym formacie, musisz dodać mapowania atrybutu niestandardowego do konfiguracji atrybuty tokenu SAML. Poniższy zrzut ekranu przedstawia przykład tego. Wartość domyślna **identyfikator użytkownika** jest **user.userprincipalname** , ale LinkedIn Learning oczekuje to być mapowane z adresu e-mail użytkownika. W przypadku którego można użyć **user.mail** atrybutu z listy lub użyj wartości atrybutu odpowiednie na podstawie konfiguracji organizacji. 

    ![Konfigurowanie rejestracji jednokrotnej](./media/linkedinlearning-tutorial/updateusermail.png)
    
9. W **atrybuty użytkownika** kliknij **widoku i edytować wszystkie atrybuty użytkowników** i ustawić atrybutów. Użytkownik chce dodać cztery oświadczeń o nazwie **e-mail**, **działu**, **imię**, i **nazwisko** i wartość ma być zmapowana z **user.mail**, **user.department**, **user.givenname**, i **user.surname** odpowiednio

    | Nazwa atrybutu | Wartość atrybutu |
    | --- | --- |
    | wyślij wiadomość e-mail| User.mail |    
    | dział| User.Department |
    | Imię| user.givenname |
    | nazwisko| user.surname |
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/linkedinlearning-tutorial/userattribute.png)
    
    a. Kliknij przycisk **Dodawanie atrybutu** aby otworzyć okno dialogowe atrybutu.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/linkedinlearning-tutorial/tutorial_attribute_04.png)

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/linkedinlearning-tutorial/tutorial_attribute_05.png)
    
    b. W **nazwa** tekstowym, wpisz nazwę atrybut wyświetlany dla danego wiersza.
    
    c. Z **wartość** listy, wpisz wartość atrybutu wyświetlany dla danego wiersza.
    
    d. Kliknij przycisk **Ok**

10. Wykonaj następujące czynności na **nazwa** — atrybut

    a. Kliknij ten atrybut można otworzyć **atrybutu Edytuj** okna.

    ![Konfigurowanie rejestracji jednokrotnej](./media/linkedinlearning-tutorial/url_update.png)

    b. Usuń wartość adresu URL z **przestrzeni nazw**.
    
    c. Kliknij przycisk **Ok** Aby zapisać ustawienia.

11. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik XML na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/linkedinlearning-tutorial/tutorial-linkedinlearning_certificate.png)

12. Kliknij pozycję **Zapisz**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/linkedinlearning-tutorial/tutorial_general_400.png)

13. Przejdź do **ustawienia administratora LinkedIn** sekcji. Przekaż plik XML, który został pobrany z portalu Azure, klikając opcję pliku XML, Przekaż.

    ![Konfigurowanie rejestracji jednokrotnej](./media/linkedinlearning-tutorial/tutorial_linkedin_metadata_03.png)

14. Kliknij przycisk **na** do włączenia funkcji logowania jednokrotnego. Zmiany stanu rejestracji Jednokrotnej z **niepołączone** do **połączony**

    ![Konfigurowanie rejestracji jednokrotnej](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_05.png)

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/linkedinlearning-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/linkedinlearning-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/linkedinlearning-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/linkedinlearning-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.

### <a name="creating-a-linkedin-learning-test-user"></a>Tworzenie użytkownika testowego LinkedIn Learning

LinkedIn Learning aplikacji obsługuje tylko w czasie Inicjowanie obsługi użytkowników i uwierzytelnianie użytkowników są tworzone automatycznie w aplikacji. Na administrator ustawienia strony na Przerzucanie portalu LinkedIn Learning przełącznika **automatycznie przypisywać licencje** na active bezpośrednio w czasie inicjowania obsługi administracyjnej i to będzie również przypisać licencję do użytkownika. LinkedIn Learning również obsługę użytkowników, można znaleźć więcej szczegółów [tutaj](linkedinlearning-provisioning-tutorial.md) na temat konfigurowania użytkowników automatycznego inicjowania obsługi administracyjnej.

   ![Tworzenie użytkownika testowego usługi Azure AD](./media/linkedinlearning-tutorial/LinkedinUserprovswitch.png)

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do uczenia LinkedIn.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta LinkedIn Learning, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji zaznacz **LinkedIn Learning**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/linkedinlearning-tutorial/tutorial-linkedinlearning_0001.png)

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.

### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka LinkedIn Learning w panelu dostępu, należy pobrać strony Azure logowania jednokrotnego i na po pomyślnym logowania, należy pobrać do aplikacji LinkedIn Learning.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Skonfiguruj Inicjowanie obsługi użytkowników](linkedinlearning-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/linkedinlearning-tutorial/tutorial_general_01.png
[2]: ./media/linkedinlearning-tutorial/tutorial_general_02.png
[3]: ./media/linkedinlearning-tutorial/tutorial_general_03.png
[4]: ./media/linkedinlearning-tutorial/tutorial_general_04.png

[100]: ./media/linkedinlearning-tutorial/tutorial_general_100.png

[200]: ./media/linkedinlearning-tutorial/tutorial_general_200.png
[201]: ./media/linkedinlearning-tutorial/tutorial_general_201.png
[202]: ./media/linkedinlearning-tutorial/tutorial_general_202.png
[203]: ./media/linkedinlearning-tutorial/tutorial_general_203.png