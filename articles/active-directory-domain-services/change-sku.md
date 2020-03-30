---
title: Zmienianie jednostki SKU usług domenowych usługi Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak ować warstwę SKU dla domeny zarządzanej usług AD Azure, jeśli zmieniają się wymagania biznesowe
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: iainfou
ms.openlocfilehash: b65310569e95173b88dd0aa0dfe1dbacd86cc8fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126703"
---
# <a name="change-the-sku-for-an-existing-azure-ad-domain-services-managed-domain"></a>Zmienianie jednostki SKU dla istniejącej domeny usług domenowych usługi Azure AD

W usługach domenowych Usługi active directory platformy Azure (Usługi Azure AD DS) dostępne wyniki i funkcje są oparte na typie jednostki SKU. Te różnice funkcji obejmują częstotliwość tworzenia kopii zapasowych lub maksymalną liczbę jednokierunkowych wychodzących relacji zaufania lasu (obecnie w wersji zapoznawczej). Podczas tworzenia domeny zarządzanej wybierasz jednostkę SKU i można przełączać jednostki SKU w górę lub w dół, ponieważ twoje potrzeby biznesowe zmieniają się po wdrożeniu domeny zarządzanej. Zmiany w wymaganiach biznesowych mogą obejmować potrzebę częstszych kopii zapasowych lub tworzenia dodatkowych relacji zaufania lasu. Aby uzyskać więcej informacji na temat limitów i cen różnych jednostek SKU, zobacz [pojęcia jednostek SKU usług Azure AD DS][concepts-sku] i strony [cenowe usług Azure AD DS.][pricing]

W tym artykule pokazano, jak zmienić jednostkę SKU dla istniejącej domeny zarządzanej usługi Azure AD DS przy użyciu witryny Azure portal.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby ukończyć ten artykuł, potrzebne są następujące zasoby i uprawnienia:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem tylko w chmurze.
    * W razie potrzeby [utwórz dzierżawę usługi Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z kontem.][associate-azure-ad-tenant]
* Domena zarządzana usługami domenowymi Usługi Active Directory platformy Azure włączona i skonfigurowana w dzierżawie usługi Azure AD.
    * W razie potrzeby wykonaj samouczek, aby [utworzyć i skonfigurować wystąpienie usług domenowych Usługi domenowe Active Directory][create-azure-ad-ds-instance]platformy Azure .

## <a name="sku-change-limitations"></a>Ograniczenia zmiany usługi SKU

Po wdrożeniu domeny zarządzanej usług Azure AD DS można zmienić jednostki SKU w górę lub w dół. Jeśli jednak używasz lasu zasobów (obecnie w wersji zapoznawczej) i utworzono jednokierunkowe wychodzące relacje zaufania lasu z usług Azure AD DS do lokalnego środowiska usług AD DS, istnieją pewne ograniczenia dla operacji zmiany jednostki SKU. Jednostki SKU *Premium* i *Enterprise* definiują limit liczby relacji zaufania, które można utworzyć. Nie można zmienić na jednostkę SKU z niższym maksymalnym limitem niż obecnie skonfigurowano.

Przykład:

* Jeśli utworzono dwa relacje zaufania lasu w jednostce SKU *premium,* nie można zmienić w dół do *standardowej* jednostki SKU. *Standardowa* jednostka SKU nie obsługuje relacji zaufania lasu.
* Lub jeśli utworzono siedem relacji zaufania na jednostce SKU *Premium,* nie można zmienić w dół do *jednostki* SKU przedsiębiorstwa. Jednostka SKU *przedsiębiorstwa* obsługuje maksymalnie pięć relacji zaufania.

Aby uzyskać więcej informacji na temat tych limitów, zobacz [Funkcje i limity jednostek SKU usług Azure AD DS][concepts-sku].

## <a name="select-a-new-sku"></a>Wybierz nową jednostkę SKU

Aby zmienić jednostkę SKU dla domeny zarządzanej usług Azure AD DS przy użyciu witryny Azure Portal, wykonaj następujące kroki:

1. W górnej części witryny Azure portal wyszukaj i wybierz pozycję **Usługi domenowe usługi Azure AD .** Wybierz domenę zarządzana z listy, na przykład *aaddscontoso.com*.
1. W menu po lewej stronie strony usług Azure AD DS wybierz pozycję **Ustawienia > jednostce SKU**.

    ![Wybierz opcję menu SKU dla domeny zarządzanej usług Azure AD DS w witrynie Azure portal](media/change-sku/overview-change-sku.png)

1. Z menu rozwijanego wybierz jednostkę SKU, którą chcesz dla domeny zarządzanej usługą Azure AD DS. Jeśli masz las zasobów, nie możesz wybrać *standardowej* jednostki SKU, ponieważ relacje zaufania lasu są dostępne tylko w jednostce SKU *przedsiębiorstwa* lub wyższej.

    Wybierz żądaną jednostkę SKU z menu rozwijanego, a następnie wybierz pozycję **Zapisz**.

    ![Wybierz wymaganą jednostkę SKU z menu rozwijanego w witrynie Azure portal](media/change-sku/change-sku-selection.png)

Zmiana typu jednostki SKU może potrwać minutę lub dwie.

## <a name="next-steps"></a>Następne kroki

Jeśli masz las zasobów i chcesz utworzyć dodatkowe relacje zaufania po zmianie jednostki SKU, zobacz [Tworzenie wychodzącego zaufania lasu do domeny lokalnej w usłudze Azure AD DS (wersja zapoznawcza)][create-trust].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus
[create-trust]: tutorial-create-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/
