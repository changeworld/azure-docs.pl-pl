---
title: 'Samouczek: Azure Active Directory integracji z sieciami Palo Alto — wewnętrzne portalu | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i sieciami Palo Alto — wewnętrzne portalu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 67a0b476-2305-4157-8658-2ec3625850d5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jeedes
ms.openlocfilehash: 92e3acff68cba0874f0efc2942a4753f5fab3608
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35940455"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---captive-portal"></a>Samouczek: Azure Active Directory integracji z sieciami Palo Alto — wewnętrzne portalu

Z tego samouczka dowiesz się Integrowanie sieci Palo Alto — wewnętrzne portalu w usłudze Azure Active Directory (Azure AD).

Integracja sieci Palo Alto — wewnętrzne portalu z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do sieci Palo Alto — wewnętrzne portalu.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do sieci Palo Alto — wewnętrzne Portal (logowanie jednokrotne) za pomocą ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z sieciami Palo Alto — wewnętrzne portalu potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Sieci Palo Alto — wewnętrzne portalu rejestracji jednokrotnej włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie sieci Palo Alto — wewnętrzne Portal z poziomu galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-palo-alto-networks---captive-portal-from-the-gallery"></a>Dodawanie sieci Palo Alto — wewnętrzne Portal z poziomu galerii
Aby skonfigurować integrację sieci Palo Alto — wewnętrzne portalu do usługi Azure AD, należy dodać Palo Alto Networks — wewnętrzne Portal z poziomu galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Palo Alto Networks — wewnętrzne Portal z poziomu galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Palo Alto Networks — wewnętrzne portalu**, wybierz pozycję **Palo Alto Networks — wewnętrzne portalu** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację .

    ![Sieci Palo Alto — wewnętrzne portalu na liście wyników](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltocaptiveportal_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z sieciami Palo Alto — wewnętrzne portalu w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w sieciach Palo Alto — wewnętrzne portalu jest dla użytkownika, w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i danemu użytkownikowi w sieciach Palo Alto — wewnętrzne portalu musi określone.

W sieciach Palo Alto — wewnętrzne portalu, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Do konfigurowania i testowania usługi Azure AD rejestracji jednokrotnej z sieciami Palo Alto — wewnętrzne portalu, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie sieci Palo Alto — wewnętrzne portalu użytkownika testowego](#create-a-palo-alto-networks---captive-portal-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Palo Alto Networks — wewnętrzne Portal, który jest połączony z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w sieci Palo Alto — wewnętrzne portalu aplikacji.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z sieciami Palo Alto — wewnętrzne portalu, wykonaj następujące czynności:**

1. W portalu Azure na **Palo Alto Networks — wewnętrzne portalu** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltocaptiveportal_samlbase.png)

3. Na **Palo Alto sieci — wewnętrzne domeny portalu i adresy URL** sekcji, wykonaj następujące czynności:

    ![Sieci Palo Alto — wewnętrzne domeny portalu i adresów URL jednym logowania jednokrotnego informacji](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltocaptiveportal_url.png)

    a. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<Customer Firewall Hostname>/SAML20/SP`

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<Customer Firewall Hostname>/SAML20/SP/ACS`

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty identyfikator i adres URL odpowiedzi, należy zaktualizować te wartości. Skontaktuj się z [Palo Alto Networks - zespołem pomocy technicznej wewnętrzne portalu](https://support.paloaltonetworks.com/support) uzyskać te wartości.

4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltocaptiveportal_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_400.png)

6. Otwórz witrynę Palo Alto jako administrator w innym oknie przeglądarki.

7. Polecenie **urządzenia**.

    ![Skonfiguruj Palo Alto rejestracji jednokrotnej](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

8. Wybierz **dostawca tożsamości SAML** z nawigacji po lewej stronie paska, a następnie kliknij przycisk Importuj, aby zaimportować plik metadanych.

    ![Skonfiguruj Palo Alto rejestracji jednokrotnej](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

9. Wykonaj następujące akcje w oknie importu

    ![Skonfiguruj Palo Alto rejestracji jednokrotnej](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. W **nazwa profilu** pole tekstowe, podaj nazwę np. Azure AD administratora interfejsu użytkownika.
    
    b. W **metadanych dostawcy tożsamości**, kliknij przycisk **Przeglądaj** i wybierz plik metadata.xml, który został pobrany z portalu Azure
    
    c. Kliknij przycisk **OK**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/paloaltonetworks-captiveportal-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/paloaltonetworks-captiveportal-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/paloaltonetworks-captiveportal-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/paloaltonetworks-captiveportal-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
  
### <a name="create-a-palo-alto-networks---captive-portal-test-user"></a>Tworzenie sieci Palo Alto — wewnętrzne portalu użytkownika testowego

Celem tej sekcji jest utworzenie użytkownika o nazwie Simona Britta w sieciach Palo Alto — wewnętrzne portalu. Sieci Palo Alto — wewnętrzne Portal obsługuje w czasie inicjowania obsługi, który jest domyślnie włączone. Nie ma elementu akcji można w tej sekcji. Nowy użytkownik jest tworzony podczas próby uzyskania dostępu do sieci Palo Alto — wewnętrzne portalu, jeśli go jeszcze nie istnieje. 

> [!NOTE]
> Jeśli trzeba ręcznie utworzyć użytkownika, należy skontaktować się [Palo Alto Networks - zespołem pomocy technicznej wewnętrzne Portal](https://support.paloaltonetworks.com/support).

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do sieci Palo Alto — wewnętrzne portalu.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta Palo Alto Networks — wewnętrzne portalu, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Palo Alto Networks — wewnętrzne Portal**.

    ![Sieci Palo Alto - Portal wewnętrzne łącza na liście aplikacji](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltocaptiveportal_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Wewnętrzne portalu jest skonfigurowana za zaporą systemu Windows w maszynie wirtualnej.  Aby przetestować rejestracji jednokrotnej w portalu wewnętrzne, logowania za pomocą maszyny Wirtualnej systemu Windows za pomocą protokołu RDP. Z sesji protokołu RDP, otwórz przeglądarkę, aby każdej witrynie sieci web, powinno zostać automatycznie otwarte adres url logowania jednokrotnego i wiersza do uwierzytelniania. Po zakończeniu uwierzytelniania należy możliwość navgiate do witryn sieci web. 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_01.png
[2]: ./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_02.png
[3]: ./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_03.png
[4]: ./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_04.png

[100]: ./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_100.png

[200]: ./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_200.png
[201]: ./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_201.png
[202]: ./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_202.png
[203]: ./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_203.png

