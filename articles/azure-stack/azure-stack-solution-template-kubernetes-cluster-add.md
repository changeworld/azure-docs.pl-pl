---
title: Dodaj rozwiązanie Kubernetes w portalu Marketplace usługi Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać rozwiązania Kubernetes w portalu Azure Marketplace stosu.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: e89575323b87ba28ef4f062da098fea4f0e27035
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2019
ms.locfileid: "54264058"
---
# <a name="add-kubernetes-to-the-azure-stack-marketplace"></a>Dodaj rozwiązanie Kubernetes w portalu Marketplace usługi Azure Stack

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

> [!note]  
> Rozwiązanie Kubernetes w usłudze Azure Stack jest w wersji zapoznawczej.

Rozwiązania Kubernetes jako elementu portalu Marketplace możesz zaoferować użytkownikom. Użytkownikom można wdrożyć rozwiązania Kubernetes w jednej, skoordynowanej operacji.

Następujący artykuł przyjrzeć się przy użyciu szablonu usługi Azure Resource Manager, wdrażanie i Inicjowanie obsługi administracyjnej zasobów dla autonomicznego klastra usługi Kubernetes. Element Marketplace klastra Kubernetes 0.3.0 wymaga usługi Azure Stack w wersji 1808. Przed rozpoczęciem należy sprawdzić usługi Azure Stack i ustawienia globalnego dzierżawy usługi Azure. Zbieranie wymaganych informacji na temat usługi Azure Stack. Dodaj wymagane zasoby do swojej dzierżawy, a w portalu Azure Marketplace stosu. Klaster zależy od tego, serwer Ubuntu, niestandardowego skryptu i elementów rozwiązania Kubernetes w portalu marketplace.

## <a name="create-a-plan-an-offer-and-a-subscription"></a>Tworzenie planu, oferty i subskrypcji

Tworzenie planu, oferty i subskrypcji dla elementu portalu Marketplace platformy Kubernetes. Można także użyć istniejącego planu i oferty.

1. Zaloguj się do [portalu administratora.](https://adminportal.local.azurestack.external)

1. Utwórz plan jako podstawowy plan. Aby uzyskać instrukcje, zobacz [Tworzenie planu w usłudze Azure Stack](azure-stack-create-plan.md).

1. Utwórz ofertę. Aby uzyskać instrukcje, zobacz [Tworzenie oferty w usłudze Azure Stack](azure-stack-create-offer.md).

1. Wybierz **oferuje**i Znajdź utworzoną ofertę.

1. Wybierz **Przegląd** w bloku oferty.

1. Wybierz **zmiany stanu**. Wybierz pozycję **Publiczna**.

1. Wybierz **+ Utwórz zasób** > **oferty i plany** > **subskrypcji** Aby utworzyć nową subskrypcję.

    a. Wprowadź **nazwy wyświetlanej**.

    b. Wprowadź **użytkownika**. Użyj konta usługi Azure AD skojarzonego z dzierżawą.

    c. **Opis dostawcy**

    d. Ustaw **dzierżawy katalogu** do dzierżawy usługi Azure AD dla usługi Azure Stack. 

    e. Wybierz **oferują**. Wybierz nazwę oferty, który został utworzony. Zanotuj identyfikator subskrypcji.

## <a name="add-an-ubuntu-server-image"></a>Dodawanie obrazu systemu Ubuntu server

Dodaj poniższy obraz Ubuntu Server w portalu Marketplace:

1. Zaloguj się do [portalu administracyjnego](https://adminportal.local.azurestack.external).

1. Wybierz **wszystkich usług**, a następnie w obszarze **administracji** kategorii, wybierz opcję **zarządzania Marketplace**.

1. Wybierz **+ Dodaj na platformie Azure**.

1. Wprowadź polecenie `UbuntuServer`.

1. Wybierz najnowszą wersję serwera. Zapoznaj się z pełną wersją i upewnij się, że masz najnowszą wersję:
    - **Wydawca**: Canonical
    - **Oferty**: UbuntuServer
    - **Wersja**: 16.04.201806120 (lub nowszy)
    - **SKU**: 16.04-LTS

1. Wybierz **pobierania.**

## <a name="add-a-custom-script-for-linux"></a>Dodawanie niestandardowego skryptu dla systemu Linux

Dodaj usługi Kubernetes z witryny Marketplace:

1. Otwórz [portalu administracyjnego](https://adminportal.local.azurestack.external).

1. Wybierz **wszystkich usług** a następnie w obszarze **administracji** kategorii, wybierz opcję **zarządzania Marketplace**.

1. Wybierz **+ Dodaj na platformie Azure**.

1. Wprowadź polecenie `Custom Script for Linux`.

1. Wybierz odpowiedni skrypt, za pomocą następującego profilu:
    - **Oferty**: Niestandardowego skryptu dla systemu Linux w wersji 2.0
    - **Wersja**: 2.0.6 (lub nowszy)
    - **Wydawca**: Microsoft Corp

    > [!Note]  
    > Może być wymieniona więcej niż jedna wersja niestandardowego skryptu dla systemu Linux. Należy dodać najnowszej wersji elementu.

1. Wybierz **pobierania.**


## <a name="add-kubernetes-to-the-marketplace"></a>Dodaj rozwiązanie Kubernetes w portalu Marketplace

1. Otwórz [portalu administracyjnego](https://adminportal.local.azurestack.external).

1. Wybierz **wszystkich usług** a następnie w obszarze **administracji** kategorii, wybierz opcję **zarządzania Marketplace**.

1. Wybierz **+ Dodaj na platformie Azure**.

1. Wprowadź polecenie `Kubernetes`.

1. Wybierz pozycję `Kubernetes Cluster`.

1. Wybierz **pobierania.**

    > [!note]  
    > Może upłynąć pięć minut przez element portalu marketplace są wyświetlane w portalu Marketplace.

    ![Kubernetes](user/media/azure-stack-solution-template-kubernetes-deploy/marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes"></a>Aktualizowanie lub usuwanie rozwiązania Kubernetes 

Podczas aktualizowania elementu Kubernetes, należy usunąć element, który znajduje się w witrynie Marketplace. Można następnie postępuj zgodnie z instrukcjami w tym artykule, aby dodać rozwiązania Kubernetes w portalu Marketplace.

Aby usunąć element Kubernetes:

1. Połącz się do usługi Azure Stack przy użyciu programu PowerShell jako operator. Aby uzyskać instrukcje, zobacz [nawiązywanie połączenia z usługi Azure Stack przy użyciu programu PowerShell jako operator](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-admin).

2. Bieżący element klastra Kubernetes można znaleźć w galerii.

    ```PowerShell  
    Get-AzsGalleryItem | Select Name
    ```
    
3. Pamiętaj, takie jak nazwa bieżącego elementu `Microsoft.AzureStackKubernetesCluster.0.3.0`

4. Aby usunąć element, należy użyć następującego polecenia cmdlet programu PowerShell:

    ```PowerShell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.3.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>Kolejne kroki

[Wdrażanie usługi Kubernetes do usługi Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)

[Przegląd oferty usług w usłudze Azure Stack](azure-stack-offer-services-overview.md)
