---
title: Jak nawiązać połączenie telefoniczne z usługi Twilio (PHP) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak nawiązywanie połączeń telefonicznych i wysyłanie wiadomości SMS za pomocą usługi interfejsu API usługi Twilio na platformie Azure. Przykłady są przeznaczone dla aplikacji PHP.
documentationcenter: php
services: ''
author: devinrader
manager: twilio
editor: mollybos
ms.assetid: 44e35adc-be06-4700-beee-8c9e2c20c540
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 11/25/2014
ms.author: microsofthelp@twilio.com
ms.openlocfilehash: 03b74f5a931e1cfbf09433af76c250607b7fc80c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60422315"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-php-application-on-azure"></a>Jak nawiązać połączenie telefoniczne za pomocą usługi Twilio w aplikacji w języku PHP na platformie Azure
Poniższy przykład pokazuje, jak można użyć usługi Twilio, aby nawiązać połączenie ze strony sieci web PHP hostowanych na platformie Azure. Wynikłej aplikacji zostanie wyświetlony monit dla wartości połączenia telefonicznego, jak pokazano na poniższym zrzucie ekranu.

![Formularz wywołania platformy Azure za pomocą usługi Twilio i PHP][twilio_php]

Należy wykonać następujące czynności, aby użyć kodu, w tym temacie:

1. Uzyskiwanie konta usługi Twilio i uwierzytelnianie tokenu z Twojej [konsoli usługi Twilio][twilio_console]. Aby rozpocząć pracę z usługą Twilio, ocenić ceny na [ https://www.twilio.com/pricing ] [ twilio_pricing]. Możesz zasubskrybować konto próbne w [ https://www.twilio.com/try-twilio ] [ try_twilio].
2. Uzyskaj [Biblioteka Twilio dla języka PHP](https://github.com/twilio/twilio-php) lub zainstalować ją jako pakiet GRUSZKI. Aby uzyskać więcej informacji, zobacz [pliku readme](https://github.com/twilio/twilio-php/blob/master/README.md).
3. Zainstaluj zestaw Azure SDK dla języka PHP. 
<!-- For an overview of the SDK and instructions on installing it, see [Set up the Azure SDK for PHP](app-service-web/web-sites-php-mysql-deploy-use-git.md) -->

## <a name="create-a-web-form-for-making-a-call"></a>Tworzenie formularza sieci web nawiązywania połączenia
Poniższy kod HTML przedstawia sposób tworzenia strony sieci web (**callform.html**), który pobiera dane użytkownika do nawiązywania połączenia:

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

## <a name="create-the-code-to-make-the-call"></a>Tworzenie kodu do wykonania wywołania
Poniższy kod przedstawia sposób tworzenia **makecall.php**, który jest wywoływana, gdy użytkownik przesyła formularz, wyświetlane przez **callform.html**. Kodu pokazany poniżej tworzy komunikat wywołania i generuje wywołanie. Ponadto należy użyć swojego konta usługi Twilio i uwierzytelniania tokenu z [konsoli Twilio] [ twilio_console] zamiast wartości symboli zastępczych, przypisany do **$sid** i  **$token** w poniższym kodzie.

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

Oprócz wywołania, **makecall.php** przedstawia niektóre metadane wywołanie pokazany na poniższej ilustracji. Aby uzyskać więcej informacji na temat metadanych wywołań zobacz [ https://www.twilio.com/docs/api/rest/call#instance-properties ] [ twilio_call_properties].

![Odpowiedź wywołania platformy Azure za pomocą usługi Twilio i PHP][twilio_php_response]

## <a name="run-the-application"></a>Uruchamianie aplikacji
Następnym krokiem jest [wdrażanie aplikacji w usłudze Azure Web Apps przy użyciu narzędzia Git](app-service/app-service-web-get-started-php.md) (choć nie wszystkie te informacje są odpowiednie). 

## <a name="next-steps"></a>Kolejne kroki
Ten kod podano Wam podstawowe funkcje w języku PHP na platformie Azure za pomocą usługi Twilio. Przed wdrożeniem na platformie Azure w środowisku produkcyjnym, można dodać więcej obsługę błędów lub innych funkcji. Na przykład:

* Zamiast przy użyciu formularza sieci web, można użyć usługi Azure storage blob lub bazy danych SQL do przechowywania numerów telefonów i wywołać tekstu. Aby dowiedzieć się, jak za pomocą usługi Azure storage BLOB w języku PHP, zobacz [przy użyciu usługi Azure Storage z aplikacji programu PHP][howto_blob_storage_php]. Aby dowiedzieć się, jak przy użyciu bazy danych SQL w języku PHP, zobacz [przy użyciu bazy danych SQL za pomocą aplikacji PHP][howto_sql_azure_php].
* **Makecall.php** kod używa adresu URL dostarczone do usługi Twilio ([https://twimlets.com/message][twimlet_message_url]) zapewnienie odpowiedź Twilio Markup Language (TwiML), która informuje o sposobie kontynuować usługi Twilio wywołanie. Na przykład może zawierać TwiML, który jest zwracany `<Say>` zlecenie, powstałego w tekście jest używany do odbiorcy połączenia. Zamiast przy użyciu adresu URL dostarczone do usługi Twilio, można utworzyć usługi odpowie na żądanie firmy Twilio; Aby uzyskać więcej informacji, zobacz [sposobu użycia Twilio dla połączeń głosowych i SMS funkcji w języku PHP][howto_twilio_voice_sms_php]. Więcej informacji na temat TwiML znajduje się w temacie [ https://www.twilio.com/docs/api/twiml ] [ twiml]i dowiedzieć się więcej o `<Say>` i innych poleceń usługi Twilio, można znaleźć w folderze [ https://www.twilio.com/docs/api/twiml/say ][twilio_say].
* Zapoznaj się ze wskazówkami zabezpieczeń Twilio na [ https://www.twilio.com/docs/security ] [ twilio_docs_security].

Aby uzyskać dodatkowe informacje na temat usługi Twilio, zobacz [ https://www.twilio.com/docs ] [ twilio_docs].

## <a name="see-also"></a>Zobacz też
* [Jak używać usługi Twilio dla połączeń głosowych i SMS funkcji w języku PHP](partner-twilio-php-how-to-use-voice-sms.md)

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
