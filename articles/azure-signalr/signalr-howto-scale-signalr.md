---
title: Skalowanie wystąpienia usługi Azure SignalR
description: Dowiedz się, jak skalować wystąpienie usługi Azure SignalR, aby dodać lub zmniejszyć pojemność za pośrednictwem witryny Azure portal lub interfejsu wiersza polecenia platformy Azure.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: zhshang
ms.openlocfilehash: c8d74342e624b837c7ee803a2bcdcc12a3fb814b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75659291"
---
# <a name="how-to-scale-an-azure-signalr-service-instance"></a>Jak skalować wystąpienie usługi Azure SignalR?
W tym artykule pokazano, jak skalować wystąpienie usługi Azure SignalR. Istnieją dwa scenariusze skalowania, skalowania w górę i skalowania w poziomie.

* [Skalowanie w górę:](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)uzyskaj więcej jednostek, połączeń, wiadomości i nie tylko. Skalowanie w górę przez zmianę warstwy cenowej z Bezpłatna na Standardowa.
* [Skalowanie w poziomie:](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)Zwiększenie liczby jednostek SignalR. Można skalować w poziomie do 100 jednostek.

Zastosowanie ustawień skali zajmuje kilka minut. W rzadkich przypadkach, może upłynąć około 30 minut, aby zastosować. Nie wymagają one zmiany kodu lub ponownego rozmieszczenia aplikacji serwera.

Aby uzyskać informacje na temat cen i pojemności poszczególnych usług SignalR, zobacz [Szczegóły cen usługi Azure SignalR](https://azure.microsoft.com/pricing/details/signalr-service/).  

> [!NOTE]
> Zmiana usługi SignalR z warstwy **bezpłatnej** na warstwę **standardową** lub odwrotnie, adres IP usługi publicznej zostanie zmieniony i zwykle trwa 30-60 minut, aby propagować zmiany na serwery DNS w całym Internecie. Usługa może być nieosiągalna przed zaktualizowaniem systemu DNS. Ogólnie rzecz biorąc, nie zaleca się zbyt często zmieniać warstwy cenowej.


## <a name="scale-on-azure-portal"></a>Skalowanie w witrynie Azure portal

1. W przeglądarce otwórz witrynę [Azure Portal](https://portal.azure.com).

2. Na stronie SignalR Service z lewego menu wybierz opcję **Skaluj**.
   
3. Wybierz warstwę cenową, a następnie kliknij przycisk **Wybierz**. Ustaw liczbę jednostek dla **warstwy standardowej.**
   
    ![Skaluj na portalu](./media/signalr-howto-scale/signalr-howto-scale.png)

4. Kliknij przycisk **Zapisz**.

## <a name="scale-using-azure-cli"></a>Skalowanie przy użyciu interfejsu wiersza polecenia platformy Azure

Ten skrypt tworzy nowy zasób usługi SignalR **warstwy** bezpłatnej warstwy i nowej grupy zasobów i skaluje go do warstwy **standardowej.** 

```azurecli-interactive
#!/bin/bash

# Generate a unique suffix for the service name
let randomNum=$RANDOM*$RANDOM

# Generate a unique service and group name with the suffix
SignalRName=SignalRTestSvc$randomNum
#resource name must be lowercase
mySignalRSvcName=${SignalRName,,}
myResourceGroupName=$SignalRName"Group"

# Create resource group 
az group create --name $myResourceGroupName --location eastus

# Create the Azure SignalR Service resource
az signalr create \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Free_F1 \
  --service-mode Default

# Scale up to Standard Tier, and scale out to 50 units
az signalr update \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Standard_S1 \
  --unit-count 50
```

Zanotuj rzeczywistą nazwę wygenerowaną dla nowej grupy zasobów. Tej nazwy grupy zasobów użyjesz, gdy zechcesz usunąć wszystkie zasoby w grupie.

[!INCLUDE [cli-script-clean-up](../../includes/cli-script-clean-up.md)]

## <a name="compare-pricing-tiers"></a>Porównanie warstw cenowych

Aby uzyskać szczegółowe informacje, takie jak dołączone komunikaty i połączenia dla każdej warstwy cenowej, zobacz [Szczegóły cen usług SignalR](https://azure.microsoft.com/pricing/details/signalr-service/).

Aby zapoznać się z tabelą limitów usług, przydziałów i ograniczeń w każdej warstwie, zobacz [Limity usługi SignalR](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-signalr-service-limits).

## <a name="next-steps"></a>Następne kroki

W tym przewodniku dowiesz się, jak skalować pojedyncze wystąpienie usługi SignalR.

Wiele punktów końcowych są również obsługiwane do skalowania, dzielenia na fragmenty i scenariuszy między regionami.

> [!div class="nextstepaction"]
> [skalowanie usługi SignalR z wieloma wystąpieniami](./signalr-howto-scale-multi-instances.md)
