---
title: 'Samouczek: Integrację usługi Azure Active Directory ze Mozy | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Mozy Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 489b5e62-85c2-45c9-8766-326632d48114
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: jeedes
ms.openlocfilehash: d4da17c5e2a9da0f18d08da56f7bd6ea6b95a0ef
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35941231"
---
# <a name="tutorial-azure-active-directory-integration-with-mozy-enterprise"></a>Samouczek: Integrację usługi Azure Active Directory ze Mozy

Z tego samouczka dowiesz się integrowanie Mozy przedsiębiorstwa w usłudze Azure Active Directory (Azure AD).

Integrowanie Mozy organizacji z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Mozy przedsiębiorstwa
- Umożliwia użytkownikom automatycznie pobrać zalogowane Mozy przedsiębiorstwem (logowanie jednokrotne) z konta usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Mozy przedsiębiorstwa, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Mozy Enterprise logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Mozy przedsiębiorstwa z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-mozy-enterprise-from-the-gallery"></a>Dodawanie Mozy przedsiębiorstwa z galerii
Aby skonfigurować integrację usługi Azure AD Mozy przedsiębiorstwa, należy dodać Mozy przedsiębiorstwa z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Mozy przedsiębiorstwa z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Mozy Enterprise**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/mozy-enterprise-tutorial/tutorial_mozyenterprise_search.png)

5. W panelu wyników wybierz **Mozy Enterprise**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/mozy-enterprise-tutorial/tutorial_mozyenterprise_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i test usługi Azure AD rejestracji jednokrotnej z Mozy przedsiębiorstwa w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w przedsiębiorstwie Mozy jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w przedsiębiorstwie Mozy musi się.

W przedsiębiorstwie Mozy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Mozy przedsiębiorstwa, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Mozy Enterprise](#creating-a-mozy-enterprise-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Mozy przedsiębiorstwo, które jest połączone z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji przedsiębiorstwa Mozy.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Mozy przedsiębiorstwa, wykonaj następujące czynności:**

1. W portalu Azure na **Mozy Enterprise** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/mozy-enterprise-tutorial/tutorial_mozyenterprise_samlbase.png)

3. Na **Mozy domeny przedsiębiorstwa i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/mozy-enterprise-tutorial/tutorial_mozyenterprise_url.png)

    W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<tenantname>.Mozyenterprise.com`

    > [!NOTE] 
    > Ta wartość nie jest prawdziwe. Zaktualizuj tę wartość przy rzeczywisty adres URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta Enterprise Mozy](http://support.mozy.com/) aby zyskać tę wartość.

4. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/mozy-enterprise-tutorial/tutorial_mozyenterprise_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/mozy-enterprise-tutorial/tutorial_general_400.png)

6. Na **konfiguracja dla przedsiębiorstw Mozy** , kliknij przycisk **skonfigurować Mozy Enterprise** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/mozy-enterprise-tutorial/tutorial_mozyenterprise_configure.png) 

7. W oknie przeglądarki innej witryny sieci web Zaloguj się do witryny firmy Mozy Enterprise jako administrator.

8. W **konfiguracji** kliknij **zasady uwierzytelniania**.
   
   ![Zasady uwierzytelniania](./media/mozy-enterprise-tutorial/ic777314.png "zasady uwierzytelniania")

9. Na **zasady uwierzytelniania** sekcji, wykonaj następujące czynności:
   
   ![Zasady uwierzytelniania](./media/mozy-enterprise-tutorial/ic777315.png "zasady uwierzytelniania")
   
   a. Wybierz **usługi katalogowej** jako **dostawcy**.
   
   b. Wybierz **wypychania LDAP**.
   
   c. Kliknij przycisk **uwierzytelnianie SAML** kartę.
   
   d. Wklej **SAML pojedynczy znak na adres URL usługi**, które zostały skopiowane z portalu Azure do **adres URL uwierzytelniania** pola tekstowego.
   
   e. Wklej **identyfikator jednostki SAML**, które zostały skopiowane z portalu Azure do **SAML punktu końcowego** pola tekstowego.
   
   f. Otwórz w Notatniku pobrany zakodowanego certyfikatu base-64, skopiuj zawartość go do Schowka, a następnie wklej cały certyfikat do **certyfikatu SAML** pola tekstowego.
   
   g. Wybierz **włączenia logowania jednokrotnego dla administratorów zalogować się przy użyciu swoich poświadczeń sieciowych**.
   
   h. Kliknij przycisk **zapisać zmiany**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/mozy-enterprise-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/mozy-enterprise-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/mozy-enterprise-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/mozy-enterprise-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-mozy-enterprise-test-user"></a>Tworzenie użytkownika testowego Mozy przedsiębiorstwa

Aby włączyć logowanie w przedsiębiorstwie Mozy użytkowników usługi Azure AD, muszą mieć przydzielone Mozy w przedsiębiorstwie. W przypadku Mozy przedsiębiorstwa Inicjowanie obsługi to zadanie ręczne.

>[!NOTE]
>Możesz użyć innych Mozy Enterprise użytkownika konta tworzenia narzędzi lub interfejsów API dostarczonych przez Mozy przedsiębiorstwa do kont użytkowników usługi AAD.

**Aby udostępnić konta użytkowników, wykonaj następujące czynności:**

1. Zaloguj się do Twojego **Mozy Enterprise** dzierżawy.

2. Kliknij przycisk **użytkowników**, a następnie kliknij przycisk **Dodaj nowego użytkownika**.
   
   ![Użytkownicy](./media/mozy-enterprise-tutorial/ic777317.png "użytkowników")
   
   >[!NOTE]
   >**Dodaj nowego użytkownika** tylko wtedy, gdy tylko jest wyświetlana opcja **Mozy** został wybrany jako dostawcy pod **zasady uwierzytelniania**. Jeśli skonfigurowano uwierzytelnianie SAML, następnie użytkownicy są automatycznie dodawane na ich pierwsze logowanie za pomocą rejestracji jednokrotnej w.
    
3. W oknie dialogowym Nowy użytkownik, wykonaj następujące czynności:
   
   ![Dodawanie użytkowników](./media/mozy-enterprise-tutorial/ic777318.png "Dodawanie użytkowników")
   
   a. Z **wybierz grupę** listy, wybierz grupę.
   
   b. Z **jakiego rodzaju użytkownika** listy, wybierz typ.
   
   c. W **Username** tekstowym, wpisz nazwę użytkownika usługi Azure AD.
   
   d. W **E-mail** tekstowym, wpisz adres e-mail użytkownika usługi Azure AD.
   
   e. Wybierz **wysyłania wiadomości e-mail z instrukcją użytkownika**.
   
   f. Kliknij przycisk **dodać użytkowników**.

     >[!NOTE]
     > Po utworzeniu użytkownika, zostanie wysłana wiadomość e-mail do użytkownika usługi Azure AD, która zawiera link do potwierdzenia konta, zanim staje się aktywny.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do Mozy przedsiębiorstwa.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta Mozy przedsiębiorstwa, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Mozy Enterprise**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/mozy-enterprise-tutorial/tutorial_mozyenterprise_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Mozy przedsiębiorstwa w panelu dostępu, należy pobrać strony logowania organizacji Mozy aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/mozy-enterprise-tutorial/tutorial_general_01.png
[2]: ./media/mozy-enterprise-tutorial/tutorial_general_02.png
[3]: ./media/mozy-enterprise-tutorial/tutorial_general_03.png
[4]: ./media/mozy-enterprise-tutorial/tutorial_general_04.png

[100]: ./media/mozy-enterprise-tutorial/tutorial_general_100.png

[200]: ./media/mozy-enterprise-tutorial/tutorial_general_200.png
[201]: ./media/mozy-enterprise-tutorial/tutorial_general_201.png
[202]: ./media/mozy-enterprise-tutorial/tutorial_general_202.png
[203]: ./media/mozy-enterprise-tutorial/tutorial_general_203.png

