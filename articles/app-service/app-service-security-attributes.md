---
title: Atrybuty zabezpieczeń dla usługi Azure App Service
description: Lista kontrolna atrybutów zabezpieczeń do oceny usługi Azure App Service
services: app-service
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbaldwin
ms.openlocfilehash: 1d7ab8008e8fbdb5f851f158d14f62bdea803f11
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/22/2019
ms.locfileid: "66001698"
---
# <a name="security-attributes-for-azure-app-service"></a>Atrybuty zabezpieczeń dla usługi Azure App Service

W tym artykule opisano typowe atrybuty zabezpieczeń wbudowane w usłudze Azure App Service.

[!INCLUDE [Security attributes header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Zapobiegawczych

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie w spoczynku (na przykład szyfrowanie po stronie serwera, szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta i inne funkcje szyfrowania) | Tak | Zawartość pliku witryny sieci Web jest przechowywana w usłudze Azure Storage automatycznie szyfruje zawartość w stanie spoczynku. Zobacz [szyfrowanie usługi Azure Storage dla danych magazynowanych](../storage/common/storage-service-encryption.md).<br><br>Wpisy tajne klienta dostarczane są szyfrowane, gdy. Wpisy tajne są szyfrowane, gdy podczas przechowywane w bazach danych konfiguracji usługi App Service.<br><br>Lokalnie dołączonych dysków może opcjonalnie służyć jako tymczasowy magazyn przez witryny sieci Web (D:\local i % TMP %). Podłączone lokalnie dyski nie są szyfrowane w stanie spoczynku. |
| Szyfrowanie podczas transferu (np. szyfrowanie usługi ExpressRoute w sieci wirtualnej, szyfrowania i szyfrowania sieć wirtualna-sieć wirtualna)| Tak | Klienci mogą skonfigurować witryny sieci web w celu wymagania i używać protokołu HTTPS dla ruchu przychodzącego. Zobacz wpis w blogu [jak usługi Azure App Service tylko HTTPS](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/). |
| Obsługa klucza szyfrowania (CMK BYOK, itp.)| Tak | Klienci mogą wybrać opcję przechowywania wpisów tajnych aplikacji w usłudze Key Vault i pobierać je w czasie wykonywania. Zobacz [odwołuje się do użycia magazynu kluczy dla usługi App Service i Azure Functions (wersja zapoznawcza)](app-service-key-vault-references.md).|
| Szyfrowanie na poziomie kolumny (Azure Data Services)| ND | |
| Wywołania interfejsu API szyfrowane| Tak | Występować wywołań zarządzania można konfigurować usługę App Service za pośrednictwem [usługi Azure Resource Manager](../azure-resource-manager/index.yml) wywołań za pośrednictwem protokołu HTTPS. |

## <a name="network-segmentation"></a>Segmentacji sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktu końcowego usługi| Tak | Obecnie dostępne w wersji zapoznawczej dla usługi App Service. Zobacz [ograniczenia dostępu do usługi Azure App Service](app-service-ip-restrictions.md). |
| Obsługa iniekcji sieci wirtualnej| Tak | Środowiska usługi App Service są implementacji prywatnej usługi App Service dedykowanego pojedynczemu klientowi, które są wstrzykiwane do sieci wirtualnej klienta. Zobacz [wprowadzenie do środowisk App Service Environment](environment/intro.md). |
| Izolacja sieci i Firewalling pomocy technicznej| Tak | Publiczne odmiany wielodostępnej usługi App Service klienci mogą skonfigurować listy ACL (ograniczenia adresów IP) do blokowania dozwolonego ruchu przychodzącego sieci.  Zobacz [ograniczenia dostępu do usługi Azure App Service](app-service-ip-restrictions.md).  Środowiska usługi App Service są wdrażane bezpośrednio do sieci wirtualnych i dlatego mogą być chronione przy użyciu sieciowych grup zabezpieczeń. |
| Obsługa tunelowania wymuszonego| Tak | Środowiska usługi App Service można wdrożyć w sieci wirtualnej klienta, gdy wymuszanie tunelowania jest konfigurowane. Klienci muszą postępuj zgodnie z instrukcjami [skonfigurować środowisko App Service Environment przy wymuszonego tunelowania](environment/forced-tunnel-support.md). |

## <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa (usługi Log analytics, usługi App insights itp.) do monitorowania platformy Azure| Tak | Usługa App Service integruje się z usługą Application Insights dla języków, które obsługują usługi Application Insights (pełny program .NET Framework, .NET Core, Java i Node.JS).  Zobacz [wydajności monitorowania usługi Azure App Service](../azure-monitor/app/azure-web-apps.md). Usługa App Service wysyła również metryki aplikacji w usłudze Azure Monitor. Zobacz [monitorować aplikacje w usłudze Azure App Service](web-sites-monitor.md). |

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Tak | Klienci mogą tworzyć aplikacje w usłudze App Service, które integrują się automatycznie z [usługi Azure Active Directory (Azure AD)](../active-directory/index.md) oraz innych dostawców tożsamości zgodnych OAuth; zobacz [uwierzytelnianie i autoryzacja w Usługa Azure App Service](overview-authentication-authorization.md). Zarządzanie dostępu do zasobów usługi App Service wszystkie dostęp jest kontrolowany przy użyciu kombinacji nazwy głównej usługi Azure AD uwierzytelniony i ról RBAC usługi Resource Manager platformy Azure. |
| Autoryzacja| Tak | Zarządzanie dostępu do zasobów usługi App Service wszystkie dostęp jest kontrolowany przy użyciu kombinacji nazwy głównej usługi Azure AD uwierzytelniony i ról RBAC usługi Resource Manager platformy Azure.  |


## <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie płaszczyzny zarządzania i kontroli i inspekcji| Tak | Wszystkie operacje zarządzania wykonywane na obiektach usługi App Service wystąpić za pośrednictwem [usługi Azure Resource Manager](../azure-resource-manager/index.yml). Historyczne Dzienniki te operacje są dostępne zarówno w portalu, jak i za pomocą interfejsu wiersza polecenia; zobacz [operacji dostawcy zasobów usługi Azure Resource Manager](../role-based-access-control/resource-provider-operations.md#microsoftweb) i [az monitor z dziennika aktywności](/cli/azure/monitor/activity-log). |
| Rejestrowanie płaszczyzny danych i inspekcji | Nie | Płaszczyzny danych dla usługi App Service jest zdalnym udziale plików zawierających zawartości wdrożonych witryn sieci web klienta.  Nie ma żadnych inspekcji w zdalnym udziale plików. |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracji (przechowywanie wersji konfiguracji itp.)| Tak | Dla operacji zarządzania stan konfiguracji usługi App Service można wyeksportować jako szablon usługi Azure Resource Manager i wersjonowanymi wraz z upływem czasu. Dla operacji środowiska uruchomieniowego klienci mogą zachować wiele różnych wersji na żywo aplikacji przy użyciu funkcji miejsc wdrożenia usługi App Service. | 

