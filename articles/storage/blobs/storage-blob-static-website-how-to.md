---
title: Hostowania statycznej witryny internetowej w usłudze Azure Storage
description: Dowiedz się, jak udostępniać zawartość statyczną (HTML, CSS, JavaScript i plików obrazów) bezpośrednio w kontenerze na koncie usługi Azure Storage w konta GPv2.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.author: normesta
ms.date: 05/28/2019
ms.openlocfilehash: 61477767c59dd521e3f46db4445238a5a1ea759e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071437"
---
# <a name="host-a-static-website-in-azure-storage"></a>Hostowania statycznej witryny internetowej w usłudze Azure Storage

Może obsługiwać zawartość statyczną (HTML, CSS, JavaScript i plików obrazów) bezpośrednio w kontenerze na koncie usługi Azure Storage w konta GPv2. Aby dowiedzieć się więcej, zobacz [hostowania statycznej witryny internetowej w usłudze Azure Storage](storage-blob-static-website.md).

W tym artykule pokazano, jak włączyć hostowania statycznej witryny internetowej przy użyciu witryny Azure portal, interfejsu wiersza polecenia platformy Azure lub programu PowerShell.

<a id="portal" />

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Aby uzyskać samouczek krok po kroku, zobacz [samouczka: Hostowanie statycznej witryny internetowej w usłudze Blob Storage ](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host).

Po włączeniu hostowania statycznych witryn internetowych, można wyświetlić strony witryny z poziomu przeglądarki przy użyciu publicznego adresu URL witryny sieci Web.

<a id="portal-find-url" />

### <a name="find-the-website-url-by-using-the-azure-portal"></a>Adres URL witryny sieci Web można znaleźć przy użyciu witryny Azure portal

W okienku pojawi się obok strony Przegląd konta konta magazynu, wybierz **statyczna witryna internetowa**. Adres URL witryny jest wyświetlana w **podstawowego punktu końcowego** pola.

![Metryki metryki statycznych witryn internetowych w usłudze Azure magazynu](./media/storage-blob-static-website/storage-blob-static-website-url.png)

<a id="cli" />

## <a name="use-the-azure-cli"></a>Używanie interfejsu wiersza polecenia platformy Azure

Aby umożliwić hostowania statycznej witryny internetowej przy użyciu [interfejsu wiersza polecenia platformy Azure (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

1. Najpierw otwórz [usługi Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest), lub jeśli [zainstalowane](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) wiersza polecenia platformy Azure lokalnie, otwórz aplikacji konsoli poleceń, takich jak Windows PowerShell.

2. W oknie polecenia, która została otwarta należy zainstalować rozszerzenie wersji zapoznawczej magazynu.

   ```azurecli-interactive
   az extension add --name storage-preview
   ```

3. Jeśli Twoją tożsamość jest skojarzone z więcej niż jedną subskrypcję, wartość aktywnych subskrypcji do subskrypcji konto magazynu, który będzie hostował statycznej witryny internetowej.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Zastąp `<subscription-id>` wartość symbolu zastępczego z Identyfikatorem subskrypcji.

4. Włącz hostowania statycznej witryny internetowej.

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * Zastąp wartość symbolu zastępczego `<storage-account-name>` nazwą konta magazynu.

   * Zastąp `<error-document-name>` symbol zastępczy nazwy dokumentu błąd, która będzie wyświetlana użytkownikom, gdy przeglądarka zgłasza strony w witrynie, która nie istnieje.

   * Zastąp `<index-document-name>` zastępczego nazwa dokumentu indeksu. Ten dokument jest często "index.html".

5. Przekazywanie obiektów *$web* kontenera z katalogu źródłowego.

   > [!NOTE]
   > Jeśli używasz usługi Azure Cloud Shell, upewnij się dodać `\` znak ucieczki przy odwoływaniu się do `$web` kontenera (na przykład: `\$web`). Jeśli używasz lokalnej instalacji interfejsu wiersza polecenia platformy Azure, nie być konieczne użycie znaku ucieczki.

   W tym przykładzie przyjęto założenie, że używasz polecenia w sesji usługi Azure Cloud Shell.

   ```azurecli-interactive
   az storage blob upload-batch -s <source-path> -d \$web --account-name <storage-account-name>
   ```

   * Zastąp wartość symbolu zastępczego `<storage-account-name>` nazwą konta magazynu.

   * Zastąp `<source-path>` zastępczego ścieżkę do lokalizacji plików, które chcesz przekazać.

   > [!NOTE]
   > Jeśli używasz instalacji z wiersza polecenia platformy Azure, a następnie służy ścieżki do dowolnej lokalizacji na komputerze lokalnym (na przykład: `C:\myFolder`.
   >
   > Jeśli używasz usługi Azure Cloud Shell, musisz odwoływać się do udziału plików, który jest widoczny do usługi Cloud Shell. Ta lokalizacja może być udział plików chmury udostępnianie autonomiczną usługą Intune a istniejący udział plików, który można zainstalować z usługi Cloud Shell. Aby dowiedzieć się, jak to zrobić, zobacz [utrwalanie plików w usłudze Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage).

<a id="cli-find-url" />

### <a name="find-the-website-url-by-using-the-azure-cli"></a>Adres URL witryny sieci Web można znaleźć przy użyciu wiersza polecenia platformy Azure

Można wyświetlić zawartości z przeglądarki, za pomocą publicznego adresu URL witryny sieci Web.

Adres URL można znaleźć przy użyciu następującego polecenia:

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* Zastąp wartość symbolu zastępczego `<storage-account-name>` nazwą konta magazynu.

* Zastąp `<resource-group-name>` wartość symbolu zastępczego z nazwą grupy zasobów.

<a id="powershell" />

## <a name="use-powershell"></a>Korzystanie z programu PowerShell

Aby umożliwić hostowania statycznej witryny internetowej przy użyciu modułu Azure PowerShell.

1. Otwórz okno poleceń programu Windows PowerShell.

2. Sprawdź, czy Azure PowerShell w wersji Az modułu 0,7 lub nowszej.

   ```powershell
   Get-InstalledModule -Name Az -AllVersions | select Name,Version
   ```

   Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps).

3. Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Connect-AzAccount` i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

   ```powershell
   Connect-AzAccount
   ```

4. Jeśli Twoją tożsamość jest skojarzone z więcej niż jedną subskrypcję, wartość aktywnych subskrypcji do subskrypcji konto magazynu, który będzie hostował statycznej witryny internetowej.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Zastąp `<subscription-id>` wartość symbolu zastępczego z Identyfikatorem subskrypcji.

5. Pobierz kontekst konta magazynu, który definiuje konto magazynu, którego chcesz użyć.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Zastąp `<resource-group-name>` wartość symbolu zastępczego z nazwą grupy zasobów.

   * Zastąp wartość symbolu zastępczego `<storage-account-name>` nazwą konta magazynu.

6. Włącz hostowania statycznej witryny internetowej.

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * Zastąp `<error-document-name>` symbol zastępczy nazwy dokumentu błąd, która będzie wyświetlana użytkownikom, gdy przeglądarka zgłasza strony w witrynie, która nie istnieje.

   * Zastąp `<index-document-name>` zastępczego nazwa dokumentu indeksu. Ten dokument jest często "index.html".

7. Przekazywanie obiektów *$web* kontenera z katalogu źródłowego.

    ```powershell
    # upload a file
    set-AzStorageblobcontent -File "<path-to-file>" `
    -Container `$web `
    -Blob "<blob-name>" `
    -Context $ctx
     ```

   * Zastąp `<path-to-file>` pełną ścieżkę do pliku, który chcesz przekazać wartość zastępczą (na przykład: `C:\temp\index.html`).

   * Zastąp `<blob-name>` wartość symbolu zastępczego nazwą, której chcesz nadać wynikowy obiekt blob (na przykład: `index.html`).

<a id="powershell-find-url" />

### <a name="find-the-website-url-by-using-powershell"></a>Adres URL witryny sieci Web można znaleźć przy użyciu programu PowerShell

Można wyświetlić zawartości z przeglądarki, za pomocą publicznego adresu URL witryny sieci Web.

Adres URL można znaleźć przy użyciu następującego polecenia:

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* Zastąp `<resource-group-name>` wartość symbolu zastępczego z nazwą grupy zasobów.

* Zastąp wartość symbolu zastępczego `<storage-account-name>` nazwą konta magazynu.

<a id="metrics" />

## <a name="enable-metrics-on-static-website-pages"></a>Włącz metryki na stronach statycznej witryny internetowej

Po włączeniu metryki ruchu statystyki dotyczące plików w **$web** kontenera są zgłaszane w pulpit nawigacyjny metryk.

1. Kliknij pozycję **ustawienia** > **monitorowania** > **metryki**.

   Dane metryk są generowane przez podłączenie do różnych metryk interfejsów API. Portal zawiera tylko składowe interfejsu API, używany w danym przedziale czasu, aby tylko skoncentrowane na elementach członkowskich, które zwracają dane. Aby upewnić się, że jesteś w stanie wybrać niezbędne składowej interfejsu API, pierwszym krokiem jest rozwiń przedział czasu.

2. Kliknij przycisk przedział czasu, a następnie wybierz **ostatnie 24 godziny** a następnie kliknij przycisk **Zastosuj**.

   ![Zakres czasu metryki statycznych witryn internetowych usługi Azure Storage](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. Wybierz **Blob** z *Namespace* listy rozwijanej.

   ![Metryki statycznych witryn internetowych usługi Azure Storage przestrzeni nazw](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. Następnie wybierz pozycję **ruch wychodzący** metryki.

   ![Metryki metryki statycznych witryn internetowych w usłudze Azure magazynu](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. Wybierz **suma** z *agregacji* selektora.

   ![Azure Storage statycznych witryn internetowych metryki agregacji](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. Kliknij przycisk **Dodaj filtr** przycisk, a następnie wybierz **Nazwa interfejsu API** z *właściwość* selektora.

   ![Nazwa metryki interfejsu API statycznych witryn internetowych w usłudze Azure Storage](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. pole wyboru obok pozycji **GetWebContent** w *wartości* selektor do wypełniania raportu metryk.

   ![Metryki statycznych witryn internetowych usługi Azure Storage GetWebContent](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

## <a name="next-steps"></a>Kolejne kroki

* [Hostowanie statycznej witryny internetowej w usłudze Azure Storage](storage-blob-static-website.md)
* [Użyj usługi Azure CDN dostęp do obiektów blob z zastosowaniem domen niestandardowych przy użyciu protokołu HTTPS](storage-https-custom-domain-cdn.md)
* [Konfigurowanie niestandardowej nazwy domeny dla punktu końcowego usługi blob Storage lub sieci web](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Usługa Azure App Service](/azure/app-service/overview)
* [Utwórz swoją pierwszą aplikację sieci web bez użycia serwera](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Samouczek: Hostuj swoją domenę, w usłudze Azure DNS](../../dns/dns-delegate-domain-azure-dns.md)
