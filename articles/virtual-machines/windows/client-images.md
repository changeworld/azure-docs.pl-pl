---
title: Korzystanie z obrazów klientów Windows na platformie Azure | Dokumentacja firmy Microsoft
description: Jak wdrożyć Windows 7, Windows 8 lub Windows 10 na platformie Azure dla scenariuszy deweloperskich lub testowych za pomocą korzyści z subskrypcji programu Visual Studio
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: 91c3880a-cede-44f1-ae25-f8f9f5b6eaa4
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: f791b17f2729af3efd2dff5d7884a168f8377154
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61403100"
---
# <a name="use-windows-client-in-azure-for-devtest-scenarios"></a>Używać klienta Windows na platformie Azure na potrzeby scenariuszy tworzenia i testowania
Możesz użyć Windows 7, Windows 8 lub Windows 10 Enterprise (x64) na platformie Azure na potrzeby scenariuszy deweloperskich/testowych, pod warunkiem, że masz odpowiednią subskrypcję programu Visual Studio (dawniej MSDN). W tym artykule opisano wymagania kwalifikacyjne dotyczące uruchamiania Windows 7, Windows 8.1, Windows 10 Enterprise na platformie Azure i korzystania z następujących obrazów w galerii platformy Azure.

![Szczegółowe informacje dotyczące obrazu z witryny Azure portal](./media/client-images/windows-client-msdn-images.png) 

> [!NOTE]
> Windows 10 Pro i Windows 10 Pro N obraz w galerii systemu Azure, można znaleźć na stronie [sposób wdrażania systemu Windows 10 na platformie Azure z praw hostingu wielodostępnej](windows-desktop-multitenant-hosting-deployment.md)
>![szczegółów Pro obrazu z witryny Azure portal](./media/client-images/windows-client-pro-images.png) 
>

## <a name="subscription-eligibility"></a>Uprawnień do subskrypcji
Aktywni subskrybenci programu Visual Studio (osób nabyło licencji subskrypcji programu Visual Studio), można użyć klienta Windows do programowania i testowania. Można użyć klienta Windows własnego sprzętu oraz maszyn wirtualnych platformy Azure w dowolnym rodzaju subskrypcji platformy Azure. Klienta Windows może nie być wdrożone używane na platformie Azure do użytku produkcyjnego normalne lub używane przez osoby, które nie są aktywni subskrybenci programu Visual Studio.

Dla Twojej wygody niektórych obrazów systemu Windows 10 są dostępne w galerii platformy Azure w ramach [kwalifikujących się ofertach tworzenia i testowania](#eligible-offers). Subskrybenci programu Visual Studio w ramach dowolnego typu oferty, może również [odpowiednio przygotowywanie i tworzenie](prepare-for-upload-vhd-image.md) 64-bitowy obraz Windows 7, Windows 8 lub Windows 10 i następnie [przekazać na platformę Azure](upload-generalized-managed.md). Użycie pozostaje ograniczony do tworzenia i testowania aktywnych subskrybentów programu Visual Studio.

## <a name="eligible-offers"></a>Kwalifikujących się ofert
W poniższej tabeli przedstawiono oferty identyfikatorów, które kwalifikują się do wdrażania systemu Windows 10 za pośrednictwem galerii systemu Azure. Obrazy systemu Windows 10 są widoczne tylko dla następujących ofert. Subskrybenci programu Visual Studio potrzebujących umożliwiającym uruchomienie klienta Windows w typie inną ofertę wymagają [odpowiednio przygotowywanie i tworzenie](prepare-for-upload-vhd-image.md) 64-bitowy obraz Windows 7, Windows 8 lub Windows 10 i [następnie przekazać na platformę Azure](upload-generalized-managed.md).

| Nazwa oferty | Numer oferty | Obrazy dostępne klienta |
|:--- |:---:|:---:|
| [Płatność za rzeczywiste użycie, tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0023p/) |0023P |Windows 10 |
| [Subskrybenci programu Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/) |0029P |Windows 10 |
| [Subskrybenci programu Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) |0059P |Windows 10 |
| [Subskrybenci programu Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) |0060P |Windows 10 |
| [Visual Studio Premium z subskrypcją MSDN (korzyści)](https://azure.microsoft.com/offers/ms-azr-0061p/) |0061P |Windows 10 |
| [Subskrybenci programu Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |0063P |Windows 10 |
| [Subskrybenci programu Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |0064P |Windows 10 |
| [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/) |0148P |Windows 10 |

## <a name="check-your-azure-subscription"></a>Sprawdź swoją subskrypcję platformy Azure
Jeśli nie znasz Identyfikatora oferty, możesz uzyskać go w witrynie Azure portal w jednym z następujących dwóch sposobów:  

- Na *subskrypcje* okna:

  ![Szczegóły Identyfikatora oferty w witrynie Azure portal](./media/client-images/offer-id-azure-portal.png) 

- Ewentualnie kliknij przycisk **rozliczeń** a następnie kliknij przycisk identyfikatora subskrypcji. Oferta identyfikator, który będzie widoczny w *rozliczeń* okna.

Można również wyświetlić Identyfikatora oferty z [kartę "Subskrypcje"](https://account.windowsazure.com/Subscriptions) w portalu konta platformy Azure:

![Szczegóły Identyfikatora oferty w portalu konta usługi Azure](./media/client-images/offer-id-azure-account-portal.png) 

## <a name="next-steps"></a>Kolejne kroki
Teraz można wdrożyć maszyny wirtualne przy użyciu [PowerShell](quick-create-powershell.md), [szablonów usługi Resource Manager](ps-template.md), lub [programu Visual Studio](../../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

