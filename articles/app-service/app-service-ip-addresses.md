---
title: Adresy IP w usłudze Azure App Service | Dokumentacja firmy Microsoft
description: Opisuje sposób przychodzący i wychodzący IP adresy są używane w aplikacji usługi oraz sposób informacje dotyczące ich aplikacji.
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
ms.openlocfilehash: 906a5d511615c57b6ff807ac240a838c63917e66
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Przychodzący i wychodzący adresów IP w usłudze Azure App Service

[Usługa aplikacji Azure](app-service-web-overview.md) jest usługą wielodostępne, z wyjątkiem [środowiska usługi App Service](environment/intro.md). Aplikacje, które nie znajdują się w środowisku usługi aplikacji (nie w [izolowanego warstwy](https://azure.microsoft.com/pricing/details/app-service/)) infrastruktury sieciowej udziału w innych aplikacjach. W związku z tym adresy IP dla ruchu przychodzącego i wychodzącego aplikacji mogą być różne, a nawet można zmienić w niektórych sytuacjach. 

[Środowiska usługi App Service](environment/intro.md) Użyj dedykowanej sieci infrastruktury, dzięki czemu aplikacje działające w środowisku usługi aplikacji uzyskać statycznych, dedykowany adres IP dotyczy zarówno dla połączeń przychodzących i wychodzących.

## <a name="when-inbound-ip-changes"></a>Podczas zmiany adresu IP dla ruchu przychodzącego

Niezależnie od liczby wystąpień skalowalnych w poziomie każda aplikacja ma jeden adres IP dla ruchu przychodzącego. Adres IP dla ruchu przychodzącego mogą ulec zmianie podczas wykonywania jednej z następujących czynności:

- Usuń aplikację i utwórz ją ponownie w innej grupie zasobów.
- Usunięcie ostatniego aplikacji w grupie zasobów _i_ kombinacja regionu i utwórz ją ponownie.
- Usuń istniejące powiązanie SSL, takich jak podczas odnawiania certyfikatów (zobacz [odnawiania certyfikatów](app-service-web-tutorial-custom-ssl.md#renew-certificates)).

## <a name="get-static-inbound-ip"></a>Pobierz statycznego adresu IP dla ruchu przychodzącego

Czasami może być dedykowany statycznego adresu IP dla aplikacji. Aby uzyskać statyczny adres IP dla ruchu przychodzącego, należy skonfigurować [powiązania SSL opartego na protokole IP](app-service-web-tutorial-custom-ssl.md#bind-your-ssl-certificate). Jeśli nie potrzebujesz faktycznie funkcjonalność protokołu SSL do zabezpieczania aplikacji, nawet można przekazać certyfikatu z podpisem własnym dla tego powiązania. Powiązania SSL opartego na protokole IP, certyfikat jest powiązany z adresu IP, tak przepisów dotyczących usług aplikacji statycznych adresów IP, aby była w stanie. 

## <a name="when-outbound-ips-change"></a>Podczas zmiany wychodzących adresów IP

Niezależnie od liczby wystąpień skalowalnych w poziomie każda aplikacja ma jest określona liczba wychodzących adresów IP w danym momencie. Dowolnego połączenia wychodzące z aplikacji usługi App Service, np. wewnętrzna baza danych korzysta z jednego z wychodzących adresów IP jako punkt początkowy adres IP. Nie wiadomo, wcześniej który adres IP wystąpienia danej aplikacji będzie używany do połączenia wychodzące, powiększając usługi zaplecza, należy otworzyć zapory do wszystkich wychodzących adresów IP aplikacji.

Zbiór Wychodzącego adresów wprowadzanie zmian aplikacji podczas skalowania aplikacji między warstwami niższe (**podstawowe**, **standardowe**, i **Premium**) i  **Premium V2** warstwy.

Można znaleźć zestaw wszystkich możliwych wychodzących adresów IP można używać aplikacji, niezależnie od ceny warstw, szukając `possibleOutboundIPAddresses` właściwości. Zobacz [znaleźć wychodzących adresów IP](#find-outbound-ips).

## <a name="find-outbound-ips"></a>Znajdź wychodzących adresów IP

Aby znaleźć wychodzących adresów IP, obecnie używana przez aplikację w portalu Azure, kliknij przycisk **właściwości** w nawigacji po lewej stronie aplikacji. 

Te same informacje można znaleźć, uruchamiając następujące polecenie [powłoki chmury](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
```

Aby znaleźć wszystkie możliwe wychodzących adresów IP dla aplikacji, niezależnie od ceny warstw, uruchom następujące polecenie [powłoki chmury](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak ograniczyć ruch przychodzący źródłowych adresów IP.

> [!div class="nextstepaction"]
> [Ograniczenia statycznych adresów IP](app-service-ip-addresses.md)
