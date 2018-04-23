---
title: Azure Service Fabric obraz magazynu ciągu połączenia | Dokumentacja firmy Microsoft
description: Zrozumienie parametry połączenia magazynu obrazu
services: service-fabric
documentationcenter: .net
author: alexwun
manager: timlt
editor: ''
ms.assetid: 00f8059d-9d53-4cb8-b44a-b25149de3030
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2018
ms.author: alexwun
ms.openlocfilehash: 3c34a3851dbb5c5258b3dc0cf35a510f62cbe14e
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2018
---
# <a name="understand-the-imagestoreconnectionstring-setting"></a>Zrozumienie ustawienie element ImageStoreConnectionString

W niektórych części dokumentacji możemy krótko wspomina istnienie parametrem "Element ImageStoreConnectionString" bez opisujące naprawdę to. I po przejściu przez artykułu, takich jak [Wdróż i usunąć aplikacje przy użyciu programu PowerShell][10], wygląda podobnie jak wszystkie zrobisz to kopiowania i wklejania wartość wyświetlaną w manifeście klastra w klastrze docelowym. Dlatego ustawienie musi być można skonfigurować dla klastra, ale po utworzeniu klastra za pomocą [portalu Azure][11], nie jest dostępna opcja do skonfigurowania tego ustawienia i jest zawsze "fabric: magazynu ImageStore". Celem tego ustawienia, następnie co to jest?

![Manifest klastra][img_cm]

Sieć szkieletowa usług uruchomiona jako platformę na potrzeby wewnętrzne firmy Microsoft przez wiele różnych zespołów, więc w dużym stopniu dostosowywane niektórych aspektów — "Image Store" jest jednym z aspektów. Zasadniczo Image Store jest podłączany repozytorium do przechowywania pakietów aplikacji. Gdy aplikacja jest wdrażana dla węzła w klastrze, ten węzeł pobiera zawartość pakietu aplikacji w magazynie obrazów. Element ImageStoreConnectionString to ustawienie obejmuje wszystkie informacje niezbędne dla klientów i węzłów można znaleźć poprawnej lokalizacji obrazu dla danego klastra.

Obecnie istnieją trzy możliwe typy dostawców magazynu obrazów i ich odpowiednie parametry połączenia są następujące:

1. Usługa składnika Image Store: "fabric: magazynu ImageStore"

2. System plików: "Ścieżka systemu file:[file]"

3. Azure Storage: "xstore:DefaultEndpointsProtocol=https;AccountName=[...];AccountKey=[...];Container=[...]"

Typ dostawcy używany w środowisku produkcyjnym stanowi usługę Magazyn obrazu, który jest usługa stanowa systemowa utrwalonego widać w narzędziu Service Fabric Explorer. 

![Usługi magazynu obrazów][img_is]

Hosting magazynu obrazów w usłudze system w obrębie samego klastra eliminuje zależności zewnętrznych dla repozytorium pakietów i daje większą kontrolę nad lokalizacji magazynu. Pod kątem dostawcy magazynu obrazów, najpierw, jeśli nie wyłącznie prawdopodobnie przyszłe ulepszenia wokół magazynu obrazów. Parametry połączenia dla dostawcy usługi magazynu obraz nie ma żadnych unikatowych informacji, ponieważ klient jest już połączony do klastra docelowego. Klient musi znać tylko protokoły przeznaczonych dla usługi system powinien być używany.

Dostawcy systemu plików umożliwia zamiast usługi magazynu obrazu dla lokalnych klastrów jeden pole podczas tworzenia nieco szybciej bootstrap klastra. Różnica jest mała zwykle, ale jest przydatne optymalizacji dla większość osób podczas tworzenia. Istnieje możliwość wdrażania klastra lokalnego pole jednego z innych magazynów dostawcy typów również, ale zwykle nie istnieje przyczyna Aby to zrobić, ponieważ programowanie i testowanie przepływu pracy jest taka sama niezależnie od dostawcy. Dostawca usługi Azure Storage istnieje tylko do obsługi starszych wersji starych klastrów wdrożone przed wprowadzeniem dostawcy usługi magazynu obrazów.

Ponadto ani dostawcy systemu plików ani dostawcę magazynu Azure powinna być używana jako metodę udostępniania magazynu obrazów między klastrami z wieloma — spowoduje to uszkodzenie danych konfiguracji klastra zgodnie z każdym klastrze można zapisywać danych powodujące konflikt Obraz magazynu. Aby udostępnić pakiety aplikacji udostępnianych między wielu klastrów, należy użyć [sfpkg] [ 12] pliki, które mogą być przekazywane do dowolnego magazynu zewnętrznego z identyfikatora URI pobierania.

Dlatego element ImageStoreConnectionString jest konfigurowalne, zazwyczaj użycia ustawieniem domyślnym. Podczas publikowania na platformie Azure za pomocą programu Visual Studio, parametr jest ustawiane automatycznie odpowiednio. Programowe wdrażanie klastrów hostowana na platformie Azure ciąg połączenia jest zawsze "fabric: magazynu ImageStore". Chociaż w razie wątpliwości, jego wartość zawsze można sprawdzić przez pobranie manifestu klastra przez [PowerShell](https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricclustermanifest), [.NET](https://msdn.microsoft.com/library/azure/mt161375.aspx), lub [REST](https://docs.microsoft.com/rest/api/servicefabric/get-a-cluster-manifest). Testowanie lokalnie i klastrów produkcyjnych powinno być zawsze konfigurowane korzysta z dostawcy usługi magazynu obrazów.

### <a name="next-steps"></a>Kolejne kroki
[Wdrażanie i usunąć aplikacje przy użyciu programu PowerShell][10]

<!--Image references-->
[img_is]: ./media/service-fabric-image-store-connection-string/image_store_service.png
[img_cm]: ./media/service-fabric-image-store-connection-string/cluster_manifest.png

[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-cluster-creation-via-portal.md
[12]: service-fabric-package-apps.md#create-an-sfpkg
