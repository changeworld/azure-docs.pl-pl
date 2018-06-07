---
title: Dodaj klaster Kubernetes do stosu Azure Marketplace | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać klaster Kubernetes w portalu Azure Marketplace stosu.
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
ms.date: 05/29/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: 0ba0e1f3d9e0f1cbb6ba4109a21fc29dc41df5fc
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34603457"
---
# <a name="add-a-kubernetes-cluster-to-the-azure-stack-marketplace"></a>Dodaj klaster Kubernetes do stosu Azure Marketplace

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

> [!note]  
> Kubernetes usługi kontenera platformy Azure (ACS) na stosie Azure znajduje się w prywatnej wersji zapoznawczej. Aby uzyskać dostęp do elementu Kubernetes Marketplace niezbędnych do wykonywania instrukcji w tym artykule [Prześlij żądanie do uzyskania dostępu](https://aka.ms/azsk8).

Możesz również zaoferować klastra Kubernetes jako element Marketplace dla użytkowników. Użytkownicy, można wdrożyć Kubernetes w jednej, skoordynowanej operacji.

Następujący artykuł przyjrzeć się przy użyciu szablonu usługi Azure Resource Manager do wdrażania i obsługi administracyjnej zasobów dla klastra Kubernetes autonomicznych. Przed rozpoczęciem Sprawdź stos Azure i ustawienia globalne dzierżawą platformy Azure. Zbieranie wymaganych informacji dotyczących stosu Azure. Dodaj zasoby niezbędne do dzierżawy i w portalu Azure Marketplace stosu. Klaster zależy od tego, Ubuntu server, skryptu niestandardowego i elementy Kubernetes klastra w witrynie marketplace.

## <a name="create-a-plan-an-offer-and-a-subscription"></a>Tworzenie planu, oferty i subskrypcji

Tworzenie planu, oferty i subskrypcji dla elementu Kubernetes klastra w witrynie Marketplace. Można również użyć istniejącego planu i oferty.

1. Zaloguj się do [portalu administracyjnego.](https://adminportal.local.azurestack.external)

2. Tworzenie planu jako planu podstawowego. Aby uzyskać instrukcje, zobacz [Tworzenie planu w stosie Azure](azure-stack-create-plan.md).

3. Utwórz ofertę. Aby uzyskać instrukcje, zobacz [utworzyć ofertę w stosie Azure](azure-stack-create-offer.md).

4. Wybierz **oferuje**i Znajdź oferta został utworzony.

5. Wybierz **omówienie** w bloku oferty.

6. Wybierz **zmiany stanu**. Wybierz **publicznego**.

7. Wybierz **+ nowy** > **oferuje oraz plany** > **subskrypcji** Aby utworzyć nową subskrypcję.

    a. Wprowadź **Nazwa wyświetlana**.

    b. Wprowadź **użytkownika**. Użyj konta usługi Azure AD skojarzony z dzierżawą.

    c. **Opis dostawcy**

    d. Ustaw **dzierżawy katalogu** do dzierżawy usługi Azure AD dla stosu Azure. 

    e. Wybierz **oferują**. Wybierz nazwę utworzonego oferty. Zanotuj identyfikator subskrypcji.

## <a name="add-an-ubuntu-server-image"></a>Dodawanie obrazu Ubuntu server

Dodaj poniższy obraz Ubuntu Server w portalu Marketplace:

1. Zaloguj się do [portalu administracyjnego](https://adminportal.local.azurestack.external).

2. Wybierz **więcej usług** > **zarządzania Marketplace**.

3. Wybierz **+ Dodaj z platformy Azure**.

4. Wprowadź `UbuntuServer`.

5. Wybierz serwer z profilem następujące:
    - **Wydawca**: kanoniczny
    - **Oferują**: UbuntuServer
    - **JEDNOSTKA SKU**: 16.04 LTS
    - **Wersja**: 16.04.201802220

    > [!Note]  
    > Więcej niż jedną wersję Ubuntu Server 16.04 LTS mogą być wyświetlone. Należy dodać wersja jest taka sama. Klaster Kubernetes wymaga dokładnej wersji elementu.

6. Wybierz **pobierania.**

## <a name="add-a-custom-script-for-linux"></a>Dodawanie niestandardowego skryptu dla systemu Linux

Dodaj klaster Kubernetes z witryny Marketplace:

1. Otwórz [portalu administracyjnego](https://adminportal.local.azurestack.external).

2. Wybierz **więcej usług** > **zarządzania Marketplace**.

3. Wybierz **+ Dodaj z platformy Azure**.

4. Wprowadź `Custom Script for Linux`.

5. Wybierz skrypt z profilem następujące:
    - **Oferują**: skryptu niestandardowego dla systemu Linux 2.0
    - **Wersja**: 2.0.3
    - **Wydawca**: Microsoft Corp.

    > [!Note]  
    > Mogą być wyświetlone więcej niż jedną wersję niestandardowego skryptu dla systemu Linux. Należy dodać wersja jest taka sama. Klaster Kubernetes wymaga dokładnej wersji elementu.

6. Wybierz **pobierania.**


## <a name="add-the-kubernetes-cluster-to-the-marketplace"></a>Dodaj klaster Kubernetes w portalu Marketplace

1. Otwórz [portalu administracyjnego](https://adminportal.local.azurestack.external).

2. Wybierz **więcej usług** > **zarządzania Marketplace**.

3. Wybierz **+ Dodaj z platformy Azure**.

4. Wprowadź `Kubernetes Cluster`.

5. Wybierz pozycję `Kubernetes Cluster`.

6. Wybierz **pobierania.**

    > [!note]  
    > Może upłynąć pięć minut dla elementu marketplace pojawią się w witrynie Marketplace.

    ![Kubernetes klastra](user\media\azure-stack-solution-template-kubernetes-deploy\marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes-cluster"></a>Aktualizowanie lub usuwanie klastra Kubernetes 

Podczas aktualizowania elementu Kubernetes klastra, należy usunąć element, który znajduje się w witrynie Marketplace. Następnie można wykonać instrukcji w tym artykule, aby dodać klaster Kubernetes w portalu Marketplace.

Aby usunąć element Kubernetes klastra:

1. Zanotuj nazwę bieżącego elementu, takich jak `Microsoft.AzureStackKubernetesCluster.0.1.0`

2. Podłącz do stosu Azure przy użyciu programu PowerShell.

3. Aby usunąć element, użyj następującego polecenia cmdlet programu PowerShell:

    ```PowerShell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.1.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>Kolejne kroki

[Wdrażanie klastra Kubernetes Azure stosu](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)



[Przegląd oferty usług Azure stosu](azure-stack-offer-services-overview.md)