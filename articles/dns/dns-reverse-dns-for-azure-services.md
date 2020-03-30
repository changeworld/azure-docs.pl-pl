---
title: Odwróć usługę DNS dla usług platformy Azure — usługa Azure DNS
description: Dzięki tej ścieżce szkoleniowej rozpocznij konfigurowanie odwrotnych odnośów DNS dla usług hostowanych na platformie Azure.
services: dns
documentationcenter: na
author: rohinkoul
manager: KumudD
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: rohink
ms.openlocfilehash: 073e84ece11f6817bfe2c5a94735ec6e16dac4fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76932369"
---
# <a name="configure-reverse-dns-for-services-hosted-in-azure"></a>Konfigurowanie odwrotnego systemu DNS dla usług hostowanych na platformie Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W tym artykule wyjaśniono, jak skonfigurować odwrotne wyszukiwania DNS dla usług hostowanych na platformie Azure.

Usługi na platformie Azure używają adresów IP przypisanych przez platformę Azure i należących do firmy Microsoft. Te odwrócone rekordy DNS (rekordy PTR) muszą być tworzone w odpowiednich strefach wyszukiwania wstecznego DNS należących do firmy Microsoft. W tym artykule wyjaśniono, jak to zrobić.

W tym scenariuszu nie należy mylić z możliwością [hosta stref wyszukiwania odwrotnego DNS dla przypisanych zakresów adresów IP w usłudze Azure DNS](dns-reverse-dns-hosting.md). W takim przypadku zakresy adresów IP reprezentowane przez strefę wyszukiwania wstecznego muszą być przypisane do organizacji, zazwyczaj przez usługodawcę internetowych.

Przed przeczytaniem tego artykułu należy zapoznać się z tym [omówieniem odwrotnego systemu DNS i pomocy technicznej na platformie Azure](dns-reverse-dns-overview.md).

W usłudze Azure DNS zasoby obliczeniowe (takie jak maszyny wirtualne, zestawy skalowania maszyn wirtualnych lub klastry sieci szkieletowej usług) są udostępniane za pośrednictwem zasobu PublicIpAddress. Odwróć wyszukiwania DNS są konfigurowane przy użyciu właściwości "ReverseFqdn" publicIpAddress.


Reverse DNS nie jest obecnie obsługiwany dla usługi Azure App Service.

## <a name="validation-of-reverse-dns-records"></a>Sprawdzanie poprawności wstecznych rekordów DNS

Strona trzecia nie powinna mieć możliwości tworzenia rekordów odwrotnego DNS dla mapowania usługi platformy Azure w domenach DNS. Aby temu zapobiec, platforma Azure zezwala na tworzenie odwrotnego rekordu DNS tylko wtedy, gdy nazwa domeny określona w rekordzie odwrotnego DNS jest taka sama jak nazwa DNS lub adres IP usługi PublicIpAddress lub Usługi w chmurze w tej samej subskrypcji platformy Azure.

To sprawdzanie poprawności jest wykonywane tylko wtedy, gdy rekord odwrotnego DNS jest ustawiony lub zmodyfikowany. Okresowa ponowna walidacja nie jest wykonywana.

Na przykład: załóżmy, że zasób PublicIpAddress ma nazwę DNS contosoapp1.northus.cloudapp.azure.com i adres IP 23.96.52.53. ReverseFqdn dla PublicIpAddress można określić jako:
* Nazwa DNS dla publicipaddress, contosoapp1.northus.cloudapp.azure.com
* Nazwa DNS dla innego PublicIpAddress w tej samej subskrypcji, takich jak contosoapp2.westus.cloudapp.azure.com
* Próżność nazwy DNS, takich jak app1.contoso.com, tak długo, jak ta nazwa jest *najpierw* skonfigurowany jako CNAME do contosoapp1.northus.cloudapp.azure.com lub do innego PublicIpAddress w tej samej subskrypcji.
* Próżność nazwy DNS, takich jak app1.contoso.com, tak długo, jak ta nazwa jest *najpierw* skonfigurowany jako rekord A do adresu IP 23.96.52.53 lub adres IP innego PublicIpAddress w tej samej subskrypcji.

Te same ograniczenia dotyczą odwrotnego systemu DNS dla usług w chmurze.


## <a name="reverse-dns-for-publicipaddress-resources"></a>Odwróć usługę DNS dla zasobów PublicIpAddress

Ta sekcja zawiera szczegółowe instrukcje dotyczące konfigurowania odwrotnego systemu DNS dla zasobów PublicIpAddress w modelu wdrażania usługi Resource Manager przy użyciu usługi Azure PowerShell, klasycznego interfejsu wiersza polecenia platformy Azure lub interfejsu wiersza polecenia platformy Azure. Konfigurowanie odwrotnego systemu DNS dla zasobów PublicIpAddress nie jest obecnie obsługiwane za pośrednictwem witryny Azure portal.

Platforma Azure obsługuje obecnie odwrotny system DNS tylko dla zasobów IPv4 PublicIpAddress. Nie jest obsługiwany dla IPv6.

### <a name="add-reverse-dns-to-an-existing-publicipaddresses"></a>Dodawanie odwrotnego dns do istniejącego publicipaddresses

#### <a name="powershell"></a>PowerShell

Aby zaktualizować odwrotny system DNS do istniejącego adresu PublicIpAddress:

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzPublicIpAddress -PublicIpAddress $pip
```

Aby dodać odwrócony DNS do istniejącego adresu PublicIpAddress, który nie ma jeszcze nazwy DNS, należy również określić nazwę DNS:

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings = New-Object -TypeName "Microsoft.Azure.Commands.Network.Models.PSPublicIpAddressDnsSettings"
$pip.DnsSettings.DomainNameLabel = "contosoapp1"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-classic-cli"></a>Klasyczny interfejs wiersza polecenia platformy Azure

Aby dodać odwrócony DNS do istniejącego adresu PublicIpAddress:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -f contosoapp1.westus.cloudapp.azure.com.
```

Aby dodać odwrócony DNS do istniejącego adresu PublicIpAddress, który nie ma jeszcze nazwy DNS, należy również określić nazwę DNS:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.
```

#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby dodać odwrócony DNS do istniejącego adresu PublicIpAddress:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com.
```

Aby dodać odwrócony DNS do istniejącego adresu PublicIpAddress, który nie ma jeszcze nazwy DNS, należy również określić nazwę DNS:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com --dns-name contosoapp1
```

### <a name="create-a-public-ip-address-with-reverse-dns"></a>Tworzenie publicznego adresu IP z odwrotnym systemem DNS

Aby utworzyć nową adres PublicIpAddress z wcześniej określoną właściwością DNS:

#### <a name="powershell"></a>PowerShell

```powershell
New-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup" -Location "WestUS" -AllocationMethod Dynamic -DomainNameLabel "contosoapp2" -ReverseFqdn "contosoapp2.westus.cloudapp.azure.com."
```

#### <a name="azure-classic-cli"></a>Klasyczny interfejs wiersza polecenia platformy Azure

```azurecli
azure network public-ip create -n PublicIp -g MyResourceGroup -l westus -d contosoapp3 -f contosoapp3.westus.cloudapp.azure.com.
```

#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli
az network public-ip create --name PublicIp --resource-group MyResourceGroup --location westcentralus --dns-name contosoapp1 --reverse-fqdn contosoapp1.westcentralus.cloudapp.azure.com
```

### <a name="view-reverse-dns-for-an-existing-publicipaddress"></a>Wyświetlanie odwrotnego dns dla istniejącego publicipaddress

Aby wyświetlić skonfigurowaną wartość dla istniejącego adresu PublicIpAddress:

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
```

#### <a name="azure-classic-cli"></a>Klasyczny interfejs wiersza polecenia platformy Azure

```azurecli
azure network public-ip show -n PublicIp -g MyResourceGroup
```

#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli
az network public-ip show --name PublicIp --resource-group MyResourceGroup
```

### <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>Usuwanie odwrotnego systemu DNS z istniejących publicznych adresów IP

Aby usunąć właściwość odwrotnego DNS z istniejącego adresu PublicIpAddress:

#### <a name="powershell"></a>PowerShell

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = ""
Set-AzPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-classic-cli"></a>Klasyczny interfejs wiersza polecenia platformy Azure

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup –f ""
```

#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn ""
```


## <a name="configure-reverse-dns-for-cloud-services"></a>Konfigurowanie odwrotnego systemu DNS dla usług w chmurze

Ta sekcja zawiera szczegółowe instrukcje dotyczące konfigurowania odwrotnego systemu DNS dla usług w chmurze w klasycznym modelu wdrażania przy użyciu programu Azure PowerShell. Konfigurowanie odwrotnego systemu DNS dla usług w chmurze nie jest obsługiwane za pośrednictwem witryny Azure portal, klasycznego interfejsu wiersza polecenia platformy Azure lub interfejsu wiersza polecenia platformy Azure.

### <a name="add-reverse-dns-to-existing-cloud-services"></a>Dodawanie odwrotnego systemu DNS do istniejących usług w chmurze

Aby dodać wsteczny rekord DNS do istniejącej usługi w chmurze:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="create-a-cloud-service-with-reverse-dns"></a>Tworzenie usługi w chmurze z odwrotnym systemem DNS

Aby utworzyć nową usługę w chmurze z już określoną właściwością odwrotnej usługi DNS:

```powershell
New-AzureService –ServiceName "contosoapp1" –Location "West US" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="view-reverse-dns-for-existing-cloud-services"></a>Wyświetlanie odwrotnego systemu DNS dla istniejących usług w chmurze

Aby wyświetlić właściwość odwrotnego DNS dla istniejącej usługi w chmurze:

```powershell
Get-AzureService "contosoapp1"
```

### <a name="remove-reverse-dns-from-existing-cloud-services"></a>Usuwanie odwrotnego systemu DNS z istniejących usług w chmurze

Aby usunąć właściwość odwrotnego DNS z istniejącej usługi w chmurze:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn ""
```

## <a name="faq"></a>Najczęściej zadawane pytania

### <a name="how-much-do-reverse-dns-records-cost"></a>Ile kosztują odwrócone rekordy DNS?

Są darmowe!  Brak dodatkowych kosztów dla odwrotnych rekordów DNS lub kwerend.

### <a name="will-my-reverse-dns-records-resolve-from-the-internet"></a>Czy moje odwrócone rekordy DNS zostaną rozwiązane z Internetu?

Tak. Po ustawieniu właściwości odwrotnej usługi DNS dla usługi Azure platforma Azure zarządza wszystkimi delegacjami DNS i strefami DNS wymaganymi do zapewnienia, że odwrócony rekord DNS jest rozpoznawany dla wszystkich użytkowników Internetu.

### <a name="are-default-reverse-dns-records-created-for-my-azure-services"></a>Czy domyślne rekordy odwrotnej dns są tworzone dla moich usług platformy Azure?

Nie. Reverse DNS jest funkcją opt-in. Jeśli nie chcesz ich konfigurować, nie są tworzone żadne domyślne rekordy odwrotnego DNS.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>Jaki jest format w pełni kwalifikowanej nazwy domeny (FQDN)?

Nazwy FQDN są określone w kolejności przesyłania dalej i muszą zostać zakończone kropką (na przykład "app1.contoso.com.").

### <a name="what-happens-if-the-validation-check-for-the-reverse-dns-ive-specified-fails"></a>Co się stanie, jeśli sprawdzanie poprawności dla odwróconego systemu DNS, który określiłem, zakończy się niepowodzeniem?

Jeśli sprawdzanie weryfikacji odwrotnej dns zakończy się niepowodzeniem, operacja konfigurowania odwrotnego rekordu DNS kończy się niepowodzeniem. Popraw wartość odwrotnego DNS zgodnie z wymaganiami i ponów próbę.

### <a name="can-i-configure-reverse-dns-for-azure-app-service"></a>Czy mogę skonfigurować reverse DNS dla usługi Azure App Service?

Nie. Reverse DNS nie jest obsługiwany dla usługi Azure App Service.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-azure-service"></a>Czy mogę skonfigurować wiele rekordów odwrotnego DNS dla mojej usługi platformy Azure?

Nie. Platforma Azure obsługuje jeden rekord odwrotnej usługi DNS dla każdej usługi Azure Cloud Service lub PublicIpAddress.

### <a name="can-i-configure-reverse-dns-for-ipv6-publicipaddress-resources"></a>Czy mogę skonfigurować odwrotny system DNS dla zasobów IPv6 PublicIpAddress?

Nie. Platforma Azure obsługuje obecnie odwrotny system DNS tylko dla zasobów IPv4 PublicIpAddress i usług w chmurze.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>Czy mogę wysyłać wiadomości e-mail do domen zewnętrznych z moich usług obliczeniowych platformy Azure?

Techniczna możliwość wysyłania wiadomości e-mail bezpośrednio z wdrożenia platformy Azure zależy od typu subskrypcji. Niezależnie od typu subskrypcji firma Microsoft zaleca używanie zaufanych usług przekazywania poczty do wysyłania poczty wychodzącej. Aby uzyskać więcej informacji, zobacz [Rozszerzone zabezpieczenia platformy Azure do wysyłania wiadomości e-mail — aktualizacja z listopada 2017 r.](https://blogs.msdn.microsoft.com/mast/2017/11/15/enhanced-azure-security-for-sending-emails-november-2017-update/)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat odwrotnego DNS, zobacz [odwrotne wyszukiwanie DNS na Wikipedii](https://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Dowiedz się, jak [hostować strefę wyszukiwania wstecznego dla zakresu adresów IP przypisanych usługodawcy sieci isp w usłudze Azure DNS](dns-reverse-dns-for-azure-services.md).

