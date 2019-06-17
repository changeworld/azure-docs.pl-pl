---
title: Odwrotne DNS dla usług platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować wstecznego wyszukiwania DNS dla usług hostowanych na platformie Azure
services: dns
documentationcenter: na
author: vhorne
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: victorh
ms.openlocfilehash: e162d838cb4895841428a827b56bec28e3e16b8a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66160927"
---
# <a name="configure-reverse-dns-for-services-hosted-in-azure"></a>Konfigurowanie odwrotnego systemu DNS dla usług hostowanych na platformie Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W tym artykule opisano sposób konfigurowania wstecznego wyszukiwania DNS dla usług hostowanych na platformie Azure.

Usługi na platformie Azure Użyj adresów IP przypisany przez platformę Azure i należące do firmy Microsoft. Te rekordami odwrotnego systemu DNS (PTR rekordów) należy utworzyć odpowiednie należącymi do firmy Microsoft odwrotnego wyszukiwania stref DNS. W tym artykule wyjaśniono, jak to zrobić.

W tym scenariuszu nie należy mylić z możliwością [hosta odwrotne strefy wyszukiwania DNS dla przypisanych zakresów adresów IP w taki sposób, w usłudze Azure DNS](dns-reverse-dns-hosting.md). W tym przypadku zakresów adresów IP, reprezentowane przez strefy wyszukiwania wstecznego należy przypisać do organizacji, zwykle przez usługodawcę internetowego.

Przed przeczytaniem tego artykułu, należy się zapoznać z tym [omówienie odwrotnego DNS i pomocy technicznej na platformie Azure](dns-reverse-dns-overview.md).

W usłudze Azure DNS zasoby obliczeniowe (np. maszyny wirtualne, zestawy skalowania maszyn wirtualnych lub klastry usługi Service Fabric) są udostępniane za pośrednictwem publicznego adresu IP zasobu. Wyszukiwania wstecznego DNS są skonfigurowane przy użyciu właściwości "Element ReverseFqdn" publiczny adres IP.


Odwrotnym systemem DNS nie jest obecnie obsługiwane w usłudze Azure App Service.

## <a name="validation-of-reverse-dns-records"></a>Sprawdzanie poprawności rekordami odwrotnego systemu DNS

Innej nie należy tworzyć rekordami odwrotnego systemu DNS dla ich mapowanie usługi platformy Azure do domen systemu DNS. Aby temu zapobiec, Azure umożliwia tworzenie odwrotnej rekord DNS, gdzie jest taka sama jak nazwa domeny określona w odwrotnej rekordu DNS lub tylko jest rozpoznawany jako nazwa DNS lub adres IP publicznego adresu IP lub usługi w chmurze w tej samej subskrypcji platformy Azure.

Tej weryfikacji jest realizowane wyłącznie odwrotnej rekord DNS jest ustawiona lub modyfikacji. Okresowe ponowne sprawdzanie poprawności nie jest wykonywana.

Na przykład: Załóżmy, że zasób publicznego adresu IP ma contosoapp1.northus.cloudapp.azure.com nazwę DNS i adres IP 23.96.52.53. Element ReverseFqdn dla publicznego adresu IP może być określony jako:
* The DNS name for the PublicIpAddress, contosoapp1.northus.cloudapp.azure.com
* Nazwy DNS dla publicznego adresu IP innego w tej samej subskrypcji, takie jak contosoapp2.westus.cloudapp.azure.com
* Znaczących nazw DNS, takich jak app1.contoso.com, tak długo, jak ta nazwa jest *pierwszy* skonfigurowany jako rekord CNAME contosoapp1.northus.cloudapp.azure.com lub inny publiczny adres IP w tej samej subskrypcji.
* Znaczących nazw DNS, takich jak app1.contoso.com, tak długo, jak ta nazwa jest *pierwszy* skonfigurowany jako rekord A adres IP 23.96.52.53 lub adres IP inny publiczny adres IP w tej samej subskrypcji.

Tego samego ograniczenia mają zastosowanie do odwrotnego systemu DNS dla usług w chmurze.


## <a name="reverse-dns-for-publicipaddress-resources"></a>Odwrotne DNS dla publicznego adresu IP zasobów

Ta sekcja zawiera szczegółowe instrukcje dotyczące sposobu konfigurowania odwrotnym systemem DNS dla publicznego adresu IP zasobów w modelu wdrażania usługi Resource Manager przy użyciu programu Azure PowerShell, klasycznego wiersza polecenia platformy Azure lub interfejsu wiersza polecenia platformy Azure. Konfigurowanie odwrotnego systemu DNS dla publicznego adresu IP zasobów nie jest obecnie obsługiwane w witrynie Azure portal.

Usługa Azure obecnie obsługuje odwrotne DNS tylko dla zasobów PublicIpAddress protokołu IPv4. Nie jest obsługiwana w przypadku protokołu IPv6.

### <a name="add-reverse-dns-to-an-existing-publicipaddresses"></a>Dodaj odwrotnym systemem DNS do istniejących PublicIpAddresses

#### <a name="powershell"></a>PowerShell

Aby dodać odwrotnym systemem DNS do istniejącego publicznego adresu IP:

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzPublicIpAddress -PublicIpAddress $pip
```

Odwrotnym systemem DNS należy dodać do istniejącego publicznego adresu IP, który nie ma jeszcze nazwę DNS, należy również określić nazwę DNS:

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings = New-Object -TypeName "Microsoft.Azure.Commands.Network.Models.PSPublicIpAddressDnsSettings"
$pip.DnsSettings.DomainNameLabel = "contosoapp1"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-classic-cli"></a>Klasyczny interfejs wiersza polecenia platformy Azure

Aby dodać odwrotnym systemem DNS do istniejącego publicznego adresu IP:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -f contosoapp1.westus.cloudapp.azure.com.
```

Odwrotnym systemem DNS należy dodać do istniejącego publicznego adresu IP, który nie ma jeszcze nazwę DNS, należy również określić nazwę DNS:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.
```

#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby dodać odwrotnym systemem DNS do istniejącego publicznego adresu IP:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com.
```

Odwrotnym systemem DNS należy dodać do istniejącego publicznego adresu IP, który nie ma jeszcze nazwę DNS, należy również określić nazwę DNS:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com --dns-name contosoapp1
```

### <a name="create-a-public-ip-address-with-reverse-dns"></a>Tworzenie publicznego adresu IP z odwrotnym systemem DNS

Aby utworzyć nowy publiczny adres IP za pomocą odwrotnego DNS już określona właściwość:

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

### <a name="view-reverse-dns-for-an-existing-publicipaddress"></a>Wyświetl odwrotnym systemem DNS dla istniejącego publicznego adresu IP

Aby wyświetlić wartość skonfigurowana dla istniejącego publicznego adresu IP:

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

### <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>Usuń odwrotnym systemem DNS z istniejących publiczne adresy IP

Aby usunąć właściwość odwrotnego DNS z istniejącego publicznego adresu IP:

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

Ta sekcja zawiera szczegółowe instrukcje dotyczące sposobu konfigurowania odwrotnym systemem DNS dla usług w chmurze w klasycznym modelu wdrażania przy użyciu programu Azure PowerShell. Konfigurowanie odwrotnego DNS dla usług w chmurze nie jest obsługiwana przy użyciu witryny Azure portal, Azure klasyczny interfejs wiersza polecenia lub wiersza polecenia platformy Azure.

### <a name="add-reverse-dns-to-existing-cloud-services"></a>Dodaj odwrotnym systemem DNS do istniejących usług w chmurze

Aby dodać rekord DNS odwrotnej do istniejącej usługi w chmurze:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="create-a-cloud-service-with-reverse-dns"></a>Utwórz usługę w chmurze za pomocą wstecznego DNS

Aby utworzyć nową usługę w chmurze za pomocą odwrotnego DNS już określona właściwość:

```powershell
New-AzureService –ServiceName "contosoapp1" –Location "West US" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="view-reverse-dns-for-existing-cloud-services"></a>Wyświetl odwrotnym systemem DNS dla istniejących usług w chmurze

Aby wyświetlić właściwości odwrotnego DNS dla istniejącej usługi w chmurze:

```powershell
Get-AzureService "contosoapp1"
```

### <a name="remove-reverse-dns-from-existing-cloud-services"></a>Usuń odwrotnym systemem DNS z istniejących usług w chmurze

Aby usunąć właściwość odwrotnego DNS z istniejącej usługi w chmurze:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn ""
```

## <a name="faq"></a>Często zadawane pytania

### <a name="how-much-do-reverse-dns-records-cost"></a>Ile odwrotnego DNS rekordów kosztów?

Są one bezpłatne!  Nie ma żadnych dodatkowych kosztów, rekordami odwrotnego systemu DNS lub kwerend.

### <a name="will-my-reverse-dns-records-resolve-from-the-internet"></a>Moje rekordami odwrotnego systemu DNS rozwiąże z Internetu?

Tak. Po ustawieniu właściwości odwrotnego DNS dla usługi Azure, platforma Azure zarządza delegowania DNS i stref DNS wymagane w celu zapewnienia, że odwrotnej rekordu DNS rozpoznaje dla wszystkich użytkowników Internetu.

### <a name="are-default-reverse-dns-records-created-for-my-azure-services"></a>Rekordami odwrotnego systemu DNS domyślne tworzonych dla moich usług systemu Azure?

Nie. Odwrotnym systemem DNS to funkcja opcjonalna. Rekordami odwrotnego systemu DNS danych nie domyślne są tworzone, jeśli nie zdecydujesz się na ich konfigurowania.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>Co to jest format w pełni kwalifikowana nazwa domeny (FQDN)?

Nazw FQDN, które są określone w kolejności do przodu i musi się kończyć znakiem pojedynczego znaku kropki (na przykład "app1.contoso.com.").

### <a name="what-happens-if-the-validation-check-for-the-reverse-dns-ive-specified-fails"></a>Co się stanie, jeśli wyszukiwanie wsteczne DNS zostały określone zakończy się niepowodzeniem?

W przypadku, gdy odwrotnego DNS sprawdzenie poprawności zakończy się niepowodzeniem, operacja konfigurowania odwrotnej rekord DNS nie powiedzie się. Popraw wartość odwrotnego DNS zgodnie z wymaganiami i ponów próbę.

### <a name="can-i-configure-reverse-dns-for-azure-app-service"></a>Czy można skonfigurować odwrotnym systemem DNS w usłudze Azure App Service?

Nie. Odwrotnym systemem DNS nie jest obsługiwana dla usługi Azure App Service.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-azure-service"></a>Czy można skonfigurować wiele rekordami odwrotnego systemu DNS w mojej usługi platformy Azure?

Nie. Platforma Azure obsługuje pojedynczy rekord DNS odwrotnego dla każdej usługi w chmurze platformy Azure lub publiczny adres IP.

### <a name="can-i-configure-reverse-dns-for-ipv6-publicipaddress-resources"></a>Można skonfigurować odwrotnym systemem DNS dla publicznego adresu IP protokołu IPv6 zasobów?

Nie. Usługa Azure obecnie obsługuje odwrotne DNS tylko dla publicznego adresu IP protokołu IPv4, zasobów i usług w chmurze.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>Czy mogę wysyłać wiadomości e-mail do domen zewnętrznych z moich usług Azure Compute?

Możliwości techniczne do wysyłania wiadomości e-mail bezpośrednio z wdrożeniu platformy Azure zależy od typu subskrypcji. Niezależnie od typu subskrypcji firma Microsoft zaleca używanie usług przekazywania poczty zaufanych wysyłanie poczty wychodzącej. Aby uzyskać więcej informacji, zobacz [zwiększone zabezpieczenia platformy Azure do wysyłania wiadomości E-mail — listopad 2017 Update](https://blogs.msdn.microsoft.com/mast/2017/11/15/enhanced-azure-security-for-sending-emails-november-2017-update/).

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat odwrotnym systemem DNS, zobacz [wyszukiwania wstecznego DNS w witrynie Wikipedia](https://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Dowiedz się, jak [hostowanie strefy wyszukiwania wstecznego dla usługodawcy internetowego, przypisany zakresowi adresów IP w usłudze Azure DNS](dns-reverse-dns-for-azure-services.md).

