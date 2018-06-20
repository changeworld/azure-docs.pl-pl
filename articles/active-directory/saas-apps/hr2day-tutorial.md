---
title: 'Samouczek: Integracji Azure Active Directory z HR2day przez Merces | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i HR2day przez Merces.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 853d08c9-27b1-48d4-b8e7-3705140eb67f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/24/2017
ms.author: jeedes
ms.openlocfilehash: a63ef3b089ec80da6a75aaaf8a4bb2454fec6cf7
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36224185"
---
# <a name="tutorial-azure-active-directory-integration-with-hr2day-by-merces"></a>Samouczek: Integracji Azure Active Directory z HR2day przez Merces

Z tego samouczka dowiesz się integrowanie HR2day przez Merces z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD HR2day przez Merces zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do HR2day przez Merces.
- Można umożliwić użytkownikom automatycznie pobrać zalogujesz się HR2day przy Merces z konta usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji--portalu Azure.

Aby uzyskać więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z HR2day przez Merces, potrzebne są następujące elementy:

- Subskrypcja usługi Azure AD.
- HR2day przez Merces logowanie jednokrotne włączone subskrypcji.

> [!NOTE]
> Nie zaleca się używanie środowiska produkcyjnego do testowania czynności w tym samouczku.

Aby przetestować kroki opisane w tym samouczku, wykonaj te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Pobierz [miesięczna bezpłatna wersja próbna usługi Azure AD](https://azure.microsoft.com/pricing/free-trial/) Jeśli jeszcze nie masz.  

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz, który jest opisana w tym temacie składa się z dwóch głównych elementów:

1. Dodawanie HR2day przez Merces z galerii.
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne.

## <a name="add-hr2day-by-merces-from-the-gallery"></a>Dodaj HR2day przez Merces z galerii
Aby skonfigurować integrację usługi Azure AD HR2day przez Merces, należy dodać HR2day przez Merces z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać HR2day przez Merces z galerii, należy wykonać następujące czynności:**

1. W [portalu Azure](https://portal.azure.com), w okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, zaznacz **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **HR2day przez Merces**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/hr2day-tutorial/tutorial_hr2daybymerces_search.png)

5. W panelu wyników wybierz **HR2day przez Merces**, a następnie wybierz **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/hr2day-tutorial/tutorial_hr2daybymerces_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej
W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z HR2day przez Merces oparte na koncie użytkownika testu o nazwie "Britta Simona".

Do rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, komu użytkownika odpowiednika w HR2day przez Merces jest użytkownikiem w usłudze Azure AD. Innymi słowy należy ustanowić powiązanie użytkownika usługi Azure AD i danemu użytkownikowi w HR2day przez Merces.

HR2day przez Merces, przypisywanie **nazwy użytkownika** w usłudze Azure AD do **Username** ustanowienie relacji.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z HR2day przez Merces, należy wykonać poniższe bloki konstrukcyjne:

1. [Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on): umożliwianie użytkownikom korzystać z tej funkcji.
2. [Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user): Test usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. [Utwórz HR2day przez użytkownika testowego Merces](#creating-an-hr2day-by-merces-test-user): tworzenie odpowiednikiem Simona Britta w HR2day przez Merces połączonego z usługi Azure AD reprezentację użytkownika.
4. [Przypisz użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user): Włącz Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. [Test rejestracji jednokrotnej](#testing-single-sign-on): Sprawdź, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i konfigurowanie rejestracji jednokrotnej w sieci HR2day przez aplikację Merces.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z HR2day przez Merces, należy wykonać następujące czynności:**

1. W portalu Azure na **HR2day przez Merces** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Aby włączyć logowanie jednokrotne, w **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego**.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/hr2day-tutorial/tutorial_hr2daybymerces_samlbase.png)

3. W **HR2day Merces domeny i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/hr2day-tutorial/tutorial_hr2daybymerces_url.png)

    a. W **adres URL logowania** wpisz adres URL przy użyciu następującego wzorca: `https://<tenantname>.force.com/<instancename>`.

    b. W **identyfikator** wpisz adres URL przy użyciu następującego wzorca: `https://hr2day.force.com/<companyname>`.

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL logowania i identyfikator, należy zaktualizować te wartości. Skontaktuj się z [HR2day przez zespół pomocy technicznej klienta Merces](mailto:servicedesk@merces.nl) uzyskać te wartości. 
 


4. Na **certyfikat podpisywania SAML** wybierz opcję **Certificate(Base64)**, a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/hr2day-tutorial/tutorial_hr2daybymerces_certificate.png) 

5. W tej sekcji opisano sposób umożliwić użytkownikom uwierzytelnianie na HR2day przez Merces do swojego konta w usłudze Azure AD. One to robić przy użyciu Federacji, która jest oparta na protokole SAML.

    Twoje HR2day przez aplikację Merces oczekuje potwierdzenia języka SAML w określonym formacie, który wymaga można dodać mapowanie atrybutów niestandardowych do Twojego tokenu SAML. Poniższy zrzut ekranu przedstawia przykład. 

    ![Konfigurowanie rejestracji jednokrotnej](./media/hr2day-tutorial/tutorial_hr2day_00.png)
    
    > [!NOTE] 
    Aby można było skonfigurować potwierdzenia języka SAML, należy skontaktować się [HR2day przez zespół pomocy technicznej klienta Merces](mailto:servicedesk@merces.nl) i zażądać wartość atrybutu Unikatowy identyfikator dla dzierżawy. Należy tę wartość, aby wykonać kroki opisane w następnej sekcji. 

6. W **logowanie jednokrotne** okna dialogowego, **atrybuty użytkownika** skonfiguruj atrybut tokenu SAML, jak pokazano na poniższej ilustracji. Następnie należy wykonać następujące kroki.
    
      | Nazwa atrybutu    |   Wartość atrybutu |  
    | ------------------- | -------------------- |    
    | ATTR_LOGINCLAIM | `join([mail],"102938475Z","@"` |
    
      a. Aby otworzyć **Dodawanie atrybutu** okno dialogowe, wybierz opcję **Dodaj atrybut**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/hr2day-tutorial/tutorial_attribute_04.png)

    ![Konfigurowanie rejestracji jednokrotnej](./media/hr2day-tutorial/tutorial_attribute_05.png)

    b. W **nazwa** wpisz **ATTR_LOGINCLAIM**.

    c. Z **wartość** listy, wybierz **Join()**.

    d. Z **ciąg1** listy, wybierz **user.mail**.

    e. Aby uzyskać **ciąg2**, wpisz unikatowy identyfikator, który jest świadczona przez Twojego zespołu HR2day.

    f. W **separatora** wpisz **\@**.
    
    g. Wybierz **Ok**.

7. Wybierz ikonę **Zapisz**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/hr2day-tutorial/tutorial_general_400.png)

8. W **HR2day przez konfigurację Merces** zaznacz **skonfigurować HR2day przez Merces** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adres URL**, **SAML identyfikator jednostki**, i **SAML pojedynczy znak na adres URL usługi** z **krótkimi opisami** sekcji.

    ![Konfigurowanie rejestracji jednokrotnej](./media/hr2day-tutorial/tutorial_hr2daybymerces_configure.png) 

9. Aby skonfigurować logowanie Jednokrotne dla aplikacji, skontaktuj się z [HR2day przez zespół pomocy technicznej klienta Merces](mailTo:servicedesk@merces.nl). Dołącz pobrany **Certificate(Base64)** pliku do wiadomości e-mail. Udostępniają **Sign-Out URL**, **identyfikator jednostki SAML**, i **SAML pojedynczy znak na adres URL usługi** tak, aby można je skonfigurować integracji logowania jednokrotnego.

    > [!NOTE]
    >Wspomina do zespołu Merces, że integracja wymaga identyfikator jednostki można ustawić za pomocą wzorca **https://hr2day.force.com/INSTANCENAME**.

    > [!TIP]
    >Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory** > **aplikacje dla przedsiębiorstw** zaznacz **rejestracji jednokrotnej** kartę. Dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzony w [usługi Azure AD osadzonych dokumentacji]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, należy wykonać następujące czynności:**

1. W **portalu Azure**, w okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/hr2day-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie wybierz **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/hr2day-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okno dialogowe, wybierz opcję **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/hr2day-tutorial/create_aaduser_03.png) 

4. W **użytkownika** okna dialogowego należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/hr2day-tutorial/create_aaduser_04.png) 

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło**, a następnie Zapisz hasło.

    d. Wybierz pozycję **Utwórz**.
 
### <a name="create-an-hr2day-by-merces-test-user"></a>Utwórz HR2day przez Merces użytkownika testowego

Celem tej sekcji jest utworzenie użytkownika o nazwie Simona Britta w HR2day przez Merces. Aby dodać użytkowników w ramach konta HR2day, pracy z [HR2day przez zespół pomocy technicznej klienta Merces](mailto:servicedesk@merces.nl). 

> [!NOTE]
> Jeśli trzeba ręcznie utworzyć użytkownika, skontaktuj się z [HR2day przez zespół pomocy technicznej klienta Merces](mailto:servicedesk@merces.nl).

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do udostępnienia jej HR2day przez Merces za pomocą usługi Azure rejestracji jednokrotnej.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta HR2day przez Merces, należy wykonać następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, przejdź do widoku katalogu, a następnie przejdź do **aplikacje dla przedsiębiorstw**. Następnie wybierz pozycję **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **HR2day przez Merces**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/hr2day-tutorial/tutorial_hr2daybymerces_app.png) 

3. W menu po lewej stronie wybierz **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Wybierz **Dodaj** przycisku. Następnie w **Dodaj przydziału** okno dialogowe, wybierz opcję **użytkowników i grup**.

    ![Przypisz użytkownika][203]

5. W **użytkowników i grup** okna dialogowego, **użytkowników** listy, wybierz **Simona Britta**.

6. Kliknij przycisk **wybierz** przycisku.

7. W **Dodaj przydziału** okno dialogowe, wybierz opcję **przypisać**.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.  

Po wybraniu HR2day przez Kafelek Merces w panelu dostępu, można automatycznie pobrać zalogowany HR2day Twojego przez aplikację Merces.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobu integracji aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/hr2day-tutorial/tutorial_general_01.png
[2]: ./media/hr2day-tutorial/tutorial_general_02.png
[3]: ./media/hr2day-tutorial/tutorial_general_03.png
[4]: ./media/hr2day-tutorial/tutorial_general_04.png

[100]: ./media/hr2day-tutorial/tutorial_general_100.png

[200]: ./media/hr2day-tutorial/tutorial_general_200.png
[201]: ./media/hr2day-tutorial/tutorial_general_201.png
[202]: ./media/hr2day-tutorial/tutorial_general_202.png
[203]: ./media/hr2day-tutorial/tutorial_general_203.png

