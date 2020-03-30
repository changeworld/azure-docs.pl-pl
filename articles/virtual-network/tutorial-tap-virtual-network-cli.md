---
title: Tworzenie, zmienianie lub usuwanie sieci wirtualnej TAP — azure cli
description: Dowiedz się, jak utworzyć, zmienić lub usunąć tap sieci wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure.
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
ms.openlocfilehash: 56288a65dc9e5b12a12393965b9670e394146181
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234971"
---
# <a name="work-with-a-virtual-network-tap-using-the-azure-cli"></a>Praca z siecią wirtualną TAP przy użyciu interfejsu wiersza polecenia platformy Azure

Usługa Azure virtual network TAP (Terminal Access Point) umożliwia ciągłe przesyłanie strumieniowe ruchu sieciowego maszyny wirtualnej do modułu zbierającego pakiety sieciowego lub narzędzia analitycznego. Narzędzie do tworzenia lub analizy jest dostarczane przez partnera [urządzenia wirtualnego sieci.](https://azure.microsoft.com/solutions/network-appliances/) Aby uzyskać listę rozwiązań partnerskich, które są sprawdzane do pracy z siecią wirtualną TAP, zobacz [rozwiązania partnerów](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions). 

## <a name="create-a-virtual-network-tap-resource"></a>Tworzenie zasobu TAP w sieci wirtualnej

Przed utworzeniem zasobu TAP sieci wirtualnej należy przeczytać [wymagania wstępne.](virtual-network-tap-overview.md#prerequisites) Można uruchomić polecenia, które należy wykonać w [usłudze Azure Cloud Shell](https://shell.azure.com/bash)lub uruchamiając interfejs wiersza polecenia platformy Azure (CLI) z komputera. Usługa Azure Cloud Shell to bezpłatna powłoka interaktywna, która nie wymaga instalowania interfejsu wiersza polecenia platformy Azure na komputerze. Musisz zalogować się na platformie Azure przy za pomocą konta, które ma odpowiednie [uprawnienia](virtual-network-tap-overview.md#permissions). Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.46 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli). Tap sieci wirtualnej jest obecnie dostępny jako rozszerzenie. Aby zainstalować rozszerzenie, musisz `az extension add -n virtual-network-tap`uruchomić program . Jeśli korzystasz z interfejsu wiersza polecenia platformy `az login` Azure lokalnie, należy również uruchomić, aby utworzyć połączenie z platformą Azure.

1. Pobierz identyfikator subskrypcji do zmiennej, która jest używana w późniejszym kroku:

   ```azurecli-interactive
   subscriptionId=$(az account show \
   --query id \
   --out tsv)
   ```

2. Ustaw identyfikator subskrypcji, który będzie używany do tworzenia zasobu TAP sieci wirtualnej.

   ```azurecli-interactive
   az account set --subscription $subscriptionId
   ```

3. Ponownie zarejestruj identyfikator subskrypcji, który będzie używany do tworzenia zasobu TAP sieci wirtualnej. Jeśli podczas tworzenia zasobu TAP pojawia się błąd rejestracji, uruchom następujące polecenie:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Network --subscription $subscriptionId
   ```

4. Jeśli miejscem docelowym dla sieci wirtualnej TAP jest interfejs sieciowy w sieciowym urządzeniu wirtualnym dla modułu zbierającego lub narzędzia analitycznego -

   - Pobierz konfigurację IP interfejsu sieciowego urządzenia wirtualnego do zmiennej, która jest używana w późniejszym kroku. Identyfikator jest punktem końcowym, który zagreguje ruch TAP. Poniższy przykład pobiera identyfikator konfiguracji *ipconfig1* IP dla interfejsu sieciowego o nazwie *myNetworkInterface*, w grupie zasobów o nazwie *myResourceGroup:*

      ```azurecli-interactive
       IpConfigId=$(az network nic ip-config show \
       --name ipconfig1 \
       --nic-name myNetworkInterface \
       --resource-group myResourceGroup \
       --query id \
       --out tsv)
      ```

   - Utwórz tap sieci wirtualnej w regionie westcentralus azure przy użyciu identyfikatora konfiguracji IP jako miejsce docelowe i opcjonalne właściwości portu. Port określa port docelowy w konfiguracji IP interfejsu sieciowego, w którym zostanie odebrany ruch TAP:  

      ```azurecli-interactive
       az network vnet tap create \
       --resource-group myResourceGroup \
       --name myTap \
       --destination $IpConfigId \
       --port 4789 \
       --location westcentralus
      ```

5. Jeśli miejscem docelowym dla sieci wirtualnej TAP jest wewnętrzny moduł równoważenia obciążenia platformy Azure:
  
   - Pobierz konfigurację ip frontonijnego modułu równoważenia obciążenia platformy Azure do zmiennej, która jest używana w późniejszym kroku. Identyfikator jest punktem końcowym, który zagreguje ruch TAP. Poniższy przykład pobiera identyfikator *frontendipconfig1* frontonu konfiguracji IP dla modułu równoważenia obciążenia o nazwie *myInternalLoadBalancer*, w grupie zasobów o nazwie *myResourceGroup:*

      ```azurecli-interactive
      FrontendIpConfigId=$(az network lb frontend-ip show \
      --name frontendipconfig1 \
      --lb-name myInternalLoadBalancer \
      --resource-group myResourceGroup \
      --query id \
      --out tsv)
      ```

   - Utwórz tap sieci wirtualnej przy użyciu identyfikatora konfiguracji IP frontona jako miejsce docelowe i opcjonalne właściwości portu. Port określa port docelowy w konfiguracji ip front-endu, w którym zostanie odebrany ruch TAP:  

      ```azurecli-interactive
      az network vnet tap create \
      --resource-group myResourceGroup \
      --name myTap \
      --destination $FrontendIpConfigId \
      --port 4789 \
     --location westcentralus
     ```

6. Potwierdź utworzenie sieci wirtualnej TAP:

   ```azurecli-interactive
   az network vnet tap show \
   --resource-group myResourceGroup
   --name myTap
   ```

## <a name="add-a-tap-configuration-to-a-network-interface"></a>Dodawanie konfiguracji TAP do interfejsu sieciowego

1. Pobierz identyfikator istniejącego zasobu TAP sieci wirtualnej. Poniższy przykład pobiera tap sieci wirtualnej o nazwie *myTap* w grupie zasobów o nazwie *myResourceGroup:*

   ```azurecli-interactive
   tapId=$(az network vnet tap show \
   --name myTap \
   --resource-group myResourceGroup \
   --query id \
   --out tsv)
   ```

2. Utwórz konfigurację TAP na interfejsie sieciowym monitorowanej maszyny wirtualnej. Poniższy przykład tworzy konfigurację TAP dla interfejsu sieciowego o nazwie *myNetworkInterface:*

   ```azurecli-interactive
   az network nic vtap-config create \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --vnet-tap $tapId \
   --name mytapconfig \
   --subscription subscriptionId
   ```

3. Potwierdź utworzenie konfiguracji TAP:

   ```azurecli-interactive
   az network nic vtap-config show \
   --resource-group myResourceGroup \
   --nic-name myNetworkInterface \
   --name mytapconfig \
   --subscription subscriptionId
   ```

## <a name="delete-the-tap-configuration-on-a-network-interface"></a>Usuwanie konfiguracji TAP w interfejsie sieciowym

   ```azurecli-interactive
   az network nic vtap-config delete \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --name myTapConfig \
   --subscription subscriptionId
   ```

## <a name="list-virtual-network-taps-in-a-subscription"></a>Wyświetlanie listy tapp sieci wirtualnej w ramach subskrypcji

   ```azurecli-interactive
   az network vnet tap list
   ```

## <a name="delete-a-virtual-network-tap-in-a-resource-group"></a>Usuwanie tap sieci wirtualnej w grupie zasobów

   ```azurecli-interactive
   az network vnet tap delete \
   --resource-group myResourceGroup \
   --name myTap
   ```
