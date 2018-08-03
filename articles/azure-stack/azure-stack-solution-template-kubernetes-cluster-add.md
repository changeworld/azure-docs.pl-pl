---
title: Dodaj klaster Kubernetes w portalu Marketplace usługi Azure Stack | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: ed2d55b8346acb79563a882bbaf2f46110dcf1bb
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39442721"
---
# <a name="add-a-kubernetes-cluster-to-the-azure-stack-marketplace"></a>Dodaj klaster Kubernetes w portalu Marketplace usługi Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

> [!note]  
> Rozwiązanie Kubernetes usługi Azure Container Services (ACS) w usłudze Azure Stack znajduje się w prywatnej wersji zapoznawczej. Aby zażądać dostępu do elementu portalu Kubernetes Marketplace potrzebne do wykonania instrukcji w tym artykule [wniosek o uzyskanie dostępu do](https://aka.ms/azsk8).

Klaster usługi Kubernetes jako elementu portalu Marketplace możesz zaoferować użytkownikom. Użytkownikom można wdrożyć rozwiązania Kubernetes w jednej, skoordynowanej operacji.

Następujący artykuł przyjrzeć się przy użyciu szablonu usługi Azure Resource Manager, wdrażanie i Inicjowanie obsługi administracyjnej zasobów dla autonomicznego klastra usługi Kubernetes. Przed rozpoczęciem należy sprawdzić usługi Azure Stack i ustawienia globalnego dzierżawy usługi Azure. Zbieranie wymaganych informacji na temat usługi Azure Stack. Dodaj wymagane zasoby do swojej dzierżawy, a w portalu Azure Marketplace stosu. Klaster zależy od tego, serwer Ubuntu, niestandardowego skryptu i elementami klastra Kubernetes w portalu marketplace.

## <a name="create-a-plan-an-offer-and-a-subscription"></a>Tworzenie planu, oferty i subskrypcji

Tworzenie planu, oferty i subskrypcji dla elementu portalu Marketplace klastra Kubernetes. Można także użyć istniejącego planu i oferty.

1. Zaloguj się do [portalu administratora.](https://adminportal.local.azurestack.external)

1. Utwórz plan jako podstawowy plan. Aby uzyskać instrukcje, zobacz [Tworzenie planu w usłudze Azure Stack](azure-stack-create-plan.md).

1. Utwórz ofertę. Aby uzyskać instrukcje, zobacz [Tworzenie oferty w usłudze Azure Stack](azure-stack-create-offer.md).

1. Wybierz **oferuje**i Znajdź utworzoną ofertę.

1. Wybierz **Przegląd** w bloku oferty.

1. Wybierz **zmiany stanu**. Wybierz **publicznych**.

1. Wybierz **+ nowy** > **oferty i plany** > **subskrypcji** Aby utworzyć nową subskrypcję.

    a. Wprowadź **nazwy wyświetlanej**.

    b. Wprowadź **użytkownika**. Użyj konta usługi Azure AD skojarzonego z dzierżawą.

    c. **Opis dostawcy**

    d. Ustaw **dzierżawy katalogu** do dzierżawy usługi Azure AD dla usługi Azure Stack. 

    e. Wybierz **oferują**. Wybierz nazwę oferty, który został utworzony. Zanotuj identyfikator subskrypcji.

## <a name="add-an-ubuntu-server-image"></a>Dodawanie obrazu systemu Ubuntu server

Dodaj poniższy obraz Ubuntu Server w portalu Marketplace:

1. Zaloguj się do [portalu administracyjnego](https://adminportal.local.azurestack.external).

1. Wybierz **więcej usług** > **zarządzania Marketplace**.

1. Wybierz **+ Dodaj na platformie Azure**.

1. Wprowadź `UbuntuServer`.

1. Wybierz serwer przy użyciu następującego profilu:
    - **Wydawca**: Canonical
    - **Oferty**: UbuntuServer
    - **JEDNOSTKA SKU**: 16.04 LTS
    - **Wersja**: 16.04.201802220

    > [!Note]  
    > Może być wymieniona więcej niż jedną wersję Ubuntu Server 16.04 LTS. Musisz dodać wersję, która jest zgodna. Klaster Kubernetes wymaga dokładną wersję elementu.

1. Wybierz **pobierania.**

## <a name="add-a-custom-script-for-linux"></a>Dodawanie niestandardowego skryptu dla systemu Linux

Dodaj klaster Kubernetes z witryny Marketplace:

1. Otwórz [portalu administracyjnego](https://adminportal.local.azurestack.external).

1. Wybierz **więcej usług** > **zarządzania Marketplace**.

1. Wybierz **+ Dodaj na platformie Azure**.

1. Wprowadź `Custom Script for Linux`.

1. Wybierz odpowiedni skrypt, za pomocą następującego profilu:
    - **Oferty**: niestandardowego skryptu dla systemu Linux w wersji 2.0
    - **Wersja**: 2.0.3
    - **Wydawca**: Microsoft Corp.

    > [!Note]  
    > Może być wymieniona więcej niż jedna wersja niestandardowego skryptu dla systemu Linux. Musisz dodać wersję, która jest zgodna. Klaster Kubernetes wymaga dokładną wersję elementu.

1. Wybierz **pobierania.**


## <a name="add-the-kubernetes-cluster-to-the-marketplace"></a>Dodaj klaster Kubernetes w portalu Marketplace

1. Otwórz [portalu administracyjnego](https://adminportal.local.azurestack.external).

1. Wybierz **więcej usług** > **zarządzania Marketplace**.

1. Wybierz **+ Dodaj na platformie Azure**.

1. Wprowadź `Kubernetes Cluster`.

1. Wybierz pozycję `Kubernetes Cluster`.

1. Wybierz **pobierania.**

    > [!note]  
    > Może upłynąć pięć minut przez element portalu marketplace są wyświetlane w portalu Marketplace.

    ![Klaster Kubernetes](user\media\azure-stack-solution-template-kubernetes-deploy\marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes-cluster"></a>Aktualizowanie lub usuwanie klastra usługi Kubernetes 

Podczas aktualizowania elementu klastra Kubernetes, należy usunąć element, który znajduje się w witrynie Marketplace. Można następnie postępuj zgodnie z instrukcjami w tym artykule, aby dodać klaster Kubernetes w portalu Marketplace.

Aby usunąć element klastra Kubernetes:

1. Pamiętaj, takie jak nazwa bieżącego elementu `Microsoft.AzureStackKubernetesCluster.0.1.0`

1. Nawiązywanie połączenia usługi Azure Stack przy użyciu programu PowerShell.

1. Aby usunąć element, należy użyć następującego polecenia cmdlet programu PowerShell:

    ```PowerShell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.1.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>Kolejne kroki

[Wdrażanie klastra Kubernetes do usługi Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)



[Przegląd oferty usług w usłudze Azure Stack](azure-stack-offer-services-overview.md)