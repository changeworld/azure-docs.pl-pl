---
title: Konfigurowanie przekazywania usług DNS dla plików platformy Azure | Dokumenty firmy Microsoft
description: Omówienie opcji sieciowych dla usług Azure Files.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 3/19/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 35dfbcb274721049f2160719222ca89038c93356
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80082500"
---
# <a name="configuring-dns-forwarding-for-azure-files"></a>Konfigurowanie przekazywania usług DNS dla plików platformy Azure
Usługa Azure Files umożliwia tworzenie prywatnych punktów końcowych dla kont magazynu zawierających udziały plików. Chociaż przydatne dla wielu różnych aplikacji, prywatne punkty końcowe są szczególnie przydatne do łączenia się z udziałami plików platformy Azure z sieci lokalnej przy użyciu połączenia SIECI VPN lub usługi ExpressRoute przy użyciu prywatnej komunikacji równorzędnej. 

Aby połączenia z kontem magazynu zostały przejmące się przez tunel sieciowy, w pełni kwalifikowana nazwa domeny (FQDN) konta magazynu musi zostać rozpoznana na prywatny adres IP prywatnego punktu końcowego. Aby to osiągnąć, należy przesłać dalej sufiks punktu końcowego magazynu (dla`core.windows.net` regionów chmury publicznej) do prywatnej usługi DNS platformy Azure dostępnej z poziomu sieci wirtualnej. W tym przewodniku pokazano, jak skonfigurować i skonfigurować przekazywanie dns, aby poprawnie rozpoznać adres IP prywatnego punktu końcowego konta magazynu.

Zdecydowanie zaleca się [przeczytanie planowania dla wdrożenia usługi Azure Files](storage-files-planning.md) i zagadnienia dotyczące sieci usługi Azure Files przed wykonaniem kroków [opisanych](storage-files-networking-overview.md) w tym artykule.

## <a name="overview"></a>Omówienie
Usługa Azure Files udostępnia dwa główne typy punktów końcowych dostępu do udziałów plików platformy Azure: 
- Publiczne punkty końcowe, które mają publiczny adres IP i są dostępne z dowolnego miejsca na świecie.
- Prywatne punkty końcowe, które istnieją w sieci wirtualnej i mają prywatny adres IP z przestrzeni adresowej tej sieci wirtualnej.

Publiczne i prywatne punkty końcowe istnieją na koncie magazynu platformy Azure. Konto magazynu jest konstrukcją zarządzania, która reprezentuje udostępnioną pulę magazynu, w której można wdrożyć wiele udziałów plików, a także inne zasoby magazynu, takie jak kontenery obiektów blob lub kolejki.

Każde konto magazynu ma w pełni kwalifikowaną nazwę domeny (FQDN). W przypadku regionów chmury publicznej ta nazwa `storageaccount.file.core.windows.net` FQDN jest zgodna ze wzorcem, gdzie `storageaccount` jest nazwa konta magazynu. Podczas wysyłania żądań dotyczących tej nazwy, takich jak instalowanie udziału na stacji roboczej przy użyciu protokołu SMB, system operacyjny wykonuje wyszukiwanie DNS w celu rozpoznania w pełni kwalifikowanej nazwy domeny na adres IP, którego może używać do wysyłania żądań SMB.

Domyślnie `storageaccount.file.core.windows.net` jest rozpoznawany jako adres IP publicznego punktu końcowego. Publiczny punkt końcowy dla konta magazynu jest obsługiwany w klastrze magazynu platformy Azure, który obsługuje wiele innych publicznych punktów końcowych kont magazynu. Podczas tworzenia prywatnego punktu końcowego prywatna strefa DNS jest połączona z siecią wirtualną, do której została dodana, z mapowaniem `storageaccount.file.core.windows.net` rekordu CNAME do wpisu rekordu A dla prywatnego adresu IP prywatnego punktu końcowego konta magazynu. Dzięki temu można `storageaccount.file.core.windows.net` używać FQDN w sieci wirtualnej i mieć go rozwiązać adres IP prywatnego punktu końcowego.

Ponieważ naszym ostatecznym celem jest dostęp do udziałów plików platformy Azure hostowanych na koncie magazynu z lokalnego tunelu sieciowego, takiego jak połączenie VPN lub ExpressRoute, należy skonfigurować lokalne serwery DNS do przesyłania dalej żądań przesyłanych na platformę Azure Pliki usługi do usługi Azure private DNS. Aby to osiągnąć, należy skonfigurować *warunkowe przekazywanie* `*.core.windows.net` (lub odpowiedni sufiks punktu końcowego magazynu dla instytucji rządowych STANÓW Zjednoczonych, Niemiec lub Chin chmury krajowe) do serwera DNS hostowanego w sieci wirtualnej platformy Azure. Ten serwer DNS będzie następnie rekursywnie przekazywać żądanie do prywatnej usługi DNS platformy Azure, która rozwiąże w pełni kwalifikowaną nazwę domeny konta magazynu na odpowiedni prywatny adres IP.

Konfigurowanie przekazywania dns dla usług Azure Files będzie wymagać uruchomienia maszyny wirtualnej do hostowania serwera DNS do przesyłania dalej żądań, jednak jest to krok jednorazowy dla wszystkich udziałów plików platformy Azure hostowanych w sieci wirtualnej. Ponadto nie jest to wyłączne wymaganie dla usługi Azure Files — każda usługa platformy Azure, która obsługuje prywatne punkty końcowe, do których chcesz uzyskać dostęp z lokalnego środowiska, może korzystać z przekazywania dalej DNS, które zostaną skonfigurowane w tym przewodniku: Magazyn obiektów Blob azure, SQL Azure, Cosmos DB; Itp. 

W tym przewodniku przedstawiono kroki konfigurowania przekazywania usług DNS dla punktu końcowego magazynu platformy Azure, więc oprócz usług Azure Files, żądania rozpoznawania nazw DNS dla wszystkich innych usług magazynu platformy Azure (magazyn obiektów Blob platformy Azure, magazyn tabel platformy Azure, magazyn kolejki platformy Azure itp.) do prywatnej usługi DNS platformy Azure. Dodatkowe punkty końcowe dla innych usług platformy Azure można również dodać w razie potrzeby. Przekierowanie DNS z powrotem do lokalnych serwerów DNS zostanie również skonfigurowane, umożliwiając zasoby w chmurze w sieci wirtualnej (takie jak serwer DFS-N) w celu rozpoznawania nazw komputerów lokalnych. 

## <a name="prerequisites"></a>Wymagania wstępne
Przed skonfigurowaniem przekierowania DNS do usługi Azure Files należy wykonać następujące kroki:

- Konto magazynu zawierające udział plików platformy Azure, który chcesz zainstalować. Aby dowiedzieć się, jak utworzyć konto magazynu i udział plików platformy Azure, zobacz [Tworzenie udziału plików platformy Azure](storage-how-to-create-file-share.md).
- Prywatny punkt końcowy dla konta magazynu. Aby dowiedzieć się, jak utworzyć prywatny punkt końcowy dla usług Azure Files, zobacz [Tworzenie prywatnego punktu końcowego](storage-files-networking-endpoints.md#create-a-private-endpoint).
- [Najnowsza wersja](https://docs.microsoft.com/powershell/azure/install-az-ps) modułu programu Azure PowerShell.

> [!Important]  
> W tym przewodniku przyjęto założenie, że serwer DNS w systemie Windows Server jest używany w środowisku lokalnym. Wszystkie kroki opisane w tym przewodniku są możliwe z dowolnego serwera DNS, a nie tylko windows serwera DNS.

## <a name="manually-configuring-dns-forwarding"></a>Ręczne konfigurowanie przekazywania dalej DNS
Jeśli masz już serwery DNS w sieci wirtualnej platformy Azure lub jeśli wolisz po prostu wdrożyć własne maszyny wirtualne jako serwery DNS według dowolnej metodologii używanej przez organizację, możesz ręcznie skonfigurować system DNS za pomocą wbudowanego serwera DNS Polecenia cmdlet programu PowerShell.

Na lokalnych serwerach DNS utwórz warunkową usługę `Add-DnsServerConditionalForwarderZone`przesyłania dalej za pomocą programu . Ta warunkowa usługa przesyłania dalej musi zostać wdrożona na wszystkich lokalnych serwerach DNS, aby była skuteczna w prawidłowym przekazywaniu ruchu na platformę Azure. Pamiętaj, `<azure-dns-server-ip>` aby zastąpić je odpowiednimi adresami IP dla danego środowiska.

```powershell
$vnetDnsServers = "<azure-dns-server-ip>", "<azure-dns-server-ip>"

$storageAccountEndpoint = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty StorageEndpointSuffix

Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers $vnetDnsServers
```

Na serwerach DNS w sieci wirtualnej platformy Azure należy również umieścić usługę przesyłania dalej w taki sposób, aby żądania strefy DNS konta magazynu `168.63.129.16`były kierowane do prywatnej usługi DNS platformy Azure, która jest kierowana przez zastrzeżony adres IP . (Pamiętaj, aby `$storageAccountEndpoint` wypełnić, jeśli używasz poleceń w ramach innej sesji programu PowerShell.)

```powershell
Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers "168.63.129.16"
```

## <a name="using-the-azure-files-hybrid-module-to-configure-dns-forwarding"></a>Konfigurowanie przekazywania dalej plików DNS za pomocą modułu Azure Files
Aby skonfigurować przekazywanie dns tak proste, jak to możliwe, udostępniliśmy automatyzację w module Azure Files Hybrid. Polecenia cmdlet przewidziane do manipulowania systemem DNS w tym module ułatwiają wdrażanie serwerów DNS w sieci wirtualnej platformy Azure i aktualizowanie lokalnych serwerów DNS w celu przekazania ich dalej. 

Jeśli nigdy nie był używany moduł hybrydowy usługi Azure Files, należy najpierw zainstalować go na stacji roboczej. Pobierz [najnowszą wersję](https://github.com/Azure-Samples/azure-files-samples/releases) modułu programu Azure Files Hybrid PowerShell:

```PowerShell
# Unzip the downloaded file
Expand-Archive -Path AzFilesHybrid.zip

# Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\AzFilesHybrid\CopyToPSPath.ps1 

# Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid
```

Wdrażanie rozwiązania do przekazywania dns ma dwa kroki, tworząc zestaw reguł przekazywania dns, który określa, które usługi platformy Azure, które chcesz przesyłać dalej żądania, i rzeczywiste wdrożenie usług przesyłania dalej DNS. 

Poniższy przykład przekazuje żądania do konta magazynu, włącznie żądań do usługi Azure Files, usługi Azure Blob storage, Usługi Azure Table Storage i Usługi Azure Queue storage. W razie potrzeby można dodać przekazywanie do reguły `-AzureEndpoints` dodatkowej `New-AzDnsForwardingRuleSet` usługi platformy Azure za pomocą parametru polecenia cmdlet. Pamiętaj, `<virtual-network-resource-group>`aby `<virtual-network-name>`zastąpić `<subnet-name>` , i z odpowiednimi wartościami dla środowiska.

```PowerShell
# Create a rule set, which defines the forwarding rules
$ruleSet = New-AzDnsForwardingRuleSet -AzureEndpoints StorageAccountEndpoint

# Deploy and configure DNS forwarders
New-AzDnsForwarder `
        -DnsForwardingRuleSet $ruleSet `
        -VirtualNetworkResourceGroupName "<virtual-network-resource-group>" `
        -VirtualNetworkName "<virtual-network-name>" `
        -VirtualNetworkSubnetName "<subnet-name>"
```

Dodatkowo może okazać się przydatne / konieczne, aby podać kilka dodatkowych parametrów:

| Nazwa parametru | Typ | Opis |
|----------------|------|-------------|
| `DnsServerResourceGroupName` | `string` | Domyślnie serwery DNS będą wdrażane w tej samej grupie zasobów co sieć wirtualna. Jeśli nie jest to pożądane, ten parametr umożliwia wybranie alternatywnej grupy zasobów dla nich do wdrożenia. |
| `DnsForwarderRootName` | `string` | Domyślnie serwery DNS, które są wdrażane `DnsFwder-*`na platformie Azure mają nazwy , gdzie gwiazdka jest wypełniana przez iteratora. Ten parametr zmienia katalog główny tej nazwy `DnsFwder`(tj. ). |
| `VmTemporaryPassword` | `SecureString` | Domyślnie losowe hasło jest wybierane dla tymczasowego konta domyślnego maszyny Wirtualnej przed przyłączeniem do domeny. Po dołączeniu do domeny konto domyślne jest wyłączone. |
| `DomainToJoin` | `string` | Domena, do łączyć maszyny wirtualne DNS do przyłączenia się. Domyślnie ta domena jest wybierana na podstawie domeny komputera, na którym są uruchomione polecenia cmdlet. |
| `DnsForwarderRedundancyCount` | `int` | Liczba maszyn wirtualnych DNS do wdrożenia dla sieci wirtualnej. Domyślnie `New-AzDnsForwarder` wdraża dwa serwery DNS w sieci wirtualnej platformy Azure w zestawie dostępności, aby zapewnić nadmiarowość. Liczba ta może zostać zmodyfikowana zgodnie z potrzebami. |
| `OnPremDnsHostNames` | `HashSet<string>` | Ręcznie określona lista lokalnych nazw hostów DNS do utworzenia usług przesyłania dalej. Ten parametr jest przydatny, gdy nie chcesz stosować usług przesyłania dalej na wszystkich lokalnych serwerach DNS, na przykład gdy masz szereg klientów z ręcznie określonymi nazwami DNS. |
| `Credential` | `PSCredential` | Poświadczenia używane podczas aktualizowania serwerów DNS. Jest to przydatne, gdy konto użytkownika, na którym się zalogowano, nie ma uprawnień do modyfikowania ustawień DNS. |
| `SkipParentDomain` | `SwitchParameter` | Domyślnie usługi przesyłania dalej DNS są stosowane do domeny najwyższego poziomu, która istnieje w twoim środowisku. Na przykład, `northamerica.corp.contoso.com` jeśli jest `corp.contoso.com`domeną podrzędną programu , usługa przesyłania `corp.contoso.com`dalej zostanie utworzona dla serwerów DNS skojarzonych z programem . Ten parametr spowoduje, że spedycyjnie zostaną utworzeni w `northamerica.corp.contoso.com`pliku . |

## <a name="confirm-dns-forwarders"></a>Potwierdź usługi przesyłania dalej DNS
Przed przetestowaniem, aby sprawdzić, czy usługi przesyłania dalej DNS zostały pomyślnie zastosowane, `Clear-DnsClientCache`zalecamy wyczyszczenie pamięci podręcznej DNS na lokalnej stacji roboczej przy użyciu . Aby sprawdzić, czy można pomyślnie rozpoznać w pełni kwalifikowaną `Resolve-DnsName` `nslookup`nazwę domeny konta magazynu, użyj lub .

```powershell
# Replace storageaccount.file.core.windows.net with the appropriate FQDN for your storage account.
# Note the proper suffix (core.windows.net) depends on the cloud your deployed in.
Resolve-DnsName -Name storageaccount.file.core.windows.net
```

Jeśli rozpoznawanie nazw zakończy się pomyślnie, powinien zostać wyświetlony rozwiązany adres IP zgodny z adresem IP konta magazynu.

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  29    Answer     csostoracct.privatelink.file.core.windows.net
net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 1769
Section    : Answer
IP4Address : 192.168.0.4
```

Jeśli masz już skonfigurowane połączenie VPN lub Usługi ExpressRoute, możesz również sprawdzić, `Test-NetConnection` czy połączenie TCP może zostać pomyślnie nawiązane do twojego konta magazynu.

```PowerShell
Test-NetConnection -ComputerName storageaccount.file.core.windows.net -CommonTCPPort SMB
```

## <a name="see-also"></a>Zobacz też
- [Planowanie wdrożenia usługi Azure Files](storage-files-planning.md)
- [Zagadnienia dotyczące sieci usług Azure Files](storage-files-networking-overview.md)
- [Konfigurowanie sieciowych punktów końcowych usługi Azure Files](storage-files-networking-endpoints.md)