---
title: Jak połączyć subskrypcji platformy Azure do usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Przewodnik krok po kroku, aby włączyć rozliczeń dla dzierżawy usługi Azure AD B2C do subskrypcji platformy Azure.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.date: 12/05/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 4dd99d492fe4f337509cacfce0e2b254e8465424
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712083"
---
# <a name="linking-an-azure-subscription-to-an-azure-ad-b2c-tenant"></a>Łączenie subskrypcji platformy Azure do dzierżawy usługi Azure AD B2C

> [!IMPORTANT]
> Najnowsze informacje dotyczące użycia rozliczeniach i cenach usługi Azure AD B2C jest na następującej stronie: [cennik usługi Azure AD B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

Opłaty za użycie dla usługi Azure AD B2C są rozliczane z subskrypcją platformy Azure. Podczas tworzenia dzierżawy usługi Azure AD B2C, administrator dzierżawy musi jawnie łącze dzierżawy usługi Azure AD B2C do subskrypcji platformy Azure. W tym artykule opisano sposób.

> [!NOTE]
> Tylko można subskrypcji połączone z dzierżawy usługi Azure AD B2C rozliczenia użycia usługi Azure AD B2C. Subskrypcja nie może służyć do dodania innych usług platformy Azure lub usługi Office 365 licencji *w ramach dzierżawy usługi Azure AD B2C*.

 Link do subskrypcji jest to osiągane przez utworzenie usługi Azure AD B2C "Zasób" w docelowym subskrypcji platformy Azure. W ramach jednej subskrypcji platformy Azure oraz innych zasobów platformy Azure (na przykład maszyny wirtualne, pamięci masowej, LogicApps) można utworzyć wiele usługi Azure AD B2C "zasoby". Można wyświetlić wszystkie zasoby w ramach subskrypcji, przechodząc do dzierżawy usługi Azure AD, która jest skojarzona subskrypcja.

Ważnej subskrypcji platformy Azure jest potrzebna, aby kontynuować.

## <a name="create-an-azure-ad-b2c-tenant"></a>Tworzenie dzierżawy usługi Azure AD B2C

Należy najpierw [tworzenie dzierżawy usługi Azure AD B2C](active-directory-b2c-get-started.md) chcesz połączyć subskrypcję. Jeśli utworzono już dzierżawy usługi Azure AD B2C, Pomiń ten krok.

## <a name="open-azure-portal-in-the-azure-ad-tenant-that-shows-your-azure-subscription"></a>Otwórz portal Azure w dzierżawie usługi Azure AD, pokazujący subskrypcji platformy Azure

Przejdź do dzierżawy usługi Azure AD, która zawiera subskrypcji platformy Azure. Otwórz [portalu Azure](https://portal.azure.com)i przejdź do dzierżawy usługi Azure AD, pokazujący subskrypcji platformy Azure, czy chcesz użyć.

![Przełączanie do dzierżawy usługi Azure AD](./media/active-directory-b2c-how-to-enable-billing/SelectAzureADTenant.png)

## <a name="find-azure-ad-b2c-in-the-azure-marketplace"></a>Znajdź usługi Azure AD B2C w portalu Azure Marketplace

Kliknij przycisk **Utwórz zasób** przycisku. W polu **Szukaj w witrynie Marketplace** wprowadź wartość `B2C`.

![Dodaj podświetlony przycisk i tekst usługi Azure AD B2C w wyszukiwania w polu marketplace](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c.png)

Na liście wyników wybierz **usługi Azure AD B2C**.

![Usługa Azure AD B2C wybranych z listy wyników](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c-result.png)

Wyświetlane są szczegóły dotyczące usługi Azure AD B2C. Aby rozpocząć konfigurowanie swojej nowej dzierżawy usługi Azure Active Directory B2C, kliknij przycisk **Utwórz**.

Na ekranie tworzenia zasobów wybierz **dzierżawy łącze istniejącej usługi Azure AD B2C do subskrypcji platformy Azure**.

## <a name="create-an-azure-ad-b2c-resource-within-the-azure-subscription"></a>Utwórz zasób usługi Azure AD B2C w ramach subskrypcji platformy Azure

W oknie dialogowym Tworzenie zasobu z listy rozwijanej wybierz opcję dzierżawy usługi Azure AD B2C. Są wyświetlane wszystkie dzierżawcami, które jest administratorem globalnym oraz te, które nie są już połączone z subskrypcją.

Nazwa zasobu usługi Azure AD B2C zostaną wstępnie wybrane, aby dopasować nazwę domeny dzierżawy usługi Azure AD B2C.

W przypadku subskrypcji należy wybrać aktywną subskrypcją platformy Azure, którego jesteś administratorem.

Wybierz grupę zasobów i lokalizacja grupy zasobów. Zaznaczenie w tym miejscu nie ma wpływu na lokalizację dzierżawy usługi Azure AD B2C, wydajność lub rozliczeń stanu.

![Utwórz zasób B2C](./media/active-directory-b2c-how-to-enable-billing/createresourceb2c.png)

## <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Zarządzanie zasobami dzierżawy usługi Azure AD B2C

Po pomyślnym utworzeniu zasobu usługi Azure AD B2C w ramach subskrypcji platformy Azure, powinien zostać wyświetlony nowy zasób typu "Dzierżawy B2C" dodane równolegle z innymi zasobami platformy Azure.

Możesz użyć tego zasobu do:

- Przejdź do subskrypcji, aby przejrzeć informacje związane z rozliczeniami.
- Przejdź do dzierżawy usługi Azure AD B2C
- Prześlij żądanie pomocy technicznej
- Przenoszenie zasobu dzierżawy usługi Azure AD B2C do innej subskrypcji platformy Azure lub innej grupie zasobów.

![Ustawienia zasobu B2C](./media/active-directory-b2c-how-to-enable-billing/b2cresourcesettings.png)

## <a name="known-issues"></a>Znane problemy

### <a name="csp-subscriptions"></a>Subskrypcje dostawcy usług Kryptograficznych

Obecnie dzierżawy usługi Azure AD B2C **nie** link do subskrypcji dostawcy usług Kryptograficznych.

### <a name="self-imposed-restrictions"></a>Własnym narzuconego ograniczenia.

Użytkownik może ustanowić regionalnych ograniczenia dotyczące tworzenia zasobów platformy Azure. To ograniczenie może uniemożliwić tworzenie zasobów usługi Azure AD B2C. Aby uniknąć, należy złagodzenie tego ograniczenia.

## <a name="next-steps"></a>Kolejne kroki

Po zakończeniu tych kroków dla każdego dzierżawcy usługi Azure AD B2C, subskrypcji platformy Azure są rozliczane zgodnie z szczegóły bezpośrednie Azure lub umowy Enterprise Agreement.

W ramach wybranej subskrypcji Azure, możesz przejrzeć użycia i szczegóły płatności. Można także przejrzeć szczegółowe dane użycia każdego dnia raportów za pomocą [użycia raportowania interfejsu API](active-directory-b2c-reference-usage-reporting-api.md).
