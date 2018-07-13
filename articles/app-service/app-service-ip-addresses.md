---
title: Adresy IP w usłudze Azure App Service | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób przychodzących i wychodzących adresów IP adresy są używane w usłudze App Service i jak znaleźć informacje na ich temat dla aplikacji.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: cephalin
ms.openlocfilehash: 752f9d82afafaf7324c0c63c0d7377b952fe0716
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39003150"
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Przychodzące i wychodzące adresy IP w usłudze Azure App Service

[Usługa Azure App Service](app-service-web-overview.md) jest usługą wielodostępną, z wyjątkiem [środowisk usługi App Service](environment/intro.md). Aplikacje, które nie znajdują się w środowisku usługi App Service (nie w [izolowane warstwy](https://azure.microsoft.com/pricing/details/app-service/)) udziału infrastruktury sieci z innymi aplikacjami. Co w efekcie adresy IP ruchu przychodzącego i wychodzącego aplikacji mogą być różne, a także zmienić w niektórych sytuacjach. 

[Środowiska usługi App Service](environment/intro.md) Użyj dedykowanej sieci infrastruktury, dzięki czemu aplikacje działające w środowisku usługi App Service statyczne, dedykowany adres IP adresów zarówno dla połączeń przychodzących i wychodzących.

## <a name="when-inbound-ip-changes"></a>Podczas zmiany adresu IP dla ruchu przychodzącego

Bez względu na liczbę wystąpień skalowanych w poziomie każda aplikacja ma pojedynczy adres IP dla ruchu przychodzącego. Adres IP dla ruchu przychodzącego mogą ulec zmianie, kiedy wykonujesz jedno z następujących czynności:

- Usuń aplikację i utworzyć ją ponownie w innej grupie zasobów.
- Usunięcie ostatniego aplikacji w grupie zasobów _i_ kombinacji region i utwórz go ponownie.
- Usuń istniejące powiązanie SSL, takich jak podczas odnawiania certyfikatów (zobacz [odnawiania certyfikatów](app-service-web-tutorial-custom-ssl.md#renew-certificates)).

## <a name="get-static-inbound-ip"></a>Uzyskaj statyczny adres IP dla ruchu przychodzącego

Czasami możesz zechcieć dedykowanych statycznego adresu IP dla swojej aplikacji. Aby uzyskać statyczny adres IP dla ruchu przychodzącego, należy skonfigurować [powiązania SSL opartego na protokole IP](app-service-web-tutorial-custom-ssl.md#bind-your-ssl-certificate). Jeśli funkcje protokołu SSL do zabezpieczania aplikacji nie jest potrzebna, można nawet przekazać certyfikat z podpisem własnym dla tego powiązania. Powiązania SSL opartego na protokole IP certyfikat jest powiązany z adresu IP, tak przepisy usługi App Service, statycznych adresów IP do Praca. 

## <a name="when-outbound-ips-change"></a>Podczas zmiany wychodzące adresy IP

Bez względu na liczbę wystąpień skalowanych w poziomie każda aplikacja ma określona liczba wychodzące adresy IP w danym momencie. Wszystkie połączenia wychodzące z aplikacji usługi app Service, np. w przypadku wewnętrznej bazy danych korzysta z jednego z wychodzące adresy IP jako adres IP źródła. Nie wiesz, wcześniej adres IP, które wystąpienie danej aplikacji użyje do wykonywania połączenia wychodzące, usługi zaplecza, należy otworzyć zapory na wszystkie wychodzące adresy IP Twojej aplikacji.

Zbiór wychodzących adresów IP adresy do wprowadzania zmian w aplikacji, skalowanie aplikacji od niższych warstwach (**podstawowe**, **standardowa**, i **Premium**) i  **Warstwa Premium V2** warstwy.

Możesz znaleźć zbiór wszystkich możliwych wychodzące adresy IP można używać aplikacji, niezależnie od tego, w warstwach cenowych, wyszukując `possibleOutboundIPAddresses` właściwości. Zobacz [znaleźć adresy IP ruchu wychodzącego](#find-outbound-ips).

## <a name="find-outbound-ips"></a>Znajdź wychodzące adresy IP

Aby znaleźć wychodzące adresy IP używane przez aplikację w witrynie Azure portal, kliknij **właściwości** w nawigacji po lewej stronie Twojej aplikacji. 

Te same informacje można znaleźć, uruchamiając następujące polecenie w [Cloud Shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
```

Aby znaleźć wszystkie możliwe wychodzące adresy IP dla swojej aplikacji, niezależnie od tego, ceny warstw, uruchom następujące polecenie [Cloud Shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak ograniczyć ruch przychodzący, źródłowe adresy IP.

> [!div class="nextstepaction"]
> [Ograniczenia statycznych adresów IP](app-service-ip-restrictions.md)
