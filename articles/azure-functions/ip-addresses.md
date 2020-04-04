---
title: Adresy IP w usłudze Azure Functions
description: Dowiedz się, jak znaleźć przychodzące i wychodzące adresy IP dla aplikacji funkcyjnych oraz co powoduje ich zmianę.
ms.topic: conceptual
ms.date: 12/03/2018
ms.openlocfilehash: bfd2d573e0a1c78d0ef4c68be224f92e8f689f62
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656782"
---
# <a name="ip-addresses-in-azure-functions"></a>Adresy IP w usłudze Azure Functions

W tym artykule opisano następujące tematy związane z adresami IP aplikacji funkcyjnych:

* Jak znaleźć adresy IP aktualnie używane przez aplikację funkcji.
* Co powoduje zmianę adresów IP aplikacji funkcji.
* Jak ograniczyć adresy IP, które mogą uzyskać dostęp do aplikacji funkcji.
* Jak uzyskać dedykowane adresy IP dla aplikacji funkcji.

Adresy IP są skojarzone z aplikacjami funkcyjnymi, a nie z poszczególnymi funkcjami. Przychodzące żądania HTTP nie mogą używać przychodzącego adresu IP do wywoływania poszczególnych funkcji; muszą używać domyślnej nazwy domeny (functionappname.azurewebsites.net) lub niestandardowej nazwy domeny.

## <a name="function-app-inbound-ip-address"></a>Przychodzący adres IP aplikacji funkcji

Każda aplikacja funkcji ma jeden przychodzący adres IP. Aby znaleźć ten adres IP:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Przejdź do aplikacji funkcji.
3. Wybierz pozycję **Funkcje platformy**.
4. Wybierz **polecenie Właściwości**, a przychodzący adres IP pojawi się w obszarze **Wirtualny adres IP**.

## <a name="function-app-outbound-ip-addresses"></a><a name="find-outbound-ip-addresses"></a>Wychodzące adresy IP aplikacji funkcji

Każda aplikacja funkcji ma zestaw dostępnych wychodzących adresów IP. Każde połączenie wychodzące z funkcji, takiej jak baza danych zaplecza, używa jednego z dostępnych wychodzących adresów IP jako początkowego adresu IP. Nie możesz wcześniej wiedzieć, jaki adres IP będzie używany przez dane połączenie. Z tego powodu usługa zaplecza musi otworzyć zaporę na wszystkie wychodzące adresy IP aplikacji funkcji.

Aby znaleźć wychodzące adresy IP dostępne dla aplikacji funkcyjnej:

1. Zaloguj się do [Eksploratora zasobów platformy Azure](https://resources.azure.com).
2. Wybierz **subskrypcje > {subskrypcja} > dostawców > witrynach microsoft.Web >**.
3. W panelu JSON znajdź witrynę `id` z właściwością, która kończy się nazwą aplikacji funkcji.
4. Zobacz `outboundIpAddresses` `possibleOutboundIpAddresses`i . 

Zestaw `outboundIpAddresses` jest obecnie dostępny dla aplikacji funkcji. Zestaw `possibleOutboundIpAddresses` zawiera adresy IP, które będą dostępne tylko wtedy, gdy aplikacja funkcji [skaluje się do innych warstw cenowych](#outbound-ip-address-changes).

Alternatywnym sposobem znajdowania dostępnych wychodzących adresów IP jest użycie [powłoki Cloud Shell:](../cloud-shell/quickstart.md)

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```
> [!NOTE]
> Gdy aplikacja funkcji, która działa w [planie zużycia](functions-scale.md#consumption-plan) jest skalowany, nowy zakres wychodzących adresów IP mogą być przypisane. Podczas uruchamiania w planie zużycia może być konieczne wybranie do białej listy całego centrum danych.

## <a name="data-center-outbound-ip-addresses"></a>Wychodzące adresy IP centrum danych

Jeśli chcesz umieszczać na białej liście wychodzące adresy IP używane przez aplikacje funkcji, inną opcją jest umieszczanie na białej liście centrum danych aplikacji funkcji (region platformy Azure). Można [pobrać plik JSON zawierający listę adresów IP dla wszystkich centrów danych platformy Azure.](https://www.microsoft.com/en-us/download/details.aspx?id=56519) Następnie znajdź fragment JSON, który ma zastosowanie do regionu, w który działa aplikacja funkcji.

Na przykład jest to, co Zachodniej Europy JSON fragment może wyglądać:

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

 Aby uzyskać informacje o tym, kiedy ten plik jest aktualizowany i gdy zmieniają się adresy IP, rozwiń sekcję **Szczegóły** na [stronie Centrum pobierania](https://www.microsoft.com/en-us/download/details.aspx?id=56519).

## <a name="inbound-ip-address-changes"></a><a name="inbound-ip-address-changes"></a>Zmiany przychodzącego adresu IP

Przychodzący adres IP **może** ulec zmianie po:

- Usuń aplikację funkcji i ponownie ją utworzyć w innej grupie zasobów.
- Usuń ostatnią aplikację funkcyjną w kombinacji grupy zasobów i regionu i ponownie ją utwórz.
- Usuń powiązanie TLS, na przykład podczas [odnawiania certyfikatu](../app-service/configure-ssl-certificate.md#renew-certificate).

Gdy aplikacja funkcji działa w [planie zużycia,](functions-scale.md#consumption-plan)przychodzący adres IP może również ulec zmianie, nawet jeśli nie podjęto żadnych działań, takich jak [wymienione powyżej.](#inbound-ip-address-changes)

## <a name="outbound-ip-address-changes"></a>Zmiany wychodzącego adresu IP

Zestaw dostępnych wychodzących adresów IP dla aplikacji funkcyjnej może ulec zmianie po:

* Podejmij wszelkie działania, które mogą zmienić przychodzący adres IP.
* Zmień warstwę cenową planu usługi App Service. Lista wszystkich możliwych wychodzących adresów IP, których aplikacja może używać, `possibleOutboundIPAddresses` dla wszystkich warstw cenowych, znajduje się we właściwości. Zobacz [Znajdowanie wychodzących usług IP](#find-outbound-ip-addresses).

Gdy aplikacja funkcji działa w [planie zużycia,](functions-scale.md#consumption-plan)wychodzący adres IP może również ulec zmianie, nawet jeśli nie podjęto żadnych działań, takich jak [wymienione powyżej.](#inbound-ip-address-changes)

Aby celowo wymusić zmianę wychodzącego adresu IP:

1. Skaluj plan usługi app service w górę lub w dół między warstwami cenowymi Standard i Premium w wersji 2.
2. Odczekaj 10 minut.
3. Skalowanie z powrotem do miejsca, w którym rozpoczęto.

## <a name="ip-address-restrictions"></a>ograniczenia adresów IP

Można skonfigurować listę adresów IP, które mają zezwalać lub odmawiać dostępu do aplikacji funkcji. Aby uzyskać więcej informacji, zobacz [Statyczne ograniczenia adresów IP usługi Azure App Service](../app-service/app-service-ip-restrictions.md).

## <a name="dedicated-ip-addresses"></a>Dedykowane adresy IP

Jeśli potrzebujesz statycznych, dedykowanych adresów IP, [zalecamy środowiska usługi App Service](../app-service/environment/intro.md) [(warstwę Izolowane](https://azure.microsoft.com/pricing/details/app-service/) planów usługi App Service). Aby uzyskać więcej informacji, zobacz [Adresy IP środowiska usługi app service](../app-service/environment/network-info.md#ase-ip-addresses) i jak kontrolować ruch [przychodzący do środowiska usługi app service](../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md).

Aby dowiedzieć się, czy aplikacja funkcji działa w środowisku usługi app service:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Przejdź do aplikacji funkcji.
3. Wybierz kartę **Przegląd**.
4. Warstwa planu usługi app service jest wyświetlana w **warstwie planu/wyceny usługi app service**. Warstwa cenowa Środowisko usługi aplikacji jest **izolowana**.
 
Alternatywnie można użyć [Cloud Shell:](../cloud-shell/quickstart.md)

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query sku --output tsv
```

Środowisko `sku` usługi aplikacji `Isolated`jest .

## <a name="next-steps"></a>Następne kroki

Częstą przyczyną zmian ip jest zmiana skali aplikacji funkcji. [Dowiedz się więcej o skalowaniu aplikacji funkcji](functions-scale.md).
