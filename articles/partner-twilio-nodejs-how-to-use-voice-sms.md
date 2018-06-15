---
title: Przy użyciu usługi Twilio głosowych, VoIP i wiadomości SMS na platformie Azure
description: Dowiedz się, jak wykonać połączenie telefoniczne i wysłać wiadomość SMS z usługi interfejsu API usługi Twilio na platformie Azure. Przykłady kodu napisane w środowisku Node.js.
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
ms.openlocfilehash: f347540c78be712fc46d1d36b47ca4e23a62e28a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23865883"
---
# <a name="using-twilio-for-voice-voip-and-sms-messaging-in-azure"></a>Przy użyciu usługi Twilio głosowych, VoIP i wiadomości SMS na platformie Azure
W tym przewodniku pokazano, jak tworzyć aplikacje, które komunikują się z usługi Twilio i node.js na platformie Azure.

<a id="whatis"/>

## <a name="what-is-twilio"></a>Co to jest usługi Twilio?
Usługi Twilio to platforma interfejsu API, która ułatwia deweloperom upewnij i odbieranie połączeń telefonicznych, wysyłanie i odbieranie wiadomości tekstowych i osadzić VoIP wywołanie przeglądarki i natywnych aplikacji dla urządzeń przenośnych. Załóżmy krótko przekazywane jak to działa przed rozpoczęciem pracy.

### <a name="receiving-calls-and-text-messages"></a>Odbieranie wywołań i wiadomości SMS
Usługi Twilio umożliwia deweloperom [zakupu numery telefonów programowalny] [ purchase_phone] które mogą służyć do wysyłać i odbierać wywołań i wiadomości SMS. W odebrania połączenia przychodzącego lub wiadomości SMS numer usługi Twilio usługi Twilio wyśle aplikacji sieci web HTTP POST lub GET żądania, zapytaniem instrukcje na temat sposobu obsługi połączenia lub wiadomości SMS. Serwer będzie odpowiadać na żądania HTTP dla usługi Twilio z [TwiML][twiml], zbiór prostych tagów XML, które zawierają instrukcje dotyczące sposobu obsługi połączenia lub wiadomości SMS. Przykłady TwiML zostaną podane w chwilę.

### <a name="making-calls-and-sending-text-messages"></a>Wykonywanie wywołań i wysyłanie wiadomości tekstowych
Dokonując żądania HTTP do interfejsu API usługi sieci web usługi Twilio, deweloperzy mogą wiadomości tekstowych lub zainicjować wychodzących rozmów telefonicznych. Wychodzący deweloper musi również określić adres URL, który zwraca TwiML instrukcje dotyczące sposobu obsługi ruchu wychodzącego wywołania po podłączeniu.

### <a name="embedding-voip-capabilities-in-ui-code-javascript-ios-or-android"></a>Osadzanie możliwości VoIP w kodzie interfejsu użytkownika (JavaScript, iOS lub Android)
Usługi Twilio zawiera zestaw SDK klienta, który można przekształcić w dowolnej przeglądarki sieci web, aplikacji dla systemu iOS lub aplikacji systemu Android telefonu VoIP. W tym artykule firma Microsoft będzie skupić się na temat korzystania z połączeń VoIP w przeglądarce. Oprócz *usługi Twilio JavaScript SDK* uruchomiona w przeglądarce, aplikację po stronie serwera (naszej aplikacji node.js) musi używane do wystawiania tokenu"funkcji" do klienta kodu JavaScript. Więcej o korzystaniu z node.js VoIP [na blogu deweloperów usługi Twilio][voipnode].

<a id="signup"/>

## <a name="sign-up-for-twilio-microsoft-discount"></a>Utwórz konto usługi Twilio (Microsoft rabat)
Przed rozpoczęciem korzystania z usługi Twilio usługi, należy najpierw [konto][signup]. Microsoft Azure klienci uzyskać rabat specjalne — [Pamiętaj zarejestrować się tutaj][signup]!

<a id="azuresite"/>

## <a name="create-and-deploy-a-nodejs-azure-website"></a>Tworzenie i wdrażanie witryny sieci Web Azure node.js
Następnie należy utworzyć witrynę sieci Web node.js działających na platformie Azure. [Oficjalnej dokumentacji dla tej czynności znajduje się tutaj][azure_new_site]. Na wysokim poziomie użytkownik będzie można wykonać następujące czynności:

* Podczas tworzenia konta platformy Azure, jeśli nie masz już
* Aby utworzyć nową witrynę sieci Web przy użyciu konsoli administracyjnej usługi Azure
* Dodawanie obsługi kontroli źródła (zakładamy, że używane git)
* Tworzenie pliku `server.js` z aplikacją sieci web node.js proste
* Wdrażanie Ta prosta aplikacja na platformie Azure

<a id="twiliomodule"/>

## <a name="configure-the-twilio-module"></a>Konfigurowanie modułu usługi Twilio
Następnie możemy zacząć pisanie aplikacji node.js proste, która korzysta z interfejsu API usługi Twilio. Zanim zaczniemy, należy skonfigurować naszych poświadczenia konta usługi Twilio.

### <a name="configuring-twilio-credentials-in-system-environment-variables"></a>Konfigurowanie poświadczeń usługi Twilio w zmiennych środowiskowych systemu
Aby wprowadzić uwierzytelnionego żądania kierowane do zaplecza usługi Twilio, potrzebujemy naszych identyfikatora SID konta i token uwierzytelniania, funkcji jako nazwy użytkownika i hasło określone dla naszego konta usługi Twilio. Najbezpieczniejszą metodą, aby skonfigurować je do użycia z modułem węzła na platformie Azure jest za pomocą zmiennych środowiskowych systemu, które można ustawić bezpośrednio w konsoli administracyjnej platformy Azure.

Wybierz witrynę sieci Web node.js, a następnie kliknij łącze "Konfiguruj".  Przewiń w dół bit zobaczysz obszaru, w którym można ustawić właściwości konfiguracji dla aplikacji.  Wprowadź poświadczenia konta usługi Twilio ([znaleziono w konsoli usługi Twilio][twilio_console]) jak pokazano — upewnij się, że nazwa je `TWILIO_ACCOUNT_SID` i `TWILIO_AUTH_TOKEN`odpowiednio:

![Konsola administracyjna platformy Azure][azure-admin-console]

Po skonfigurowaniu tych zmiennych, ponownie uruchom aplikację w konsoli platformy Azure.

### <a name="declaring-the-twilio-module-in-packagejson"></a>Deklarowanie w module usługi Twilio w pliku package.json
Następnie należy utworzyć package.json do zarządzania naszych węzeł zależności modułu za pośrednictwem [npm]. Na tym samym poziomie jako `server.js` pliku utworzonego w *Azure/node.js* samouczek, Utwórz plik o nazwie `package.json`.  Wewnątrz tego pliku należy umieścić następujące czynności:

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

Moduł usługi twilio to deklarowana jako zależności, a także popularnych [Express platforma sieci web] [ express] i EJS aparatu szablonu.  Zgoda teraz możemy wszystko jest gotowe — Napiszmy kod!

<a id="makecall"/>

## <a name="make-an-outbound-call"></a>Wywoływania ruchu wychodzącego
Umożliwia tworzenie prostych formularz, który będzie nawiązać połączenie z liczbą się, że wybrany. Otwórz `server.js`, a następnie wprowadź poniższy kod. Należy zwrócić uwagę, gdy wyświetlany jest tekst "CHANGE_ME" - umieszczono nazwę witryny sieci Web platformy azure:

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

Następnie należy utworzyć katalog o nazwie `views` — w tym katalogu, Utwórz plik o nazwie `index.ejs` z następującą zawartość:

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

Teraz Wdrażanie witryny sieci Web na platformie Azure i otworzyć domu. Można wprowadzić numer telefonu w polu tekstowym i odbierać rozmowę z numer usługi Twilio!

<a id="sendmessage"/>

## <a name="send-an-sms-message"></a>Wyślij wiadomość SMS
Teraz Skonfigurujmy interfejsu użytkownika i obsługi logiki formularzy do wysyłania wiadomości tekstowej. Otwórz `server.js`i Dodaj następujący kod po ostatnim wywołaniem `app.post`:

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

W `views/index.ejs`, Dodaj innej formy w obszarze pierwsza z nich można przesłać numer i wiadomości SMS:

```html
<form action="/sms" method="POST">
  <input placeholder="Enter a phone number" name="number"/>
  <br/>
  <input placeholder="Enter a message to send" name="message"/>
  <br/>
  <input type="submit" value="Send text to the number above"/>
</form>
```

Teraz należy może przesyłać, które tworzą i wysłać wiadomość SMS samodzielnie (lub któregokolwiek z Twoich znajomych najbliższego) i ponownie wdrożyć aplikację na platformie Azure!

<a id="nextsteps"/>

## <a name="next-steps"></a>Następne kroki
Ma teraz znasz już podstawy do tworzenia aplikacji, które komunikują się za pomocą środowiska node.js i usługi Twilio. Jednak te przykłady prawie pliki tymczasowe powierzchni co to jest możliwe za pomocą usługi Twilio i node.js. Aby uzyskać więcej informacji o node.js przy użyciu usługi Twilio zobacz następujące zasoby:

* [Moduł oficjalna dokumentacja][docs]
* [Samouczek dotyczący VoIP z aplikacji node.js][voipnode]
* [Votr - wiadomość SMS w czasie rzeczywistym głosowania aplikacji node.js i CouchDB (trzy części)][votr]
* [Programowanie pary w przeglądarce, za pomocą języka node.js][pair]

Mamy nadzieję, że lubisz przejęcie node.js i usługi Twilio na platformie Azure!

[purchase_phone]: https://www.twilio.com/console/phone-numbers/search
[twiml]: https://www.twilio.com/docs/api/twiml
[signup]: http://ahoy.twilio.com/azure
[azure_new_site]: app-service/app-service-web-get-started-nodejs.md
[twilio_console]: https://www.twilio.com/console
[npm]: http://npmjs.org
[express]: http://expressjs.com
[voipnode]: http://www.twilio.com/blog/2013/04/introduction-to-twilio-client-with-node-js.html
[docs]: http://twilio.github.io/twilio-node/
[votr]: http://www.twilio.com/blog/2012/09/building-a-real-time-sms-voting-app-part-1-node-js-couchdb.html
[pair]: http://www.twilio.com/blog/2013/06/pair-programming-in-the-browser-with-twilio.html
[azure-admin-console]: ./media/partner-twilio-nodejs-how-to-use-voice-sms/twilio_1.png
