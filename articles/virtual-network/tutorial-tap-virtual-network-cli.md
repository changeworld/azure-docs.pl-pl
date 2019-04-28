---
title: Tworzenie, zmienianie lub usuwanie sieci wirtualnej wzorca TAP — interfejs wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzenie, zmienianie lub usuwanie sieci wirtualnej NACIŚNIJ przy użyciu wiersza polecenia platformy Azure.
services: virtual-network
documentationcenter: na
author: karthikananth
manager: ganesr
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/18/2018
ms.author: kaanan
ms.openlocfilehash: 3d95a9ea555cceda82530eb5c487eeb993c1a678
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743194"
---
# <a name="work-with-a-virtual-network-tap-using-the-azure-cli"></a>Praca z sieci wirtualnej, NACIŚNIJ przy użyciu wiersza polecenia platformy Azure

Usługa Azure virtual network TAP (Terminal punktem dostępu) umożliwia ciągłego strumienia do narzędzia sieci pakiet modułu zbierającego lub analizy ruchu sieciowego maszyny wirtualnej. Narzędzie modułu zbierającego lub analizy są dostarczane przez [wirtualnego urządzenia sieciowego](https://azure.microsoft.com/solutions/network-appliances/) partnera. Aby uzyskać listę rozwiązań partnerskich, które są sprawdzane do pracy z usługą virtual network TAP zobacz [rozwiązania partnerskie](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions). 

## <a name="create-a-virtual-network-tap-resource"></a>Tworzenie sieci wirtualnej wybierz zasób

Odczyt [wymagania wstępne](virtual-network-tap-overview.md#prerequisites) przed utworzeniem sieci wirtualnej wybierz zasób. Możesz uruchamiać polecenia, które należy wykonać w [usługi Azure Cloud Shell](https://shell.azure.com/bash), lub przy użyciu interfejsu wiersza polecenia platformy Azure (CLI) z komputera. Azure Cloud Shell to bezpłatna interaktywna powłoka, która nie wymaga instalowania interfejsu wiersza polecenia platformy Azure na komputerze. Musisz się zalogować do platformy Azure przy użyciu konta które ma odpowiednie [uprawnienia](virtual-network-tap-overview.md#permissions). Ten artykuł wymaga interfejsu wiersza polecenia Azure w wersji 2.0.46 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli). Virtual network TAP jest obecnie dostępna jako rozszerzenie. Aby zainstalować rozszerzenie, należy uruchomić `az extension add -n virtual-network-tap`. Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, trzeba będzie również uruchomić `az login` do utworzenia połączenia z platformą Azure.

1. Pobierz identyfikator subskrypcji do zmiennej, który jest używany w kolejnym kroku:

   ```azurecli-interactive
   subscriptionId=$(az account show \
   --query id \
   --out tsv)
   ```

2. Ustaw identyfikator subskrypcji, używanego do tworzenia sieci wirtualnej zasobów NACIŚNIJ.

   ```azurecli-interactive
   az account set --subscription $subscriptionId
   ```

3. Należy ponownie zarejestrować identyfikator subskrypcji, które będzie używane, aby utworzyć sieć wirtualną zasobu wzorca TAP. Jeśli wystąpi błąd rejestracji podczas tworzenia zasobu wzorca TAP, uruchom następujące polecenie:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Network --subscription $subscriptionId
   ```

4. Jeśli miejsce docelowe dla sieci wirtualnej wzorca TAP jest interfejs sieciowy na wirtualne urządzenie sieciowe dla modułu zbierającego lub narzędzie do analizy —

   - Pobieranie konfiguracji IP interfejsu sieciowego urządzenia wirtualnego w sieci do zmiennej, który jest używany w kolejnym kroku. Identyfikator jest punktem końcowym, która będzie używana do agregowania ruchu wzorca TAP. Poniższy przykład pobiera identyfikator *ipconfig1* konfiguracji adresów IP dla karty sieciowej o nazwie *myNetworkInterface*, w grupie zasobów o nazwie *myResourceGroup*:

      ```azurecli-interactive
       IpConfigId=$(az network nic ip-config show \
       --name ipconfig1 \
       --nic-name myNetworkInterface \
       --resource-group myResourceGroup \
       --query id \
       --out tsv)
      ```

   - Utwórz sieć wirtualną wzorca TAP w regionie westcentralus platformy azure przy użyciu Identyfikatora konfiguracji adresu IP jako miejsca docelowego, a właściwość opcjonalnego portu. Port Określa port docelowy, na którym będą odbierane ruchu wzorca TAP konfiguracji adresu IP interfejsu sieciowego:  

      ```azurecli-interactive
       az network vnet tap create \
       --resource-group myResourceGroup \
       --name myTap \
       --destination $IpConfigId \
       --port 4789 \
       --location westcentralus
      ```

5. Wybierz lokalizację docelową dla sieci wirtualnej czy azure wewnętrznego modułu równoważenia obciążenia:
  
   - Pobieranie konfiguracji IP frontonu Azure wewnętrznego modułu równoważenia obciążenia do zmiennej, który jest używany w kolejnym kroku. Identyfikator jest punktem końcowym, która będzie używana do agregowania ruchu wzorca TAP. Poniższy przykład pobiera identyfikator *frontendipconfig1* konfiguracji IP frontonu modułu równoważenia obciążenia o nazwie *myInternalLoadBalancer*, w grupie zasobów o nazwie  *myResourceGroup*:

      ```azurecli-interactive
      FrontendIpConfigId=$(az network lb frontend-ip show \
      --name frontendipconfig1 \
      --lb-name myInternalLoadBalancer \
      --resource-group myResourceGroup \
      --query id \
      --out tsv)
      ```
   - Tworzenie sieci wirtualnej NACIŚNIJ jako miejsca docelowego, a właściwość opcjonalnego portu przy użyciu Identyfikatora konfiguracji adresu IP frontonu. Port Określa port docelowy w konfiguracji protokołu IP frontonu, gdy zostanie odebrany ruch wzorca TAP:  

      ```azurecli-interactive
      az network vnet tap create \
      --resource-group myResourceGroup \
      --name myTap \
      --destination $FrontendIpConfigId \
      --port 4789 \
     --location westcentralus
     ```

6. Upewnij się, tworzenie PODSŁUCHU sieci wirtualnej:

   ```azurecli-interactive
   az network vnet tap show \
   --resource-group myResourceGroup
   --name myTap
   ```

## <a name="add-a-tap-configuration-to-a-network-interface"></a>Dodaj konfigurację wzorca TAP do interfejsu sieciowego

1. Pobierz identyfikator istniejącej sieci wirtualnej wybierz zasób. Poniższy przykład pobiera sieć wirtualną o nazwie wzorca TAP *myTap* w grupie zasobów o nazwie *myResourceGroup*:

   ```azurecli-interactive
   tapId=$(az network vnet tap show \
   --name myTap \
   --resource-group myResourceGroup \
   --query id \
   --out tsv)
   ```

2. Utwórz konfigurację wzorca TAP interfejsu sieciowego monitorowanych maszyny wirtualnej. Poniższy przykład umożliwia utworzenie konfiguracji wzorca TAP, dla karty sieciowej o nazwie *myNetworkInterface*:

   ```azurecli-interactive
   az network nic vtap-config create \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --vnet-tap $tapId \
   --name mytapconfig \
   --subscription subscriptionId
   ```

3. Upewnij się, Tworzenie konfiguracji wzorca TAP:

   ```azurecli-interactive
   az network nic vtap-config show \
   --resource-group myResourceGroup \
   --nic-name myNetworkInterface \
   --name mytapconfig \
   --subscription subscriptionId
   ```

## <a name="delete-the-tap-configuration-on-a-network-interface"></a>Usuń konfigurację wzorca TAP w interfejsie sieciowym

   ```azure-cli-interactive
   az network nic vtap-config delete \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --name myTapConfig \
   --subscription subscriptionId
   ```

## <a name="list-virtual-network-taps-in-a-subscription"></a>Sieć wirtualna listy naciśnięcia w ramach subskrypcji

   ```azurecli-interactive
   az network vnet tap list
   ```

## <a name="delete-a-virtual-network-tap-in-a-resource-group"></a>Usuń PODSŁUCHU sieci wirtualnych w grupie zasobów

   ```azurecli-interactive
   az network vnet tap delete \
   --resource-group myResourceGroup \
   --name myTap
   ```
