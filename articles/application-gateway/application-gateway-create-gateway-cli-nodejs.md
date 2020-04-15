---
title: Tworzenie bramy aplikacji platformy Azure — klasyczna platforma 11-osobowa platformy Azure
description: Dowiedz się, jak utworzyć bramę aplikacji przy użyciu klasycznego interfejsu wiersza polecenia platformy Azure w Menedżerze zasobów
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 4/15/2019
ms.author: victorh
ms.openlocfilehash: ed7bbbf55956d655115b94c8ac46432b3e36c49b
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312643"
---
# <a name="create-an-application-gateway-by-using-the-azure-cli"></a>Tworzenie bramy aplikacji przy użyciu interfejsu wiersza polecenia platformy Azure

Usługa Azure Application Gateway to moduł równoważenia obciążenia warstwy 7. Udostępnia tryb failover, oparty na wydajności routing żądań HTTP między różnymi serwerami — w chmurze i lokalnymi. Brama aplikacji ma następujące funkcje dostarczania aplikacji: równoważenie obciążenia HTTP, koligacja sesji oparta na plikach cookie i zabezpieczenia warstwy transportu (TLS), wcześniej znane jako secure sockets layer (SSL), odciążanie, niestandardowe sondy kondycji i obsługa wielu lokacji.

## <a name="prerequisite-install-the-azure-cli"></a>Warunek wstępny: instalowanie interfejsu wiersza polecenia platformy Azure

Aby wykonać kroki opisane w tym artykule, należy [zainstalować interfejsu wiersza polecenia platformy Azure](../xplat-cli-install.md) i zalogować się na [platformie Azure](/cli/azure/authenticate-azure-cli). 

> [!NOTE]
> Jeśli nie masz konta platformy Azure, potrzebujesz go. Zarejestruj się, aby skorzystać z [bezpłatnego demo](../active-directory/fundamentals/sign-up-organization.md).

## <a name="scenario"></a>Scenariusz

W tym scenariuszu dowiesz się, jak utworzyć bramę aplikacji przy użyciu witryny Azure portal.

W tym scenariuszu:

* Utwórz średnią bramę aplikacji z dwoma wystąpieniami.
* Utwórz sieć wirtualną o nazwie ContosoVNET z zarezerwowanym blokiem CIDR 10.0.0.0/16.
* Utwórz podsieć o nazwie podsieć, która używa 10.0.0.0/28 jako blok CIDR.

> [!NOTE]
> Dodatkowa konfiguracja bramy aplikacji, w tym niestandardowe sondy kondycji, adresy puli wewnętrznej bazy danych i dodatkowe reguły są konfigurowane po skonfigurowaniu bramy aplikacji, a nie podczas wdrażania początkowego.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Usługa Azure Application Gateway wymaga własnej podsieci. Podczas tworzenia sieci wirtualnej upewnij się, że pozostawiasz wystarczającą ilość miejsca na adres, aby mieć wiele podsieci. Po wdrożeniu bramy aplikacji do podsieci można dodać tylko dodatkowe bramy aplikacji do podsieci.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Otwórz **wiersz polecenia platformy Microsoft Azure**i zaloguj się.

```azurecli-interactive
az login
```

Po wpisaniu poprzedniego przykładu jest dostarczany kod. Przejdź https://aka.ms/devicelogin do przeglądarki, aby kontynuować proces logowania.

![cmd pokazujący login urządzenia][1]

W przeglądarce wprowadź otrzymany kod. Zostaniesz przekierowany do strony logowania.

![przeglądarka, aby wprowadzić kod][2]

Po wprowadzeniu kodu jesteś zalogowany, zamknij przeglądarkę, aby kontynuować scenariusz.

![pomyślnie zalogowany][3]

## <a name="switch-to-resource-manager-mode"></a>Przełączanie do trybu Menedżera zasobów

```azurecli-interactive
azure config mode arm
```

## <a name="create-the-resource-group"></a>Tworzenie grupy zasobów

Przed utworzeniem bramy aplikacji tworzona jest grupa zasobów zawierająca bramę aplikacji. Poniżej przedstawiono polecenia.

```azurecli-interactive
azure group create \
--name ContosoRG \
--location eastus
```

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Po utworzeniu grupy zasobów dla bramy aplikacji jest tworzona sieć wirtualna.  W poniższym przykładzie przestrzeń adresowa była jako 10.0.0.0/16 zgodnie z definicją w poprzednich notatek scenariusza.

```azurecli-interactive
azure network vnet create \
--name ContosoVNET \
--address-prefixes 10.0.0.0/16 \
--resource-group ContosoRG \
--location eastus
```

## <a name="create-a-subnet"></a>Tworzenie podsieci

Po utworzeniu sieci wirtualnej dla bramy aplikacji jest dodawana podsieć.  Jeśli planujesz używać bramy aplikacji z aplikacją sieci web hostowanych w tej samej sieci wirtualnej co brama aplikacji, pamiętaj, aby pozostawić wystarczająco dużo miejsca dla innej podsieci.

```azurecli-interactive
azure network vnet subnet create \
--resource-group ContosoRG \
--name subnet01 \
--vnet-name ContosoVNET \
--address-prefix 10.0.0.0/28 
```

## <a name="create-the-application-gateway"></a>Tworzenie bramy aplikacji

Po utworzeniu sieci wirtualnej i podsieci zostaną ukończone wymagania wstępne dla bramy aplikacji. Ponadto wcześniej wyeksportowany certyfikat .pfx i hasło do certyfikatu są wymagane w następującym kroku: Adresy IP używane dla wewnętrznej bazy danych są adresami IP serwera wewnętrznej bazy danych. Wartości te mogą być prywatnymi adresami IP w sieci wirtualnej, publicznymi adresami IP lub w pełni kwalifikowanymi nazwami domen dla serwerów zaplecza.

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
> Aby wyświetlić listę parametrów, które mogą być dostarczone podczas tworzenia, uruchom następujące polecenie: **Azure network application-gateway create --help**.

W tym przykładzie tworzy podstawową bramę aplikacji z ustawieniami domyślnymi dla odbiornika, puli wewnętrznej bazy danych, ustawień http wewnętrznej bazy danych i reguł. Można zmodyfikować te ustawienia, aby dopasować je do wdrożenia po pomyślnym zakończeniu inicjowania obsługi administracyjnej.
Jeśli aplikacja sieci web jest już zdefiniowana z pulą wewnętrznej bazy danych w poprzednich krokach, po utworzeniu rozpoczyna się równoważenie obciążenia.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak utworzyć niestandardowe sondy kondycji, odwiedzając pozycję [Utwórz niestandardową sondę kondycji](application-gateway-create-probe-portal.md)

Dowiedz się, jak skonfigurować odciążanie TLS i odszyfrować kosztowne odszyfrowywanie protokołu TLS z serwerów sieci web, odwiedzając pozycję [Konfiguruj odciążanie TLS](application-gateway-ssl-arm.md)

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli-nodejs/scenario.png
[1]: ./media/application-gateway-create-gateway-cli-nodejs/figure1.png
[2]: ./media/application-gateway-create-gateway-cli-nodejs/figure2.png
[3]: ./media/application-gateway-create-gateway-cli-nodejs/figure3.png
