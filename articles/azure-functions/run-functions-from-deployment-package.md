---
title: Uruchamianie usługi Azure Functions z pakietu
description: Niech środowisko uruchomieniowe usługi Azure Functions uruchamia funkcje, montując plik pakietu wdrażania, który zawiera pliki projektu aplikacji funkcji.
ms.topic: conceptual
ms.date: 07/15/2019
ms.openlocfilehash: d40896d6a4659945dbeda9ca965366f0b2ca4bd2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365275"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Uruchamianie usługi Azure Functions z pliku pakietu

Na platformie Azure można uruchamiać funkcje bezpośrednio z pliku pakietu wdrażania w aplikacji funkcji. Inną opcją jest wdrożenie plików `d:\home\site\wwwroot` w katalogu aplikacji funkcji.

W tym artykule opisano korzyści wynikające z uruchamiania funkcji z pakietu. Pokazano również, jak włączyć tę funkcję w aplikacji funkcji.

> [!IMPORTANT]
> Podczas wdrażania funkcji w aplikacji z funkcją systemu Linux w [planie Premium](functions-scale.md#premium-plan)należy zawsze uruchomić z pliku pakietu i [opublikować aplikację przy użyciu narzędzia Azure Functions Core Tools.](functions-run-local.md#project-file-deployment)

## <a name="benefits-of-running-from-a-package-file"></a>Korzyści z uruchamiania z pliku pakietu
  
Istnieje kilka korzyści z uruchamiania z pliku pakietu:

+ Zmniejsza ryzyko problemów z blokowaniem kopii plików.
+ Można wdrożyć w aplikacji produkcyjnej (z ponownym uruchomieniem).
+ Możesz mieć pewność, że pliki są uruchomione w aplikacji.
+ Zwiększa wydajność [wdrożeń usługi Azure Resource Manager](functions-infrastructure-as-code.md).
+ Może skrócić czas rozruchu na zimno, szczególnie w przypadku funkcji JavaScript z dużymi drzewami pakietów npm.

Aby uzyskać więcej informacji, zobacz [to ogłoszenie](https://github.com/Azure/app-service-announcements/issues/84).

## <a name="enabling-functions-to-run-from-a-package"></a>Włączanie uruchamiania funkcji z pakietu

Aby włączyć uruchamianie aplikacji funkcji z pakietu, `WEBSITE_RUN_FROM_PACKAGE` wystarczy dodać ustawienie do ustawień aplikacji funkcji. Ustawienie `WEBSITE_RUN_FROM_PACKAGE` może mieć jedną z następujących wartości:

| Wartość  | Opis  |
|---------|---------|
| **`1`**  | Zalecane dla aplikacji funkcyjnych działających w systemie Windows. Uruchom z pliku pakietu `d:\home\data\SitePackages` w folderze aplikacji funkcji. Jeśli nie [jest wdrażany z wdrażaniem zip,](#integration-with-zip-deployment)ta `packagename.txt`opcja wymaga, aby folder miał również plik o nazwie . Ten plik zawiera tylko nazwę pliku pakietu w folderze, bez żadnych odstępów. |
|**`<URL>`**  | Lokalizacja określonego pliku pakietu, który chcesz uruchomić. Podczas korzystania z magazynu obiektów Blob, należy użyć kontenera prywatnego z [podpisem dostępu współdzielonego (SAS),](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) aby włączyć środowisko uruchomieniowe funkcji dostępu do pakietu. Za pomocą [Eksploratora usługi Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md) można przekazać pliki pakietów do konta magazynu obiektów Blob. Po określeniu adresu URL należy również [synchronizować wyzwalacze](functions-deployment-technologies.md#trigger-syncing) po opublikowaniu zaktualizowanego pakietu. |

> [!CAUTION]
> Podczas uruchamiania aplikacji funkcji w systemie Windows, zewnętrzna opcja adresu URL daje gorsze wyniki cold-start. Podczas wdrażania aplikacji funkcji w systemie `WEBSITE_RUN_FROM_PACKAGE` Windows `1` należy ustawić i publikować za pomocą wdrożenia zip.

Poniżej przedstawiono aplikację funkcji skonfigurowaną do uruchamiania z pliku zip hostowanego w magazynie obiektów Blob platformy Azure:

![WEBSITE_RUN_FROM_ZIP ustawienie aplikacji](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> Obecnie obsługiwane są tylko pliki pakietu .zip.

## <a name="integration-with-zip-deployment"></a>Integracja z wdrażaniem zip

[Wdrożenie zip][Zip deployment for Azure Functions] jest funkcją usługi Azure App Service, która `wwwroot` umożliwia wdrożenie projektu aplikacji funkcji do katalogu. Projekt jest pakowany jako plik wdrażania zip. Te same interfejsy API mogą służyć do `d:\home\data\SitePackages` wdrażania pakietu w folderze. Z `WEBSITE_RUN_FROM_PACKAGE` wartością ustawienia `1`aplikacji interfejsy API wdrażania zip `d:\home\data\SitePackages` kopiują pakiet do folderu zamiast wyodrębniania plików do `d:\home\site\wwwroot`. Tworzy również `packagename.txt` plik. Po ponownym uruchomieniu pakiet jest `wwwroot` instalowany jako system plików tylko do odczytu. Aby uzyskać więcej informacji na temat wdrażania zip, zobacz [wdrażanie zip dla usługi Azure Functions](deployment-zip-push.md).

## <a name="adding-the-website_run_from_package-setting"></a>Dodawanie ustawienia WEBSITE_RUN_FROM_PACKAGE

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]


## <a name="troubleshooting"></a>Rozwiązywanie problemów

- Uruchom z `wwwroot` pakietu sprawia, że tylko do odczytu, więc pojawi się błąd podczas pisania plików do tego katalogu.
- Formaty tar i gzip nie są obsługiwane.
- Ta funkcja nie jest redagowana z lokalną pamięcią podręczną.
- Aby uzyskać lepszą wydajność na zimno,`WEBSITE_RUN_FROM_PACKAGE`użyj lokalnej opcji Zip ( =1).
- Uruchom z pakietu jest niezgodny z`SCM_DO_BUILD_DURING_DEPLOYMENT=true`opcją dostosowywania wdrożenia ( ), krok kompilacji zostanie zignorowany podczas wdrażania.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Ciągłe wdrażanie dla usług Azure Functions](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
