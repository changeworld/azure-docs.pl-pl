---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Shmoop dla szkół | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między Shmoop dla szkół i usługi Azure Active Directory.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 1d75560a-55b3-42e9-bda1-92b01c572d8e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4091b20e97ca76629260a7420beecb77412b0d39
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60310862"
---
# <a name="tutorial-azure-active-directory-integration-with-shmoop-for-schools"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Shmoop dla szkół

W tym samouczku dowiesz się, jak zintegrować Shmoop dla szkoły z usługą Azure Active Directory (Azure AD).

Integrowanie Shmoop dla szkoły z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, który ma dostęp do Shmoop dla szkół.
- Aby umożliwić użytkownikom automatycznie pobrać zalogowany do Shmoop dla szkoły za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Aby uzyskać więcej informacji na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Shmoop dla szkół, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Shmoop dla szkół logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Nie zaleca się testowanie kroki opisane w tym samouczku przy użyciu środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, zalecamy:

- Przy użyciu środowiska produkcyjnego, tylko wtedy, gdy jest to konieczne.
- Wprowadzenie [bezpłatnej miesięcznej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/) Jeśli nie masz jeszcze środowisko w wersji próbnej usługi Azure AD.

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz, który jest opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Shmoop dla szkół, za pomocą galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="add-shmoop-for-schools-from-the-gallery"></a>Dodaj Shmoop dla szkół z galerii
Aby skonfigurować integrację Shmoop dla szkół w usłudze Azure AD, należy dodać Shmoop dla szkół w galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Shmoop dla szkół w galerii, wykonaj następujące czynności:**

1. W [witryny Azure portal](https://portal.azure.com), w okienku po lewej stronie wybierz **usługi Azure Active Directory** ikony. 

    ![Przycisk Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw][2]
    
3. Aby dodać nową aplikację, wybierz **nową aplikację** przycisk u góry okno dialogowe.

    ![Przycisk Nowa aplikacja][3]

4. W polu wyszukiwania wpisz **Shmoop dla szkół**. Wybierz **Shmoop dla szkół** z wyników, następnie wybierz pozycję **Dodaj** przycisk, aby dodać aplikację.

    ![Shmoop dla szkół, na liście wyników](./media/shmoopforschools-tutorial/tutorial_shmoopforschools_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą Shmoop dla szkół oparte na użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi wiedzieć, kim jest odpowiednikiem użytkownik Shmoop dla szkół do użytkownika w usłudze Azure AD. Innymi słowy należy ustanowić łącze między użytkownika usługi Azure AD i powiązanych użytkowników Shmoop dla szkół.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Shmoop dla szkół, wykonaj poniższe bloki konstrukcyjne:

1. [Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on) — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. [Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą użytkownika Britta Simon.
3. [Tworzenie użytkownika testowego Shmoop dla szkół](#create-a-shmoop-for-schools-test-user) mieć odpowiednikiem Britta Simon Shmoop dla szkół połączonego z usługi Azure AD reprezentacja użytkownika.
4. [Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user) — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. [Przetestuj logowanie jednokrotne](#test-single-sign-on), aby sprawdzić działanie konfiguracji.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Shmoop dla szkół.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Shmoop dla szkół, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Shmoop dla szkół** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

2. W **logowanie jednokrotne** w menu rozwijanym w oknie dialogowym **tryb rejestracji jednokrotnej**, wybierz opcję **opartej na SAML logowania jednokrotnego**.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/shmoopforschools-tutorial/tutorial_shmoopforschools_samlbase.png)

3. W **Shmoop dla szkół domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/shmoopforschools-tutorial/tutorial_shmoopforschools_url.png)

    a. W **adres URL logowania** wpisz adres URL z następującym wzorcem: `https://schools.shmoop.com/public-api/saml2/start/<uniqueid>`

    b. W **identyfikator** wpisz adres URL z następującym wzorcem: `https://schools.shmoop.com/<uniqueid>`

    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości, używając rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej Shmoop dla szkół klienta](mailto:support@shmoop.com) do uzyskania tych wartości. 
 
4. Aplikacja Shmoop dla szkół oczekuje twierdzenia SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Poniższy zrzut ekranu pokazuje, jak skonfigurować potwierdzenia:

    ![Konfigurowanie logowania jednokrotnego](./media/shmoopforschools-tutorial/tutorial_attribute.png)

    > [!NOTE]
    > Shmoop dla szkoły obsługuje dwie role użytkowników: **Nauczyciel** i **uczniów**. Skonfiguruj te role w usłudze Azure AD, aby użytkownicy mogą przypisać odpowiednie role. Aby dowiedzieć się, jak skonfigurować role w usłudze Azure AD, zobacz [zarządzanie dostępem przy użyciu RBAC i witryny Azure portal](../../role-based-access-control/role-assignments-portal.md).
    
5. W **atrybutów użytkownika** sekcji **logowanie jednokrotne** okna dialogowego Skonfiguruj atrybut tokenu SAML, jak pokazano na poprzedniej ilustracji.  Następnie wykonaj następujące kroki:

    | Nazwa atrybutu | Wartość atrybutu |
    | -------------- | --------------- |
    | role (rola)           | user.assignedroles |

    a. Aby otworzyć **Dodawanie atrybutu** okno dialogowe, wybierz opcję **Dodaj atrybut**.
    
    ![Konfigurowanie logowania jednokrotnego](./media/shmoopforschools-tutorial/tutorial_attribute_04.png)
    
    ![Konfigurowanie logowania jednokrotnego](./media/shmoopforschools-tutorial/tutorial_attribute_05.png)
    
    b. W **nazwa** wpisz nazwę atrybutu, która jest wyświetlana dla tego wiersza.
    
    c. Z **wartość** , wybierz wartość atrybutu, który jest wyświetlany dla danego wiersza na liście.

    d. Pozostaw **Namespace** puste pole.
    
    e. Wybierz przycisk **OK**.

6. Wybierz ikonę **Zapisz**.

    ![Konfigurowanie logowania jednokrotnego](./media/shmoopforschools-tutorial/tutorial_general_400.png)

7. Na **certyfikat podpisywania SAML** sekcji, kliknij przycisk kopiowania, aby skopiować **adres Url metadanych Federacji aplikacji** i wklej go w Notatniku.

    ![Link do pobierania certyfikatu](./media/shmoopforschools-tutorial/tutorial_shmoopforschools_certificate.png)

8. Aby skonfigurować logowanie jednokrotne na **Shmoop dla szkół** stronie, musisz wysłać **adres Url metadanych Federacji aplikacji** do [zespołu pomocy technicznej Shmoop dla szkół](mailto:support@shmoop.com).

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego o nazwie Britta Simon w witrynie Azure portal.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie wybierz **usługi Azure Active Directory** przycisku.

    ![Przycisk Azure Active Directory](./media/shmoopforschools-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**. Następnie wybierz pozycję **wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](./media/shmoopforschools-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, wybierz opcję **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/shmoopforschools-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe Użytkownik](./media/shmoopforschools-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Wybierz pozycję **Utwórz**.
 
### <a name="create-a-shmoop-for-schools-test-user"></a>Tworzenie użytkownika testowego Shmoop dla szkół

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon Shmoop dla szkół. Shmoop dla szkół obsługę just-in-time, który jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli nowy użytkownik jeszcze nie istnieje, zostanie utworzony podczas próby dostępu Shmoop dla szkół.

>[!NOTE]
>Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołu pomocy technicznej Shmoop dla szkół](mailto:support@shmoop.com).

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do Shmoop dla szkół.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon Shmoop dla szkół, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji. Następnie przejdź do **aplikacje dla przedsiębiorstw** w widoku katalogu.  Następnie wybierz pozycję **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **Shmoop dla szkół**.

    ![Link Shmoop dla szkół na liście aplikacji](./media/shmoopforschools-tutorial/tutorial_shmoopforschools_app.png)  

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”][202]

4. Wybierz przycisk **Add** (Dodaj). Następnie w **Dodaj przydziału** okno dialogowe, wybierz opcję **użytkowników i grup**.

    ![Okienko Dodawanie przypisania][203]

5. W **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. W **użytkowników i grup** okno dialogowe, kliknij przycisk **wybierz** przycisku. 

7. W **Dodaj przydziału** okno dialogowe, wybierz opcję **przypisać** przycisku.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po wybraniu **Shmoop dla szkół** kafelka w panelu dostępu, należy uzyskać automatycznie zalogowano się do aplikacji Shmoop dla szkół.

Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/shmoopforschools-tutorial/tutorial_general_01.png
[2]: ./media/shmoopforschools-tutorial/tutorial_general_02.png
[3]: ./media/shmoopforschools-tutorial/tutorial_general_03.png
[4]: ./media/shmoopforschools-tutorial/tutorial_general_04.png

[100]: ./media/shmoopforschools-tutorial/tutorial_general_100.png

[200]: ./media/shmoopforschools-tutorial/tutorial_general_200.png
[201]: ./media/shmoopforschools-tutorial/tutorial_general_201.png
[202]: ./media/shmoopforschools-tutorial/tutorial_general_202.png
[203]: ./media/shmoopforschools-tutorial/tutorial_general_203.png

