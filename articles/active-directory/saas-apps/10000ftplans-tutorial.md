---
title: 'Samouczek: Integracji Azure Active Directory z planami 10 000 ft | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i planów ft 10 000.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: b60c955e-8fa3-4872-a897-c4e81fd7beac
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: jeedes
ms.openlocfilehash: 9dcfb6d8a27e418533670691c1d22ed4400204a9
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35942593"
---
# <a name="tutorial-azure-active-directory-integration-with-10000ft-plans"></a>Samouczek: Integracji Azure Active Directory z planami ft 10 000

Z tego samouczka dowiesz się sposobu integracji z usługą Azure Active Directory (Azure AD) planów ft 10 000.

Integrowanie planów 10 000 ft z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do planów ft 10 000
- Umożliwia użytkownikom automatycznie pobrać zalogowane do 10 000 ft planów (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z planami 10 000 ft, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- 10 000 ft planów logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna tutaj [wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie 10 000 ft plany z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-10000ft-plans-from-the-gallery"></a>Dodawanie 10 000 ft plany z galerii
Aby skonfigurować integrację 10 000 ft planów usługi Azure AD, należy dodać planów 10 000 ft z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać planów 10 000 ft z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **planów 10 000 ft**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/10000ftplans-tutorial/tutorial_10,000ftplans_search.png)

5. W panelu wyników wybierz **planów 10 000 ft**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/10000ftplans-tutorial/tutorial_10,000ftplans_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z 10 000 ft planów opartych na koncie użytkownika testu o nazwie "Britta Simona".

Do rejestracji jednokrotnej do pracy usługi Azure AD musi ustalić użytkownika odpowiednika w planach ft 10 000 do użytkownika w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w 10 000 ft planów musi się.

W planie 10 000 ft przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z planami 10 000 ft, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[10 000 ft Tworzenie planów testów użytkownika](#creating-a-10000ft-plans-test-user)**  — mają odpowiednika Simona Britta w 10 000 ft planów połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji planów ft 10 000.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z planami 10 000 ft, wykonaj następujące czynności:**

1. W portalu Azure na **planów 10 000 ft** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/10000ftplans-tutorial/tutorial_10,000ftplans_samlbase.png)

3. Na **10 000 ft plany domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/10000ftplans-tutorial/tutorial_10,000ftplans_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL: `https://app.10000ft.com`

    b. W **identyfikator** tekstowym, wpisz adres URL: `https://app.10000ft.com/saml/metadata`

    > [!NOTE] 
    > Wartość **identyfikator** jest inny, jeśli masz domenę niestandardową. Skontaktuj się z [zespołem pomocy technicznej planów 10 000 ft](https://www.10000ft.com/plans/support) aby zyskać tę wartość. 
 
4. Na **certyfikat podpisywania SAML** kliknij **Certificate(Raw)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/10000ftplans-tutorial/tutorial_10,000ftplans_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/10000ftplans-tutorial/tutorial_general_400.png)

6. Na **10 000 ft plany konfiguracji** , kliknij przycisk **skonfigurować plany ft 10 000** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/10000ftplans-tutorial/tutorial_10,000ftplans_configure.png) 

7. Skonfigurować logowanie jednokrotne w **planów 10 000 ft** stronie, musisz wysłać pobrany **Certificate(Raw), adres URL Sign-Out, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** do [zespołem pomocy technicznej planów ft 10 000](https://www.10000ft.com/plans/support).

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/10000ftplans-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/10000ftplans-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/10000ftplans-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/10000ftplans-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-10000ft-plans-test-user"></a>10 000 ft Tworzenie planów użytkownik testowy

Celem tej sekcji jest utworzenie użytkownika o nazwie Simona Britta w planach ft 10 000. 10 000 ft planów obsługę w czasie, który jest domyślnie włączone. Nie ma elementu akcji można w tej sekcji. Nowy użytkownik został utworzony podczas próby dostępu 10 000 ft planów, jeśli go jeszcze nie istnieje. 

> [!NOTE]
> Jeśli trzeba ręcznie utworzyć użytkownika, należy skontaktować się [zespołem pomocy technicznej planów 10 000 ft](https://www.10000ft.com/plans/support).

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do 10 000 ft planów.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta do 10 000 ft planów, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **planów 10 000 ft**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/10000ftplans-tutorial/tutorial_10,000ftplans_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

Celem tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.  
Po kliknięciu kafelka planów 10 000 ft w panelu dostępu należy należy pobrać automatycznie zalogowane do 10 000 ft planów aplikacji.
 
## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/10000ftplans-tutorial/tutorial_general_01.png
[2]: ./media/10000ftplans-tutorial/tutorial_general_02.png
[3]: ./media/10000ftplans-tutorial/tutorial_general_03.png
[4]: ./media/10000ftplans-tutorial/tutorial_general_04.png

[100]: ./media/10000ftplans-tutorial/tutorial_general_100.png

[200]: ./media/10000ftplans-tutorial/tutorial_general_200.png
[201]: ./media/10000ftplans-tutorial/tutorial_general_201.png
[202]: ./media/10000ftplans-tutorial/tutorial_general_202.png
[203]: ./media/10000ftplans-tutorial/tutorial_general_203.png

