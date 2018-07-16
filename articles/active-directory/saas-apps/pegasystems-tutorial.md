---
title: 'Samouczek: Integracja usługi Azure Active Directory przy użyciu systemów Pega | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między systemami Pega a usługi Azure Active Directory.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 31acf80f-1f4b-41f1-956f-a9fbae77ee69
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: jeedes
ms.openlocfilehash: c1b29cb7d09bcaa25457cf6f99731beae30fa7b2
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39046145"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Samouczek: Integracja usługi Azure Active Directory przy użyciu systemów Pega

W tym samouczku dowiesz się, jak integrować systemy Pega za pomocą usługi Azure Active Directory (Azure AD).

Integrowanie systemów Pega z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do systemów Pega.
- Użytkowników, aby automatycznie uzyskać zalogowanych do systemów Pega (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu systemów Pega, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Systemy Pega logowania jednokrotnego włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie systemów Pega z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-pega-systems-from-the-gallery"></a>Dodawanie systemów Pega z galerii
Aby skonfigurować integrację Pega systemów w usłudze Azure AD, należy dodać Pega systemów z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Pega systemów z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Pega systemów**, wybierz opcję **Pega systemów** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Systemy Pega na liście wyników](./media/pegasystems-tutorial/tutorial_pegasystems_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą systemów Pega, w zależności od użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w systemach Pega do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w systemach Pega musi zostać ustanowione.

W systemach Pega przypisze się wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu systemów Pega, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego systemów Pega](#create-a-pega-systems-test-user)**  — aby odpowiednikiem Britta Simon w systemach Pega, połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Pega systemów.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z systemami Pega, wykonaj następujące czynności:**

1. W witrynie Azure portal na **systemów Pega** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/pegasystems-tutorial/tutorial_pegasystems_samlbase.png)

3. Na **Pega systemów domena i adresy URL** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![Pega systemów domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/pegasystems-tutorial/tutorial_pegasystems_url.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<CUSTOMERNAME>.pegacloud.io:443/prweb/sp/<INSTANCEID>`

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<CUSTOMERNAME>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![Pega systemów domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/pegasystems-tutorial/tutorial_pegasystems_url1.png)

    W **tan przekaźnika** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<CUSTOMERNAME>.pegacloud.io/prweb/sso`
     
    > [!NOTE] 
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora, adres URL odpowiedzi i adres URL przekaźnika stanu. Można znaleźć wartości identyfikatora i adres URL odpowiedzi z Pega aplikacji, które zostało wyjaśnione w dalszej części tego samouczka. Stan przekazywania, skontaktuj się z pomocą [zespołem pomocy technicznej klienta systemy Pega](https://www.pega.com/contact-us) można uzyskać wartość. 

5. Aplikacja systemów Pega oczekuje twierdzenia SAML w określonym formacie, który wymaga dodania mapowania atrybutów niestandardowych konfiguracji atrybuty tokenu języka SAML. Te oświadczenia są zależnie od klienta i zależy od wymagań klienta. Następujące opcjonalne oświadczenia są przykładem tylko, które można skonfigurować dla aplikacji. Możesz zarządzać wartości te atrybuty z "**atrybutów użytkownika**" sekcji na stronie integracji aplikacji. 

    ![Konfigurowanie logowania jednokrotnego](./media/pegasystems-tutorial/tutorial_attribute.png)

6. W **atrybutów użytkownika** sekcji na **logowanie jednokrotne** okno dialogowe, skonfiguruj atrybut tokenu SAML, pokazany na wcześniejszej ilustracji, używając i wykonaj następujące czynności:
    
    | Nazwa atrybutu | Wartość atrybutu |
    | ------------------- | -------------------- |    
    | Identyfikator UID | *********** |
    | cn  | *********** |
    | poczta | *********** |
    | grupy dostępu | *********** |
    | organizacja | *********** |
    | orgdivision | *********** |
    | orgunit | *********** |
    | Grupa robocza | *********** |
    | Numer telefonu | *********** |

    > [!NOTE]
    > Są to wartości określonego klienta. Podaj odpowiednie wartości.

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](./media/pegasystems-tutorial/tutorial_attribute_04.png)

    ![Konfigurowanie logowania jednokrotnego](./media/pegasystems-tutorial/tutorial_attribute_05.png)

    b. W **nazwa** polu tekstowym wpisz nazwę atrybutu, wyświetlanego dla tego wiersza.

    c. Z **wartość** wpisz wartość atrybutu wyświetlanego dla tego wiersza.
    
    d. Kliknij przycisk **OK**.

7. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/pegasystems-tutorial/tutorial_pegasystems_certificate.png) 
8. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/pegasystems-tutorial/tutorial_general_400.png)
    
9. Aby skonfigurować logowanie jednokrotne na **systemów Pega** po stronie Otwórz **Pega Portal** przy użyciu konta administratora w innym oknie przeglądarki.

10. Wybierz **tworzenie** -> **SysAdmin** -> **usługi uwierzytelniania**.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
11. Wykonaj następujące czynności na **żądania utworzenia usługi Aauthentication** ekranu:

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    a. Wybierz **SAML 2.0** z typu

    b. W **nazwa** polu tekstowym wprowadź wszelkie np. nazwa logowania jednokrotnego usługi Azure AD

    c. W **krótki opis** polu tekstowym wprowadź opis wszystkie  

    d. Kliknij pozycję **Utwórz i Otwórz** 
    
12. W **informacji o dostawcy tożsamości (IdP)** sekcji, kliknij pozycję **metadanych tożsamości importu** i przejdź do pliku metadanych, który został pobrany z witryny Azure portal. Kliknij przycisk **przesyłania** można załadować metadanych.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
13. Spowoduje to wypełnienie danych dostawcy tożsamości, jak pokazano poniżej.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
14. Wykonaj następujące czynności na **ustawienia usługi dostawcy (SP)** sekcji:

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    a. Kopiuj **identyfikacji jednostki** wartość i Wklej w portalu Azure **identyfikator** pola tekstowego.

    b.  Kopiowanie **lokalizacji potwierdzenie konsumenta Service (ACS)** wartość i Wklej w portalu Azure **adres URL odpowiedzi** pole tekstowe.

    c. Wybierz **wyłączenie podpisywanie**.

15. Kliknij pozycję **Zapisz**
    
> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/pegasystems-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/pegasystems-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/pegasystems-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/pegasystems-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-pega-systems-test-user"></a>Tworzenie użytkownika testowego Pega systemów

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon w systemach Pega. Skontaktuj się z [zespołem pomocy technicznej klienta systemy Pega](https://www.pega.com/contact-us) utworzyć użytkowników w Pega Sysyems.


### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do udzielania dostępu do systemów Pega za pomocą platformy Azure logowania jednokrotnego.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon Pega systemów, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **systemów Pega**.

    ![Link Pega systemów na liście aplikacji](./media/pegasystems-tutorial/tutorial_pegasystems_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Pega systemów w panelu dostępu, możesz należy pobrać automatycznie zalogowanych do aplikacji Pega systemów.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/pegasystems-tutorial/tutorial_general_01.png
[2]: ./media/pegasystems-tutorial/tutorial_general_02.png
[3]: ./media/pegasystems-tutorial/tutorial_general_03.png
[4]: ./media/pegasystems-tutorial/tutorial_general_04.png

[100]: ./media/pegasystems-tutorial/tutorial_general_100.png

[200]: ./media/pegasystems-tutorial/tutorial_general_200.png
[201]: ./media/pegasystems-tutorial/tutorial_general_201.png
[202]: ./media/pegasystems-tutorial/tutorial_general_202.png
[203]: ./media/pegasystems-tutorial/tutorial_general_203.png

