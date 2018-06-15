---
title: 'Samouczek: Integracji Azure Active Directory z Shmoop dla szkoły | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Shmoop dla szkoły.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 1d75560a-55b3-42e9-bda1-92b01c572d8e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: jeedes
ms.openlocfilehash: 799a88344d6c348061af19bfbbd9022025d2d66b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34350614"
---
# <a name="tutorial-azure-active-directory-integration-with-shmoop-for-schools"></a>Samouczek: Integracji Azure Active Directory z Shmoop dla szkoły

Z tego samouczka dowiesz się integrowanie Shmoop dla szkoły w usłudze Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Shmoop dla szkoły zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, kto ma dostęp do Shmoop dla szkoły.
- Można umożliwić użytkownikom automatycznie pobrać zalogowany do Shmoop dla szkoły z konta usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji--portalu Azure.

Aby uzyskać więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Shmoop dla szkoły, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Shmoop dla szkoły logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Nie zaleca się używanie środowiska produkcyjnego do testowania czynności w tym samouczku.

Aby przetestować kroki opisane w tym samouczku, zalecamy:

- Przy użyciu Twojej evironment produkcji tylko wtedy, gdy jest to konieczne.
- Pobieranie [bezpłatna miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/) Jeśli nie masz jeszcze środowisko wersji próbnej usługi Azure AD.

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz, który jest opisane w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Shmoop dla szkoły z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="add-shmoop-for-schools-from-the-gallery"></a>Dodaj Shmoop dla szkoły z galerii
Aby skonfigurować integrację usługi Azure AD Shmoop dla szkoły, należy dodać Shmoop dla szkoły z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Shmoop dla szkoły z galerii, należy wykonać następujące czynności:**

1. W [portalu Azure](https://portal.azure.com), w okienku po lewej stronie wybierz **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, zaznacz **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Shmoop dla szkoły**. Wybierz **Shmoop dla szkoły** spośród wyników wybierz **Dodaj** przycisk, aby dodać aplikację.

    ![Shmoop dla szkoły na liście wyników](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Shmoop dla szkoły oparte na koncie użytkownika testu o nazwie "Britta Simona".

Do rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, komu użytkownika odpowiednika w Shmoop dla szkoły jest użytkownikiem w usłudze Azure AD. Innymi słowy należy ustanowić powiązanie użytkownika usługi Azure AD i danemu użytkownikowi w Shmoop dla szkoły.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Shmoop dla szkoły, wykonaj poniższe bloki konstrukcyjne:

1. [Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on) umożliwienie użytkownikom korzystać z tej funkcji.
2. [Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) do testowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. [Tworzenie użytkownika testowego Shmoop dla szkoły](#create-a-shmoop-for-schools-test-user) w celu zapewnienia odpowiednikiem Simona Britta Shmoop dla szkoły połączonego z usługi Azure AD reprezentację użytkownika.
4. [Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user) umożliwiające Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. [Test rejestracji jednokrotnej](#test-single-sign-on) Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne do aplikacji Shmoop dla szkoły.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Shmoop dla szkoły, wykonaj następujące czynności:**

1. W portalu Azure na **Shmoop dla szkoły** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. W **logowanie jednokrotne** okno dialogowe, w menu rozwijane w obszarze **tryb rejestracji jednokrotnej**, wybierz pozycję **na języku SAML logowania jednokrotnego**.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_samlbase.png)

3. W **Shmoop dla szkoły domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_url.png)

    a. W **adres URL logowania** wpisz adres URL z następującego wzorca: `https://schools.shmoop.com/public-api/saml2/start/<uniqueid>`

    b. W **identyfikator** wpisz adres URL z następującego wzorca: `https://schools.shmoop.com/<uniqueid>`

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL logowania i identyfikator, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej Shmoop dla klienta szkoły](mailto:support@shmoop.com) uzyskać te wartości. 
 
4. Aplikacja Shmoop dla szkoły oczekuje potwierdzenia języka SAML w określonym formacie. Skonfiguruj następujące oświadczeń dla tej aplikacji. Możesz zarządzać wartości tych atrybutów z **atrybuty użytkownika** na stronie integracji aplikacji. Poniższy zrzut ekranu pokazuje, jak skonfigurować potwierdzenia:

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_attribute.png)

    > [!NOTE]
    > Shmoop do szkoły obsługuje dwie role dla użytkowników: **nauczyciel** i **uczniowie**. Konfigurowanie tych ról w usłudze Azure AD, dzięki czemu użytkownicy mogą być przypisani do odpowiedniej roli. Aby zrozumieć, jak skonfigurować role w usłudze Azure AD, zobacz [kontroli dostępu na podstawie ról w aplikacji w chmurze przy użyciu usługi Azure AD](http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/).
    
5. W **atrybuty użytkownika** sekcji **logowanie jednokrotne** okna dialogowego Skonfiguruj atrybut tokenu SAML, jak pokazano na poprzedniej ilustracji.  Następnie wykonaj następujące czynności:

    | Nazwa atrybutu | Wartość atrybutu |
    | -------------- | --------------- |
    | rola           | User.assignedroles |

    a. Aby otworzyć **Dodawanie atrybutu** okno dialogowe, wybierz opcję **Dodaj atrybut**.
    
    ![Konfigurowanie rejestracji jednokrotnej ](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_attribute_04.png)
    
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_attribute_05.png)
    
    b. W **nazwa** wpisz nazwę atrybutu, który jest wyświetlany dla danego wiersza.
    
    c. Z **wartość** listy, wybierz wartość atrybutu, który jest wyświetlany dla danego wiersza.

    d. Pozostaw **Namespace** pole puste.
    
    e. Wybierz **Ok**.

6. Wybierz ikonę **Zapisz**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_400.png)

7. Na **certyfikat podpisywania SAML** sekcji, kliknij przycisk Kopiuj, aby skopiować **adres Url metadanych Federacji aplikacji** i wklej go do Notatnika.

    ![Łącze pobierania certyfikatu](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_certificate.png)

8. Aby skonfigurować logowanie jednokrotne w **Shmoop dla szkoły** stronie, musisz wysłać **adres Url metadanych Federacji aplikacji** do [Shmoop dla szkoły obsługuje team](mailto:support@shmoop.com).

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego o nazwie Simona Britta w portalu Azure.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, należy wykonać następujące czynności:**

1. W portalu Azure, w lewym okienku wybierz **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/active-directory-saas-shmoopforschools-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**. Następnie wybierz **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/active-directory-saas-shmoopforschools-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, wybierz opcję **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/active-directory-saas-shmoopforschools-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego należy wykonać następujące czynności:

    ![Okno dialogowe użytkownika](./media/active-directory-saas-shmoopforschools-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Wybierz pozycję **Utwórz**.
 
### <a name="create-a-shmoop-for-schools-test-user"></a>Tworzenie użytkownika testowego Shmoop dla szkoły

Celem tej sekcji jest utworzenie użytkownika o nazwie Simona Britta w Shmoop dla szkoły. Shmoop dla szkoły obsługę w czasie, który jest domyślnie włączona. Nie ma elementu akcji można w tej sekcji. Jeśli nowy użytkownik jeszcze nie istnieje, jest tworzony podczas próby dostępu Shmoop dla szkoły.

>[!NOTE]
>Jeśli trzeba ręcznie utworzyć użytkownika, skontaktuj się z [Shmoop dla szkoły obsługuje zespołu](mailto:support@shmoop.com).

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji musisz włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Shmoop dla szkoły.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta Shmoop dla szkoły, należy wykonać następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji. Następnie przejdź do **aplikacje dla przedsiębiorstw** w widoku katalogu.  Następnie wybierz pozycję **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Shmoop dla szkoły**.

    ![Łącze Shmoop dla szkoły na liście aplikacji](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_app.png)  

3. W menu po lewej stronie wybierz **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Wybierz **Dodaj** przycisku. Następnie w **Dodaj przydziału** okno dialogowe, wybierz opcję **użytkowników i grup**.

    ![W okienku Dodaj przydziału][203]

5. W **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. W **użytkowników i grup** okno dialogowe, kliknij przycisk **wybierz** przycisku. 

7. W **Dodaj przydziału** okno dialogowe, wybierz opcję **przypisać** przycisku.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po wybraniu **Shmoop dla szkoły** kafelka w panelu dostępu użytkownik powinien pobrać automatycznie zalogowany do aplikacji Shmoop dla szkoły.

Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobu integracji aplikacji SaaS w usłudze Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_203.png

