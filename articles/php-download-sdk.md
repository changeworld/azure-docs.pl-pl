---
title: Pobierz zestaw Azure SDK dla języka PHP
description: Dowiedz się, jak pobrać i zainstalować zestaw Azure SDK dla języka PHP.
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
ms.openlocfilehash: f6b21f288b94e06414fe66ff775ebb264368c0b8
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65411599"
---
# <a name="download-the-azure-sdk-for-php"></a>Pobierz zestaw Azure SDK dla języka PHP

## <a name="overview"></a>Omówienie

Zestaw Azure SDK dla języka PHP zawiera składniki, które umożliwiają tworzenie, wdrażanie i zarządzanie aplikacjami PHP na platformie Azure. W szczególności zestawu Azure SDK dla języka PHP obejmuje następujące funkcje:

* **Biblioteki klienckie PHP na platformie Azure**. Te biblioteki klas zapewniają interfejs do uzyskiwania dostępu do funkcji platformy Azure, takich jak usługi zarządzania danymi i usługami w chmurze.
* **Interfejsu wiersza polecenia platformy Azure dla systemów Mac, Linux i Windows (Azure CLI)**. Jest to zestaw poleceń służących do wdrażania i zarządzania nimi usług platformy Azure, takich jak usługa Azure Websites i maszyn wirtualnych platformy Azure. Praca wiersza polecenia platformy Azure na dowolnej platformie, w tym systemów Mac, Linux i Windows.
* **Program Azure PowerShell (tylko Windows)**. Jest to zestaw poleceń cmdlet programu PowerShell dotyczące wdrażania i zarządzania usługami platformy Azure, takie jak maszyny wirtualne i usługi w chmurze.
* **Emulatory systemu Azure (tylko Windows)**. Emulatory zasobów obliczeniowych i magazynu są lokalne emulatorów usług w chmurze i usług zarządzania danymi, które umożliwiają przetestowanie aplikacji w środowisku lokalnym. Emulatory Azure uruchomić tylko na Windows.

W poniższych sekcjach opisano sposób pobrać i zainstalować składniki opisanych powyżej.

Instrukcje w tym temacie założono, że [PHP] [ install-php] zainstalowane.

> [!NOTE]
> Konieczne jest posiadanie PHP 5.5 lub nowszego wykorzystać biblioteki klienckie PHP na platformie Azure.
>
>

## <a name="php-client-libraries-for-azure"></a>Biblioteki klienckie języka PHP dla systemu Azure

Biblioteki klienckie PHP dla platformy Azure zapewniają interfejs do uzyskiwania dostępu do funkcji platformy Azure, takich jak usługi zarządzania danymi i usługami w chmurze, z dowolnego systemu operacyjnego. Biblioteki te mogą być instalowane za pośrednictwem Composer (kompozytor).

Aby uzyskać informacje o tym, jak wykorzystać biblioteki klienckie PHP na platformie Azure, zobacz [sposobu korzystania z usługi Blob][blob-service], [sposobu korzystania z usługi Table] [ table-service]i [sposobu korzystania z usługi Queue][queue-service].

### <a name="install-via-composer"></a>Instalowanie za pomocą Composer (kompozytor)

1. [Zainstaluj oprogramowanie Git][install-git]. W Windows należy również dodać Git pliku wykonywalnego do zmiennej środowiskowej PATH.

2. Utwórz plik o nazwie **composer.json** w katalogu głównym projektu i dodać do niego następujący kod:

        {
            "require": {
                "microsoft/windowsazure": "^0.4"
            }
        }

3. Pobierz **[composer.phar] [ composer-phar]** w katalogu głównym projektu.

4. Otwórz wiersz polecenia i wykonania tej operacji w katalogu głównym projektu

        php composer.phar install

## <a name="azure-powershell-and-azure-emulators"></a>Program Azure PowerShell i emulatory systemu Azure

Program Azure PowerShell to zestaw poleceń cmdlet programu PowerShell dotyczące wdrażania i zarządzania usługami platformy Azure (np. maszyny wirtualne i usługi w chmurze). Emulatory platformy Azure są emulatorów usług w chmurze i usług zarządzania danymi, które umożliwiają przetestowanie aplikacji w środowisku lokalnym. Te składniki są obsługiwane tylko Windows.

Zalecanym sposobem instalowania programu Azure PowerShell i emulatory Azure jest użycie [Instalatora platformy sieci Web firmy Microsoft][download-wpi]. Należy pamiętać, że możesz również zainstalować inne składniki programowania, takich jak PHP, programu SQL Server, Drivers firmy Microsoft dla programu SQL Server dla języka PHP i programu WebMatrix.

Aby uzyskać informacje o sposobie używania programu Azure PowerShell, zobacz [jak używać programu Azure PowerShell][powershell-tools].

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure to zestaw poleceń służących do wdrażania i zarządzania nimi usług platformy Azure, takich jak usługa Azure Websites i maszyn wirtualnych platformy Azure. Aby uzyskać informacje o instalowaniu interfejsu wiersza polecenia platformy Azure, zobacz [zainstalować interfejs wiersza polecenia platformy Azure](cli-install-nodejs.md).

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji, zobacz [Centrum deweloperów języka PHP](https://azure.microsoft.com/develop/php/).

[install-php]: https://www.php.net/manual/en/install.php
[composer-github]: https://github.com/composer/composer
[composer-phar]: https://getcomposer.org/composer.phar
[nodejs-org]: https://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[download-wpi]: https://go.microsoft.com/fwlink/?LinkId=253447
[mac-installer]: https://go.microsoft.com/fwlink/?LinkId=252249
[blob-service]: https://go.microsoft.com/fwlink/?LinkId=252714
[table-service]: https://go.microsoft.com/fwlink/?LinkId=252715
[queue-service]: https://go.microsoft.com/fwlink/?LinkId=252716
[azure cli]: https://go.microsoft.com/fwlink/?LinkId=252717
[powershell-tools]: https://go.microsoft.com/fwlink/?LinkId=252718
[php-sdk-github]: https://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: https://git-scm.com/book/en/Getting-Started-Installing-Git
