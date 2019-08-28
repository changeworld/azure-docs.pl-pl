---
title: Rozszerzenia i funkcje maszyny wirtualnej platformy Azure | Microsoft Docs
description: Informacje o rozszerzeniach maszyn wirtualnych platformy Azure i sposobach ich używania z maszynami wirtualnymi platformy Azure
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: roiyz
ms.openlocfilehash: 8c63df2d9a7e398fb9b67edd3b57a3ba06cbe7a1
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70084323"
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Rozszerzenia i funkcje maszyny wirtualnej platformy Azure
Rozszerzenia maszyny wirtualnej platformy Azure to małe aplikacje, które udostępniają zadania konfiguracji po wdrożeniu i automatyzacji na maszynach wirtualnych platformy Azure, można użyć istniejących obrazów, a następnie dostosować je w ramach wdrożeń, dzięki czemu możesz uzyskać niestandardową firmę Kompilowanie obrazów.

Platforma Azure obsługuje wiele rozszerzeń, które przedziały od konfiguracji maszyny wirtualnej, monitorowania, zabezpieczeń i narzędzi. Wydawcy pobierają aplikację, zaznaczają ją w rozszerzeniu i upraszczają instalację, dlatego należy podać obowiązkowe parametry. 

 Jeśli aplikacja w repozytorium rozszerzeń nie istnieje, można użyć rozszerzenia niestandardowego skryptu i skonfigurować maszynę wirtualną przy użyciu własnych skryptów i poleceń, jeśli nie istnieją.

Przykłady kluczowych scenariuszy, w których rozszerzenia są używane dla:
* Konfiguracja maszyny wirtualnej pozwala zainstalować agentów konfiguracji maszyny wirtualnej i skonfigurować maszynę wirtualną przy użyciu programu PowerShell DSC (Konfiguracja żądanego stanu), Chef, Puppet i niestandardowych rozszerzeń skryptów. 
* Produkty AV, takie jak Symantec, ESET.
* Narzędzie do luk w zabezpieczeniach maszyny wirtualnej, takie jak Qualys, Rapid7, HPE.
* Narzędzia do monitorowania maszyn wirtualnych i aplikacji, takie jak DynaTrace, Azure Network Watcher, Site24x7 i rozwiązania Stackify.

Rozszerzenia można powiązać z nowym wdrożeniem maszyny wirtualnej. Mogą na przykład być częścią większego wdrożenia, konfigurowania aplikacji na maszynach wirtualnych lub uruchamiania ich na wszystkich obsługiwanych systemach obsługujących rozszerzenia po wdrożeniu.

## <a name="how-can-i-find-what-extensions-are-available"></a>Jak mogę znaleźć dostępne rozszerzenia?
Dostępne rozszerzenia można wyświetlić w bloku maszyny wirtualnej w portalu, w obszarze rozszerzenia reprezentuje ona tylko niewielką ilość, aby uzyskać pełną listę, można użyć narzędzi interfejsu wiersza polecenia, zobacz odnajdywanie [rozszerzeń maszyn wirtualnych dla systemu Linux](features-linux.md) i [odnajdywanie rozszerzeń maszyn wirtualnych w systemie Windows](features-windows.md).

## <a name="how-can-i-install-an-extension"></a>Jak mogę zainstalować rozszerzenie?
Rozszerzenia maszyny wirtualnej platformy Azure można zarządzać przy użyciu interfejsu wiersza polecenia platformy Azure, Azure PowerShell, szablonów Azure Resource Manager i Azure Portal. Aby wypróbować rozszerzenie, możesz przejść do Azure Portal, wybrać rozszerzenie niestandardowego skryptu, a następnie przekazać polecenie/skrypt i uruchomić rozszerzenia.

Jeśli chcesz mieć to samo rozszerzenie dodane w portalu według interfejsu wiersza polecenia lub szablonu Menedżer zasobów, zobacz inną dokumentację rozszerzenia, taką jak rozszerzenie [niestandardowego skryptu systemu Windows](custom-script-windows.md) i [rozszerzenia niestandardowego skryptu](custom-script-linux.md)w systemie Linux.

## <a name="how-do-i-manage-extension-application-lifecycle"></a>Jak mogę zarządzać cyklem życia aplikacji rozszerzenia?
Nie musisz łączyć się z maszyną wirtualną bezpośrednio, aby zainstalować lub usunąć rozszerzenie. Gdy cykl życia aplikacji rozszerzenia platformy Azure jest zarządzany poza maszyną wirtualną i jest zintegrowany z platformą Azure, można również uzyskać zintegrowany stan rozszerzenia.

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>Coś innego, co muszę zrobić w przypadku rozszerzeń?
Rozszerzenia instalują aplikacje, podobnie jak w przypadku niektórych aplikacji, istnieją pewne wymagania, w przypadku rozszerzenia istnieje lista obsługiwanych systemów operacyjnych systemów Windows i Linux oraz wymaganych jest zainstalowanie agentów maszyny wirtualnej platformy Azure. Niektóre pojedyncze aplikacje rozszerzenia maszyny wirtualnej mogą mieć własne wymagania wstępne dotyczące środowiska, takie jak dostęp do punktu końcowego.

## <a name="next-steps"></a>Następne kroki
* Aby uzyskać więcej informacji na temat działania agenta i rozszerzeń systemu Linux, zobacz [Azure VM Extensions and features for Linux](features-linux.md).
* Aby uzyskać więcej informacji o działaniu agenta i rozszerzeń gościa systemu Windows, zobacz [rozszerzenia i funkcje maszyny wirtualnej platformy Azure dla systemu Windows](features-windows.md).  
* Aby zainstalować agenta gościa systemu Windows, zobacz [Omówienie agenta maszyny wirtualnej systemu Windows Azure](agent-windows.md).  
* Aby zainstalować agenta systemu Linux, zobacz [Omówienie agenta maszyny wirtualnej z systemem Linux](agent-linux.md).  

