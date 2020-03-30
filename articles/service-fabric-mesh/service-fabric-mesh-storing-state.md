---
title: Opcje magazynu o stanie w usłudze Azure Service Fabric Mesh
description: Dowiedz się więcej o niezawodnym przechowywaniu stanu w aplikacjach sieci szkieletowej usług uruchomionych w usłudze Azure Service Fabric Mesh.
author: dkkapur
ms.author: dekapur
ms.date: 11/27/2018
ms.topic: conceptual
ms.openlocfilehash: 0b0bd611fa86d155bb5bf2e07808e7365e28871c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259099"
---
# <a name="state-management-with-service-fabric"></a>Zarządzanie stanem za pomocą sieci szkieletowej usług

Usługa Sieci szkieletowej obsługuje wiele różnych opcji magazynu stanu. Aby zapoznać się z koncepcyjnym omówieniem wzorców zarządzania stanami i sieci szkieletowej usług, zobacz [Pojęcia sieci szkieletowej usług: Stan](/azure/service-fabric/service-fabric-concepts-state). Wszystkie te same pojęcia mają zastosowanie niezależnie od tego, czy usługi są uruchamiane wewnątrz lub na zewnątrz sieci szkieletowej usługi Mesh. 

Dzięki sieci szkieletowej usług Mesh można łatwo wdrożyć nową aplikację i połączyć ją z istniejącym magazynem danych hostowanym na platformie Azure. Oprócz korzystania z dowolnej zdalnej bazy danych istnieje kilka opcji przechowywania danych, w zależności od tego, czy usługa ma chęć magazynu lokalnego lub zdalnego. 

## <a name="volumes"></a>Woluminy

Kontenery często korzystają z dysków tymczasowych. Dyski tymczasowe są jednak efemeryczne, więc otrzymasz nowy dysk tymczasowy i utracisz informacje, gdy kontener ulegnie awarii. Jest również trudne do udostępniania informacji na dyskach tymczasowych z innymi kontenerami. Woluminy są katalogi, które są zainstalowane wewnątrz wystąpień kontenera, które można użyć do utrwalenia stanu. Woluminy zapewniają magazyn plików ogólnego przeznaczenia i umożliwiają odczytywanie/zapisywanie plików przy użyciu zwykłych interfejsów API plików we/wy dysku. Zasób woluminu opisuje sposób instalowania katalogu i który magazyn zapasowy do użycia. Do przechowywania danych można wybrać dysk magazynu plików platformy Azure lub woluminu sieci szkieletowej usług.

![Woluminy][image3]

### <a name="service-fabric-reliable-volume"></a>Niezawodna objętość sieci szkieletowej usług

Usługa Sieć szkieletowa Niezawodny wolumin jest sterownikiem woluminu platformy Docker używanym do instalowania woluminu lokalnego w kontenerze. Odczyty i zapisy są operacje lokalne i szybko. Dane są replikowane do węzłów pomocniczych, dzięki czemu są bardzo dostępne. Przełącznie awaryjne jest również szybkie. Gdy kontener ulega awarii, działa w sieci w sieci danych do węzła, który ma już kopię danych. Na przykład zobacz [Jak wdrożyć aplikację z niezawodnym woluminem sieci szkieletowej usług](service-fabric-mesh-howto-deploy-app-sfreliable-disk-volume.md).

### <a name="azure-files-volume"></a>Wolumin plików platformy Azure

Azure Files Volume to sterownik woluminu platformy Docker używany do instalowania udziału usługi Azure Files w kontenerze. Usługa Azure Files storage używa magazynu sieciowego, więc odczyty i zapisy odbywają się za pośrednictwem sieci. W porównaniu do niezawodnego woluminu sieci szkieletowej usług, usługa Azure Files storage jest mniej wydajna, ale zapewnia tańszą i w pełni niezawodną opcję danych. Na przykład zobacz [Jak wdrożyć aplikację z woluminem plików Azure](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat modelu aplikacji, zobacz [Zasoby sieci szkieletowej usług](service-fabric-mesh-service-fabric-resources.md)

[image3]: ./media/service-fabric-mesh-storing-state/volumes.png
