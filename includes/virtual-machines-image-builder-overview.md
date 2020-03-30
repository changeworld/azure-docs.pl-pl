---
author: cynthn
ms.author: cynthn
ms.date: 01/23/2020
ms.topic: include
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 00fc3a01e6f42c2704af9dbc807dce193ff2971c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80117134"
---
Obrazy znormalizowanej maszyny wirtualnej umożliwiają organizacjom migrację do chmury i zapewniają spójność wdrożeń. Obrazy zazwyczaj zawierają wstępnie zdefiniowane ustawienia zabezpieczeń i konfiguracji oraz niezbędne oprogramowanie. Konfigurowanie własnego potoku przetwarzania wymaga czasu, infrastruktury i konfiguracji, ale za pomocą programu Azure VM Image Builder po prostu podaj prostą konfigurację opisującą obraz, prześlij go do usługi, a obraz zostanie utworzony i rozproszony.
 
Usługa Azure VM Image Builder (Azure Image Builder) umożliwia rozpoczęcie od obrazu azure marketplace opartego na systemie Windows lub Linux, istniejących obrazów niestandardowych lub iso systemu Linux (RHEL) red hat enterprise i rozpocząć dodawanie własnych dostosowań. Ponieważ Kreator obrazów jest zbudowany na [HashiCorp Packer,](https://packer.io/)można również zaimportować istniejące skrypty aprowizacji powłoki Packer. Można również określić, gdzie mają być hostowane obrazy w [galerii obrazów udostępnionych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries), jako obraz zarządzany lub VHD.

> [!IMPORTANT]
> Usługa Azure Image Builder jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="preview-features"></a>Funkcje w wersji zapoznawczej

W przypadku wersji zapoznawczej te funkcje są obsługiwane:

- Tworzenie złotych obrazów linii bazowych, które obejmują minimalne zabezpieczenia i konfiguracje firmowe, i umożliwiają działom dostosowanie ich do ich potrzeb.
- Łatanie istniejących obrazów, Kreator obrazów pozwoli ci stale załatać istniejące obrazy niestandardowe.
- Połącz konstruktora obrazów z istniejącymi sieciami wirtualnymi, aby można było połączyć się z istniejącymi serwerami konfiguracyjnymi (DSC, Chef, Puppet itp.), udziałami plików lub innymi serwerami/usługami rutowalnymi.
- Integracja z galerią obrazów udostępnionych platformy Azure umożliwia globalną dystrybucję, przechowywanie wersji i skalowania obrazów oraz system zarządzania obrazami.
- Integracja z istniejącymi potokami kompilacji obrazów, wystarczy wywołać Konstruktora obrazów z potoku lub użyć prostego zadania programu Preview Image Builder Azure DevOps.
- Migrowanie istniejącego potoku dostosowywania obrazu na platformę Azure. Użyj istniejących skryptów, poleceń i procesów, aby dostosować obrazy.
- Tworzenie obrazów w formacie VHD.
 

## <a name="regions"></a>Regiony
Usługa Azure Image Builder będzie dostępna w wersji zapoznawczej w tych regionach. Obrazy mogą być dystrybuowane poza tymi regionami.
- Wschodnie stany USA
- Wschodnie stany USA 2
- Zachodnio-środkowe stany USA
- Zachodnie stany USA
- Zachodnie stany USA 2
- Europa Północna
- Europa Zachodnia

## <a name="os-support"></a>Obsługa systemu operacyjnego
AIB będzie obsługiwać obrazy systemu operacyjnego bazy portalu Azure Marketplace:
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7.6, 7.7
- CentOS 7,6, 7,7
- SLES 12 SP4
- SLES 15, SLES 15 SP1
- Windows 10 RS5 Enterprise/Professional/Enterprise for Virtual Desktop (EVD) 
- Windows 2016
- Windows 2019

Obsługa iso RHEL jest przestarzałe, należy przejrzeć dokumentację szablonu, aby uzyskać więcej informacji.

## <a name="how-it-works"></a>Jak to działa


![Koncepcyjny rysunek konstruktora azure](./media/virtual-machines-image-builder-overview/image-builder.png)

Usługa Azure Image Builder to w pełni zarządzana usługa platformy Azure, która jest dostępna dla dostawcy zasobów platformy Azure. Proces azure image builder ma trzy główne części: źródło, dostosować i rozpowszechniać, są one reprezentowane w szablonie. Poniższy diagram przedstawia składniki, z niektórymi ich właściwościami. 
 


**Proces konstruktora obrazów** 

![Koncepcyjny rysunek procesu azure image builder](./media/virtual-machines-image-builder-overview/image-builder-process.png)

1. Utwórz szablon obrazu jako plik json. Ten plik .json zawiera informacje o źródle obrazu, dostosowaniach i dystrybucji. Istnieje wiele przykładów w [repozytorium Usługi Azure Image Builder GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).
1. Prześlij go do usługi, spowoduje to utworzenie artefaktu szablonu obrazu w określonej grupie zasobów. W tle Konstruktor obrazów pobierze obraz źródłowy lub ISO i skrypty w razie potrzeby. Są one przechowywane w oddzielnej grupie zasobów, która jest automatycznie tworzona w ramach subskrypcji, w formacie:\<IT_ DestinationResourceGroup>_\<TemplateName>. 
1. Po utworzeniu szablonu obrazu można następnie utworzyć obraz. W tle Konstruktor obrazów używa szablonu i plików źródłowych do utworzenia maszyny Wirtualnej (domyślny rozmiar: Standard_D1_v2), sieci,\<publicznego adresu IP,\<sieciowej grupy sieciowej i magazynu w IT_ grupie zasobów>_>>_ DestinationResourceGroup.
1. W ramach tworzenia obrazu konstruktor obrazów dystrybuuje obraz zgodnie z szablonem, a\<następnie usuwa dodatkowe zasoby\<w IT_ DestinationResourceGroup>_ TemplateName> grupy zasobów, która została utworzona dla tego procesu.


## <a name="permissions"></a>Uprawnienia

Aby umożliwić konstruktorowi obrazów maszyn wirtualnych platformy Azure rozpowszechnianie obrazów do obrazów zarządzanych lub do galerii obrazów udostępnionych, należy podać uprawnienia współautora dla usługi "Azure Virtual Machine Image Builder" (identyfikator aplikacji: cf32a0cc-373c-47c9-9156-0db11f6a6dfc ) w grupach zasobów. 

Jeśli używasz istniejącego niestandardowego obrazu zarządzanego lub wersji obrazu, a następnie Azure Image Builder będzie potrzebować co najmniej "Reader" dostęp do tych grup zasobów.

Dostęp można przypisać przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName>
```

Dostęp można przypisać za pomocą programu PowerShell:

```azurePowerShell-interactive
New-AzRoleAssignment -ObjectId ef511139-6170-438e-a6e1-763dc31bdf74 -Scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName> -RoleDefinitionName Contributor
```


Jeśli konto usługi nie zostanie znalezione, może to oznaczać, że subskrypcja, w której dodajesz przypisanie roli, nie została jeszcze zarejestrowana dla dostawcy zasobów.


## <a name="costs"></a>Koszty
Podczas tworzenia, tworzenia, tworzenia i przechowywania obrazów za pomocą usługi Azure Image Builder poniesiesz pewne koszty obliczeniowe, sieciowe i magazynowe. Koszty te są podobne do kosztów poniesionych podczas ręcznego tworzenia obrazów niestandardowych. W przypadku zasobów zostanie naliczona opłata według stawek platformy Azure. 

Podczas procesu tworzenia obrazu pliki są pobierane `IT_<DestinationResourceGroup>_<TemplateName>` i przechowywane w grupie zasobów, co wiąże się z niewielkimi kosztami magazynowania. Jeśli nie chcesz ich przechowywać, usuń **szablon obrazu** po kompilacji obrazu.
 
Konstruktor obrazów tworzy maszynę wirtualną przy użyciu rozmiaru maszyny Wirtualnej D1v2 oraz magazynu i sieci potrzebnej dla maszyny Wirtualnej. Zasoby te będą trwać przez cały czas trwania procesu kompilacji i zostaną usunięte po zakończeniu tworzenia obrazu przez Konstruktora obrazów. 
 
Usługa Azure Image Builder będzie dystrybuować obraz do wybranych regionów, które mogą nieść z powodu opłat wychodzących sieciowych.
 
## <a name="next-steps"></a>Następne kroki 
 
Aby wypróbować usługę Azure Image Builder, zobacz artykuły dotyczące tworzenia obrazów [systemu Linux](../articles/virtual-machines/linux/image-builder.md) lub [Windows.](../articles/virtual-machines/windows/image-builder.md)
 
 
