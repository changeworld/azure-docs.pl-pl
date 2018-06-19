---
title: 'Samouczek: Integracji Azure Active Directory z Cisco Spark | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Cisco Spark.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: 634fdc22bd4fa6b35fd20a0af4adc23865068106
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35942807"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-spark"></a>Samouczek: Integracji Azure Active Directory z Cisco Spark

Z tego samouczka dowiesz się integrowanie Cisco Spark w usłudze Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Cisco Spark zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Cisco Spark
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Cisco Spark (logowanie jednokrotne) z konta usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Cisco Spark, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Cisco Spark logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Cisco Spark z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-cisco-spark-from-the-gallery"></a>Dodawanie Cisco Spark z galerii
Aby skonfigurować integrację Cisco Spark w usłudze Azure Active Directory, należy dodać Cisco Spark z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Cisco Spark z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Cisco Spark**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/cisco-spark-tutorial/tutorial_ciscospark_search.png)

5. W panelu wyników wybierz **Cisco Spark**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/cisco-spark-tutorial/tutorial_ciscospark_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Cisco Spark oparty na koncie użytkownika testu o nazwie "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Cisco Spark jest dla użytkownika, w usłudze Azure AD. Innymi słowy musi można ustanowić łącze relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w łączniku Spark Cisco.

W łączniku Spark Cisco, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Cisco Spark, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Cisco Spark](#creating-a-cisco-spark-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Spark Cisco, połączonej z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne do aplikacji Cisco Spark.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Cisco Spark, wykonaj następujące czynności:**

1. W portalu Azure na **Cisco Spark** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/cisco-spark-tutorial/tutorial_ciscospark_samlbase.png)

3. Na **Cisco Spark domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/cisco-spark-tutorial/tutorial_ciscospark_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL jako: `https://web.ciscospark.com/#/signin`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://idbroker.webex.com/<companyname>`

    > [!NOTE] 
    > Ta wartość nie jest prawdziwe. Zaktualizuj tę wartość z rzeczywistego identyfikatora. Skontaktuj się z [zespołem pomocy technicznej klienta Spark Cisco](https://support.ciscospark.com/) aby zyskać tę wartość. 
 
4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/cisco-spark-tutorial/tutorial_ciscospark_certificate.png) 

5. Aplikacji Spark Cisco oczekuje potwierdzenia SAML w celu uwzględnienia określonych atrybutów. Skonfiguruj następujące atrybuty dla tej aplikacji. Możesz zarządzać wartości tych atrybutów z **atrybuty użytkownika** sekcji na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład tego.
    
    ![Konfigurowanie rejestracji jednokrotnej](./media/cisco-spark-tutorial/tutorial_ciscospark_07.png) 

6. W **atrybuty użytkownika** sekcji na **logowanie jednokrotne** okna dialogowego, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej i wykonaj następujące czynności:
    
    | Nazwa atrybutu  | Wartość atrybutu |
    | --------------- | -------------------- |    
    |   Identyfikator UID    | user.userprincipalname |   

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie rejestracji jednokrotnej](./media/cisco-spark-tutorial/tutorial_attribute_04.png)

    ![Konfigurowanie rejestracji jednokrotnej](./media/cisco-spark-tutorial/tutorial_attribute_05.png)
    
    b. W **nazwa** tekstowym, wpisz nazwę atrybut wyświetlany dla danego wiersza.
    
    c. Z **wartość** listy, wpisz wartość atrybutu wyświetlany dla danego wiersza.
    
    d. Kliknij przycisk **OK**.

7. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/cisco-spark-tutorial/tutorial_general_400.png)

8. Zaloguj się do [zarządzania współpracy chmury Cisco](https://admin.ciscospark.com/) przy użyciu poświadczeń administratora pełnego.

9. Wybierz **ustawienia** i w obszarze **uwierzytelniania** kliknij **Modyfikuj**.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/cisco-spark-tutorial/tutorial_cisco_spark_10.png)
    
10. Wybierz **dostawcy tożsamości firm 3rd integracji. (Zaawansowane)**  i przejdź do następnego ekranu.

11. Na **Importowanie metadanych Idp** strony albo przeciągania i upuszczania pliku metadanych usługi Azure AD na stronę lub użyj opcji przeglądarki plików do lokalizowania i przekazać plik metadanych usługi Azure AD. Następnie wybierz opcję **wymagają certyfikatu podpisanego przez urząd certyfikacji w metadanych (bardziej bezpieczny)** i kliknij przycisk **dalej**. 
    
    ![Konfigurowanie rejestracji jednokrotnej](./media/cisco-spark-tutorial/tutorial_cisco_spark_11.png)

12. Wybierz **Test rejestracji Jednokrotnej połączenia**i po otwarciu nowej karty przeglądarki uwierzytelniania za pomocą usługi Azure AD logując się.

13. Wróć do **zarządzania współpracy chmury Cisco** karty przeglądarki. Jeśli test zakończyło się pomyślnie, wybierz **ten test zakończyła się pomyślnie. Włącz opcję logowania jednokrotnego** i kliknij przycisk **dalej**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/cisco-spark-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/cisco-spark-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/cisco-spark-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/cisco-spark-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-cisco-spark-test-user"></a>Tworzenie użytkownika testowego Cisco Spark

W tej sekcji należy utworzyć użytkownika o nazwie Simona Britta w Cisco Spark. W tej sekcji należy utworzyć użytkownika o nazwie Simona Britta w Cisco Spark.

1. Przejdź do [zarządzania współpracy chmury Cisco](https://admin.ciscospark.com/) przy użyciu poświadczeń administratora pełnego.

2. Kliknij przycisk **użytkowników** , a następnie **Zarządzanie użytkownikami**.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/cisco-spark-tutorial/tutorial_cisco_spark_12.png) 

3. W **zarządzania użytkownika** wybierz **ręcznego dodawania lub modyfikowania użytkowników** i kliknij przycisk **dalej**.

4. Wybierz **nazwy i adresu E-mail**. Następnie należy wypełnić pole tekstowe w następujący sposób:
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/cisco-spark-tutorial/tutorial_cisco_spark_13.png) 
    
    a. W **imię** pole tekstowe, typ **Britta**. 
    
    b. W **nazwisko** pole tekstowe, typ **Simona**.
    
    c. W **adres E-mail** pole tekstowe, typ **britta.simon@contoso.com**.

5. Kliknij znak plus, aby dodać Simona Britta. Następnie kliknij przycisk **Dalej**.

6. W **Dodaj usługi dla użytkowników** okna, kliknij przycisk **zapisać** , a następnie **Zakończ**.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Cisco Spark.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta Cisco Spark, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Cisco Spark**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/cisco-spark-tutorial/tutorial_ciscospark_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

Celem tej sekcji służy do testowania konfigurację usługi Azure AD z logowania jednokrotnego za pomocą panelu dostępu.

Po kliknięciu kafelka Cisco Spark w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji Cisco Spark.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

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

