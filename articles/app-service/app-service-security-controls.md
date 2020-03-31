---
title: Mechanizmy zabezpieczeń
description: Znajdź listę kontrolną zabezpieczeń do oceny usługi Azure App Service dla twojej organizacji.
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 2586821c4c48f809efb5408c3cdae5e42e3b3fcf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74671449"
---
# <a name="security-controls-for-azure-app-service"></a>Mechanizmy kontroli zabezpieczeń usługi Azure App Service

Ten artykuł dokumentuje kontrolki zabezpieczeń wbudowane w usługę Azure App Service.

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Sieć)

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi | Dokumentacja
|---|---|--|
| Obsługa punktu końcowego usługi| Tak | Dostępne dla usługi app service.| [Ograniczenia dostępu do usługi Azure App Service](app-service-ip-restrictions.md)
| Obsługa wtrysku sieci wirtualnej| Tak | Środowiska usługi app service są prywatne implementacje usługi App Service dedykowane do jednego klienta wstrzykuje się do sieci wirtualnej klienta. | [Wprowadzenie do środowisk App Service Environment](environment/intro.md)
| Obsługa izolacji sieci i zapory sieciowej| Tak | W przypadku publicznej odmiany usługi App Service dla wielu dzierżawców klienci mogą skonfigurować sieciowe listy ACL (ograniczenia adresów IP), aby zablokować dozwolony ruch przychodzący.  Środowiska usługi app service są wdrażane bezpośrednio w sieciach wirtualnych i dlatego mogą być zabezpieczone za pomocą sieciowych grup zabezpieczeń. | [Ograniczenia dostępu do usługi Azure App Service](app-service-ip-restrictions.md)
| Wymuszone wsparcie tunelowania| Tak | Środowiska usługi app service można wdrożyć w sieci wirtualnej klienta, gdzie jest skonfigurowany wymuszony tunelowanie. | [Konfigurowanie wymuszonego tunelowania środowiska App Service Environment](environment/forced-tunnel-support.md)

## <a name="monitoring--logging"></a>Monitorowanie & rejestrowania

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi | Dokumentacja
|---|---|--|
| Obsługa monitorowania platformy Azure (analiza dzienników, wgląd w aplikacje itp.)| Tak | Usługa App Service integruje się z aplikacją App Insights dla języków obsługujących usługę Application Insights (Full .NET Framework, .NET Core, Java i Node.JS).  Zobacz [Monitorowanie wydajności usługi Azure App Service](../azure-monitor/app/azure-web-apps.md). Usługa App Service wysyła również metryki aplikacji do usługi Azure Monitor. | [Monitorowanie aplikacji w usłudze Azure App Service](web-sites-monitor.md)
| Rejestrowanie i audyt płaszczyzny kontroli i zarządzania| Tak | Wszystkie operacje zarządzania wykonywane na obiektach usługi App Service odbywają się za pośrednictwem [usługi Azure Resource Manager](../azure-resource-manager/index.yml). Dzienniki historyczne tych operacji są dostępne zarówno w portalu, jak i za pośrednictwem interfejsu wiersza polecenia. | [Operacje dostawcy zasobów usługi Azure Resource Manager](../role-based-access-control/resource-provider-operations.md#microsoftweb), dziennik aktywności [monitora az](/cli/azure/monitor/activity-log)
| Rejestrowanie i inspekcja płaszczyzny danych | Nie | Płaszczyzna danych dla usługi App Service to zdalny udział plików zawierający wdrożoną zawartość witryny sieci Web klienta.  Nie ma inspekcji zdalnego udziału plików. |

## <a name="identity"></a>Tożsamość

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi |  Dokumentacja
|---|---|--|
| Uwierzytelnianie| Tak | Klienci mogą tworzyć aplikacje w usłudze App Service, które automatycznie integrują się z [usługą Azure Active Directory (Azure AD),](../active-directory/index.yml) a także z innymi dostawcami tożsamości zgodnymi z usługą OAuth Aby dostęp do zasobów usługi App Service, cały dostęp był kontrolowany przez kombinację uwierzytelnionych zabezpieczeń usługi Azure AD i ról RBAC usługi Azure Resource Manager. | [Uwierzytelnianie i autoryzacja w usłudze Azure App Service](overview-authentication-authorization.md)
| Autoryzacja| Tak | W przypadku dostępu do zasobów usługi App Service cały dostęp jest kontrolowany przez kombinację uwierzytelnionego podmiotu usługi Azure AD i ról RBAC usługi Azure Resource Manager.  | [Uwierzytelnianie i autoryzacja w usłudze Azure App Service](overview-authentication-authorization.md)

## <a name="data-protection"></a>Ochrona danych

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi | Dokumentacja
|---|---|--|
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez firmę Microsoft | Tak | Zawartość pliku witryny sieci Web jest przechowywana w usłudze Azure Storage, która automatycznie szyfruje zawartość w spoczynku. <br><br>Wpisy tajne dostarczone przez klienta są szyfrowane w spoczynku. Wpisy tajne są szyfrowane w spoczynku, podczas gdy przechowywane w bazach danych konfiguracji usługi App Service.<br><br>Dyski podłączone lokalnie mogą być opcjonalnie używane jako tymczasowe przechowywanie przez witryny sieci Web (D:\local i %TMP%). Dyski podłączone lokalnie nie są szyfrowane w spoczynku. | [Szyfrowanie usługi Azure Storage dla danych w stanie spoczynku](../storage/common/storage-service-encryption.md)
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez klienta (BYOK) | Tak | Klienci mogą przechowywać wpisy tajne aplikacji w magazynie kluczy i pobierać je w czasie wykonywania. | [Korzystanie z odwołań do usługi App Service i usługi Azure Functions (wersja zapoznawcza)](app-service-key-vault-references.md)
| Szyfrowanie na poziomie kolumny (usługi Azure Data Services)| Nie dotyczy | |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie usługi ExpressRoute, szyfrowanie w sieci wirtualnej i szyfrowanie sieci wirtualnej wirtualnej)| Tak | Klienci mogą skonfigurować witryny sieci web tak, aby wymagały i używały protokołu HTTPS do ruchu przychodzącego.  | [Jak zrobić tylko https usługi Azure App Service](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/) (wpis w blogu)
| Szyfrowane wywołania interfejsu API| Tak | Wywołania zarządzania w celu skonfigurowania usługi App Service występują za pośrednictwem wywołań [usługi Azure Resource Manager](../azure-resource-manager/index.yml) za pośrednictwem protokołu HTTPS. |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi | Dokumentacja
|---|---|--|
| Obsługa zarządzania konfiguracją (przechowywanie wersji konfiguracji itp.)| Tak | W przypadku operacji zarządzania stan konfiguracji usługi App Service można wyeksportować jako szablon usługi Azure Resource Manager i wersjonować w czasie. W przypadku operacji w czasie wykonywania klienci mogą obsługiwać wiele różnych wersji aplikacji na żywo przy użyciu funkcji slotów wdrażania usługi App Service. | 

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wbudowanych zabezpieczeniach w usługach platformy Azure.](../security/fundamentals/security-controls.md)
