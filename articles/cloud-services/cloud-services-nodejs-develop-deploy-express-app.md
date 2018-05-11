---
title: Tworzenie i wdrażanie aplikacji Node.js Express z usługami w chmurze Azure
description: Tworzenie i wdrażanie aplikacji Express.js w środowisku Node.js do usługi w chmurze Azure
services: cloud-services
documentationcenter: nodejs
author: thraka
manager: timlt
editor: ''
ms.assetid: 24f8e7ef-e90d-4554-9b1e-a9b31d5824e5
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: adegeo
ms.openlocfilehash: 8bb78330591b0557b036a161eb83e47b41a8fd43
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="build-and-deploy-a-nodejs-web-application-using-express-on-an-azure-cloud-services"></a>Tworzenie i wdrażanie aplikacji sieci web Node.js za pomocą ekspresowego na usługi w chmurze Azure

Node.js zawiera minimalny zestaw funkcji w podstawowego środowiska wykonawczego.
Deweloperzy często używane 3 modułów strona oferowanie dodatkowych funkcji podczas opracowywania aplikacji Node.js. W tym samouczku spowoduje utworzenie nowej aplikacji przy użyciu [Express](https://github.com/expressjs/express) moduł, który zapewnia platformę MVC do tworzenia aplikacji sieci web Node.js.

Zrzut ekranu przedstawiający ukończona aplikacja znajduje się poniżej:

![Przeglądarka wyświetlająca Zapraszamy do Express na platformie Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="create-a-cloud-service-project"></a>Tworzenie projektu usługi w chmurze
[!INCLUDE [install-dev-tools](../../includes/install-dev-tools.md)]

Wykonaj poniższe kroki, aby utworzyć nowy projekt usługi w chmurze o nazwie "expressapp":

1. Z **Start Menu** lub **ekranu startowego**, wyszukaj **programu Windows PowerShell**. Na koniec kliknij prawym przyciskiem myszy **programu Windows PowerShell** i wybierz **Uruchom jako Administrator**.
   
    ![Ikona Azure PowerShell](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)
2. Przejdź do **c:\\węzła** katalogu, a następnie wprowadź następujące polecenia, aby utworzyć nowe rozwiązanie o nazwie **expressapp** i rolę sieci web o nazwie **WebRole1**:
   
        PS C:\node> New-AzureServiceProject expressapp
        PS C:\Node\expressapp> Add-AzureNodeWebRole
        PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 Node 0.10.21
   
    > [!NOTE]
    > Domyślnie **Add-AzureNodeWebRole** używa starszej wersji środowiska node.js. **AzureServiceProjectRole zestaw** powyższych instrukcji nakazuje Azure, aby użyć v0.10.21 węzła.  Należy zauważyć, że parametry jest rozróżniana wielkość liter.  Można sprawdzić poprawnej wersji środowiska Node.js została wybrana sprawdzając **aparaty** właściwości w **WebRole1\package.json**.
    > 
    > 

## <a name="install-express"></a>Zainstaluj Express
1. Generator aplikacji Express należy zainstalować wydając polecenie:
   
        PS C:\node\expressapp> npm install express-generator -g
   
    Dane wyjściowe polecenia npm powinien wyglądać podobnie do poniższych wyników. 
   
    ![Wyświetlanie npm środowiska Windows PowerShell instalacji ekspresowej polecenia.](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)
2. Przejdź do **WebRole1** katalogu i użyj polecenia express, aby wygenerować nową aplikację:
   
        PS C:\node\expressapp\WebRole1> express
   
    Pojawi się monit zastąpienie starszych aplikacji. Wprowadź **y** lub **tak** aby kontynuować. Express spowoduje wygenerowanie pliku app.js i struktury folderów, do tworzenia aplikacji.
   
    ![Dane wyjściowe polecenia express](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)
3. Aby zainstalować dodatkowe zależności zdefiniowane w pliku package.json, wprowadź następujące polecenie:
   
       PS C:\node\expressapp\WebRole1> npm install
   
   ![Dane wyjściowe programu npm install, polecenie](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)
4. Użyj następującego polecenia, aby skopiować **bin/www** pliku **server.js**. Jest to tak usługi w chmurze można znaleźć punktu wejścia dla tej aplikacji.
   
       PS C:\node\expressapp\WebRole1> copy bin/www server.js
   
   Po wykonaniu tego polecenia, powinien mieć **server.js** pliku w katalogu WebRole1.
5. Modyfikowanie **server.js** Aby usunąć jedną z '.' znaków z następującego wiersza.
   
       var app = require('../app');
   
   Po wprowadzeniu tej zmiany należy wiersz powinna wyglądać następująco.
   
       var app = require('./app');
   
   Ta zmiana jest wymagana, ponieważ przenieśliśmy pliku (dawniej **bin/www**,) na tym samym katalogu co plik aplikacji jest wymagany. Po wprowadzeniu tej zmiany należy zapisać **server.js** pliku.
6. Aby uruchomić aplikację w emulatorze platformy Azure, użyj następującego polecenia:
   
       PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch
   
    ![Strony sieci web zawierającej express — Zapraszamy!.](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## <a name="modifying-the-view"></a>Zmodyfikowanie widoku
Teraz zmodyfikuj widok, aby wyświetlić komunikat "Zapraszamy do Express w Azure".

1. Wprowadź następujące polecenie, aby otworzyć plik index.jade:
   
       PS C:\node\expressapp\WebRole1> notepad views/index.jade
   
   ![Zawartość pliku index.jade.](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)
   
   Jade jest domyślny aparat widoku używany przez aplikacje Express. Aby uzyskać więcej informacji na aparatu Jade widoku, zobacz [ http://jade-lang.com ] [ http://jade-lang.com].
2. Modyfikowanie ostatniego wiersza tekstu przez dołączenie **na platformie Azure**.
   
   ![Odczytuje ostatni wiersz w pliku index.jade: p — Zapraszamy! \#{nazwa} na platformie Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)
3. Zapisz plik i zamknij Notatnik.
4. Odśwież przeglądarkę i zobaczą zmiany.
   
   ![Okno przeglądarki, strona zawiera Express na platformie Azure — Zapraszamy!](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

Po zakończeniu testowania aplikacji, użyj **Stop AzureEmulator** polecenia cmdlet, aby zatrzymać emulator.

## <a name="publishing-the-application-to-azure"></a>Publikowanie aplikacji Azure
W oknie programu Azure PowerShell, użyj **Publish-AzureServiceProject** polecenia cmdlet, aby wdrożyć aplikację usługi w chmurze

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

Po zakończeniu operacji wdrażania, przeglądarki otworzyć i wyświetli tej strony sieci web.

![Przeglądarka wyświetlająca stronę Express. Adres URL wskazuje, że jest ona obecnie hostowana na platformie Azure.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji, odwiedź stronę [Centrum deweloperów środowiska Node.js](/develop/nodejs/).

[Node.js Web Application]: http://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Express]: http://expressjs.com/
[http://jade-lang.com]: http://jade-lang.com


