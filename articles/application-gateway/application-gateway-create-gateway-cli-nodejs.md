---
title: Utwórz bramę aplikacji Azure — Azure CLI 1.0 | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć bramę aplikacji przy użyciu 1.0 interfejsu wiersza polecenia Azure Resource Manager
services: application-gateway
documentationcenter: na
author: vhorne
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: c2f6516e-3805-49ac-826e-776b909a9104
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: victorh
ms.openlocfilehash: 3d67e896da5e616e443fc4e1edd9aaafb0f0e2f9
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
---
# <a name="create-an-application-gateway-by-using-the-azure-cli"></a>Tworzenie bramy aplikacji przy użyciu wiersza polecenia platformy Azure

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-gateway-portal.md)
> * [Azure Resource Manager — program PowerShell](application-gateway-create-gateway-arm.md)
> * [Klasyczny portal Azure — program PowerShell](application-gateway-create-gateway.md)
> * [Szablon usługi Azure Resource Manager](application-gateway-create-gateway-arm-template.md)
> * [Interfejs wiersza polecenia platformy Azure 1.0](application-gateway-create-gateway-cli.md)
> * [Interfejs wiersza polecenia platformy Azure 2.0](application-gateway-create-gateway-cli.md)
> 
> 

Usługa Azure Application Gateway to moduł równoważenia obciążenia warstwy 7. Udostępnia tryb failover, oparty na wydajności routing żądań HTTP między różnymi serwerami — w chmurze i lokalnymi. Brama aplikacji w zawiera następujące funkcje dostarczania aplikacji: HTTP załadować sondy kondycji niestandardowych równoważenia koligacji na podstawie plików cookie sesji i odciążanie protokołu Secure Sockets Layer (SSL) i obsługę wielu lokacji.

## <a name="prerequisite-install-the-azure-cli"></a>Wymaganie wstępne: Instalowanie interfejsu wiersza polecenia platformy Azure

Aby wykonać kroki opisane w tym artykule, należy [instalowanie interfejsu wiersza polecenia platformy Azure dla komputerów Mac, Linux i Windows (Azure CLI)](../xplat-cli-install.md) i trzeba [Zaloguj się do usługi Azure](/cli/azure/authenticate-azure-cli). 

> [!NOTE]
> Jeśli nie masz konta platformy Azure, można je utworzyć. Zarejestruj się, aby skorzystać z [bezpłatnego demo](../active-directory/sign-up-organization.md).

## <a name="scenario"></a>Scenariusz

W tym scenariuszu Dowiedz się jak utworzyć bramę aplikacji przy użyciu portalu Azure.

W tym scenariuszu obejmują:

* Utwórz bramę aplikacji średnia z dwóch wystąpień.
* Tworzenie sieci wirtualnej o nazwie ContosoVNET z zarezerwowanym blokiem CIDR 10.0.0.0/16.
* Utwórz podsieć o nazwie subnet01, która używa 10.0.0.0/28 bloku CIDR.

> [!NOTE]
> Dodatkowa konfiguracja bramy aplikacji, w tym kondycji niestandardowych sondy, adresy w puli zaplecza i dodatkowe reguły są konfigurowane po skonfigurowaniu bramy aplikacji i nie podczas pierwszego wdrożenia.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Brama aplikacji w Azure wymaga jego własnej podsieci. Podczas tworzenia sieci wirtualnej, upewnij się, że pozostanie wystarczająco dużo przestrzeni adresowej do mają wiele podsieci. Po wdrożono bramę aplikacji do podsieci bramy aplikacji tylko dodatkowe mogą mają zostać dodane do tej podsieci.

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Otwórz **wiersza polecenia usługi Microsoft Azure**i zaloguj się. 

```azurecli-interactive
azure login
```

Po wpisaniu powyższego przykładu znajduje się kod. Przejdź do https://aka.ms/devicelogin w przeglądarce, aby kontynuować proces logowania.

![cmd przedstawiający urządzenia logowania][1]

W przeglądarce wprowadź otrzymany kod. Nastąpi przekierowanie do strony logowania.

![przeglądarki, aby wprowadzić kod][2]

Po wprowadzeniu kodu użytkownik jest zalogowany, zamknij przeglądarkę, aby kontynuować na ze scenariuszem.

![pomyślnie zalogował się][3]

## <a name="switch-to-resource-manager-mode"></a>Przełącz tryb Resource Manager

```azurecli-interactive
azure config mode arm
```

## <a name="create-the-resource-group"></a>Tworzenie grupy zasobów

Przed utworzeniem bramy aplikacji, tworzona jest grupa zasobów zawiera bramy aplikacji. Poniżej przedstawiono polecenia.

```azurecli-interactive
azure group create \
--name ContosoRG \
--location eastus
```

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Po utworzeniu grupy zasobów sieci wirtualnej jest tworzona dla bramy aplikacji.  W poniższym przykładzie przestrzeni adresowej został jako 10.0.0.0/16 zgodnie z definicją w uwagach do poprzedniego scenariusza.

```azurecli-interactive
azure network vnet create \
--name ContosoVNET \
--address-prefixes 10.0.0.0/16 \
--resource-group ContosoRG \
--location eastus
```

## <a name="create-a-subnet"></a>Tworzenie podsieci

Po utworzeniu sieci wirtualnej podsieci jest dodawany do bramy aplikacji.  Jeśli zamierzasz korzystać z bramy aplikacji z aplikacją sieci web hostowanej w tej samej sieci wirtualnej co brama aplikacji, należy pozostawić wystarczająco dużo miejsca w innej podsieci.

```azurecli-interactive
azure network vnet subnet create \
--resource-group ContosoRG \
--name subnet01 \
--vnet-name ContosoVNET \
--address-prefix 10.0.0.0/28 
```

## <a name="create-the-application-gateway"></a>Tworzenie bramy aplikacji

Po utworzeniu sieci wirtualnej i podsieci wymagania wstępne dla bramy aplikacji są spełnione. Ponadto PFX wcześniej wyeksportowany certyfikat i hasło dla certyfikatu są wymagane dla następujący krok: adresy IP używane do wewnętrznej bazy danych są adresami IP dla serwera wewnętrznej bazy danych. Te wartości można prywatnych adresów IP w sieci wirtualnej, publiczne adresy IP lub nazwy FQDN dla serwerów zaplecza.

```azurecli-interactive
azure network application-gateway create \
--name AdatumAppGateway \
--location eastus \
--resource-group ContosoRG \
--vnet-name ContosoVNET \
--subnet-name subnet01 \
--servers 134.170.185.46,134.170.188.221,134.170.185.50 \
--capacity 2 \
--sku-tier Standard \
--routing-rule-type Basic \
--frontend-port 80 \
--http-settings-cookie-based-affinity Enabled \
--http-settings-port 80 \
--http-settings-protocol http \
--frontend-port http \
--sku-name Standard_Medium
```

> [!NOTE]
> Lista parametrów, które można podać podczas tworzenia, uruchom następujące polecenie: **brama aplikacji w sieci azure, Utwórz — Pomoc**.

W tym przykładzie tworzy bramę aplikacji w warstwie podstawowa z domyślnych ustawień odbiornika, puli zaplecza, ustawienia http zaplecza i zasady. Można zmodyfikować te ustawienia do wdrożenia po udostępnianie zakończy się pomyślnie.
Jeśli masz już aplikację sieci web zdefiniowane z puli zaplecza w poprzednich krokach utworzono raz, równoważenie obciążenia sieciowego rozpoczyna się.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak utworzyć sondy kondycji niestandardowych odwiedzając [utworzyć sondy kondycji niestandardowych](application-gateway-create-probe-portal.md)

Dowiedz się, jak skonfigurować odciążanie protokołu SSL i podejmij kosztowne odszyfrowywania SSL poza serwerów sieci web, odwiedzając [skonfigurować odciążanie protokołu SSL](application-gateway-ssl-arm.md)

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli-nodejs/scenario.png
[1]: ./media/application-gateway-create-gateway-cli-nodejs/figure1.png
[2]: ./media/application-gateway-create-gateway-cli-nodejs/figure2.png
[3]: ./media/application-gateway-create-gateway-cli-nodejs/figure3.png
