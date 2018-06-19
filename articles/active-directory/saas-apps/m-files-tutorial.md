---
title: 'Samouczek: Integracji Azure Active Directory z plikami M | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i pliki M.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 4536fd49-3a65-4cff-9620-860904f726d0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: 72a04df6de4294dbfcba12ec28775c78c690ba47
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35941049"
---
# <a name="tutorial-azure-active-directory-integration-with-m-files"></a>Samouczek: Integracji Azure Active Directory z plikami M

Z tego samouczka dowiesz się integrowanie M plików w usłudze Azure Active Directory (Azure AD).

Integrowanie M pliki z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do plików M
- Umożliwia użytkownikom automatycznie pobrać zalogowane M — pliki (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z plikami M, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Pliki M logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Trwa dodawanie plików M z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-m-files-from-the-gallery"></a>Trwa dodawanie plików M z galerii
Aby skonfigurować integrację usługi Azure AD M plików, należy dodać pliki M z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać pliki M z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **pliki M**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/m-files-tutorial/tutorial_m-files_search.png)

5. W panelu wyników wybierz **M pliki**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/m-files-tutorial/tutorial_m-files_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z plikami M, w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w plikach M jest dla użytkownika, w usłudze Azure AD. Innymi słowy musi można ustanowić łącze relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w plikach M.

W plikach M, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z plikami M, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego pliki M](#creating-a-m-files-test-user)**  — aby odpowiednikiem Britta Simona w plikach M jest połączony z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji M plików.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z plikami M, wykonaj następujące czynności:**

1. W portalu Azure na **M pliki** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/m-files-tutorial/tutorial_m-files_samlbase.png)

3. Na **adresy URL i pliki M domeny** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/m-files-tutorial/tutorial_m-files_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<tenantname>.cloudvault.m-files.com/authentication/MFiles.AuthenticationProviders.Core/sso`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<tenantname>.cloudvault.m-files.com`

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL logowania i identyfikator, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej klienta pliki M](mailto:support@m-files.com) uzyskać te wartości. 
 
4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/m-files-tutorial/tutorial_m-files_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/m-files-tutorial/tutorial_general_400.png)

6. Aby uzyskać logowania jednokrotnego skonfigurowane dla aplikacji, skontaktuj się z [zespołu obsługi plików M](mailto:support@m-files.com) i podaj metadanych pobranych.
   
    >[!NOTE]
    >Wykonaj poniższe czynności, jeśli chcesz skonfigurować logowanie Jednokrotne dla Ciebie M pliku aplikacji komputerowych. Żadne dodatkowe czynności nie są wymagane, jeśli chcesz skonfigurować logowania jednokrotnego dla wersji web M plików.  

7. Wykonaj poniższe czynności, aby skonfigurować aplikację pulpitu pliku M do włączenia funkcji logowania jednokrotnego w usłudze Azure AD. Aby pobrać pliki M, przejdź do [pobrać pliki M](https://www.m-files.com/en/download-latest-version) strony.

8. Otwórz **ustawienia pulpitu pliki M** okna. Następnie kliknij przycisk **Dodaj**.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/m-files-tutorial/tutorial_m_files_10.png)

9. Na **właściwości połączenia magazynu dokumentu** okna, wykonaj następujące czynności:
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/m-files-tutorial/tutorial_m_files_11.png)  

    W obszarze serwera, sekcja typu wartości w następujący sposób:  

    a. Aby uzyskać **nazwa**, typ `<tenant-name>.cloudvault.m-files.com`. 
 
    b. Aby uzyskać **numer portu**, typ **4466**. 

    c. Aby uzyskać **protokołu**, wybierz pozycję **HTTPS**. 

    d. W **uwierzytelniania** pól, zaznacz **określonego użytkownika systemu Windows**. Następnie zostanie wyświetlony monit o strony podpisywania. Wstaw poświadczeń usługi Azure AD. 

    e. Aby uzyskać **magazynu na serwerze**, wybierz odpowiedni magazyn na serwerze.
 
    f. Kliknij przycisk **OK**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/m-files-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/m-files-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/m-files-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/m-files-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-m-files-test-user"></a>Tworzenie użytkownika testowego M — pliki

Celem tej sekcji jest utworzenie użytkownika o nazwie Simona Britta w plikach M. Praca z [zespołu obsługi plików M](mailto:support@m-files.com) Aby dodać użytkowników w plikach M.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji musisz włączyć Simona Britta do użycia usługi Azure logowania jednokrotnego za udzielanie dostępu do plików M.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta M pliki, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **pliki M**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/m-files-tutorial/tutorial_m-files_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

Celem tej sekcji służy do testowania konfigurację usługi Azure AD z logowania jednokrotnego za pomocą panelu dostępu.

Po kliknięciu kafelka M pliki w panelu dostępu należy należy pobrać automatycznie zalogowane M pliki aplikacji.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/m-files-tutorial/tutorial_general_01.png
[2]: ./media/m-files-tutorial/tutorial_general_02.png
[3]: ./media/m-files-tutorial/tutorial_general_03.png
[4]: ./media/m-files-tutorial/tutorial_general_04.png

[100]: ./media/m-files-tutorial/tutorial_general_100.png

[200]: ./media/m-files-tutorial/tutorial_general_200.png
[201]: ./media/m-files-tutorial/tutorial_general_201.png
[202]: ./media/m-files-tutorial/tutorial_general_202.png
[203]: ./media/m-files-tutorial/tutorial_general_203.png

