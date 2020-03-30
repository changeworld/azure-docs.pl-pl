---
title: Zastąpienie podsieci usługi Azure Traffic Manager przy użyciu interfejsu wiersza polecenia platformy Azure | Dokumenty firmy Microsoft
description: Ten artykuł pomoże Ci zrozumieć, jak zastąpienie podsieci usługi Traffic Manager może służyć do zastępowania metody routingu profilu usługi Traffic Manager, aby kierować ruch do punktu końcowego na podstawie adresu IP użytkownika końcowego za pośrednictwem wstępnie zdefiniowanego zakresu adresów IP do mapowań punktów końcowych.
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.topic: article
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: rohink
ms.openlocfilehash: 818b692884bd9d31efd08663a582ebcfec2032e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938469"
---
# <a name="traffic-manager-subnet-override-using-azure-cli"></a>Zastępowanie podsieci usługi Traffic Manager przy użyciu interfejsu wiersza polecenia usługi Azure

Zastępowanie podsieci Usługi Traffic Manager umożliwia zmianę metody routingu profilu.  Dodanie zastąpienia spowoduje kierowanie ruchu na podstawie adresu IP użytkownika końcowego z wstępnie zdefiniowanym zakresem adresu IP do mapowania punktu końcowego. 

## <a name="how-subnet-override-works"></a>Jak działa zastępowanie podsieci

Gdy zastąpienia podsieci są dodawane do profilu menedżera ruchu, usługa Traffic Manager najpierw sprawdzi, czy adres IP użytkownika końcowego jest zastępowanie podsieci. Jeśli zostanie znaleziony, kwerenda DNS użytkownika zostanie przekierowana do odpowiedniego punktu końcowego.  Jeśli mapowanie nie zostanie znalezione, Usługa Traffic Manager powróci do oryginalnej metody routingu profilu. 

Zakresy adresów IP można określić jako zakresy CIDR (na przykład 1.2.3.0/24) lub jako zakresy adresów (na przykład 1.2.3.4-5.6.7.8). Zakresy adresów IP skojarzone z każdym punktem końcowym muszą być unikatowe dla tego punktu końcowego. Wszelkie nakładanie się zakresów adresów IP między różnymi punktami końcowymi spowoduje odrzucenie profilu przez menedżera ruchu.

Istnieją dwa typy profilów routingu, które obsługują zastąpienia podsieci:

* **Geograficzne** — jeśli Usługa Traffic Manager znajdzie zastąpienie podsieci dla adresu IP kwerendy DNS, będzie kierować kwerendę do punktu końcowego, niezależnie od kondycji punktu końcowego.
* **Wydajność** — jeśli Usługa Traffic Manager znajdzie zastąpienie podsieci dla adresu IP kwerendy DNS, będzie kierować ruch do punktu końcowego tylko wtedy, gdy jest w dobrej kondycji.  Usługa Traffic Manager powróci do heurystyki routingu wydajności, jeśli punkt końcowy zastąpienia podsieci nie jest w dobrej kondycji.

## <a name="create-a-traffic-manager-subnet-override"></a>Tworzenie zastąpienia podsieci usługi Traffic Manager

Aby utworzyć zastąpienie podsieci usługi Traffic Manager, można użyć interfejsu wiersza polecenia platformy Azure, aby dodać podsieci do zastąpienia punktu końcowego usługi Traffic Manager.

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="update-the-traffic-manager-endpoint-with-subnet-override"></a>Zaktualizuj punkt końcowy usługi Traffic Manager za pomocą zastąpienia podsieci.
Użyj interfejsu wiersza polecenia platformy Azure, aby zaktualizować punkt końcowy za pomocą [aktualizacji punktu końcowego menedżera ruchu sieciowego az](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update).

```azurecli

### Add a range of IPs ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 1.2.3.4-5.6.7.8 \
    --type AzureEndpoints

### Add a subnet ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 9.10.11.0:24 \
    --type AzureEndpoints

```

Zakresy adresów IP można usunąć, uruchamiając [aktualizację punktu końcowego menedżera ruchu sieciowego az](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update) z opcją **--remove.**

```azurecli

az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --remove subnets \
    --type AzureEndpoints

```
## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [metodach routingu ruchu](traffic-manager-routing-methods.md)usługi Traffic Manager .

Dowiedz się więcej o [metodzie routingu ruchu podsieci](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#subnet-traffic-routing-method)