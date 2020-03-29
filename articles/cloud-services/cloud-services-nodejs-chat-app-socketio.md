---
title: Aplikacja Node.js używająca Socket.io — Azure
description: Dowiedz się, jak używać socket.io w aplikacji node.js hostowanej na platformie Azure.
services: cloud-services
documentationcenter: nodejs
author: tgore03
ms.service: cloud-services
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: tagore
ms.openlocfilehash: 0b515c630d8a3539cdab1df64b1925e9fcaf206e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75360773"
---
# <a name="build-a-nodejs-chat-application-with-socketio-on-an-azure-cloud-service"></a>Tworzenie aplikacji do czatu Node.js z Socket.IO w usłudze w chmurze azure

Socket.IO zapewnia komunikację w czasie rzeczywistym między serwerem node.js a klientami. Ten samouczek przeprowadzi Cię przez hosting gniazda. Aplikacja do czatu oparta na usługach We/Wy na platformie Azure. Aby uzyskać więcej informacji na temat Socket.IO, zobacz [socket.io](https://socket.io).

Zrzut ekranu wypełnionej aplikacji znajduje się poniżej:

![Okno przeglądarki z usługą hostowana na platformie Azure][completed-app]  

## <a name="prerequisites"></a>Wymagania wstępne
Upewnij się, że następujące produkty i wersje są zainstalowane, aby pomyślnie zakończyć przykład w tym artykule:

* Instalowanie [programu Visual Studio](https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx)
* Instalowanie [pliku Node.js](https://nodejs.org/download/)
* Instalowanie [języka Python w wersji 2.7.10](https://www.python.org/)

## <a name="create-a-cloud-service-project"></a>Tworzenie projektu usługi w chmurze
Poniższe kroki tworzą projekt usługi w chmurze, który będzie obsługiwać Socket.IO aplikacji.

1. Z **menu Start** lub **ekranu startowego**wyszukaj **program Windows PowerShell**. Na koniec kliknij prawym przyciskiem myszy **program Windows PowerShell** i wybierz polecenie **Uruchom jako administrator**.
   
    ![Ikona programu Azure PowerShell][powershell-menu]
2. Tworzenie katalogu o nazwie **c:\\node**. 
   
        PS C:\> md node
3. Zmienianie katalogów na katalog **węzłów c:\\**
   
        PS C:\> cd node
4. Wprowadź następujące polecenia, aby utworzyć nowe rozwiązanie o nazwie **chatapp** i rolę pracownika o nazwie **WorkerRole1:**
   
        PS C:\node> New-AzureServiceProject chatapp
        PS C:\Node> Add-AzureNodeWorkerRole
   
    Zostanie wyświetlna następująca odpowiedź:
   
    ![Dane wyjściowe nowych azureservice i add-azurenodeworkerrolecmdlets](./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png)

## <a name="download-the-chat-example"></a>Pobierz przykład czatu
W tym projekcie użyjemy przykładu czatu z [repozytorium Socket.IO GitHub]. Wykonaj następujące kroki, aby pobrać przykład i dodać go do projektu, który został wcześniej utworzony.

1. Utwórz lokalną kopię repozytorium za pomocą przycisku **Klonuj.** Możesz również użyć przycisku **ZIP,** aby pobrać projekt.
   
   ![Wyświetlanie https://github.com/LearnBoost/socket.io/tree/master/examples/chatokna przeglądarki z wyróżnioną ikoną pobierania ZIP](./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png)
2. Przejdź do struktury katalogów lokalnego repozytorium, aż dojdziesz do **przykładowego\\katalogu czatu.** Skopiuj zawartość tego katalogu do utworzonego wcześniej katalogu **C:\\node\\chatapp\\WorkerRole1.**
   
   ![Explorer, wyświetlanie zawartości przykładowego\\katalogu czatu wyodrębnione z archiwum][chat-contents]
   
   Wyróżnione elementy na powyższym zrzucie ekranu to pliki skopiowane z **przykładowego\\** katalogu czatu
3. W katalogu **\\C: node\\\\chatapp WorkerRole1** usuń plik **server.js,** a następnie zmień nazwę pliku **app.js** na **server.js**. Spowoduje to usunięcie domyślnego pliku **server.js** utworzonego wcześniej przez polecenie cmdlet **Add-AzureNodeWorkerRole** i zastąpienie go plikiem aplikacji z przykładu czatu.

### <a name="modify-serverjs-and-install-modules"></a>Modyfikowanie modułów Server.js i instalacyjnych
Przed przetestowaniem aplikacji w emulatorze platformy Azure należy wprowadzić pewne drobne modyfikacje. Wykonaj następujące kroki w pliku server.js:

1. Otwórz plik **server.js** w programie Visual Studio lub dowolnym edytorze tekstu.
2. Znajdź sekcję **Zależności modułu** na początku server.js i zmień wiersz zawierający **sio = require('.. //.. lib//socket.io')** do **sio = require('socket.io'),** jak pokazano poniżej:
   
       var express = require('express')
         , stylus = require('stylus')
         , nib = require('nib')
       //, sio = require('..//..//lib//socket.io'); //Original
         , sio = require('socket.io');                //Updated
         var port = process.env.PORT || 3000;         //Updated
3. Aby upewnić się, że aplikacja nasłuchuje na właściwym porcie, otwórz plik server.js w Notatniku lub ulubionym edytorze, a następnie zmień następujący wiersz, zastępując **3000** **process.env.port,** jak pokazano poniżej:
   
       //app.listen(3000, function () {            //Original
       app.listen(process.env.port, function () {  //Updated
         var addr = app.address();
         console.log('   app listening on http://' + addr.address + ':' + addr.port);
       });

Po zapisaniu zmian w **pliku server.js**należy wykonać następujące czynności, aby zainstalować wymagane moduły, a następnie przetestować aplikację w emulatorze platformy Azure:

1. Za pomocą **programu Azure PowerShell**, zmienić katalogi do katalogu **C:\\\\node chatapp\\WorkerRole1** i użyć następującego polecenia, aby zainstalować moduły wymagane przez tę aplikację:
   
       PS C:\node\chatapp\WorkerRole1> npm install
   
   Spowoduje to zainstalowanie modułów wymienionych w pliku package.json. Po wykonaniu tego polecenia dane wyjściowe powinny wyglądać mniej więcej tak:
   
   ![Dane wyjściowe polecenia instalacji npm][The-output-of-the-npm-install-command]
2. Ponieważ ten przykład był pierwotnie częścią repozytorium Socket.IO GitHub i bezpośrednio odwoływał się do biblioteki Socket.IO ścieżką względną, Socket.IO nie był odwoływany do pliku package.json, więc musimy go zainstalować, wydając następujące polecenie:
   
       PS C:\node\chatapp\WorkerRole1> npm install socket.io --save

### <a name="test-and-deploy"></a>Testowanie i wdrażanie
1. Uruchom emulator, wydając następujące polecenie:
   
       PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch
   
   > [!NOTE]
   > Jeśli wystąpią problemy z uruchamianiem emulatora, np.: Start-AzureEmulator: Wystąpił nieoczekiwany błąd.  Szczegóły: Napotkał nieoczekiwany błąd Obiekt komunikacji, System.ServiceModel.Channels.ServiceChannel, nie może być używany do komunikacji, ponieważ jest w stanie faulted.
   > 
   > Zainstaluj ponownie narzędzia AzureAuthoringTools w wersji 2.7.1 i AzureComputeEmulator v 2.7 — upewnij się, że wersja jest zgodna.

2. Otwórz przeglądarkę i **http://127.0.0.1**przejdź do pliku .
3. Po otwarciu okna przeglądarki wprowadź pseudonim, a następnie naciśnij enter.
   Pozwoli to na publikowanie wiadomości jako określonego pseudonimu. Aby przetestować funkcjonalność wielu użytkowników, otwórz dodatkowe okna przeglądarki przy użyciu tego samego adresu URL i wprowadź różne pseudonimy.
   
   ![Dwa okna przeglądarki wyświetlające wiadomości czatu od użytkownika1 i użytkownika2](./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png)
4. Po przetestowaniu aplikacji zatrzymaj emulator, wydając następujące polecenie:
   
       PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator
5. Aby wdrożyć aplikację na platformie Azure, należy użyć polecenia cmdlet **Publish-AzureServiceProject.** Przykład:
   
       PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch
   
   > [!IMPORTANT]
   > Pamiętaj, aby użyć unikatowej nazwy, w przeciwnym razie proces publikowania zakończy się niepowodzeniem. Po zakończeniu wdrażania przeglądarka otworzy się i przejdzie do wdrożonej usługi.
   > 
   > Jeśli zostanie wyświetlony błąd informujący, że podana nazwa subskrypcji nie istnieje w zaimportowanym profilu publikowania, należy pobrać i zaimportować profil publikowania dla subskrypcji przed wdrożeniem na platformie Azure. Zobacz **sekcję Wdrażanie aplikacji na platformie Azure** [kompilacji i wdrażanie aplikacji Node.js w usłudze azure cloud service](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   > 
   > 
   
   ![Okno przeglądarki z usługą hostowana na platformie Azure][completed-app]
   
   > [!NOTE]
   > Jeśli zostanie wyświetlony błąd informujący, że podana nazwa subskrypcji nie istnieje w zaimportowanym profilu publikowania, należy pobrać i zaimportować profil publikowania dla subskrypcji przed wdrożeniem na platformie Azure. Zobacz **sekcję Wdrażanie aplikacji na platformie Azure** [kompilacji i wdrażanie aplikacji Node.js w usłudze azure cloud service](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   > 
   > 

Aplikacja jest teraz uruchomiona na platformie Azure i może przekazywać wiadomości czatu między różnymi klientami przy użyciu Socket.IO.

> [!NOTE]
> Dla uproszczenia ten przykład jest ograniczony do rozmów między użytkownikami podłączonymi do tego samego wystąpienia. Oznacza to, że jeśli usługa w chmurze tworzy dwa wystąpienia roli procesu roboczego, użytkownicy będą mogli rozmawiać tylko z innymi osobami połączonymi z tym samym wystąpieniem roli procesu roboczego. Aby skalować aplikację do pracy z wieloma wystąpieniami roli, można użyć technologii, takiej jak usługa Service Bus, aby udostępnić stan Socket.IO magazynu w różnych wystąpieniach. Na przykład zobacz przykłady użycia kolejek i tematów usługi Service Bus w pliku [SDK azure dla repozytorium Node.js GitHub](https://github.com/WindowsAzure/azure-sdk-for-node).
> 
> 

## <a name="next-steps"></a>Następne kroki
W tym samouczku dowiesz się, jak utworzyć podstawową aplikację czatu hostowana w usłudze Azure Cloud Service. Aby dowiedzieć się, jak hostować tę aplikację w witrynie sieci Web platformy Azure, zobacz [Tworzenie aplikacji czatu node.js z Socket.IO w witrynie sieci Web platformy Azure.][chatwebsite]

Aby uzyskać więcej informacji, zobacz także [Node.js Developer Center](https://docs.microsoft.com/azure/javascript/).

[chatwebsite]: https://docs.microsoft.com/azure/cloud-services/cloud-services-nodejs-develop-deploy-app

[Azure SLA]: https://www.windowsazure.com/support/sla/
[Azure SDK for Node.js GitHub repository]: https://github.com/WindowsAzure/azure-sdk-for-node
[completed-app]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png
[Azure SDK for Node.js]: https://www.windowsazure.com/develop/nodejs/
[Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Socket.IO repozytorium GitHub]: https://github.com/LearnBoost/socket.io/tree/0.9.14
[Azure Considerations]: #windowsazureconsiderations
[Hosting the Chat Example in a Worker Role]: #hostingthechatexampleinawebrole
[Summary and Next Steps]: #summary
[powershell-menu]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png

[chat example]: https://github.com/LearnBoost/socket.io/tree/master/examples/chat
[chat-example-view]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png


[chat-contents]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-5.png
[The-output-of-the-npm-install-command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png
[The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-9.png





