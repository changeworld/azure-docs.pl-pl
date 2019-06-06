---
title: Hostowania statycznej witryny internetowej w usłudze Azure Storage
description: Usługa Azure Storage statycznej witryny internetowej hostingu, zapewniając ekonomiczne i skalowalne rozwiązanie do hostowania nowoczesnych aplikacji sieci web.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.author: normesta
ms.reviewer: seguler
ms.date: 05/29/2019
ms.subservice: blobs
ms.openlocfilehash: 36cc8cebdb567cb9650ad1ad3baf72a0b5478247
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427959"
---
# <a name="static-website-hosting-in-azure-storage"></a>Hostowania statycznej witryny internetowej w usłudze Azure Storage

Można udostępniać zawartość statyczną (HTML, CSS, JavaScript i plików obrazów) bezpośrednio z kontenera magazynu o nazwie *$web*. Hosting zawartości w usłudze Azure Storage pozwala na użycie architektur bez użycia serwera, które obejmują [usługi Azure Functions](/azure/azure-functions/functions-overview) i inne platformy jako usługi (PaaS).

> [!NOTE]
> Jeśli witryna jest zależna od kodu po stronie serwera, użyj [usługi Azure App Service](/azure/app-service/overview) zamiast tego.

## <a name="setting-up-a-static-website"></a>Definiowanie statycznej witryny internetowej

Hostowania statycznej witryny internetowej jest funkcją, które należy włączyć na koncie magazynu.

Aby włączyć hostowania statycznych witryn internetowych, wybierz nazwę pliku domyślnego, a następnie opcjonalnie podaj ścieżkę do niestandardowa strona 404. Jeśli kontener magazynu obiektów blob o nazwie **$web** jeszcze nie istnieje na koncie jednego zostanie utworzony. Dodaj pliki witryny do tego kontenera.

Aby uzyskać instrukcje, zobacz [hostowania statycznej witryny internetowej w usłudze Azure Storage](storage-blob-static-website-how-to.md).

![Metryki metryki statycznych witryn internetowych w usłudze Azure magazynu](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

Pliki **$web** kontenera jest rozróżniana wielkość liter, w dostarczanych za pośrednictwem żądań dostępu anonimowego i są dostępne wyłącznie za pośrednictwem operacji odczytu.

## <a name="uploading-content"></a>Przekazywanie zawartości

Można użyć dowolnego z tych narzędzi, aby przekazać zawartość do **$web** kontenera:

> [!div class="checklist"]
> * [Interfejs wiersza polecenia platformy Azure](storage-blob-static-website-how-to.md#cli)
> * [Moduł programu Azure PowerShell](storage-blob-static-website-how-to.md#powershell)
> * [Narzędzie AzCopy](../common/storage-use-azcopy-v10.md)
> * [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
> * [Potoki usługi Azure](https://azure.microsoft.com/services/devops/pipelines/)
> * [Rozszerzenie programu Visual Studio Code](https://code.visualstudio.com/tutorials/static-website/getting-started)

## <a name="viewing-content"></a>Wyświetlanie zawartości

Użytkownicy mogą wyświetlać zawartość witryny z poziomu przeglądarki przy użyciu publicznego adresu URL witryny sieci Web. Adres URL można znaleźć przy użyciu witryny Azure portal, interfejsu wiersza polecenia platformy Azure lub programu PowerShell. Użyj tej tabeli jako wskazówki.

|Narzędzie| Wskazówki |
|----|----|
|**Azure Portal** | [Adres URL witryny sieci Web można znaleźć przy użyciu witryny Azure portal](storage-blob-static-website-how-to.md#portal-find-url) |
|**Interfejs wiersza polecenia platformy Azure** | [Adres URL witryny sieci Web można znaleźć przy użyciu wiersza polecenia platformy Azure](storage-blob-static-website-how-to.md#cli-find-url) |
|**Moduł programu Azure PowerShell** | [Adres URL witryny sieci Web można znaleźć przy użyciu programu PowerShell](storage-blob-static-website-how-to.md#powershell-find-url) |

Adres URL witryny zawiera kodu regionu. Na przykład adres URL `https://contosoblobaccount.z22.web.core.windows.net/` zawiera kod regionalnych `z22`.

Gdy kod musi pozostać adres URL, jest tylko do użytku wewnętrznego i nie będą musieli używać ten kod w inny sposób.

Dokument indeksu, który określasz, że po włączeniu hostowania statycznej witryny internetowej, jest wyświetlany, gdy użytkownicy Otwórz witrynę i określać określonego pliku (na przykład: `https://contosoblobaccount.z22.web.core.windows.net`).  

Jeśli serwer zwraca błąd 404, a nie określono dokumentu błędu po włączeniu witryny sieci Web, domyślna strona 404 jest zwracana do użytkownika.

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>Wpływ ustawienia poziom dostępu publicznego do kontenera sieci web

Możesz zmodyfikować poziom dostępu publicznego **$web** kontenera, ale nie ma wpływu na punkt końcowy głównej statycznej witryny internetowej, ponieważ te pliki są udostępniane za pośrednictwem żądań dostępu anonimowego. Oznacza to, publiczny (dostęp tylko do odczytu) do wszystkich plików.

Poniższy zrzut ekranu przedstawia ustawienie poziomie dostępu publicznego w witrynie Azure portal:

![Zrzut ekranu przedstawiający sposób Ustaw poziom dostępu publicznego w portalu](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

Gdy nie występuje punktu końcowego podstawowego statycznej witryny internetowej, zmień poziom dostępu publicznego wpływu na punkt końcowy usługi blob podstawowego.

Na przykład, jeśli zmienisz poziom dostępu publicznego **$web** kontenera z **prywatny (bez dostępu anonimowego)** do **obiektów Blob (anonimowy dostęp do odczytu tylko dla obiektów blob.)** , a następnie poziom dostępu publicznego do punktu końcowego podstawowego statycznej witryny internetowej `https://contosoblobaccount.z22.web.core.windows.net/index.html` nie ulega zmianie.

Jednak jest publicznego dostępu do podstawowego punktu końcowego usługi obiektów blob `https://contosoblobaccount.blob.core.windows.net/$web/index.html` zmienić z prywatnej na publiczną. Teraz użytkownicy mogą otworzyć tego pliku przy użyciu jednej z tych dwóch punktów końcowych.

## <a name="content-delivery-network-cdn-and-secure-socket-layer-ssl-support"></a>Content Delivery Network (CDN) i obsługę protokołu Secure Socket Layer (SSL)

Aby udostępnić pliki statycznej witryny internetowej za pośrednictwem protokołu HTTPS i domeną niestandardową, na których, zobacz [dostęp do obiektów blob z zastosowaniem domen niestandardowych przy użyciu protokołu HTTPS przy użyciu usługi Azure CDN](storage-https-custom-domain-cdn.md). W ramach tego procesu, należy wskazać podstawowej sieci CDN *statycznej witryny internetowej* punktu końcowego, w przeciwieństwie do podstawowego *usługi blob service* punktu końcowego. Może być konieczne Poczekaj kilka minut, zanim zawartość jest widoczna, zgodnie z konfiguracją sieci CDN nie jest wykonywane natychmiast.

Po zaktualizowaniu statycznej witryny internetowej, należy wyczyścić buforowanej zawartości na serwerach granicznych usługi CDN według przeczyszczanie punktu końcowego usługi CDN. Aby uzyskać więcej informacji, zobacz [Przeczyszczanie punktu końcowego usługi Azure CDN](../../cdn/cdn-purge-endpoint.md).

> [!NOTE]
> Obsługiwany protokół HTTPS jest natywnie za pośrednictwem punktu końcowego sieci web konta, więc punkt końcowy sieci web jest dostępna za pośrednictwem protokołów HTTP i HTTPS. Jednak jeśli konto magazynu jest skonfigurowany do Wymaganie bezpiecznego transferu za pośrednictwem protokołu HTTPS, następnie użytkownicy mogą używać punktu końcowego HTTPS. Aby uzyskać więcej informacji, zobacz [Wymaganie bezpiecznego transferu w usłudze Azure Storage](../common/storage-require-secure-transfer.md).
>
> Użyj domen niestandardowych przy użyciu protokołu HTTPS wymaga użycia usługi Azure CDN w tej chwili.

## <a name="custom-domain-names"></a>Niestandardowe nazwy domen

Możesz udostępnić statycznej witryny internetowej przy użyciu domeny niestandardowej. Aby dowiedzieć się więcej, zobacz [Konfigurowanie niestandardowej nazwy domeny dla konta usługi Azure Storage](storage-custom-domain-name.md).

Aby przyjrzeć się hosting domeny na platformie Azure, zobacz [Hostuj swoją domenę, w usłudze Azure DNS](../../dns/dns-delegate-domain-azure-dns.md).

## <a name="pricing"></a>Cennik

Aby umożliwić hostowania statycznej witryny internetowej bezpłatnie. Są naliczane tylko w przypadku magazynu obiektów blob, który korzysta z witryny i koszty operacyjne. Aby uzyskać szczegółowe informacje na temat cen usługi Azure Blob Storage, zapoznaj się [stronie cennika usługi Azure Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="metrics"></a>Metryki

Można włączyć metryki na stronach statycznej witryny internetowej. Po włączeniu metryki ruchu statystyki dotyczące plików w **$web** kontenera są zgłaszane w pulpit nawigacyjny metryk.

Aby włączyć metryki na stronach statycznej witryny internetowej, zobacz [włączyć metryki na stronach statycznej witryny internetowej](storage-blob-static-website-how-to.md#metrics).

## <a name="next-steps"></a>Kolejne kroki

* [Hostowania statycznej witryny internetowej w usłudze Azure Storage](storage-blob-static-website-how-to.md)
* [Użyj usługi Azure CDN dostęp do obiektów blob z zastosowaniem domen niestandardowych przy użyciu protokołu HTTPS](storage-https-custom-domain-cdn.md)
* [Konfigurowanie niestandardowej nazwy domeny dla punktu końcowego usługi blob Storage lub sieci web](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Usługa Azure App Service](/azure/app-service/overview)
* [Utwórz swoją pierwszą aplikację sieci web bez użycia serwera](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Samouczek: Hostuj swoją domenę, w usłudze Azure DNS](../../dns/dns-delegate-domain-azure-dns.md)
