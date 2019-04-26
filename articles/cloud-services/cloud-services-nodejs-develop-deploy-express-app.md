---
title: Tworzenie i wdrażanie aplikacji Node.js Express z usługami w chmurze platformy Azure
description: Tworzenie i wdrażanie aplikacji Express.js w środowisku Node.js w usłudze Azure Cloud Services
services: cloud-services
documentationcenter: nodejs
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 24f8e7ef-e90d-4554-9b1e-a9b31d5824e5
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: jeconnoc
ms.openlocfilehash: b212eaffb977846d40270a5f2abc76192aee4c0d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60528003"
---
# <a name="build-and-deploy-a-nodejs-web-application-using-express-on-an-azure-cloud-services"></a>Tworzenie i wdrażanie aplikacji internetowej Node.js za pomocą Express w usługach Azure Cloud Services

Node.js zawiera minimalny zestaw funkcjonalności w środowisku uruchomieniowym core.
Deweloperzy często używają 3 modułów innych firm oferowanie dodatkowych funkcji, podczas tworzenia aplikacji Node.js. W tym samouczku utworzysz nową aplikację, używając [Express](https://github.com/expressjs/express) moduł, który zawiera struktura MVC do tworzenia aplikacji sieci web w technologii Node.js.

Zrzut ekranu ukończonej aplikacji znajduje się poniżej:

![Przeglądarka wyświetlająca Witaj dla programu Express, platformie Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="create-a-cloud-service-project"></a>Tworzenie projektu usługi w chmurze
[!INCLUDE [install-dev-tools](../../includes/install-dev-tools.md)]

Wykonaj poniższe kroki, aby utworzyć nowy projekt usługi w chmurze o nazwie "expressapp":

1. Z **Start Menu** lub **ekranu startowego**, wyszukaj **programu Windows PowerShell**. Na koniec kliknij prawym przyciskiem myszy **programu Windows PowerShell** i wybierz **Uruchom jako Administrator**.
   
    ![Ikona usługi Azure PowerShell](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)
2. Zmień katalog na **c:\\węzła** katalogu, a następnie wprowadź następujące polecenia, aby utworzyć nowe rozwiązanie o nazwie **expressapp** oraz roli sieci web o nazwie **WebRole1**:
   
        PS C:\node> New-AzureServiceProject expressapp
        PS C:\Node\expressapp> Add-AzureNodeWebRole
        PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 Node 0.10.21
   
    > [!NOTE]
    > Domyślnie **Add-AzureNodeWebRole** używa starszej wersji środowiska node.js. **AzureServiceProjectRole zestaw** powyższych instrukcji powoduje, że platformy Azure, aby użyć v0.10.21 węzła.  Należy pamiętać, że parametrów jest rozróżniana wielkość liter.  Można sprawdzić poprawnej wersji Node.js został wybrany, sprawdzając **aparatów** właściwość **WebRole1\package.json**.
    > 
    > 

## <a name="install-express"></a>Zainstaluj program Express
1. Zainstaluj Generator aplikacji Express, wydając polecenie:
   
        PS C:\node\expressapp> npm install express-generator -g
   
    Dane wyjściowe polecenia npm powinien wyglądać podobnie do poniższych wyników. 
   
    ![Windows PowerShell, wyświetlanie npm zainstalować polecenia express.](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)
2. Zmień katalog na **WebRole1** katalogu i użyj express polecenie, aby wygenerować nową aplikację:
   
        PS C:\node\expressapp\WebRole1> express
   
    Zostanie wyświetlony monit zastąpienie starszych aplikacji. Wprowadź **y** lub **tak** aby kontynuować. Express spowoduje wygenerowanie pliku app.js i strukturę folderów podczas tworzenia aplikacji.
   
    ![Dane wyjściowe polecenia express](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)
3. Aby zainstalować dodatkowe zależności zdefiniowane w pliku package.json, wprowadź następujące polecenie:
   
       PS C:\node\expressapp\WebRole1> npm install
   
   ![Dane wyjściowe programu npm zainstalować polecenia](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)
4. Użyj następującego polecenia, aby skopiować **bin/www** plik **server.js**. Jest to, dzięki czemu usługa w chmurze można znaleźć punktu wejścia dla tej aplikacji.
   
       PS C:\node\expressapp\WebRole1> copy bin/www server.js
   
   Po wykonaniu tego polecenia, musisz mieć **server.js** pliku w katalogu WebRole1.
5. Modyfikowanie **server.js** można usunąć jednego z "." znaków z następującego wiersza.
   
       var app = require('../app');
   
   Po wprowadzeniu tej zmiany należy wierszu powinna wyglądać następująco.
   
       var app = require('./app');
   
   Ta zmiana jest wymagana, ponieważ przeszliśmy pliku (dawniej **bin/www**,) w tym samym katalogu co plik aplikacji jest wymagany. Po wprowadzeniu tej zmiany należy zapisać **server.js** pliku.
6. Aby uruchomić aplikację w emulatorze platformy Azure, użyj następującego polecenia:
   
       PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch
   
    ![Strona sieci web zawierający wprowadzenie do programu express.](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## <a name="modifying-the-view"></a>Modyfikowanie w widoku
Teraz zmodyfikuj widok, aby wyświetlić komunikat "Zapraszamy do Express w przypadku platformy Azure".

1. Wprowadź następujące polecenie, aby otworzyć plik index.jade:
   
       PS C:\node\expressapp\WebRole1> notepad views/index.jade
   
   ![Zawartość pliku index.jade.](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)
   
   Jade jest domyślny aparat widoku używany przez aplikacje Express. Aby uzyskać więcej informacji na temat aparatu Jade widoku, zobacz [ http://jade-lang.com ] [ http://jade-lang.com].
2. Zmodyfikuj ostatni wiersz tekstu, dodając **na platformie Azure**.
   
   ![Odczytuje ostatni wiersz w pliku index.jade: p — Zapraszamy! \#{title} na platformie Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)
3. Zapisz plik i zamknij Notatnik.
4. Odśwież przeglądarkę, a zobaczysz zmiany.
   
   ![Okno przeglądarki, strona zawiera Express na platformie Azure — Zapraszamy!](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

Po przetestowaniu aplikacji, należy użyć **Stop AzureEmulator** polecenie cmdlet do zatrzymywania emulatora.

## <a name="publishing-the-application-to-azure"></a>Publikowanie aplikacji na platformie Azure
W oknie programu PowerShell platformy Azure za pomocą **Publish-AzureServiceProject** polecenia cmdlet, aby wdrożyć aplikację do usługi w chmurze

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

Po zakończeniu operacji wdrażania przeglądarce otworzy i wyświetli tej strony sieci web.

![Przeglądarka wyświetlająca stronę Express. Adres URL wskazuje, że teraz jest obsługiwana na platformie Azure.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji, odwiedź stronę [Centrum deweloperów środowiska Node.js](https://docs.microsoft.com/javascript/azure/?view=azure-node-latest).

[Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Express]: https://expressjs.com/
[http://jade-lang.com]: http://jade-lang.com


