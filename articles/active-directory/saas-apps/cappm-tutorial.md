---
title: 'Samouczek: Integracji Azure Active Directory z urzędu certyfikacji PPM | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i PPM urzędu certyfikacji.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: ca9d5e71-e429-4891-8d10-3498e7210e89
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2017
ms.author: jeedes
ms.openlocfilehash: ba9aabf88597bc97bedec0b4edeca313727f14be
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36214223"
---
# <a name="tutorial-azure-active-directory-integration-with-ca-ppm"></a>Samouczek: Integracji Azure Active Directory z PPM urzędu certyfikacji

Z tego samouczka dowiesz się integrowanie PPM urzędu certyfikacji w usłudze Azure Active Directory (Azure AD).

Integrowanie PPM urzędu certyfikacji z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do urzędu certyfikacji PPM
- Umożliwia użytkownikom automatycznie pobrać zalogowane do urzędu certyfikacji PPM (logowanie jednokrotne) z konta usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z urzędu certyfikacji PPM, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Urząd certyfikacji PPM jednokrotnego włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie PPM urzędu certyfikacji z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-ca-ppm-from-the-gallery"></a>Dodawanie PPM urzędu certyfikacji z galerii
Aby skonfigurować integrację usługi Azure AD PPM urzędu certyfikacji, należy dodać PPM urzędu certyfikacji z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać PPM urzędu certyfikacji z poziomu galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **PPM urzędu certyfikacji**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/cappm-tutorial/tutorial_cappm_search.png)

5. W panelu wyników wybierz **PPM urzędu certyfikacji**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/cappm-tutorial/tutorial_cappm_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z PPM urzędu certyfikacji, na podstawie użytkownika testowego, nazywany "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w PPM urzędu certyfikacji jest dla użytkownika, w usłudze Azure AD. Innymi słowy musi można ustanowić łącze relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w PPM urzędu certyfikacji.

W PPM urzędu certyfikacji, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z PPM urzędu certyfikacji, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego urzędu certyfikacji PPM](#creating-a-ca-ppm-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta PPM urzędu certyfikacji, połączonej z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne do aplikacji PPM urzędu certyfikacji.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z urzędu certyfikacji PPM, wykonaj następujące czynności:**

1. W portalu Azure na **PPM urzędu certyfikacji** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/cappm-tutorial/tutorial_cappm_samlbase.png)

3. Na **domeny PPM urzędu certyfikacji i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/cappm-tutorial/tutorial_cappm_url.png)

    a. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://ca.ondemand.saml.20.post.<companyname>`
    
    b. W **adres URL odpowiedzi** pole tekstowe, typu co: `https://fedsso.ondemand.ca.com/affwebservices/public/saml2assertionconsumer`

    > [!NOTE] 
    > Ta wartość nie jest prawdziwe. Zaktualizuj tę wartość z rzeczywistego identyfikatora. Skontaktuj się z [zespołem pomocy technicznej PPM urzędu certyfikacji](mailto:catechnicalsupport@ca.com) aby zyskać tę wartość.
 
4. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/cappm-tutorial/tutorial_cappm_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/cappm-tutorial/tutorial_general_400.png)

6. Na **konfiguracji urzędu certyfikacji PPM** , kliknij przycisk **PPM skonfigurować urząd certyfikacji** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki SAML** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/cappm-tutorial/tutorial_cappm_configure.png) 

7. Skonfigurować logowanie jednokrotne w **PPM urzędu certyfikacji** stronie, musisz wysłać pobrany **Certificate(Base64)** i **identyfikator jednostki SAML** do [zespołem pomocy technicznej PPM urzędu certyfikacji](mailto:catechnicalsupport@ca.com).

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/cappm-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/cappm-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/cappm-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/cappm-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-ca-ppm-test-user"></a>Tworzenie użytkownika testowego PPM urzędu certyfikacji

W tej sekcji należy utworzyć użytkownika o nazwie Simona Britta w PPM urzędu certyfikacji. Praca z [zespołem pomocy technicznej PPM urzędu certyfikacji](mailto:catechnicalsupport@ca.com) Aby dodać użytkowników do platformy PPM urzędu certyfikacji.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu PPM urzędu certyfikacji.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta PPM urzędu certyfikacji, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **PPM urzędu certyfikacji**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/cappm-tutorial/tutorial_cappm_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfigurację usługi Azure AD z logowania jednokrotnego za pomocą panelu dostępu.

Po kliknięciu kafelka PPM urzędu certyfikacji w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji PPM urzędu certyfikacji.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/cappm-tutorial/tutorial_general_01.png
[2]: ./media/cappm-tutorial/tutorial_general_02.png
[3]: ./media/cappm-tutorial/tutorial_general_03.png
[4]: ./media/cappm-tutorial/tutorial_general_04.png

[100]: ./media/cappm-tutorial/tutorial_general_100.png

[200]: ./media/cappm-tutorial/tutorial_general_200.png
[201]: ./media/cappm-tutorial/tutorial_general_201.png
[202]: ./media/cappm-tutorial/tutorial_general_202.png
[203]: ./media/cappm-tutorial/tutorial_general_203.png

