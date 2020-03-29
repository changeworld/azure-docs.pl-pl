---
title: Tworzenie i wdrażanie aplikacji Node.js Express w usługach w chmurze azure
titleSuffix: Azure Cloud Services
description: Tworzenie i wdrażanie aplikacji Express.js w pliku Node.js w usługach w chmurze Azure
services: cloud-services
documentationcenter: nodejs
author: tgore03
ms.service: cloud-services
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: tagore
ms.openlocfilehash: 79a998930a384420b22add8825ee4b2269eb4539
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75360756"
---
# <a name="build-and-deploy-a-nodejs-web-application-using-express-on-an-azure-cloud-services"></a>Tworzenie i wdrażanie aplikacji sieci Web Node.js przy użyciu usługi Express w usługach w chmurze azure

Node.js zawiera minimalny zestaw funkcji w podstawowym czasie wykonywania.
Deweloperzy często używają modułów innych firm, aby zapewnić dodatkowe funkcje podczas tworzenia aplikacji Node.js. W tym samouczku utworzysz nową aplikację przy użyciu modułu [Express,](https://github.com/expressjs/express) który zapewnia strukturę MVC do tworzenia aplikacji sieci Web Node.js.

Zrzut ekranu wypełnionej aplikacji znajduje się poniżej:

![Przeglądarka internetowa wyświetlająca program Zapraszamy do wyrażenia express na platformie Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="create-a-cloud-service-project"></a>Tworzenie projektu usługi w chmurze
[!INCLUDE [install-dev-tools](../../includes/install-dev-tools.md)]

Wykonaj następujące kroki, aby utworzyć nowy projekt usługi w chmurze o nazwie "expressapp":

1. Z **menu Start** lub **ekranu startowego**wyszukaj **program Windows PowerShell**. Na koniec kliknij prawym przyciskiem myszy **program Windows PowerShell** i wybierz polecenie **Uruchom jako administrator**.
   
    ![Ikona programu Azure PowerShell](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)
2. Zmień katalogi na katalog **węzłów c:\\** i wprowadź następujące polecenia, aby utworzyć nowe rozwiązanie o nazwie **expressapp** i rolę sieci Web o nazwie **WebRole1:**
   
        PS C:\node> New-AzureServiceProject expressapp
        PS C:\Node\expressapp> Add-AzureNodeWebRole
        PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 Node 0.10.21
   
    > [!NOTE]
    > Domyślnie **dodatek AzureNodeWebRole** używa starszej wersji pliku Node.js. **Instrukcja Set-AzureProjectRole** powyżej nakazuje platformie Azure używanie wersji 0.10.21 węzła.  Należy zauważyć, że parametry są rozróżniane wielkość liter.  Można sprawdzić poprawną wersję pliku Node.js, sprawdzając właściwość **engines** w **aplikacji WebRole1\package.json**.
    > 
    > 

## <a name="install-express"></a>Instalowanie platformy Express
1. Zainstalować generator Express, wydając następujące polecenie:
   
        PS C:\node\expressapp> npm install express-generator -g
   
    Dane wyjściowe polecenia npm powinny wyglądać podobnie do poniższego wyniku. 
   
    ![Program Windows PowerShell wyświetlający dane wyjściowe polecenia npm install express.](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)
2. Zmień katalogi w katalogu **WebRole1** i użyj polecenia ekspresowego do wygenerowania nowej aplikacji:
   
        PS C:\node\expressapp\WebRole1> express
   
    Zostanie wyświetlony monit o zastąpienie wcześniejszej aplikacji. Wprowadź **y** lub **tak,** aby kontynuować. Express wygeneruje plik app.js i strukturę folderów do tworzenia aplikacji.
   
    ![Wyjście polecenia ekspresowego](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)
3. Aby zainstalować dodatkowe zależności zdefiniowane w pliku package.json, wprowadź następujące polecenie:
   
       PS C:\node\expressapp\WebRole1> npm install
   
   ![Dane wyjściowe polecenia instalacji npm](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)
4. Użyj następującego polecenia, aby skopiować plik **bin/www** do **pliku server.js**. Jest to tak, że usługa w chmurze można znaleźć punkt wejścia dla tej aplikacji.
   
       PS C:\node\expressapp\WebRole1> copy bin/www server.js
   
   Po zakończeniu tego polecenia w katalogu WebRole1 powinien znajdować się plik **server.js.**
5. Zmodyfikuj **plik server.js,** aby usunąć jeden ze znaków '.' z następującego wiersza.
   
       var app = require('../app');
   
   Po dokonaniu tej modyfikacji linia powinna być wyświetlana w następujący sposób.
   
       var app = require('./app');
   
   Ta zmiana jest wymagana, ponieważ przenieśliśmy plik (dawniej **bin/www**,) do tego samego katalogu, co wymagany plik aplikacji. Po dokonaniu tej zmiany zapisz plik **server.js.**
6. Użyj następującego polecenia, aby uruchomić aplikację w emulatorze platformy Azure:
   
       PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch
   
    ![Strona internetowa zawierająca zapraszamy do wyrażenia.](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## <a name="modifying-the-view"></a>Modyfikowanie widoku
Teraz zmodyfikuj widok, aby wyświetlić komunikat "Zapraszamy do wyrażenia na platformie Azure".

1. Wprowadź następujące polecenie, aby otworzyć plik index.jade:
   
       PS C:\node\expressapp\WebRole1> notepad views/index.jade
   
   ![Zawartość pliku index.jade.](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)
   
   Jade to domyślny aparat widoku używany przez aplikacje Express. Aby uzyskać więcej informacji na [http://jade-lang.com][http://jade-lang.com]temat silnika widoku Jade, zobacz .
2. Zmodyfikuj ostatni wiersz tekstu, dołączając go na **platformie Azure**.
   
   ![Plik index.jade, ostatni wiersz brzmi: \#p Witamy w {title} na platformie Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)
3. Zapisz plik i zamknij Notatnik.
4. Odśwież przeglądarkę, a zobaczysz zmiany.
   
   ![Okno przeglądarki, strona zawiera program Welcome to Express na platformie Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

Po przetestowaniu aplikacji użyj polecenia cmdlet **Stop-AzureEmulator,** aby zatrzymać emulator.

## <a name="publishing-the-application-to-azure"></a>Publikowanie aplikacji na platformie Azure
W oknie programu Azure PowerShell użyj polecenia cmdlet **Publish-AzureServiceProject,** aby wdrożyć aplikację w usłudze w chmurze

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

Po zakończeniu operacji wdrażania przeglądarka otworzy i wyświetli stronę internetową.

![Przeglądarka internetowa wyświetlająca stronę Express. Adres URL wskazuje, że jest teraz hostowany na platformie Azure.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji, odwiedź stronę [Centrum deweloperów środowiska Node.js](https://docs.microsoft.com/azure/javascript/).

[Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Express]: https://expressjs.com/
[http://jade-lang.com]: http://jade-lang.com





