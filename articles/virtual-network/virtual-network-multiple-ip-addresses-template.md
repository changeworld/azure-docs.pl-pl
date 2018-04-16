---
title: "Wiele adresów IP dla maszyn wirtualnych platformy Azure — szablon | Dokumentacja firmy Microsoft"
description: "Dowiedz się, jak można przypisać wiele adresów IP do maszyny wirtualnej przy użyciu szablonu usługi Azure Resource Manager."
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/08/2016
ms.author: jdial
ms.openlocfilehash: d4b189fb23dda1167c4f6b17b618c718d32dd98f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-an-azure-resource-manager-template"></a>Przypisz wielu adresów IP do maszyn wirtualnych przy użyciu szablonu usługi Azure Resource Manager

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

W tym artykule opisano sposób tworzenia maszyny wirtualnej (VM) za pośrednictwem modelu wdrażania usługi Azure Resource Manager przy użyciu szablonu usługi Resource Manager. Nie można przypisać wiele prywatnych i publicznych adresów IP do tej samej karty Sieciowej, w przypadku wdrażania maszyny Wirtualnej za pośrednictwem klasycznego modelu wdrażania. Aby dowiedzieć się więcej na temat modeli wdrażania platformy Azure, przeczytaj [zrozumieć modele wdrażania](../resource-manager-deployment-model.md) artykułu.

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="template-description"></a>Opis szablonu

Wdrażanie szablonu umożliwia szybkie i spójne tworzenie zasobów Azure o wartości innej konfiguracji. Odczyt [Przewodnik po szablonie usługi Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artykułu, jeśli nie masz doświadczenia w obsłudze szablonów usługi Azure Resource Manager. [Wdrożyć maszynę Wirtualną z wielu adresów IP](https://azure.microsoft.com/resources/templates/101-vm-multiple-ipconfig) szablon jest wykorzystywany w tym artykule.

<a name="resources"></a>Wdrażanie szablonu tworzy następujące zasoby:

|Zasób|Nazwa|Opis|
|---|---|---|
|Interfejs sieciowy|*myNic1*|Trzy konfiguracje adresów IP, opisane w sekcji scenariusza w tym artykule są tworzone i przypisywane do tej karty sieciowej.|
|Publiczny zasobu adresu IP|2 są tworzone: *myPublicIP* i *myPublicIP2*|Te zasoby są przypisane statyczne publiczne adresy IP i są przypisane do *IPConfig 1* i *IPConfig 2* opisany w scenariuszu konfiguracji adresów IP.|
|VM|*myVM1*|Standardowe DS3 maszyny Wirtualnej.|
|Sieć wirtualna|*myVNet1*|Sieć wirtualną z jednej podsieci o nazwie *mySubnet*.|
|Konto magazynu|Unikatowy do wdrożenia|Konto magazynu.|

<a name="parameters"></a>Podczas wdrażania szablonu, podaj wartości następujących parametrów:

|Nazwa|Opis|
|---|---|
|adminUsername|Nazwa użytkownika administratora. Nazwa użytkownika musi być zgodne z [wymagania Azure username](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json).|
|adminPassword|Hasło musi spełniać hasło administratora [wymagania dotyczące hasła Azure](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
|dnsLabelPrefix|Nazwa DNS PublicIPAddressName1. Nazwa DNS zostanie rozpoznana jako jeden publicznych adresów IP przypisanych do maszyny Wirtualnej. Nazwa musi być unikatowa w obrębie regionu Azure (lokalizacja), utwórz maszynę Wirtualną w.|
|dnsLabelPrefix1|Nazwa DNS PublicIPAddressName2. Nazwa DNS zostanie rozpoznana jako jeden publicznych adresów IP przypisanych do maszyny Wirtualnej. Nazwa musi być unikatowa w obrębie regionu Azure (lokalizacja), utwórz maszynę Wirtualną w.|
|OSVersion|Wersja systemu Windows i Linux dla maszyny Wirtualnej. System operacyjny jest obraz pełni poprawioną danego wybrana wersja systemu Windows i Linux.|
|imagePublisher|Wydawcy obrazu systemu Windows i Linux dla wybranej maszyny Wirtualnej.|
|imageOffer|Obraz systemu Windows i Linux dla wybranej maszyny Wirtualnej.|

Każdy z zasobów wdrożone przez szablon jest skonfigurowany przy użyciu kilku ustawień domyślnych. Można wyświetlić te ustawienia przy użyciu jednej z następujących metod:

- **Wyświetlanie szablonu w witrynie GitHub:** Jeśli znasz szablony, można wyświetlać ustawienia w ramach [szablonu](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json).
- **Wyświetl ustawienia po wdrożeniu:** Jeśli nie masz doświadczenia w obsłudze szablony, można wdrożyć szablon przy użyciu kroków w jednym z następujących sekcji i następnie Wyświetl ustawienia po wdrożeniu.

Portalu Azure, programu PowerShell lub interfejsu wiersza polecenia platformy Azure (CLI) można użyć do wdrożenia szablonu. Wszystkie metody uzyskania tego samego wyniku. Aby wdrożyć szablon, wykonaj kroki jednej z następujących sekcji:

## <a name="deploy-using-the-azure-portal"></a>Wdrażanie przy użyciu portalu Azure

Aby wdrożyć szablon przy użyciu portalu Azure, wykonaj następujące kroki:

1. W razie potrzeby zmodyfikuj szablon. Szablon wdraża zasoby i ustawienia na liście [zasobów](#resources) sekcji tego artykułu. Aby dowiedzieć się więcej o szablonach i sposobie ich tworzyć, odczytywać [szablonów Authoring Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-network%2ftoc.json)artykułu.
2. Wdrażanie szablonu z jedną z następujących metod:
    - **Wybierz szablon w portalu:** wykonaj kroki [wdrażanie zasobów z szablonu niestandardowego](../azure-resource-manager/resource-group-template-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template) artykułu. Wybierz istniejące szablon o nazwie *101-vm wiele ipconfig*.
    - **Bezpośrednio:** kliknij poniższy przycisk, aby otworzyć szablon bezpośrednio w portalu:<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-multiple-ipconfig%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

Niezależnie od metody wybierzesz, musisz podać wartości dla [parametry](#parameters) wymienione wcześniej w tym artykule. Po wdrożeniu maszyny Wirtualnej, połączenie z maszyną Wirtualną i Dodaj prywatnych adresów IP do systemu operacyjnego została wdrożona, wykonując kroki opisane w [adresów IP Dodaj do systemu operacyjnego maszyny Wirtualnej](#os-config) sekcji tego artykułu. Nie dodawaj publiczne adresy IP do systemu operacyjnego.

## <a name="deploy-using-powershell"></a>Wdrażanie przy użyciu programu PowerShell

Aby wdrożyć szablon przy użyciu programu PowerShell, wykonaj następujące kroki:

1. Wdrażanie szablonu, wykonując kroki opisane w [wdrożyć szablon przy użyciu programu PowerShell](../azure-resource-manager/resource-group-template-deploy-cli.md) artykułu. W artykule opisano wiele opcji wdrażania szablonu. Jeśli wybierzesz do wdrożenia przy użyciu `-TemplateUri parameter`, identyfikator URI dla tego szablonu jest *https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json*. Jeśli wybierzesz do wdrożenia przy użyciu `-TemplateFile` parametru, skopiuj zawartość [pliku szablonu](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json) z serwisu GitHub do nowego pliku na tym komputerze. W razie potrzeby zmodyfikuj zawartość szablonu. Szablon wdraża zasoby i ustawienia na liście [zasobów](#resources) sekcji tego artykułu. Aby dowiedzieć się więcej o szablonach i sposobie ich tworzyć, odczytywać [szablonów Authoring Azure Resource Manager ](../azure-resource-manager/resource-group-authoring-templates.md)artykułu.

    Niezależnie od wybranej do wdrożenia szablonu z opcji, należy podać wartości na liście wartości parametru [parametry](#parameters) sekcji tego artykułu. Jeśli chcesz podać parametry, używając pliku parametrów, skopiuj zawartość [pliku parametrów](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.parameters.json) z serwisu GitHub do nowego pliku na komputerze. Zmodyfikuj wartości w pliku. Użyj pliku został utworzony jako wartość `-TemplateParameterFile` parametru.

    Aby ustalić prawidłowe wartości, OSVersion, ImagePublisher i parametry imageOffer, wykonaj kroki [Nawigacja i wybierz artykuł obrazów maszyny Wirtualnej systemu Windows](../virtual-machines/windows/cli-ps-findimage.md) artykułu.

    >[!TIP]
    >Jeśli nie masz pewności, czy dnslabelprefix jest dostępny, wprowadź `Test-AzureRmDnsAvailability -DomainNameLabel <name-you-want-to-use> -Location <location>` polecenie, aby dowiedzieć się. Jeśli jest dostępny, polecenie zwróci błąd `True`.

2. Po wdrożeniu maszyny Wirtualnej, połączenie z maszyną Wirtualną i Dodaj prywatnych adresów IP do systemu operacyjnego została wdrożona, wykonując kroki opisane w [adresów IP Dodaj do systemu operacyjnego maszyny Wirtualnej](#os-config) sekcji tego artykułu. Nie dodawaj publiczne adresy IP do systemu operacyjnego.

## <a name="deploy-using-the-azure-cli"></a>Wdrażanie przy użyciu wiersza polecenia platformy Azure

Aby wdrożyć szablon przy użyciu 1.0 interfejsu wiersza polecenia Azure, wykonaj następujące kroki:

1. Wdrażanie szablonu, wykonując kroki opisane w [wdrażania szablonu z wiersza polecenia platformy Azure](../azure-resource-manager/resource-group-template-deploy-cli.md) artykułu. W artykule opisano wiele opcji wdrażania szablonu. Jeśli wybierzesz do wdrożenia przy użyciu `--template-uri` (-f), identyfikator URI dla tego szablonu jest *https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json*. Jeśli wybierzesz do wdrożenia przy użyciu `--template-file` (-f) parametru, skopiuj zawartość [pliku szablonu](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json) z serwisu GitHub do nowego pliku na tym komputerze. W razie potrzeby zmodyfikuj zawartość szablonu. Szablon wdraża zasoby i ustawienia na liście [zasobów](#resources) sekcji tego artykułu. Aby dowiedzieć się więcej o szablonach i sposobie ich tworzyć, odczytywać [szablonów Authoring Azure Resource Manager ](../azure-resource-manager/resource-group-authoring-templates.md)artykułu.

    Niezależnie od wybranej do wdrożenia szablonu z opcji, należy podać wartości na liście wartości parametru [parametry](#parameters) sekcji tego artykułu. Jeśli chcesz podać parametry, używając pliku parametrów, skopiuj zawartość [pliku parametrów](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.parameters.json) z serwisu GitHub do nowego pliku na komputerze. Zmodyfikuj wartości w pliku. Użyj pliku został utworzony jako wartość `--parameters-file` (-e) parametru.

    Aby ustalić prawidłowe wartości, OSVersion, ImagePublisher i parametry imageOffer, wykonaj kroki [Nawigacja i wybierz artykuł obrazów maszyny Wirtualnej systemu Windows](../virtual-machines/windows/cli-ps-findimage.md) artykułu.

2. Po wdrożeniu maszyny Wirtualnej, połączenie z maszyną Wirtualną i Dodaj prywatnych adresów IP do systemu operacyjnego została wdrożona, wykonując kroki opisane w [adresów IP Dodaj do systemu operacyjnego maszyny Wirtualnej](#os-config) sekcji tego artykułu. Nie dodawaj publiczne adresy IP do systemu operacyjnego.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
