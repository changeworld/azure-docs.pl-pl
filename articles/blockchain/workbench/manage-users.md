---
title: Zarządzanie użytkownikami w usłudze Azure Blockchain Workbench
description: Jak zarządzać użytkownikami w usłudze Azure Blockchain Workbench.
ms.date: 05/09/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 573ec477a3e75beb91f90da0545fb7d4c0f9bf39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252183"
---
# <a name="manage-users-in-azure-blockchain-workbench"></a>Zarządzanie użytkownikami w usłudze Azure Blockchain Workbench

Azure Blockchain Workbench obejmuje zarządzanie użytkownikami dla osób i organizacji, które są częścią konsorcjum.

## <a name="prerequisites"></a>Wymagania wstępne

Wymagane jest wdrożenie blockchain workbench. Zobacz [wdrożenie programu Azure Blockchain Workbench,](deploy.md) aby uzyskać szczegółowe informacje na temat wdrażania.

## <a name="add-azure-ad-users"></a>Dodawanie użytkowników usługi Azure AD

Usługa Azure Blockchain Workbench używa usługi Azure Active Directory (Azure AD) do uwierzytelniania, kontroli dostępu i ról. Użytkownicy dzierżawy usługi Azure AD w usłudze Blockchain Workbench mogą uwierzytelniać się i używać programu Blockchain Workbench. Dodaj użytkowników do roli aplikacji Administrator, aby wchodzili w interakcje i wykonywać akcje.

Użytkownicy workbench łańcucha bloków muszą istnieć w dzierżawie usługi Azure AD, zanim będzie można przypisać je do aplikacji i ról. Aby dodać użytkowników do usługi Azure AD, należy wykonać następujące kroki:

1.  Zaloguj się do [Portalu Azure](https://portal.azure.com).
2.  Wybierz swoje konto w prawym górnym rogu i przełącz się do dzierżawy usługi Azure AD skojarzonej z blockchain Workbench.
3.  Wybierz **pozycję Azure Active Directory > użytkowników**. W katalogu jest widoczna lista użytkowników.
4.  Aby dodać użytkowników do katalogu, wybierz pozycję **Nowy użytkownik**. W przypadku użytkowników zewnętrznych wybierz pozycję **Nowy użytkownik-gość**.

    ![Nowy użytkownik](./media/manage-users/add-ad-user.png)

5.  Wypełnij wymagane pola dla nowego użytkownika. Wybierz **pozycję Utwórz**.

Odwiedź dokumentację [usługi Azure AD,](../../active-directory/fundamentals/add-users-azure-active-directory.md) aby uzyskać więcej informacji na temat zarządzania użytkownikami w usłudze Azure AD.

## <a name="manage-blockchain-workbench-administrators"></a>Zarządzanie administratorami blockchain workbench

Po dodaniu użytkowników do katalogu następnym krokiem jest wybranie użytkowników, którzy są administratorami Blockchain Workbench. Użytkownicy w grupie **Administrator** są skojarzeni z **rolą aplikacji Administrator** w blockchain workbench. Administratorzy mogą dodawać lub usuwać użytkowników, przypisywać użytkowników do określonych scenariuszy i tworzyć nowe aplikacje.

Aby dodać użytkowników do grupy **Administrator** w katalogu usługi Azure AD:

1.  Zaloguj się do [Portalu Azure](https://portal.azure.com).
2.  Sprawdź, czy znajdujesz się w dzierżawie usługi Azure AD skojarzonej z blockchain Workbench, wybierając swoje konto w prawym górnym rogu.
3.  Wybierz **pozycję Azure Active Directory > aplikacje enterprise**.
4.  Wybieranie aplikacji klienta usługi Azure AD dla workbenchu blockchain
    
    ![Wszystkie rejestracje aplikacji dla przedsiębiorstw](./media/manage-users/select-blockchain-client-app.png)

5.  Wybierz **pozycję Użytkownicy i grupy > Dodaj użytkownika**.
6.  W okienku **Dodawanie przypisania** wybierz pozycję **Użytkownicy**. Wybierz lub wyszukaj użytkownika, którego chcesz dodać jako administratora. Po zakończeniu wyboru **kliknij pozycję Wybierz.**

    ![Dodawanie przydziału](./media/manage-users/add-user-assignment.png)

9.  Sprawdź, **czy rola** jest ustawiona na **Administratora**
10. Wybierz opcję **Przypisz**. Dodani użytkownicy są wyświetlani na liście z przypisaną rolą administratora.

    ![Użytkownicy aplikacji klienckich blockchain](./media/manage-users/blockchain-admin-list.png)

## <a name="managing-blockchain-workbench-members"></a>Zarządzanie członkami Blockchain Workbench

Aplikacja Blockchain Workbench służy do zarządzania użytkownikami i organizacjami należącymi do konsorcjum. Można dodawać lub usuwać użytkowników do aplikacji i ról.

1. [Otwórz blockchain workbench](deploy.md#blockchain-workbench-web-url) w przeglądarce i zaloguj się jako administrator.

    ![Blockchain Workbench](./media/manage-users/blockchain-workbench-applications.png)

    Członkowie są dodawane do każdej aplikacji. Członkowie mogą mieć jedną lub więcej ról aplikacji do inicjowania kontraktów lub podejmowania działań.

2. Aby zarządzać elementami członkowskich aplikacji, wybierz kafelek aplikacji w okienku **Aplikacje.**

    Liczba elementów członkowskich skojarzonych z wybraną aplikacją jest odzwierciedlana w kafelku członków.

    ![Wybierz aplikację](./media/manage-users/blockchain-workbench-select-application.png)


#### <a name="add-member-to-application"></a>Dodawanie członka do aplikacji

1. Wybierz kafelek elementu członkowskiego, aby wyświetlić listę bieżących członków.
2. Wybierz **pozycję Dodaj członków**.

    ![Dodawanie członków](./media/manage-users/application-add-members.png)

3. Wyszukaj nazwę użytkownika.  Na liście znajdują się tylko użytkownicy usługi Azure AD, którzy istnieją w dzierżawie usługi Blockchain Workbench. Jeśli użytkownik nie zostanie znaleziony, należy [dodać użytkowników usługi Azure AD](#add-azure-ad-users).

    ![Dodawanie członków](./media/manage-users/find-user.png)

4. Wybierz **rolę** z listy rozwijanej.

    ![Wybieranie członków roli](./media/manage-users/application-select-role.png)

5. Wybierz pozycję **Dodaj**, aby dodać członka ze skojarzoną rolą do aplikacji.

#### <a name="remove-member-from-application"></a>Usuwanie członka z aplikacji

1. Wybierz kafelek elementu członkowskiego, aby wyświetlić listę bieżących członków.
2. Dla użytkownika, którego chcesz usunąć, wybierz pozycję **Usuń** z listy rozwijanej roli.

    ![Usuwanie członka](./media/manage-users/application-remove-member.png)

#### <a name="change-or-add-role"></a>Zmienianie lub dodawanie roli

1. Wybierz kafelek elementu członkowskiego, aby wyświetlić listę bieżących członków.
2. Dla użytkownika, którego chcesz zmienić, kliknij pozycję rozwijaną i wybierz nową rolę.

    ![Zmiana roli](./media/manage-users/application-change-role.png)

## <a name="next-steps"></a>Następne kroki

W tym artykule instrukcję dowiesz się, jak zarządzać użytkownikami dla usługi Azure Blockchain Workbench. Aby dowiedzieć się, jak utworzyć aplikację blockchain, przejdź do następnego artykułu in jak to zrobić.

> [!div class="nextstepaction"]
> [Tworzenie aplikacji łańcucha bloków w usłudze Azure Blockchain Workbench](create-app.md)
