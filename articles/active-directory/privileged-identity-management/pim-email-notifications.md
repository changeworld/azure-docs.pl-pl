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
ms.component: pim
ms.date: 07/24/2018
ms.author: rolyon
ms.reviewer: hanki
ms.custom: pim
ms.openlocfilehash: 7943b4fb8c2027b50ce04c30d21f1b0a58f98ace
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621586"
---
# <a name="email-notifications-in-azure-ad-pim"></a>Powiadomienia e-mail w usłudze Azure AD PIM

Gdy zdarzenia klawiszy zachodzą w usłudze Azure AD Privileged Identity Management (PIM), powiadomienia e-mail są wysyłane do odpowiedniego administratora lub użytkownika. Na przykład PIM wysyła wiadomości e-mail do następujących zdarzeń:

- Po aktywacji ról uprzywilejowanych oczekuje na zatwierdzenie
- Gdy zostanie zatwierdzony żądanie aktywacji roli uprzywilejowanej
- Po aktywowaniu ról uprzywilejowanych
- Po przypisaniu roli uprzywilejowanej
- Po włączeniu usługi Azure AD PIM

Od pod koniec lipca 2018 r. powiadomień e-mail wysłanych przez usługę PIM mają nowy adres e-mail nadawcy oraz nowe projektowania wizualnego. Ta aktualizacja będzie mieć wpływ na obie usługi PIM dla usługi Azure AD i usługi PIM dla zasobów platformy Azure. Wszystkie zdarzenia, które zostały wcześniej wyzwolone wiadomość e-mail z powiadomieniem, będą w dalszym ciągu Wyślij wiadomość e-mail. Niektóre wiadomości e-mail zostanie zaktualizowana zawartość dostarczanie bardziej informacji docelowej.

## <a name="sender-email-address"></a>Adres e-mail nadawcy

Uruchamianie pod koniec lipca 2018 r., powiadomienia e-mail ma następujący adres:

- Adres e-mail:  **azure-noreply@microsoft.com**
- Nazwa wyświetlana: Microsoft Azure

Wcześniej powiadomienia e-mail ma następujący adres:

- Adres e-mail:  **azureadnotifications@microsoft.com**
- Nazwa wyświetlana: usługi powiadomień systemu Microsoft Azure AD

## <a name="email-subject-line"></a>Wiersz tematu wiadomości e-mail

Począwszy od zakończenia z lipca 2018 r. wiadomości e-mail z powiadomieniami dla usługi Azure AD, a role zasobów platformy Azure będzie mieć **PIM** prefiks w wierszu tematu. Oto przykład:

- Usługi PIM: Alain Charon trwale została przypisana rola Czytelnik kopii zapasowej.

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
