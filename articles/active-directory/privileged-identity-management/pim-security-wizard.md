---
title: Azure AD role zabezpieczeń kreatora w usłudze PIM — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: W tym artykule opisano Kreatora zabezpieczeń, który służy do przypisania roli usługi Azure AD convert stałe uprzywilejowany do kwalifikujących się przy użyciu usługi Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: b6f978612cbbf0c326c3e66f25a0fbf4b749cc73
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59496611"
---
# <a name="azure-ad-roles-security-wizard-in-pim"></a>Azure AD role zabezpieczeń kreatora w usłudze PIM

Jeśli jesteś pierwszą osobą do uruchamiania usługi Azure Active Directory (Azure AD) Privileged Identity Management (PIM) dla Twojej organizacji, zobaczysz za pomocą kreatora. Kreator pomoże Ci zrozumieć zagrożenia dla bezpieczeństwa uprzywilejowanych tożsamości oraz jak używać aplikacji PIM, aby ograniczyć te zagrożenia. Nie musisz wprowadzać żadnych zmian istniejących przypisań ról w kreatorze, aby zrobić to później.

## <a name="wizard-overview"></a>Omówienie Kreatora

Przed rozpoczęciem Twojej organizacji za pomocą usługi PIM są trwałe, wszystkie przypisania roli: użytkownicy są zawsze w ramach tych ról nawet wtedy, gdy nie ma obecnie potrzeby swoich uprawnień. Pierwszym krokiem, który Kreator pokazuje listę ról z wysokim poziomem uprawnień i jak wielu użytkowników są obecnie dostępne w tych ról. Możesz przejść do określonej roli, aby uzyskać więcej informacji użytkownikom, jeśli jeden lub jeden z nich jest nieznane.

Drugiego kroku kreatora daje możliwość zmiany przypisania ról administratora.  

> [!WARNING]
> Jest ważne, że masz co najmniej jeden Administrator globalny i więcej niż jeden Administrator ról uprzywilejowanych przy użyciu konta organizacji (nie konta Microsoft). Jeśli istnieje tylko jeden Administrator ról uprzywilejowanych, organizacja nie będzie można Zarządzanie usługą PIM, jeśli to konto zostanie usunięte.
> Ponadto przypisań ról zachować stały, jeśli użytkownik ma konto Microsoft (konto używane do logowania do usług firmy Microsoft, takich jak Skype i Outlook.com). Jeśli planowane jest wymagane uwierzytelnianie MFA do aktywacji dla tej roli, ten użytkownik zostanie zablokowany.

## <a name="run-the-wizard"></a>Uruchamianie kreatora

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Otwórz **usługi Azure AD Privileged Identity Management**.

1. Kliknij przycisk **ról usługi Azure AD** a następnie kliknij przycisk **kreatora**.

    ![Role usługi Azure AD — kreator](./media/pim-security-wizard/wizard-start.png)

1. Kliknij przycisk **1 odnajdowania ról uprzywilejowanych**.

1. Przejrzyj listę ról uprzywilejowanych, aby zobaczyć, którzy użytkownicy są trwałe i kwalifikujące się.

    ![Odnajdywanie użytkowników do ról uprzywilejowanych](./media/pim-security-wizard/discover-privileged-roles-users.png)

1. Kliknij przycisk **dalej** zaznacz składniki mają być uprawnieni.

    ![Konwersja członków na uprawnionych](./media/pim-security-wizard/convert-members-eligible.png)

1. Po wybraniu elementów członkowskich, kliknij przycisk **dalej**.

    ![Przeglądanie zmian](./media/pim-security-wizard/review-changes.png)

1. Kliknij przycisk **OK** przekonwertować stałych przypisań uprawnionych.

    Po zakończeniu procesu konwersji, zostanie wyświetlone powiadomienie.

    ![Powiadomienia](./media/pim-security-wizard/notification-completion.png)

Aby przekonwertować kwalifikujących się inne przypisania ról uprzywilejowanych, możesz Uruchom ponownie kreatora. Jeśli chcesz używać interfejsu PIM zamiast kreatora, zobacz [przypisać role usługi Azure AD w usłudze PIM](pim-how-to-add-role-to-user.md).

## <a name="next-steps"></a>Kolejne kroki

- [Przypisz role usługi Azure AD w usłudze PIM](pim-how-to-add-role-to-user.md)
- [Udzielanie dostępu do innych administratorów do zarządzania usługi PIM](pim-how-to-give-access-to-pim.md)
