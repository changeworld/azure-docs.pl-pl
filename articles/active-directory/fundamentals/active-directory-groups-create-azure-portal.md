---
title: Tworzenie grupy podstawowej i dodawanie członków — Azure Active Directory | Microsoft Docs
description: Instrukcje tworzenia grupy podstawowej za pomocą usługi Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: quickstart
ms.date: 03/01/2019
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c551a81788df8d68664abaa03f467a4521222d0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "73473467"
---
# <a name="create-a-basic-group-and-add-members-using-azure-active-directory"></a>Tworzenie grupy podstawowej i dodawanie członków przy użyciu usługi Azure Active Directory
Grupę podstawową można utworzyć przy użyciu portalu usługi Azure Active Directory (Azure AD). Na potrzeby tego artykułu grupa podstawowa jest dodawana do pojedynczego zasobu przez właściciela zasobu (administratora) i zawiera określonych członków (pracowników), którzy potrzebują dostępu do tego zasobu. Aby uzyskać informacje o bardziej złożonych scenariuszach, w tym dynamicznym członkostwie i tworzeniu reguł, zobacz [dokumentację zarządzania użytkownikami w usłudze Azure Active Directory](../users-groups-roles/index.yml).

## <a name="create-a-basic-group-and-add-members"></a>Tworzenie grupy podstawowej i dodawanie członków
Istnieje możliwość jednoczesnego utworzenia grupy podstawowej i dodania członków.

### <a name="to-create-a-basic-group-and-add-members"></a>Tworzenie grupy podstawowej i dodawanie członków
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) przy użyciu konta administratora globalnego dla katalogu.

1. Wyszukaj i wybierz pozycję **Azure Active Directory**.

    ![Strona usługi Azure AD z pokazami grup](media/active-directory-groups-create-azure-portal/search-active-directory.png)

1. Na stronie **Active Directory** wybierz pozycję **Grupy,** a następnie wybierz pozycję **Nowa grupa**.

    ![Strona usługi Azure AD z pokazami grup](media/active-directory-groups-create-azure-portal/group-full-screen.png)

1. Na stronie **Nowa grupa** wypełnij wymagane informacje.

    ![Strona nowej grupy wypełniona za pomocą informacji z przykładu](media/active-directory-groups-create-azure-portal/new-group-blade.png)

   - **Typ grupy (wymagany).** Wybierz wstępnie zdefiniowany typ grupy. Obejmuje to: - **Bezpieczeństwo**. Używany do zarządzania dostępem członków i komputerów do udostępnionych zasobów dla grupy użytkowników. Na przykład można utworzyć grupę zabezpieczeń dla konkretnych zasad zabezpieczeń. W ten sposób można jednocześnie udzielić zestawu uprawnień wszystkim członkom zamiast udzielania uprawnień każdemu członkowi osobno. Aby uzyskać więcej informacji na temat zarządzania dostępem do zasobów, zobacz [Zarządzanie dostępem do zasobów przy użyciu grup usługi Azure Active Directory](active-directory-manage-groups.md).
               - **Usługa Office 365**. Zapewnia możliwości współpracy przez udostępnienie członkom współużytkowanej skrzynki pocztowej, kalendarza, plików, witryny programu SharePoint i innych. Ta opcja umożliwia także udzielenie dostępu do grupy osobom spoza organizacji. Aby uzyskać więcej informacji na temat grup usługi Office 365, zobacz [Więcej informacji na temat grup usługi Office 365](https://support.office.com/article/learn-about-office-365-groups-b565caa1-5c40-40ef-9915-60fdb2d97fa2).

   - **Nazwa grupy (wymagana).** Dodaj nazwę grupy, która ma znaczenie i którą łatwo zapamiętać. Zostanie przeprowadzona kontrola w celu ustalenia, czy nazwa jest już używana dla innej grupy. Jeśli nazwa jest już używana, aby uniknąć duplikatów nazewnictwa, zostaniesz poproszony o zmodyfikowanie nazwy grupy.

   - **Grupowa wiadomość e-mail (wymagana).** Dodaj adres e-mail grupy lub zachowaj adres e-mail, który jest wypełniany automatycznie.

   - **Opis grupy.** Do grupy możesz dodać opcjonalny opis.

   - **Typ członkostwa (wymagany).** Wybierz wstępnie zdefiniowany typ członkostwa. Obejmuje to: - **Przypisane.** Umożliwia dodanie do grupy określonych użytkowników z unikatowymi uprawnieniami. Na potrzeby tego artykułu użyjemy tej opcji.
          - **Dynamiczny użytkownik.** Umożliwia automatyczne dodawanie i usuwanie członków za pomocą dynamicznych reguł członkostwa. Jeśli atrybuty członka zmienią się, system sprawdzi reguły grupy dynamicznej w katalogu, aby określić, czy członek spełnia wymagania reguły (zostanie dodany), czy też nie spełnia już wymagań reguł (zostanie usunięty).
          - **Urządzenie dynamiczne.** Umożliwia korzystanie z reguł grupy dynamicznej do automatycznego dodawania i usuwania urządzeń. Jeśli atrybuty urządzenia zmienią się, system sprawdzi reguły grupy dynamicznej w katalogu, aby określić, czy urządzenie spełnia wymagania reguły (zostanie dodane), czy też nie spełnia już wymagań reguł (zostanie usunięte).

        >[!Important]
        >Grupę dynamiczną można utworzyć dla urządzeń lub użytkowników, ale nie dla obu tych elementów. Ponadto nie można utworzyć grupy urządzeń na podstawie atrybutów właścicieli urządzeń. Reguły członkostwa urządzenia mogą przywoływać tylko atrybuty urządzeń. Aby uzyskać więcej informacji na temat tworzenia grupy dynamicznej dla użytkowników i urządzeń, zobacz [Tworzenie grupy dynamicznej i sprawdzanie stanu](../users-groups-roles/groups-create-rule.md).

1. Wybierz **pozycję Utwórz**.

    Grupa zostanie utworzona i będzie gotowa na dodanie członków.

1. Wybierz obszar **Elementy członkowskie** na stronie **Grupy**, a następnie rozpocznij wyszukiwanie członków do dodania do tej grupy na stronie **Wybieranie elementów członkowskich**.

    ![Wybieranie członków grupy w ramach procesu tworzenia grupy](media/active-directory-groups-create-azure-portal/select-members-create-group.png)

1. Po zakończeniu dodawania członków wybierz pozycję **Wybierz**.

    Strona **Przegląd grupy** zostanie zaktualizowana i będzie wyświetlać liczbę członków dodanych teraz do grupy.

    ![Strona Przegląd grupy z wyróżnioną liczbą członków](media/active-directory-groups-create-azure-portal/group-overview-blade-number-highlight.png)

## <a name="turn-on-or-off-welcome-email"></a>Włączanie lub wyłączanie powitalnej poczty e-mail

Po utworzeniu nowej grupy usługi Office 365, niezależnie od tego, czy istnieje członkostwo dynamiczne, czy statyczne, powiadomienie powitalne jest wysyłane do wszystkich użytkowników, którzy zostaną dodani do grupy. Po zmianie jakichkolwiek atrybutów użytkownika lub urządzenia wszystkie reguły grupy dynamicznej w organizacji są przetwarzane pod kątem potencjalnych zmian członkostwa. Użytkownicy, którzy zostaną dodana, otrzymają również powiadomienie powitalne. To zachowanie można wyłączyć w [programie Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps). 

## <a name="next-steps"></a>Następne kroki
Teraz, po dodaniu grupy i co najmniej jednego użytkownika, możesz wykonywać następujące działania:

- [Wyświetlanie grup i elementów członkowskich](active-directory-groups-view-azure-portal.md)

- [Zarządzanie członkostwem w grupie](active-directory-groups-membership-azure-portal.md)

- [Zarządzanie regułami dynamicznymi dla użytkowników w grupie](../users-groups-roles/groups-create-rule.md)

- [Edytowanie ustawień grupy](active-directory-groups-settings-azure-portal.md)

- [Zarządzanie dostępem do zasobów przy użyciu grup](active-directory-manage-groups.md)

- [Zarządzanie dostępem do aplikacji SaaS przy użyciu grup](../users-groups-roles/groups-saasapps.md)

- [Zarządzanie grupami przy użyciu poleceń programu PowerShell](../users-groups-roles/groups-settings-v2-cmdlets.md)

- [Kojarzenie lub dodawanie subskrypcji platformy Azure do usługi Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
