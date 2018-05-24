---
title: Samouczek — tworzenie zestawu skalowania maszyn wirtualnych dla systemu Linux na platformie Azure | Microsoft Docs
description: Z tego samouczka dowiesz się, jak za pomocą interfejsu wiersza polecenia platformy Azure 2.0 utworzyć i wdrożyć aplikację o wysokiej dostępności na maszynach wirtualnych z systemem Linux przy użyciu zestawu skalowania maszyn wirtualnych
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 12/15/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 741cabd37a5a508257f0307dfec25b5bb2d25153
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-linux-with-the-azure-cli-20"></a>Samouczek: tworzenie zestawu skalowania maszyn wirtualnych i wdrażanie aplikacji o wysokiej dostępności w systemie Windows za pomocą interfejsu wiersza polecenia platformy Azure 2.0

Zestaw skalowania maszyn wirtualnych umożliwia wdrożenie zestawu identycznych, automatycznie skalowanych maszyn wirtualnych, oraz zarządzanie nimi. Maszyny wirtualne w zestawie skalowania można skalować ręcznie lub można zdefiniować reguły skalowania automatycznego na podstawie użycia zasobów, takich jak procesor CPU, zapotrzebowanie na pamięć lub ruch sieciowy. W tym samouczku wdrażany jest zestaw skalowania maszyn wirtualnych na platformie Azure. Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie aplikacji do skalowania za pomocą pliku cloud-init
> * Tworzenie zestawu skalowania maszyn wirtualnych
> * Zwiększanie lub zmniejszanie liczby wystąpień w zestawie skalowania
> * Tworzenie reguł skalowania automatycznego
> * Wyświetlanie informacji o połączeniach dla wystąpień zestawu skalowania
> * Korzystanie z dysków danych w zestawie skalowania

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.30 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="scale-set-overview"></a>Omówienie zestawów skalowania
Zestaw skalowania maszyn wirtualnych umożliwia wdrożenie zestawu identycznych, automatycznie skalowanych maszyn wirtualnych, oraz zarządzanie nimi. Maszyny wirtualne w zestawie skalowania są dystrybuowane w ramach domen aktualizacji i usterek logiki w co najmniej jednej *grupie umieszczania*. Są to grupy podobnie skonfigurowanych maszyn wirtualnych — podobne do [zestawów dostępności](tutorial-availability-sets.md).

Maszyny wirtualne są tworzone zgodnie z potrzebami w zestawie skalowania. W celu kontrolowania tego, jak i kiedy maszyny wirtualne są dodawane lub usuwane z zestawu skalowania, definiuje się reguły skalowania automatycznego. Reguły te mogą być wyzwalane na podstawie metryk, takich jak obciążenie procesora, użycie pamięci lub ruch sieciowy.

Zestawy skalowania obsługują maksymalnie 1000 maszyn wirtualnych, gdy jest używany obraz platformy Azure. W przypadku obciążeń ze znaczącymi wymaganiami dotyczącymi dostosowywania instalacji lub maszyn wirtualnych warto rozważyć [utworzenie niestandardowego obrazu maszyny wirtualnej](tutorial-custom-images.md). Przy użyciu obrazu niestandardowego można utworzyć maksymalnie 300 maszyn wirtualnych w zestawie skalowania.


## <a name="create-an-app-to-scale"></a>Tworzenie aplikacji do skalowania
Dla środowiska produkcyjnego warto rozważyć [utworzenie niestandardowego obrazu maszyny wirtualnej](tutorial-custom-images.md) zawierającego już zainstalowaną i skonfigurowaną aplikację. Ten samouczek obejmuje dostosowywanie maszyn wirtualnych przy pierwszym rozruchu w celu szybkiego zobaczenia zestawu skalowania w działaniu.

Poprzedni samouczek dotyczył [dostosowywania maszyny wirtualnej z systemem Linux przy pierwszym rozruchu](tutorial-automate-vm-deployment.md) przy użyciu pliku cloud-init. Tego samego pliku konfiguracji cloud-init można użyć do zainstalowania aparatu NGINX i uruchomienia prostej aplikacji Node.js: „Hello World”. 

W bieżącej powłoce utwórz plik o nazwie *cloud-init.txt* i wklej poniższą konfigurację. Na przykład utwórz plik w usłudze Cloud Shell, a nie na maszynie lokalnej. Wprowadź `sensible-editor cloud-init.txt`, aby utworzyć plik i wyświetlić listę dostępnych edytorów. Upewnij się, że skopiowano cały plik cloud-init chmury, a szczególnie pierwszy wiersz:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```


## <a name="create-a-scale-set"></a>Tworzenie zestawu skalowania
Zanim będzie można utworzyć zestaw skalowania, utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroupScaleSet* w lokalizacji *eastus*:

```azurecli-interactive 
az group create --name myResourceGroupScaleSet --location eastus
```

Teraz utwórz zestaw skalowania maszyn wirtualnych przy użyciu polecenia [az vmss create](/cli/azure/vmss#az_vmss_create). W poniższym przykładzie pokazano tworzenie zestawu skalowania o nazwie *myScaleSet*, dostosowywanie maszyny wirtualnej za pomocą pliku cloud-init i generowanie kluczy SSH, jeśli nie istnieją:

```azurecli-interactive 
az vmss create \
  --resource-group myResourceGroupScaleSet \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys
```

Utworzenie i skonfigurowanie wszystkich zasobów zestawu skalowania i maszyn wirtualnych trwa kilka minut. Pewne zadania w tle działają nadal po powrocie do wiersza polecenia w interfejsie wiersza polecenia platformy Azure. Może upłynąć kilka minut, zanim będzie można uzyskać dostęp do aplikacji.


## <a name="allow-web-traffic"></a>Zezwalanie na ruch internetowy
Jako część zestawu skalowania maszyn wirtualnych został automatycznie utworzony moduł równoważenia obciążenia. Moduł równoważenia obciążenia dystrybuuje ruch w ramach zestawu zdefiniowanych maszyn wirtualnych przy użyciu reguł modułu równoważenia obciążenia. Więcej informacji o pojęciach dotyczących modułu równoważenia obciążenia i jego konfiguracji podano w następnym samouczku: [Jak równoważyć obciążenie maszyn wirtualnych na platformie Azure](tutorial-load-balancer.md).

Aby zezwolić na docieranie ruchu do aplikacji internetowej, utwórz regułę za pomocą polecenia [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create). W poniższym przykładzie pokazano tworzenie reguły o nazwie *myLoadBalancerRuleWeb*:

```azurecli-interactive 
az network lb rule create \
  --resource-group myResourceGroupScaleSet \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

## <a name="test-your-app"></a>Testowanie aplikacji
Aby zobaczyć aplikację Node.js w Internecie, uzyskaj publiczny adres IP modułu równoważenia obciążenia za pomocą polecenia [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show). W poniższym przykładzie pokazano uzyskiwanie adresu IP dla modułu *myScaleSetLBPublicIP* utworzonego w ramach zestawu skalowania:

```azurecli-interactive 
az network public-ip show \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSetLBPublicIP \
    --query [ipAddress] \
    --output tsv
```

Wprowadź publiczny adres IP w przeglądarce internetowej. Aplikacja zostanie wyświetlona — wraz z nazwą hosta maszyny wirtualnej, do której moduł równoważenia obciążenia kieruje ruch:

![Uruchamianie aplikacji Node.js](./media/tutorial-create-vmss/running-nodejs-app.png)

Aby zobaczyć zestaw skalowania w działaniu, możesz wymusić odświeżenie przeglądarki internetowej i przyjrzeć się temu, jak moduł równoważenia obciążenia rozdziela ruch między wszystkie maszyny wirtualne, na których działa aplikacja.


## <a name="management-tasks"></a>Zadania zarządzania
W całym cyklu życia zestawu skalowania konieczne może być uruchomienie jednego lub większej liczby zadań zarządzania. Ponadto może pojawić się potrzeba tworzenia skryptów automatyzujących różne zadania cyklu życia. Interfejs wiersza polecenia platformy Azure 2.0 umożliwia szybkie wykonywanie tych zadań. Poniżej przedstawiono kilka typowych zadań.

### <a name="view-vms-in-a-scale-set"></a>Wyświetlanie maszyn wirtualnych w zestawie skalowania
Aby wyświetlić listę uruchomionych maszyn wirtualnych w zestawie skalowania, użyj polecenia [az vmss list-instances](/cli/azure/vmss#az_vmss_list_instances) w następujący sposób:

```azurecli-interactive 
az vmss list-instances \
  --resource-group myResourceGroupScaleSet \
  --name myScaleSet \
  --output table
```

Dane wyjściowe są podobne do poniższego przykładu:

```azurecli-interactive 
  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup            VmId
------------  --------------------  ----------  ------------  -------------------  -----------------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            MYRESOURCEGROUPSCALESET  c72ddc34-6c41-4a53-b89e-dd24f27b30ab
           3  True                  eastus      myScaleSet_3  Succeeded            MYRESOURCEGROUPSCALESET  44266022-65c3-49c5-92dd-88ffa64f95da
```


### <a name="increase-or-decrease-vm-instances"></a>Zwiększanie lub zmniejszanie liczby wystąpień maszyn wirtualnych
Aby wyświetlić liczbę bieżących wystąpień w zestawie skalowania, użyj polecenia [az vmss show](/cli/azure/vmss#az_vmss_show) i zapytania *sku.capacity*:

```azurecli-interactive 
az vmss show \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

Następnie możesz ręcznie zwiększyć lub zmniejszyć liczbę maszyn wirtualnych w zestawie skalowania za pomocą polecenia [az vmss scale](/cli/azure/vmss#az_vmss_scale). W poniższym przykładzie liczba maszyn wirtualnych w zestawie skalowania jest ustawiana na *3*:

```azurecli-interactive 
az vmss scale \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --new-capacity 3
```


### <a name="configure-autoscale-rules"></a>Konfigurowanie skalowania automatycznego
Zamiast ręcznie skalować liczbę wystąpień w zestawie skalowania, można zdefiniować reguły automatycznego skalowania. Te reguły monitorują wystąpienia w zestawie skalowania i odpowiednio reagują na podstawie zdefiniowanych metryk i progów. W poniższym przykładzie skala liczby wystąpień jest zwiększana o jedno, gdy średnie obciążenie procesora przekracza 60% w okresie 5 minut. Jeśli w okresie 5 minut średnie obciążenie procesora spadnie poniżej 30%, skala liczby wystąpień zostanie zmniejszona o jedno. Identyfikator subskrypcji jest używany do tworzenia identyfikatorów URI zasobów dla różnych składników zestawu skalowania. Aby utworzyć te reguły za pomocą polecenia [az monitor autoscale-settings create](/cli/azure/monitor/autoscale-settings#az_monitor_autoscale_settings_create), skopiuj i wklej poniższy profil polecenia skalowania automatycznego:

```azurecli-interactive 
sub=$(az account show --query id -o tsv)

az monitor autoscale-settings create \
    --resource-group myResourceGroupScaleSet \
    --name autoscale \
    --parameters '{"autoscale_setting_resource_name": "autoscale",
      "enabled": true,
      "location": "East US",
      "notifications": [],
      "profiles": [
        {
          "name": "Auto created scale condition",
          "capacity": {
            "minimum": "2",
            "maximum": "10",
            "default": "2"
          },
          "rules": [
            {
              "metricTrigger": {
                "metricName": "Percentage CPU",
                "metricNamespace": "",
                "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/myResourceGroupScaleSet/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet",
                "metricResourceLocation": "eastus",
                "timeGrain": "PT1M",
                "statistic": "Average",
                "timeWindow": "PT5M",
                "timeAggregation": "Average",
                "operator": "GreaterThan",
                "threshold": 70
              },
              "scaleAction": {
                "direction": "Increase",
                "type": "ChangeCount",
                "value": "1",
                "cooldown": "PT5M"
              }
            },
            {
              "metricTrigger": {
                "metricName": "Percentage CPU",
                "metricNamespace": "",
                "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/myResourceGroupScaleSet/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet",
                "metricResourceLocation": "eastus",
                "timeGrain": "PT1M",
                "statistic": "Average",
                "timeWindow": "PT5M",
                "timeAggregation": "Average",
                "operator": "LessThan",
                "threshold": 30
              },
              "scaleAction": {
                "direction": "Decrease",
                "type": "ChangeCount",
                "value": "1",
                "cooldown": "PT5M"
              }
            }
          ]
        }
      ],
      "tags": {},
      "target_resource_uri": "/subscriptions/'$sub'/resourceGroups/myResourceGroupScaleSet/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet"
    }'
```

Aby ponownie użyć tego profilu skalowania automatycznego, można utworzyć plik JSON (JavaScript Object Notation) i przekazać go do polecenia `az monitor autoscale-settings create` z parametrem `--parameters @autoscale.json`. Aby uzyskać więcej informacji dotyczących projektowania na temat korzystania ze skalowania automatycznego, zobacz [najlepsze rozwiązania w zakresie skalowania automatycznego](/azure/architecture/best-practices/auto-scaling).


### <a name="get-connection-info"></a>Uzyskiwanie informacji o połączeniu
Aby uzyskać informacje o połączeniu związane z maszynami wirtualnymi w używanych zestawach skalowania, użyj polecenia [az vmss list-instance-connection-info](/cli/azure/vmss#az_vmss_list_instance_connection_info). To polecenie zwraca publiczny adres IP i port dla każdej maszyny wirtualnej, która umożliwia nawiązanie połączenia przy użyciu powłoki SSH:

```azurecli-interactive 
az vmss list-instance-connection-info \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet
```


## <a name="use-data-disks-with-scale-sets"></a>Korzystanie z dysków danych z zestawami skalowania
Można tworzyć dyski danych i używać ich z zestawami skalowania. W poprzednim samouczku przedstawiono sposób [zarządzania dyskami platformy Azure](tutorial-manage-disks.md) oraz przedstawiono najlepsze rozwiązania i ulepszenia wydajności dotyczące tworzenia aplikacji na dyskach danych zamiast na dyskach systemu operacyjnego.

### <a name="create-scale-set-with-data-disks"></a>Tworzenie zestawu skalowania z dyskami danych
Aby utworzyć zestaw skalowania z dołączonymi dyskami danych, dodaj parametr `--data-disk-sizes-gb` do polecenia [az vmss create](/cli/azure/vmss#az_vmss_create). W poniższym przykładzie tworzony jest zestaw skalowania z dyskami danych o rozmiarze *50* GB dołączonymi do poszczególnych wystąpień:

```azurecli-interactive 
az vmss create \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSetDisks \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --custom-data cloud-init.txt \
    --admin-username azureuser \
    --generate-ssh-keys \
    --data-disk-sizes-gb 50
```

Usunięcie wystąpienia z zestawu skalowania powoduje też usunięcie wszelkich dołączonych dysków danych.

### <a name="add-data-disks"></a>Dodawanie dysków danych
Aby dodać dysk danych do wystąpień w zestawie skalowania, użyj polecenia [az vmss disk attach](/cli/azure/vmss/disk#az_vmss_disk_attach). W poniższym przykładzie do każdego wystąpienia dodano dysk o rozmiarze *50* GB:

```azurecli-interactive 
az vmss disk attach \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --size-gb 50 \
    --lun 2
```

### <a name="detach-data-disks"></a>Odłączanie dysków danych
Aby usunąć dysk danych z wystąpień w zestawie skalowania, użyj polecenia [az vmss disk detach](/cli/azure/vmss/disk#az_vmss_disk_detach). W poniższym przykładzie z każdego wystąpienia usunięto dysk danych w jednostce LUN *2*:

```azurecli-interactive 
az vmss disk detach \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --lun 2
```


## <a name="next-steps"></a>Następne kroki
W tym samouczku tworzony jest zestaw skalowania maszyn wirtualnych. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie aplikacji do skalowania za pomocą pliku cloud-init
> * Tworzenie zestawu skalowania maszyn wirtualnych
> * Zwiększanie lub zmniejszanie liczby wystąpień w zestawie skalowania
> * Tworzenie reguł skalowania automatycznego
> * Wyświetlanie informacji o połączeniach dla wystąpień zestawu skalowania
> * Korzystanie z dysków danych w zestawie skalowania

Przejdź do kolejnego samouczka, aby uzyskać więcej informacji o pojęciach dotyczących równoważenia obciążenia dla maszyn wirtualnych.

> [!div class="nextstepaction"]
> [Równoważenie obciążenia maszyn wirtualnych](tutorial-load-balancer.md)
