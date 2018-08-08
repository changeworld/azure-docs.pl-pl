---
title: Kreator zabezpieczeń usługi Azure AD Privileged Identity Management
description: Użyj rozszerzenia usługi Azure Active Directory Privileged Identity Management po raz pierwszy zostanie wyświetlone za pomocą Kreatora zabezpieczeń. W tym artykule opisano kroki dotyczące korzystania z kreatora.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 02/27/2017
ms.author: rolyon
ms.custom: pim ; H1Hack27Feb2017
ms.openlocfilehash: 19eb2b36b217dc67fabcc3c2c4721fb13b2224ec
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39617010"
---
# <a name="using-the-security-wizard-in-azure-ad-privileged-identity-management"></a>Za pomocą Kreatora zabezpieczeń w usłudze Azure AD Privileged Identity Management 
Jeśli jesteś pierwszą osobą do uruchamiania usługi Azure Privileged Identity Management (PIM) dla Twojej organizacji, zobaczysz za pomocą kreatora. Kreator pomoże Ci zrozumieć zagrożenia dla bezpieczeństwa uprzywilejowanych tożsamości oraz jak używać aplikacji PIM, aby ograniczyć te zagrożenia. Nie musisz wprowadzać żadnych zmian istniejących przypisań ról w kreatorze, aby zrobić to później.

## <a name="what-to-expect"></a>Czego oczekiwać
Przed rozpoczęciem Twojej organizacji za pomocą usługi PIM są trwałe, wszystkie przypisania roli: użytkownicy są zawsze w ramach tych ról nawet wtedy, gdy nie ma obecnie potrzeby swoich uprawnień.  Pierwszym krokiem, który Kreator pokazuje listę ról z wysokim poziomem uprawnień i jak wielu użytkowników są obecnie dostępne w tych ról. Możesz przejść do określonej roli, aby uzyskać więcej informacji użytkownikom, jeśli jeden lub jeden z nich jest nieznane.

Drugiego kroku kreatora daje możliwość zmiany przypisania ról administratora.  

> [!WARNING]
> Jest ważne, że masz co najmniej jeden administrator globalny i więcej niż jeden administrator ról uprzywilejowanych przy użyciu konta organizacji (nie konta Microsoft). Jeśli istnieje tylko jeden administrator ról uprzywilejowanych, organizacja nie będzie można Zarządzanie usługą PIM, jeśli to konto zostanie usunięte.
> Ponadto przypisań ról zachować stały, jeśli użytkownik ma konto Microsoft (konto używane do logowania do usług firmy Microsoft, takich jak Skype i Outlook.com). Jeśli planowane jest wymagane uwierzytelnianie MFA do aktywacji dla tej roli, ten użytkownik zostanie zablokowany.
> 
> 

Po dokonaniu zmiany kreatora będą już widoczne. Następnym razem użytkownik lub inny administrator ról uprzywilejowanych używanie usługi PIM, zostanie wyświetlony pulpit nawigacyjny usługi PIM.  

* Jeśli chcesz dodać lub usunąć użytkowników z ról lub zmienić przypisania trwałego na kwalifikujących się Dowiedz się więcej o [jak dodawanie i usuwanie roli użytkownika](pim-how-to-add-role-to-user.md).
* Jeśli chcesz zapewnić większą liczbę użytkowników dostęp umożliwiający zarządzanie usługą PIM, Dowiedz się więcej o [udzielanie dostępu do zarządzania w usłudze PIM](pim-how-to-give-access-to-pim.md).

## <a name="next-steps"></a>Kolejne kroki
[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]

