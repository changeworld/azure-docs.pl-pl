---
title: Adresy IP usługi Usługi Azure API Management | Dokumenty firmy Microsoft
description: Dowiedz się, jak pobrać adresy IP usługi Azure API Management i kiedy się zmieniają.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/26/2019
ms.author: apimpm
ms.openlocfilehash: 45501fee9ae6ff47643a1ed197a07c4ba598e981
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80047746"
---
# <a name="ip-addresses-of-azure-api-management"></a>Adresy IP usługi Azure API Management

W tym artykule opisano, jak pobrać adresy IP usługi Azure API Management. Adresy IP mogą być publiczne lub prywatne, jeśli usługa znajduje się w sieci wirtualnej.

Adresy IP umożliwiają tworzenie reguł zapory, filtrowanie ruchu przychodzącego do usług wewnętrznej bazy danych lub ograniczanie ruchu wychodzącego.

## <a name="ip-addresses-of-api-management-service"></a>Adresy IP usługi API Management

Każde wystąpienie usługi zarządzania interfejsami API w warstwie Deweloper, Basic, Standard lub Premium ma publiczne adresy IP, które są wyłączne tylko dla tego wystąpienia usługi (nie są one współużytkowane z innymi zasobami). 

Adresy IP można pobrać z pulpitu nawigacyjnego przeglądu zasobu w witrynie Azure portal.

![Adres IP zarządzania interfejsami API](media/api-management-howto-ip-addresses/public-ip.png)

Można również pobrać je programowo z następujących wywołań interfejsu API:

```
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>
```

Publiczne adresy IP będą częścią odpowiedzi:

```json
{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.77.143.53"
    ],
    ...
  }
  ...
}
```

W [przypadku wdrożeń wieloregionalnych](api-management-howto-deploy-multi-region.md)każde wdrożenie regionalne ma jeden publiczny adres IP.

## <a name="ip-addresses-of-api-management-service-in-vnet"></a>Adresy IP usługi API Management w sieci wirtualnej

Jeśli usługa api Management znajduje się w sieci wirtualnej, będzie miała dwa typy adresów IP — publiczny i prywatny.

Publiczne adresy IP są używane `3443` do komunikacji wewnętrznej na porcie — do zarządzania konfiguracją (na przykład za pośrednictwem usługi Azure Resource Manager). W konfiguracji zewnętrznej sieci wirtualnej są one również używane do ruchu interfejsu API środowiska wykonawczego. Gdy żądanie jest wysyłane z usługi API Management do publicznego zaplecza (internet-facing), publiczny adres IP będzie widoczny jako początek żądania.

Prywatne wirtualne adresy IP (VIP), dostępne **tylko** w [wewnętrznym trybie sieci wirtualnej,](api-management-using-with-internal-vnet.md)są używane do łączenia się z sieci z punktami końcowymi usługi API Management — bramami, portalem dewelopera i płaszczyzną zarządzania w celu bezpośredniego dostępu do interfejsu API. Można ich używać do konfigurowania rekordów DNS w sieci.

Zobaczysz adresy obu typów w witrynie Azure portal i w odpowiedzi wywołania interfejsu API:

![Zarządzanie interfejsami API w sieci wirtualnej adres IP](media/api-management-howto-ip-addresses/vnet-ip.png)


```json
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>

{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.85.20.170"
    ],
    "privateIPAddresses": [
      "192.168.1.5"
    ],
    ...
  },
  ...
}
```

Usługa API Management używa publicznego adresu IP dla połączeń poza siecią wirtualną i prywatnego adresu IP dla połączeń w sieci wirtualnej.

## <a name="ip-addresses-of-consumption-tier-api-management-service"></a>Adresy IP usługi Zarządzanie interfejsami API warstwy zużycie

Jeśli usługa api Management jest usługą warstwy zużycie, nie ma dedykowanego adresu IP. Usługa warstwy zużycia jest uruchamiana w infrastrukturze udostępnionej i bez deterministycznego adresu IP. 

Na potrzeby ograniczeń ruchu można użyć zakresu adresów IP centrów danych platformy Azure. Szczegółowe kroki można znaleźć [w dokumentacji usługi Azure Functions.](../azure-functions/ip-addresses.md#data-center-outbound-ip-addresses)

## <a name="changes-to-the-ip-addresses"></a>Zmiany w adresach IP

W warstwach Developer, Basic, Standard i Premium zarządzania interfejsami API publiczne adresy IP (VIP) są statyczne przez cały okres istnienia usługi, z następującymi wyjątkami:

* Usługa zostanie usunięta, a następnie ponownie utworzona.
* Subskrypcja usługi zostanie [zawieszona](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) lub [ostrzeżona](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) (na przykład za niepłacenie), a następnie przywrócona.
* Usługa Azure Virtual Network jest dodawana lub usuwana z usługi.
* Usługa api Management jest przełączana między trybem wdrażania zewnętrznej i wewnętrznej sieci wirtualnej.

W [przypadku wdrożeń wieloregionalnych](api-management-howto-deploy-multi-region.md)regionalny adres IP zmienia się, jeśli region jest opuszczony, a następnie przywrócony.
