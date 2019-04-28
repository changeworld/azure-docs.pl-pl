---
title: Hostowania statycznej witryny internetowej w usłudze Azure Storage
description: Usługa Azure Storage statycznej witryny internetowej hostingu, zapewniając ekonomiczne i skalowalne rozwiązanie do hostowania nowoczesnych aplikacji sieci web.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/25/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 67d3dcad4ec73ee09ec40282b2fbdea945daefe4
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122680"
---
# <a name="static-website-hosting-in-azure-storage"></a>Hostowania statycznej witryny internetowej w usłudze Azure Storage
Konta magazynu GPv2 platformy Azure umożliwiają obsługę zawartości statycznej (HTML, CSS, JavaScript i plików obrazów) bezpośrednio z kontenera magazynu o nazwie *$web*. Korzystając z zalet hosting w usłudze Azure Storage pozwala na używanie architektur bez użycia serwera, w tym [usługi Azure Functions](/azure/azure-functions/functions-overview) i inne usługi PaaS.

W przeciwieństwie do hostowania statycznej witryny internetowej, dynamicznych witryn, które są zależne od kodu po stronie serwera są najlepiej hostowane przy użyciu [usługi Azure App Service](/azure/app-service/overview).

## <a name="how-does-it-work"></a>Jak to działa?
Po włączeniu statycznej witryny internetowej hostingu na swoim koncie magazynu, wybierz nazwę pliku domyślnego i opcjonalnie podaj ścieżkę do niestandardowa strona 404. Ponieważ ta funkcja jest włączona, kontener o nazwie *$web* jest tworzony, jeśli jeszcze nie istnieje.

Pliki *$web* kontenera są:

- obsługiwane za pośrednictwem żądań dostępu anonimowego
- dostępne tylko za pośrednictwem operacji odczytu obiektu
- wielkość liter
- dostępne w publicznej sieci Web zgodnie z tego wzorca:
    - `https://<ACCOUNT_NAME>.<ZONE_NAME>.web.core.windows.net/<FILE_NAME>`
- dostępne za pośrednictwem punktu końcowego magazynu obiektów Blob, zgodnie z tego wzorca:
    - `https://<ACCOUNT_NAME>.blob.core.windows.net/$web/<FILE_NAME>`

Punkt końcowy usługi Blob storage służy do przekazywania plików. Na przykład plik przekazany do tej lokalizacji:

```bash
https://contoso.blob.core.windows.net/$web/image.png
```

jest dostępny w przeglądarce w lokalizacji, w następujący sposób:

```bash
https://contoso.z4.web.core.windows.net/image.png
```

Wybrane domyślnej nazwy pliku jest używana w katalogu głównym i wszystkich podkatalogach podczas nie podano nazwy pliku. Jeśli serwer zwraca błąd 404 — nie zostanie określona ścieżka dokumentu błędu, domyślna strona 404 jest zwracana do użytkownika.

> [!NOTE]
> Domyślny poziom dostępu publicznego dla plików jest prywatny. Ponieważ pliki są udostępniane za pośrednictwem żądań dostępu anonimowego, to ustawienie jest ignorowane. Brak publicznego dostępu do wszystkich plików i uprawnień RBAC są ignorowane.

## <a name="cdn-and-ssl-support"></a>Obsługa sieci CDN i protokołu SSL

Zapewnienie swoje statycznej witryny sieci Web pliki dostępne za pośrednictwem protokołu HTTPS, zobacz [dostęp do obiektów blob z zastosowaniem domen niestandardowych przy użyciu protokołu HTTPS przy użyciu usługi Azure CDN](storage-https-custom-domain-cdn.md). W ramach tego procesu, musisz *punktu sieci CDN w punkcie końcowym sieci web* w przeciwieństwie do punktu końcowego obiektu blob. Może być konieczne Poczekaj kilka minut, zanim zawartość jest widoczna, zgodnie z konfiguracją sieci CDN nie jest wykonywane natychmiast.

Po zaktualizowaniu statycznej witryny internetowej, należy wyczyścić buforowanej zawartości na serwerach granicznych usługi CDN według przeczyszczanie punktu końcowego usługi CDN. Aby uzyskać więcej informacji, zobacz [Przeczyszczanie punktu końcowego usługi Azure CDN](../../cdn/cdn-purge-endpoint.md).

## <a name="custom-domain-names"></a>Niestandardowe nazwy domen

Możesz [Konfigurowanie niestandardowej nazwy domeny dla konta usługi Azure Storage](storage-custom-domain-name.md) udostępnić statycznej witryny internetowej przy użyciu domeny niestandardowej. Dla przyjrzeć się hosting domeny na [platformy Azure, zobacz Hostuj swoją domenę, w usłudze Azure DNS](../../dns/dns-delegate-domain-azure-dns.md).

## <a name="pricing"></a>Cennik
Podano hostowania statycznej witryny internetowej bez ponoszenia dodatkowych kosztów. Aby uzyskać szczegółowe informacje na temat cen usługi Azure Blob Storage, zapoznaj się [stronie cennika usługi Azure Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="quickstart"></a>Szybki start

### <a name="azure-portal"></a>Azure Portal
Zacznij od otwarcia witryny Azure portal pod https://portal.azure.com i wykonaj następujące czynności na swoim koncie magazynu GPv2:

1. Kliknij pozycję **ustawienia**
2. Kliknij pozycję **statycznej witryny internetowej**
3. Wprowadź *nazwa dokumentu indeksu*. (Jest wspólną wartość *index.html)*
4. Opcjonalnie wprowadź *ścieżka dokumentu błędu* do niestandardowa strona 404. (Jest wspólną wartość *404.html)*

![](media/storage-blob-static-website/storage-blob-static-website-portal-config.PNG)

Następnie przekaż zasoby do *$web* kontenera w witrynie Azure portal lub za pomocą [Eksploratora usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) do przekazania całej katalogów. Pamiętaj dołączyć plik, który odpowiada *nazwa dokumentu indeksu* wybranego podczas włączania funkcji.

Na koniec przejdź do punktu końcowego usługi sieci web do testowania witryny sieci Web.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Zainstaluj rozszerzenie w wersji zapoznawczej magazynu:

```azurecli-interactive
az extension add --name storage-preview
```
W przypadku wielu subskrypcji należy ustawić do subskrypcji konto magazynu GPv2, którego chcesz włączyć interfejs wiersza polecenia:

```azurecli-interactive
az account set --subscription <SUBSCRIPTION_ID>
```
Należy włączyć funkcję. Upewnij się zamienić wszystkie wartości symboli zastępczych, razem z nawiasami własnymi wartościami:

```azurecli-interactive
az storage blob service-properties update --account-name <ACCOUNT_NAME> --static-website --404-document <ERROR_DOCUMENT_NAME> --index-document <INDEX_DOCUMENT_NAME>
```
Zapytanie adresu URL punktu końcowego sieci web:

```azurecli-interactive
az storage account show -n <ACCOUNT_NAME> -g <RESOURCE_GROUP> --query "primaryEndpoints.web" --output tsv
```

Przekazywanie obiektów *$web* kontenera z katalogu źródłowego. Pamiętaj prawidłowo ucieczki odwołanie do *$web* kontenera w poleceniu. Na przykład, jeśli używasz interfejsu wiersza polecenia platformy Azure w usłudze CloudShell w witrynie Azure portal, ucieczki *$web* kontenera, jak pokazano:

```azurecli-interactive
az storage blob upload-batch -s <SOURCE_PATH> -d \$web --account-name <ACCOUNT_NAME>
```

## <a name="deployment"></a>Wdrożenie

Dostępne metody wdrażania zawartości do kontenera magazynu są następujące:

- [Narzędzie AzCopy](../common/storage-use-azcopy.md)
- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
- [Potoki usługi Azure](https://azure.microsoft.com/services/devops/pipelines/)
- [Rozszerzenie programu Visual Studio Code](https://code.visualstudio.com/tutorials/static-website/getting-started)

We wszystkich przypadkach, upewnij się, należy skopiować pliki *$web* kontenera.

## <a name="metrics"></a>Metryki

Aby włączyć metryki na stronach statycznej witryny internetowej, kliknij **ustawienia** > **monitorowanie** > **metryki**.

Dane metryk są generowane przez podłączenie do różnych metryk interfejsów API. Portal zawiera tylko składowe interfejsu API, używany w danym przedziale czasu, aby tylko skoncentrowane na elementach członkowskich, które zwracają dane. Aby upewnić się, że jesteś w stanie wybrać niezbędne składowej interfejsu API, pierwszym krokiem jest rozwiń przedział czasu.

Kliknij przycisk przedział czasu, a następnie wybierz **ostatnie 24 godziny** a następnie kliknij przycisk **Zastosuj**

![Zakres czasu metryki statycznych witryn internetowych usługi Azure Storage](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

Następnie wybierz pozycję **Blob** z *Namespace* listy rozwijanej.

![Metryki statycznych witryn internetowych usługi Azure Storage przestrzeni nazw](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

Następnie wybierz pozycję **ruch wychodzący** metryki.

![Metryki metryki statycznych witryn internetowych w usłudze Azure magazynu](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

Wybierz **suma** z *agregacji* selektora.

![Azure Storage statycznych witryn internetowych metryki agregacji](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

Następnie kliknij przycisk **Dodaj filtr** przycisk, a następnie wybierz **Nazwa interfejsu API** z *właściwość* selektora.

![Nazwa metryki interfejsu API statycznych witryn internetowych w usłudze Azure Storage](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

Na koniec zaznacz pole wyboru obok pozycji **GetWebContent** w *wartości* selektor do wypełniania raportu metryk.

![Metryki statycznych witryn internetowych usługi Azure Storage GetWebContent](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

Po włączeniu ruchu statystyki dotyczące plików w *$web* kontenera są zgłaszane w pulpit nawigacyjny metryk.

## <a name="faq"></a>Często zadawane pytania

**Funkcja statycznej witryny sieci Web jest dostępna dla wszystkich typów kont magazynu?**  
Nie, hostowania statycznej witryny internetowej jest dostępna tylko na kontach magazynu w warstwie standardowa GPv2.

**Czy magazynu w sieci Wirtualnej i reguł zapory, obsługiwane na nowy punkt końcowy sieci web?**  
Tak, nowy punkt końcowy web przestrzegają zasad sieci Wirtualnej i zapory, skonfigurowany dla konta magazynu.

**Punkt końcowy sieci web jest uwzględniana wielkość liter?**  
Tak, punkt końcowy sieci web jest rozróżniana wielkość liter, podobnie jak punkt końcowy obiektu blob. 

## <a name="next-steps"></a>Kolejne kroki
* [Uzyskiwanie dostępu do obiektów blob z zastosowaniem domen niestandardowych przy użyciu protokołu HTTPS za pomocą sieci Azure CDN](storage-https-custom-domain-cdn.md)
* [Konfigurowanie niestandardowej nazwy domeny dla punktu końcowego usługi blob Storage lub sieci web](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [Utwórz swoją pierwszą aplikację sieci web bez użycia serwera](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Samouczek: Hostuj swoją domenę, w usłudze Azure DNS](../../dns/dns-delegate-domain-azure-dns.md)
