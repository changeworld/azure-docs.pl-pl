---
title: Tworzenia konta platformy Azure z kontem usługi Office 365 | Dokumentacja firmy Microsoft
description: Informacje o sposobie tworzenia subskrypcji platformy Azure przy użyciu konta usługi Office 365
services: ''
documentationcenter: ''
author: JiangChen79
manager: adpick
editor: ''
tags: billing,top-support-issue
ms.assetid: 129cdf7a-2165-483d-83e4-8f11f0fa7f8b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 11/03/2017
ms.author: cjiang
ms.openlocfilehash: dfb39b809f9a1082682d269d8bd4c180c8a264ce
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
---
# <a name="sign-up-for-an-azure-subscription-with-your-office-365-account"></a>Załóż subskrypcji platformy Azure z Twoim kontem usługi Office 365
Jeśli masz subskrypcję usługi Office 365, można użyć konta usługi Office 365, można utworzyć subskrypcji platformy Azure. Zaloguj się do [portalu Azure](https://portal.azure.com/) przy użyciu usługi Office 365, nazwę użytkownika i hasło. Jeśli chcesz skonfigurować maszyn wirtualnych lub innych usług platformy Azure, musisz zarejestrować subskrypcji platformy Azure. Twoja subskrypcja platformy Azure można udostępniać innym użytkownikom i [kontroli dostępu opartej na rolach umożliwia zarządzanie dostępem do Twojej subskrypcji platformy Azure i zasoby](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

Jeśli masz już konto usługi Office 365 oraz subskrypcji platformy Azure, zobacz [skojarzyć dzierżawy usługi Office 365 z subskrypcją platformy Azure](billing-add-office-365-tenant-to-azure-subscription.md).

## <a name="get-an-azure-subscription-using-your-office-365-account"></a>Uzyskiwanie subskrypcji platformy Azure przy użyciu konta usługi Office 365

Zaoszczędzić czas i uniknąć mnożenie konta po utworzeniu konta platformy Azure przy użyciu usługi Office 365, nazwę użytkownika i hasło. 

1. Zarejestruj się w [witryny Azure.com](https://account.azure.com/signup?offer=MS-AZR-0044p&appId=docs). 
2. Zaloguj się przy użyciu usługi Office 365, nazwę użytkownika i hasło. Konto, którego używasz, nie trzeba mieć uprawnienia administratora. Jeśli masz więcej niż jedno konto usługi Office 365, upewnij się, że można używać poświadczeń dla konta usługi Office 365, które chcesz skojarzyć z subskrypcją platformy Azure. 

   ![Zrzut ekranu przedstawiający stronę logowania.](./media/billing-use-existing-office-365-account-azure-subscription/billing-sign-in-with-office-365-account.png)

3. Wprowadź wymagane informacje i ukończyć proces tworzenia konta. Niektóre informacje mogą być wymagane, jeśli masz już konto usługi Office 365.

    ![Zrzut ekranu pokazujący wypełnieniu formularza.](./media/billing-use-existing-office-365-account-azure-subscription/billing-azure-sign-up-fill-information.png)

- Jeśli konieczne jest dodanie innych osób w organizacji z subskrypcją platformy Azure, zobacz [wprowadzenie do zarządzania dostępem w portalu Azure](../role-based-access-control/overview.md). 

## <a id="more-about-subs">Więcej informacji na temat subskrypcji platformy Azure i usługi Office 365</a>
Office 365 i Azure umożliwia zarządzanie użytkownikami i subskrypcji usługi Azure AD. Katalog platformy Azure przypomina kontenera, w którym można pogrupować użytkowników i subskrypcje. Aby użyć tego samego konta użytkowników dla subskrypcji platformy Azure i usługi Office 365, należy się upewnić, że subskrypcji platformy Azure są tworzone w tym samym katalogu co subskrypcji usługi Office 365. Mieć na uwadze następujące kwestie:

* Subskrypcja jest tworzony w katalogu
* Użytkownicy należą do katalogów
* Subskrypcja znajdzie się w katalogu użytkownika, który tworzy subskrypcję. Dlatego subskrypcji usługi Office 365 zostaje związana z tym samym kontem Twojej subskrypcji platformy Azure.
* Subskrypcje platformy Azure są własnością poszczególnych użytkowników w katalogu
* Sam katalog należy do subskrypcji usługi Office 365. Użytkownicy mający odpowiednie uprawnienia w katalogu można zarządzać te subskrypcje.

![Zrzut ekranu pokazujący relacji katalogu, użytkowników i subskrypcje.](./media/billing-use-existing-office-365-account-azure-subscription/19-background-information.png)

Aby uzyskać więcej informacji, zobacz [jak subskrypcje platformy Azure są kojarzone z usługi Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md).

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną.
Jeśli nadal potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) uzyskać szybkie rozwiązanie problemu. 
