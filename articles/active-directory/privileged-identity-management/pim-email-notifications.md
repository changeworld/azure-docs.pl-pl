---
title: Wiadomości e-mail z powiadomieniami w usłudze PIM — Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano powiadomienia e-mail w usłudze Azure AD Privileged Identity Management (PIM).
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
ms.date: 09/07/2018
ms.author: rolyon
ms.reviewer: hanki
ms.custom: pim
ms.openlocfilehash: de1d29d3ab1b370257c3a2d6b6ff9f677197fc2a
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2018
ms.locfileid: "44303068"
---
# <a name="email-notifications-in-pim"></a>Powiadomienia e-mail w usłudze PIM

W przypadku zdarzenia klawiszy zachodzą w usłudze Azure AD Privileged Identity Management (PIM), powiadomienia e-mail będą wysyłane. Na przykład PIM wysyła wiadomości e-mail do następujących zdarzeń:

- Po aktywacji ról uprzywilejowanych oczekuje na zatwierdzenie
- Po wykonaniu żądania aktywacji roli uprzywilejowanej
- Po aktywowaniu ról uprzywilejowanych
- Po przypisaniu roli uprzywilejowanej
- Po włączeniu usługi Azure AD PIM

Powiadomienia e-mail są wysyłane do następujących administratorów:

- Administrator ról uprzywilejowanych
- Administrator zabezpieczeń

Powiadomienia e-mail również są wysyłane do użytkownika końcowego, który ma ról uprzywilejowanych dla następujących zdarzeń:

- Po wykonaniu żądania aktywacji roli uprzywilejowanej
- Po przypisaniu roli uprzywilejowanej

Od pod koniec lipca 2018 r. powiadomień e-mail wysłanych przez usługę PIM mają nowy adres e-mail nadawcy i nowe projektowania wizualnego. Ta aktualizacja będzie mieć wpływ na obie usługi PIM dla usługi Azure AD i usługi PIM dla zasobów platformy Azure. Wszystkie zdarzenia, które zostały wcześniej wyzwolone wiadomość e-mail z powiadomieniem, będą w dalszym ciągu Wyślij wiadomość e-mail. Niektóre wiadomości e-mail zostanie zaktualizowana zawartość dostarczanie bardziej informacji docelowej.

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

- [Konfigurowanie ustawień roli w katalogu usługi Azure AD w usłudze PIM](pim-how-to-change-default-settings.md)
- [Zatwierdź lub Odrzuć żądania dla ról katalogu usługi Azure AD w usłudze PIM](azure-ad-pim-approval-workflow.md)
