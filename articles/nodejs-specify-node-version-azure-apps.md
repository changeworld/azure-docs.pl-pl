---
title: Określanie wersji środowiska Node.js
description: Dowiedz się, jak można określić wersji środowiska node.js używane przez witryny sieci Web platformy Azure i usługi w chmurze
services: ''
documentationcenter: nodejs
author: TomArcher
manager: routlaw
editor: ''
ms.assetid: d0e15278-2ab4-4ec8-8256-913839c6d5ef
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2016
ms.author: tarcher
ms.openlocfilehash: a20179c72b227deb14df442bea7b80cf31728aa7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23864301"
---
# <a name="specifying-a-nodejs-version-in-an-azure-application"></a>Specifying a Node.js version in an Azure application (Określanie wersji środowiska Node.js w aplikacji Azure)
Hosting aplikacji Node.js, można zapewnić, że aplikacja korzysta z określonej wersji środowiska node.js. Istnieje kilka sposobów, w tym celu dla aplikacji hostowanej na platformie Azure.

## <a name="default-versions"></a>Wersji domyślnej
Wersje Node.js dostarczany przez platformę Azure są stale aktualizowane. Inaczej, domyślna wersja określona w `WEBSITE_NODE_DEFAULT_VERSION` będzie można użyć zmiennej środowiskowej. Aby zastąpić wartość domyślną, wykonaj czynności opisane w poniższych sekcjach tego artykułu

> [!NOTE]
> Jeśli przechowujesz aplikacji usługi w chmurze Azure (rola sieci web lub procesu roboczego) i jest przy pierwszym wdrożeniu aplikacji, Azure będzie podejmować próby używana ta sama wersja środowiska node.js, ponieważ został zainstalowany na środowiska projektowego, jeśli jest to jeden t HE domyślne wersje dostępnych na platformie Azure.
>
>

## <a name="versioning-with-packagejson"></a>Przechowywanie wersji pliku package.json
Można określić wersji środowiska node.js do użycia, dodając następujące polecenie, aby Twoje **package.json** pliku:

    "engines":{"node":version}

Gdzie *wersji* jest numerem wersji do użycia. Można określić bardziej skomplikowane warunki dotyczące wersji, takich jak:

    "engines":{"node": "0.6.22 || 0.8.x"}

Ponieważ 0.6.22 nie jest jedną z dostępnych w środowisku macierzystym wersji, jest najwyższa wersja 0,8, serii, która jest dostępna, zostanie użyty - 0.8.4.

## <a name="versioning-websites-with-app-settings"></a>Przechowywanie wersji witryn sieci Web przy użyciu ustawień aplikacji
Jeśli przechowujesz aplikacji w witrynie sieci Web, można ustawić zmiennej środowiskowej **WEBSITE_NODE_DEFAULT_VERSION** do żądanej wersji.

## <a name="versioning-cloud-services-with-powershell"></a>Przechowywanie wersji usługi w chmurze przy użyciu programu PowerShell
Jeśli są hosting aplikacji w usłudze w chmurze i wdrażania aplikacji przy użyciu programu Azure PowerShell, można zastąpić domyślną wersję środowiska Node.js przy użyciu **AzureServiceProjectRole zestaw** polecenia cmdlet programu PowerShell. Na przykład:

    Set-AzureServiceProjectRole WebRole1 Node 0.8.4

Należy zauważyć, że parametry w powyższych instrukcji jest rozróżniana wielkość liter.  Można sprawdzić poprawnej wersji środowiska Node.js została wybrana sprawdzając **aparaty** właściwości w roli użytkownika **package.json**.

Można również użyć **Get AzureServiceProjectRoleRuntime** można pobrać listy wersji środowiska Node.js dostępnych dla aplikacji hostowanej jako usługa w chmurze.  Zawsze, czy wersja programu Node.js zależy od projektu jest na tej liście.

## <a name="using-a-custom-version-with-azure-websites"></a>Przy użyciu dostosowanej wersji z witrynami sieci Web Azure
Podczas gdy platforma Azure udostępnia kilka domyślnej wersji środowiska Node.js, warto Użyj wersji, która domyślnie nie jest dostępne. Jeśli aplikacja jest obsługiwana jako witryny sieci Web platformy Azure, możesz to zrobić przy użyciu **plik iisnode.yml** pliku. W poniższych krokach objaśniono proces przy użyciu dostosowanej wersji środowiska Node.Js z witryny sieci Web platformy Azure:

1. Utwórz nowy katalog, a następnie utwórz **server.js** pliku w katalogu. **Server.js** plik powinien zawierać następujące:

        var http = require('http');
        http.createServer(function(req,res) {
          res.writeHead(200, {'Content-Type': 'text/html'});
          res.end('Hello from Azure running node version: ' + process.version + '</br>');
        }).listen(process.env.PORT || 3000);

    Spowoduje to wyświetlenie wersji środowiska Node.js, używane podczas przeglądania witryny sieci Web.
2. Utwórz nową witrynę sieci Web i zanotuj nazwę witryny. Na przykład następujące zastosowania [narzędzi wiersza polecenia platformy Azure] do tworzenia nowej witryny internetowej platformy Azure o nazwie **MojaWitrynaSieciWeb**, a następnie włącz repozytorium Git dla witryny sieci Web.

        azure site create mywebsite --git
3. Utwórz nowy katalog o nazwie **bin** jako element podrzędny katalog zawierający **server.js** pliku.
4. Pobierz określoną wersję **node.exe** (wersja systemu Windows), które mają być używane z aplikacji. Na przykład następujące zastosowania **curl** w celu pobrania wersji 0.8.1:

        curl -O http://nodejs.org/dist/v0.8.1/node.exe

    Zapisz **node.exe** pliku do **bin** folder utworzony wcześniej.
5. Utwórz **plik iisnode.yml** pliku w tym samym katalogu co **server.js** pliku, a następnie dodaj następującą zawartość do **plik iisnode.yml** pliku:

        nodeProcessCommandLine: "D:\home\site\wwwroot\bin\node.exe"

    Ta ścieżka jest where **node.exe** pliku w projekcie zostaną umieszczone po opublikowaniu aplikacji do witryny sieci Web platformy Azure.
6. Publikowanie aplikacji. Na przykład od I utworzenia nowej witryny sieci Web z parametrem--git wcześniej, następujące polecenia spowoduje dodanie plików aplikacji do mojego lokalnego repozytorium Git i wypychanie ich do repozytorium witryny sieci Web:

        git add .
        git commit -m "testing node v0.8.1"
        git push azure master

    Po opublikowaniu aplikacji ma Otwórz witrynę sieci Web w przeglądarce. Powinien zostać wyświetlony komunikat z informacją "Hello Azure uruchomionej wersji węzła: v0.8.1".

## <a name="next-steps"></a>Następne kroki
Teraz, że rozumiesz sposób Określ wersję środowiska Node.js używanych przez aplikację, Dowiedz się, jak [Praca z modułami], [tworzenia i wdrażania witryn sieci Web Node.js](app-service/app-service-web-get-started-nodejs.md), i [sposobu korzystania z platformy Azure Narzędzia wiersza polecenia dla komputerów Mac i Linux].

Aby uzyskać więcej informacji, odwiedź stronę [Centrum deweloperów środowiska Node.js](https://azure.microsoft.com/develop/nodejs/).

[sposobu korzystania z platformy Azure Narzędzia wiersza polecenia dla komputerów Mac i Linux]:cli-install-nodejs.md
[narzędzi wiersza polecenia platformy Azure]:cli-install-nodejs.md
[Praca z modułami]: nodejs-use-node-modules-azure-apps.md
[build and deploy a Node.js Web Site]: app-service/app-service-web-get-started-nodejs.md
