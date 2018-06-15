---
title: Zarejestruj się w usłudze Office 365 z kontem platformy Azure | Dokumentacja firmy Microsoft
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
ms.date: 11/03/2017
ms.author: cjiang
ms.openlocfilehash: bce4b58d4197eee34be43a4dc852c6f43e0e18b4
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
ms.locfileid: "33203378"
---
# <a name="sign-up-for-an-office-365-subscription-with-your-azure-account"></a>Załóż subskrypcji usługi Office 365 z Twoim kontem platformy Azure
Jeśli jest to Twoja subskrypcja platformy Azure, można użyć konta platformy Azure w celu uzyskania subskrypcji usługi Office 365. W przypadku części organizacji, która ma subskrypcji platformy Azure, można utworzyć subskrypcji usługi Office 365 dla użytkowników w istniejącej Azure usługi Active Directory (Azure AD). Utwórz konto usługi Office 365 przy użyciu konta mającego uprawnienia administratora globalnego lub administratorem rozliczeń w dzierżawie usługi Azure Active Directory. Aby uzyskać więcej informacji, zobacz [Sprawdź uprawnienia Moje konto w usłudze Azure AD](#RoleInAzureAD) i [przypisywanie ról administratorów w usłudze Azure Active Directory](../active-directory/active-directory-assign-admin-roles-azure-portal.md).

Jeśli masz już konto usługi Office 365 oraz subskrypcji platformy Azure, możesz [skojarzyć dzierżawy usługi Office 365 z subskrypcją platformy Azure](billing-add-office-365-tenant-to-azure-subscription.md).

## <a name="get-an-office-365-subscription-by-using-your-azure-account"></a>Uzyskiwanie subskrypcji usługi Office 365 przy użyciu konta platformy Azure

1. Przejdź do [stronę produktu usługi Office 365](https://products.office.com/business)i wybierz plan.
2. Kliknij przycisk **Zaloguj** w prawym górnym rogu strony.

    ![Zrzut ekranu przedstawiający stronę wersji próbnej usługi Office 365](./media/billing-use-existing-azure-account-office-365-subscription/12-office-365-trial-page.png)
3. Zaloguj się przy użyciu poświadczeń konta platformy Azure. Jeśli tworzysz subskrypcję dla swojej organizacji, należy użyć konta platformy Azure, który jest członkiem roli Administrator globalny lub administrator rozliczeń katalogu w dzierżawie usługi Azure Active Directory.

    ![Zrzut ekranu usługi Office 365, logowanie](./media/billing-use-existing-azure-account-office-365-subscription/13-office-365-sign-in.png)
4. Kliknij przycisk **Wypróbuj teraz**.

    ![Zrzut ekranu potwierdzenie zamówienia dla usługi Office 365.](./media/billing-use-existing-azure-account-office-365-subscription/14-office-365-confirm-your-order.png)
5. Na stronie Potwierdzenie dostarczenia kolejności kliknij **Kontynuuj**.

    ![Zrzut ekranu przedstawiający odbieranie kolejności usługi Office 365](./media/billing-use-existing-azure-account-office-365-subscription/15-office-365-order-receipt.png)

Teraz wszystko jest gotowe. Jeśli utworzono subskrypcji usługi Office 365 w organizacji, wykonaj następujące kroki, aby sprawdzić, teraz są użytkownicy usługi Azure AD w usłudze Office 365.

1. Otwórz Centrum administracyjne usługi Office 365.
2. Rozwiń węzeł **użytkowników**, a następnie kliknij przycisk **aktywni użytkownicy**.

    ![Zrzut ekranu przedstawiający użytkownicy Centrum administracyjnego usługi Office 365](./media/billing-use-existing-azure-account-office-365-subscription/16-office-365-admin-center-users.png)

Po utworzeniu konta subskrypcji usługi Office 365 jest dodawany do tego samego wystąpienia usługi Azure Active Directory, należącego do Twojej subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [więcej informacji na temat subskrypcji platformy Azure i usługi Office 365](billing-use-existing-office-365-account-azure-subscription.md#more-about-subs) i [jak subskrypcje platformy Azure są kojarzone z usługi Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md).

## <a id="RoleInAzureAD"></a>Sprawdź uprawnienia Moje konto w usłudze Azure AD
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Kliknij przycisk **wszystkie usługi**, a następnie wyszukaj **usługi Active Directory**.

    ![Zrzut ekranu z usługi Active Directory w portalu Azure](./media/billing-use-existing-azure-account-office-365-subscription/billing-more-services-active-directory.png)
3. Kliknij przycisk **użytkowników i grup** > **wszyscy użytkownicy**.
4. Wybierz nazwę użytkownika. 

    ![Zrzut ekranu pokazujący użytkowników usługi Azure Active Directory](./media/billing-use-existing-azure-account-office-365-subscription/billing-users-groups.png)

5. Kliknij przycisk **roli katalogu**.
  
    ![Zrzut ekranu pokazujący roli katalogu portalu platformy Azure](./media/billing-use-existing-azure-account-office-365-subscription/billing-user-directory-role.png)
6.  Rola **administratora globalnego** lub **administratorem ograniczonym** > **administrator rozliczeń** jest wymagane do utworzenia subskrypcji usługi Office 365 dla użytkowników w istniejącej Azure usługi Active Directory.

    ![Zrzut ekranu pokazujący usługi Azure directory portalu rolę administratora rozliczeń](./media/billing-use-existing-azure-account-office-365-subscription/billing-directoryrole-limited.png)

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną.
Jeśli nadal potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) uzyskać szybkie rozwiązanie problemu. 
