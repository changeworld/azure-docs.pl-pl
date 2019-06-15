---
title: Przegląd dostawców niestandardowych usługi Azure (wersja zapoznawcza)
description: Zawiera opis pojęć dotyczących tworzenia dostawcy zasobów niestandardowych za pomocą usługi Azure Resource Manager
author: MSEvanhi
ms.service: managed-applications
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: evanhi
ms.openlocfilehash: bbfb10f612690af0f4fd3683e0f58986a21048d8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65159858"
---
# <a name="azure-custom-providers-preview-overview"></a>Omówienie niestandardowych dostawców w wersji zapoznawczej na platformie Azure

Za pomocą dostawców niestandardowych usługi Azure można rozszerzyć platformy Azure, aby pracować z Twoją usługą. Możesz utworzyć własnego dostawcę zasobów, w tym typów niestandardowych zasobów i akcji. Niestandardowego dostawcy jest zintegrowana z usługą Azure Resource Manager. Funkcje Menedżera zasobów, takie jak wdrożeń szablonu i kontroli dostępu opartej na rolach, można użyć do wdrożenia i zabezpieczanie usługi.

Ten artykuł zawiera omówienie niestandardowych dostawców i jego możliwości. Na poniższej ilustracji przedstawiono przepływ pracy dla zasobów i akcji zdefiniowanych w niestandardowego dostawcy.

![Omówienie niestandardowego dostawcy](./media/custom-providers-overview/overview.png)

> [!IMPORTANT]
> Niestandardowi dostawcy jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="define-your-custom-provider"></a>Definiowanie niestandardowego dostawcy

Możesz rozpocząć, umożliwiając usługi Azure Resource Manager o niestandardowego dostawcy. Wdrażanie na platformie Azure zasobu niestandardowego dostawcy, który używa typ zasobu **Microsoft.CustomProviders/resourceProviders**. W tego zasobu można zdefiniować zasoby i akcje dla Twojej usługi.

Na przykład, jeśli usługa wymaga typu zasobu o nazwie **użytkowników**, obejmują tego typu zasobu w definicji niestandardowego dostawcy. Dla każdego typu zasobu, musisz podać punktu końcowego, który udostępnia operacje REST (PUT, GET, DELETE) dla tego typu zasobu. Punkt końcowy może być hostowana na dowolnym środowisku i zawiera logikę jak usługa obsługuje operacje na typ zasobu.

Można również definiować niestandardowe akcje dla dostawcy zasobów. Działania reprezentują operacje POST. Za pomocą akcji dla operacji, takich jak początek, zatrzymywanie lub ponowne uruchomienie. Możesz podać punktu końcowego, który obsługuje żądanie.

Poniższy przykład pokazuje jak zdefiniować dostawcy niestandardowego za pomocą action i typu zasobu.

```json
{
  "apiVersion": "2018-09-01-preview",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "name": "[parameters('funcName')]",
  "location": "[parameters('location')]",
  "properties": {
    "actions": [
      {
        "name": "ping",
        "routingType": "Proxy",
        "endpoint": "[concat('https://', parameters('funcName'), '.azurewebsites.net/api/{requestPath}')]"
      }
    ],
    "resourceTypes": [
      {
        "name": "users",
        "routingType": "Proxy,Cache",
        "endpoint": "[concat('https://', parameters('funcName'), '.azurewebsites.net/api/{requestPath}')]"
      }
    ]
  }
},
```

Aby uzyskać **routingType**, dopuszczalne wartości `Proxy` i `Cache`. Serwer proxy oznacza, że żądania dla tego typu zasobu lub działań są obsługiwane przez punkt końcowy. Ustawienie pamięci podręcznej jest obsługiwana tylko dla typów zasobów, a nie akcji. Aby określić pamięci podręcznej, należy także określić serwer proxy. Pamięć podręczna oznacza, że odpowiedzi z punktu końcowego są przechowywane w celu optymalizacji operacji odczytu. Przy użyciu ustawienia pamięci podręcznej ułatwia implementowania interfejsu API, który jest spójny i zgodne z innymi usługami usługi Resource Manager.

## <a name="deploy-your-resource-types"></a>Wdrażanie typów zasobów

Po zdefiniowaniu niestandardowego dostawcy, można wdrożyć swoje typy zasobów niestandardowych. Poniższy przykład ilustruje JSON uwzględnić w szablonie, aby wdrożyć ten typ zasobu dla niestandardowego dostawcy. W tym samym szablonie z innymi zasobami platformy Azure można wdrożyć tego typu zasobu.

```json
{
    "apiVersion": "2018-09-01-preview",
    "type": "Microsoft.CustomProviders/resourceProviders/users",
    "name": "[concat(parameters('rpname'), '/santa')]",
    "location": "[parameters('location')]",
    "properties": {
        "FullName": "Santa Claus",
        "Location": "NorthPole"
    }
}
```

## <a name="manage-access"></a>Zarządzanie dostępem

Korzystanie z systemu Azure [kontroli dostępu opartej na rolach](../role-based-access-control/overview.md) zarządzanie dostępem do Twojego dostawcę zasobów. Możesz przypisać [wbudowane role](../role-based-access-control/built-in-roles.md) takich jak właściciela, współautora lub czytelnika dla użytkowników. Alternatywnie można zdefiniować [ról niestandardowych](../role-based-access-control/custom-roles.md) specyficznych dla operacji dostawcy zasobów.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule omówiono niestandardowych dostawców. Przejdź do następnego artykułu, aby utworzyć niestandardowego dostawcę.

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie niestandardowego dostawcy i wdrażanie zasobów niestandardowych](create-custom-provider.md)
