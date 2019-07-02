---
title: Uruchamianie wielu usług zależnych przy użyciu środowiska Node.js i program VS Code
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 11/21/2018
ms.topic: tutorial
description: Szybkie tworzenie w środowisku Kubernetes za pomocą kontenerów i mikrousług na platformie Azure
keywords: Docker, Kubernetes, Azure, usługi AKS, usłudze Azure Kubernetes Service, kontenerów, narzędzia Helm, usługa siatki, routing siatki usługi, narzędzia kubectl, k8s
ms.openlocfilehash: 136d48f1c420ac71896eaafa0daab476c7fba6fa
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503078"
---
# <a name="multi-service-development-with-azure-dev-spaces"></a>Programowanie wielu usług za pomocą usługi Azure Dev Spaces

Z tego samouczka dowiesz się, jak programować aplikacje wielousługowe za pomocą usługi Azure Dev Spaces oraz poznasz niektóre dodatkowe korzyści zapewniane przez usługę Dev Spaces.

## <a name="call-a-service-running-in-a-separate-container"></a>Wywoływanie usługi uruchomionej w oddzielnym kontenerze

W tej sekcji utworzysz drugą usługę, `mywebapi`, a usługa `webfrontend` ją wywoła. Każda usługa będzie działać w osobnych kontenerach. Następnie przeprowadzisz debugowanie w obu kontenerach.

![](media/common/multi-container.png)

### <a name="open-sample-code-for-mywebapi"></a>Otwieranie przykładowego kodu aplikacji *mywebapi*
W Twoim folderze o nazwie `samples` powinien już znajdować się przykładowy kod usługi `mywebapi` na potrzeby tego przewodnika (w przeciwnym razie przejdź do witryny https://github.com/Azure/dev-spaces i wybierz pozycję **Clone or Download** (Sklonuj lub pobierz), aby pobrać repozytorium GitHub). Kod dla tej sekcji znajduje się w folderze `samples/nodejs/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Uruchamianie aplikacji *mywebapi*
1. Otwórz aplikację `mywebapi` w *osobnym oknie programu VS Code*.
1. Otwórz okno **Paleta poleceń** (za pomocą menu **Widok | Paleta poleceń**) i przy użyciu autouzupełniania wpisz i wybierz to polecenie: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`. Nie należy mylić tego polecenia z poleceniem `azds prep`, które umożliwia skonfigurowanie projektu na potrzeby wdrożenia.
1. Naciśnij klawisz F5 i zaczekaj na skompilowanie i wdrożenie usługi. Będziesz wiedzieć, jest gotowy kiedy *nasłuchuje na porcie 80* pojawi się komunikat w konsoli debugowania.
1. Zanotuj adres URL punktu końcowego, który będzie wyglądał mniej więcej tak: `http://localhost:<portnumber>`. **Porada: Na pasku stanu programu VS Code zostanie pomarańczowy i zostaną wyświetlone możesz klikać adresu URL.** Może się wydawać, że kontener działa lokalnie, ale faktycznie jest on uruchamiany w środowisku deweloperskim na platformie Azure. Adres hosta lokalnego jest tworzony, ponieważ w aplikacji `mywebapi` nie zdefiniowano żadnych publicznych punktów końcowych i dostęp do niej można uzyskać wyłącznie z poziomu wystąpienia w środowisku Kubernetes. Dla Twojej wygody i ułatwienia interakcji z usługą prywatną z komputera lokalnego usługa Azure Dev Spaces tworzy tymczasowy tunel SSH do kontenera uruchomionego na platformie Azure.
1. Gdy aplikacja `mywebapi` jest gotowa, otwórz w przeglądarce adres hosta lokalnego. Powinna pojawić się odpowiedź z usługi `mywebapi` („Hello from mywebapi”).


### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Tworzenie żądania z aplikacji *webfrontend* do aplikacji *mywebapi*
Napiszmy w aplikacji `webfrontend` kod, który będzie wysyłał żądanie do aplikacji `mywebapi`.
1. Przełącz się na okno programu VS Code, aby uzyskać dostęp do aplikacji `webfrontend`.
1. Dodaj następujące wiersze kodu w górnej części pliku `server.js`:
    ```javascript
    var request = require('request');
    ```

3. *Zastąp* kod procedury obsługi GET `/api`. Podczas obsługi żądania wywołuje ono usługę `mywebapi`, a następnie zwraca wyniki z obu usług.

    ```javascript
    app.get('/api', function (req, res) {
       request({
          uri: 'http://mywebapi',
          headers: {
             /* propagate the dev space routing header */
             'azds-route-as': req.headers['azds-route-as']
          }
       }, function (error, response, body) {
           res.send('Hello from webfrontend and ' + body);
       });
    });
    ```
   1. *Usuń* wiersz `server.close()` na końcu pliku `server.js`

W poprzednim przykładzie kodu nagłówek `azds-route-as` jest przekazywany z żądania przychodzącego do żądania wychodzącego. Później zobaczysz, jak ułatwia to zespołom programowanie zespołowe.

### <a name="debug-across-multiple-services"></a>Debugowanie w wielu usługach
1. W tym momencie aplikacja `mywebapi` powinna być nadal uruchomiona z dołączonym debugerem. Jeśli nie jest, naciśnij klawisz F5 w projekcie `mywebapi`.
1. Ustaw punkt przerwania w domyślnej GET `/` obsługi [w wierszu 8 `server.js` ](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/mywebapi/server.js#L8).
1. W projekcie `webfrontend` ustaw punkt przerwania tuż przed wysłaniem żądania GET do aplikacji `http://mywebapi`.
1. W projekcie `webfrontend` naciśnij klawisz F5.
1. Otwórz aplikację internetową i wykonaj kod w obu usługach. W aplikacji internetowej powinien zostać wyświetlony połączony komunikat z dwóch usług: „Hello from webfrontend and Hello from mywebapi”.

### <a name="well-done"></a>Gotowe!
Teraz masz aplikację z wieloma kontenerami, z których każdy może być tworzony i wdrażany oddzielnie.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat programowania zespołowego w usłudze Dev Spaces](team-development-nodejs.md)
