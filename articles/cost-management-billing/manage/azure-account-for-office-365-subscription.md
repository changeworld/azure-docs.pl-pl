---
title: Rejestracja w usłudze Office 365 przy użyciu konta platformy Azure | Microsoft Docs
description: Dowiedz się, jak utworzyć subskrypcję usługi Office 365 przy użyciu konta platformy Azure
services: ''
documentationcenter: ''
author: JiangChen79
manager: adpick
editor: ''
tags: billing,top-support-issue
ms.assetid: ''
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: banders
ms.openlocfilehash: 034df0de027a50c61e5a7cab3d1e829446002c86
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75993598"
---
# <a name="sign-up-for-an-office-365-subscription-with-your-azure-account"></a>Rejestracja przy użyciu konta platformy Azure w celu uzyskania subskrypcji usługi Office 365
Jeśli jesteś subskrybentem platformy Azure, możesz użyć swojego konta platformy Azure do zarejestrowania subskrypcji usługi Office 365. Jeśli reprezentujesz organizację, która ma subskrypcję platformy Azure, możesz utworzyć subskrypcje usługi Office 365 dla użytkowników w istniejącej usłudze Azure Active Directory (Azure AD). Zarejestruj się w usłudze Office 365 przy użyciu konta z uprawnieniami administratora globalnego lub administratora rozliczeń w dzierżawie usługi Azure Active Directory. Aby uzyskać więcej informacji, zobacz [Sprawdzanie uprawnień konta w usłudze Azure AD](#RoleInAzureAD) i [Przypisywanie ról administratorów w usłudze Azure Active Directory](../../active-directory/users-groups-roles/directory-assign-admin-roles.md).

Jeśli masz już konto usługi Office 365 i subskrypcję platformy Azure, możesz [skojarzyć dzierżawę usługi Office 365 z subskrypcją platformy Azure](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="get-an-office-365-subscription-by-using-your-azure-account"></a>Uzyskiwanie subskrypcji usługi Office 365 przy użyciu konta platformy Azure

1. Przejdź do [strony produktu Office 365](https://products.office.com/business) i wybierz plan.
2. Kliknij pozycję **Zaloguj się** w prawym górnym rogu strony.

    ![Zrzut ekranu przedstawiający stronę wersji próbnej usługi Office 365](./media/azure-account-for-office-365-subscription/12-office-365-trial-page.png)
3. Zaloguj się przy użyciu poświadczeń konta platformy Azure. Jeśli tworzysz subskrypcję dla swojej organizacji, użyj konta platformy Azure, które ma uprawnienia roli katalogu Administrator globalny lub Administrator rozliczeń w dzierżawie usługi Azure Active Directory.

    ![Zrzut ekranu przedstawiający logowanie do usługi Office 365](./media/azure-account-for-office-365-subscription/13-office-365-sign-in.png)
4. Kliknij pozycję **Wypróbuj teraz**.

    ![Zrzut ekranu z potwierdzeniem zamówienia usługi Office 365.](./media/azure-account-for-office-365-subscription/14-office-365-confirm-your-order.png)
5. Na stronie potwierdzenia zamówienia kliknij pozycję **Kontynuuj**.

    ![Zrzut ekranu przedstawiający potwierdzenie zamówienia usługi Office 365](./media/azure-account-for-office-365-subscription/15-office-365-order-receipt.png)

Teraz wszystko jest już gotowe.
Jeśli utworzono subskrypcję usługi Office 365 dla organizacji, wykonaj następujące kroki, aby sprawdzić, czy użytkownicy usługi Azure AD mają dostęp do usługi Office 365.

1. Otwórz Centrum administracyjne platformy Microsoft 365.
2. Rozwiń węzeł **UŻYTKOWNICY**, a następnie kliknij pozycję **Aktywni użytkownicy**.

    ![Zrzut ekranu przedstawiający użytkowników Centrum administracyjnego platformy Microsoft 365](./media/azure-account-for-office-365-subscription/16-microsoft-365-admin-center-users.png)

Po zarejestrowaniu subskrypcja usługi Office 365 jest dodawana do tego samego wystąpienia usługi Azure Active Directory, do którego należy subskrypcja platformy Azure. Aby uzyskać więcej informacji, zobacz [Więcej informacji o subskrypcjach platformy Azure i usługi Office 365](office-365-account-for-azure-subscription.md#more-about-subs) i [Jak subskrypcje platformy Azure są kojarzone z usługą Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a id="RoleInAzureAD"></a>Sprawdzanie uprawnień konta w usłudze Azure AD
1. Zaloguj się do [portalu Azure](https://portal.azure.com/).
2. Kliknij pozycję **Wszystkie usługi**, a następnie wyszukaj usługę **Active Directory**.

    ![Zrzut ekranu przedstawiający usługę Active Directory w witrynie Azure Portal](./media/azure-account-for-office-365-subscription/billing-more-services-active-directory.png)
3. Kliknij pozycję **Użytkownicy i grupy** > **Wszyscy użytkownicy**.
4. Wybierz nazwę użytkownika.

    ![Zrzut ekranu przedstawiający użytkowników usługi Azure Active Directory](./media/azure-account-for-office-365-subscription/billing-users-groups.png)

5. Kliknij pozycję **Rola katalogu**.

    ![Zrzut ekranu przedstawiający rolę katalogu w witrynie Azure Portal](./media/azure-account-for-office-365-subscription/billing-user-directory-role.png)
6.  Aby utworzyć subskrypcję usługi Office 365 dla użytkowników w istniejącej usłudze Azure Active Directory, wymagana jest rola **Administrator globalny** lub rola **Administrator z ograniczonymi uprawnieniami** > **Administrator rozliczeń**.

    ![Zrzut ekranu przedstawiający rolę katalogu Administrator rozliczeń w witrynie Azure Portal](./media/azure-account-for-office-365-subscription/billing-directoryrole-limited.png)

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).
