---
title: Jak wykonać telefon z Twilio (PHP) | Dokumenty firmy Microsoft
description: Dowiedz się, jak nawiązać połączenie telefoniczne i wysłać wiadomość SMS za pomocą usługi interfejsu API usługi Twilio na platformie Azure. Próbki są przeznaczone do zastosowania PHP.
documentationcenter: php
services: ''
author: georgewallace
ms.assetid: 44e35adc-be06-4700-beee-8c9e2c20c540
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: fb1623c4a409f1c6cba94bad56d773e166d2b182
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69637321"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-php-application-on-azure"></a>Jak nawiązać połączenie telefoniczne przy użyciu usługi Twilio w aplikacji PHP na platformie Azure
W poniższym przykładzie pokazano, jak można użyć usługi Twilio do nawinięcia połączenia ze strony internetowej PHP hostowanego na platformie Azure. Wynikowa aplikacja wyświetli monit o podanie wartości połączeń telefonicznych, jak pokazano na poniższym zrzucie ekranu.

![Formularz wywołania platformy Azure przy użyciu usług Twilio i PHP][twilio_php]

Aby użyć kodu w tym temacie, należy wykonać następujące czynności:

1. Uzyskaj konto usługi Twilio i token uwierzytelniania z [konsoli usługi Twilio][twilio_console]. Aby rozpocząć korzystanie z usługi Twilio, należy ocenić ceny według . [https://www.twilio.com/pricing][twilio_pricing] Możesz założyć konto próbne [https://www.twilio.com/try-twilio][try_twilio]w pliku .
2. Uzyskaj [bibliotekę Twilio dla PHP](https://github.com/twilio/twilio-php) lub zainstaluj ją jako pakiet PEAR. Aby uzyskać więcej informacji, zobacz [plik readme](https://github.com/twilio/twilio-php/blob/master/README.md).
3. Zainstaluj zestaw Azure SDK dla PHP. 
<!-- For an overview of the SDK and instructions on installing it, see [Set up the Azure SDK for PHP](app-service-web/web-sites-php-mysql-deploy-use-git.md) -->

## <a name="create-a-web-form-for-making-a-call"></a>Tworzenie formularza internetowego do nawiązywania połączenia
Poniższy kod HTML pokazuje, jak utworzyć stronę internetową **(callform.html),** która pobiera dane użytkownika do nawiązywania połączenia:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Automated call form</title>
</head>
<body>
  <h1>Automated Call Form</h1>
  <p>Fill in all fields and click <b>Make this call</b>.</p>
  <form action="makecall.php" method="post">
    <table>
      <tr>
        <td>To:</td>
        <td><input name="callTo" size="50" type="text" value=""></td>
      </tr>
      <tr>
        <td>From:</td>
        <td><input name="callFrom" size="50" type="text" value=""></td>
      </tr>
      <tr>
        <td>Call message:</td>
        <td><input name="callText" size="100" type="text" value="Hello. This is the call text. Good bye."></td>
      </tr>
      <tr>
        <td colspan="2"><input type="submit" value="Make this call"></td>
      </tr>
    </table>
  </form><br>
</body>
</html>
```

## <a name="create-the-code-to-make-the-call"></a>Tworzenie kodu w celu nawiązać połączenie
Poniższy kod pokazuje, jak zbudować **makecall.php**, który jest wywoływany, gdy użytkownik przesyła formularz wyświetlany przez **callform.html**. Kod pokazany poniżej tworzy komunikat o połączeniu i generuje wywołanie. Ponadto należy użyć konta usługi Twilio i tokenu uwierzytelniania z [konsoli usługi Twilio][twilio_console] zamiast wartości zastępczych przypisanych do **$sid** i **$token** w poniższym kodzie.

```html
<html>
<head><title>Making call...</title></head>
<body>
<p>Your call is being made.</p>

<?php
require_once 'path/to/vendor/autoload.php';

$sid   = "your_account_sid";
$token = "your_authentication_token";

$from_number = $_POST['callFrom']; // Calls must be made from a registered Twilio number.
$to_number   = $_POST['callTo'];
$message     = $_POST['callText'];

$client = new Twilio\Rest\Client($sid, $token);

$call = $client->calls->create(
            $to_number,
            $from_number,
            array('url' => https://twimlets.com/message?Message=' . urlencode($message))
        );

echo "Call status: " . $call->status . "<br />";
echo "URI resource: " . $call->uri . "<br />";
?>
</body>
</html>
```

Oprócz nawiązywania połączenia **makecall.php** wyświetla metadane niektórych połączeń, jak pokazano na poniższej ilustracji. Aby uzyskać więcej informacji na [https://www.twilio.com/docs/api/rest/call#instance-properties][twilio_call_properties]temat metadanych połączeń, zobacz .

![Odpowiedź na wywołanie platformy Azure przy użyciu usług Twilio i PHP][twilio_php_response]

## <a name="run-the-application"></a>Uruchamianie aplikacji
Następnym krokiem jest [wdrożenie aplikacji w usłudze Azure Web Apps za pomocą git](app-service/app-service-web-get-started-php.md) (choć nie wszystkie informacje są odpowiednie). 

## <a name="next-steps"></a>Następne kroki
Ten kod został dostarczony, aby pokazać podstawowe funkcje przy użyciu usługi Twilio w PHP na platformie Azure. Przed wdrożeniem na platformie Azure w procesach produkcyjnych można dodać więcej obsługi błędów lub innych funkcji. Przykład:

* Zamiast używać formularza sieci web, można użyć obiektów blob magazynu platformy Azure lub bazy danych SQL do przechowywania numerów telefonów i wywoływania tekstu. Aby uzyskać informacje na temat używania obiektów blob magazynu platformy Azure w PHP, zobacz [Korzystanie z usługi Azure Storage z aplikacjami PHP][howto_blob_storage_php]. Aby uzyskać informacje dotyczące korzystania z bazy danych SQL w PHP, zobacz [Korzystanie z bazy danych SQL z aplikacjami PHP][howto_sql_azure_php].
* Kod **makecall.php** używa adresu URL dostarczonego[https://twimlets.com/message][twimlet_message_url]przez program Twilio ( ) w celu zapewnienia odpowiedzi TwiML (TwiML) dotyczącej języka znaczników usługi Twilio, która informuje program Twilio, jak kontynuować wywołanie. Na przykład TwiML, który jest `<Say>` zwracany może zawierać zlecenie, które powoduje tekst jest używany do odbiorcy wywołania. Zamiast używać adresu URL dostarczonego przez usługę Twilio, można utworzyć własną usługę, aby odpowiedzieć na żądanie usługi Twilio; Aby uzyskać więcej informacji, zobacz [Jak używać funkcji Twilio dla funkcji głosowych i SMS w PHP][howto_twilio_voice_sms_php]. Więcej informacji na temat TwiML można znaleźć `<Say>` na stronie , a więcej [https://www.twilio.com/docs/api/twiml/say][twilio_say]informacji na [https://www.twilio.com/docs/api/twiml][twiml]temat i innych czasowników Twilio można znaleźć na stronie .
* Przeczytaj wytyczne dotyczące zabezpieczeń [https://www.twilio.com/docs/security][twilio_docs_security]usługi Twilio pod adresem .

Aby uzyskać dodatkowe informacje na [https://www.twilio.com/docs][twilio_docs]temat usługi Twilio, zobacz .

## <a name="see-also"></a>Zobacz też
* [Jak korzystać z funkcji Twilio dla funkcji głosowych i SMS w PHP](partner-twilio-php-how-to-use-voice-sms.md)

[twilio_console]: https://www.twilio.com/console
[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/docs/api
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified
[twimlet_message_url]: https://twimlets.com/message
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api_service]: https://api.twilio.com
[build_php_azure_app]: http://azurephp.interoperabilitybridges.com/articles/build-and-deploy-a-windows-azure-php-application
[howto_twilio_voice_sms_php]: partner-twilio-php-how-to-use-voice-sms.md
[howto_blob_storage_php]: https://azure.microsoft.com/documentation/articles/storage-php-how-to-use-blobs/
[howto_sql_azure_php]: https://azure.microsoft.com/documentation/articles/sql-database-php-how-to-use/
[twilio_call_properties]: https://www.twilio.com/docs/api/rest/call#instance-properties
[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
[twilio_php]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPCallForm.jpg
[twilio_php_response]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPMakeCall.jpg
[twilio_php_github]: https://github.com/twilio/twilio-php
