---
title: Za pomocą usługi Twilio, połączeń głosowych, VoIP i wiadomości SMS na platformie Azure
description: Dowiedz się, jak nawiązywanie połączeń telefonicznych i wysyłanie wiadomości SMS za pomocą usługi interfejsu API usługi Twilio na platformie Azure. Przykłady kodu napisane w języku Node.js.
services: ''
documentationcenter: nodejs
author: devinrader
manager: wpickett
editor: ''
ms.assetid: f558cbbd-13d2-416f-b9b1-33a99c426af9
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 11/25/2014
ms.author: wpickett
ms.openlocfilehash: d9f419c48f64ba697e031dfc680bc9cb12bba5c4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60422919"
---
# <a name="using-twilio-for-voice-voip-and-sms-messaging-in-azure"></a>Za pomocą usługi Twilio, połączeń głosowych, VoIP i wiadomości SMS na platformie Azure
Ten przewodnik pokazuje, jak tworzyć aplikacje, które komunikują się za pomocą usługi Twilio i środowiska node.js na platformie Azure.

<a id="whatis"/>

## <a name="what-is-twilio"></a>Co to jest Twilio?
Twilio to platforma interfejsu API, która ułatwia deweloperom upewnij oraz odbieranie połączeń telefonicznych, wysyłanie i odbieranie wiadomości tekstowych i osadzić połączenia VoIP w przeglądarce i natywnych aplikacji mobilnych. Teraz chwilę omijają jak to działa przed zagłębieniem się.

### <a name="receiving-calls-and-text-messages"></a>Odbieranie rozmowy i wiadomości SMS
Twilio umożliwia deweloperom [zakupu numery telefonów programowalny] [ purchase_phone] który może służyć do wysyłać i odbierać wywołania i wiadomości SMS. Gdy liczba Twilio odbiera przychodzącego połączenia lub wiadomości SMS, Twilio wyśle aplikacji sieci web żądania HTTP POST lub GET żądanie, prośbą instrukcje dotyczące sposobu obsługi połączenia lub wiadomości SMS. Serwer będzie odpowiadać na żądania HTTP przez Twilio za pomocą [TwiML][twiml], prostego zestawu tagów XML, które zawierają instrukcje dotyczące sposobu obsługi połączenia lub wiadomości SMS. Zobaczymy przykłady TwiML w tylko chwilę.

### <a name="making-calls-and-sending-text-messages"></a>Wykonywanie wywołań i wysyłanie wiadomości SMS
Podejmując żądania HTTP do interfejsu API usługi sieci web usługi Twilio, deweloperzy mogą wysyłania wiadomości SMS lub zainicjować wychodzących połączeń telefonicznych. Połączenia wychodzące dewelopera należy także określić adres URL, który zwraca TwiML instrukcje dotyczące sposobu obsługi wywołań wychodzących po nawiązaniu.

### <a name="embedding-voip-capabilities-in-ui-code-javascript-ios-or-android"></a>Możliwości VoIP osadzania w kodzie interfejsu użytkownika (JavaScript, iOS lub Android)
Twilio zawiera zestaw SDK po stronie klienta, który można przekształcić w dowolnej przeglądarki sieci web, aplikacji dla systemu iOS lub aplikacji dla systemu Android telefonu VoIP. W tym artykule skupimy się na temat korzystania z połączeń VoIP w przeglądarce. Oprócz *zestaw JavaScript SDK usługi Twilio* uruchomienia w przeglądarce, aplikacją po stronie serwera (naszej aplikacji node.js) może służyć do wystawi token"możliwości" klienta języka JavaScript. Możesz dowiedzieć się więcej o używaniu VoIP przy użyciu środowiska node.js [na blogu deweloperów usługi Twilio][voipnode].

<a id="signup"/>

## <a name="sign-up-for-twilio-microsoft-discount"></a>Rejestracja w usłudze Twilio (Microsoft rabatu)
Przed rozpoczęciem korzystania z usługi Twilio, należy najpierw [założyć konto][signup]. Microsoft Azure klienci otrzymują rabat w wysokości specjalne - [Pamiętaj zarejestrować się tutaj][signup]!

<a id="azuresite"/>

## <a name="create-and-deploy-a-nodejs-azure-website"></a>Tworzenie i wdrażanie witryny sieci Web środowiska node.js platformy Azure
Następnie należy utworzyć witrynę node.js na platformie Azure. [W dokumentacji oficjalnego temu znajduje się tutaj][azure_new_site]. Na wysokim poziomie można będzie można wykonać następujące czynności:

* Założeniem konta platformy Azure, jeśli nie masz jeszcze takiego
* Aby utworzyć nową witrynę sieci Web przy użyciu konsoli administracyjnej platformy Azure
* Dodanie obsługi kontroli źródła (Firma Microsoft zakłada, że używasz git)
* Tworzenie pliku `server.js` z aplikacją sieci web node.js prosty
* Wdrażanie tej prostej aplikacji na platformie Azure

<a id="twiliomodule"/>

## <a name="configure-the-twilio-module"></a>Konfigurowanie modułu usługi Twilio
Następnie Zaczniemy napisz aplikację node.js proste, co sprawia, że korzystanie z interfejsu API usługi Twilio. Przed rozpoczęciem należy skonfigurować nasz poświadczeń konta usługi Twilio.

### <a name="configuring-twilio-credentials-in-system-environment-variables"></a>Konfigurowanie poświadczeń usługi Twilio w zmiennych środowiskowych systemu
Aby można było wprowadzić uwierzytelnianych żądań względem zaplecza usługi Twilio, potrzebujemy naszych identyfikator SID konta i token uwierzytelniania, która funkcja jako nazwy użytkownika i hasło skonfigurowane w ramach naszego konta usługi Twilio. To najbezpieczniejszy sposób skonfigurować je pod kątem użycia za pomocą modułu węzła na platformie Azure za pośrednictwem systemowe zmienne środowiskowe, które można ustawić bezpośrednio w konsoli administracyjnej platformy Azure.

Wybierz witrynę sieci Web node.js, a następnie kliknij link "Konfiguruj".  Po przewinięciu w dół bitu, zobaczysz obszar, w którym można ustawić właściwości konfiguracji dla aplikacji.  Wprowadź poświadczenia konta usługi Twilio ([znalezione na konsolę usługi Twilio][twilio_console]) pokazane — upewnij się, że ich nazwy `TWILIO_ACCOUNT_SID` i `TWILIO_AUTH_TOKEN`odpowiednio:

![Konsoli administracyjnej platformy Azure][azure-admin-console]

Po skonfigurowaniu tych zmiennych, ponownie uruchom aplikację w konsoli platformy Azure.

### <a name="declaring-the-twilio-module-in-packagejson"></a>Deklarowanie modułu usługi Twilio, w pliku package.json
Następnie należy utworzyć package.json do zarządzania naszych zależności modułów węzła za pośrednictwem [npm]. W tym samym poziomie co `server.js` pliku utworzonego w sekcji *Azure/node.js* samouczka, Utwórz plik o nazwie `package.json`.  W tym pliku należy umieścić następujące czynności:

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

Moduł usługi twilio to deklaruje jako zależność, a także popularne [struktury sieci web Express] [ express] i EJS aparatu szablonów.  OK teraz mamy wszystko jest gotowe — umożliwia pisanie kodu!

<a id="makecall"/>

## <a name="make-an-outbound-call"></a>Wykonywanie wywołania interfejsu wychodzącego
Utwórzmy prosty formularz, który będzie nawiązać połączenie z liczbą możemy wybrać. Otwórz `server.js`, a następnie wprowadź poniższy kod. Należy zwrócić uwagę na to, gdzie wyświetlany jest tekst "CHANGE_ME" - ważną nazwa witryny sieci Web platformy azure:

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

Następnie utwórz katalog o nazwie `views` — w tym katalogu Utwórz plik o nazwie `index.ejs` z następującą zawartością:

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

Teraz Wdrażanie witryny sieci Web na platformie Azure i otworzyć domu. Powinno być możliwe wprowadzenie numeru telefonu w polu tekstowym i odebrać połączenie z numeru Twilio!

<a id="sendmessage"/>

## <a name="send-an-sms-message"></a>Wyślij wiadomość SMS
Teraz Skonfigurujmy interfejsu użytkownika i logika obsługi formularza do wysyłania wiadomości tekstowej. Otwórz `server.js`i Dodaj następujący kod po ostatnim wywołaniu `app.post`:

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

W `views/index.ejs`, Dodaj innej formy w obszarze pierwszy z nich można przesłać numeru i wiadomość SMS:

```html
<form action="/sms" method="POST">
  <input placeholder="Enter a phone number" name="number"/>
  <br/>
  <input placeholder="Enter a message to send" name="message"/>
  <br/>
  <input type="submit" value="Send text to the number above"/>
</form>
```

Ponownie wdróż aplikację na platformie Azure, a teraz powinno być możliwe do przesyłania, który tworzą i Wyślij do siebie (lub dowolnej znajomych najbliższego) wiadomość SMS!

<a id="nextsteps"/>

## <a name="next-steps"></a>Następne kroki
Teraz już wiesz, podstawowe informacje dotyczące tworzenia aplikacji, które komunikują się przy użyciu środowiska node.js i usługi Twilio. Jednak te przykłady jedynie ułamek możliwości za pomocą usługi Twilio i node.js. Aby uzyskać więcej informacji, w środowisku node.js za pomocą usługi Twilio zapoznaj się z następującymi zasobami:

* [Moduł oficjalne dokumenty][docs]
* [Samouczek dotyczący VoIP z aplikacjami node.js][voipnode]
* [Votr — w czasie rzeczywistym programu SMS, głosowanie aplikacji przy użyciu środowiska node.js i CouchDB (trzy części)][votr]
* [Para programowania w przeglądarce przy użyciu środowiska node.js][pair]

Mamy nadzieję, że polubisz stosowanie metod hakerskich środowiska node.js i usługi Twilio, na platformie Azure!

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
