---
title: Hostowanie statycznej witryny sieci Web w usłudze Azure Storage
description: Dowiedz się, jak udostępniać zawartość statyczną (pliki HTML, CSS, JavaScript i Image) bezpośrednio z kontenera na koncie usługi Azure Storage GPv2.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 05/28/2019
ms.openlocfilehash: d5b8121c7888903f3e4552a21a6ddc175ecc5176
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489085"
---
# <a name="host-a-static-website-in-azure-storage"></a>Hostowanie statycznej witryny sieci Web w usłudze Azure Storage

Zawartości statycznej (HTML, CSS, JavaScript i plików obrazów) można obsłużyć bezpośrednio w kontenerze na koncie usługi Azure Storage GPv2. Aby dowiedzieć się więcej, zobacz [statyczne hostowanie witryn sieci Web w usłudze Azure Storage](storage-blob-static-website.md).

W tym artykule opisano sposób włączania obsługi statycznej witryny sieci Web przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure lub programu PowerShell.

<a id="portal" />

## <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Aby zapoznać się z samouczkiem krok po kroku, zobacz [Samouczek: hostowanie statycznej witryny sieci Web na BLOB Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host).

Po włączeniu hostingu statycznej witryny sieci Web można wyświetlić strony witryny z przeglądarki przy użyciu publicznego adresu URL witryny sieci Web.

<a id="portal-find-url" />

### <a name="find-the-website-url-by-using-the-azure-portal"></a>Znajdź adres URL witryny sieci Web przy użyciu Azure Portal

W okienku, które pojawia się obok strony Przegląd konta na koncie magazynu, wybierz pozycję **statyczna witryna sieci Web**. Adres URL witryny zostanie wyświetlony w polu **podstawowy punkt końcowy** .

![Metryka statycznych witryn sieci Web usługi Azure Storage](./media/storage-blob-static-website/storage-blob-static-website-url.png)

<a id="cli" />

## <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Można włączyć obsługę statycznej witryny sieci Web przy użyciu [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

1. Najpierw Otwórz [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)lub jeśli interfejs wiersza polecenia platformy Azure został [zainstalowany](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) lokalnie, Otwórz aplikację konsoli poleceń, taką jak Windows PowerShell.

2. Jeśli Twoja tożsamość jest skojarzona z więcej niż jedną subskrypcją, ustaw aktywną subskrypcję na konto magazynu, które będzie hostować statyczną witrynę sieci Web.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Zastąp wartość symbolu zastępczego `<subscription-id>` IDENTYFIKATORem subskrypcji.

3. Włącz hosting statycznej witryny sieci Web.

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * Zastąp wartość symbolu zastępczego `<storage-account-name>` nazwą konta magazynu.

   * Zastąp symbol zastępczy `<error-document-name>` nazwą dokumentu błędu, który będzie widoczny dla użytkowników, gdy przeglądarka zażąda strony, która nie istnieje.

   * Zastąp symbol zastępczy `<index-document-name>` nazwą dokumentu indeksu. Ten dokument jest często "index. html".

4. Przekaż obiekty do kontenera *$Web* z katalogu źródłowego.

   > [!NOTE]
   > Jeśli używasz Azure Cloud Shell, pamiętaj o dodaniu znaku ucieczki `\` podczas odwoływania się do kontenera `$web` (na przykład: `\$web`). Jeśli używasz lokalnej instalacji interfejsu wiersza polecenia platformy Azure, nie musisz używać znaku ucieczki.

   W tym przykładzie przyjęto założenie, że używasz poleceń z sesji Azure Cloud Shell.

   ```azurecli-interactive
   az storage blob upload-batch -s <source-path> -d \$web --account-name <storage-account-name>
   ```

   * Zastąp wartość symbolu zastępczego `<storage-account-name>` nazwą konta magazynu.

   * Zastąp symbol zastępczy `<source-path>` ścieżką do lokalizacji plików, które chcesz przekazać.

   > [!NOTE]
   > Jeśli używasz instalacji lokalizacji interfejsu wiersza polecenia platformy Azure, możesz użyć ścieżki do dowolnej lokalizacji na komputerze lokalnym (na przykład: `C:\myFolder`.
   >
   > Jeśli używasz Azure Cloud Shell, musisz odwołać się do udziału plików, który jest widoczny dla Cloud Shell. Ta lokalizacja może być udziałem plików w udziale w chmurze lub istniejącym udziałem plików, który można zainstalować z Cloud Shell. Aby dowiedzieć się, jak to zrobić, zobacz [utrwalanie plików w Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage).

<a id="cli-find-url" />

### <a name="find-the-website-url-by-using-the-azure-cli"></a>Znajdowanie adresu URL witryny sieci Web przy użyciu interfejsu wiersza polecenia platformy Azure

Zawartość z przeglądarki można wyświetlić za pomocą publicznego adresu URL witryny sieci Web.

Znajdź adres URL przy użyciu następującego polecenia:

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* Zastąp wartość symbolu zastępczego `<storage-account-name>` nazwą konta magazynu.

* Zastąp wartość symbolu zastępczego `<resource-group-name>` nazwą grupy zasobów.

<a id="powershell" />

## <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)

Można włączyć hosting statycznej witryny sieci Web przy użyciu modułu Azure PowerShell.

1. Otwórz okno poleceń programu Windows PowerShell.

2. Sprawdź, czy masz Azure PowerShell module AZ w wersji 0,7 lub nowszej.

   ```powershell
   Get-InstalledModule -Name Az -AllVersions | select Name,Version
   ```

   Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps).

3. Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Connect-AzAccount` i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

   ```powershell
   Connect-AzAccount
   ```

4. Jeśli Twoja tożsamość jest skojarzona z więcej niż jedną subskrypcją, ustaw aktywną subskrypcję na konto magazynu, które będzie hostować statyczną witrynę sieci Web.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Zastąp wartość symbolu zastępczego `<subscription-id>` IDENTYFIKATORem subskrypcji.

5. Pobierz kontekst konta magazynu, który definiuje konto magazynu, którego chcesz użyć.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Zastąp wartość symbolu zastępczego `<resource-group-name>` nazwą grupy zasobów.

   * Zastąp wartość symbolu zastępczego `<storage-account-name>` nazwą konta magazynu.

6. Włącz hosting statycznej witryny sieci Web.

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * Zastąp symbol zastępczy `<error-document-name>` nazwą dokumentu błędu, który będzie widoczny dla użytkowników, gdy przeglądarka zażąda strony, która nie istnieje.

   * Zastąp symbol zastępczy `<index-document-name>` nazwą dokumentu indeksu. Ten dokument jest często "index. html".

7. Przekaż obiekty do kontenera *$Web* z katalogu źródłowego.

    ```powershell
    # upload a file
    set-AzStorageblobcontent -File "<path-to-file>" `
    -Container `$web `
    -Blob "<blob-name>" `
    -Context $ctx
     ```

   * Zastąp wartość symbolu zastępczego `<path-to-file>` z w pełni kwalifikowaną ścieżką do pliku, który chcesz przekazać (na przykład: `C:\temp\index.html`).

   * Zastąp wartość symbolu zastępczego `<blob-name>` nazwą, która ma dać otrzymany obiekt BLOB (na przykład: `index.html`).

<a id="powershell-find-url" />

### <a name="find-the-website-url-by-using-powershell"></a>Znajdowanie adresu URL witryny sieci Web przy użyciu programu PowerShell

Zawartość z przeglądarki można wyświetlić za pomocą publicznego adresu URL witryny sieci Web.

Znajdź adres URL przy użyciu następującego polecenia:

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* Zastąp wartość symbolu zastępczego `<resource-group-name>` nazwą grupy zasobów.

* Zastąp wartość symbolu zastępczego `<storage-account-name>` nazwą konta magazynu.

<a id="metrics" />

---

## <a name="enable-metrics-on-static-website-pages"></a>Włącz metryki na stronach statycznej witryny internetowej

Po włączeniu metryk statystyki ruchu dla plików w kontenerze **$Web** są raportowane na pulpicie nawigacyjnym metryk.

1. Kliknij pozycję **ustawienia** > **monitorowanie** > **metryki**.

   Dane metryk są generowane przez Podłączanie do różnych interfejsów API metryk. W portalu są wyświetlane tylko elementy API używane w danym przedziale czasowym, aby skoncentrować się tylko na elementach członkowskich, które zwracają dane. Aby upewnić się, że można wybrać wymagany element członkowski interfejsu API, pierwszym krokiem jest rozwinięcie przedziału czasu.

2. Kliknij przycisk ramy czasowe i wybierz pozycję **ostatnie 24 godziny** , a następnie kliknij pozycję **Zastosuj**.

   ![Zakres czasu metryk statycznych witryn sieci Web usługi Azure Storage](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. Wybierz pozycję **obiekt BLOB** z listy rozwijanej *przestrzeń nazw* .

   ![Przestrzeń nazw metryk statycznych witryn sieci Web usługi Azure Storage](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. Następnie wybierz metrykę **ruchu** wychodzącego.

   ![Metryka statycznych witryn sieci Web usługi Azure Storage](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. Wybierz pozycję **sum** z selektora *agregacji* .

   ![Agregacja metryk statycznych witryn sieci Web usługi Azure Storage](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. Kliknij przycisk **Dodaj filtr** i wybierz pozycję **nazwa interfejsu API** z selektora *Właściwości* .

   ![Nazwa interfejsu API metryk statycznych witryn sieci Web usługi Azure Storage](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. Zaznacz pole wyboru obok pozycji **GetWebContent** w selektorze *wartości* , aby wypełnić raport metryk.

   ![Metryki statycznych witryn sieci Web usługi Azure Storage GetWebContent](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

## <a name="next-steps"></a>Następne kroki

* [Hostowanie statycznej witryny internetowej w usłudze Azure Storage](storage-blob-static-website.md)
* [Użyj Azure CDN, aby uzyskać dostęp do obiektów BLOB za pomocą domen niestandardowych za pośrednictwem protokołu HTTPS](storage-https-custom-domain-cdn.md)
* [Konfigurowanie niestandardowej nazwy domeny dla obiektu BLOB lub punktu końcowego sieci Web](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [Tworzenie pierwszej aplikacji sieci Web bezserwerowej](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Samouczek: Hostowanie domeny w Azure DNS](../../dns/dns-delegate-domain-azure-dns.md)
