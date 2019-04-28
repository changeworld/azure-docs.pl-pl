---
title: Adresy IP w usłudze Azure Functions
description: Dowiedz się, jak znaleźć adresy IP ruchu przychodzącego i wychodzącego dla aplikacji funkcji, a co powoduje, że ich zmiany.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: glenga
ms.openlocfilehash: 83e5a15d8a7f9c01f6a180ebceb715600b8a39db
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61035865"
---
# <a name="ip-addresses-in-azure-functions"></a>Adresy IP w usłudze Azure Functions

W tym artykule opisano następujące tematy związane z adresów IP aplikacji funkcji:

* Jak znaleźć adresy IP aktualnie używany przez aplikację funkcji.
* Co powoduje, że funkcja adresów IP aplikacji mają być zmienione.
* Jak ograniczyć adresy IP, które mogą uzyskiwać dostęp do aplikacji funkcji.
* Jak uzyskać dedykowane adresy IP dla aplikacji funkcji.

Adresy IP są skojarzone z aplikacji funkcji, a nie poszczególnych funkcji. Przychodzące żądania HTTP nie można używać dla ruchu przychodzącego adresu IP do wywoływania poszczególnych funkcji; muszą oni korzystać domyślnej nazwy domeny (functionappname.azurewebsites.net) lub niestandardowej nazwy domeny.

## <a name="function-app-inbound-ip-address"></a>Aplikacja funkcji dla ruchu przychodzącego adresu IP

Każda aplikacja funkcji zawiera pojedynczy adres IP dla ruchu przychodzącego. Aby znaleźć ten adres IP:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Przejdź do aplikacji funkcji.
3. Wybierz **funkcje platformy**.
4. Wybierz **właściwości**, i adres IP dla ruchu przychodzącego, który pojawia się w obszarze **wirtualny adres IP**.

## <a name="find-outbound-ip-addresses"></a>Wychodzące adresy IP aplikacji — funkcja

Każda aplikacja funkcji ma zestaw dostępnych adresów IP ruchu wychodzącego. Wszystkie połączenia wychodzące z funkcji, np. w przypadku wewnętrznej bazy danych korzysta z jednego z dostępnych adresów IP ruchu wychodzącego jako punkt początkowy adres IP. Nie wiesz, wcześniej adres IP, które danego połączenia użyje. Z tego powodu usługa zaplecza należy otworzyć zapory do wszystkich aplikacji funkcji wychodzące adresy IP.

Aby znaleźć dostępne dla aplikacji funkcji wychodzące adresy IP:

1. Zaloguj się do [Eksplorator zasobów Azure](https://resources.azure.com).
2. Wybierz **subskrypcje > {subscription} > dostawców > Microsoft.Web > witryn**.
3. W panelu JSON można znaleźć lokacji przy użyciu `id` właściwość, która kończy nazwę aplikacji funkcji.
4. Zobacz `outboundIpAddresses` i `possibleOutboundIpAddresses`. 

Zbiór `outboundIpAddresses` jest obecnie dostępny dla aplikacji funkcji. Zbiór `possibleOutboundIpAddresses` zawiera adresy IP, które będą dostępne tylko wtedy, gdy aplikacja funkcji [można skalować do innej warstwy cenowej](#outbound-ip-address-changes).

Alternatywny sposób, aby znaleźć dostępne adresy IP ruchu wychodzącego jest przy użyciu [Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```
> [!NOTE]
> Gdy aplikacja funkcji, które jest uruchamiane na [planu zużycie](functions-scale.md#consumption-plan) jest skalowana, nowy zakres wychodzące adresy IP można przypisać. Podczas uruchamiania na planie zużycie, może być konieczne dozwolonych całego centrum danych.

## <a name="data-center-outbound-ip-addresses"></a>Wychodzące adresy IP centrum danych

Jeśli chcesz umieścić na liście dozwolonych wychodzące adresy IP używane przez Twoje aplikacje funkcji, innym rozwiązaniem jest do listy dozwolonych aplikacji funkcji centrum danych (region platformy Azure). Możesz [pobranie pliku JSON, który zawiera listę adresów IP dla wszystkich centrów danych platformy Azure](https://www.microsoft.com/en-us/download/details.aspx?id=56519). Następnie znajdź fragment kodu JSON, który ma zastosowanie do regionu, który aplikacja funkcji zostanie uruchomiona w.

Na przykład jest to, jak może wyglądać fragment kodu JSON Europa Zachodnia:

```
{
  "name": "AzureCloud.westeurope",
  "id": "AzureCloud.westeurope",
  "properties": {
    "changeNumber": 9,
    "region": "westeurope",
    "platform": "Azure",
    "systemService": "",
    "addressPrefixes": [
      "13.69.0.0/17",
      "13.73.128.0/18",
      ... Some IP addresses not shown here
     "213.199.180.192/27",
     "213.199.183.0/24"
    ]
  }
}
```

 W przypadku informacji o gdy ten plik zostanie zaktualizowany, i gdy adresy IP ulegają, rozwiń **szczegóły** części [strony Centrum pobierania](https://www.microsoft.com/en-us/download/details.aspx?id=56519).

## <a name="inbound-ip-address-changes"></a>Przychodzące zmiany adresu IP

Adres IP dla ruchu przychodzącego **może** zmienić, gdy użytkownik:

- Usuń aplikację funkcji i utworzyć ją ponownie w innej grupie zasobów.
- Usuń ostatni aplikacji funkcji w grupie i regionie kombinacji zasobów, a następnie utworzyć ją ponownie.
- Usuwanie powiązania SSL, takich jak podczas [odnowienia certyfikatu](../app-service/app-service-web-tutorial-custom-ssl.md#renew-certificates)).

Gdy aplikacja funkcji zostanie uruchomiona [planu zużycie](functions-scale.md#consumption-plan), adres IP dla ruchu przychodzącego również mogą ulec zmianie, gdy nie jeszcze podjęte akcje, takie jak te wyświetlane.

## <a name="outbound-ip-address-changes"></a>Wychodzące zmiany adresu IP

Zestaw dostępny adres IP ruchu wychodzącego adresów dla aplikacji funkcji mogą ulec zmianie po użytkownik:

* Podejmować żadnych działań, które można zmienić adres IP dla ruchu przychodzącego.
* Zmień plan usługi App Service w warstwie cenowej. Lista wszystkich możliwych wychodzące adresy IP Twoja aplikacja może używać dla wszystkich warstw cenowych, znajduje się w `possibleOutboundIPAddresses` właściwości. Zobacz [znaleźć adresy IP ruchu wychodzącego](#find-outbound-ip-addresses).

Gdy aplikacja funkcji zostanie uruchomiona [planu zużycie](functions-scale.md#consumption-plan), wychodzący adres IP również mogą ulec zmianie, gdy nie jeszcze podjęte akcje, takie jak te wyświetlane.

Aby wymusić celowo zmiana adresu IP ruchu wychodzącego:

1. Skalowanie planu usługi App Service w górę lub w dół między warstwami cenowymi v2 standardowa i Premium.
2. Poczekaj 10 minut.
3. Skalowanie do, w którym uruchomiono.

## <a name="ip-address-restrictions"></a>Ograniczenia adresów IP

Można skonfigurować listę adresów IP, które chcesz zezwolić lub odmówić dostępu do aplikacji funkcji. Aby uzyskać więcej informacji, zobacz [usługi Azure App Service statyczne ograniczenia adresów IP](../app-service/app-service-ip-restrictions.md).

## <a name="dedicated-ip-addresses"></a>Dedykowane adresy IP

Jeśli potrzebujesz statycznego, dedykowane adresy IP, firma Microsoft zaleca [środowisk usługi App Service](../app-service/environment/intro.md) ( [izolowane warstwy](https://azure.microsoft.com/pricing/details/app-service/) planów usługi App Service). Aby uzyskać więcej informacji, zobacz [adresy IP środowiska usługi App](../app-service/environment/network-info.md#ase-ip-addresses) i [jak kontrolować ruch przychodzący do środowiska usługi App Service](../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md).

Aby dowiedzieć się, jeśli aplikacja funkcji zostanie uruchomiona w środowisku usługi App Service:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Przejdź do aplikacji funkcji.
3. Wybierz **Przegląd** kartę.
4. Warstwę planu usługi App Service jest wyświetlane w obszarze **usługi App Service plan/warstwa cenowa**. Warstwa cenowa usługi App Service Environment jest **izolowany**.
 
Alternatywnie, można użyć [Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query sku --output tsv
```

Usługa App Service Environment `sku` jest `Isolated`.

## <a name="next-steps"></a>Kolejne kroki

Częstą przyczyną zmiany adresu IP jest zmiana skali aplikacji funkcji. [Dowiedz się więcej na temat skalowania aplikacji funkcji](functions-scale.md).
