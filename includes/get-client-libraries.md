---
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/25/2018
ms.author: spelluru
ms.openlocfilehash: 3cd5f59a3dcf3afcd26d16874e7dc77ca0a7e844
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71841691"
---
### <a name="install-via-composer"></a>Instalowanie przez układacz
1. Utwórz plik o nazwie **Composer. JSON** w katalogu głównym projektu i Dodaj do niego następujący kod:
   
    ```json
    {
      "require": {
        "microsoft/azure-storage": "*"
      }
    }
    ```
2. Pobierz **[układacz. PHAR][composer-phar]** w katalogu głównym projektu.
3. Otwórz wiersz polecenia i wykonaj następujące polecenie w katalogu głównym projektu
   
    ```
    php composer.phar install
    ```

Alternatywnie przejdź do [biblioteki klienta php usługi Azure Storage][php-sdk-github] w witrynie GitHub, aby sklonować kod źródłowy.

[php-sdk-github]: https://github.com/Azure/azure-storage-php
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: https://github.com/Azure/azure-sdk-for-php
[composer-phar]: http://getcomposer.org/composer.phar
