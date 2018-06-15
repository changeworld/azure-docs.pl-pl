---
title: 'Samouczek: Azure Active Directory integracji z sieciami Palo Alto - GlobalProtect | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i sieciami Palo Alto - GlobalProtect.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 03bef6f2-3ea2-4eaa-a828-79c5f1346ce5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jeedes
ms.openlocfilehash: 43355374436cb9efac82cf118fce285c01a59175
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34349509"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---globalprotect"></a>Samouczek: Azure Active Directory integracji z sieciami Palo Alto - GlobalProtect

Z tego samouczka dowiesz się Integrowanie sieci Palo Alto - GlobalProtect w usłudze Azure Active Directory (Azure AD).

Integracja sieci Palo Alto - GlobalProtect z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do sieci Palo Alto - GlobalProtect.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do sieci Palo Alto - GlobalProtect (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z sieciami Palo Alto - GlobalProtect, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Sieci Palo Alto — GlobalProtect logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie sieci Palo Alto - GlobalProtect z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-palo-alto-networks---globalprotect-from-the-gallery"></a>Dodawanie sieci Palo Alto - GlobalProtect z galerii
Aby skonfigurować integrację sieci Palo Alto - GlobalProtect do usługi Azure AD, należy dodać Palo Alto Networks - GlobalProtect z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Palo Alto Networks - GlobalProtect z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Palo Alto Networks - GlobalProtect**, wybierz pozycję **Palo Alto Networks - GlobalProtect** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację .

    ![Sieci Palo Alto - GlobalProtect na liście wyników](./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z sieciami Palo Alto — GlobalProtect w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w sieciach Palo Alto - GlobalProtect jest dla użytkownika, w usłudze Azure AD. Innymi słowy łącze relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w sieciach Palo Alto - GlobalProtect musi się.

W sieciach Palo Alto - GlobalProtect, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z sieciami Palo Alto - GlobalProtect, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie sieci Palo Alto - użytkownika testowego GlobalProtect](#create-a-palo-alto-networks---globalprotect-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Palo Alto Networks - GlobalProtect połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w sieci Palo Alto - GlobalProtect aplikacji.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z sieciami Palo Alto - GlobalProtect, wykonaj następujące czynności:**

1. W portalu Azure na **Palo Alto Networks - GlobalProtect** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_samlbase.png)

3. Na **Palo Alto Networks - GlobalProtect domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Sieci Palo Alto - GlobalProtect domeny i adresy URL pojedynczego logowania jednokrotnego informacji](./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<Customer Firewall URL>`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<Customer Firewall URL>/SAML20/SP`

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL logowania i identyfikator, należy zaktualizować te wartości. Skontaktuj się z [Palo Alto Networks - zespołem pomocy technicznej klienta GlobalProtect](https://support.paloaltonetworks.com/support) uzyskać te wartości. 
 
4. Sieci Palo Alto - GlobalProtect aplikacji oczekuje potwierdzenia języka SAML w określonym formacie. Skonfiguruj następujące oświadczeń dla tej aplikacji. Możesz zarządzać wartości tych atrybutów z "**atrybuty użytkownika**" sekcji na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład tego.
    
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_attribute.png)
    
5. W **atrybuty użytkownika** sekcji na **logowanie jednokrotne** okna dialogowego, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej i wykonaj następujące czynności:
    
    | Nazwa atrybutu | Wartość atrybutu |
    | --- | --- |    
    | nazwa użytkownika | user.userprincipalname |

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_attribute_04.png)

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_attribute_05.png)
    
    b. W **nazwa** tekstowym, wpisz nazwę atrybut wyświetlany dla danego wiersza.
    
    c. Z **wartość** listy, wpisz wartość atrybutu wyświetlany dla danego wiersza. Obecnie wartości z user.userprincipalname, na przykład, ale możesz mapować z odpowiednią wartość. 
    
    d. Kliknij przycisk **Ok**


6. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_certificate.png) 

7. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_general_400.png)

8. Otwórz interfejs użytkownika administratora Palo Alto sieci zapory jako administrator w innym oknie przeglądarki.

9. Polecenie **urządzenia**.

    ![Skonfiguruj Palo Alto rejestracji jednokrotnej](./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin1.png)

10. Wybierz **dostawca tożsamości SAML** z nawigacji po lewej stronie paska, a następnie kliknij przycisk Importuj, aby zaimportować plik metadanych.

    ![Skonfiguruj Palo Alto rejestracji jednokrotnej](./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin2.png)

11. Wykonaj następujące akcje w oknie importu

    ![Skonfiguruj Palo Alto rejestracji jednokrotnej](./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. W **nazwa profilu** pole tekstowe, podaj nazwę np. Azure AD GlobalProtect.
    
    b. W **metadanych dostawcy tożsamości**, kliknij przycisk **Przeglądaj** i wybierz plik metadata.xml, który został pobrany z portalu Azure
    
    c. Kliknij przycisk **OK**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/active-directory-saas-paloaltoglobalprotect-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/active-directory-saas-paloaltoglobalprotect-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/active-directory-saas-paloaltoglobalprotect-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/active-directory-saas-paloaltoglobalprotect-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-palo-alto-networks---globalprotect-test-user"></a>Tworzenie sieci Palo Alto - GlobalProtect użytkownika testowego

Sieci Palo Alto - GlobalProtect obsługuje Just in time Inicjowanie obsługi użytkowników dlatego użytkownik zostanie automatycznie utworzona w systemie po pomyślnym uwierzytelnieniu Jeśli już nie istnieje. Nie trzeba wykonywać żadnych czynności w tym miejscu. 

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do sieci Palo Alto - GlobalProtect.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta Palo Alto Networks - GlobalProtect, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Palo Alto Networks - GlobalProtect**.

    ![Sieci Palo Alto - GlobalProtect łącza na liście aplikacji](./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu Palo Alto Networks - GlobalProtect kafelka w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane z sieciami Palo Alto - GlobalProtect aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_general_203.png

