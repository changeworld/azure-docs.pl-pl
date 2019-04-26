---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą usługi LinkedIn podnieść poziom | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i podnieść LinkedIn.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 2ad9941b-c574-42c3-bd0f-5d6ec68537ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ca8e537f261b59fb4e069d47d24e21abbdeca46
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60260025"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-elevate"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą usługi LinkedIn podnieść poziom

W tym samouczku dowiesz się, jak zintegrować podniesienie poziomu usługi LinkedIn z usługą Azure Active Directory (Azure AD).

Integrowanie podniesienie poziomu usługi LinkedIn z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do wyniesienia rozgrywek LinkedIn
- Użytkowników, aby automatycznie uzyskać zalogowanych do wyniesienia rozgrywek LinkedIn (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — portalu zarządzania platformy Azure

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą usługi LinkedIn podnieść poziom, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Podniesienie poziomu LinkedIn logowania jednokrotnego włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym.
Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie usługi LinkedIn podniesienie poziomu z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-linkedin-elevate-from-the-gallery"></a>Dodawanie usługi LinkedIn podniesienie poziomu z galerii
Aby skonfigurować integrację z podniesienie poziomu usługi LinkedIn w usłudze Azure AD, należy dodać podniesienie poziomu usługi LinkedIn z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać podniesienie poziomu usługi LinkedIn z galerii, wykonaj następujące czynności:**

1. W  **[portalu zarządzania systemu Azure](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]

1. Kliknij przycisk **Dodaj** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **podnieść LinkedIn**. Panel wyników kliknij **podnieść LinkedIn** umożliwiające dodanie aplikacji.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/linkedinelevate-tutorial/tutorial-linkedinElevate_000.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą usługi LinkedIn podnieść oparte na użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w LinkedIn podnieś uprawnienia dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w LinkedIn podniesienie poziomu musi można ustanowić.

Ustanowieniu tej relacji łączy, przypisując wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** w podniesienie poziomu usługi LinkedIn.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą usługi LinkedIn podniesienie poziomu, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego podnieść LinkedIn](#creating-a-linkedin-elevate-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji Włączanie usługi Azure AD logowanie jednokrotne w portalu zarządzania platformy Azure i konfigurowanie logowania jednokrotnego w aplikacji podniesienie poziomu usługi LinkedIn.

**Aby skonfigurować usługi Azure AD logowanie jednokrotne za pomocą usługi LinkedIn podniesienie poziomu, wykonaj następujące czynności:**

1. W portalu zarządzania platformy Azure na **podnieść LinkedIn** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, jako **tryb** wybierz **opartej na SAML logowania jednokrotnego** na włączanie logowania jednokrotnego.

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinelevate-tutorial/tutorial-linkedin_01.png)

1. W oknie przeglądarki internetowej innej logowanie jednokrotne do dzierżawy usługi LinkedIn podnieść poziom konta administratora.

1. W **Centrum kont** kliknij przycisk **Ustawienia globalne** w obszarze **Ustawienia**. Zaznacz również **podniesienie uprawnień — podniesienie poziomu testu AAD** z listy rozwijanej.

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_01.png)

1. Kliknij pozycję **lub kliknij tutaj, aby załadować i skopiuj poszczególne pola w formularzu** i skopiuj **identyfikator jednostki** i **adresu Url asercji klienta dostępu (ACS)**

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_03.png)

1. W witrynie Azure Portal w obszarze **LinkedIn podnieść domena i adresy URL**, wykonaj następujące kroki, aby skonfigurować logowanie Jednokrotne w **inicjowane przez dostawcę tożsamości** tryb

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinelevate-tutorial/tutorial_linkedin_signon_01.png)

    a. W **identyfikator** polu tekstowym wprowadź **identyfikator jednostki** skopiowane z portalu usługi LinkedIn 

    b. W **adres URL odpowiedzi** polu tekstowym wprowadź **adresu Url asercji klienta dostępu (ACS)** skopiowane z portalu usługi LinkedIn

1. Jeśli chcesz skonfigurować logowanie Jednokrotne w **zainicjowane SP**, a następnie kliknij opcję Ustawienia Pokaż zaawansowane adresu URL w sekcji konfiguracji i konfigurowanie logowania na adres URL przy użyciu następującego wzorca:

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=elevate&applicationInstanceId=<InstanceId>` 

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinelevate-tutorial/tutorial_linkedin_signon_02.png) 

1. Podniesienie poziomu LinkedIn aplikacji oczekuje twierdzenia SAML w określonym formacie, który wymaga dodania mapowania atrybutów niestandardowych konfiguracji atrybuty tokenu języka SAML. Poniższy zrzut ekranu przedstawia przykład tego działania. Wartość domyślna **identyfikator użytkownika** jest **user.userprincipalname** a w przypadku podniesienia poziomu LinkedIn to mają być mapowane z adresem e-mail użytkownika. W tym celu możesz użyć atrybutu **user.mail** z listy lub odpowiedniej wartości atrybutu zgodnie z konfiguracją w organizacji.

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinelevate-tutorial/updateusermail.png)

1. W **atrybutów użytkownika** kliknij **Wyświetl i Edytuj wszystkie inne atrybuty użytkownika** i ustawić atrybuty. Należy dodać inny oświadczeń o nazwie **działu** i wartość musi być mapowane do **user.department**.

    | Nazwa atrybutu | Wartość atrybutu |
    | --- | --- |
    | department| user.department |

      ![Tworzenie użytkownika testowego usługi Azure AD](./media/linkedinelevate-tutorial/userattribute.png)

      a. Kliknij pozycję Dodaj atrybut, aby otworzyć stronę szczegółów atrybut Dodaj atrybut działu, jak pokazano poniżej-

      ![Tworzenie użytkownika testowego usługi Azure AD](./media/linkedinelevate-tutorial/adduserattribute.png)

      b. Kliknij pozycję **Ok** można zapisać atrybutu.

      c. Zmień nazwę atrybutu, **emailaddress** do **e-mail**.

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik XML na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinelevate-tutorial/tutorial-linkedinElevate_certificate.png) 

1. Kliknij pozycję **Zapisz**.

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinelevate-tutorial/tutorial_general_400.png)

1. Przejdź do sekcji **LinkedIn Admin Settings** (Ustawienia administratora LinkedIn). Przekaż plik XML, który tylko pobrane z witryny Azure portal, klikając opcję XML Przekaż plik.

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinelevate-tutorial/tutorial_linkedin_metadata_03.png)

1. Kliknij przycisk **On** (Włącz), aby włączyć funkcję logowania jednokrotnego. Usługa rejestracji Jednokrotnej stan zmieni się z **niepołączony** do **połączono**

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_05.png)

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu zarządzania platformy Azure o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu zarządzania Azure**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/linkedinelevate-tutorial/create_aaduser_01.png) 

1. Przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy** do wyświetlania listy użytkowników.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/linkedinelevate-tutorial/create_aaduser_02.png) 

1. W górnej części okna dialogowego kliknij **Dodaj** otworzyć **użytkownika** okna dialogowego.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/linkedinelevate-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/linkedinelevate-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.

### <a name="creating-a-linkedin-elevate-test-user"></a>Tworzenie użytkownika testowego podniesienie poziomu usługi LinkedIn

LinkedIn podniesienie poziomu aplikacji obsługuje tylko w czasie Inicjowanie obsługi użytkowników oraz uwierzytelniania użytkowników w aplikacji, automatycznie tworzony. Na administratora ustawienia strony na podniesienie poziomu LinkedIn Przerzucanie portalu przełącznika **automatycznie przypisywać licencje** do aktywnego tylko w czasie inicjowania obsługi administracyjnej i to będzie również przypisać licencję do użytkownika. Podniesienie poziomu LinkedIn obsługuje również automatyczna aprowizacja użytkowników, więcej szczegółów można znaleźć [tutaj](linkedinelevate-provisioning-tutorial.md) dotyczące sposobu konfigurowania automatycznej aprowizacji użytkowników.

   ![Tworzenie użytkownika testowego usługi Azure AD](./media/linkedinelevate-tutorial/LinkedinUserprovswitch.png)

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do użycia platformy Azure logowania jednokrotnego przez udostępnienie jej do wyniesienia rozgrywek LinkedIn.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon do wyniesienia rozgrywek LinkedIn, wykonaj następujące czynności:**

1. W portalu zarządzania platformy Azure powoduje ono otwarcie widoku aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

1. Na liście aplikacji wybierz **podnieść LinkedIn**.

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinelevate-tutorial/tutorial-linkedinElevate_0001.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.

### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka podniesienie poziomu usługi LinkedIn w panelu dostępu, należy pobrać na stronie logowania platformy Azure i na po pomyślnym zalogowaniu, należy uzyskać w aplikacji podniesienie poziomu usługi LinkedIn.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Samouczek: Konfigurowanie podniesienia poziomu usługi LinkedIn na potrzeby automatycznej aprowizacji użytkowników z usługą Azure Active Directory](linkedinelevate-provisioning-tutorial.md)
* [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie aprowizacji użytkowników](linkedinelevate-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/linkedinElevate-tutorial/tutorial_general_01.png
[2]: ./media/linkedinElevate-tutorial/tutorial_general_02.png
[3]: ./media/linkedinElevate-tutorial/tutorial_general_03.png
[4]: ./media/linkedinElevate-tutorial/tutorial_general_04.png

[100]: ./media/linkedinElevate-tutorial/tutorial_general_100.png

[200]: ./media/linkedinElevate-tutorial/tutorial_general_200.png
[201]: ./media/linkedinElevate-tutorial/tutorial_general_201.png
[202]: ./media/linkedinElevate-tutorial/tutorial_general_202.png
[203]: ./media/linkedinElevate-tutorial/tutorial_general_203.png
