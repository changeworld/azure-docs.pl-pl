---
title: Rozpoczynanie korzystania z usługi PIM — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Dowiedz się, jak włączyć i rozpocząć korzystanie z usługi Azure AD Privileged Identity Management (PIM) w witrynie Azure portal.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 03/13/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cbb9b4340a7cdb9be5039722a8f75e09288ec48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472864"
---
# <a name="start-using-privileged-identity-management"></a>Rozpoczęcie korzystania z usługi Privileged Identity Management

Dzięki uprzywilejowanej zarządzania tożsamościami (PIM) można zarządzać, kontrolować i monitorować dostęp w organizacji usługi Azure Active Directory (Azure AD). Ten zakres obejmuje dostęp do zasobów platformy Azure, usługi Azure AD i innych usług online firmy Microsoft, takich jak Office 365 lub Microsoft Intune.

W tym artykule opisano, jak włączyć i rozpocząć korzystanie z zarządzania tożsamościami uprzywilejowanymi.

## <a name="prerequisites"></a>Wymagania wstępne

Aby korzystać z zarządzania tożsamościami uprzywilejowanymi, musisz mieć jedną z następujących licencji:

- Usługa Azure AD — warstwa Premium P2
- Enterprise Mobility + Security (EMS) E5

Aby uzyskać więcej informacji, zobacz [Wymagania licencyjne dotyczące korzystania z zarządzania tożsamościami uprzywilejowanymi](subscription-requirements.md).

## <a name="sign-up-pim-for-azure-ad-roles"></a>Rejestrowanie ról usługi PIM dla usługi Azure AD

Po włączeniu zarządzania tożsamościami uprzywilejowanymi dla katalogu, musisz zarejestrować zarządzanie uprzywilejowanymi tożsamościami, aby zarządzać rolami usługi Azure AD.

1. Otwórz **zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD**.

1. Wybierz **role usługi Azure AD**.

    ![Rejestrowanie uprzywilejowanego zarządzania tożsamościami dla ról usługi Azure AD](./media/pim-getting-started/sign-up-pim-azure-ad-roles.png)

1. Wybierz pozycję **Zarejestruj się**.

1. W wyświetlonym komunikacie kliknij przycisk **Tak,** aby zarejestrować zarządzanie uprzywilejowanymi tożsamościami w celu zarządzania rolami usługi Azure AD.

    ![Zarejestruj się komunikat Zarządzanie tożsamościami uprzywilejowanymi dla usługi Azure AD](./media/pim-getting-started/sign-up-pim-message.png)

    Po zakończeniu rejestracji opcje usługi Azure AD zostaną włączone. Może być konieczne odświeżenie portalu.

    Aby uzyskać informacje dotyczące odnajdywać i wybierać zasoby platformy Azure do ochrony za pomocą zarządzania tożsamościami uprzywilejowanymi, zobacz [Odnajdowanie zasobów platformy Azure do zarządzania w dziale Zarządzanie tożsamościami uprzywilejowanymi.](pim-resource-roles-discover-resources.md)

## <a name="navigate-to-your-tasks"></a>Przejdź do zadań

Po skonfigurowaniu zarządzania tożsamościami uprzywilejowanymi można uruchomić zadania zarządzania tożsamościami.

![Okno nawigacji w zarządzania tożsamościami uprzywilejowanymi z wyświetlaniem opcji Zadania i Zarządzanie opcjami](./media/pim-getting-started/pim-quickstart-tasks.png)

| Zadanie + Zarządzanie | Opis |
| --- | --- |
| **Moje role**  | Wyświetla listę przypisanych do Ciebie kwalifikujących się i aktywnych ról. Możesz tu aktywować dowolne przypisane, kwalifikujące się role. |
| **Moje prośby** | Wyświetla oczekujące żądania aktywowania kwalifikujących się przypisań ról. |
| **Zatwierdzanie żądań** | Wyświetla listę żądań aktywowania kwalifikujących się ról przez użytkowników w katalogu, które są wyznaczone do zatwierdzenia. |
| **Przegląd dostępu** | Wyświetla listę aktywnych recenzji dostępu, do których masz przypisany, niezależnie od tego, czy przeglądasz dostęp dla siebie, czy dla innej osoby. |
| **Role usługi Azure AD** | Wyświetla pulpit nawigacyjny i ustawienia dla uprzywilejowanych administratorów ról do zarządzania przypisaniami ról usługi Azure AD. Z tego pulpitu nawigacyjnego nie mogą korzystać osoby, które nie są administratorami ról uprzywilejowanych. Tacy użytkownicy mają dostęp do specjalnego pulpitu nawigacyjnego zatytułowanego Mój widok. Pulpit nawigacyjny Mój widok zawiera tylko informacje o użytkowniku, który uzyskał dostęp do pulpitu nawigacyjnego, a nie całej dzierżawie. |
| **Zasoby platformy Azure** | Wyświetla pulpit nawigacyjny i ustawienia dla uprzywilejowanych administratorów ról do zarządzania przydziałami ról zasobów platformy Azure. Z tego pulpitu nawigacyjnego nie mogą korzystać osoby, które nie są administratorami ról uprzywilejowanych. Tacy użytkownicy mają dostęp do specjalnego pulpitu nawigacyjnego zatytułowanego Mój widok. Pulpit nawigacyjny Mój widok zawiera tylko informacje o użytkowniku, który uzyskał dostęp do pulpitu nawigacyjnego, a nie całej dzierżawie. |

## <a name="add-a-pim-tile-to-the-dashboard"></a>Dodawanie kafelka PIM do pulpitu nawigacyjnego

Aby ułatwić otwieranie zarządzania tożsamościami uprzywilejowanymi, dodaj kafelek zarządzania tożsamościami uprzywilejowanymi do pulpitu nawigacyjnego witryny Azure portal.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).

1. Wybierz **wszystkie usługi** i znajdź usługę Azure AD **Privileged Identity Management.**

    ![Zarządzanie tożsamościami uprzywilejowanymi usługi Azure AD we wszystkich usługach](./media/pim-getting-started/pim-all-services-find.png)

1. Wybierz szybki start zarządzania tożsamościami uprzywilejowanymi.

1. Zaznacz **pole wyboru Przypinanie bloku do pulpitu nawigacyjnego,** aby przypiąć blok Szybki start zarządzania tożsamościami uprzywilejowanymi do pulpitu nawigacyjnego.

    ![Ikona pinezki do przypnij ostrze Zarządzanie tożsamością uprzywilejowaną do pulpitu nawigacyjnego](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    Na pulpicie nawigacyjnym platformy Azure zobaczysz kafelek w ten sposób:

    ![Kafelek Szybki start zarządzania tożsamościami uprzywilejowanych na pulpicie nawigacyjnym](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie ról usługi Azure AD w zarządzania tożsamościami uprzywilejowanymi](pim-how-to-add-role-to-user.md)
- [Odnajdowanie zasobów platformy Azure do zarządzania w dziale Zarządzanie tożsamościami uprzywilejowanymi](pim-resource-roles-discover-resources.md)
