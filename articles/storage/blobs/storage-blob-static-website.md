---
title: Hostowanie statycznej witryny internetowej w usłudze Azure Storage
description: Hosting statycznej witryny usługi Azure Storage, zapewniający ekonomiczne, skalowalne rozwiązanie do obsługi nowoczesnych aplikacji sieci Web.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.author: normesta
ms.reviewer: dineshm
ms.date: 05/29/2019
ms.subservice: blobs
ms.openlocfilehash: 848fd89444281d82d6d0d1bfc4df15d499c09ee0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370495"
---
# <a name="static-website-hosting-in-azure-storage"></a>Hostowanie statycznej witryny internetowej w usłudze Azure Storage

Zawartość statyczną (HTML, CSS, JavaScript i pliki obrazów) można wyświetlać bezpośrednio z kontenera magazynu o nazwie *$web*. Hostowanie zawartości w usłudze Azure Storage umożliwia korzystanie z architektur bezserwerowych, które obejmują [usługi Azure Functions](/azure/azure-functions/functions-overview) i inne usługi platformy jako usługi (PaaS).

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

> [!NOTE]
> Jeśli witryna zależy od kodu po stronie serwera, użyj [usługi Azure App Service.](/azure/app-service/overview)

## <a name="setting-up-a-static-website"></a>Konfigurowanie statycznej witryny sieci Web

Statyczny hosting stron internetowych to funkcja, którą musisz włączyć na koncie pamięci masowej.

Aby włączyć statyczny hosting witryny sieci Web, wybierz nazwę pliku domyślnego, a następnie opcjonalnie podaj ścieżkę do niestandardowej strony 404. Jeśli kontener magazynu obiektów blob o nazwie **$web** jeszcze nie istnieje na koncie, jeden jest tworzony dla Ciebie. Dodaj pliki witryny do tego kontenera.

Aby uzyskać wskazówki krok po kroku, zobacz [Hostowanie statycznej witryny sieci Web w usłudze Azure Storage.](storage-blob-static-website-how-to.md)

![Metryka metryk statycznych witryn sieci Web usługi Azure Storage](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

W plikach w kontenerze **$web** rozróżniana jest wielkość liter, jest obsługiwana za pośrednictwem żądań dostępu anonimowego i jest dostępna tylko za pośrednictwem operacji odczytu.

## <a name="uploading-content"></a>Przesyłanie zawartości

Za pomocą dowolnego z tych narzędzi można przesyłać zawartość do kontenera **$web:**

> [!div class="checklist"]
> * [Interfejs wiersza polecenia platformy Azure](storage-blob-static-website-how-to.md#cli)
> * [Moduł programu Azure PowerShell](storage-blob-static-website-how-to.md#powershell)
> * [AzCopy (Polski)](../common/storage-use-azcopy-v10.md)
> * [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/)
> * [Potoki platformy Azure](https://azure.microsoft.com/services/devops/pipelines/)
> * [Rozszerzenie programu Visual Studio Code](/azure/javascript/tutorial-vscode-static-website-node-01)

## <a name="viewing-content"></a>Wyświetlanie zawartości

Użytkownicy mogą wyświetlać zawartość witryny z przeglądarki przy użyciu publicznego adresu URL witryny. Adres URL można znaleźć przy użyciu witryny Azure portal, interfejsu wiersza polecenia platformy Azure lub programu PowerShell. Użyj tej tabeli jako przewodnika.

|Narzędzie| Wskazówki |
|----|----|
|**Portal Azure** | [Znajdź adres URL witryny sieci Web za pomocą witryny Azure portal](storage-blob-static-website-how-to.md#portal-find-url) |
|**Interfejs wiersza polecenia platformy Azure** | [Znajdowanie adresu URL witryny sieci Web przy użyciu interfejsu wiersza polecenia platformy Azure](storage-blob-static-website-how-to.md#cli-find-url) |
|**Moduł programu Azure PowerShell** | [Znajdowanie adresu URL witryny sieci Web przy użyciu programu PowerShell](storage-blob-static-website-how-to.md#powershell-find-url) |

Adres URL witryny zawiera kod regionalny. Na przykład `https://contosoblobaccount.z22.web.core.windows.net/` adres URL `z22`zawiera kod regionalny .

Chociaż ten kod musi pozostać w adresie URL, jest tylko do użytku wewnętrznego i nie trzeba używać tego kodu w inny sposób.

Dokument indeksu określony po włączeniu statycznego hostingu witryny sieci Web pojawia się, gdy `https://contosoblobaccount.z22.web.core.windows.net`użytkownicy otwierają witrynę i nie określają określonego pliku (na przykład: ).  

Jeśli serwer zwraca błąd 404 i nie określono dokumentu błędu po włączeniu witryny sieci Web, domyślna strona 404 jest zwracana do użytkownika.

> [!NOTE]
> [CORS](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) nie jest obsługiwany w statycznej witrynie sieci Web.

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>Wpływ ustawienia poziomu dostępu publicznego kontenera sieci web

Można zmodyfikować poziom dostępu publicznego **kontenera $web,** ale nie ma to wpływu na podstawowy statyczny punkt końcowy witryny sieci Web, ponieważ te pliki są obsługiwane za pośrednictwem żądań dostępu anonimowego. Oznacza to publiczny (tylko do odczytu) dostęp do wszystkich plików.

Poniższy zrzut ekranu przedstawia ustawienie poziomu dostępu publicznego w witrynie Azure portal:

![Zrzut ekranu przedstawiający ustawianie poziomu dostępu publicznego w portalu](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

Chociaż nie ma to wpływu na podstawowy statyczny punkt końcowy witryny sieci Web, zmiana poziomu dostępu publicznego ma wpływ na podstawowy punkt końcowy usługi obiektu blob.

Na przykład jeśli zmienisz poziom dostępu publicznego **kontenera $web** z **Private (bez dostępu anonimowego)** na **obiekt Blob (anonimowy dostęp do odczytu tylko dla obiektów blob),** poziom publicznego dostępu do podstawowego statycznego punktu końcowego `https://contosoblobaccount.z22.web.core.windows.net/index.html` witryny sieci Web nie ulegnie zmianie.

Jednak publiczny dostęp do punktu końcowego `https://contosoblobaccount.blob.core.windows.net/$web/index.html` podstawowej usługi obiektu blob zmienia się z prywatnego na publiczny. Teraz użytkownicy mogą otworzyć ten plik przy użyciu jednego z tych dwóch punktów końcowych.

## <a name="mapping-a-custom-domain-to-a-static-website-url"></a>Mapowanie domeny niestandardowej na statyczny adres URL witryny sieci Web

Możesz udostępnić swoją statyczną stronę internetową za pośrednictwem domeny niestandardowej. 

Łatwiej jest włączyć dostęp HTTP dla domeny niestandardowej, ponieważ usługa Azure Storage natywnie ją obsługuje. Aby włączyć protokół HTTPS, musisz użyć usługi Azure CDN, ponieważ usługa Azure Storage nie obsługuje jeszcze natywnie protokołu HTTPS z domenami niestandardowymi. Zobacz [Mapowanie domeny niestandardowej do punktu końcowego usługi Azure Blob Storage, aby](storage-custom-domain-name.md) uzyskać wskazówki krok po kroku.

Jeśli konto magazynu jest skonfigurowane tak, aby [wymagało bezpiecznego transferu](../common/storage-require-secure-transfer.md) za pośrednictwem protokołu HTTPS, użytkownicy muszą użyć punktu końcowego HTTPS. 

> [!TIP]
> Rozważ hostowanie domeny na platformie Azure. Aby uzyskać więcej informacji, zobacz [Hostuj swoją domenę w usłudze Azure DNS](../../dns/dns-delegate-domain-azure-dns.md).

## <a name="adding-http-headers"></a>Dodawanie nagłówków HTTP

Nie ma możliwości skonfigurowania nagłówków jako części funkcji statycznej witryny sieci Web. Jednak można użyć usługi Azure CDN, aby dodać nagłówki i dołączyć (lub zastąpić) wartości nagłówka. Zobacz [Odwołanie do aparatu reguł standardowych dla usługi Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-standard-rules-engine-reference).

Jeśli chcesz używać nagłówków do kontrolowania buforowania, zobacz [Kontrolowanie zachowania buforowania usługi Azure CDN za pomocą reguł buforowania](https://docs.microsoft.com/azure/cdn/cdn-caching-rules).

## <a name="pricing"></a>Cennik

Możesz bezpłatnie włączyć hosting statycznej strony internetowej. Opłaty są naliczane tylko za magazyn obiektów blob, który wykorzystuje witryna i koszty operacyjne. Aby uzyskać więcej informacji na temat cen usługi Azure Blob Storage, zapoznaj się ze [stroną cennik usługi Azure Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="metrics"></a>Metryki

Możesz włączyć dane na statycznych stronach witryny. Po włączeniu metryki statystyki ruchu plików w kontenerze **$web** są zgłaszane na pulpicie nawigacyjnym metryk.

Aby włączyć dane na statycznych stronach witryny, zobacz [Włączanie danych na statycznych stronach witryny](storage-blob-static-website-how-to.md#metrics).

## <a name="next-steps"></a>Następne kroki

* [Hostowanie statycznej witryny sieci Web w usłudze Azure Storage](storage-blob-static-website-how-to.md)
* [Mapowanie domeny niestandardowej do punktu końcowego usługi Azure Blob Storage](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Usługa aplikacji platformy Azure](/azure/app-service/overview)
* [Zbuduj pierwszą bezserwerowa aplikację internetową](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Samouczek: hostowanie własnej domeny w usłudze Azure DNS](../../dns/dns-delegate-domain-azure-dns.md)
