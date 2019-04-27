---
title: Parametry połączenia magazynu obrazów platformy Azure usługi Service Fabric | Dokumentacja firmy Microsoft
description: Zrozumienie parametry połączenia magazynu obrazu
services: service-fabric
documentationcenter: .net
author: alexwun
manager: chackdan
editor: ''
ms.assetid: 00f8059d-9d53-4cb8-b44a-b25149de3030
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2018
ms.author: alexwun
ms.openlocfilehash: 4a56b48c0041e963b89312c59335b45cabacc1bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60720199"
---
# <a name="understand-the-imagestoreconnectionstring-setting"></a>Omówienie ustawienia ImageStoreConnectionString

W niektórych naszej dokumentacji firma Microsoft krótko wspomina o identyfikatorach istnienie parametru "ImageStoreConnectionString" bez opisujący, co naprawdę oznacza. I po przeprowadzeniu artykułu, takich jak [Wdróż i usunąć aplikacje przy użyciu programu PowerShell][10], wygląda podobnie jak wszystkie zrobisz to kopiowania/wklejania wartość, jak pokazano w manifeście klastra w klastrze docelowym. Aby ustawienie musi być można skonfigurować dla klastra, ale podczas tworzenia klastra za pośrednictwem [witryny Azure portal][11], nie jest dostępna opcja do skonfigurowania tego ustawienia i jest zawsze "Service fabric: ImageStore". Celem tego ustawienia, następnie co to jest?

![Manifest klastra][img_cm]

Usługi Service Fabric pracy jako platformy do wewnętrznego użytku firmy Microsoft przez wiele różnych zespołów, więc w dużym stopniu dostosować niektóre aspekty — "Store obrazu" jest jednym z aspektów. Zasadniczo Store obrazu jest podłączany repozytorium do przechowywania pakietów aplikacji. Gdy aplikacja jest wdrażana do węzła w klastrze, ten węzeł pobierze zawartość pakietu aplikacji z Store obrazu. ImageStoreConnectionString to ustawienie, który zawiera wszystkie informacje niezbędne dla klientów i węzłów można znaleźć poprawne Store obrazu dla danego klastra.

Obecnie istnieją trzy rodzaje możliwe Store obraz dostawców i ich odpowiednie parametry połączenia są następujące:

1. Usługa Store obrazu: "Service fabric: ImageStore"

2. System plików: "Ścieżka systemu file:[file]"

3. Usługa Azure Storage: "xstore:DefaultEndpointsProtocol = https; AccountName = [...]; AccountKey = [...]; Kontener = [...] "

Typ dostawcy używane w środowisku produkcyjnym jest usługę Store obrazu, który z nich jest usługa stanowa system utrwalonych wyświetlanych w narzędziu Service Fabric Explorer. 

![Usługa Store obrazu][img_is]

Hosting Store obrazu w usłudze system, w obrębie samego klastra eliminuje zależności zewnętrznych dotyczących repozytorium pakietów i daje większą kontrolę nad lokalizacji magazynu. Przyszłe ulepszenia wokół Store obrazu będzie prawdopodobnie pod kątem dostawca Store obrazu, najpierw, o ile nie wyłącznie. Parametry połączenia dla dostawcy usług Store obraz nie ma żadnych unikatowych informacji, ponieważ klient jest podłączony do klastra docelowego. Tylko klient musi wiedzieć, że protokoły przeznaczone dla usługi system powinien być używany.

Dostawca systemu plików jest używany zamiast usługi Store obrazów lokalnych klastrów jednopunktowe podczas tworzenia w nieco szybsze uruchomienie klastra. Różnica polega na tym zwykle małe, ale jest przydatne optymalizacji dla większości osób podczas programowania. Umożliwia wdrożenie lokalnego klastra jednopunktowe przy użyciu innego magazynu dostawcy typu także, ale zwykle nie ma powodu aby to zrobić, ponieważ programowanie i testowanie przepływu pracy pozostaje taki sam, niezależnie od dostawcy. Dostawca usługi Azure Storage istnieje tylko do obsługi starszych wersji starych klastry wdrażane przed wprowadzeniem obraz Store usługodawcy.

Ponadto nie dostawcy systemu plików lub dostawcy usługi Azure Storage, powinny służyć jako metoda udostępniania Store obrazów między wieloma klastrami — spowoduje to uszkodzenie danych konfiguracji klastra zgodnie z każdym klastrze można napisać powodujące konflikt danych do obrazu Store. Aby udostępnić pakietów aplikacji elastycznie między wiele klastrów, należy użyć [sfpkg] [ 12] zamiast tego pliki, które mogą być przekazywane do dowolnego zewnętrznego magazynu za pomocą identyfikatora URI pobierania.

Dlatego ImageStoreConnectionString jest konfigurowalne, możesz po prostu użyj ustawienia domyślnego. W przypadku publikowania na platformie Azure za pomocą programu Visual Studio, parametr jest ustawiane automatycznie odpowiednio. Wdrażanie programowe do klastrów hostowanych na platformie Azure ciąg połączenia jest zawsze "Service fabric: ImageStore". Chociaż w razie wątpliwości, jego wartość zawsze można zweryfikować przez pobieranie manifestu klastra, [PowerShell](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclustermanifest), [.NET](https://msdn.microsoft.com/library/azure/mt161375.aspx), lub [REST](https://docs.microsoft.com/rest/api/servicefabric/get-a-cluster-manifest). Przetestuj zarówno lokalnie, i klastrów produkcyjnych należy skonfigurować zawsze używać tego dostawcy usługi Store obrazów.

### <a name="next-steps"></a>Kolejne kroki
[Wdrażanie i usunąć aplikacje przy użyciu programu PowerShell][10]

<!--Image references-->
[img_is]: ./media/service-fabric-image-store-connection-string/image_store_service.png
[img_cm]: ./media/service-fabric-image-store-connection-string/cluster_manifest.png

[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-cluster-creation-via-portal.md
[12]: service-fabric-package-apps.md#create-an-sfpkg
