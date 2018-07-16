---
title: Wiadomości e-mail z powiadomieniami w usłudze Azure AD PIM | Dokumentacja firmy Microsoft
description: W tym artykule opisano powiadomienia e-mail w usłudze Azure AD Privileged Identity Management (PIM)
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: protection
ms.date: 07/14/2018
ms.author: rolyon
ms.reviewer: hanki
ms.custom: pim
ms.openlocfilehash: 6c329554b5854f113fb216f874fa5a918110f9c5
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39059781"
---
# <a name="email-notifications-in-azure-ad-pim"></a>Powiadomienia e-mail w usłudze Azure AD PIM

Gdy zdarzenia klawiszy zachodzą w usłudze Azure AD Privileged Identity Management (PIM), powiadomienia e-mail są wysyłane do odpowiedniego administratora lub użytkownika. Na przykład PIM wysyła wiadomości e-mail do następujących zdarzeń:

- Po aktywacji ról uprzywilejowanych oczekuje na zatwierdzenie
- Gdy zostanie zatwierdzony żądanie aktywacji roli uprzywilejowanej
- Po aktywowaniu ról uprzywilejowanych
- Po przypisaniu roli uprzywilejowanej
- Po włączeniu usługi Azure AD PIM

Od pod koniec lipca 2018 r. powiadomień e-mail wysłanych przez usługę PIM mają nowy adres e-mail nadawcy oraz nowe projektowania wizualnego. Ta aktualizacja będzie mieć wpływ na obie usługi PIM dla usługi Azure AD i usługi PIM dla zasobów platformy Azure. Wszystkie zdarzenia, które zostały wcześniej wyzwolone wiadomość e-mail z powiadomieniem, będą w dalszym ciągu Wyślij wiadomość e-mail. Ta aktualizacja jest wyłącznie zmianę wizualną bez konieczności wprowadzania zmian do funkcji.

## <a name="sender-email-address"></a>Adres e-mail nadawcy

Uruchamianie pod koniec lipca 2018 r., powiadomienia e-mail ma następujący adres:

- Adres e-mail:  **azure-noreply@microsoft.com**
- Nazwa wyświetlana: Microsoft Azure

Wcześniej powiadomienia e-mail ma następujący adres:

- Adres e-mail:  **azureadnotifications@microsoft.com**
- Nazwa wyświetlana: usługi powiadomień systemu Microsoft Azure AD

## <a name="pim-emails-for-azure-ad-roles"></a>Wiadomości e-mail z usługi PIM dla ról usługi Azure AD

Uruchamianie pod koniec lipca 2018 r., powiadomienia e-mail usługi PIM dla ról usługi Azure AD ma zaktualizowanego projektu. Poniżej przedstawiono przykładową wiadomością e-mail jest wysyłana, gdy użytkownik aktywuje ról uprzywilejowanych dla fikcyjnej firmy Contoso.

![Outlook.com PIM dla ról usługi Azure AD](./media/pim-email-notifications/email-directory-new.png)

Poprzednio po użytkownik aktywacji ról uprzywilejowanych, wiadomości e-mail wyglądał następująco.

![Stary e-mail usługi PIM dla ról usługi Azure AD](./media/pim-email-notifications/email-directory-old.png)

## <a name="pim-emails-for-azure-resource-roles"></a>Wiadomości e-mail z usługi PIM dla ról zasobów platformy Azure

Uruchamianie pod koniec lipca 2018 r., powiadomienia e-mail usługi PIM dla ról zasobów platformy Azure mają zaktualizowanego projektu. Poniżej przedstawiono przykładową wiadomością e-mail jest wysyłana, gdy użytkownik jest przypisany do ról uprzywilejowanych dla fikcyjnej firmy Contoso.

![Outlook.com PIM dla ról zasobów platformy Azure](./media/pim-email-notifications/email-resources-new.png)

Wcześniej po użytkownik został przypisany ról uprzywilejowanych, wiadomości e-mail wyglądał następująco.

![Stary e-mail usługi PIM dla ról zasobów platformy Azure](./media/pim-email-notifications/email-resources-old.png)

## <a name="next-steps"></a>Kolejne kroki

- [Jak zarządzać ustawień aktywacji roli w usłudze Azure AD PIM](pim-how-to-change-default-settings.md)
- [Zatwierdzenia w usłudze Azure AD PIM](azure-ad-pim-approval-workflow.md)
