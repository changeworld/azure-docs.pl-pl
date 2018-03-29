---
title: Reverse DNS dla usługi Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować wstecznego wyszukiwania DNS dla usługi hostowanej na platformie Azure
services: dns
documentationcenter: na
author: KumudD
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: kumud
ms.openlocfilehash: bba2d75ed760a6e4eef2caacb0bb5924680b1f4b
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
---
# <a name="configure-reverse-dns-for-services-hosted-in-azure"></a>Skonfiguruj wstecznego DNS dla usługi hostowanej na platformie Azure

W tym artykule opisano sposób konfigurowania wstecznego wyszukiwania DNS dla usługi hostowanej na platformie Azure.

Usługi w usłudze Azure używać adresów IP przypisany przez usługę Azure i należących do firmy Microsoft. Te rekordy DNS odwrotnej (rekordów PTR) należy utworzyć odpowiedniego należące do firmy Microsoft wstecznego wyszukiwania stref DNS. W tym artykule wyjaśniono, jak to zrobić.

W tym scenariuszu nie należy mylić z możliwością [hosta stref wyszukiwania wstecznego wyszukiwania DNS dla przypisane zakresy IP w usłudze Azure DNS](dns-reverse-dns-hosting.md). W takim przypadku zakresów IP reprezentowany przez strefy wyszukiwania wstecznego musi być przypisany do organizacji, zazwyczaj przez Usługodawcę internetowego.

Przed przeczytaniem tego artykułu, należy się zapoznać z tym [omówienie wstecznego DNS i pomocy technicznej na platformie Azure](dns-reverse-dns-overview.md).

W usłudze Azure DNS zasobów obliczeniowych (np. maszyny wirtualne, zestawy skalowania maszyny wirtualnej lub klastrów sieci szkieletowej usług) są udostępniane za pośrednictwem publicznego adresu IP zasobu. Wyszukiwania wstecznego DNS są skonfigurowane przy użyciu właściwości 'ReverseFqdn' publicznego adresu IP.


Wstecznego DNS nie jest obecnie obsługiwany dla usługi Azure App Service.

## <a name="validation-of-reverse-dns-records"></a>Sprawdzanie poprawności odwrotnej rekordów DNS

Innych firm nie będą już mogli tworzenia wstecznego rekordów DNS, aby ich mapowanie usługi Azure DNS domeny. Aby tego uniknąć, Azure tylko pozwala tworzenia wstecznego rekord DNS, gdzie jest taka sama jak nazwa domeny określona w odwrotnej rekordu DNS i jest rozpoznawana jako nazwę DNS lub adres IP publicznego adresu IP lub usługi w chmurze w tej samej subskrypcji platformy Azure.

To tylko weryfikacji wstecznego rekord DNS jest ustawiona lub modyfikacji. Okresowe ponowne sprawdzanie poprawności nie jest wykonywane.

Na przykład: Załóżmy, że zasób publicznego adresu IP ma contosoapp1.northus.cloudapp.azure.com nazwę DNS i adres IP 23.96.52.53. ReverseFqdn dla publicznego adresu IP może być określona jako:
* The DNS name for the PublicIpAddress, contosoapp1.northus.cloudapp.azure.com
* Nazwy DNS dla różnych publicznego adresu IP w tej samej subskrypcji, takie jak contosoapp2.westus.cloudapp.azure.com
* Niestandardowych nazw DNS, takich jak app1.contoso.com, tak długo, jak ta nazwa jest *pierwszy* skonfigurowany jako rekord CNAME contosoapp1.northus.cloudapp.azure.com lub innego publicznego adresu IP w tej samej subskrypcji.
* Niestandardowych nazw DNS, takich jak app1.contoso.com, tak długo, jak ta nazwa jest *pierwszy* skonfigurowany jako rekord A z adresem IP 23.96.52.53 lub adres IP innego publicznego adresu IP w tej samej subskrypcji.

Takich samych ograniczeń dotyczą reverse DNS dla usługi w chmurze.


## <a name="reverse-dns-for-publicipaddress-resources"></a>Reverse DNS dla zasobów publicznego adresu IP

Ta sekcja zawiera szczegółowe instrukcje dotyczące sposobu konfigurowania wstecznego DNS dla publicznego adresu IP zasobów w modelu wdrażania usługi Resource Manager przy użyciu programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure w wersji 1.0 lub 2.0 interfejsu wiersza polecenia platformy Azure. Konfigurowanie wstecznego DNS dla zasobów publicznego adresu IP nie jest obecnie obsługiwane za pośrednictwem portalu Azure.

Azure obecnie obsługuje reverse DNS tylko dla zasobów IPv4 publicznego adresu IP. Nie jest obsługiwana w przypadku protokołu IPv6.

### <a name="add-reverse-dns-to-an-existing-publicipaddresses"></a>Dodawanie do istniejących elementów Publicipaddress wstecznego DNS

#### <a name="powershell"></a>PowerShell

Aby dodać wstecznego DNS do istniejącego publicznego adresu IP:

```powershell
$pip = Get-AzureRmPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzureRmPublicIpAddress -PublicIpAddress $pip
```

Aby dodać wstecznego DNS do istniejącego publicznego adresu IP, który nie ma nazwy DNS, należy również określić nazwę DNS:

```powershell
$pip = Get-AzureRmPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings = New-Object -TypeName "Microsoft.Azure.Commands.Network.Models.PSPublicIpAddressDnsSettings"
$pip.DnsSettings.DomainNameLabel = "contosoapp1"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzureRmPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-cli-10"></a>Interfejs wiersza polecenia platformy Azure CLI w wersji 1.0

Aby dodać wstecznego DNS do istniejącego publicznego adresu IP:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -f contosoapp1.westus.cloudapp.azure.com.
```

Aby dodać wstecznego DNS do istniejącego publicznego adresu IP, który nie ma nazwy DNS, należy również określić nazwę DNS:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.
```

#### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0

Aby dodać wstecznego DNS do istniejącego publicznego adresu IP:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com.
```

Aby dodać wstecznego DNS do istniejącego publicznego adresu IP, który nie ma nazwy DNS, należy również określić nazwę DNS:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com --dns-name contosoapp1
```

### <a name="create-a-public-ip-address-with-reverse-dns"></a>Utwórz publiczny adres IP z wstecznego DNS

Aby utworzyć nowego publicznego adresu IP z wstecznego DNS już określona właściwość:

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup" -Location "WestUS" -AllocationMethod Dynamic -DomainNameLabel "contosoapp2" -ReverseFqdn "contosoapp2.westus.cloudapp.azure.com."
```

#### <a name="azure-cli-10"></a>Interfejs wiersza polecenia platformy Azure CLI w wersji 1.0

```azurecli
azure network public-ip create -n PublicIp -g MyResourceGroup -l westus -d contosoapp3 -f contosoapp3.westus.cloudapp.azure.com.
```

#### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0

```azurecli
az network public-ip create --name PublicIp --resource-group MyResourceGroup --location westcentralus --dns-name contosoapp1 --reverse-fqdn contosoapp1.westcentralus.cloudapp.azure.com
```

### <a name="view-reverse-dns-for-an-existing-publicipaddress"></a>Widok wstecznego DNS dla istniejącego publicznego adresu IP

Aby wyświetlić wartość skonfigurowana dla istniejącego publicznego adresu IP:

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzureRmPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
```

#### <a name="azure-cli-10"></a>Interfejs wiersza polecenia platformy Azure CLI w wersji 1.0

```azurecli
azure network public-ip show -n PublicIp -g MyResourceGroup
```

#### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0

```azurecli
az network public-ip show --name PublicIp --resource-group MyResourceGroup
```

### <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>Usuń wstecznego DNS z istniejących publicznych adresów IP

Aby usunąć odwrotnej właściwości DNS z istniejącego publicznego adresu IP:

#### <a name="powershell"></a>PowerShell

```powershell
$pip = Get-AzureRmPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = ""
Set-AzureRmPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-cli-10"></a>Interfejs wiersza polecenia platformy Azure CLI w wersji 1.0

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup –f ""
```

#### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn ""
```


## <a name="configure-reverse-dns-for-cloud-services"></a>Konfigurowanie wstecznego DNS dla usługi w chmurze

Ta sekcja zawiera szczegółowe instrukcje dotyczące sposobu konfigurowania wstecznego DNS dla usługi w chmurze w klasycznego modelu wdrażania przy użyciu programu Azure PowerShell. Konfigurowanie wstecznego DNS dla usługi w chmurze nie jest obsługiwane w portalu Azure, Azure CLI w wersji 1.0 lub 2.0 interfejsu wiersza polecenia platformy Azure.

### <a name="add-reverse-dns-to-existing-cloud-services"></a>Dodaj wstecznego DNS do istniejącej usługi w chmurze

Aby dodać wstecznego rekord DNS do istniejącej usługi w chmurze:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="create-a-cloud-service-with-reverse-dns"></a>Tworzenie usługi w chmurze z wstecznego DNS

Aby utworzyć nową usługę w chmurze z wstecznego DNS już określona właściwość:

```powershell
New-AzureService –ServiceName "contosoapp1" –Location "West US" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="view-reverse-dns-for-existing-cloud-services"></a>Widok wstecznego DNS dla istniejącej usługi w chmurze

Aby wyświetlić właściwości wstecznego DNS dla istniejącej usługi w chmurze:

```powershell
Get-AzureService "contosoapp1"
```

### <a name="remove-reverse-dns-from-existing-cloud-services"></a>Usuń wstecznego DNS z istniejącej usługi w chmurze

Aby usunąć wstecznego DNS we właściwości z istniejącej usługi w chmurze:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn ""
```

## <a name="faq"></a>Często zadawane pytania

### <a name="how-much-do-reverse-dns-records-cost"></a>Ile wstecznego koszt rekordy DNS?

Są one wolnego!  Nie ma żadnych dodatkowych kosztów odwrotnej rekordów DNS lub zapytania.

### <a name="will-my-reverse-dns-records-resolve-from-the-internet"></a>Odwrotnej rekordy DNS rozwiąże z Internetu?

Tak. Po ustawieniu właściwości wstecznego DNS dla usługi Azure, Azure zarządza delegowania DNS oraz stref DNS wymagane w celu zapewnienia, że wstecznego rekord DNS rozpoznaje dla wszystkich użytkowników Internetu.

### <a name="are-default-reverse-dns-records-created-for-my-azure-services"></a>Domyślne odwrotnej rekordy DNS są tworzone dla moich usług platformy Azure?

Nie. Wstecznego DNS jest funkcji opcjonalnych. Żadne rekordy DNS odwrotnej domyślne są tworzone, jeśli nie chcesz je skonfigurować.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>Co to jest format w pełni kwalifikowaną nazwą domeny (FQDN)?

Nazwy FQDN są określone w kolejności do przodu, a musi być zakończona kropką (na przykład "app1.contoso.com.").

### <a name="what-happens-if-the-validation-check-for-the-reverse-dns-ive-specified-fails"></a>Co się stanie, jeśli wyszukiwanie wsteczne DNS I wybranymi zakończy się niepowodzeniem?

W przypadku, gdy wstecznego DNS sprawdzania operację, aby skonfigurować wstecznego rekord DNS nie powiedzie się. Należy poprawić wartość wstecznego DNS, zgodnie z wymaganiami, a następnie spróbuj ponownie.

### <a name="can-i-configure-reverse-dns-for-azure-app-service"></a>Można skonfigurować wstecznego DNS dla usługi Azure App Service?

Nie. Wstecznego DNS nie jest obsługiwana dla usługi Azure App Service.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-azure-service"></a>Moje usługi Azure można skonfigurować wiele rekordów DNS odwrotnej?

Nie. Azure obsługuje odwrotnej pojedynczego rekordu DNS dla każdej usługi w chmurze Azure lub publicznego adresu IP.

### <a name="can-i-configure-reverse-dns-for-ipv6-publicipaddress-resources"></a>Można skonfigurować wstecznego DNS dla zasobów publicznego adresu IP protokołu IPv6?

Nie. Azure obecnie obsługuje reverse DNS tylko dla zasobów IPv4 publicznego adresu IP i usługi w chmurze.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>Można wysyłać wiadomości e-mail do domen zewnętrznych z mojej usług rozwiązań usługi obliczenia Azure?

Techniczne możliwość wysyłania wiadomości e-mail bezpośrednio z wdrożenia usługi Azure zależy od typu subskrypcji. Niezależnie od tego typu subskrypcji firma Microsoft zaleca używanie usług przekazywania zaufanych poczty wysyłanie poczty wychodzącej. Aby uzyskać szczegółowe informacje, zobacz [zwiększonych zabezpieczeń platformy Azure do wysyłania wiadomości E-mail — listopad 2017 aktualizacji](https://blogs.msdn.microsoft.com/mast/2017/11/15/enhanced-azure-security-for-sending-emails-november-2017-update/).

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji dotyczących wstecznego DNS, zobacz [istnienia wstecznego wyszukiwania DNS dla Wikipedia](http://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Dowiedz się, jak [hostowanie strefy wyszukiwania wstecznego zakres IP przypisany usługodawcy internetowego w usłudze Azure DNS](dns-reverse-dns-for-azure-services.md).

