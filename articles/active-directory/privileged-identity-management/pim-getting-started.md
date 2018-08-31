---
title: Rozpoczynanie korzystania z usługi PIM — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozpocząć korzystanie z usługi Azure AD Privileged Identity Management (PIM) w witrynie Azure portal.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 08/27/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 5b3bff27821964648713b02589c941c99e3eb03d
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190093"
---
# <a name="start-using-pim"></a>Rozpoczynanie korzystania z usługi PIM

Za pomocą usługi Azure Active Directory (Azure AD) Privileged Identity Management (PIM), możesz zarządzanie, sterowanie i monitorowanie dostępu w organizacji. Ten zakres obejmuje dostęp do zasobów platformy Azure, usługi Azure AD i innych usług online firmy Microsoft, takich jak Office 365 lub Microsoft Intune.

W tym artykule wyjaśniono, jak dodać aplikację Azure AD PIM do pulpitu nawigacyjnego witryny Azure Portal.

## <a name="first-person-to-use-pim"></a>Pierwszą osobą, aby używać aplikacji PIM

Jeśli jesteś pierwszą osobą, która można używać aplikacji PIM w katalogu, są automatycznie przypisywane [administrator zabezpieczeń](../users-groups-roles/directory-assign-admin-roles.md#security-administrator) i [administratorem ról uprzywilejowanych](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) ról w katalogu. Tylko administratorzy ról uprzywilejowanych mogą zarządzać przypisaniami ról użytkowników w katalogu usługi Azure AD. Ponadto można zdecydować się na uruchomienie [kreatora zabezpieczeń](pim-security-wizard.md), który przeprowadzi Cię przez środowisko początkowego odnajdywania i przypisywania.

## <a name="add-pim-tile-to-the-dashboard"></a>Dodaj Kafelek usługi PIM do pulpitu nawigacyjnego

Aby ułatwić Otwórz PIM, należy dodać Kafelek usługi PIM do pulpitu nawigacyjnego witryny Azure portal.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) jako administrator globalny katalogu.

1. Kliknij przycisk **wszystkich usług** i Znajdź **usługi Azure AD Privileged Identity Management** usługi.

    ![Usługa Azure AD Privileged Identity Management w przypadku wszystkich usług](./media/pim-getting-started/pim-all-services-find.png)

1. Kliknij, aby otworzyć tego przewodnika Szybki Start usługi PIM.

1. Sprawdź **Przypnij blok do pulpitu nawigacyjnego** Aby przypiąć blok szybkiego startu usługi PIM do pulpitu nawigacyjnego.

    ![Przypnij blok do pulpitu nawigacyjnego](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    Na pulpicie nawigacyjnym platformy Azure zobaczysz Kafelek następująco:

    ![Przewodnik Szybki Start usługi PIM kafelka](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="navigate-to-your-tasks"></a>Przejdź do zadań

Po skonfigurowaniu usługi PIM, można użyć tego bloku, wykonywać zadania związane z zarządzaniem tożsamościami.

![Zrzut ekranu przedstawiający zadania najwyższego poziomu dla aplikacji PIM](./media/pim-getting-started/pim-quickstart-tasks.png)

| Zadania i zarządzanie nimi | Opis |
| --- | --- |
| **Moje role**  | Wyświetla listę kwalifikujących się i aktywnych ról przypisanych do Ciebie. Możesz tu aktywować dowolne przypisane, kwalifikujące się role. |
| **Moje żądania** | Wyświetla oczekujące żądania aktywowania kwalifikujących się przypisań ról. |
| **Dostęp do aplikacji** | Można zmniejszyć opóźnienia potencjalnych i od razu po aktywacji za pomocą roli. |
| **Zatwierdzanie żądań** | Przedstawia listę żądań aktywowania kwalifikujących się ról użytkowników w katalogu, które zostały oznaczone do zatwierdzenia. |
| **Przegląd dostępu wszystkich użytkowników** | Wyświetla listę aktywnych przeglądów dostępu, które są przypisane do ukończenia, czy przeglądasz dostęp dla siebie lub kogoś innego. |
| **Role katalogu usługi Azure AD** | Przedstawia pulpit nawigacyjny i ustawienia dla ról uprzywilejowanych administratorów do zarządzania przypisaniami ról katalogu usługi Azure AD. Z tego pulpitu nawigacyjnego nie mogą korzystać osoby, które nie są administratorami ról uprzywilejowanych. Tacy użytkownicy mają dostęp do specjalnego pulpitu nawigacyjnego zatytułowanego Mój widok. Pulpit nawigacyjny Mój widok zawiera tylko informacje o użytkowniku, który uzyskał dostęp do pulpitu nawigacyjnego, a nie całej dzierżawie. |
| **Zasoby platformy Azure** | Przedstawia pulpit nawigacyjny i ustawienia dla ról uprzywilejowanych administratorów do zarządzania przypisaniami ról zasobów platformy Azure. Z tego pulpitu nawigacyjnego nie mogą korzystać osoby, które nie są administratorami ról uprzywilejowanych. Tacy użytkownicy mają dostęp do specjalnego pulpitu nawigacyjnego zatytułowanego Mój widok. Pulpit nawigacyjny Mój widok zawiera tylko informacje o użytkowniku, który uzyskał dostęp do pulpitu nawigacyjnego, a nie całej dzierżawie. |

## <a name="next-steps"></a>Kolejne kroki

- [Uaktywnij Moje role katalogu usługi Azure AD w usłudze PIM](pim-how-to-activate-role.md)
- [Uaktywnij Moje role zasobów platformy Azure w usłudze PIM](pim-resource-roles-activate-your-roles.md)
