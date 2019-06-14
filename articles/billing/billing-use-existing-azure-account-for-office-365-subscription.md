---
title: Rejestracja w usłudze Office 365, przy użyciu konta platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć subskrypcję usługi Office 365 przy użyciu konta platformy Azure
services: ''
documentationcenter: ''
author: JiangChen79
manager: adpick
editor: ''
tags: billing,top-support-issue
ms.assetid: ''
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: banders
ms.openlocfilehash: b67f3c590be290515329af390b4d3d79a9746112
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60369885"
---
# <a name="sign-up-for-an-office-365-subscription-with-your-azure-account"></a>Zamów subskrypcję usługi Office 365 przy użyciu konta platformy Azure
Jeśli jesteś subskrybenta platformy Azure, można użyć konta platformy Azure w celu uzyskania subskrypcji usługi Office 365. Jeśli jesteś częścią organizacji, która ma subskrypcję platformy Azure, można utworzyć subskrypcji usługi Office 365 dla użytkowników w usłudze istniejącej usługi Azure Active Directory (Azure AD). Zarejestruj się do usługi Office 365 za pomocą konta mającego uprawnienia administratora globalnego lub administratorem rozliczeń w dzierżawie usługi Azure Active Directory. Aby uzyskać więcej informacji, zobacz [Sprawdź moje uprawnienia konta w usłudze Azure AD](#RoleInAzureAD) i [przypisywanie ról administratorów w usłudze Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

Jeśli masz już konto usługi Office 365 i subskrypcję platformy Azure, możesz to zrobić [kojarzenie dzierżawy usługi Office 365 w ramach subskrypcji platformy Azure](billing-add-office-365-tenant-to-azure-subscription.md).

## <a name="get-an-office-365-subscription-by-using-your-azure-account"></a>Uzyskiwanie subskrypcji usługi Office 365 przy użyciu konta platformy Azure

1. Przejdź do [strona produktu usługi Office 365](https://products.office.com/business)i wybierz plan.
2. Kliknij przycisk **Zaloguj** w prawym górnym rogu strony.

    ![Zrzut ekranu przedstawiający stronę wersji próbnej usługi Office 365](./media/billing-use-existing-azure-account-office-365-subscription/12-office-365-trial-page.png)
3. Zaloguj się przy użyciu poświadczeń konta platformy Azure. Jeśli tworzysz subskrypcję dla swojej organizacji, należy użyć konta platformy Azure, który jest administratorem globalnym lub administratorem rozliczeń w roli katalogu w dzierżawie usługi Azure Active Directory.

    ![Zrzut ekranu usługi Office 365 logowania](./media/billing-use-existing-azure-account-office-365-subscription/13-office-365-sign-in.png)
4. Kliknij przycisk **Wypróbuj teraz usługę**.

    ![Zrzut ekranu, który potwierdza zamówienia dla usługi Office 365.](./media/billing-use-existing-azure-account-office-365-subscription/14-office-365-confirm-your-order.png)
5. Na stronie potwierdzenia zamówienia kliknij **Kontynuuj**.

    ![Zrzut ekranu przedstawiający po otrzymaniu potwierdzenia zamówienia usługi Office 365](./media/billing-use-existing-azure-account-office-365-subscription/15-office-365-order-receipt.png)

Teraz wszystko jest gotowe.
Jeśli utworzono subskrypcję usługi Office 365 w Twojej organizacji, należy użyć następujące kroki do sprawdzenia, czy użytkownikom usługi Azure AD są teraz w usłudze Office 365.

1. Otwórz Centrum administracyjne usługi Microsoft 365.
2. Rozwiń **użytkowników**, a następnie kliknij przycisk **aktywni użytkownicy**.

    ![Zrzut ekranu przedstawiający użytkownicy Centrum administratora usługi Microsoft 365](./media/billing-use-existing-azure-account-office-365-subscription/16-microsoft-365-admin-center-users.png)

Po zarejestrowaniu się subskrypcji usługi Office 365 jest dodawany do tego samego wystąpienia usługi Azure Active Directory, której należy do subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [więcej informacji na temat subskrypcji platformy Azure i usługi Office 365](billing-use-existing-office-365-account-azure-subscription.md#more-about-subs) i [jak subskrypcje platformy Azure są skojarzone z usługą Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a id="RoleInAzureAD"></a>Sprawdź uprawnienia dla mojego konta w usłudze Azure AD
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Kliknij przycisk **wszystkich usług**, a następnie wyszukaj **usługi Active Directory**.

    ![Zrzut ekranu z usługi Active Directory w witrynie Azure portal](./media/billing-use-existing-azure-account-office-365-subscription/billing-more-services-active-directory.png)
3. Kliknij przycisk **użytkowników i grup** > **wszyscy użytkownicy**.
4. Wybierz nazwę użytkownika.

    ![Zrzut ekranu pokazujący użytkowników usługi Azure Active Directory](./media/billing-use-existing-azure-account-office-365-subscription/billing-users-groups.png)

5. Kliknij przycisk **roli w katalogu**.

    ![Zrzut ekranu przedstawiający rolę katalogu portalu platformy Azure](./media/billing-use-existing-azure-account-office-365-subscription/billing-user-directory-role.png)
6.  Rola **administratora globalnego** lub **ograniczony administrator** > **administrator rozliczeń** jest wymagana do utworzenia subskrypcji usługi Office 365 dla użytkowników Twoje istniejące usługi Azure Active Directory.

    ![Zrzut ekranu przedstawiający rolę katalogu portalu platformy Azure administratorem rozliczeń](./media/billing-use-existing-azure-account-office-365-subscription/billing-directoryrole-limited.png)

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://go.microsoft.com/fwlink/?linkid=2083458).
