---
title: Zarządzanie użytkownikami w Azure Blockchain Workbench
description: Jak zarządzać użytkownikami w Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/26/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 80f20e438b1d923e688aeef9e6b353642bd74c27
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="manage-users-in-azure-blockchain-workbench"></a>Zarządzanie użytkownikami w Azure Blockchain Workbench

Azure Blockchain Workbench obejmują zarządzanie użytkownikami dla osób i organizacji, które są częścią sieci konsorcjum.

## <a name="prerequisites"></a>Wymagania wstępne

Deployment Blockchain Workbench jest wymagana. Zobacz [deployment Azure Blockchain Workbench](blockchain-workbench-deploy.md) szczegółowe informacje na temat wdrażania.

## <a name="add-azure-ad-users"></a>Dodaj użytkowników usługi Azure AD

Azure Blockchain Workbench używa usługi Azure Active Directory (Azure AD) do uwierzytelniania, kontroli dostępu i ról. Użytkowników w dzierżawie Workbench Blockchain usługi Azure AD można uwierzytelniać, a Blockchain Workbench. Dodawanie użytkowników do roli administratora aplikacji do interakcji i wykonywać akcje.

Użytkownicy Blockchain Workbench muszą istnieć w dzierżawie usługi Azure AD, zanim można przypisać je do aplikacji i ról. Do dodawania użytkowników do usługi Azure AD, wykonaj następujące kroki:

1.  Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2.  W prawym górnym rogu wybierz konto, a następnie przełączyć się do dzierżawy usługi Azure AD skojarzonej Blockchain Workbench.
3.  Wybierz **usługi Azure Active Directory > Użytkownicy**. Możesz wyświetlić listę użytkowników w katalogu.
4.  Aby dodać użytkowników do katalogu, wybierz **nowego użytkownika**. Dla użytkowników zewnętrznych, wybierz **nowego użytkownika gościa**.

    ![Nowy użytkownik](media/blockchain-workbench-manage-users/add-ad-user.png)

5.  Wypełnij wymagane pola dla nowego użytkownika. Wybierz pozycję **Utwórz**.

Odwiedź stronę [usługi Azure AD](../active-directory/add-users-azure-active-directory.md) dokumentacji, aby uzyskać więcej informacji na temat sposobu zarządzania użytkownikami w usłudze Azure AD.

## <a name="manage-blockchain-workbench-administrators"></a>Zarządzaj administratorami Blockchain Workbench

Gdy użytkownicy zostali dodani do katalogu, następnym krokiem jest Wybierz użytkowników, którzy są administratorami Blockchain Workbench. Użytkownicy w **administratora** grupy są skojarzone z **roli Administrator aplikacji** w Blockchain Workbench. Administratorzy mogą Dodaj lub usuń użytkowników, przypisywania użytkowników do określonych scenariuszy i Utwórz nowe aplikacje.

Aby dodać użytkowników do **administratora** w katalogu usługi Azure AD:

1.  Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2.  Sprawdź, czy są w dzierżawie usługi Azure AD skojarzonego z Blockchain Workbench, wybierając konto w prawym górnym rogu.
3.  Wybierz **usługi Azure Active Directory > aplikacje dla przedsiębiorstw**.
4.  Wybierz aplikację klienta usługi Azure AD dla Blockchain Workbench
    
    ![Wszystkich rejestracji aplikacji przedsiębiorstwa](media/blockchain-workbench-manage-users/select-blockchain-client-app.png)

5.  Wybierz **użytkowników i grup > Dodaj użytkownika**.
6.  W **Dodaj przydziału**, wybierz pozycję **użytkowników**. Wybierz lub Wyszukaj użytkownika, którego chcesz dodać jako administrator. Kliknij przycisk **wybierz** po zakończeniu wybierania.

    ![Dodawanie przypisania](media/blockchain-workbench-manage-users/add-user-assignment.png)

9.  Sprawdź **roli** ustawiono **administratora**
10. Wybierz opcję **Przypisz**. Dodano użytkowników są wyświetlane na liście z rolą administratora przypisane.

    ![Użytkownicy aplikacji klienta Blockchain](media/blockchain-workbench-manage-users/blockchain-admin-list.png)

## <a name="managing-blockchain-workbench-members"></a>Zarządzanie Blockchain Workbench elementów członkowskich

Aplikacja Blockchain Workbench służy do zarządzania użytkownikami i organizacji, które są częścią sieci konsorcjum. Można dodawać i usuwać użytkowników do aplikacji i ról.

1. [Otwórz Blockchain Workbench](blockchain-workbench-deploy.md#blockchain-workbench-web-url) w przeglądarce i zaloguj się jako administrator.

    ![Blockchain Workbench](media/blockchain-workbench-manage-users/blockchain-workbench-applications.png)

    Elementy członkowskie są dodawane do każdej aplikacji. Elementy Członkowskie mogą mieć co najmniej jedną rolę aplikacji o rozpoczęciu umów lub wykonać akcje.

2. Aby zarządzać elementy członkowskie dla aplikacji, należy wybrać kafelka aplikacji w **aplikacji** okienka.

    Liczba elementów członkowskich skojarzonych do wybranej aplikacji jest widoczny na kafelku elementów członkowskich.

    ![Wybierz aplikację](media/blockchain-workbench-manage-users/blockchain-workbench-select-application.png)


#### <a name="add-member-to-application"></a>Dodawanie członka do aplikacji

1. Wybierz Kafelek — członek, aby wyświetlić listę bieżących członków.
2. Wybierz **dodawać członków**.

    ![Dodaj członków](media/blockchain-workbench-manage-users/application-add-members.png)

3. Wyszukiwanie nazwy użytkownika.  Tylko Azure użytkowników usługi AD, które istnieją w dzierżawie Blockchain Workbench są wyświetlane. Jeśli użytkownik nie zostanie znaleziony, musisz [Dodaj użytkowników usługi Azure AD](#add-azure-ad-users).

    ![Dodaj członków](media/blockchain-workbench-manage-users/find-user.png)

4. Wybierz **roli** z listy rozwijanej.

    ![Wybierz członków roli](media/blockchain-workbench-manage-users/application-select-role.png)

5. Wybierz **Dodaj** można dodać elementu członkowskiego z rolą skojarzone z aplikacją.

#### <a name="remove-member-from-application"></a>Usuń element członkowski z aplikacji

1. Wybierz Kafelek — członek, aby wyświetlić listę bieżących członków.
2. Dla użytkownika, aby usunąć, wybierz **Usuń** z listy rozwijanej roli.

    ![Usuń członka](media/blockchain-workbench-manage-users/application-remove-member.png)

#### <a name="change-or-add-role"></a>Zmiana lub dodanie roli

1. Wybierz Kafelek — członek, aby wyświetlić listę bieżących członków.
2. Dla użytkownika, którego chcesz zmienić kliknij listę rozwijaną i wybierz nową rolę.

    ![Zmiany roli](media/blockchain-workbench-manage-users/application-change-role.png)

## <a name="next-steps"></a>Kolejne kroki

W tym artykule zapoznaniu do zarządzania użytkownikami dla Azure Blockchain Workbench. Aby dowiedzieć się, jak utworzyć aplikację blockchain, nadal dalej artykule.

> [!div class="nextstepaction"]
> [Tworzenie aplikacji blockchain w Azure Blockchain Workbench](blockchain-workbench-create-app.md)
