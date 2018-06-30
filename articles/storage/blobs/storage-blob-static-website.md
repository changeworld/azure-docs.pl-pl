---
title: Statyczna witryna internetowa hosting w usłudze Azure Storage (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Usługa Azure Storage oferuje teraz statycznej witryny sieci Web obsługujący (wersja zapoznawcza), zapewniając ekonomiczne i skalowalne rozwiązanie do obsługi nowoczesnych aplikacji sieci web.
services: storage
author: MichaelHauss
manager: vamshik
ms.service: storage
ms.topic: article
ms.date: 06/26/18
ms.author: mihauss
ms.openlocfilehash: df1661b5fe7a2c0e37deef5259d6b5842ed6ee5e
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37131613"
---
# <a name="static-website-hosting-in-azure-storage-preview"></a>Statyczna witryna internetowa hosting w usłudze Azure Storage (wersja zapoznawcza)
Usługa Azure Storage oferuje teraz statycznej witryny sieci Web obsługujący (wersja zapoznawcza), dzięki któremu można wdrożyć ekonomiczne i skalowalne nowoczesnych aplikacji sieci web na platformie Azure. W statycznej witryny sieci Web stron sieci Web zawiera zawartość statyczna i JavaScript lub inny kod po stronie klienta. Z kolei dynamicznych witryn sieci Web są zależne od kodu po stronie serwera i może być obsługiwany przy użyciu [Azure Web Apps](/app-service/app-service-web-overview.md).

W wdrożeń przesunięcia kierunku modeli elastyczne i ekonomiczne, bardzo ważne jest możliwość dostarczania zawartości sieci web bez konieczności zarządzania serwerem. Wprowadzenie obsługi statycznej witryny sieci Web w usłudze Azure Storage jest to możliwe dzięki, włączanie funkcji zaawansowanych wewnętrznej bazy danych z architektur niekorzystającą wykorzystaniu [usługi Azure Functions](/azure-functions/functions-overview.md) i innych usług PaaS.

## <a name="how-does-it-work"></a>Jak to działa?
Po włączeniu statycznych witryn internetowych w koncie magazynu nowy punkt końcowy usługi sieci web jest tworzony w postaci `<account-name>.<zone-name>.web.core.windows.net`.

Zawsze punkt końcowy usługi sieci web umożliwia anonimowy dostęp do odczytu, zwraca sformatowany stron HTML w odpowiedzi na błędy usługi i pozwala tylko obiekt operacji odczytu. Punkt końcowy usługi sieci web zwraca w dokumencie indeksu w wymaganym katalogu głównego i wszystkich jego podkatalogach. Gdy Usługa magazynu zwraca błąd 404, punktu końcowego sieci web zwraca dokumentu błędów niestandardowych, jeśli została skonfigurowana.

Zawartości statycznej witryny sieci Web znajduje się w kontenerze specjalne o nazwie "$web". Jako część procesu włączania "$web" jest tworzony automatycznie, jeśli jeszcze nie istnieje. W katalogu głównym konta przy użyciu punktu końcowego sieci web można uzyskać dostępu do zawartości w "$web". Na przykład `https://contoso.z4.web.core.windows.net/` zwraca w dokumencie indeksu skonfigurowana dla witryny sieci Web, jeśli dokument o tej nazwie istnieje w katalogu głównym $web.

Podczas przekazywania zawartości do witryny sieci Web, użyj punktu końcowego magazynu obiektów blob. Do przekazania obiektu blob o nazwie "image.jpg" dostępny w głównym konta, użyj następującego adresu URL `https://contoso.blob.core.windows.net/$web/image.jpg`. Załadowanego obrazu można wyświetlić w przeglądarce sieci web na odpowiednich punktu końcowego sieci web `https://contoso.z4.web.core.windows.net/image.jpg`.


## <a name="custom-domain-names"></a>Niestandardowe nazwy domen
Domeny niestandardowej służy do obsługi zawartości sieci web. Aby to zrobić, postępuj zgodnie ze wskazówkami w [Konfigurowanie niestandardowej nazwy domeny dla konta magazynu Azure](storage-custom-domain-name.md). Aby uzyskać dostęp do witryny sieci Web hostowanej w lokalizacji niestandardowej nazwy domeny przy użyciu protokołu HTTPS, zobacz [dostęp do obiektów blob z domenami niestandardowymi za pośrednictwem protokołu HTTPS przy użyciu usługi Azure CDN](storage-https-custom-domain-cdn.md).

## <a name="pricing-and-billing"></a>Cennik i rozliczenia
Hosting statycznej witryny sieci Web są udostępniane bez ponoszenia dodatkowych kosztów. Aby uzyskać więcej informacji dotyczących cen magazynu obiektów Blob Azure, zapoznaj się [stronie cennika usługi magazynu obiektów Blob Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="quickstart"></a>Szybki start
### <a name="azure-portal"></a>Azure Portal
Aby uruchomić hosting aplikacji sieci web w usłudze Azure Storage, można skonfigurować funkcję przy użyciu portalu Azure i kliknij "Statycznej witryny sieci Web (wersja zapoznawcza)" w obszarze "Ustawienia" na pasku nawigacyjnym po lewej stronie. Kliknij pozycję "Włączone", a następnie wprowadź nazwę w dokumencie indeksu i (opcjonalnie) ścieżka dokumentu błędów niestandardowych.

![](media/storage-blob-static-website/storage-blob-static-website-portal-config.PNG)

Przekaż zasobów sieci web do kontenera "$web", który został utworzony jako część włączenie statycznej witryny sieci Web. Można to zrobić bezpośrednio w portalu Azure, lub możesz korzystać z [Eksploratora usługi Storage Azure](https://azure.microsoft.com/features/storage-explorer/) przekazać struktury cały katalog. Upewnij się uwzględnić w dokumencie indeksu o nazwie, które zostały skonfigurowane. W tym przykładzie nazwa dokumentu jest "index.html".

> [!NOTE]
> Nazwa dokumentu jest rozróżniana wielkość liter i dlatego musi dokładnie pasować do nazwy pliku w magazynie.

Na koniec przejdź do punktu końcowego sieci web do testowania witryny sieci Web.

## <a name="faq"></a>Często zadawane pytania
**Statycznych witryn sieci Web jest dostępna dla wszystkich typów kont magazynu?**  
Nie, hosting statycznej witryny sieci Web jest dostępna tylko na kontach magazynu w warstwie standardowa GPv2.

**Czy magazynu w sieci Wirtualnej i jego reguły zapory obsługiwane na nowy punkt końcowy sieci web?**  
Tak, nowy punkt końcowy sieci web przestrzega sieci Wirtualnej i reguł skonfigurowanych dla konta magazynu.

## <a name="next-steps"></a>Kolejne kroki
* [Dostęp do obiektów blob z domenami niestandardowymi za pośrednictwem protokołu HTTPS przy użyciu usługi Azure CDN](storage-https-custom-domain-cdn.md)
* [Konfigurowanie niestandardowej nazwy domeny dla punktu końcowego obiektu blob lub sieci web](storage-custom-domain-name.md)
* [Azure Functions](/azure-functions/functions-overview.md)
* [Azure Web Apps](/app-service/app-service-web-overview.md)
* [Utworzyć pierwszą aplikację niekorzystającą sieci web](https://aka.ms/static-serverless-webapp)
