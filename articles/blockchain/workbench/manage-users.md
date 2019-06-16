---
title: Zarządzanie użytkownikami w aplikacji Azure Blockchain Workbench
description: Jak zarządzać użytkownikami w aplikacji Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/09/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 15babefda36ba37cf6df7820ac888668e4a502be
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65509920"
---
# <a name="manage-users-in-azure-blockchain-workbench"></a>Zarządzanie użytkownikami w aplikacji Azure Blockchain Workbench

Azure Blockchain Workbench obejmuje zarządzanie użytkownikami w przypadku osób i organizacji, które są częścią sieci konsorcjum.

## <a name="prerequisites"></a>Wymagania wstępne

Wymagane jest wdrożenie aplikacji Blockchain Workbench. Zobacz [wdrażania aplikacji Azure Blockchain Workbench](deploy.md) szczegółowe informacje na temat wdrażania.

## <a name="add-azure-ad-users"></a>Dodaj użytkowników usługi Azure AD

W aplikacji Azure Blockchain Workbench używa usługi Azure Active Directory (Azure AD) do uwierzytelniania, kontroli dostępu i ról. Użytkowników w dzierżawie, Blockchain Workbench w usłudze Azure AD można uwierzytelnić i używać aplikacji Blockchain Workbench. Dodawanie użytkowników do roli administratora aplikacji do interakcji i wykonywania akcji.

Użytkownicy aplikacji Blockchain Workbench konieczne istnieje w dzierżawie usługi Azure AD, aby można było przypisać je do aplikacji i ról. Aby dodać użytkowników do usługi Azure AD, wykonaj następujące kroki:

1.  Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2.  Wybierz swoje konto w prawym górnym rogu, a następnie przełącz się do dzierżawy usługi Azure AD, powiązanych z aplikacji Blockchain Workbench.
3.  Wybierz **usługi Azure Active Directory > Użytkownicy**. Zobaczysz listę użytkowników w katalogu.
4.  Aby dodać użytkowników do katalogu, wybierz **nowego użytkownika**. Dla użytkowników zewnętrznych, wybierz **nowy użytkownik-Gość**.

    ![Nowy użytkownik](./media/manage-users/add-ad-user.png)

5.  Wypełnij wymagane pola dla nowego użytkownika. Wybierz pozycję **Utwórz**.

Odwiedź stronę [usługi Azure AD](../../active-directory/fundamentals/add-users-azure-active-directory.md) dokumentacji, aby uzyskać więcej informacji na temat sposobu zarządzania użytkownikami w usłudze Azure AD.

## <a name="manage-blockchain-workbench-administrators"></a>Zarządzanie administratorami Blockchain Workbench

Gdy użytkownicy zostali dodani do katalogu, następnym krokiem jest Wybierz użytkowników, którzy są administratorami Blockchain Workbench. Użytkownicy w **administratora** skojarzona z grupą **roli aplikacji Administrator** w aplikacji Blockchain Workbench. Administratorzy mogą dodać lub usunąć użytkowników, przypisywanie użytkowników do określonych scenariuszy i twórz nowe aplikacje.

Aby dodać użytkowników do **administratora** grupy w katalogu usługi Azure AD:

1.  Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2.  Sprawdź, czy znajdują się w dzierżawie usługi Azure AD, powiązanych z aplikacji Blockchain Workbench, wybierając swoje konto w prawym górnym rogu.
3.  Wybierz **usługi Azure Active Directory > aplikacje dla przedsiębiorstw**.
4.  Wybierz aplikację klienta usługi Azure AD dla aplikacji Blockchain Workbench
    
    ![Wszystkie rejestracje aplikacji przedsiębiorstwa](./media/manage-users/select-blockchain-client-app.png)

5.  Wybierz **użytkownicy i grupy > Dodaj użytkownika**.
6.  W **Dodaj przydziału**, wybierz opcję **użytkowników**. Wybierz lub Wyszukaj użytkownika, który ma zostać dodany jako administrator. Kliknij przycisk **wybierz** po zakończeniu wybierania.

    ![Dodawanie przypisania](./media/manage-users/add-user-assignment.png)

9.  Sprawdź **roli** ustawiono **administratora**
10. Wybierz opcję **Przypisz**. Dodani użytkownicy są wyświetlane na liście z przypisaną rolą administratora.

    ![Użytkownicy aplikacji klienta łańcucha bloków](./media/manage-users/blockchain-admin-list.png)

## <a name="managing-blockchain-workbench-members"></a>Zarządzanie członkami Blockchain Workbench

Do zarządzania użytkownikami i organizacji, które są częścią sieci konsorcjum za pomocą aplikacji Blockchain Workbench. Można dodawać lub usuwać użytkowników z aplikacji i ról.

1. [Otwieranie aplikacji Blockchain Workbench](deploy.md#blockchain-workbench-web-url) w przeglądarce i zaloguj się jako administrator.

    ![Blockchain Workbench](./media/manage-users/blockchain-workbench-applications.png)

    Elementy członkowskie są dodawane do każdej aplikacji. Członkowie mogą mieć jedną lub więcej ról aplikacji o rozpoczęciu kontraktów lub podjąć działania.

2. Aby zarządzać elementami członkowskimi do aplikacji, wybierz Kafelek aplikacji **aplikacje** okienka.

    Liczba członków skojarzonych do wybranej aplikacji jest widoczny na kafelku elementów członkowskich.

    ![Wybierz aplikację](./media/manage-users/blockchain-workbench-select-application.png)


#### <a name="add-member-to-application"></a>Dodaj członka do aplikacji

1. Wybierz Kafelek elementu członkowskiego, aby wyświetlić listę bieżących członków.
2. Wybierz **dodawać członków**.

    ![Dodaj członków](./media/manage-users/application-add-members.png)

3. Wyszukaj nazwę użytkownika.  Na liście znajdują się tylko użytkownicy usługi Azure AD, którzy istnieją w dzierżawie usługi Blockchain Workbench. Jeśli użytkownik nie zostanie znaleziony, musisz [Dodaj użytkowników usługi Azure AD](#add-azure-ad-users).

    ![Dodaj członków](./media/manage-users/find-user.png)

4. Wybierz **roli** z listy rozwijanej.

    ![Wybierz członków roli](./media/manage-users/application-select-role.png)

5. Wybierz pozycję **Dodaj**, aby dodać członka ze skojarzoną rolą do aplikacji.

#### <a name="remove-member-from-application"></a>Usuń członka z poziomu aplikacji

1. Wybierz Kafelek elementu członkowskiego, aby wyświetlić listę bieżących członków.
2. Dla użytkownika, aby usunąć, wybierz **Usuń** z roli listy rozwijanej.

    ![Usuwanie elementu członkowskiego](./media/manage-users/application-remove-member.png)

#### <a name="change-or-add-role"></a>Zmiana lub dodanie roli

1. Wybierz Kafelek elementu członkowskiego, aby wyświetlić listę bieżących członków.
2. Dla użytkownika, dla których chcesz zmienić, kliknij przycisk listy rozwijanej i wybierz nową rolę.

    ![Zmiana roli](./media/manage-users/application-change-role.png)

## <a name="next-steps"></a>Kolejne kroki

W tym artykule wiesz jak zarządzać użytkownikami dla aplikacji Azure Blockchain Workbench. Informacje na temat tworzenia aplikacji łańcucha bloków, przejdź do następnego artykułu porad.

> [!div class="nextstepaction"]
> [Tworzenie aplikacji łańcucha bloków w aplikacji Azure Blockchain Workbench](create-app.md)
