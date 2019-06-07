---
title: Jak połączyć subskrypcję platformy Azure — usługa Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Przewodnik krok po kroku do włączenia opcji naliczania opłat dla dzierżawy usługi Azure AD B2C do subskrypcji platformy Azure.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 01/24/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d2440b638a9c8eb27505b747d26917de8032e6cd
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66510121"
---
# <a name="link-an-azure-subscription-to-an-azure-active-directory-b2c-tenant"></a>Połączyć subskrypcję platformy Azure z dzierżawy usługi Azure Active Directory B2C

> [!IMPORTANT]
> Aby uzyskać najnowsze informacje dotyczące użycia, rozliczeniach i cenach usługi Azure Active Directory (Azure AD) B2C, zobacz [cennik usługi Azure AD B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

Opłaty za użycie w usłudze Azure AD B2C są rozliczane z subskrypcją platformy Azure. Po utworzeniu dzierżawy usługi Azure AD B2C, administrator dzierżawy musi jawnie utworzyć łącze dzierżawy usługi Azure AD B2C z subskrypcją platformy Azure. W tym artykule przedstawiono, jak.

> [!NOTE]
> Naliczanie opłat za użycie usługi Azure AD B2C lub innych zasobów platformy Azure, łącznie z dodatkowymi zasobami usługi Azure AD B2C umożliwia subskrypcji połączonej z dzierżawą usługi Azure AD B2C.  Nie można dodać innych usług platformy Azure na podstawie licencji lub licencji usługi Office 365 w ramach dzierżawy usługi Azure AD B2C.

Link do subskrypcji można uzyskać, tworząc usługi Azure AD B2C "Zasób" w ramach docelowa subskrypcja platformy Azure. Wiele usługi Azure AD B2C "zasoby" mogą być tworzone w ramach pojedynczej subskrypcji platformy Azure wraz z innymi zasobami platformy Azure (na przykład maszyny wirtualne, Magazyn danych, LogicApps). Widać wszystkie zasoby w ramach subskrypcji, przechodząc do dzierżawy usługi Azure AD, która jest skojarzona subskrypcja.

Subskrypcje dostawcy rozwiązań w chmurze (CSP) obsługiwanych w usłudze Azure AD B2C. Funkcje są dostępne dla usługi Azure AD B2C i dla wszystkich zasobów platformy Azure przy użyciu interfejsów API lub portalu Azure. Administratorzy subskrypcji dostawcy CSP można połączyć, przenieść, a następnie usunięcie relacji z usługi Azure AD B2C w taki sam sposób, że została wykonana dla wszystkich zasobów platformy Azure. Skojarzenie między dzierżawy usługi Azure AD B2C i subskrypcji Azure CSP nie dotyczy zarządzania usługi Azure AD B2C, za pomocą kontroli dostępu opartej na rolach. Kontrola dostępu oparta na rolach odbywa się przy użyciu ról base dzierżawy, nie subskrypcji na podstawie ról.

Aby kontynuować, potrzebna jest ważnej subskrypcji platformy Azure.

## <a name="create-an-azure-ad-b2c-tenant"></a>Tworzenie dzierżawy usługi Azure AD B2C

Należy najpierw [tworzenie dzierżawy usługi Azure AD B2C](active-directory-b2c-get-started.md) chcesz połączyć subskrypcję. Pomiń ten krok, jeśli utworzono już dzierżawę usługi Azure AD B2C.

## <a name="open-azure-portal-in-the-azure-ad-tenant-that-shows-your-azure-subscription"></a>Otwórz portal Azure w ramach dzierżawy usługi Azure AD, pokazujący subskrypcji platformy Azure

Przejdź do dzierżawy usługi Azure AD, pokazujący subskrypcji platformy Azure. Otwórz [witryny Azure portal](https://portal.azure.com)i przełącz się do dzierżawy usługi Azure AD, pokazujący subskrypcji platformy Azure, czy chcesz użyć.

![Przełączanie do dzierżawy usługi Azure AD](./media/active-directory-b2c-how-to-enable-billing/SelectAzureADTenant.png)

## <a name="find-azure-ad-b2c-in-the-azure-marketplace"></a>Znajdowanie usługi Azure AD B2C w witrynie Azure Marketplace

Kliknij przycisk **Utwórz zasób** przycisku. W polu **Szukaj w witrynie Marketplace** wprowadź wartość `B2C`.

![Podświetlony przycisk Dodaj i tekst Azure AD B2C w wyszukiwaniu pola witryny marketplace](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c.png)

Na liście wyników wybierz **usługi Azure AD B2C**.

![Usługa Azure AD B2C wybrana na liście wyników](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c-result.png)

Wyświetlane są szczegóły dotyczące usługi Azure AD B2C. Aby rozpocząć konfigurowanie swojej nowej dzierżawy usługi Azure Active Directory B2C, kliknij przycisk **Utwórz**.

Na ekranie tworzenia zasobów wybierz **dzierżawy moją subskrypcję systemu Azure łączy istniejące usługi Azure AD B2C**.

## <a name="create-an-azure-ad-b2c-resource-within-the-azure-subscription"></a>Utwórz zasób usługi Azure AD B2C w ramach subskrypcji platformy Azure

W oknie dialogowym Tworzenie zasobów wybierz dzierżawę usługi Azure AD B2C z listy rozwijanej. Zobaczysz wszystkie dzierżaw, których jesteś administratorem globalnym i te, które nie są już połączone z subskrypcją.

Nazwa zasobu usługi Azure AD B2C zostaną wstępnie wybrane, aby dopasować nazwę domeny dzierżawy usługi Azure AD B2C.

W przypadku subskrypcji należy wybrać aktywną subskrypcją platformy Azure, którego jesteś administratorem.

Wybierz grupę zasobów i lokalizacja grupy zasobów. Wybrany na tym etapie nie ma wpływu na lokalizację dzierżawy usługi Azure AD B2C, wydajności lub stan rozliczeń.

![Utwórz zasób B2C](./media/active-directory-b2c-how-to-enable-billing/createresourceb2c.png)

## <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Zarządzanie zasobami dzierżawy usługi Azure AD B2C

Po pomyślnym utworzeniu zasobu usługi Azure AD B2C w ramach subskrypcji platformy Azure, powinien zostać wyświetlony nowy zasób typu "Dzierżawy B2C" dodany wraz z innymi zasobami platformy Azure.

Możesz użyć tego zasobu, aby:

- Przejdź do subskrypcji, aby przejrzeć informacje rozliczeniowe.
- Przejdź do dzierżawy usługi Azure AD B2C
- Prześlij żądanie pomocy technicznej
- Przenoszenie zasobu dzierżawy usługi Azure AD B2C do innej subskrypcji platformy Azure lub do innej grupy zasobów.

![Ustawienia zasób B2C](./media/active-directory-b2c-how-to-enable-billing/b2cresourcesettings.png)

## <a name="known-issues"></a>Znane problemy

### <a name="self-imposed-restrictions"></a>Samodzielnie narzuconego ograniczenia

Użytkownik może ustanowić regionalnych ograniczeń do tworzenia zasobów platformy Azure. To ograniczenie może uniemożliwić tworzenie zasobów usługi Azure AD B2C. Aby rozwiązać problem, może Poluzować to ograniczenie.

## <a name="next-steps"></a>Kolejne kroki

Gdy te kroki są ukończony dla każdego dzierżawcy usługi Azure AD B2C, Twoja subskrypcja platformy Azure jest rozliczana zgodnie ze swojego bezpośrednia platformy Azure lub umowy Enterprise Agreement.

W ramach wybranej subskrypcji platformy Azure, możesz przejrzeć użycia i szczegółów rozliczeń. Możesz również przejrzeć szczegóły użycia każdego dnia raportów za pomocą [użycia interfejsu API raportowania](active-directory-b2c-reference-usage-reporting-api.md).
