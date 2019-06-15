---
title: Klient i serwer przechowywanie wersji zestawu SDK funkcji Mobile Apps i Mobile Services | Dokumentacja firmy Microsoft
description: Lista zestawów SDK klienta i zgodności z wersji zestawu SDK serwera dla usług Mobile Services i Azure Mobile Apps
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 35b19672-c9d6-49b5-b405-a6dcd1107cd5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: cfa6a363725c35083b32d6de1dd1371777f91907
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66240302"
---
# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>Przechowywanie wersji klienta i serwera w funkcji Mobile Apps i Mobile Services
Najnowsza wersja usługi Azure Mobile Services to **Mobile Apps** funkcji Azure App Service.

Zestawy SDK klienta i serwera funkcji Mobile Apps pierwotnie zależą od tych w usługach Mobile Services, ale są one *nie* ze sobą zgodne.
Oznacza to, należy użyć *Mobile Apps* zestawu SDK klienta za pomocą *Mobile Apps* zestaw SDK serwera i podobnie dla *usług Mobile Services*. Ten kontrakt jest wymuszane za pośrednictwem wartość nagłówka specjalne używany przez klienta i serwera zestawów SDK, `ZUMO-API-VERSION`.

Uwaga: zawsze, gdy ten dokument odwołuje się do *usług Mobile Services* wewnętrznej bazy danych, nie zawsze musi być hostowane w usłudze Mobile Services. Teraz jest możliwa migracja usługi mobilnej do uruchamiania w usłudze App Service bez żadnych zmian w kodzie, ale nadal będzie korzystać z usługi *usług Mobile Services* wersji zestawu SDK.

Aby dowiedzieć się więcej na temat migracji do usługi App Service bez żadnych zmian w kodzie, zobacz artykuł [migracji usługi mobilnej w usłudze Azure App Service].

## <a name="header-specification"></a>Specyfikacja nagłówka
Klucz `ZUMO-API-VERSION` może być określony w nagłówku HTTP lub ciągu zapytania. Wartość jest ciągiem wersji w postaci **x.y.z**.

Na przykład:

POBIERZ https://service.azurewebsites.net/tables/TodoItem

NAGŁÓWKI: ZUMO-API-VERSION: 2.0.0

POST https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0

## <a name="opting-out-of-version-checking"></a>Rezygnacja z sprawdzania wersji
Użytkownik może zrezygnować z wersji sprawdzanie przez ustawienie wartości **true** dla ustawienia aplikacji **MS_SkipVersionCheck**. Określ, z pliku web.config albo w obszarze Ustawienia aplikacji w witrynie Azure Portal.

> [!NOTE]
> Istnieje szereg zmian w zachowaniu między usługami Mobile Services i Mobile Apps, szczególnie w obszarach synchronizacji w trybie offline, uwierzytelnianie i powiadomienia wypychane. Tylko powinien możesz zrezygnować z wersji sprawdzania po Ukończ testowanie zapewnienie te zachowania zmiany nie przerywają funkcjonalności aplikacji.

## <a name="2.0.0"></a>Usługa Azure Mobile Apps klienta i serwera
### <a name="MobileAppsClients"></a> Mobile *aplikacje* zestawów SDK klienta
Sprawdzanie wersji został wprowadzony w następujących wersjach zestawu SDK klienta dla **usługi Azure Mobile Apps**:

| Platforma klienta | Version | Wartość nagłówka wersji |
| --- | --- | --- |
| Klient zarządzany (Windows, Xamarin) |[2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) |2.0.0 |
| iOS |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=529823) |2.0.0 |
| Android |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) |3.0.0 |

### <a name="mobile-apps-server-sdks"></a>Mobile *aplikacje* zestawami SDK serwera
Sprawdzanie wersji znajduje się w poniższych wersjach zestawu SDK serwera:

| Platforma serwera | SDK | Zaakceptowana wersja nagłówka |
| --- | --- | --- |
| .NET |[Microsoft.Azure.Mobile.Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) |2.0.0 |
| Node.js |[azure-mobile-apps)](https://www.npmjs.com/package/azure-mobile-apps) |2.0.0 |

### <a name="behavior-of-mobile-apps-backends"></a>Zachowanie zaplecza funkcji Mobile Apps
| ZUMO-API-VERSION | Wartość MS_SkipVersionCheck | Odpowiedź |
| --- | --- | --- |
| x.y.z ani mieć wartości Null |True |200 — OK |
| Null |Określona wartość false/nie |400 - Niewłaściwe żądanie |
| 1.x.y |Określona wartość false/nie |400 - Niewłaściwe żądanie |
| 2.0.0-2.x.y |Określona wartość false/nie |200 — OK |
| 3.0.0-3.x.y |Określona wartość false/nie |400 - Niewłaściwe żądanie |

[Mobile Services clients]: #MobileServicesClients
[Mobile Apps clients]: #MobileAppsClients
[Mobile App Server SDK]: https://www.nuget.org/packages/microsoft.azure.mobile.server
