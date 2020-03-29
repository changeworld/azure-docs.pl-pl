---
title: Przechowywanie wersji sdk klienta i serwera
description: Lista zestawów SDK klienta i zgodność z wersjami zestawów SDK serwera dla usług mobilnych i aplikacji mobilnych platformy Azure.
ms.assetid: 35b19672-c9d6-49b5-b405-a6dcd1107cd5
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.openlocfilehash: a9ba442c00ec2498139ee34a1ff7497c98f17ede
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293480"
---
# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>Przechowywanie wersji dla klientów i serwerów w aplikacjach mobilnych i usługach mobilnych

Najnowsza wersja usług azure mobile services to funkcja **aplikacji mobilnych** usługi Azure App Service.

SDK klientów i serwerów aplikacji mobilnych są pierwotnie oparte na tych w usługach mobilnych, ale *nie* są ze sobą zgodne.
Oznacza to, że należy użyć sdk klienta *aplikacji mobilnych* z SDK serwera *aplikacji mobilnych* i podobnie dla *usług mobilnych*. Ta umowa jest wymuszana za pomocą specjalnej wartości nagłówka używanej przez sdk klienta i serwera, `ZUMO-API-VERSION`.

Uwaga: za każdym razem, gdy ten dokument odnosi się do zaplecza *usług mobilnych,* nie musi być hostowany w usługach mobilnych. Teraz można przeprowadzić migrację usługi mobilnej do uruchomienia w usłudze App Service bez żadnych zmian kodu, ale usługa nadal będzie używać wersji SDK *usług mobilnych.*

## <a name="header-specification"></a>Specyfikacja nagłówka
Klucz `ZUMO-API-VERSION` może być określony w nagłówku HTTP lub ciągu zapytania. Wartość jest ciągiem wersji w postaci **x.y.z**.

Przykład:

`GET https://service.azurewebsites.net/tables/TodoItem`

NAGŁÓWKI: ZUMO-API-VERSION: 2.0.0

`POST https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0`

## <a name="opting-out-of-version-checking"></a>Rezygnacja z sprawdzania wersji
Możesz zrezygnować z sprawdzania wersji, ustawiając wartość **true** dla ustawienia aplikacji **MS_SkipVersionCheck**. Określ to w witrynie web.config lub w sekcji Ustawienia aplikacji w witrynie Azure portal.

> [!NOTE]
> Istnieje wiele zmian w zachowaniu między usługami mobilnymi i aplikacjami mobilnymi, szczególnie w obszarach synchronizacji w trybie offline, uwierzytelniania i powiadomień wypychanych. Należy zrezygnować z sprawdzania wersji tylko po zakończeniu testowania, aby upewnić się, że te zmiany behawioralne nie łamią funkcji aplikacji.

## <a name="azure-mobile-apps-client-and-server"></a><a name="2.0.0"></a>Klient i serwer usługi Azure Mobile Apps
### <a name="mobile-apps-client-sdks"></a><a name="MobileAppsClients"></a>*SDK* klienta aplikacji mobilnych
Wprowadzono sprawdzanie wersji, począwszy od następujących wersji sdk klienta dla **aplikacji mobilnych platformy Azure:**

| Klient platforma | Wersja | Wartość nagłówka wersji |
| --- | --- | --- |
| Klient zarządzany (Windows, Xamarin) |[2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) |2.0.0 |
| iOS |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=529823) |2.0.0 |
| Android |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) |3.0.0 |

### <a name="mobile-apps-server-sdks"></a>SDK serwera *aplikacji* mobilnych
Sprawdzanie wersji jest zawarte w następujących wersjach SDK serwera:

| Platforma serwerowa | SDK | Nagłówek zaakceptowana wersja |
| --- | --- | --- |
| .NET |[Microsoft.Azure.Mobile.Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) |2.0.0 |
| Node.js |[azure-mobile-apps)](https://www.npmjs.com/package/azure-mobile-apps) |2.0.0 |

### <a name="behavior-of-mobile-apps-backends"></a>Zachowanie zaplecza aplikacji mobilnych
| ZUMO-API-WERSJA | Wartość MS_SkipVersionCheck | Odpowiedź |
| --- | --- | --- |
| x.y.z lub null |True |200 - OK |
| Null |Fałsz/nieokreślone |400 — Nieprawidłowe żądanie |
| 1.x.r. |Fałsz/nieokreślone |400 — Nieprawidłowe żądanie |
| 2.0.0-2.x.y |Fałsz/nieokreślone |200 - OK |
| 3.0.0-3.x.y |Fałsz/nieokreślone |400 — Nieprawidłowe żądanie |

[Mobile Services clients]: #MobileServicesClients
[Mobile Apps clients]: #MobileAppsClients
[Mobile App Server SDK]: https://www.nuget.org/packages/microsoft.azure.mobile.server
