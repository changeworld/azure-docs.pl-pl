---
title: Uruchamianie Azure Functions z pakietu | Microsoft Docs
description: Aby środowisko uruchomieniowe Azure Functions uruchomiło funkcje, instalując plik pakietu wdrożeniowego zawierający pliki projektu aplikacji funkcji.
services: functions
documentationcenter: na
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: glenga
ms.openlocfilehash: b6a2347ff79268cdaf54993952d59bd700b781bc
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70095958"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Uruchamianie Azure Functions z pliku pakietu

Na platformie Azure możesz uruchamiać funkcje bezpośrednio z pliku pakietu wdrożeniowego w aplikacji funkcji. Druga opcja polega na wdrożeniu plików w `d:\home\site\wwwroot` katalogu aplikacji funkcji.

W tym artykule opisano zalety uruchamiania funkcji z pakietu. Pokazano również, jak włączyć tę funkcję w aplikacji funkcji.

> [!IMPORTANT]
> Podczas wdrażania funkcji w aplikacji funkcji systemu Linux w [planie Premium](functions-scale.md#premium-plan)należy zawsze uruchamiać ją z pliku pakietu i [publikować aplikację przy użyciu Azure Functions Core Tools](functions-run-local.md#project-file-deployment).

## <a name="benefits-of-running-from-a-package-file"></a>Zalety uruchamiania z pliku pakietu
  
Istnieje kilka korzyści, które należy wykonać, korzystając z pliku pakietu:

+ Zmniejsza ryzyko problemów z blokowaniem kopiowania plików.
+ Program można wdrożyć w aplikacji produkcyjnej (z ponownym uruchomieniem).
+ Możesz mieć pewne pliki, które są uruchomione w aplikacji.
+ Zwiększa wydajność [wdrożeń Azure Resource Manager](functions-infrastructure-as-code.md).
+ Może skrócić czas zimnego startu, szczególnie w przypadku funkcji JavaScript z dużymi drzewami pakietów npm.

Aby uzyskać więcej informacji, zobacz [ten anons](https://github.com/Azure/app-service-announcements/issues/84).

## <a name="enabling-functions-to-run-from-a-package"></a>Włączanie funkcji do uruchamiania z pakietu

Aby umożliwić uruchamianie aplikacji funkcji z pakietu, wystarczy dodać `WEBSITE_RUN_FROM_PACKAGE` ustawienie do ustawień aplikacji funkcji. `WEBSITE_RUN_FROM_PACKAGE` Ustawienie może mieć jedną z następujących wartości:

| Value  | Opis  |
|---------|---------|
| **`1`**  | Zalecane w przypadku aplikacji funkcji działających w systemie Windows. Uruchom plik pakietu w `d:\home\data\SitePackages` folderze aplikacji funkcji. Jeśli nie jest [wdrażany przy użyciu narzędzia zip Deploy](#integration-with-zip-deployment), ta opcja wymaga, aby folder miał `packagename.txt`również plik o nazwie. Ten plik zawiera tylko nazwę pliku pakietu w folderze, bez odstępów. |
|**`<url>`**  | Lokalizacja określonego pliku pakietu, który ma zostać uruchomiony. Korzystając z usługi BLOB Storage, należy użyć prywatnego kontenera z sygnaturą [dostępu współdzielonego (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) , aby umożliwić środowisko uruchomieniowe funkcji dostęp do pakietu. Aby przekazać pliki pakietu do konta magazynu obiektów blob, można użyć [Eksplorator usługi Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md) .         |

> [!CAUTION]
> W przypadku uruchamiania aplikacji funkcji w systemie Windows opcja zewnętrzny adres URL daje w wyniku gorszą wydajność zimnego startu. Podczas wdrażania aplikacji funkcji w systemie Windows należy ustawić wartość `WEBSITE_RUN_FROM_PACKAGE` i opublikować ją `1` z wdrożeniem zip.

Poniżej przedstawiono aplikację funkcji skonfigurowaną do uruchamiania z pliku zip hostowanego w usłudze Azure Blob Storage:

![Ustawienie aplikacji WEBSITE_RUN_FROM_ZIP](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> Obecnie obsługiwane są tylko pliki pakietu zip.

## <a name="integration-with-zip-deployment"></a>Integracja z wdrożeniem zip

[Wdrożenie zip][Zip deployment for Azure Functions] to funkcja Azure App Service, która umożliwia wdrożenie projektu aplikacji funkcji w `wwwroot` katalogu. Projekt jest spakowany jako plik wdrożenia. zip. Te same interfejsy API mogą służyć do wdrażania pakietu w `d:\home\data\SitePackages` folderze. W przypadku ustawienia `1` `d:\home\data\SitePackages` aplikacji w interfejsie API wdrożenia zip Skopiuj pakiet do folderu zamiast wyodrębniania plików do `d:\home\site\wwwroot`programu. `WEBSITE_RUN_FROM_PACKAGE` Tworzy `packagename.txt` również plik. Aplikacja funkcji jest następnie uruchamiana z pakietu po ponownym uruchomieniu i `wwwroot` jest tylko do odczytu. Aby uzyskać więcej informacji o wdrażaniu zip, zobacz [zip Deployment for Azure Functions](deployment-zip-push.md).

## <a name="adding-the-website_run_from_package-setting"></a>Dodawanie ustawienia WEBSITE_RUN_FROM_PACKAGE

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

- Polecenie Uruchom z pakietu `wwwroot` sprawia, że jest tylko do odczytu, więc podczas zapisywania plików w tym katalogu zostanie wyświetlony komunikat o błędzie.
- Formaty tar i gzip nie są obsługiwane.
- Ta funkcja nie składa się z lokalnej pamięci podręcznej.
- W celu zwiększenia wydajności zimnej lokalizacji Użyj opcji zip (`WEBSITE_RUN_FROM_PACKAGE`= 1).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Ciągłe wdrażanie dla usługi Azure Functions](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
