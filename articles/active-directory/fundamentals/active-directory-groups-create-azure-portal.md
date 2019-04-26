---
title: Tworzenie grupy podstawowej i dodawanie członków — Azure Active Directory | Microsoft Docs
description: Instrukcje tworzenia grupy podstawowej za pomocą usługi Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: quickstart
ms.date: 03/01/2019
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78e83f4a1aea04bbaea12181419de6c5c06034f2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60249496"
---
# <a name="create-a-basic-group-and-add-members-using-azure-active-directory"></a>Tworzenie grupy podstawowej i dodawanie członków przy użyciu usługi Azure Active Directory
Grupę podstawową można utworzyć przy użyciu portalu usługi Azure Active Directory (Azure AD). Na potrzeby tego artykułu grupa podstawowa jest dodawana do pojedynczego zasobu przez właściciela zasobu (administratora) i zawiera określonych członków (pracowników), którzy potrzebują dostępu do tego zasobu. Aby uzyskać informacje o bardziej złożonych scenariuszach, w tym dynamicznym członkostwie i tworzeniu reguł, zobacz [dokumentację zarządzania użytkownikami w usłudze Azure Active Directory](../users-groups-roles/index.yml).

## <a name="create-a-basic-group-and-add-members"></a>Tworzenie grupy podstawowej i dodawanie członków
Istnieje możliwość jednoczesnego utworzenia grupy podstawowej i dodania członków.

### <a name="to-create-a-basic-group-and-add-members"></a>Tworzenie grupy podstawowej i dodawanie członków
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) przy użyciu konta administratora globalnego dla katalogu.

2. Wybierz pozycje **Azure Active Directory** i **Grupy**, a następnie pozycję **Nowa grupa**.

    ![Strona usługi AD Azure, przy użyciu grup przedstawiający](media/active-directory-groups-create-azure-portal/group-full-screen.png)

3. Na stronie **Grupy** podaj wymagane informacje.

    ![Strona nowej grupy wypełniona za pomocą informacji z przykładu](media/active-directory-groups-create-azure-portal/new-group-blade.png)

   - **Typ grupy (wymagany).** Wybierz wstępnie zdefiniowany typ grupy. Obejmuje to:
        
       - **Bezpieczeństwo** Używany do zarządzania dostępem członków i komputerów do udostępnionych zasobów dla grupy użytkowników. Na przykład można utworzyć grupę zabezpieczeń dla konkretnych zasad zabezpieczeń. W ten sposób można jednocześnie udzielić zestawu uprawnień wszystkim członkom zamiast udzielania uprawnień każdemu członkowi osobno. Aby uzyskać więcej informacji na temat zarządzania dostępem do zasobów, zobacz [Zarządzanie dostępem do zasobów przy użyciu grup usługi Azure Active Directory](active-directory-manage-groups.md).
        
       - **Office 365**. Zapewnia możliwości współpracy przez udostępnienie członkom współużytkowanej skrzynki pocztowej, kalendarza, plików, witryny programu SharePoint i innych. Ta opcja umożliwia także udzielenie dostępu do grupy osobom spoza organizacji. Aby uzyskać więcej informacji na temat grup usługi Office 365, zobacz [Więcej informacji na temat grup usługi Office 365](https://support.office.com/article/learn-about-office-365-groups-b565caa1-5c40-40ef-9915-60fdb2d97fa2).

   - **Nazwa grupy (wymagana).** Dodaj nazwę grupy, która ma znaczenie i którą łatwo zapamiętać.

   - **Opis grupy.** Do grupy możesz dodać opcjonalny opis.

   - **Typ członkostwa (wymagany).** Wybierz wstępnie zdefiniowany typ członkostwa. Obejmuje to:

     - **Przypisane.** Umożliwia dodanie do grupy określonych użytkowników z unikatowymi uprawnieniami. Na potrzeby tego artykułu użyjemy tej opcji.

     - **Użytkownik dynamiczny.** Umożliwia używanie reguły członkostwa dynamicznego do automatycznego dodawania i usuwania elementów członkowskich. Jeśli atrybuty członka zmienią się, system sprawdzi reguły grupy dynamicznej w katalogu, aby określić, czy członek spełnia wymagania reguły (zostanie dodany), czy też nie spełnia już wymagań reguł (zostanie usunięty).

     - **Urządzenie dynamiczne.** Umożliwia korzystanie z reguł grupy dynamicznej do automatycznego dodawania i usuwania urządzeń. Jeśli atrybuty urządzenia zmienią się, system sprawdzi reguły grupy dynamicznej w katalogu, aby określić, czy urządzenie spełnia wymagania reguły (zostanie dodane), czy też nie spełnia już wymagań reguł (zostanie usunięte).

       >[!Important]
       >Grupę dynamiczną można utworzyć dla urządzeń lub użytkowników, ale nie dla obu tych elementów. Ponadto nie można utworzyć grupy urządzeń na podstawie atrybutów właścicieli urządzeń. Reguły członkostwa urządzenia mogą przywoływać tylko atrybuty urządzeń. Aby uzyskać więcej informacji na temat tworzenia grupy dynamicznej dla użytkowników i urządzeń, zobacz [Tworzenie grupy dynamicznej i sprawdzanie stanu](../users-groups-roles/groups-create-rule.md).

4. Wybierz pozycję **Utwórz**.

    Grupa zostanie utworzona i będzie gotowa na dodanie członków.

5. Wybierz obszar **Elementy członkowskie** na stronie **Grupy**, a następnie rozpocznij wyszukiwanie członków do dodania do tej grupy na stronie **Wybieranie elementów członkowskich**.

    ![Wybieranie członków grupy w ramach procesu tworzenia grupy](media/active-directory-groups-create-azure-portal/select-members-create-group.png)

6. Po zakończeniu dodawania członków wybierz pozycję **Wybierz**.

    Strona **Przegląd grupy** zostanie zaktualizowana i będzie wyświetlać liczbę członków dodanych teraz do grupy.

    ![Strona Przegląd grupy z wyróżnioną liczbą członków](media/active-directory-groups-create-azure-portal/group-overview-blade-number-highlight.png)

## <a name="turn-on-or-off-welcome-email"></a>Włączanie lub wyłączanie powitalnej wiadomości e-mail

W przypadku dowolnej usługi Office 365 zostanie utworzona nowa grupa, czy członkostwo dynamiczne lub statyczne, powitalnej powiadomienie jest wysyłane do wszystkich użytkowników, którzy zostaną dodani do grupy. Po zmianie dowolnych atrybutów użytkownika lub urządzenia, wszystkie reguły dynamicznego w grupach w organizacji są przetwarzane dla potencjalnych zmian członkostwa. Użytkownicy, którzy są dodawani następnie otrzymają powiadomienie powitalnej. Możesz wyłączyć to zachowanie [PowerShell programu Exchange](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps). 

## <a name="next-steps"></a>Kolejne kroki
Teraz, po dodaniu grupy i co najmniej jednego użytkownika, możesz wykonywać następujące działania:

- [Wyświetlanie grup i elementów członkowskich](active-directory-groups-view-azure-portal.md)

- [Zarządzanie członkostwem w grupie](active-directory-groups-membership-azure-portal.md)

- [Zarządzanie regułami dynamicznymi dla użytkowników w grupie](../users-groups-roles/groups-create-rule.md)

- [Edytowanie ustawień grupy](active-directory-groups-settings-azure-portal.md)

- [Zarządzanie dostępem do zasobów przy użyciu grup](active-directory-manage-groups.md)

- [Zarządzanie dostępem do aplikacji SaaS przy użyciu grup](../users-groups-roles/groups-saasapps.md)

- [Zarządzanie grupami przy użyciu poleceń programu PowerShell](../users-groups-roles/groups-settings-v2-cmdlets.md)

- [Kojarzenie lub dodawanie subskrypcji platformy Azure do usługi Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
