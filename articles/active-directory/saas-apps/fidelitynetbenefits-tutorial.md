---
title: 'Samouczek: Integracji Azure Active Directory z NetBenefits wierności | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i NetBenefits rozdzielczości.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 77dc8a98-c0e7-4129-ab88-28e7643e432a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2018
ms.author: jeedes
ms.openlocfilehash: c3959e6e33ef603edb51d8f0b29ee140d6bd01c7
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36223063"
---
# <a name="tutorial-azure-active-directory-integration-with-fidelity-netbenefits"></a>Samouczek: Integracji Azure Active Directory z NetBenefits wierności

Z tego samouczka dowiesz integrowanie NetBenefits wierności w usłudze Azure Active Directory (Azure AD).

Integracja z usługą Azure AD NetBenefits wierności zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do NetBenefits rozdzielczości.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do NetBenefits wierności (logowanie jednokrotne) z konta usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z NetBenefits wierności, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- NetBenefits wierności logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie NetBenefits wierności z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-fidelity-netbenefits-from-the-gallery"></a>Dodawanie NetBenefits wierności z galerii
Aby skonfigurować integrację usługi Azure AD NetBenefits wierności, należy dodać NetBenefits wierności z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać NetBenefits wierności z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **NetBenefits wierności**, wybierz pozycję **NetBenefits wierności** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![NetBenefits wierności na liście wyników](./media/fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z NetBenefits wierności w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w NetBenefits wierności jest dla użytkownika, w usłudze Azure AD. Innymi słowy łącze relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w NetBenefits wierności musi się.

W NetBenefits wierności **użytkownika** mapowania ma się odbywać z **użytkownika usługi Azure AD** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z NetBenefits wierności, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego wierności NetBenefits](#create-a-fidelity-netbenefits-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta NetBenefits wierności, połączonej z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji NetBenefits wierności.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z NetBenefits wierności, wykonaj następujące czynności:**

1. W portalu Azure na **NetBenefits wierności** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_samlbase.png)

3. Na **domeny NetBenefits wierności i adres URL** sekcji, wykonaj następujące czynności:

    ![Adresy URL i domeny NetBenefits wierności pojedynczy informacje logowania jednokrotnego](./media/fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_url.png)

    a. W **identyfikator** tekstowym, wpisz adres URL:

    Testowanie środowiska:  `urn:sp:fidelity:geninbndnbparts20:uat:xq1`

    W środowisku produkcyjnym:  `urn:sp:fidelity:geninbndnbparts20`

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL:

    Testowanie środowiska:  `https://loginxq1.fidelity.com/ftgw/Fas/NBExternal/NBPartSSO/InboundSSO/consumer/sp/ACS.saml2`

    W środowisku produkcyjnym:  `https://login.fidelity.com/ftgw/Fas/NBExternal/NBPartSSO/InboundSSO/consumer/sp/ACS.saml2`
 
4. Aplikacja NetBenefits wierności oczekuje potwierdzenia języka SAML w określonym formacie. Firma Microsoft zamapowaniu **identyfikator użytkownika** z **user.userprincipalname**. Możesz mapować to z **identyfikator pracownika** lub innych oświadczeń, która ma zastosowanie do organizacji jako **identyfikator użytkownika**. Poniższy zrzut ekranu przedstawia przykład tylko dla tego.

    ![Atrybut NetBenefits wierności](./media/fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_attribute.png)

    >[!Note]
    >NetBenefits wierności obsługuje statyczne i dynamiczne federacji. Statyczne oznacza, że nie będzie używać tylko w czasie użytkownika inicjowania obsługi administracyjnej i dynamicznych oznacza, że obsługuje tylko w czasie Inicjowanie obsługi użytkowników SAML. Dla przy użyciu JIT oparte na inicjowania obsługi administracyjnej klienci muszą dodać niektóre więcej oświadczenia w usłudze Azure AD, takie jak data urodzenia użytkownika itd. Informacje te są dostarczane przez [NetBenefits wierności obsługuje zespołu](mailto:SSOMaintenance@fmr.com) i konieczne jest włączenie tej Federacji dynamiczny dla wystąpienia.
    
4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/fidelitynetbenefits-tutorial/tutorial_general_400.png)

6. Na **wierności NetBenefits konfiguracji** kliknij **skonfigurować NetBenefits wierności** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Wierności NetBenefits konfiguracji](./media/fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_configure.png) 

7. Do konfigurowania rejestracji jednokrotnej na **NetBenefits wierności** stronie, musisz wysłać pobrany **XML metadanych**, **SAML pojedynczy znak na adres URL usługi** i  **Identyfikator jednostki SAML** do [NetBenefits wierności obsługuje zespołu](mailto:SSOMaintenance@fmr.com). To ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML one wartość.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/fidelitynetbenefits-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/fidelitynetbenefits-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/fidelitynetbenefits-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/fidelitynetbenefits-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
  
### <a name="create-a-fidelity-netbenefits-test-user"></a>Tworzenie użytkownika testowego NetBenefits wierności

W tej sekcji utworzysz użytkownika o nazwie Simona Britta w NetBenefits rozdzielczości. W przypadku tworzenia statycznego federacyjnych we współpracy z [NetBenefits wierności obsługuje zespołu](mailto:SSOMaintenance@fmr.com) może tworzyć użytkowników na platformie NetBenefits rozdzielczości. Tych użytkowników, należy utworzyć i aktywowana, aby użyć rejestracji jednokrotnej. 

Dla Federacji dynamiczne użytkowników są tworzone przy użyciu Inicjowanie obsługi użytkowników tylko w czasie. Dla przy użyciu JIT oparte na inicjowania obsługi administracyjnej klienci muszą dodać niektóre więcej oświadczenia w usłudze Azure AD, takie jak data urodzenia użytkownika itd. Informacje te są dostarczane przez [NetBenefits wierności obsługuje zespołu](mailto:SSOMaintenance@fmr.com) i konieczne jest włączenie tej Federacji dynamiczny dla wystąpienia.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu NetBenefits rozdzielczości.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta NetBenefits wierności, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **NetBenefits wierności**.

    ![Łącze NetBenefits wierności na liście aplikacji](./media/fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka NetBenefits wierności w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji NetBenefits wierności.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/fidelitynetbenefits-tutorial/tutorial_general_01.png
[2]: ./media/fidelitynetbenefits-tutorial/tutorial_general_02.png
[3]: ./media/fidelitynetbenefits-tutorial/tutorial_general_03.png
[4]: ./media/fidelitynetbenefits-tutorial/tutorial_general_04.png

[100]: ./media/fidelitynetbenefits-tutorial/tutorial_general_100.png

[200]: ./media/fidelitynetbenefits-tutorial/tutorial_general_200.png
[201]: ./media/fidelitynetbenefits-tutorial/tutorial_general_201.png
[202]: ./media/fidelitynetbenefits-tutorial/tutorial_general_202.png
[203]: ./media/fidelitynetbenefits-tutorial/tutorial_general_203.png

