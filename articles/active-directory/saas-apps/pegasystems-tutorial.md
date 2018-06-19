---
title: 'Samouczek: Integracji Azure Active Directory z systemami Pega | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między systemami Pega a usługą Azure Active Directory.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 31acf80f-1f4b-41f1-956f-a9fbae77ee69
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: jeedes
ms.openlocfilehash: e56a738c22d763bfc85e5223fa6c4640ebf35092
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35943472"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Samouczek: Integracji Azure Active Directory z systemami Pega

W tym samouczku Dowiedz się jak integrować systemy Pega z usługą Azure Active Directory (Azure AD).

Integrowania systemów Pega z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do systemów Pega.
- Umożliwia użytkownikom automatycznie pobrać zalogowane systemów Pega (logowanie jednokrotne) z konta usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z systemami Pega, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Systemy Pega jednokrotnego włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Pega systemów z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-pega-systems-from-the-gallery"></a>Dodawanie Pega systemów z galerii
Aby skonfigurować integrację usługi Azure AD Pega systemy, należy dodać Pega systemów z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Pega systemów z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **systemów Pega**, wybierz pozycję **systemów Pega** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Systemy Pega na liście wyników](./media/pegasystems-tutorial/tutorial_pegasystems_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Pega systemy oparte na użytkownika testowego o nazwie "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w systemach Pega jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w systemach Pega musi się.

W systemach Pega, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z systemami Pega, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego systemów Pega](#create-a-pega-systems-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta systemów Pega, połączonej z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Pega systemów.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z systemami Pega, wykonaj następujące czynności:**

1. W portalu Azure na **systemów Pega** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/pegasystems-tutorial/tutorial_pegasystems_samlbase.png)

3. Na **adresy URL i domeny systemów Pega** sekcji, wykonaj następujące kroki, aby skonfigurować aplikację w **IDP** inicjowane tryb:

    ![Adresy URL i domeny systemów Pega pojedynczy informacje logowania jednokrotnego](./media/pegasystems-tutorial/tutorial_pegasystems_url.png)

    a. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<CUSTOMERNAME>.pegacloud.io:443/prweb/sp/<INSTANCEID>`

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<CUSTOMERNAME>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** inicjowane tryb:

    ![Adresy URL i domeny systemów Pega pojedynczy informacje logowania jednokrotnego](./media/pegasystems-tutorial/tutorial_pegasystems_url1.png)

    W **stan przekazywania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<CUSTOMERNAME>.pegacloud.io/prweb/sso`
     
    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty identyfikator, adres URL odpowiedzi służący i adres URL stanu przekazywania, należy zaktualizować te wartości. Wartości identyfikatora i adres URL odpowiedzi z aplikacji Pega, który znajduje się w dalszej części tego samouczka można znaleźć. Stan przekazywania, skontaktuj się z [zespołem pomocy technicznej klienta systemów Pega](https://www.pega.com/contact-us) można uzyskać wartość. 

5. Aplikacja systemów Pega oczekuje potwierdzenia języka SAML w określonym formacie, musisz dodać mapowania atrybutu niestandardowego do konfiguracji atrybuty tokenu SAML. Oświadczenia te są określonego klienta i zależy od wymagań. Przykład są następujące opcjonalne oświadczenia tylko w którym można skonfigurować dla aplikacji. Możesz zarządzać wartości tych atrybutów z "**atrybuty użytkownika**" sekcji na stronie integracji aplikacji. 

    ![Konfigurowanie rejestracji jednokrotnej](./media/pegasystems-tutorial/tutorial_attribute.png)

6. W **atrybuty użytkownika** sekcji na **logowanie jednokrotne** okna dialogowego, skonfiguruj atrybut tokenu SAML, jak pokazano na powyższej ilustracji i wykonaj następujące czynności:
    
    | Nazwa atrybutu | Wartość atrybutu |
    | ------------------- | -------------------- |    
    | Identyfikator UID | *********** |
    | cn  | *********** |
    | poczta | *********** |
    | grupy dostępu | *********** |
    | Organizacja | *********** |
    | orgdivision | *********** |
    | orgunit | *********** |
    | Grupa robocza | *********** |
    | Numer telefonu | *********** |

    > [!NOTE]
    > Są to wartości określonego klienta. Podaj odpowiednie wartości.

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie rejestracji jednokrotnej](./media/pegasystems-tutorial/tutorial_attribute_04.png)

    ![Konfigurowanie rejestracji jednokrotnej](./media/pegasystems-tutorial/tutorial_attribute_05.png)

    b. W **nazwa** tekstowym, wpisz nazwę atrybut wyświetlany dla danego wiersza.

    c. Z **wartość** listy, wpisz wartość atrybutu wyświetlany dla danego wiersza.
    
    d. Kliknij przycisk **OK**.

7. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/pegasystems-tutorial/tutorial_pegasystems_certificate.png) 
8. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/pegasystems-tutorial/tutorial_general_400.png)
    
9. Skonfigurować logowanie jednokrotne w **systemów Pega** po stronie Otwórz **Pega Portal** z konta administratora w innym oknie przeglądarki.

10. Wybierz **utworzyć** -> **SysAdmin** -> **usługi uwierzytelniania**.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
11. Wykonaj następujące czynności na **Utwórz usługę Aauthentication** ekranu:

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    a. Wybierz **SAML 2.0** z typu

    b. W **nazwa** pole tekstowe, np. wszystkie nazwy logowania jednokrotnego programu Azure AD wprowadź

    c. W **krótki opis** pole tekstowe, wprowadź wszelkie  

    d. Polecenie **Utwórz i Otwórz** 
    
12. W **informacji o dostawcy tożsamości (IdP)** kliknij na **IdP Importowanie metadanych** , a następnie przejdź do pliku metadanych, który został pobrany z portalu Azure. Kliknij przycisk **przesyłania** można załadować metadanych.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
13. Spowoduje to wypełnij dane IdP, jak pokazano poniżej.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
14. Wykonaj następujące czynności na **ustawienia usługi dostawcy (SP)** sekcji:

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    a. Kopiuj **identyfikacji jednostki** i Wklej w portalu Azure **identyfikator** pola tekstowego.

    b.  Kopiuj **lokalizacji potwierdzenia konsumenta Service (ACS)** i Wklej w portalu Azure **adres URL odpowiedzi służący** pola tekstowego.

    c. Wybierz **wyłącz podpisywanie**.

15. Kliknij pozycję **Zapisz**
    
> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/pegasystems-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/pegasystems-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/pegasystems-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/pegasystems-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-pega-systems-test-user"></a>Tworzenie użytkownika testowego systemów Pega

Celem tej sekcji jest utworzenie użytkownika o nazwie Simona Britta w systemach Pega. We współpracy z [zespołem pomocy technicznej klienta systemów Pega](https://www.pega.com/contact-us) może tworzyć użytkowników w Pega Sysyems.


### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do systemów Pega.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta Pega systemów, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **systemów Pega**.

    ![Łącze systemów Pega na liście aplikacji](./media/pegasystems-tutorial/tutorial_pegasystems_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Pega systemów w panelu dostępu należy należy pobrać automatycznie zalogowane do aplikacji Pega systemów.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
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

