---
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/25/2018
ms.author: spelluru
ms.openlocfilehash: b6e0e57881154f5885e9f518363eda3c5b1169a0
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183473"
---
### <a name="install-via-composer"></a>Instalowanie za pomocą Composer (kompozytor)
1. Utwórz plik o nazwie **composer.json** w katalogu głównym projektu i dodać do niego następujący kod:
   
    ```json
    {
      "require": {
        "microsoft/azure-storage": "*"
      }
    }
    ```
2. Pobierz **[composer.phar] [ composer-phar]** w katalogu głównym projektu.
3. Otwórz wiersz polecenia i uruchom następujące polecenie w katalogu głównym projektu
   
    ```
    php composer.phar install
    ```

Ewentualnie przejdź do [biblioteki klienta usługi Azure Storage PHP] [ php-sdk-github] w serwisie GitHub, klonowanie kodu źródłowego.

[php-sdk-github]: https://github.com/Azure/azure-storage-php
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: ../articles/php-download-sdk.md
[composer-phar]: http://getcomposer.org/composer.phar
