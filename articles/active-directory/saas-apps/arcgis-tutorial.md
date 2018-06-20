---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługą ArcGIS Online | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i ArcGIS Online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a9e132a4-29e7-48bf-beb9-4148e617c8a9
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/13/2017
ms.author: jeedes
ms.openlocfilehash: 2b3fe6efc96b1cd65db42bab947a7499b3c97c8e
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36228350"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-online"></a>Samouczek: Integracja usługi Azure Active Directory z usługą ArcGIS Online

Z tego samouczka dowiesz się integrowanie ArcGIS Online z usługą Azure Active Directory (Azure AD).

Integrowanie ArcGIS Online z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do ArcGIS w trybie Online.
- Umożliwia użytkownikom automatycznie pobrać zalogowane ArcGIS online (logowanie jednokrotne) z konta usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z ArcGIS Online, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- ArcGIS Online logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie ArcGIS Online z poziomu galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-arcgis-online-from-the-gallery"></a>Dodawanie ArcGIS Online z poziomu galerii
Aby skonfigurować integrację ArcGIS online z usługą Azure AD, należy dodać do listy zarządzanych aplikacji SaaS ArcGIS Online z poziomu galerii.

**Aby dodać ArcGIS Online z poziomu galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **ArcGIS Online**, wybierz pozycję **ArcGIS Online** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![ArcGIS Online na liście wyników](./media/arcgis-tutorial/tutorial_arcgisonline_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z ArcGIS Online na podstawie użytkownika testowego, nazywany "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w ArcGIS w trybie Online jest dla użytkownika, w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i danemu użytkownikowi w trybie Online ArcGIS musi określone.

W ArcGIS w trybie Online, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z ArcGIS Online, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego ArcGIS Online](#create-a-arcgis-online-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta ArcGIS Online, które jest połączone z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w trybie Online ArcGIS aplikacji.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z ArcGIS Online, wykonaj następujące czynności:**

1. W portalu Azure na **ArcGIS Online** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/arcgis-tutorial/tutorial_arcgisonline_samlbase.png)

3. Na **ArcGIS Online domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Adresy URL i domen ArcGIS pojedynczy informacje logowania jednokrotnego](./media/arcgis-tutorial/tutorial_arcgisonline_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<companyname>.maps.arcgis.com`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `<companyname>.maps.arcgis.com`

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL logowania i identyfikator, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej Online klienta ArcGIS](http://support.esri.com/en/) uzyskać te wartości. 
 


4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/arcgis-tutorial/tutorial_arcgisonline_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/arcgis-tutorial/tutorial_general_400.png)

6. W oknie przeglądarki innej witryny sieci web Zaloguj się do witryny firmy ArcGIS jako administrator.

7. Kliknij przycisk **Edytuj ustawienia**.

    ![Edytuj ustawienia](./media/arcgis-tutorial/ic784742.png "edytować ustawienia")

8. Kliknij przycisk **zabezpieczeń**.

    ![Zabezpieczenia](./media/arcgis-tutorial/ic784743.png "zabezpieczeń")

9. W obszarze **logowania do organizacji**, kliknij przycisk **USTAWIĆ dostawcy tożsamości**.

    ![Logowania do organizacji](./media/arcgis-tutorial/ic784744.png "logowania do organizacji")

10. Na **ustawić dostawcy tożsamości** konfiguracji wykonaj następujące czynności:
   
    ![Ustawienie dostawcy tożsamości](./media/arcgis-tutorial/ic784745.png "ustawienie dostawcy tożsamości")
   
    a. W **nazwa** tekstowym, wpisz nazwę organizacji.

    b. Aby uzyskać **metadanych dla dostawcy tożsamości organizacji dostarczane za pomocą**, wybierz pozycję **plik**.

    c. Aby przekazać plik metadanych pobranych, kliknij przycisk **wybierz plik**.

    d. Kliknij przycisk **dostawcy tożsamości zestawu**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/arcgis-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/arcgis-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/arcgis-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/arcgis-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-arcgis-online-test-user"></a>Tworzenie użytkownika testowego ArcGIS Online

Aby włączyć użytkowników usługi Azure AD zalogować się do ArcGIS Online, muszą mieć przydzielone do ArcGIS Online.  
W przypadku ArcGIS Online Inicjowanie obsługi to zadanie ręczne.

**Aby udostępnić konta użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do Twojego **ArcGIS** dzierżawy.

2. Kliknij przycisk **członków zaproszenia**.
   
    ![Zaprosić](./media/arcgis-tutorial/ic784747.png "zaprosić")

3. Wybierz **automatycznie dodawać członków bez wysyłania wiadomości e-mail**, a następnie kliknij przycisk **dalej**.
   
    ![Automatyczne dodawanie członków](./media/arcgis-tutorial/ic784748.png "automatycznie dodawać członków")

4. Na **członków** okna dialogowego strony, należy wykonać następujące czynności:
   
     ![Dodaj i przejrzyj](./media/arcgis-tutorial/ic784749.png "Dodaj i przejrzyj")
    
     a. Wprowadź **E-mail**, **imię**, i **nazwisko** chcesz udostępnić poprawnego konta usługi AAD.
  
     b. Kliknij przycisk **Dodaj i przejrzyj**.
5. Przejrzyj dane zostały wprowadzone, a następnie kliknij przycisk **Dodaj członków**.
   
    ![Dodaj element członkowski](./media/arcgis-tutorial/ic784750.png "Dodaj element członkowski")
        
    > [!NOTE]
    > Właścicielem konta usługi Azure Active Directory otrzymasz wiadomość e-mail, a następnie kliknij łącze, aby potwierdzić swoje konto, zanim staje się aktywny.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do ArcGIS Online.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta ArcGIS Online, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **ArcGIS Online**.

    ![Łącze ArcGIS Online na liście aplikacji](./media/arcgis-tutorial/tutorial_arcgisonline_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka ArcGIS Online w panelu dostępu należy należy pobrać automatycznie zalogowane do aplikacji ArcGIS w trybie Online.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/arcgis-tutorial/tutorial_general_01.png
[2]: ./media/arcgis-tutorial/tutorial_general_02.png
[3]: ./media/arcgis-tutorial/tutorial_general_03.png
[4]: ./media/arcgis-tutorial/tutorial_general_04.png

[100]: ./media/arcgis-tutorial/tutorial_general_100.png

[200]: ./media/arcgis-tutorial/tutorial_general_200.png
[201]: ./media/arcgis-tutorial/tutorial_general_201.png
[202]: ./media/arcgis-tutorial/tutorial_general_202.png
[203]: ./media/arcgis-tutorial/tutorial_general_203.png

