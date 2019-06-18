---
title: Tworzenie usługi Azure Application Gateway — klasycznego wiersza polecenia platformy Azure
description: Dowiedz się, jak utworzyć bramę aplikacji przy użyciu klasycznego wiersza polecenia platformy Azure w usłudze Resource Manager
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 4/15/2019
ms.author: victorh
ms.openlocfilehash: 7107f45253c4f13b3378489726bf5034e104fa30
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62095986"
---
# <a name="create-an-application-gateway-by-using-the-azure-cli"></a>Tworzenie bramy aplikacji przy użyciu wiersza polecenia platformy Azure

Usługa Azure Application Gateway to moduł równoważenia obciążenia warstwy 7. Udostępnia tryb failover, oparty na wydajności routing żądań HTTP między różnymi serwerami — w chmurze i lokalnymi. Usługa Application gateway ma następujące funkcje dostarczania aplikacji: Równoważenie obciążenia HTTP, koligację sesji na podstawie plików cookie i odciążanie protokołu Secure Sockets Layer (SSL), niestandardowe sondy kondycji i obsługę wielu witryn.

## <a name="prerequisite-install-the-azure-cli"></a>Wymagania wstępne: Zainstaluj interfejs wiersza polecenia platformy Azure

Aby wykonać kroki opisane w tym artykule, musisz [zainstalować interfejs wiersza polecenia platformy Azure](../xplat-cli-install.md) i trzeba [Zaloguj się na platformie Azure](/cli/azure/authenticate-azure-cli). 

> [!NOTE]
> Jeśli nie masz konta platformy Azure, możesz je utworzyć. Zarejestruj się, aby skorzystać z [bezpłatnego demo](../active-directory/fundamentals/sign-up-organization.md).

## <a name="scenario"></a>Scenariusz

W tym scenariuszu dowiesz się, jak utworzyć bramę aplikacji przy użyciu witryny Azure portal.

W tym scenariuszu wykonują następujące czynności:

* Tworzenie bramy aplikacji średnie z dwoma wystąpieniami.
* Tworzenie sieci wirtualnej o nazwie ContosoVNET przy użyciu zarezerwowanego bloku CIDR 10.0.0.0/16.
* Utwórz podsieć o nazwie subnet01, która używa 10.0.0.0/28 jako bloku CIDR.

> [!NOTE]
> Dodatkowa konfiguracja usługi application gateway, w tym kondycji niestandardowe sondy, adresy w puli zaplecza oraz dodatkowe reguły są skonfigurowane, po skonfigurowaniu bramy aplikacji, a nie podczas początkowego wdrożenia.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Usługa Azure Application Gateway wymaga własnej podsieci. Podczas tworzenia sieci wirtualnej, upewnij się, pozostanie wystarczająco dużo przestrzeni adresowej do wielu podsieci. Po wdrożeniu bramy aplikacji z podsiecią bramy aplikacji z jedynymi dodatkowymi są w stanie mają zostać dodane do tej podsieci.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Otwórz **wiersza polecenia platformy Azure Microsoft**i zaloguj się.

```azurecli-interactive
az login
```

Po wpisaniu poprzedniego przykładu, znajduje się kod. Przejdź do https://aka.ms/devicelogin w przeglądarce, aby kontynuować proces logowania.

![Logowanie do urządzenia przedstawiający cmd][1]

W przeglądarce wprowadź otrzymany kod. Nastąpi przekierowanie do strony logowania.

![Wprowadź kod w przeglądarce][2]

Po wprowadzeniu kodu użytkownik jest zalogowany, zamknij przeglądarkę, aby kontynuować z tego scenariusza.

![Zalogowano pomyślnie][3]

## <a name="switch-to-resource-manager-mode"></a>Przełącz do trybu usługi Resource Manager

```azurecli-interactive
azure config mode arm
```

## <a name="create-the-resource-group"></a>Tworzenie grupy zasobów

Przed utworzeniem bramy aplikacji, grupy zasobów zostanie utworzona brama aplikacji. Poniżej przedstawiono polecenia.

```azurecli-interactive
azure group create \
--name ContosoRG \
--location eastus
```

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Po utworzeniu grupy zasobów, sieć wirtualna jest tworzona w usłudze application gateway.  W poniższym przykładzie przestrzeni adresowej była jako 10.0.0.0/16 zgodnie z definicją w poprzednim scenariuszu o.

```azurecli-interactive
azure network vnet create \
--name ContosoVNET \
--address-prefixes 10.0.0.0/16 \
--resource-group ContosoRG \
--location eastus
```

## <a name="create-a-subnet"></a>Tworzenie podsieci

Po utworzeniu sieci wirtualnej, podsieć zostanie dodany w usłudze application gateway.  Jeśli planujesz użyć bramy aplikacji z aplikacją sieci web hostowanych w tej samej sieci wirtualnej co brama aplikacji, należy pozostawić wystarczającą ilość miejsca na inną podsieć.

```azurecli-interactive
azure network vnet subnet create \
--resource-group ContosoRG \
--name subnet01 \
--vnet-name ContosoVNET \
--address-prefix 10.0.0.0/28 
```

## <a name="create-the-application-gateway"></a>Tworzenie bramy aplikacji

Po utworzeniu sieci wirtualnej i podsieci spełniono wymagania wstępne w usłudze application gateway. Ponadto certyfikat wcześniej wyeksportowanego pliku PFX i hasło dla certyfikatu są wymagane dla następujący krok: Adresy IP używane dla zaplecza są adresy IP dla serwera wewnętrznej bazy danych. Te wartości mogą być prywatne adresy IP w sieci wirtualnej, publiczne adresy IP lub w pełni kwalifikowanych nazw domen dla serwerów zaplecza.

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
> Aby uzyskać listę parametrów, które można podać podczas tworzenia, uruchom następujące polecenie: **Tworzenie bramy aplikacji sieci platformy azure — Pomoc**.

Ten przykład tworzy bramę aplikacji w warstwie podstawowa przy użyciu ustawień domyślnych dla odbiornika, puli zaplecza, ustawienia http zaplecza i reguł. Można zmodyfikować te ustawienia do potrzeb danego wdrożenia po pomyślnej aprowizacji.
Jeśli masz już aplikację sieci web zdefiniowany z pulą zaplecza w poprzednich krokach, po utworzeniu równoważenia obciążenia rozpoczyna się.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak tworzyć niestandardowe sondy kondycji, odwiedzając [Utwórz sondę kondycji niestandardowe](application-gateway-create-probe-portal.md)

Dowiedz się, jak skonfigurować odciążanie protokołu SSL i kosztownych odszyfrowywania protokołu SSL, wyłącz serwerów sieci web, odwiedzając [skonfigurować odciążanie protokołu SSL](application-gateway-ssl-arm.md)

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli-nodejs/scenario.png
[1]: ./media/application-gateway-create-gateway-cli-nodejs/figure1.png
[2]: ./media/application-gateway-create-gateway-cli-nodejs/figure2.png
[3]: ./media/application-gateway-create-gateway-cli-nodejs/figure3.png
