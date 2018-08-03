---
title: 'Samouczek: Integracja usługi Azure Active Directory z platformą Spark Cisco | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Spark firmy Cisco.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: bebc8d674d7448ea0ce6a1f11b7ae80335df9cdc
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431702"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-spark"></a>Samouczek: Integracja usługi Azure Active Directory z platformą Spark firmy Cisco

W tym samouczku dowiesz się, jak zintegrować Cisco Spark z usługą Azure Active Directory (Azure AD).

Integrowanie Cisco Spark z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do platformy Spark firmy Cisco
- Użytkowników, aby automatycznie uzyskać zalogowanych do Cisco Spark (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą platformy Spark Cisco, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Cisco Spark logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Cisco Spark za pomocą galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-cisco-spark-from-the-gallery"></a>Dodawanie Cisco Spark za pomocą galerii
Aby skonfigurować integrację Cisco platformy Spark w usłudze Azure AD, należy dodać Cisco Spark z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Cisco Spark z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **Cisco Spark**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/cisco-spark-tutorial/tutorial_ciscospark_search.png)

1. W panelu wyników wybierz **Cisco Spark**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/cisco-spark-tutorial/tutorial_ciscospark_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą platformy Spark Cisco oparte na użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Spark firmy Cisco dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika platformie Cisco Spark musi nawiązać.

Platformy Spark Cisco przypisze się wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą platformy Spark Cisco, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Cisco Spark](#creating-a-cisco-spark-test-user)**  — aby odpowiednikiem Britta Simon w Spark Cisco, połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Cisco Spark.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne za pomocą platformy Spark Cisco, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Cisco Spark** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/cisco-spark-tutorial/tutorial_ciscospark_samlbase.png)

1. Na **Cisco Spark domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/cisco-spark-tutorial/tutorial_ciscospark_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL jako: `https://web.ciscospark.com/#/signin`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://idbroker.webex.com/<companyname>`

    > [!NOTE] 
    > Ta wartość nie jest prawdziwe. Zaktualizuj tę wartość przy użyciu rzeczywistego identyfikatora. Skontaktuj się z pomocą [zespół obsługi klienta platformy Spark Cisco](https://support.ciscospark.com/) aby zyskać tę wartość. 
 
1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/cisco-spark-tutorial/tutorial_ciscospark_certificate.png) 

1. Aplikacja Spark Cisco oczekuje twierdzenia SAML w celu uwzględnienia określonych atrybutów. Skonfiguruj następujące atrybuty dla tej aplikacji. Możesz zarządzać wartości te atrybuty z **atrybutów użytkownika** sekcji na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład tego.
    
    ![Konfigurowanie logowania jednokrotnego](./media/cisco-spark-tutorial/tutorial_ciscospark_07.png) 

1. W **atrybutów użytkownika** sekcji na **logowanie jednokrotne** okno dialogowe, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej i wykonaj następujące czynności:
    
    | Nazwa atrybutu  | Wartość atrybutu |
    | --------------- | -------------------- |    
    |   Identyfikator UID    | user.userprincipalname |   

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](./media/cisco-spark-tutorial/tutorial_attribute_04.png)

    ![Konfigurowanie logowania jednokrotnego](./media/cisco-spark-tutorial/tutorial_attribute_05.png)
    
    b. W **nazwa** polu tekstowym wpisz nazwę atrybutu, wyświetlanego dla tego wiersza.
    
    c. Z **wartość** wpisz wartość atrybutu wyświetlanego dla tego wiersza.
    
    d. Kliknij przycisk **OK**.

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/cisco-spark-tutorial/tutorial_general_400.png)

1. Zaloguj się do [Zarządzanie współpracy w chmurze firmy Cisco](https://admin.ciscospark.com/) przy użyciu poświadczeń administrator o pełnych uprawnieniach.

1. Wybierz **ustawienia** i w obszarze **uwierzytelniania** kliknij **Modyfikuj**.
   
    ![Konfigurowanie logowania jednokrotnego](./media/cisco-spark-tutorial/tutorial_cisco_spark_10.png)
    
1. Wybierz **zintegrować z dostawcy tożsamości innych firm 3. (Zaawansowane)**  i przejdź do następnego ekranu.

1. Na **Importowanie metadanych tożsamości** stronie albo przeciągnij i upuść plik metadanych usługi Azure AD na stronie lub użyj opcji przeglądarki plików Znajdź i przekaż plik metadanych usługi Azure AD. Następnie wybierz **wymagają certyfikatu podpisanego przez urząd certyfikacji w metadanych (bardziej bezpieczny)** i kliknij przycisk **dalej**. 
    
    ![Konfigurowanie logowania jednokrotnego](./media/cisco-spark-tutorial/tutorial_cisco_spark_11.png)

1. Wybierz **Test rejestracji Jednokrotnej połączenia**i gdy zostanie otwarta nowa karta przeglądarki, Uwierzytelnij się przy użyciu usługi Azure AD, logując się.

1. Wróć do **Zarządzanie współpracy w chmurze firmy Cisco** karcie przeglądarki. Jeśli test zakończył się pomyślnie, wybierz opcję **ten test zakończył się pomyślnie. Włącz opcję logowania jednokrotnego** i kliknij przycisk **dalej**.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/cisco-spark-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/cisco-spark-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/cisco-spark-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/cisco-spark-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-cisco-spark-test-user"></a>Tworzenie użytkownika testowego Spark firmy Cisco

W tej sekcji utworzysz użytkownika o nazwie Britta Simon platformie Spark firmy Cisco. W tej sekcji utworzysz użytkownika o nazwie Britta Simon platformie Spark firmy Cisco.

1. Przejdź do [Zarządzanie współpracy w chmurze firmy Cisco](https://admin.ciscospark.com/) przy użyciu poświadczeń administrator o pełnych uprawnieniach.

1. Kliknij przycisk **użytkowników** i następnie **Zarządzanie użytkownikami**.
   
    ![Konfigurowanie logowania jednokrotnego](./media/cisco-spark-tutorial/tutorial_cisco_spark_12.png) 

1. W **Zarządzanie użytkownika** wybierz **ręcznie dodać lub zmodyfikować użytkowników** i kliknij przycisk **dalej**.

1. Wybierz **nazwy i adresu E-mail**. Następnie wypełnij pola tekstowego w następujący sposób:
   
    ![Konfigurowanie logowania jednokrotnego](./media/cisco-spark-tutorial/tutorial_cisco_spark_13.png) 
    
    a. W **imię** polu tekstowym wpisz **Britta**. 
    
    b. W **nazwisko** polu tekstowym wpisz **Simon**.
    
    c. W **adres E-mail** polu tekstowym wpisz **britta.simon@contoso.com**.

1. Kliknij znak plus, aby dodać Britta Simon. Następnie kliknij przycisk **Dalej**.

1. W **Dodawanie usług oferowanych użytkownikom końcowym** okna, kliknij przycisk **Zapisz** i następnie **Zakończ**.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do platformy Spark firmy Cisco.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Cisco Spark, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Cisco Spark**.

    ![Konfigurowanie logowania jednokrotnego](./media/cisco-spark-tutorial/tutorial_ciscospark_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest test konfiguracji logowania jednokrotnego usługi Azure AD za pomocą panelu dostępu.

Po kliknięciu kafelka Cisco platformy Spark w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Cisco Spark.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/cisco-spark-tutorial/tutorial_general_01.png
[2]: ./media/cisco-spark-tutorial/tutorial_general_02.png
[3]: ./media/cisco-spark-tutorial/tutorial_general_03.png
[4]: ./media/cisco-spark-tutorial/tutorial_general_04.png
[10]: ./media/cisco-spark-tutorial/tutorial_general_060.png
[100]: ./media/cisco-spark-tutorial/tutorial_general_100.png

[200]: ./media/cisco-spark-tutorial/tutorial_general_200.png
[201]: ./media/cisco-spark-tutorial/tutorial_general_201.png
[202]: ./media/cisco-spark-tutorial/tutorial_general_202.png
[203]: ./media/cisco-spark-tutorial/tutorial_general_203.png

