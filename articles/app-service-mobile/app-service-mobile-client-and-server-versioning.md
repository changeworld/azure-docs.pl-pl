---
title: Przechowywanie wersji zestawu SDK klienta i serwera
description: Lista zestawów SDK klienta i zgodność z wersjami zestawu SDK serwera dla Mobile Services i Mobile Apps platformy Azure.
ms.assetid: 35b19672-c9d6-49b5-b405-a6dcd1107cd5
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.openlocfilehash: f24ae0a48b835785a2e000210f3609b82d42d0f6
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461559"
---
# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>Przechowywanie wersji klienta i serwera w Mobile Apps i Mobile Services

Najnowsza wersja platformy Azure Mobile Services jest funkcją **Mobile Apps** Azure App Service.

Zestawy SDK klienta i serwera Mobile Apps są pierwotnie oparte na Mobile Services, ale *nie* są ze sobą zgodne.
Oznacza to, że należy użyć zestawu SDK klienta *Mobile Apps* z zestawem SDK *Mobile Apps* Server, podobnie jak w przypadku *Mobile Services*. Ta umowa jest wymuszana za pomocą specjalnej wartości nagłówka używanej przez zestawy SDK klienta i serwera `ZUMO-API-VERSION`.

Uwaga: zawsze, gdy ten dokument odwołuje się do *Mobile Services* zaplecza, nie musi być hostowany na Mobile Services. Teraz można migrować usługę mobilną do uruchamiania na App Service bez żadnych zmian w kodzie, ale usługa nadal będzie używać *Mobile Services* wersji zestawu SDK.

## <a name="header-specification"></a>Specyfikacja nagłówka
Klucz `ZUMO-API-VERSION` może być określony w nagłówku HTTP lub ciągu zapytania. Wartość jest ciągiem wersji w postaci **x. y. z**.

Na przykład:

Pobierz https://service.azurewebsites.net/tables/TodoItem

NAGŁÓWKI: ZUMO-API-VERSION: 2.0.0

Opublikuj https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0

## <a name="opting-out-of-version-checking"></a>Rezygnacja z sprawdzania wersji
Możesz zrezygnować z sprawdzania wersji, ustawiając wartość **true** dla ustawienia aplikacji **MS_SkipVersionCheck**. Określ ją w pliku Web. config lub w sekcji Ustawienia aplikacji Azure Portal.

> [!NOTE]
> Istnieje wiele zmian w zachowaniu między Mobile Services i Mobile Apps, szczególnie w obszarach synchronizacji w trybie offline, uwierzytelniania i powiadomień wypychanych. Sprawdzanie wersji należy zrezygnować tylko po zakończeniu testowania, aby upewnić się, że te zmiany zachowania nie przerywają funkcjonalności aplikacji.

## <a name="2.0.0"></a>Klient i serwer Mobile Apps platformy Azure
### <a name="MobileAppsClients"></a>Zestawy SDK klienta *aplikacji* mobilnych
Wprowadzono Sprawdzanie wersji, rozpoczynając od następujących wersji zestawu Client SDK dla **platformy Azure Mobile Apps**:

| Platforma klienta | Wersja | Wartość nagłówka wersji |
| --- | --- | --- |
| Klient zarządzany (Windows, Xamarin) |[2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) |2.0.0 |
| iOS |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=529823) |2.0.0 |
| Android |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) |3.0.0 |

### <a name="mobile-apps-server-sdks"></a>Zestawy SDK serwera *aplikacji* mobilnych
Sprawdzanie wersji jest zawarte w następujących wersjach zestawu SDK serwera:

| Platforma serwera | SDK | Nagłówek zaakceptowanej wersji |
| --- | --- | --- |
| .NET |[Microsoft. Azure. Mobile. Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) |2.0.0 |
| Node.js |[Azure-Mobile-Apps)](https://www.npmjs.com/package/azure-mobile-apps) |2.0.0 |

### <a name="behavior-of-mobile-apps-backends"></a>Zachowanie Mobile Apps
| ZUMO-API-VERSION | Wartość MS_SkipVersionCheck | Odpowiedź |
| --- | --- | --- |
| x. y. z lub null |True |200 — OK |
| Null |Nie określono wartości false/not |400 — Nieprawidłowe żądanie |
| 1. x. y |Nie określono wartości false/not |400 — Nieprawidłowe żądanie |
| 2.0.0-2. x. y |Nie określono wartości false/not |200 — OK |
| 3.0.0-3. x. y |Nie określono wartości false/not |400 — Nieprawidłowe żądanie |

[Mobile Services clients]: #MobileServicesClients
[Mobile Apps clients]: #MobileAppsClients
[Mobile App Server SDK]: https://www.nuget.org/packages/microsoft.azure.mobile.server
