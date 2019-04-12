---
title: Zatwierdź lub Odrzuć żądania dla ról usługi Azure AD w usłudze PIM — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak akceptują lub odrzucają żądania dla ról usługi Azure AD w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 47e9033b33c70a72a1685696f75a442e88eed033
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2019
ms.locfileid: "59493573"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-pim"></a>Zatwierdź lub Odrzuć żądania dla ról usługi Azure AD w usłudze PIM

Za pomocą usługi Azure Active Directory (Azure AD) Privileged Identity Management (PIM), można skonfigurować role, aby wymagały zatwierdzenia aktywacji i wybierz jednego lub wielu użytkowników lub grup w ramach delegowanego osób zatwierdzających. Osoby zatwierdzające delegowanego mają 24 godziny do żądań zatwierdzenia. Jeśli żądanie nie zostanie zatwierdzone w ciągu 24 godzin, w uprawniony użytkownik musi ponownie przesłać nowe żądanie. Nie konfiguruje się w danym przedziale czasu zatwierdzania 24-godzinnym.

Wykonaj kroki opisane w tym artykule, akceptują lub odrzucają żądania dla ról usługi Azure AD.

## <a name="view-pending-requests"></a>Wyświetlanie oczekujących żądań

Jako delegowany osoby zatwierdzającej otrzymasz wiadomość e-mail z powiadomieniem, gdy żądanie roli usługi Azure AD jest oczekujące na zatwierdzenie. Możesz wyświetlić te oczekujących żądań w usłudze PIM.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Otwórz **usługi Azure AD Privileged Identity Management**.

1. Kliknij przycisk **ról usługi Azure AD**.

1. Kliknij przycisk **zatwierdzanie żądań**.

    ![Role usługi AD PIM Azure — role](./media/azure-ad-pim-approval-workflow/pim-directory-roles-approve-requests.png)

    Zobaczysz listę żądań oczekujących na zatwierdzenie.

## <a name="approve-requests"></a>Zatwierdzanie żądań

1. Zaznacz żądania, aby zatwierdzić, a następnie kliknij przycisk **Zatwierdź** otworzyć Zatwierdź wybrane żądania okienka.

    ![Lista żądań zatwierdzenia PIM](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. W **Przyczyna zatwierdzenia** wpisz przyczynę.

    ![PIM Zatwierdź wybrane żądania](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. Kliknij przycisk **zatwierdzić**.

    Symbol stan zostanie zaktualizowany o zatwierdzenie.

    ![PIM Zatwierdź wybrane żądania](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

## <a name="deny-requests"></a>Odmów żądań

1. Zaznacz żądania, aby odmówić dostępu, a następnie kliknij przycisk **Odmów** otworzyć Odmów zaznaczone okienko żądań.

    ![Lista żądań zatwierdzenia PIM](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. W **Przyczyna odrzucenia** wpisz przyczynę.

    ![Odmów usługi PIM, wybrane żądania](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. Kliknij przycisk **Odmów**.

    Symbol stan zostanie zaktualizowany przy użyciu usługi typu "odmowa".

## <a name="next-steps"></a>Kolejne kroki

- [Powiadomienia e-mail w usłudze PIM](pim-email-notifications.md)
- [Zatwierdź lub Odrzuć żądania dla ról zasobów platformy Azure w usłudze PIM](pim-resource-roles-approval-workflow.md)
