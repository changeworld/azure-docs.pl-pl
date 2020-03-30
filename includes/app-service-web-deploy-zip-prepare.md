---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/14/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 6b5aa4f409b8c2f5a9125ab01e8587f4ac9c4ee5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75945154"
---
## <a name="create-a-project-zip-file"></a>Tworzenie pliku ZIP projektu

>[!NOTE]
> Jeśli pliki zostały pobrane w pliku ZIP, najpierw wyodrębnij je. Na przykład po pobraniu pliku ZIP z gitHub nie można wdrożyć tego pliku w stanie takim, w jakim się znajduje. GitHub dodaje dodatkowe zagnieżdżone katalogi, które nie działają z usługą App Service. 
>

W oknie terminala lokalnego przejdź do katalogu głównego projektu aplikacji. 

Ten katalog powinien zawierać plik wejścia do aplikacji internetowej, na przykład _index.html_, _index.php_i _app.js_. Może również zawierać pliki do zarządzania pakietami, takie jak _project.json_, _composer.json_, _package.json_, _bower.json_i _requirements.txt_.

Jeśli usługa App Service nie ma działać automatyzacji wdrażania, uruchom `npm` `bower`wszystkie `gulp` `composer`zadania `pip`kompilacji (na przykład , , , , i ) i upewnij się, że masz wszystkie pliki potrzebne do uruchomienia aplikacji. Ten krok jest wymagany, jeśli chcesz [uruchomić pakiet bezpośrednio](../articles/app-service/deploy-run-package.md).

Utwórz archiwum ZIP z wszystkimi elementami w projekcie. Następujące polecenie używa domyślnego narzędzia w terminalu:

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

