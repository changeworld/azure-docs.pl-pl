---
title: Aplikacji node.js za pomocą biblioteki Socket.io - Azure
description: Dowiedz się, jak używać biblioteki socket.io w aplikacji node.js hostowanych na platformie Azure.
services: cloud-services
documentationcenter: nodejs
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 7f9435e0-7732-4aa1-a4df-ea0e894b847f
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: jeconnoc
ms.openlocfilehash: cd0bceae770182e778410d8065d34dfeed055acc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61433203"
---
# <a name="build-a-nodejs-chat-application-with-socketio-on-an-azure-cloud-service"></a>Tworzenie aplikacji czatu Node.js przy użyciu biblioteki Socket.IO w usłudze w chmurze platformy Azure

Biblioteka Socket.IO zapewnia komunikację realtime między zestaw node.js server a klientami. Ten samouczek przeprowadzi Cię przez gniazdo hostingu. We/Wy na podstawie aplikacji rozmów na platformie Azure. Aby uzyskać więcej informacji na temat biblioteki Socket.IO, zobacz [biblioteki socket.io](https://socket.io).

Zrzut ekranu ukończonej aplikacji znajduje się poniżej:

![Okno przeglądarki zawierające usługi hostowanej na platformie Azure][completed-app]  

## <a name="prerequisites"></a>Wymagania wstępne
Upewnij się, że następujące produkty i wersje są zainstalowane do pomyślnego ukończenia w przykładzie w tym artykule:

* Zainstalować program [Visual Studio](https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx)
* Zainstalować środowisko [Node.js](https://nodejs.org/download/).
* Zainstaluj [2.7.10 wersji języka Python](https://www.python.org/)

## <a name="create-a-cloud-service-project"></a>Tworzenie projektu usługi w chmurze
Poniższe kroki umożliwiają utworzenie projektu usługi w chmurze, który będzie hostem aplikacji biblioteki Socket.IO.

1. Z **Start Menu** lub **ekranu startowego**, wyszukaj **programu Windows PowerShell**. Na koniec kliknij prawym przyciskiem myszy **programu Windows PowerShell** i wybierz **Uruchom jako Administrator**.
   
    ![Ikona usługi Azure PowerShell][powershell-menu]
2. Utwórz katalog o nazwie **c:\\węzła**. 
   
        PS C:\> md node
3. Zmień katalog na **c:\\węzła** katalogu
   
        PS C:\> cd node
4. Wprowadź następujące polecenia, aby utworzyć nowe rozwiązanie o nazwie **chatapp** i roli proces roboczy o nazwie **WorkerRole1**:
   
        PS C:\node> New-AzureServiceProject chatapp
        PS C:\Node> Add-AzureNodeWorkerRole
   
    Zostanie wyświetlony następującą odpowiedź:
   
    ![Dane wyjściowe w nowy azureservice i Dodaj azurenodeworkerrolecmdlets](./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png)

## <a name="download-the-chat-example"></a>Pobierz przykład rozmowy
Dla tego projektu użyto przykładu rozmowy z [repozytorium GitHub biblioteki Socket.IO]. Wykonaj poniższe kroki, aby pobrać przykład i dodać go do projektu, który został wcześniej utworzony.

1. Utwórz kopię lokalną repozytorium przy użyciu **klonowania** przycisku. Można także użyć **ZIP** przycisk, aby wczytać projekt.
   
   ![Wyświetlanie okna przeglądarki https://github.com/LearnBoost/socket.io/tree/master/examples/chat, z wyróżnioną ikonę pobierania pliku ZIP](./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png)
2. Przejdź struktura katalogów w repozytorium lokalnym, dopóki nie zostanie wyświetlony **przykłady\\rozmowy** katalogu. Skopiuj zawartość tego katalogu, aby **C:\\węzła\\chatapp\\WorkerRole1** Katalog utworzony wcześniej.
   
   ![Eksplorator, wyświetlanie zawartość przykłady\\katalogu czatu wyodrębnione z archiwum][chat-contents]
   
   Wyróżnione na zrzucie ekranu powyżej pozycje plików skopiowanych z **przykłady\\rozmowy** katalogu
3. W **C:\\węzła\\chatapp\\WorkerRole1** katalogu, Usuń **server.js** pliku, a następnie zmień **app.js** plik **server.js**. Spowoduje to usunięcie domyślnie **server.js** plik utworzony wcześniej przez **AzureNodeWorkerRole Dodaj** polecenia cmdlet i zastępuje go przy użyciu pliku aplikacji z przykładu rozmowy.

### <a name="modify-serverjs-and-install-modules"></a>Modyfikowanie plików Server.js i instalowanie modułów
Przed testowaniem aplikacji w emulatorze platformy Azure, możemy wprowadzić niewielkimi modyfikacjami. Wykonaj poniższe kroki, aby pliku server.js:

1. Otwórz **server.js** pliku w programie Visual Studio lub dowolnego edytora tekstów.
2. Znajdź **zależności modułów** sekcji na początku server.js i zmień wiersz zawierający **sio = require('.. //.. lib//Socket.IO ")** do **sio = require('socket.io')** jak pokazano poniżej:
   
       var express = require('express')
         , stylus = require('stylus')
         , nib = require('nib')
       //, sio = require('..//..//lib//socket.io'); //Original
         , sio = require('socket.io');                //Updated
         var port = process.env.PORT || 3000;         //Updated
3. Aby upewnić się, aplikacja nasłuchuje na odpowiednim porcie, otwórz server.js w Notatniku lub w ulubionym edytorze, a następnie zmień następujący wiersz, zastępując **3000** z **process.env.port** jak pokazano poniżej:
   
       //app.listen(3000, function () {            //Original
       app.listen(process.env.port, function () {  //Updated
         var addr = app.address();
         console.log('   app listening on http://' + addr.address + ':' + addr.port);
       });

Po zapisaniu zmian **server.js**, wykonaj następujące kroki, aby zainstalować wymagane moduły, a następnie przetestuj aplikację w emulatorze platformy Azure:

1. Za pomocą **programu Azure PowerShell**, zmień katalog na **C:\\węzła\\chatapp\\WorkerRole1** katalogu i użyj następującego polecenia, aby zainstalować moduły wymagane przez tę aplikację:
   
       PS C:\node\chatapp\WorkerRole1> npm install
   
   Spowoduje to zainstalowanie modułów wymienione w pliku package.json. Po zakończeniu wykonywania polecenia powinny zostać wyświetlone dane wyjściowe podobne do następujących:
   
   ![Dane wyjściowe programu npm zainstalować polecenia][The-output-of-the-npm-install-command]
2. Ponieważ w tym przykładzie pierwotnie był częścią repozytorium GitHub biblioteki Socket.IO i bezpośrednie odwołanie do biblioteki biblioteki Socket.IO przy użyciu ścieżki względnej, biblioteki Socket.IO nie był przywoływany plik package.json, dlatego firma Microsoft musi zainstalować wydając polecenie:
   
       PS C:\node\chatapp\WorkerRole1> npm install socket.io --save

### <a name="test-and-deploy"></a>Wdrażanie i testowanie
1. Uruchom emulator, wykonując następujące polecenie:
   
       PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch
   
   > [!NOTE]
   > Jeśli wystąpią problemy z uruchomieniem emulatora, np.: Start-AzureEmulator: Wystąpił nieoczekiwany błąd.  Szczegóły: Napotkano nieoczekiwany błąd obiektu komunikacyjnego System.ServiceModel.Channels.ServiceChannel nie można użyć do komunikacji, ponieważ jest ona w stanie Faulted.
   > 
   > Zainstaluj ponownie AzureAuthoringTools v 2.7.1 i AzureComputeEmulator v 2.7 — upewnij się, że ta wersja jest zgodna.

2. Otwórz przeglądarkę i przejdź do **http://127.0.0.1**.
3. Gdy zostanie otwarte okno przeglądarki, wprowadź nazwę, a następnie naciśnij klawisz enter.
   Pozwoli to publikować wiadomości jako określonych pseudonim. Aby przetestować działanie wielu użytkowników, Otwórz dodatkowe okna przeglądarki przy użyciu tego samego adresu URL i wprowadź różne pseudonimy.
   
   ![Dwa okna przeglądarki, wyświetlanie wiadomości od użytkownika Użytkownik1 i Użytkownik2](./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png)
4. Po przetestowaniu aplikacji, należy zatrzymać emulator wydając polecenie:
   
       PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator
5. Aby wdrożyć aplikację na platformie Azure, użyj **Publish-AzureServiceProject** polecenia cmdlet. Na przykład:
   
       PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch
   
   > [!IMPORTANT]
   > Należy użyć unikatowej nazwy, w przeciwnym razie proces publikowania zakończy się niepowodzeniem. Po zakończeniu wdrożenia przeglądarce otworzy się i przejdź do wdrożonej usługi.
   > 
   > Jeśli zostanie wyświetlony komunikat o błędzie informujący, że nazwa podana subskrypcja nie istnieje w profilu publikowania importowanych, należy pobrać i zaimportować profil publikowania dla Twojej subskrypcji, przed wdrożeniem na platformie Azure. Zobacz **wdrażania aplikacji na platformie Azure** części [kompilowanie i wdrażanie aplikacji Node.js w usłudze w chmurze platformy Azure](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   > 
   > 
   
   ![Okno przeglądarki zawierające usługi hostowanej na platformie Azure][completed-app]
   
   > [!NOTE]
   > Jeśli zostanie wyświetlony komunikat o błędzie informujący, że nazwa podana subskrypcja nie istnieje w profilu publikowania importowanych, należy pobrać i zaimportować profil publikowania dla Twojej subskrypcji, przed wdrożeniem na platformie Azure. Zobacz **wdrażania aplikacji na platformie Azure** części [kompilowanie i wdrażanie aplikacji Node.js w usłudze w chmurze platformy Azure](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   > 
   > 

Aplikacja działa teraz na platformie Azure oraz możliwość przekazywania wiadomości na czacie między różnych klientów przy użyciu biblioteki Socket.IO.

> [!NOTE]
> Dla uproszczenia w tym przykładzie jest ograniczona do rozmowy między użytkowników podłączonych do tego samego wystąpienia. Oznacza to, że jeśli usługa w chmurze tworzy dwa wystąpienia ról procesów roboczych, użytkownicy tylko będą mogli rozmawiać z innymi osobami podłączone do tego samego wystąpienia roli procesu roboczego. Skalowanie aplikacji do pracy z wielu wystąpień ról, można użyć technologii, takich jak usługi Service Bus do udostępniania biblioteki Socket.IO przechowywanie stanu między wystąpieniami. Przykłady, zobacz przykłady użycia kolejek usługi Service Bus i tematy w [zestawu Azure SDK dla środowiska Node.js w usłudze GitHub repozytorium](https://github.com/WindowsAzure/azure-sdk-for-node).
> 
> 

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku przedstawiono sposób tworzenie aplikacji czatu podstawowe hostowanej w usłudze w chmurze systemu Azure. Aby dowiedzieć się, jak hostować tę aplikację w witrynie internetowej platformy Azure, zobacz [tworzenie aplikacji rozmów Node.js przy użyciu biblioteki Socket.IO w witrynie sieci Web Azure][chatwebsite].

Aby uzyskać więcej informacji, zobacz też [Centrum deweloperów środowiska Node.js](https://docs.microsoft.com/javascript/azure/?view=azure-node-latest).

[chatwebsite]: https://docs.microsoft.com/azure/cloud-services/cloud-services-nodejs-develop-deploy-app

[Azure SLA]: https://www.windowsazure.com/support/sla/
[Azure SDK for Node.js GitHub repository]: https://github.com/WindowsAzure/azure-sdk-for-node
[completed-app]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png
[Azure SDK for Node.js]: https://www.windowsazure.com/develop/nodejs/
[Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Repozytorium GitHub biblioteki Socket.IO]: https://github.com/LearnBoost/socket.io/tree/0.9.14
[Azure Considerations]: #windowsazureconsiderations
[Hosting the Chat Example in a Worker Role]: #hostingthechatexampleinawebrole
[Summary and Next Steps]: #summary
[powershell-menu]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png

[chat example]: https://github.com/LearnBoost/socket.io/tree/master/examples/chat
[chat-example-view]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png


[chat-contents]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-5.png
[The-output-of-the-npm-install-command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png
[The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-9.png


