---
title: Korzystanie z usługi Twilio dla obsługi wiadomości głosowych, voip i wiadomości SMS na platformie Azure
description: Dowiedz się, jak nawiązać połączenie telefoniczne i wysłać wiadomość SMS za pomocą usługi interfejsu API usługi Twilio na platformie Azure. Przykłady kodu napisane w pliku Node.js.
services: ''
documentationcenter: nodejs
author: georgewallace
ms.assetid: f558cbbd-13d2-416f-b9b1-33a99c426af9
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: 164bedffcf9a1aca9f1fa46dea254fb928abcf04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69637267"
---
# <a name="using-twilio-for-voice-voip-and-sms-messaging-in-azure"></a>Korzystanie z usługi Twilio dla obsługi wiadomości głosowych, voip i wiadomości SMS na platformie Azure
W tym przewodniku pokazano, jak tworzyć aplikacje, które komunikują się z usługą Twilio i node.js na platformie Azure.

<a id="whatis"/>

## <a name="what-is-twilio"></a>Co to jest Twilio?
Twilio to platforma interfejsu API, która ułatwia deweloperom nawiązywać i odbierać połączenia telefoniczne, wysyłać i odbierać wiadomości tekstowe oraz osadzać połączenia VoIP w aplikacjach mobilnych opartych na przeglądarce i natywnych. Porozmawiajmy krótko o tym, jak to działa przed nurkowaniem.

### <a name="receiving-calls-and-text-messages"></a>Odbieranie połączeń i wiadomości tekstowych
Usługa Twilio umożliwia deweloperom [zakup programowalnych numerów telefonów,][purchase_phone] które mogą być używane zarówno do wysyłania, jak i odbierania połączeń i wiadomości tekstowych. Gdy numer usługi Twilio odbiera przychodzące połączenie lub tekst, aplikacja Twilio wyśle aplikacji sieci web żądanie HTTP POST lub GET, z prośbą o instrukcje dotyczące obsługi połączenia lub tekstu. Serwer odpowie na żądanie HTTP usługi Twilio za pomocą [TwiML][twiml], prostego zestawu znaczników XML, które zawierają instrukcje dotyczące obsługi połączenia lub tekstu. Zobaczymy przykłady TwiML w ciągu zaledwie jednej chwili.

### <a name="making-calls-and-sending-text-messages"></a>Nawiązywki do połączeń i wysyłanie wiadomości tekstowych
Wykonując żądania HTTP do interfejsu API usługi sieci web usługi Twilio, deweloperzy mogą wysyłać wiadomości tekstowe lub inicjować wychodzące połączenia telefoniczne. W przypadku wywołań wychodzących deweloper musi również określić adres URL, który zwraca instrukcje TwiML dotyczące obsługi połączenia wychodzącego po jego nawiązaniu.

### <a name="embedding-voip-capabilities-in-ui-code-javascript-ios-or-android"></a>Osadzanie możliwości VoIP w kodzie interfejsu użytkownika (JavaScript, iOS lub Android)
Usługa Twilio udostępnia sdk po stronie klienta, który może przekształcić dowolną przeglądarkę internetową, aplikację na iOS lub aplikację na Androida w telefon VoIP. W tym artykule skupimy się na tym, jak korzystać z połączeń VoIP w przeglądarce. Oprócz *SDK javascript usługi Twilio* działającego w przeglądarce, aplikacja po stronie serwera (nasza aplikacja node.js) musi być używana do wystawiania klientowi JavaScript "tokenu możliwości". Możesz przeczytać więcej na temat korzystania z VoIP z node.js [na blogu dewelopera Twilio][voipnode].

<a id="signup"/>

## <a name="sign-up-for-twilio-microsoft-discount"></a>Zarejestruj się w usłudze Twilio (rabat Microsoft)
Przed skorzystaniem z usług usługi Twilio należy najpierw [założyć konto][signup]. Klienci platformy Microsoft Azure otrzymują specjalną zniżkę - [zarejestruj się tutaj!][signup]

<a id="azuresite"/>

## <a name="create-and-deploy-a-nodejs-azure-website"></a>Tworzenie i wdrażanie witryny sieci Web usługi Azure w witrynie sieci Web usługi node.js
Następnie należy utworzyć witrynę internetową node.js działającą na platformie Azure. [Oficjalna dokumentacja w tym zakresie znajduje się tutaj][azure_new_site]. Na wysokim poziomie, będziesz robić następujące czynności:

* Rejestracja konta platformy Azure, jeśli jeszcze go nie masz
* Tworzenie nowej witryny sieci Web za pomocą konsoli administracyjnej platformy Azure
* Dodawanie obsługi kontroli źródła (zakładamy, że użyłeś git)
* Tworzenie pliku `server.js` za pomocą prostej aplikacji sieci Web node.js
* Wdrażanie tej prostej aplikacji na platformie Azure

<a id="twiliomodule"/>

## <a name="configure-the-twilio-module"></a>Konfigurowanie modułu Twilio
Następnie rozpoczniemy pisanie prostej aplikacji node.js, która korzysta z interfejsu API usługi Twilio. Zanim zaczniemy, musimy skonfigurować nasze poświadczenia konta usługi Twilio.

### <a name="configuring-twilio-credentials-in-system-environment-variables"></a>Konfigurowanie poświadczeń usługi Twilio w zmiennych środowiska systemowego
Aby tworzyć uwierzytelnione żądania względem zaplecza usługi Twilio, potrzebujemy naszego konta SID i tokenu uwierzytelniania, które działają jako nazwa użytkownika i hasło ustawione dla naszego konta Twilio. Najbezpieczniejszym sposobem skonfigurowania ich do użycia z modułem węzła na platformie Azure są zmienne środowiskowe systemu, które można ustawić bezpośrednio w konsoli administracyjnej platformy Azure.

Wybierz witrynę internetową node.js i kliknij łącze "KONFIGURUJ".  Jeśli przewiń nieco w dół, zobaczysz obszar, w którym można ustawić właściwości konfiguracji dla aplikacji.  Wprowadź poświadczenia konta usługi Twilio[(znajdujące się na konsoli usługi Twilio)][twilio_console]w sposób pokazany — upewnij się, że je nadają `TWILIO_ACCOUNT_SID` i, `TWILIO_AUTH_TOKEN`odpowiednio:

![Konsola administracyjna platformy Azure][azure-admin-console]

Po skonfigurowaniu tych zmiennych uruchom ponownie aplikację w konsoli platformy Azure.

### <a name="declaring-the-twilio-module-in-packagejson"></a>Deklarowanie modułu usługi Twilio w pliku package.json
Następnie musimy utworzyć package.json do zarządzania naszymi zależnościami modułu węzła za pośrednictwem [npm]. Na tym samym `server.js` poziomie co plik utworzony w samouczku *azure/node.js* utwórz plik o nazwie `package.json`.  W tym pliku umieść następujące miejsca:

```json
{
  "name": "application-name",
  "version": "0.0.1",
  "private": true,
  "scripts": {
    "start": "node server"
  },
  "dependencies": {
    "body-parser": "^1.16.1",
    "ejs": "^2.5.5",
    "errorhandler": "^1.5.0",
    "express": "^4.14.1",
    "morgan": "^1.8.1",
    "twilio": "^2.11.1"
  }
}
```

Deklaruje moduł twilio jako zależność, a także popularną [platformę sieci Web Express][express] i aparat szablonów EJS.  Dobra, teraz wszyscy jesteśmy ustawiene - napiszmy jakiś kod!

<a id="makecall"/>

## <a name="make-an-outbound-call"></a>Nawiązywania połączenia wychodzącego
Stwórzmy prosty formularz, który spowoduje połączenie z numerem, który wybierzemy. Otwórz `server.js`i wprowadź następujący kod. Uwaga, gdzie mówi "CHANGE_ME" - umieść nazwę swojej witryny azure tam:

```javascript
// Module dependencies
const express = require('express');
const path = require('path');
const http = require('http');
const twilio = require('twilio');
const logger = require('morgan');
const bodyParser = require('body-parser');
const errorHandler = require('errorhandler');
const accountSid = process.env.TWILIO_ACCOUNT_SID;
const authToken = process.env.TWILIO_AUTH_TOKEN;
// Create Express web application
const app = express();

// Express configuration
app.set('port', process.env.PORT || 3000);
app.set('views', __dirname + '/views');
app.set('view engine', 'ejs');
app.use(logger('tiny'));
app.use(bodyParser.urlencoded({ extended: false }))
app.use(bodyParser.json())
app.use(express.static(path.join(__dirname, 'public')));

if (app.get('env') !== 'production') {
  app.use(errorHandler());
}

// Render an HTML user interface for the application's home page
app.get('/', (request, response) => response.render('index'));

// Handle the form POST to place a call
app.post('/call', (request, response) => {
  var client = twilio(accountSid, authToken);

  client.makeCall({
    // make a call to this number
    to:request.body.number,

    // Change to a Twilio number you bought - see:
    // https://www.twilio.com/console/phone-numbers/incoming
    from:'+15558675309',

    // A URL in our app which generates TwiML
    // Change "CHANGE_ME" to your app's name
    url:'https://CHANGE_ME.azurewebsites.net/outbound_call'
  }, () => {
      // Go back to the home page
      response.redirect('/');
  });
});

// Generate TwiML to handle an outbound call
app.post('/outbound_call', (request, response) => {
  var twiml = new twilio.TwimlResponse();

  // Say a message to the call's receiver
  twiml.say('hello - thanks for checking out Twilio and Azure', {
      voice:'woman'
  });

  response.set('Content-Type', 'text/xml');
  response.send(twiml.toString());
});

// Start server
app.listen(app.get('port'), function(){
  console.log(`Express server listening on port ${app.get('port')}`);
});
```

Następnie utwórz katalog `views` o nazwie - wewnątrz tego `index.ejs` katalogu, utwórz plik o nazwie z następującą zawartością:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Twilio Test</title>
  <style>
    input { height:20px; width:300px; font-size:18px; margin:5px; padding:5px; }
  </style>
</head>
<body>
  <h1>Twilio Test</h1>
  <form action="/call" method="POST">
      <input placeholder="Enter a phone number" name="number"/>
      <br/>
      <input type="submit" value="Call the number above"/>
  </form>
</body>
</html>
```

Teraz rozmieszcz swoją witrynę sieci Web na platformie Azure i otwórz swój dom. Powinieneś być w stanie wprowadzić swój numer telefonu w polu tekstowym i odebrać połączenie z numeru Twilio!

<a id="sendmessage"/>

## <a name="send-an-sms-message"></a>Wysyłanie wiadomości SMS
Teraz skonfigurujmy interfejs użytkownika i logikę obsługi formularzy, aby wysłać wiadomość tekstową. Otwórz `server.js`i dodaj następujący kod po ostatnim `app.post`wywołaniu do:

```javascript
app.post('/sms', (request, response) => {
  const client = twilio(accountSid, authToken);

  client.sendSms({
      // send a text to this number
      to:request.body.number,

      // A Twilio number you bought - see:
      // https://www.twilio.com/console/phone-numbers/incoming
      from:'+15558675309',

      // The body of the text message
      body: request.body.message

  }, () => {
      // Go back to the home page
      response.redirect('/');
  });
});
```

W `views/index.ejs`, dodaj inny formularz pod pierwszym, aby przesłać numer i wiadomość tekstową:

```html
<form action="/sms" method="POST">
  <input placeholder="Enter a phone number" name="number"/>
  <br/>
  <input placeholder="Enter a message to send" name="message"/>
  <br/>
  <input type="submit" value="Send text to the number above"/>
</form>
```

Ponownie wdrożyć aplikację na platformie Azure, a teraz powinieneś być w stanie przesłać ten formularz i wysłać sobie (lub któregokolwiek z twoich najbliższych znajomych) wiadomość tekstową!

<a id="nextsteps"/>

## <a name="next-steps"></a>Następne kroki
Teraz poznaliśmy podstawy używania node.js i Twilio do tworzenia aplikacji, które się komunikują. Ale te przykłady ledwo zarysować powierzchnię, co jest możliwe z Twilio i node.js. Aby uzyskać więcej informacji przy użyciu usługi Twilio z node.js, zapoznaj się z następującymi zasobami:

* [Oficjalne dokumenty modułu][docs]
* [Samouczek na temat VoIP z aplikacjami node.js][voipnode]
* [Votr - aplikacja do głosowania SMS w czasie rzeczywistym z node.js i CouchDB (trzy części)][votr]
* [Sparowanie programowania w przeglądarce z node.js][pair]

Mamy nadzieję, że uwielbiasz hakowanie node.js i Twilio na platformie Azure!

[purchase_phone]: https://www.twilio.com/console/phone-numbers/search
[twiml]: https://www.twilio.com/docs/api/twiml
[signup]: https://ahoy.twilio.com/azure
[azure_new_site]: app-service/app-service-web-get-started-nodejs.md
[twilio_console]: https://www.twilio.com/console
[npm]: https://npmjs.org
[express]: https://expressjs.com
[voipnode]: https://www.twilio.com/blog/2013/04/introduction-to-twilio-client-with-node-js.html
[docs]: https://www.twilio.com/docs/libraries/reference/twilio-node/
[votr]: https://www.twilio.com/blog/2012/09/building-a-real-time-sms-voting-app-part-1-node-js-couchdb.html
[pair]: https://www.twilio.com/blog/2013/06/pair-programming-in-the-browser-with-twilio.html
[azure-admin-console]: ./media/partner-twilio-nodejs-how-to-use-voice-sms/twilio_1.png
