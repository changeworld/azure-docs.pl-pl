---
title: Zatwierdzanie lub odrzucanie żądań dla ról usługi Azure AD w programie PIM-Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak zatwierdzać lub odrzucać żądania dla ról usługi Azure AD w Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60a8d373a7e6edeaefd933e4f8ec8ee11e3c14ee
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804025"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-pim"></a>Zatwierdzanie lub odrzucanie żądań dla ról usługi Azure AD w usłudze PIM

Za pomocą usługi Azure Active Directory (Azure AD) Privileged Identity Management (PIM) można skonfigurować role, aby wymagały zatwierdzenia dla aktywacji, i wybrać jednego lub wielu użytkowników lub grupy jako delegowane osoby zatwierdzające. Delegowane osoby zatwierdzające mają 24 godziny na zatwierdzanie żądań. Jeśli żądanie nie zostanie zatwierdzone w ciągu 24 godzin, wówczas uprawniony użytkownik musi ponownie przesłać nowe żądanie. Nie można skonfigurować okna czasu zatwierdzania 24-godzinnego.

Wykonaj kroki opisane w tym artykule, aby zatwierdzić lub odrzucić żądania dla ról usługi Azure AD.

## <a name="view-pending-requests"></a>Wyświetl oczekujące żądania

W przypadku delegowanej osoby zatwierdzającej otrzymasz powiadomienie e-mail, gdy żądanie roli usługi Azure AD oczekuje na zatwierdzenie. Te oczekujące żądania można wyświetlić w usłudze PIM.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Otwórz **Azure AD Privileged Identity Management**.

1. Kliknij pozycję **role usługi Azure AD**.

1. Kliknij pozycję **Zatwierdź żądania**.

    ![Role usługi Azure AD — zatwierdzanie żądań](./media/azure-ad-pim-approval-workflow/pim-directory-roles-approve-requests.png)

    Zostanie wyświetlona lista żądań oczekujących na zatwierdzenie.

## <a name="approve-requests"></a>Zatwierdź żądania

1. Wybierz żądania, które chcesz zatwierdzić, a następnie kliknij przycisk **Zatwierdź** , aby otworzyć okienko Zatwierdź wybrane żądania.

    ![Zatwierdź listę żądań z wyróżnioną opcją zatwierdzania](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. W polu **Przyczyna zatwierdzenia** wpisz przyczynę.

    ![Zatwierdź okienko wybrane żądania z przyczyną zatwierdzenia](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. Kliknij przycisk **Zatwierdź**.

    Symbol stanu zostanie zaktualizowany przy użyciu zatwierdzenia.

    ![Zatwierdź okienko wybrane żądania po kliknięciu przycisku Zatwierdź](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

## <a name="deny-requests"></a>Odmów żądań

1. Wybierz żądania, które chcesz odmówić, a następnie kliknij przycisk **Odmów** , aby otworzyć okienko Odmów wybranych żądań.

    ![Zatwierdź listę żądań z wyróżnioną opcją Odmów](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. W polu **Przyczyna odrzucenia** wpisz przyczynę.

    ![Odmowa okienka wybrane żądania z powodu odrzucenia](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. Kliknij przycisk **Odmów**.

    Symbol stanu zostanie zaktualizowany z odmową.

## <a name="next-steps"></a>Następne kroki

- [Powiadomienia e-mail w usłudze PIM](pim-email-notifications.md)
- [Zatwierdzanie lub odrzucanie żądań dotyczących ról zasobów platformy Azure w usłudze PIM](pim-resource-roles-approval-workflow.md)
