---
title: Ciąg połączenia magazynu obrazów sieci szkieletowej usługi Azure
description: Dowiedz się więcej o ciągu połączenia magazynu obrazów, w tym jego zastosowaniach i aplikacjach do klastra sieci szkieletowej usług.
author: alexwun
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: alexwun
ms.openlocfilehash: c3395248188c2a16736cfc8cea262fe163a6944b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645671"
---
# <a name="understand-the-imagestoreconnectionstring-setting"></a>Omówienie ustawienia ImageStoreConnectionString

W niektórych z naszej dokumentacji pokrótce wspominamy o istnieniu parametru "ImageStoreConnectionString", nie opisując, co to naprawdę oznacza. I po przejściu przez artykuł, takich jak [Wdrażanie i usuwanie aplikacji za pomocą programu PowerShell][10], wygląda na to, wszystko, co robisz, to skopiować / wkleić wartość, jak pokazano w manifeście klastra klastra klastra docelowego. Więc ustawienie musi być konfigurowalne dla klastra, ale podczas tworzenia klastra za pośrednictwem [witryny Azure portal,][11]nie ma opcji, aby skonfigurować to ustawienie i zawsze jest "fabric:ImageStore". Jaki jest zatem cel tego ustawienia?

![Manifest klastra][img_cm]

Usługa Service Fabric rozpoczęła się jako platforma do wewnętrznego wykorzystania firmy Microsoft przez wiele różnych zespołów, więc niektóre jej aspekty są wysoce konfigurowalne - "Image Store" jest jednym z takich aspektów. Zasadniczo magazyn obrazów jest podłączanym repozytorium do przechowywania pakietów aplikacji. Gdy aplikacja jest wdrażana w węźle w klastrze, ten węzeł pobiera zawartość pakietu aplikacji z Magazynu obrazów. ImageStoreConnectionString jest ustawieniem, które zawiera wszystkie informacje niezbędne dla klientów i węzłów, aby znaleźć poprawny magazyn obrazów dla danego klastra.

Obecnie istnieją trzy możliwe rodzaje dostawców magazynu obrazów i ich odpowiednie parametry połączenia są następujące:

1. Usługa sklepu z obrazami: "fabric:ImageStore"

2. System plików: "file:[ścieżka systemu plików]"

3. Usługa Azure Storage: "xstore:DefaultEndpointsProtocol=https; AccountName=[...]; AccountKey=[...]; Kontener=[...]"

Typ dostawcy używany w produkcji jest usługa magazynu obrazów, która jest stanową utrwalone usługi systemowej, które można zobaczyć z Eksploratora sieci szkieletowej usług. 

![Usługa magazynu obrazów][img_is]

Hostowanie magazynu obrazów w usłudze systemowej w samym klastrze eliminuje zależności zewnętrzne dla repozytorium pakietów i daje nam większą kontrolę nad lokalizacjami magazynu. Przyszłe ulepszenia wokół magazynu obrazów mogą najpierw kierować reklamy na dostawcę magazynu obrazów, jeśli nie wyłącznie. Parametry połączenia dla dostawcy usługi magazynu obrazów nie ma żadnych unikatowych informacji, ponieważ klient jest już połączony z klastrem docelowym. Klient musi tylko wiedzieć, że protokoły przeznaczone dla usługi systemowej powinny być używane.

Dostawca systemu plików jest używany zamiast usługi magazynu obrazów dla lokalnych klastrów jednokojowych podczas tworzenia, aby nieco szybciej uruchamiać klaster. Różnica jest zazwyczaj mała, ale jest to przydatna optymalizacja dla większości ludzi podczas rozwoju. Jest możliwe do wdrożenia lokalnego klastra jednokojowego z innymi typami dostawcy magazynu, jak również, ale zwykle nie ma powodu, aby to zrobić, ponieważ przepływ pracy rozwijać/test pozostaje taka sama, niezależnie od dostawcy. Dostawca usługi Azure Storage istnieje tylko dla starszej obsługi starych klastrów wdrożonych przed dostawcą usługi magazynu obrazów został wprowadzony.

Ponadto dostawca systemu plików ani dostawca usługi Azure Storage nie powinien być używany jako metoda udostępniania magazynu obrazów między wieloma klastrami — spowoduje to uszkodzenie danych konfiguracji klastra, ponieważ każdy klaster może zapisywać w obrazie dane powodujące konflikty. Sklepie. Aby udostępnić aprowizacji pakietów aplikacji między wieloma klastrami, należy użyć plików [sfpkg][12] zamiast tego, które mogą być przekazywane do dowolnego magazynu zewnętrznego z pobrania identyfikatora URI.

Tak więc, podczas gdy ImageStoreConnectionString jest konfigurowalny, wystarczy użyć ustawienia domyślnego. Podczas publikowania na platformie Azure za pośrednictwem programu Visual Studio parametr jest automatycznie ustawiany odpowiednio dla Ciebie. W przypadku wdrażania programowego w klastrach hostowanych na platformie Azure parametry połączenia są zawsze "fabric:ImageStore". Chociaż w razie wątpliwości, jego wartość zawsze można zweryfikować, pobierając manifest klastra przez [program PowerShell](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclustermanifest), [.NET](https://msdn.microsoft.com/library/azure/mt161375.aspx)lub [REST](https://docs.microsoft.com/rest/api/servicefabric/get-a-cluster-manifest). Klastry testowe i produkcyjne lokalne powinny być zawsze skonfigurowane do używania dostawcy usługi magazynu obrazów.

### <a name="next-steps"></a>Następne kroki
[Wdrażanie i usuwanie aplikacji przy użyciu programu PowerShell][10]

<!--Image references-->
[img_is]: ./media/service-fabric-image-store-connection-string/image_store_service.png
[img_cm]: ./media/service-fabric-image-store-connection-string/cluster_manifest.png

[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-cluster-creation-via-portal.md
[12]: service-fabric-package-apps.md#create-an-sfpkg
