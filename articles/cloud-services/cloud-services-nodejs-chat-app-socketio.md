---
title: Aplikacji node.js za pomocą użyciu biblioteki Socket.io - Azure
description: Dowiedz się, jak używać użyciu biblioteki socket.io w aplikacji node.js hostowanej na platformie Azure.
services: cloud-services
documentationcenter: nodejs
author: thraka
manager: timlt
editor: ''
ms.assetid: 7f9435e0-7732-4aa1-a4df-ea0e894b847f
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: adegeo
ms.openlocfilehash: b5d706f82dd41f0c271aa5f67676bba08cceb3b1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34608597"
---
# <a name="build-a-nodejs-chat-application-with-socketio-on-an-azure-cloud-service"></a>Tworzenie aplikacji czatu Node.js przy użyciu biblioteki Socket.IO w usługi w chmurze platformy Azure

Użyciu biblioteki Socket.IO zapewnia czasu rzeczywistego komunikację między serwerem środowiska node.js i klientami. Ten samouczek przedstawia za pośrednictwem obsługi gniazda. We/Wy na podstawie rozmów aplikacji na platformie Azure. Aby uzyskać więcej informacji o użyciu biblioteki Socket.IO, zobacz [użyciu biblioteki socket.io](http://socket.io).

Zrzut ekranu przedstawiający ukończona aplikacja znajduje się poniżej:

![Okno przeglądarki zawierające usługi hostowanej na platformie Azure][completed-app]  

## <a name="prerequisites"></a>Wymagania wstępne
Upewnij się, że do pomyślnego ukończenia w przykładzie w tym artykule są zainstalowane następujące produkty i wersje:

* Zainstalować program [Visual Studio](https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx)
* Zainstalować środowisko [Node.js](https://nodejs.org/download/).
* Zainstaluj [2.7.10 wersji języka Python](https://www.python.org/)

## <a name="create-a-cloud-service-project"></a>Tworzenie projektu usługi w chmurze
Poniższe kroki tworzenia projektu usługi w chmurze, który będzie hostem aplikacji użyciu biblioteki Socket.IO.

1. Z **Start Menu** lub **ekranu startowego**, wyszukaj **programu Windows PowerShell**. Na koniec kliknij prawym przyciskiem myszy **programu Windows PowerShell** i wybierz **Uruchom jako Administrator**.
   
    ![Ikona Azure PowerShell][powershell-menu]
2. Utwórz katalog o nazwie **c:\\węzła**. 
   
        PS C:\> md node
3. Przejdź do **c:\\węzła** katalogu
   
        PS C:\> cd node
4. Wprowadź następujące polecenia, aby utworzyć nowe rozwiązanie o nazwie **chatapp** i rolę procesu roboczego o nazwie **WorkerRole1**:
   
        PS C:\node> New-AzureServiceProject chatapp
        PS C:\Node> Add-AzureNodeWorkerRole
   
    Zostanie wyświetlony następujący odpowiedzi:
   
    ![Dane wyjściowe nowy azureservice i Dodaj azurenodeworkerrolecmdlets](./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png)

## <a name="download-the-chat-example"></a>Pobierz przykład rozmowy
Dla tego projektu, użyjemy przykład rozmów z [repozytorium GitHub użyciu biblioteki Socket.IO]. Wykonaj poniższe kroki, aby pobrać przykład i dodaj go do projektu, utworzonych wcześniej.

1. Utwórz kopię lokalną repozytorium przy użyciu **klonowania** przycisku. Można także użyć **ZIP** przycisk, aby pobrać projekt.
   
   ![Wyświetlanie okna przeglądarki https://github.com/LearnBoost/socket.io/tree/master/examples/chat, z ikoną pobierania ZIP wyróżnione][chat-example-view]
2. Przejdź struktury katalogu lokalnego repozytorium, dopóki przyjeździe **przykłady\\rozmów** katalogu. Skopiuj zawartość tego katalogu, aby **C:\\węzła\\chatapp\\WorkerRole1** Katalog utworzony wcześniej.
   
   ![Eksplorator, wyświetlania zawartości przykłady\\katalogu rozmów wyodrębnione z archiwum][chat-contents]
   
   Zaznaczone elementy na zrzucie ekranu powyżej znajdują się pliki kopiowane z **przykłady\\rozmów** katalogu
3. W **C:\\węzła\\chatapp\\WorkerRole1** katalogu, Usuń **server.js** pliku, a następnie zmień **app.js** pliku **server.js**. Spowoduje to usunięcie domyślnie **server.js** plik utworzony wcześniej przez **AzureNodeWorkerRole Dodaj** polecenia cmdlet i zastępuje go z plikiem aplikacji z przykładu rozmów.

### <a name="modify-serverjs-and-install-modules"></a>Modyfikowanie Server.js i zainstalować moduły
Przed testowaniem aplikacji w emulatorze platformy Azure, firma Microsoft wprowadzać niektórych drobne zmiany. Wykonaj poniższe kroki, aby pliku server.js:

1. Otwórz **server.js** pliku w Visual Studio lub w dowolnym edytorze tekstów.
2. Znajdź **zależności modułu** sekcji na początku server.js i zmień wiersz zawierający **sio = require('.. //.. lib//Socket.IO ")** do **sio = require('socket.io')** w sposób przedstawiony poniżej:
   
       var express = require('express')
         , stylus = require('stylus')
         , nib = require('nib')
       //, sio = require('..//..//lib//socket.io'); //Original
         , sio = require('socket.io');                //Updated
         var port = process.env.PORT || 3000;         //Updated
3. Aby upewnić się, aplikacja nasłuchuje właściwego portu, Otwórz w Notatniku lub ulubionego edytora server.js, a następnie zmień następujący wiersz zastępując **3000** z **process.env.port** w sposób przedstawiony poniżej:
   
       //app.listen(3000, function () {            //Original
       app.listen(process.env.port, function () {  //Updated
         var addr = app.address();
         console.log('   app listening on http://' + addr.address + ':' + addr.port);
       });

Po zapisaniu zmian do **server.js**, wykonaj następujące kroki, aby zainstalować wymagane moduły, a następnie przetestować aplikację w emulatorze platformy Azure:

1. Przy użyciu **programu Azure PowerShell**, przejdź do **C:\\węzła\\chatapp\\WorkerRole1** katalogu i użyj następujące polecenie, aby zainstalować moduły wymagane przez tę aplikację:
   
       PS C:\node\chatapp\WorkerRole1> npm install
   
   Spowoduje to zainstalowanie modułów wymieniony w pliku package.json. Po zakończeniu wykonywania polecenia, powinny być widoczne dane wyjściowe podobne do następującego:
   
   ![Dane wyjściowe programu npm install, polecenie][The-output-of-the-npm-install-command]
2. Ponieważ w tym przykładzie pierwotnie częścią repozytorium GitHub użyciu biblioteki Socket.IO i bezpośrednie odwołanie do biblioteki użyciu biblioteki Socket.IO przez ścieżkę względną, użyciu biblioteki Socket.IO nie został przywołany w pliku package.json, więc trzeba zainstalować wydając polecenie:
   
       PS C:\node\chatapp\WorkerRole1> npm install socket.io --save

### <a name="test-and-deploy"></a>Testowania i wdrażania
1. Uruchom emulator, wykonując następujące polecenie:
   
       PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch
   
   > [!NOTE]
   > Jeśli wystąpią problemy związane z uruchamianie emulatora, np.: Start AzureEmulator: Wystąpił nieoczekiwany błąd.  Szczegóły: Napotkano nieoczekiwany błąd obiektu komunikacji System.ServiceModel.Channels.ServiceChannel, nie można użyć do komunikacji, ponieważ jest w stanie Faulted.
   
      Zainstaluj ponownie AzureAuthoringTools v 2.7.1 i AzureComputeEmulator v 2.7 - upewnij się, że ta wersja jest zgodna.
   >
   >


2. Otwórz przeglądarkę i przejdź do **http://127.0.0.1**.
3. Po otwarciu okna przeglądarki, wprowadź pseudonim, a następnie naciśnij klawisz enter.
   Umożliwi to publikowania wiadomości jako określonych pseudonim. Aby przetestować funkcje wielu użytkowników, Otwórz dodatkowe okna przeglądarki przy użyciu tego samego adresu URL i wprowadź inny pseudonimy.
   
   ![Wyświetlanie wiadomości od użytkowników Użytkownik1 i Użytkownik2 dwa okna przeglądarki](./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png)
4. Po zakończeniu testowania aplikacji, należy zatrzymać emulator wydając polecenie:
   
       PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator
5. Aby wdrożyć aplikację na platformie Azure, użyj **Publish-AzureServiceProject** polecenia cmdlet. Na przykład:
   
       PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch
   
   > [!IMPORTANT]
   > Należy użyć unikatową nazwę, w przeciwnym razie proces publikowania zakończy się niepowodzeniem. Po zakończeniu wdrożenia przeglądarki otworzyć i przejdź do wdrożonej usługi.
   > 
   > Jeśli zostanie wyświetlony komunikat o błędzie informujący, że nazwa podana subskrypcja nie istnieje w profilu publikowania importowane, musisz pobrać i zaimportować profil publikowania dla subskrypcji przed wdrożeniem na platformie Azure. Zobacz **wdrażanie aplikacji na platformie Azure** sekcji [tworzenia i wdrażania aplikacji Node.js do usługi w chmurze platformy Azure](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   > 
   > 
   
   ![Okno przeglądarki zawierające usługi hostowanej na platformie Azure][completed-app]
   
   > [!NOTE]
   > Jeśli zostanie wyświetlony komunikat o błędzie informujący, że nazwa podana subskrypcja nie istnieje w profilu publikowania importowane, musisz pobrać i zaimportować profil publikowania dla subskrypcji przed wdrożeniem na platformie Azure. Zobacz **wdrażanie aplikacji na platformie Azure** sekcji [tworzenia i wdrażania aplikacji Node.js do usługi w chmurze platformy Azure](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   > 
   > 

Aplikacja jest teraz uruchomiona na platformie Azure i możliwość przekazywania wiadomości między różnych klientów przy użyciu użyciu biblioteki Socket.IO.

> [!NOTE]
> Dla uproszczenia w tym przykładzie jest ograniczona do rozmowy między użytkowników podłączonych do tego samego wystąpienia. Oznacza to, że jeśli usługa w chmurze tworzy dwa wystąpienia roli procesu roboczego, użytkownicy będą mieć tylko rozmawiać z innymi podłączone do tego samego wystąpienia roli procesu roboczego. Do skalowania aplikacji do pracy z wielu wystąpień ról, można użyć technologii, takich jak usługi Service Bus do udostępniania stanu magazynu użyciu biblioteki Socket.IO w wystąpieniach. Przykłady można znaleźć przykłady użycia tematów i kolejek usługi Service Bus w [zestawu Azure SDK dla repozytorium Node.js GitHub](https://github.com/WindowsAzure/azure-sdk-for-node).
> 
> 

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku przedstawiono sposób tworzenia aplikacji rozmów podstawowe hostowanych w usłudze w chmurze Azure. Aby uzyskać informacje o obsłudze tej aplikacji w witrynie sieci Web platformy Azure, zobacz [tworzenie aplikacji czatu Node.js przy użyciu biblioteki Socket.IO w witrynie sieci Web Azure][chatwebsite].

Aby uzyskać więcej informacji, zobacz też [Centrum deweloperów środowiska Node.js](https://docs.microsoft.com/javascript/azure/?view=azure-node-latest).

[chatwebsite]: https://docs.microsoft.com/azure/cloud-services/cloud-services-nodejs-develop-deploy-app

[Azure SLA]: http://www.windowsazure.com/support/sla/
[Azure SDK for Node.js GitHub repository]: https://github.com/WindowsAzure/azure-sdk-for-node
[completed-app]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png
[Azure SDK for Node.js]: https://www.windowsazure.com/develop/nodejs/
[Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Repozytorium GitHub użyciu biblioteki Socket.IO]: https://github.com/LearnBoost/socket.io/tree/0.9.14
[Azure Considerations]: #windowsazureconsiderations
[Hosting the Chat Example in a Worker Role]: #hostingthechatexampleinawebrole
[Summary and Next Steps]: #summary
[powershell-menu]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png

[chat example]: https://github.com/LearnBoost/socket.io/tree/master/examples/chat
[chat-example-view]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png


[chat-contents]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-5.png
[The-output-of-the-npm-install-command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png
[The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-9.png


