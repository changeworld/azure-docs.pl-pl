---
title: Jak połączyć subskrypcję platformy Azure — Azure Active Directory B2C | Microsoft Docs
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
ms.openlocfilehash: 892f47b6acf22c62ce2290e2ede9d0bcd21eefc8
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065893"
---
# <a name="link-an-azure-subscription-to-an-azure-active-directory-b2c-tenant"></a>Łączenie subskrypcji platformy Azure z dzierżawą Azure Active Directory B2C

> [!IMPORTANT]
> Aby uzyskać najnowsze informacje o rozliczeniach użycia i cenach dla Azure Active Directory B2C (Azure AD B2C), zobacz [Cennik usługi Azure AD B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

Opłaty za użycie w usłudze Azure AD B2C są rozliczane z subskrypcją platformy Azure. Po utworzeniu dzierżawy usługi Azure AD B2C, administrator dzierżawy musi jawnie utworzyć łącze dzierżawy usługi Azure AD B2C z subskrypcją platformy Azure. W tym artykule przedstawiono, jak.

> [!NOTE]
> Naliczanie opłat za użycie usługi Azure AD B2C lub innych zasobów platformy Azure, łącznie z dodatkowymi zasobami usługi Azure AD B2C umożliwia subskrypcji połączonej z dzierżawą usługi Azure AD B2C.  Nie można dodać innych usług platformy Azure na podstawie licencji lub licencji usługi Office 365 w ramach dzierżawy usługi Azure AD B2C.

Link do subskrypcji można uzyskać, tworząc usługi Azure AD B2C "Zasób" w ramach docelowa subskrypcja platformy Azure. Wiele usługi Azure AD B2C "zasoby" mogą być tworzone w ramach pojedynczej subskrypcji platformy Azure wraz z innymi zasobami platformy Azure (na przykład maszyny wirtualne, Magazyn danych, LogicApps). Widać wszystkie zasoby w ramach subskrypcji, przechodząc do dzierżawy usługi Azure AD, która jest skojarzona subskrypcja.

Subskrypcje dostawcy rozwiązań w chmurze (CSP) platformy Azure są obsługiwane w Azure AD B2C. Ta funkcja jest dostępna przy użyciu interfejsów API lub Azure Portal dla Azure AD B2C i dla wszystkich zasobów platformy Azure. Administratorzy subskrypcji CSP mogą łączyć, przenosić i usuwać relacje z Azure AD B2C w taki sam sposób, jak w przypadku wszystkich zasobów platformy Azure. Skojarzenie między dzierżawą Azure AD B2C i subskrypcją dostawcy usług kryptograficznych platformy Azure nie ma wpływ na zarządzanie Azure AD B2C przy użyciu kontroli dostępu opartej na rolach. Kontrola dostępu oparta na rolach jest realizowana przy użyciu ról opartych na dzierżawie, a nie ról opartych na subskrypcji.

Aby kontynuować, potrzebna jest ważnej subskrypcji platformy Azure.

## <a name="create-an-azure-ad-b2c-tenant"></a>Tworzenie dzierżawy usługi Azure AD B2C

Należy najpierw [tworzenie dzierżawy usługi Azure AD B2C](active-directory-b2c-get-started.md) chcesz połączyć subskrypcję. Pomiń ten krok, jeśli utworzono już dzierżawę usługi Azure AD B2C.

## <a name="open-azure-portal-in-the-azure-ad-tenant-that-shows-your-azure-subscription"></a>Otwórz portal Azure w ramach dzierżawy usługi Azure AD, pokazujący subskrypcji platformy Azure

Przejdź do dzierżawy usługi Azure AD, pokazujący subskrypcji platformy Azure. Otwórz [witryny Azure portal](https://portal.azure.com)i przełącz się do dzierżawy usługi Azure AD, pokazujący subskrypcji platformy Azure, czy chcesz użyć.

![Przełączanie do dzierżawy usługi Azure AD](./media/active-directory-b2c-how-to-enable-billing/SelectAzureADTenant.png)

## <a name="find-azure-ad-b2c-in-the-azure-marketplace"></a>Znajdowanie usługi Azure AD B2C w witrynie Azure Marketplace

Kliknij przycisk **Utwórz zasób** przycisku. W polu **Wyszukaj w witrynie Marketplace** wprowadź `Active Directory B2C`wartość.

![Zrzut ekranu portalu z elementem "Active Directory B2C" w wyszukiwaniu w portalu Marketplace](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c.png)

Na liście wyników wybierz **usługi Azure AD B2C**.

![Usługa Azure AD B2C wybrana na liście wyników](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c-result.png)

Wyświetlane są szczegóły dotyczące usługi Azure AD B2C. Aby rozpocząć konfigurowanie swojej nowej dzierżawy usługi Azure Active Directory B2C, kliknij przycisk **Utwórz**.

Na ekranie tworzenia zasobów wybierz **dzierżawy moją subskrypcję systemu Azure łączy istniejące usługi Azure AD B2C**.

## <a name="create-an-azure-ad-b2c-resource-within-the-azure-subscription"></a>Utwórz zasób usługi Azure AD B2C w ramach subskrypcji platformy Azure

W oknie dialogowym Tworzenie zasobów wybierz dzierżawę usługi Azure AD B2C z listy rozwijanej. Zobaczysz wszystkie dzierżaw, których jesteś administratorem globalnym i te, które nie są już połączone z subskrypcją.

Nazwa zasobu usługi Azure AD B2C zostaną wstępnie wybrane, aby dopasować nazwę domeny dzierżawy usługi Azure AD B2C.

W przypadku subskrypcji należy wybrać aktywną subskrypcją platformy Azure, którego jesteś administratorem.

Wybierz grupę zasobów i lokalizacja grupy zasobów. Wybrany na tym etapie nie ma wpływu na lokalizację dzierżawy usługi Azure AD B2C, wydajności lub stan rozliczeń.

![Strona tworzenia zasobów Azure AD B2C w programie Azure Portal](./media/active-directory-b2c-how-to-enable-billing/createresourceb2c.png)

## <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Zarządzanie zasobami dzierżawy usługi Azure AD B2C

Po pomyślnym utworzeniu zasobu usługi Azure AD B2C w ramach subskrypcji platformy Azure, powinien zostać wyświetlony nowy zasób typu "Dzierżawy B2C" dodany wraz z innymi zasobami platformy Azure.

Możesz użyć tego zasobu, aby:

- Przejdź do subskrypcji, aby przejrzeć informacje rozliczeniowe.
- Przejdź do dzierżawy usługi Azure AD B2C
- Prześlij żądanie pomocy technicznej
- Przenoszenie zasobu dzierżawy usługi Azure AD B2C do innej subskrypcji platformy Azure lub do innej grupy zasobów.

![Strona ustawień zasobów B2C w Azure Portal](./media/active-directory-b2c-how-to-enable-billing/b2cresourcesettings.PNG)

## <a name="change-the-azure-ad-b2c-tenant-billing-subscription"></a>Zmień subskrypcję rozliczeń dzierżawy Azure AD B2C

Dzierżawy Azure AD B2C mogą zostać przeniesione do innej subskrypcji, jeśli subskrypcje źródłowe i docelowe znajdują się w tej samej dzierżawie Azure Active Directory.

Aby dowiedzieć się, jak przenieść zasoby platformy Azure, takie jak dzierżawa Azure AD B2C do innej subskrypcji, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/resource-group-move-resources.md).

Przed rozpoczęciem przenoszenia należy przeczytać cały artykuł, aby w pełni zrozumieć ograniczenia i wymagania dotyczące takiego przeniesienia. Oprócz instrukcji dotyczących przenoszenia zasobów zawiera informacje o kluczowym znaczeniu, takie jak lista kontrolna przedniesiona i sposób sprawdzania poprawności operacji przenoszenia.

## <a name="known-issues"></a>Znane problemy

### <a name="self-imposed-restrictions"></a>Samodzielnie narzuconego ograniczenia

Użytkownik może ustanowić regionalnych ograniczeń do tworzenia zasobów platformy Azure. To ograniczenie może uniemożliwić tworzenie zasobów usługi Azure AD B2C. Aby rozwiązać problem, może Poluzować to ograniczenie.

## <a name="next-steps"></a>Kolejne kroki

Gdy te kroki są ukończony dla każdego dzierżawcy usługi Azure AD B2C, Twoja subskrypcja platformy Azure jest rozliczana zgodnie ze swojego bezpośrednia platformy Azure lub umowy Enterprise Agreement.

W ramach wybranej subskrypcji platformy Azure, możesz przejrzeć użycia i szczegółów rozliczeń. Możesz również przejrzeć szczegóły użycia każdego dnia raportów za pomocą [użycia interfejsu API raportowania](active-directory-b2c-reference-usage-reporting-api.md).
