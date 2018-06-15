---
title: Klient i serwer przechowywanie wersji zestawu SDK w Mobile Apps i usług Mobile Services | Dokumentacja firmy Microsoft
description: Lista zestawów SDK klienta i zgodności serwera wersje zestawu SDK usług Mobile Services i usługi Azure Mobile Apps
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
ms.openlocfilehash: 37bf36af535eb9b5c8b0ba38434b71f1a6686811
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
ms.locfileid: "27593378"
---
# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>Przechowywanie wersji klienta i serwera w usługach Mobile i aplikacje mobilne
Najnowszą wersję usług Azure Mobile Services **Mobile Apps** funkcji Azure App Service.

Mobile Apps SDK klienta i serwera są początkowo na podstawie usług Mobile Services, ale są one *nie* zgodne ze sobą.
Oznacza to, należy użyć *Mobile Apps* zestawu SDK klienta z *Mobile Apps* serwera zestawu SDK i podobnie dla *usług Mobile Services*. Niniejsza Umowa jest wymuszane za pośrednictwem wartość nagłówka specjalnych używanych przez klienta i serwera zestawów SDK, `ZUMO-API-VERSION`.

Uwaga: po każdej zmianie tego dokumentu dotyczy *usług Mobile Services* wewnętrznej bazy danych, nie zawsze musi ma być obsługiwana w usłudze Mobile Services. Obecnie istnieje możliwość migracji usługi mobilnej do uruchamiania w usłudze aplikacji bez żadnych zmian w kodzie, ale usługi będą nadal stosowane *usług Mobile Services* wersji zestawu SDK.

Aby dowiedzieć się więcej na temat migracji do usługi aplikacji bez wprowadzania żadnych zmian kodu, zobacz artykuł [migracji usługi mobilnej w usłudze Azure App Service].

## <a name="header-specification"></a>Specyfikacja nagłówka
Klucz `ZUMO-API-VERSION` mogą być określone w nagłówku HTTP lub ciąg zapytania. Wartość jest ciąg wersji w postaci **x.y.z**.

Na przykład:

POBIERZ https://service.azurewebsites.net/tables/TodoItem

NAGŁÓWKI: ZUMO-API-VERSION: 2.0.0

POST https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0

## <a name="opting-out-of-version-checking"></a>Rezygnacja z kontroli wersji
Można zrezygnować z wersji sprawdzanie przez ustawienie wartości **true** dla ustawienia aplikacji **MS_SkipVersionCheck**. Określ, w pliku web.config lub w sekcji Ustawienia aplikacji w portalu Azure.

> [!NOTE]
> Istnieje wiele zmian zachowania między usług Mobile Services i aplikacji mobilnych, zwłaszcza w obszarach synchronizacji w trybie offline, uwierzytelniania i powiadomień wypychanych. Należy tylko opcja rezygnacji z wersji sprawdzania po Ukończ testowanie, aby upewnić się, że te zmiany zachowania nie dzielone funkcjonalności aplikacji.
>
>

## <a name="summary-of-compatibility-for-all-versions"></a>Podsumowanie zgodności dla wszystkich wersji
Poniższej tabeli przedstawiono informacje o zgodności między wszystkie typy klienta i serwera. Wewnętrznej bazie danych zostanie sklasyfikowany jako albo Mobile **usług** lub Mobile **aplikacji** na podstawie zestawu SDK, który używa serwera.

|  | **Usługi mobilne** Node.js lub .NET | **Aplikacje mobilne** Node.js lub .NET |
| --- | --- | --- |
| [Klienci usług Mobile Services] |OK |Błąd\* |
| [Aplikacje mobilne klientów] |Błąd\* |OK |

\*Może to być kontrolowane przez określenie **MS_SkipVersionCheck**.

<!-- IMPORTANT!  The anchors for Mobile Services and Mobile Apps MUST be 1.0.0 and 2.0.0 respectively, since there is an exception error message that uses those anchors. -->

<!-- NOTE: the fwlink to this document is http://go.microsoft.com/fwlink/?LinkID=690568 -->

## <a name="1.0.0"></a>Usługi mobilne klienta i serwera
Zestawy SDK klienta w poniższej tabeli są zgodne z **usług Mobile Services**.

Uwaga: klientem usług Mobile Services SDK *nie* wysyłania wartości nagłówka `ZUMO-API-VERSION`. Gdy usługa odbiera ten nagłówek lub ciąg zapytania zostanie zwrócony błąd, chyba że jawnie użytkownik zgodził się zgodnie z powyższym opisem.

### <a name="MobileServicesClients"></a> Mobile *usług* zestawów SDK klienta
| Platforma klienta | Wersja | Wartość nagłówka wersji |
| --- | --- | --- |
| Klient zarządzany (z systemem Windows, Xamarin) |[1.3.2](https://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.2) |Nie dotyczy |
| iOS |[2.2.2](http://aka.ms/gc6fex) |Nie dotyczy |
| Android |[2.0.3](https://go.microsoft.com/fwLink/?LinkID=280126) |Nie dotyczy |
| HTML |[1.2.7](http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.7.min.js) |Nie dotyczy |

### <a name="mobile-services-server-sdks"></a>Mobile *usług* server SDK
| Platforma serwera | Wersja | Zaakceptowana wersja nagłówka |
| --- | --- | --- |
| .NET |[WindowsAzure.MobileServices.Backend.* Version 1.0.x](https://www.nuget.org/packages/WindowsAzure.MobileServices.Backend/) |** Bez nagłówka wersji ** |
| Node.js |(wkrótce) |**Brak nagłówka wersji** |

<!-- TODO: add Node npm version -->

### <a name="behavior-of-mobile-services-backends"></a>Zachowanie zapleczy usług mobilnych
| ZUMO-API-VERSION | Wartość MS_SkipVersionCheck | Odpowiedź |
| --- | --- | --- |
| Nie określono |Dowolne |200 - OK |
| Dowolna wartość |True |200 - OK |
| Dowolna wartość |Określona wartość false nie |400 - Niewłaściwe żądanie |

## <a name="2.0.0"></a>Azure Mobile Apps klienta i serwera
### <a name="MobileAppsClients"></a> Mobile *aplikacji* zestawów SDK klienta
Sprawdzanie wersji wprowadzono począwszy od następujących wersji zestawu SDK klienta dla **Azure Mobile Apps**:

| Platforma klienta | Wersja | Wartość nagłówka wersji |
| --- | --- | --- |
| Klient zarządzany (z systemem Windows, Xamarin) |[2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) |2.0.0 |
| iOS |[3.0.0](http://go.microsoft.com/fwlink/?LinkID=529823) |2.0.0 |
| Android |[3.0.0](http://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) |3.0.0 |

<!-- TODO: add HTML version when released -->

### <a name="mobile-apps-server-sdks"></a>Mobile *aplikacji* server SDK
Sprawdzanie wersji znajduje się w następujących wersjach zestawu SDK serwera:

| Platforma serwera | SDK | Zaakceptowana wersja nagłówka |
| --- | --- | --- |
| .NET |[Microsoft.Azure.Mobile.Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) |2.0.0 |
| Node.js |[azure-mobile-apps)](https://www.npmjs.com/package/azure-mobile-apps) |2.0.0 |

### <a name="behavior-of-mobile-apps-backends"></a>Zachowanie zapleczy aplikacji mobilnych
| ZUMO-API-VERSION | Wartość MS_SkipVersionCheck | Odpowiedź |
| --- | --- | --- |
| x.y.z ani mieć wartości Null |True |200 - OK |
| Null |Określona wartość false nie |400 - Niewłaściwe żądanie |
| 1.x.y |Określona wartość false nie |400 - Niewłaściwe żądanie |
| 2.0.0-2.x.y |Określona wartość false nie |200 - OK |
| 3.0.0-3.x.y |Określona wartość false nie |400 - Niewłaściwe żądanie |

## <a name="next-steps"></a>Następne kroki
* [migracji usługi mobilnej w usłudze Azure App Service]

[Klienci usług Mobile Services]: #MobileServicesClients
[Aplikacje mobilne klientów]: #MobileAppsClients


[Mobile App Server SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[migracji usługi mobilnej w usłudze Azure App Service]: app-service-mobile-migrating-from-mobile-services.md
