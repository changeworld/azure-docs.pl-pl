---
title: Azure Front drzwiczki Service — interfejs API REST | Dokumentacja firmy Microsoft
description: Ten artykuł pomoże Ci odwoływać się do specyfikacji interfejsu API REST dla usługi Azure Service drzwiami frontowymi wraz z zasady zapory aplikacji sieci Web
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: a827889684b3713593c7bb5ce7b621ae91816ed1
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47048136"
---
# <a name="rest-api-specification-for-azure-front-door-service"></a>Specyfikacja interfejsu API REST dla usługi Azure Service drzwi
Usługa Azure drzwiami frontowymi umożliwia definiowanie, zarządzanie i monitorowanie globalnej routing ruchu w sieci web, optymalizując uzyskać najlepszą wydajność i błyskawiczne globalnego trybu failover w celu zapewnienia wysokiej dostępności. Za pomocą drzwiami frontowymi można przekształcić usługi globalne konsumentów (wielu regionów) oraz aplikacje dla przedsiębiorstw do niezawodne, wysokowydajnych spersonalizowane nowoczesnych aplikacji, interfejsów API i zawartość, która dotrzeć do odbiorców globalnych, którzy za pomocą platformy Azure.


Poniższa tabela zawiera odwołanie interfejsu API REST dla usługi Azure Service wejściu.

Wersja interfejsu API: 2018-08-01

| Grupy operacji | Opis |
|-------------|------------|
| [Drzwi](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/frontdoor/resource-manager/Microsoft.Network/preview/2018-08-01-preview/frontdoor.json) | Definiuje, jak tworzyć i zarządzać konfiguracją drzwi |
| [Zasady zapory aplikacji sieci Web](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/frontdoor/resource-manager/Microsoft.Network/preview/2018-08-01-preview/webapplicationfirewall.json) | Definiuje sposób zarządzania zasady zapory aplikacji sieci web dla usługi drzwi |
