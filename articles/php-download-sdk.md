---
title: Pobierz zestaw Azure SDK dla języka PHP
description: Dowiedz się, jak pobrać i zainstalować zestaw Azure SDK for PHP.
documentationcenter: php
services: app-service\web
author: allclark
manager: douge
editor: ''
ms.assetid: bac355ac-4c25-42f4-8273-c5112eafa8d4
ms.service: app-service-web
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 06/01/2016
ms.author: allclark;yaqiyang
ms.openlocfilehash: cfcf908145e8a384782953e045f9e10fd3c0e8f9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34639473"
---
# <a name="download-the-azure-sdk-for-php"></a>Pobierz zestaw Azure SDK dla języka PHP

## <a name="overview"></a>Przegląd

Zestaw Azure SDK for PHP zawiera składniki, które umożliwiają tworzenie, wdrażanie i zarządzanie aplikacji PHP dla systemu Azure. W szczególności zestaw Azure SDK for PHP obejmuje następujące funkcje:

* **PHP bibliotek klienta platformy Azure**. Te biblioteki klas zapewniają interfejs do uzyskiwania dostępu do funkcji platformy Azure, takich jak dane usługi zarządzania i usługi w chmurze.
* **Interfejs wiersza polecenia platformy Azure dla komputerów Mac, Linux i Windows (Azure CLI)**. Jest to zestaw poleceń służących do wdrażania i zarządzania usługami Azure, takich jak witryny sieci Web Azure i maszyn wirtualnych platformy Azure. Praca wiersza polecenia platformy Azure na dowolnej platformie, w tym Mac, Linux i Windows.
* **Program Azure PowerShell (tylko system Windows)**. Jest to zestaw poleceń cmdlet programu PowerShell dotyczące wdrażania i zarządzania usługami Azure, takich jak usługi w chmurze i maszyn wirtualnych.
* **Emulatory Azure (tylko system Windows)**. Emulatory obliczeniowych i przestrzeni dyskowej są emulatory lokalne usługi w chmurze i usług zarządzania danych, dzięki którym można przetestować aplikację lokalnie. Emulatory Azure uruchamianie tylko w systemie Windows.

W poniższych rozdziałach opisano, jak pobrać i zainstalować składniki opisane powyżej.

Instrukcje w tym temacie założono, że istnieje [PHP] [ install-php] zainstalowane.

> [!NOTE]
> Musi mieć PHP 5.5 lub nowszego, aby użyć bibliotek klienckich PHP na platformie Azure.
>
>

## <a name="php-client-libraries-for-azure"></a>Biblioteki klienckie języka PHP dla systemu Azure

PHP bibliotek klienta platformy Azure zapewniają interfejs do uzyskiwania dostępu do funkcji platformy Azure, takich jak dane usługi zarządzania i usługami z dowolnego systemu operacyjnego w chmurze. Te biblioteki można zainstalować za pomocą Composer.

Aby uzyskać informacje o sposobie używania bibliotek klienckich PHP na platformie Azure, zobacz [jak używać usługi Blob][blob-service], [sposobu korzystania z usługi tabel] [ table-service]i [jak używać usługi kolejki][queue-service].

### <a name="install-via-composer"></a>Zainstaluj za pośrednictwem Composer

1. [Zainstaluj usługę Git][install-git]. W systemie Windows należy również dodać Git pliku wykonywalnego do Twojej zmiennej środowiskowej PATH.

2. Utwórz plik o nazwie **composer.json** w folderze głównym projektu i Dodaj do niej następujący kod:

        {
            "require": {
                "microsoft/windowsazure": "^0.4"
            }
        }

3. Pobierz **[composer.phar] [ composer-phar]** w katalogu głównym projektu.

4. Otwórz wiersz polecenia i wykonania tej operacji w katalogu głównym projektu

        php composer.phar install

## <a name="azure-powershell-and-azure-emulators"></a>Program Azure PowerShell i emulatory Azure

Program Azure PowerShell jest zestawem poleceń cmdlet programu PowerShell dotyczące wdrażania i zarządzania usługami Azure (takie jak usługi w chmurze i maszyn wirtualnych). Emulatory Azure są emulatory usługi w chmurze i usług zarządzania danych, dzięki którym można przetestować aplikację lokalnie. Te składniki są obsługiwane tylko w systemie Windows.

Zalecanym sposobem instalowania programu Azure PowerShell i emulatory Azure jest użycie [Instalatora platformy sieci Web firmy Microsoft][download-wpi]. Należy pamiętać, że użytkownik można również zainstalować inne składniki programowanie, takich jak PHP, SQL Server, Drivers firmy Microsoft dla programu SQL Server dla PHP i programu WebMatrix.

Aby uzyskać informacje o sposobie używania programu Azure PowerShell, zobacz [jak używać programu Azure PowerShell][powershell-tools].

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Azure CLI jest zestaw poleceń służących do wdrażania i zarządzania usługami Azure, takich jak witryny sieci Web Azure i maszyn wirtualnych platformy Azure. Aby uzyskać informacje o instalowaniu interfejsu wiersza polecenia Azure, zobacz [instalowanie interfejsu wiersza polecenia Azure](cli-install-nodejs.md).

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji, zobacz [Centrum deweloperów języka PHP](https://azure.microsoft.com/develop/php/).

[install-php]: http://www.php.net/manual/en/install.php
[composer-github]: https://github.com/composer/composer
[composer-phar]: http://getcomposer.org/composer.phar
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[download-wpi]: http://go.microsoft.com/fwlink/?LinkId=253447
[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[blob-service]: http://go.microsoft.com/fwlink/?LinkId=252714
[table-service]: http://go.microsoft.com/fwlink/?LinkId=252715
[queue-service]: http://go.microsoft.com/fwlink/?LinkId=252716
[azure cli]: http://go.microsoft.com/fwlink/?LinkId=252717
[powershell-tools]: http://go.microsoft.com/fwlink/?LinkId=252718
[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
