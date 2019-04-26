---
title: Konta na platformie Azure przy użyciu konta usługi Office 365 | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzyć subskrypcji platformy Azure przy użyciu konta usługi Office 365
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
ms.date: 06/15/2018
ms.author: banders
ms.openlocfilehash: 44ff08c84b3e95a5b598ebf4279fc2ffc46313e3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60369524"
---
# <a name="sign-up-for-an-azure-subscription-with-your-office-365-account"></a>Zamów subskrypcję platformy Azure przy użyciu konta usługi Office 365
Jeśli masz subskrypcję usługi Office 365, można użyć konta usługi Office 365, aby utworzyć subskrypcję usługi Azure. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) przy użyciu usługi Office 365, nazwę użytkownika i hasło. Jeśli chcesz skonfigurować maszyn wirtualnych lub innych usług platformy Azure, należy zasubskrybować subskrypcji platformy Azure. Twoja subskrypcja platformy Azure można udostępniać innym osobom i [zarządzanie dostępem do zasobów i subskrypcji platformy Azure za pomocą kontroli dostępu opartej na rolach](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

Jeśli masz już konto usługi Office 365 i subskrypcję platformy Azure, zobacz [kojarzenie dzierżawy usługi Office 365 w ramach subskrypcji platformy Azure](billing-add-office-365-tenant-to-azure-subscription.md).

## <a name="get-an-azure-subscription-using-your-office-365-account"></a>Uzyskiwanie subskrypcji platformy Azure przy użyciu swojego konta usługi Office 365

Oszczędzaj czas i uniknąć rozprzestrzenianie konta rejestracji na platformie Azure przy użyciu usługi Office 365, nazwę użytkownika i hasło. 

1. Zarejestruj się pod adresem [Azure.com](https://account.azure.com/signup?offer=MS-AZR-0044p&appId=docs). 
2. Zaloguj się przy użyciu usługi Office 365 nazwa użytkownika i hasło. Konto, którego używasz, nie musi mieć uprawnienia administratora. Jeśli masz więcej niż jedno konto usługi Office 365, upewnij się, że używasz poświadczeń dla konta usługi Office 365, które chcesz skojarzyć z subskrypcją platformy Azure. 

   ![Zrzut ekranu przedstawiający stronę logowania.](./media/billing-use-existing-office-365-account-azure-subscription/billing-sign-in-with-office-365-account.png)

3. Wprowadź wymagane informacje i ukończyć proces rejestracji. Niektóre informacje mogą być wymagane, jeśli masz już konto usługi Office 365.

    ![Zrzut ekranu pokazujący formularz rejestracji.](./media/billing-use-existing-office-365-account-azure-subscription/billing-azure-sign-up-fill-information.png)

- Jeśli potrzebujesz dodać inne osoby w Twojej organizacji do subskrypcji platformy Azure, zobacz [wprowadzenie do zarządzania dostępem w witrynie Azure portal](../role-based-access-control/overview.md). 

## <a id="more-about-subs">Więcej informacji o subskrypcjach platformy Azure i usługi Office 365</a>
Usługi Office 365 i platformą Azure usługa Azure AD do zarządzania użytkownikami i subskrypcjami. Usługi Azure directory przypomina kontenera, w którym można pogrupować użytkowników i subskrypcji. Aby użyć tych samych kont użytkowników dla subskrypcji platformy Azure i usługi Office 365, należy się upewnić, że subskrypcji platformy Azure są tworzone w tym samym katalogu co w przypadku subskrypcji usługi Office 365. Mieć na uwadze następujące kwestie:

* Subskrypcja zostanie utworzona w katalogu
* Użytkownicy należą do katalogów
* Subskrypcja znajdzie się w katalogu użytkownika, który tworzy subskrypcję. Dlatego subskrypcji usługi Office 365 jest powiązany do tego samego konta, co subskrypcja systemu Azure.
* Subskrypcje platformy Azure należą do poszczególnych użytkowników w katalogu
* Subskrypcje usługi Office 365 są własnością sam katalog. Użytkownicy mający odpowiednie uprawnienia w katalogu mogą zarządzać tych subskrypcji.

![Zrzut ekranu pokazujący relacji katalogu, użytkownikami i subskrypcjami.](./media/billing-use-existing-office-365-account-azure-subscription/19-background-information.png)

Aby uzyskać więcej informacji, zobacz [jak subskrypcje platformy Azure są skojarzone z usługą Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://go.microsoft.com/fwlink/?linkid=2083458).