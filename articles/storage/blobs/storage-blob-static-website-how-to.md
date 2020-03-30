---
title: Hostowanie statycznej witryny sieci Web w usłudze Azure Storage
description: Dowiedz się, jak wyświetlać zawartość statyczną (HTML, CSS, JavaScript i pliki obrazów) bezpośrednio z kontenera na koncie GPv2 usługi Azure Storage.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 03/04/2020
ms.openlocfilehash: 056e23f0f0cf1a3a1c70042cef3c92dd41f14f82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247014"
---
# <a name="host-a-static-website-in-azure-storage"></a>Hostowanie statycznej witryny sieci Web w usłudze Azure Storage

Zawartość statyczną (HTML, CSS, JavaScript i pliki obrazów) można wyświetlać bezpośrednio z kontenera na koncie GPv2 usługi Azure Storage. Aby dowiedzieć się więcej, zobacz [Hosting statycznej witryny sieci Web w usłudze Azure Storage](storage-blob-static-website.md).

W tym artykule pokazano, jak włączyć hosting statycznej witryny sieci Web przy użyciu witryny Azure portal, interfejsu wiersza polecenia platformy Azure lub programu PowerShell.

## <a name="enable-static-website-hosting"></a>Włącz statyczny hosting stron internetowych

Statyczny hosting stron internetowych to funkcja, którą musisz włączyć na koncie pamięci masowej.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/), aby rozpocząć pracę.

2. Znajdź konto magazynu i wyświetl omówienie konta.

3. Wybierz pozycję **Statyczna witryna internetowa**, aby wyświetlić stronę konfiguracji dla statycznych witryn internetowych.

4. Wybierz pozycję **Włączone**, aby umożliwić hostowanie statycznej witryny internetowej na koncie magazynu.

5. W polu **Nazwa dokumentu indeksu** określ domyślną stronę indeksu (na przykład: *index.html*). 

   Gdy użytkownik przejdzie do głównego elementu statycznej witryny internetowej, wyświetlona zostanie domyślna strona indeksu.  

6. W polu **Ścieżka dokumentu Błąd** określ domyślną stronę błędu (na przykład: *404.html*). 

   Gdy użytkownik spróbuje przejść do strony, która nie istnieje w statycznej witrynie internetowej, wyświetlona zostanie domyślna strona błędu.

7. Kliknij przycisk **Zapisz**. W witrynie Azure Portal jest teraz wyświetlany punkt końcowy statycznej witryny internetowej. 

    ![Włączanie hostowania statycznej witryny internetowej na koncie magazynu](media/storage-blob-static-website-host/enable-static-website-hosting.png)

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

<a id="cli" />

Statyczne hostowanie witryn sieci Web można włączyć za pomocą [interfejsu wiersza polecenia platformy Azure (CLI).](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)

1. Najpierw otwórz [usługę Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)lub jeśli [zainstalowano](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) interfejs wiersza polecenia platformy Azure lokalnie, otwórz aplikację konsoli poleceń, taką jak Windows PowerShell.

2. Jeśli Twoja tożsamość jest skojarzona z więcej niż jedną subskrypcją, ustaw aktywną subskrypcję na subskrypcję konta magazynu, które będzie hostować statyczną witrynę sieci Web.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Zastąp wartość symbolu `<subscription-id>` zastępczego identyfikatorem subskrypcji.

3. Włącz statyczny hosting stron internetowych.

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * Zastąp wartość symbolu zastępczego `<storage-account-name>` nazwą konta magazynu.

   * Zastąp `<error-document-name>` symbol zastępczy nazwą dokumentu błędu, który będzie wyświetlany użytkownikom, gdy przeglądarka zażąda strony w witrynie, która nie istnieje.

   * Zastąp `<index-document-name>` symbol zastępczy nazwą dokumentu indeksu. Ten dokument jest powszechnie "index.html".

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

<a id="powershell" />

Hosting statycznej witryny sieci Web można włączyć przy użyciu modułu programu Azure PowerShell.

1. Otwórz okno polecenia programu Windows PowerShell.

2. Sprawdź, czy masz moduł Azure PowerShell Az w wersji 0.7 lub nowszej.

   ```powershell
   Get-InstalledModule -Name Az -AllVersions | select Name,Version
   ```

   Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps).

3. Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Connect-AzAccount` i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

   ```powershell
   Connect-AzAccount
   ```

4. Jeśli Twoja tożsamość jest skojarzona z więcej niż jedną subskrypcją, ustaw aktywną subskrypcję na subskrypcję konta magazynu, które będzie hostować statyczną witrynę sieci Web.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Zastąp wartość symbolu `<subscription-id>` zastępczego identyfikatorem subskrypcji.

5. Pobierz kontekst konta magazynu, który definiuje konto magazynu, którego chcesz użyć.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Zastąp `<resource-group-name>` wartość zastępczą nazwą grupy zasobów.

   * Zastąp wartość symbolu zastępczego `<storage-account-name>` nazwą konta magazynu.

6. Włącz statyczny hosting stron internetowych.

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * Zastąp `<error-document-name>` symbol zastępczy nazwą dokumentu błędu, który będzie wyświetlany użytkownikom, gdy przeglądarka zażąda strony w witrynie, która nie istnieje.

   * Zastąp `<index-document-name>` symbol zastępczy nazwą dokumentu indeksu. Ten dokument jest powszechnie "index.html".

---

## <a name="upload-files"></a>Przekazywanie plików 

### <a name="portal"></a>[Portal](#tab/azure-portal)

W tych instrukcjach pokazano sposób przekazywania plików przy użyciu wersji Eksploratora magazynu, która jest wyświetlana w witrynie Azure portal. Jednak można również użyć wersji [Eksploratora magazynu,](https://azure.microsoft.com/features/storage-explorer/) który działa poza witryną Azure portal. Można użyć [AzCopy](../common/storage-use-azcopy-v10.md), PowerShell, CLI lub dowolnej aplikacji niestandardowej, która może przekazywać pliki do **$web** kontenera konta. Aby uzyskać samouczek krok po kroku, który przekazuje pliki przy użyciu kodu programu Visual Studio, zobacz [Samouczek: Hostowanie statycznej witryny sieci Web w magazynie obiektów Blob](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host).

1. Wybierz **Eksploratora magazynu (podgląd)**.

2. Rozwiń węzeł **KONTENERY OBIEKTÓW BLOB,** a następnie wybierz **kontener $web.**

3. Wybierz przycisk **Przekaż,** aby przekazać pliki.

   ![Przekazywanie plików](media/storage-blob-static-website/storage-blob-static-website-upload.png)

4. Jeśli zamierzasz, aby przeglądarka wyświetlała zawartość pliku, upewnij się, że `text/html`typ zawartości tego pliku jest ustawiony na . 

   ![Sprawdzanie typów zawartości](media/storage-blob-static-website/storage-blob-static-website-content-type.png)

   >[!NOTE]
   > Eksplorator magazynu automatycznie `text/html` ustawia tę właściwość na `.html`często rozpoznawane rozszerzenia, takie jak . Jednak w niektórych przypadkach będziesz musiał ustawić to samodzielnie. Jeśli ta właściwość nie `text/html`zostanie ustawiona na , przeglądarka wyświetli monit o pobranie pliku zamiast renderowania zawartości. Aby ustawić tę właściwość, kliknij plik prawym przyciskiem myszy, a następnie kliknij polecenie **Właściwości**.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Przekaż obiekty do kontenera *$web* z katalogu źródłowego.

> [!NOTE]
> Jeśli używasz usługi Azure Cloud Shell, `\` upewnij się, że `$web` podczas odwoływania `\$web`się do kontenera (na przykład: ). Jeśli używasz instalacji lokalnej interfejsu wiersza polecenia platformy Azure, nie trzeba używać znaku ucieczki.

W tym przykładzie przyjęto założenie, że używasz poleceń z sesji usługi Azure Cloud Shell.

```azurecli-interactive
az storage blob upload-batch -s <source-path> -d \$web --account-name <storage-account-name> --content-type 'text/html; charset=utf-8'
```

* Zastąp wartość symbolu zastępczego `<storage-account-name>` nazwą konta magazynu.

* Zastąp `<source-path>` symbol zastępczy ścieżką do lokalizacji plików, które chcesz przekazać.

> [!NOTE]
> Jeśli używasz instalacji lokalizacji interfejsu wiersza polecenia platformy Azure, możesz użyć ścieżki do `C:\myFolder`dowolnej lokalizacji na komputerze lokalnym (na przykład: .
>
> Jeśli używasz usługi Azure Cloud Shell, musisz odwołać się do udziału plików, który jest widoczny dla powłoki chmury. Ta lokalizacja może być udziałem plików samego udziału w chmurze lub istniejącym udziałem plików, który można zainstalować z powłoki chmury. Aby dowiedzieć się, jak to zrobić, zobacz [Utrwalanie plików w usłudze Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage).

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Przekaż obiekty do kontenera *$web* z katalogu źródłowego.

```powershell
# upload a file
set-AzStorageblobcontent -File "<path-to-file>" `
-Properties @{ ContentType = "text/html; charset=utf-8";} `
-Container `$web `
-Blob "<blob-name>" `
-Context $ctx
```

* Zastąp wartość symbolu `<path-to-file>` zastępczego w pełni kwalifikowaną ścieżką `C:\temp\index.html`do pliku, który chcesz przekazać (na przykład: ).

* Zastąp wartość symbolu `<blob-name>` zastępczego nazwą, którą chcesz nadać `index.html`wynikowy obiekt blob (na przykład: ).

---

## <a name="find-the-website-url-by-using-the-azure-portal"></a>Znajdź adres URL witryny sieci Web za pomocą witryny Azure portal

Możesz przeglądać strony witryny z przeglądarki, używając publicznego adresu URL witryny.

### <a name="portal"></a>[Portal](#tab/azure-portal)

<a id="portal-find-url" />

W okienku wyświetlanym obok strony przeglądu konta konta magazynu wybierz **pozycję Statyczna witryna sieci Web**. Adres URL witryny jest wyświetlany w polu **Podstawowy punkt końcowy.**

![Metryka metryk statycznych witryn sieci Web usługi Azure Storage](./media/storage-blob-static-website/storage-blob-static-website-url.png)

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

<a id="cli-find-url" />

Znajdź publiczny adres URL swojej statycznej witryny sieci Web za pomocą następującego polecenia:

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* Zastąp wartość symbolu zastępczego `<storage-account-name>` nazwą konta magazynu.

* Zastąp `<resource-group-name>` wartość zastępczą nazwą grupy zasobów.

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

<a id="powershell-find-url" />

Znajdź publiczny adres URL swojej statycznej witryny sieci Web za pomocą następującego polecenia:

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* Zastąp `<resource-group-name>` wartość zastępczą nazwą grupy zasobów.

* Zastąp wartość symbolu zastępczego `<storage-account-name>` nazwą konta magazynu.

---

<a id="metrics" />

## <a name="enable-metrics-on-static-website-pages"></a>Włączanie danych na statycznych stronach witryny sieci Web

Po włączeniu metryki statystyki ruchu plików w kontenerze **$web** są zgłaszane na pulpicie nawigacyjnym metryk.

1. Kliknij **pozycję Metryki** w sekcji **Monitor** w menu konta magazynu.

   > [!div class="mx-imgBorder"]
   > ![Łącze metryki](./media/storage-blob-static-website/metrics-link.png)

   > [!NOTE]
   > Dane metryki są generowane przez podłączenie do różnych metryki interfejsów API. Portal wyświetla tylko elementy członkowskie interfejsu API używane w danym przedziale czasowym, aby skupić się tylko na członkach, które zwracają dane. Aby upewnić się, że jesteś w stanie wybrać niezbędnego członka interfejsu API, pierwszym krokiem jest rozszerzenie przedziału czasu.

2. Kliknij przycisk przedział czasu, wybierz przedział czasu, a następnie kliknij przycisk **Zastosuj**.

   ![Zakres czasu metryk statycznych witryn sieci Web usługi Azure Storage](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. Wybierz **pozycję Blob** z *listy rozwijanej Obszar nazw.*

   ![Statyczne witryny sieci Web usługi Azure Storage metryki obszaru nazw](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. Następnie wybierz metrykę **Wychodzącą.**

   ![Metryka metryk statycznych witryn sieci Web usługi Azure Storage](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. Wybierz **opcję Suma** z *selektora agregacji.*

   ![Agregacja metryk statycznych witryn sieci Web usługi Azure Storage](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. Kliknij przycisk **Dodaj filtr** i wybierz nazwę **interfejsu API** z selektora *właściwości.*

   ![Statyczne witryny sieci Web usługi Azure Storage metryki nazwa interfejsu API](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. Zaznacz pole wyboru **GetWebContent** w selektorze *Wartości,* aby wypełnić raport metryki.

   ![Metryki statycznych witryn sieci Web usługi Azure Storage GetWebContent](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

   >[!NOTE]
   > Pole wyboru **GetWebContent** jest wyświetlane tylko wtedy, gdy ten element członkowski interfejsu API był używany w danym przedziale czasu. Portal wyświetla tylko elementy członkowskie interfejsu API używane w danym przedziale czasowym, aby skupić się tylko na członkach, które zwracają dane. Jeśli nie możesz znaleźć określonego członka interfejsu API na tej liście, rozwiń przedział czasu.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak skonfigurować domenę niestandardową za pomocą statycznej witryny sieci Web. Zobacz [Mapowanie domeny niestandardowej do punktu końcowego usługi Azure Blob Storage](storage-custom-domain-name.md).

