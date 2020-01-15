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
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75945154"
---
## <a name="create-a-project-zip-file"></a>Tworzenie pliku ZIP projektu

>[!NOTE]
> Jeśli pobrano pliki w pliku ZIP, najpierw Wyodrębnij pliki. Na przykład jeśli pobrano plik ZIP z usługi GitHub, nie można wdrożyć tego pliku jako-is. W witrynie GitHub są dodawane dodatkowe katalogi zagnieżdżone, które nie działają z App Service. 
>

W lokalnym oknie terminalu przejdź do katalogu głównego projektu aplikacji. 

Ten katalog powinien zawierać plik wpisu do aplikacji sieci Web, taki jak _index. html_, _index. php_i _App. js_. Może również zawierać pliki zarządzania pakietami, takie jak _Project. JSON_, _Composer. JSON_, _Package. JSON_, _Bower. JSON_i _Requirements. txt_.

Jeśli nie chcesz, aby App Service do uruchamiania automatyzacji wdrażania, uruchom wszystkie zadania kompilacji (na przykład `npm`, `bower`, `gulp`, `composer`i `pip`) i upewnij się, że masz wszystkie pliki potrzebne do uruchomienia aplikacji. Ten krok jest wymagany, jeśli chcesz [uruchomić pakiet bezpośrednio](../articles/app-service/deploy-run-package.md).

Utwórz archiwum ZIP z wszystkimi elementami w projekcie. Następujące polecenie używa domyślnego narzędzia w terminalu:

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

