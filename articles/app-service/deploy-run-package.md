---
title: Uruchamianie aplikacji z pakietu ZIP
description: Wdrażanie pakietu ZIP aplikacji z niepodzielnością. Zwiększ przewidywalność i niezawodność zachowania aplikacji podczas procesu wdrażania usługi ZIP.
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 5cc909d79b3f5ea2b4c6a3da12bc7250addbe00c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920726"
---
# <a name="run-your-app-in-azure-app-service-directly-from-a-zip-package"></a>Uruchamianie aplikacji w usłudze Azure App Service bezpośrednio z pakietu ZIP

W [usłudze Azure App Service](overview.md)aplikacje można uruchamiać bezpośrednio z pliku pakietu ZIP wdrożenia. W tym artykule pokazano, jak włączyć tę funkcję w aplikacji.

Wszystkie inne metody wdrażania w usłudze App Service mają coś wspólnego: pliki są wdrażane w *D:\home\site\wwwroot* w aplikacji (lub */home/site/wwwroot* dla aplikacji linux). Ponieważ ten sam katalog jest używany przez aplikację w czasie wykonywania, jest możliwe wdrożenie zakończyć się niepowodzeniem z powodu konfliktów blokady plików i dla aplikacji zachowywać się nieprzewidywalnie, ponieważ niektóre pliki nie są jeszcze aktualizowane.

Natomiast po uruchomieniu bezpośrednio z pakietu pliki w pakiecie nie są kopiowane do katalogu *wwwroot.* Zamiast tego sam pakiet ZIP jest montowany bezpośrednio jako katalog *wwwroot* tylko do odczytu. Istnieje kilka korzyści do uruchomienia bezpośrednio z pakietu:

- Eliminuje konflikty blokady plików między wdrożeniem a czasem wykonywania.
- Zapewnia, że tylko w pełni wdrożone aplikacje są uruchomione w dowolnym momencie.
- Można wdrożyć w aplikacji produkcyjnej (z ponownym uruchomieniem).
- Zwiększa wydajność wdrożeń usługi Azure Resource Manager.
- Może skrócić czas rozruchu na zimno, szczególnie w przypadku funkcji JavaScript z dużymi drzewami pakietów npm.

> [!NOTE]
> Obecnie obsługiwane są tylko pliki pakietów ZIP.

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

## <a name="enable-running-from-package"></a>Włącz uruchamianie z pakietu

Ustawienie `WEBSITE_RUN_FROM_PACKAGE` aplikacji umożliwia uruchamianie z pakietu. Aby go ustawić, uruchom następujące polecenie za pomocą interfejsu wiersza polecenia platformy Azure.

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_RUN_FROM_PACKAGE="1"
```

`WEBSITE_RUN_FROM_PACKAGE="1"`umożliwia uruchamianie aplikacji z lokalnego pakietu do aplikacji. Można również [uruchomić z pakietu zdalnego](#run-from-external-url-instead).

## <a name="run-the-package"></a>Uruchamianie pakietu

Najprostszym sposobem uruchomienia pakietu w usłudze App Service jest polecenie [config-zip źródła wdrażania](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) aplikacji Azure CLI az webapp. Przykład:

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src <filename>.zip
```

Ponieważ `WEBSITE_RUN_FROM_PACKAGE` ustawienie aplikacji jest ustawione, to polecenie nie wyodrębnia zawartości pakietu do katalogu *D:\home\site\wwwroot* aplikacji. Zamiast tego przekazuje plik ZIP jako —jest do *D:\home\data\SitePackages*i tworzy *packagename.txt* w tym samym katalogu, który zawiera nazwę pakietu ZIP do załadowania w czasie wykonywania. Jeśli pakiet ZIP zostanie przesłany w inny sposób (na przykład [FTP),](deploy-ftp.md)należy ręcznie utworzyć katalog *D:\home\data\SitePackages* i plik *packagename.txt.*

Polecenie również uruchamia ponownie aplikację. Ponieważ `WEBSITE_RUN_FROM_PACKAGE` jest ustawiona, usługa App Service montuje przekazany pakiet jako katalog *wwwroot* tylko do odczytu i uruchamia aplikację bezpośrednio z tego zainstalowanego katalogu.

## <a name="run-from-external-url-instead"></a>Zamiast tego uruchom z zewnętrznego adresu URL

Pakiet można również uruchomić z zewnętrznego adresu URL, takiego jak usługa Azure Blob Storage. Za pomocą [Eksploratora usługi Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md) można przekazać pliki pakietów do konta magazynu obiektów Blob. Należy użyć kontenera magazynu prywatnego z [podpisem dostępu współdzielonego (SAS),](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) aby włączyć środowisko uruchomieniowe usługi App Service, aby bezpiecznie uzyskać dostęp do pakietu. 

Po przekazaniu pliku do magazynu obiektów Blob i uzyskaniu `WEBSITE_RUN_FROM_PACKAGE` adresu URL sygnatury dostępu Współdzielonego dla pliku ustaw ustawienie aplikacji na adres URL. W poniższym przykładzie wykonuje to przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="https://myblobstorage.blob.core.windows.net/content/SampleCoreMVCApp.zip?st=2018-02-13T09%3A48%3A00Z&se=2044-06-14T09%3A48%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=bNrVrEFzRHQB17GFJ7boEanetyJ9DGwBSV8OM3Mdh%2FM%3D"
```

Jeśli publikujesz zaktualizowany pakiet o tej samej nazwie do magazynu obiektów Blob, należy ponownie uruchomić aplikację, tak aby zaktualizowany pakiet jest ładowany do usługi App Service.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

- Uruchamianie bezpośrednio z `wwwroot` pakietu sprawia, że tylko do odczytu. Aplikacja otrzyma błąd, jeśli spróbuje zapisać pliki w tym katalogu.
- Formaty TAR i GZIP nie są obsługiwane.
- Ta funkcja nie jest zgodna z [lokalną pamięcią podręczną](overview-local-cache.md).
- Aby uzyskać lepszą wydajność na zimno,`WEBSITE_RUN_FROM_PACKAGE`użyj lokalnej opcji Zip ( =1).

## <a name="more-resources"></a>Więcej zasobów

- [Ciągłe wdrażanie usługi Azure App Service](deploy-continuous-deployment.md)
- [Wdrażanie kodu za pomocą pliku ZIP lub WAR](deploy-zip.md)
