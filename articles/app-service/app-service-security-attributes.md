---
title: Atrybuty zabezpieczeń dla Azure App Service
description: Lista kontrolna atrybutów zabezpieczeń do oceny Azure App Service
services: app-service
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbaldwin
ms.openlocfilehash: 299262610c027529749840720f46d6dc97a07b21
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442266"
---
# <a name="security-attributes-for-azure-app-service"></a>Atrybuty zabezpieczeń dla Azure App Service

W tym artykule opisano atrybuty zabezpieczeń wbudowane w Azure App Service.

[!INCLUDE [Security attributes header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Zapobiegawczej

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie w spoczynku (takie jak szyfrowanie po stronie serwera, szyfrowanie po stronie serwera z kluczami zarządzanymi przez klienta i inne funkcje szyfrowania) | Tak | Zawartość pliku witryny sieci Web jest przechowywana w usłudze Azure Storage, która automatycznie szyfruje zawartość w stanie spoczynku. Zobacz [szyfrowanie usługi Azure Storage dla danych przechowywanych w spoczynku](../storage/common/storage-service-encryption.md).<br><br>Klucze tajne dostarczone przez klienta są szyfrowane w stanie spoczynku. Wpisy tajne są szyfrowane w stanie spoczynku, podczas gdy są przechowywane w App Service bazach danych konfiguracji.<br><br>Opcjonalnie dyski dołączone lokalnie mogą być używane jako magazyn tymczasowy przez witryny sieci Web (D:\Local i% TMP%). Dyski dołączone lokalnie nie są szyfrowane w stanie spoczynku. |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie sieci wirtualnej)| Tak | Klienci mogą konfigurować witryny sieci Web tak, aby wymagały protokołu HTTPS i używać go do obsługi ruchu przychodzącego. Zapoznaj się z wpisem w blogu [jak utworzyć Azure App Service tylko https](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/). |
| Obsługa kluczy szyfrowania (CMK, BYOK itp.)| Yes | Klienci mogą wybrać przechowywanie wpisów tajnych aplikacji w Key Vault i pobrać je w czasie wykonywania. Zobacz [używanie Key Vault odwołań do App Service i Azure Functions (wersja zapoznawcza)](app-service-key-vault-references.md).|
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| ND | |
| Wywołania interfejsu API są szyfrowane| Tak | Wywołania zarządzania w celu skonfigurowania App Service występują za pośrednictwem [Azure Resource Manager](../azure-resource-manager/index.yml) wywołań za pośrednictwem protokołu HTTPS. |

## <a name="network-segmentation"></a>Segmentacja sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| Yes | Obecnie dostępne w wersji zapoznawczej dla App Service. Zobacz [ograniczenia dostępu Azure App Service](app-service-ip-restrictions.md). |
| Obsługa iniekcji sieci wirtualnej| Tak | Środowiska App Service są prywatnymi implementacjami App Service przeznaczonymi dla pojedynczego klienta, które zostały dodane do sieci wirtualnej klienta. Zobacz [wprowadzenie do środowisk App Service](environment/intro.md). |
| Izolacja sieci i obsługa zapór| Tak | W przypadku publicznej App Service odmiany wielodostępnej wielu dzierżawców klienci mogą konfigurować listy ACL sieci (ograniczenia adresów IP), aby zablokować dozwolony ruch przychodzący.  Zobacz [ograniczenia dostępu Azure App Service](app-service-ip-restrictions.md).  Środowiska App Service są wdrażane bezpośrednio w sieciach wirtualnych, dlatego można je zabezpieczyć za pomocą sieciowych grup zabezpieczeń. |
| Obsługa tunelowania wymuszonego| Tak | Środowiska App Service można wdrożyć w sieci wirtualnej klienta, w której jest skonfigurowany tunelowanie wymuszone. Klienci muszą postępować zgodnie z instrukcjami w temacie [konfigurowanie App Service Environment przy użyciu wymuszonego tunelowania](environment/forced-tunnel-support.md). |

## <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Tak | App Service integruje się z Application Insights dla języków, które obsługują Application Insights (pełny .NET Framework, .NET Core, Java i Node. JS).  Zobacz [monitorowanie wydajności Azure App Service](../azure-monitor/app/azure-web-apps.md). App Service również wysyła metryki aplikacji do Azure Monitor. Zobacz [monitorowanie aplikacji w Azure App Service](web-sites-monitor.md). |

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Tak | Klienci mogą tworzyć aplikacje na App Service, które automatycznie integrują się z [Azure Active Directory (Azure AD)](../active-directory/index.yml) , a także z innymi dostawcami tożsamości zgodnymi z uwierzytelnianiem OAuth. Zobacz [uwierzytelnianie i autoryzacja w Azure App Service](overview-authentication-authorization.md). W celu zarządzania dostępem do zasobów App Service, cały dostęp jest kontrolowany przez kombinację uwierzytelnionego podmiotu zabezpieczeń usługi Azure AD i Azure Resource Manager ról RBAC. |
| Authorization| Tak | W celu zarządzania dostępem do zasobów App Service, cały dostęp jest kontrolowany przez kombinację uwierzytelnionego podmiotu zabezpieczeń usługi Azure AD i Azure Resource Manager ról RBAC.  |


## <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania| Yes | Wszystkie operacje zarządzania wykonywane na App Service obiektów odbywają się za pośrednictwem [Azure Resource Manager](../azure-resource-manager/index.yml). Dzienniki historyczne tych operacji są dostępne zarówno w portalu, jak i za pośrednictwem interfejsu wiersza polecenia; Zobacz [Azure Resource Manager operacje dostawcy zasobów](../role-based-access-control/resource-provider-operations.md#microsoftweb) i [AZ monitor Activity-Log](/cli/azure/monitor/activity-log). |
| Rejestrowanie i inspekcja płaszczyzny danych | Nie | Płaszczyzna danych dla App Service jest zdalnym udziałem plików zawierającym zawartość witryny sieci Web wdrożonej przez klienta.  Nie istnieje Inspekcja zdalnego udziału plików. |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Tak | W przypadku operacji zarządzania stanem konfiguracji App Service można wyeksportować jako szablon Azure Resource Manager i wersji w czasie. W przypadku operacji środowiska uruchomieniowego klienci mogą obsługiwać wiele różnych wersji na żywo aplikacji przy użyciu funkcji App Service miejsc wdrożenia. | 

