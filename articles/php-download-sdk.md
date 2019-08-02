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
ms.author: yaya
ms.openlocfilehash: 9dd336ef973b603108aad62c90e4dab385d20317
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "67873896"
---
# <a name="download-the-azure-sdk-for-php"></a>Pobierz zestaw Azure SDK dla języka PHP

## <a name="overview"></a>Omówienie

Zestaw Azure SDK dla języka PHP zawiera składniki, które umożliwiają tworzenie i wdrażanie aplikacji PHP oraz zarządzanie nimi na platformie Azure. Zestaw Azure SDK dla języka PHP obejmuje następujące elementy:

* **Biblioteki KLIENCKIE php dla platformy Azure**. Te biblioteki klas zapewniają interfejs do uzyskiwania dostępu do funkcji platformy Azure, takich jak usługi zarządzania danymi i usługi w chmurze.
* **Interfejs wiersza polecenia platformy Azure dla systemów Mac, Linux i Windows (Azure CLI)** . Jest to zestaw poleceń służących do wdrażania usług platformy Azure, takich jak Azure Websites i Azure Virtual Machines. Interfejs wiersza polecenia platformy Azure pracuje na dowolnej platformie, w tym dla systemów Mac, Linux i Windows.
* **Azure PowerShell (tylko system Windows)** . Jest to zestaw poleceń cmdlet programu PowerShell służących do wdrażania usług platformy Azure i zarządzania nimi, takich jak Cloud Services i Virtual Machines.
* **Emulatory platformy Azure (tylko system Windows)** . Emulatory obliczeniowe i magazynowe są lokalnymi emulatorami usług w chmurze i usług zarządzania danymi, które umożliwiają przetestowanie aplikacji lokalnie. Emulatory platformy Azure są uruchamiane tylko w systemie Windows.

W poniższych sekcjach opisano, jak pobrać i zainstalować składniki opisane powyżej.

Instrukcje w tym temacie zakładają, że zainstalowano język [php][install-php] .

> [!NOTE]
> Aby korzystać z bibliotek klienckich PHP dla platformy Azure, musisz mieć język PHP 5,5 lub nowszy.
>
>

## <a name="php-client-libraries-for-azure"></a>Biblioteki klienckie języka PHP dla systemu Azure

Biblioteki klienckie PHP dla systemu Azure udostępniają interfejs do uzyskiwania dostępu do funkcji platformy Azure, takich jak usługi zarządzania danymi i usługi w chmurze, z dowolnego systemu operacyjnego. Te biblioteki można instalować za pośrednictwem programu Composer.

Aby uzyskać informacje o sposobach korzystania z bibliotek klienta PHP dla platformy Azure, zobacz [jak używać usługi BLOB Service][blob-service], [jak korzystać][table-service] z usługi Table Service oraz [jak korzystać z usługi Queue Service][queue-service].

### <a name="install-via-composer"></a>Instalowanie przez układacz

1. [Zainstaluj oprogramowanie Git][install-git]. W systemie Windows należy również dodać plik wykonywalny git do zmiennej środowiskowej PATH.

2. Utwórz plik o nazwie **Composer. JSON** w katalogu głównym projektu i Dodaj do niego następujący kod:

        {
            "require": {
                "microsoft/windowsazure": "^0.4"
            }
        }

3. Pobierz **[układacz. PHAR][composer-phar]** w katalogu głównym projektu.

4. Otwórz wiersz polecenia i wykonaj to w katalogu głównym projektu

        php composer.phar install

## <a name="azure-powershell-and-azure-emulators"></a>Azure PowerShell i emulatory platformy Azure

Azure PowerShell to zestaw poleceń cmdlet programu PowerShell służących do wdrażania usług platformy Azure i zarządzania nimi (takich jak Cloud Services i Virtual Machines). Emulatory platformy Azure to emulatory usług Cloud Services i zarządzania danymi, które umożliwiają przetestowanie aplikacji lokalnie. Te składniki są obsługiwane tylko dla systemu Windows.

Zalecanym sposobem instalacji Azure PowerShell i emulatorów platformy Azure jest użycie [Instalator platformy Microsoft Web][download-wpi]. Należy pamiętać, że można również zainstalować inne składniki programistyczne, takie jak PHP, SQL Server, Sterowniki firmy Microsoft dla SQL Server for PHP i WebMatrix.

Aby uzyskać informacje o sposobach korzystania z Azure PowerShell, zobacz [temat jak korzystać z Azure PowerShell][powershell-tools].

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure to zestaw poleceń umożliwiających wdrażanie usług platformy Azure, takich jak Azure Websites i Azure Virtual Machines, oraz zarządzanie nimi. Aby uzyskać informacje na temat instalowania interfejsu wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](cli-install-nodejs.md).

## <a name="next-steps"></a>Następne kroki

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
