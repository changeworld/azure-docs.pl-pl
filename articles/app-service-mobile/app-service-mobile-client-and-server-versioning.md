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
ms.openlocfilehash: 56c5e0582afe55dcd63aa056817898d3d4942419
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60859077"
---
# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>Przechowywanie wersji klienta i serwera w funkcji Mobile Apps i Mobile Services
Najnowsza wersja usługi Azure Mobile Services to **Mobile Apps** funkcji Azure App Service.

Zestawy SDK klienta i serwera funkcji Mobile Apps pierwotnie zależą od tych w usługach Mobile Services, ale są one *nie* ze sobą zgodne.
Oznacza to, należy użyć *Mobile Apps* zestawu SDK klienta za pomocą *Mobile Apps* zestaw SDK serwera i podobnie dla *usług Mobile Services*. Ten kontrakt jest wymuszane za pośrednictwem wartość nagłówka specjalne używany przez klienta i serwera zestawów SDK, `ZUMO-API-VERSION`.

Uwaga: zawsze, gdy ten dokument odwołuje się do *usług Mobile Services* wewnętrznej bazy danych, nie zawsze musi być hostowane w usłudze Mobile Services. Teraz jest możliwa migracja usługi mobilnej do uruchamiania w usłudze App Service bez żadnych zmian w kodzie, ale nadal będzie korzystać z usługi *usług Mobile Services* wersji zestawu SDK.

Aby dowiedzieć się więcej na temat migracji do usługi App Service bez żadnych zmian w kodzie, zobacz artykuł [Migracja usługi mobilnej w usłudze Azure App Service].

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
>
>

## <a name="summary-of-compatibility-for-all-versions"></a>Podsumowanie zgodności dla wszystkich wersji
Na wykresie poniżej przedstawiono zgodność między wszystkie typy klienta i serwera. Zapleczem jest klasyfikowana jako albo Mobile **usług** lub Mobile **aplikacje** na podstawie zestawu SDK, który używa serwera.

|  | **Usługi Mobile Services** Node.js lub .NET | **Aplikacje mobilne** Node.js lub .NET |
| --- | --- | --- |
| [Klienci usług Mobile Services] |OK |Błąd\* |
| [Aplikacje mobilne klientów] |Błąd\* |OK |

\*To można kontrolować, określając **MS_SkipVersionCheck**.

<!-- IMPORTANT!  The anchors for Mobile Services and Mobile Apps MUST be 1.0.0 and 2.0.0 respectively, since there is an exception error message that uses those anchors. -->

<!-- NOTE: the fwlink to this document is https://go.microsoft.com/fwlink/?LinkID=690568 -->

## <a name="1.0.0"></a>Klienta usługi Mobile Services i serwera
Zestawy SDK klientów w poniższej tabeli są zgodne z **usług Mobile Services**.

Uwaga: usług Mobile Services zestawy SDK klientów *nie* Wyślij wartość nagłówka dla `ZUMO-API-VERSION`. Jeśli usługa otrzyma ten nagłówek lub wartości ciągu zapytania, zostanie zwrócony błąd, chyba że jawnie użytkownik zgodził się zgodnie z powyższym opisem.

### <a name="MobileServicesClients"></a> Mobile *usług* zestawów SDK klienta
| Platforma klienta | Wersja | Wartość nagłówka wersji |
| --- | --- | --- |
| Klient zarządzany (Windows, Xamarin) |[1.3.2](https://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.2) |Nie dotyczy |
| iOS |[2.2.2](https://aka.ms/gc6fex) |Nie dotyczy |
| Android |[2.0.3](https://go.microsoft.com/fwLink/?LinkID=280126) |Nie dotyczy |
| HTML |[1.2.7](https://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.7.min.js) |Nie dotyczy |

### <a name="mobile-services-server-sdks"></a>Mobile *usług* zestawami SDK serwera
| Platforma serwera | Wersja | Zaakceptowana wersja nagłówka |
| --- | --- | --- |
| .NET |[WindowsAzure.MobileServices.Backend.* Version 1.0.x](https://www.nuget.org/packages/WindowsAzure.MobileServices.Backend/) |**Nie nagłówka wersji** |
| Node.js |(już wkrótce) |**Nie nagłówka wersji** |

<!-- TODO: add Node npm version -->

### <a name="behavior-of-mobile-services-backends"></a>Zachowanie zaplecza usług Mobile Services
| ZUMO-API-VERSION | Wartość MS_SkipVersionCheck | Odpowiedź |
| --- | --- | --- |
| Nie określono |Dowolne |200 — OK |
| Dowolna wartość |True |200 — OK |
| Dowolna wartość |Określona wartość false/nie |400 - Niewłaściwe żądanie |

## <a name="2.0.0"></a>Usługa Azure Mobile Apps klienta i serwera
### <a name="MobileAppsClients"></a> Mobile *aplikacje* zestawów SDK klienta
Sprawdzanie wersji został wprowadzony w następujących wersjach zestawu SDK klienta dla **usługi Azure Mobile Apps**:

| Platforma klienta | Wersja | Wartość nagłówka wersji |
| --- | --- | --- |
| Klient zarządzany (Windows, Xamarin) |[2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) |2.0.0 |
| iOS |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=529823) |2.0.0 |
| Android |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) |3.0.0 |

<!-- TODO: add HTML version when released -->

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

## <a name="next-steps"></a>Następne kroki
* [Migracja usługi mobilnej w usłudze Azure App Service]

[Klienci usług Mobile Services]: #MobileServicesClients
[Aplikacje mobilne klientów]: #MobileAppsClients


[Mobile App Server SDK]: https://www.nuget.org/packages/microsoft.azure.mobile.server
[Migracja usługi mobilnej w usłudze Azure App Service]: app-service-mobile-migrating-from-mobile-services.md
