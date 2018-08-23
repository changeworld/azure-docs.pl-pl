---
title: Hostowania statycznej witryny internetowej w usłudze Azure Storage (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Usługa Azure Storage oferuje teraz statycznej witryny internetowej hostingu (wersja zapoznawcza), zapewniając ekonomiczne i skalowalne rozwiązanie do hostowania nowoczesnych aplikacji sieci web.
services: storage
author: MichaelHauss
ms.service: storage
ms.topic: article
ms.date: 08/17/18
ms.author: mihauss
ms.component: blobs
ms.openlocfilehash: 65a1cd85baf18ac1f0d193e7e6d6c3139919fb59
ms.sourcegitcommit: a62cbb539c056fe9fcd5108d0b63487bd149d5c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2018
ms.locfileid: "42617401"
---
# <a name="static-website-hosting-in-azure-storage-preview"></a>Hostowania statycznej witryny internetowej w usłudze Azure Storage (wersja zapoznawcza)
Usługa Azure Storage oferuje teraz statycznej witryny internetowej hostingu (wersja zapoznawcza), dzięki czemu można wdrażać ekonomiczne i skalowalne nowoczesnych aplikacji sieci web na platformie Azure. W statycznej witryny internetowej stron sieci Web zawierać zawartość statyczną i JavaScript lub inny kod po stronie klienta. Z drugiej strony dynamicznych witryn sieci Web są zależne od kodu po stronie serwera i może być hostowana przy użyciu [Azure Web Apps](/azure/app-service/app-service-web-overview).

Jak wdrożeń zmiana kierunku modeli elastyczne i niedrogie rozwiązanie zdolności do dostarczania zawartości sieci web bez konieczności zarządzania serwerem jest krytyczny. Wprowadzenie hostowania statycznej witryny internetowej w usłudze Azure Storage sprawia, że jest to możliwe, włączania funkcji zaawansowanych zaplecza za pomocą architektury bezserwerowe, wykorzystując [usługi Azure Functions](/azure/azure-functions/functions-overview) i inne usługi PaaS.

## <a name="how-does-it-work"></a>Jak to działa?
Po włączeniu statycznych witryn internetowych na swoim koncie magazynu nowy punkt końcowy usługi sieci web jest tworzony w postaci `<account-name>.<zone-name>.web.core.windows.net`.

Punkt końcowy usługi sieci web zawsze umożliwia anonimowy dostęp do odczytu, zwraca sformatowany strony HTML w odpowiedzi na błędy usługi i pozwala tylko obiekt operacji odczytu. Punkt końcowy usługi sieci web zwraca dokument indeksu w wymaganym katalogu dla katalogu głównego i wszystkich podkatalogach. W przypadku usługi storage zwróci błąd 404, punkt końcowy sieci web zwraca dokument błędów niestandardowych, jeśli zostały one skonfigurowane.

Zawartości statycznej witryny sieci Web jest hostowana w specjalnych kontener o nazwie "$web". Jako część procesu włączania "$web" jest tworzony automatycznie, jeśli jeszcze nie istnieje. Zawartość "$web" są dostępne w katalogu głównym konta przy użyciu punktu końcowego sieci web. Na przykład `https://contoso.z4.web.core.windows.net/` zwraca indeks dokument został skonfigurowany dla witryny sieci Web, jeśli dokument o tej nazwie istnieje w katalogu głównym $web.

Podczas przekazywania zawartości do witryny sieci Web, użyj punktu końcowego magazynu obiektów blob. Aby przekazać obiekt blob o nazwie "image.jpg", które są dostępne w katalogu głównym konta Użyj następującego adresu URL `https://contoso.blob.core.windows.net/$web/image.jpg`. Przekazany obraz mogą być wyświetlane w przeglądarce sieci web w odpowiedniej punkcie końcowym sieci web `https://contoso.z4.web.core.windows.net/image.jpg`.


## <a name="custom-domain-names"></a>Niestandardowe nazwy domen
Domenę niestandardową można użyć do hostowania zawartości sieci web. Aby to zrobić, postępuj zgodnie ze wskazówkami w [Konfigurowanie niestandardowej nazwy domeny dla konta usługi Azure Storage](storage-custom-domain-name.md). Aby uzyskać dostęp do witryny sieci Web hostowanych na niestandardowej nazwy domeny za pośrednictwem protokołu HTTPS, zobacz [dostęp do obiektów blob z zastosowaniem domen niestandardowych przy użyciu protokołu HTTPS przy użyciu usługi Azure CDN](storage-https-custom-domain-cdn.md). Wskaż sieci CDN punktu końcowego sieci web, w przeciwieństwie do punktu końcowego obiektu blob i pamiętaj, że konfiguracja sieci CDN nie jest realizowane natychmiast, więc może być konieczne odczekanie kilku minut, zanim Twoja zawartość jest widoczna.

## <a name="pricing-and-billing"></a>Cennik i rozliczenia
Podano hostowania statycznej witryny internetowej bez ponoszenia dodatkowych kosztów. Aby uzyskać szczegółowe informacje na temat cen usługi Azure Blob Storage, zapoznaj się [stronie cennika usługi Azure Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="quickstart"></a>Szybki start
### <a name="azure-portal"></a>Azure Portal
Jeśli jeszcze nie, [Tworzenie konta magazynu GPv2](../common/storage-quickstart-create-account.md) aby rozpocząć, hostowanie aplikacji sieci web, można skonfigurować funkcję przy użyciu witryny Azure Portal i kliknij pozycję "Statyczna witryna internetowa (wersja zapoznawcza)" w obszarze "Settings" w lewym pasku nawigacyjnym. Kliknij pozycję "Włączone", a następnie wprowadź nazwę dokumentu indeksu i (opcjonalnie) ścieżka dokumentu błędu niestandardowego.

![](media/storage-blob-static-website/storage-blob-static-website-portal-config.PNG)

Twoje zasoby sieci web należy przekazać do kontenera "$web", który został utworzony jako część włączenie statycznej witryny internetowej. Można to zrobić bezpośrednio w witrynie Azure Portal lub korzystać z zalet [Eksploratora usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) przekazywania całego katalogu struktury. Upewnij się uwzględnić dokument indeksu o nazwie, które zostały skonfigurowane. W tym przykładzie nazwa dokumentu jest "index.html".

> [!NOTE]
> Nazwa dokumentu jest uwzględniana wielkość liter i w związku z tym musi dokładnie pasować do nazwy pliku w magazynie.

Na koniec przejdź do punktu końcowego usługi sieci web do testowania witryny sieci Web.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Zainstaluj rozszerzenie w wersji zapoznawczej magazynu:

```azurecli-interactive
az extension add --name storage-preview
```
Włącz tę funkcję:

```azurecli-interactive
az storage blob service-properties update --account-name <account-name> --static-website --404-document <error-doc-name> --index-document <index-doc-name>
```
Zapytanie adresu URL punktu końcowego sieci web:

```azurecli-interactive
az storage account show -n <account-name> -g <resource-group> --query "primaryEndpoints.web" --output tsv
```

Obiekty można przekazać do kontenera $web:

```azurecli-interactive
az storage blob upload-batch -s deploy -d $web --account-name <account-name>
```

## <a name="faq"></a>Często zadawane pytania
**Statycznej witryny sieci Web jest dostępna dla wszystkich typów kont magazynu?**  
Nie, hostowania statycznej witryny internetowej jest dostępna tylko na kontach magazynu w warstwie standardowa GPv2.

**Czy magazynu w sieci Wirtualnej i reguł zapory, obsługiwane na nowy punkt końcowy sieci web?**  
Tak, nowy punkt końcowy web przestrzegają zasad sieci Wirtualnej i zapory, skonfigurowany dla konta magazynu.

**Punkt końcowy sieci web jest uwzględniana wielkość liter?**  
Tak, punkt końcowy sieci web jest uwzględniana wielkość liter, podobnie jak punkt końcowy obiektu blob. 

## <a name="next-steps"></a>Kolejne kroki
* [Dostęp do obiektów blob z zastosowaniem domen niestandardowych przy użyciu protokołu HTTPS przy użyciu usługi Azure CDN](storage-https-custom-domain-cdn.md)
* [Konfigurowanie niestandardowej nazwy domeny dla punktu końcowego usługi blob Storage lub sieci web](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure Web Apps](/azure/app-service/app-service-web-overview)
* [Utwórz swoją pierwszą aplikację sieci web bez użycia serwera](https://aka.ms/static-serverless-webapp)
