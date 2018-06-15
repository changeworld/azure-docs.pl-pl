---
title: 'Porady: tworzenie rozmowy telefonicznej z usługi Twilio (PHP) | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak wykonać połączenie telefoniczne i wysłać wiadomość SMS z usługi interfejsu API usługi Twilio na platformie Azure. Przykłady są przeznaczone dla aplikacji PHP.
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
ms.openlocfilehash: 9866a196b3be10548d7a431430e570b41c190fc0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23865757"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-php-application-on-azure"></a>Porady: tworzenie rozmowę telefoniczną w aplikacji PHP na platformie Azure przy użyciu usługi Twilio
Poniższy przykład pokazuje, jak można użyć usługi Twilio, aby nawiązać połączenie ze strony sieci web PHP hostowana na platformie Azure. Wynikowa aplikacja pojawi się monit dla wartości rozmowy telefonicznej, jak pokazano na poniższym zrzucie ekranu.

![Formularz wywołania platformy Azure przy użyciu usługi Twilio i PHP][twilio_php]

Należy wykonać następujące czynności, aby użyć kodu w tym temacie:

1. Uzyskać konta usługi Twilio i uwierzytelniania tokenu z Twojej [usługi Twilio Console][twilio_console]. Aby zacząć korzystać z usługi Twilio, ocenę cennik w [http://www.twilio.com/pricing][twilio_pricing]. Można założyć konto próbne w [https://www.twilio.com/try-twilio][try_twilio].
2. Uzyskaj [biblioteki usługi Twilio dla PHP](https://github.com/twilio/twilio-php) lub zainstaluj go w pakiecie GRUSZKOWA. Aby uzyskać więcej informacji, zobacz [plik readme](https://github.com/twilio/twilio-php/blob/master/README.md).
3. Zainstaluj zestaw Azure SDK for PHP. 
<!-- For an overview of the SDK and instructions on installing it, see [Set up the Azure SDK for PHP](app-service-web/web-sites-php-mysql-deploy-use-git.md) -->

## <a name="create-a-web-form-for-making-a-call"></a>Tworzenie formularza sieci web nawiązywania połączenia
Poniższy kod HTML przedstawia sposób tworzenia strony sieci web (**callform.html**), który pobiera dane użytkownika dla wywołania:

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

## <a name="create-the-code-to-make-the-call"></a>Utwórz kod, aby wykonać wywołanie
Poniższy kod przedstawia sposób tworzenia **makecall.php**, która jest wywoływana, gdy użytkownik przesyła formularz, wyświetlane przez **callform.html**. Kodem przedstawionym poniżej tworzy komunikat wywołania i generuje wywołania. Ponadto należy użyć Twojego konta usługi Twilio i uwierzytelniania tokenu z [usługi Twilio Console] [ twilio_console] zamiast wartości symbolu zastępczego, przypisane do **$sid** i  **$token** w poniższym kodzie.

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
            array('url' => http://twimlets.com/message?Message=' . urlencode($message))
        );

echo "Call status: " . $call->status . "<br />";
echo "URI resource: " . $call->uri . "<br />";
?>
</body>
</html>
```

Oprócz wywołania, **makecall.php** przedstawia niektóre metadane wywołania pokazany na poniższej ilustracji. Aby uzyskać więcej informacji o metadanych wywołanie, zobacz [https://www.twilio.com/docs/api/rest/call#instance-properties][twilio_call_properties].

![Odpowiedź wywołania platformy Azure przy użyciu usługi Twilio i PHP][twilio_php_response]

## <a name="run-the-application"></a>Uruchamianie aplikacji
Następnym krokiem jest [wdrożyć aplikację na platformie Azure aplikacje sieci Web za pomocą narzędzia Git](app-service/app-service-web-get-started-php.md) (choć nie wszystkie informacje są istotne). 

## <a name="next-steps"></a>Następne kroki
Ten kod został podany pokazanie podstawowe funkcje w kodzie PHP na platformie Azure przy użyciu usługi Twilio. Przed wdrożeniem na platformie Azure w środowisku produkcyjnym, możesz dodać więcej obsługi błędów lub innych funkcji. Na przykład:

* Zamiast za pomocą formularza sieci web, można użyć obiektów blob magazynu Azure lub bazy danych SQL do przechowywania numerów telefonów i wywołać tekstu. Aby dowiedzieć się, jak za pomocą obiektów blob magazynu Azure w kodzie PHP, zobacz [przy użyciu usługi Azure Storage z aplikacji PHP][howto_blob_storage_php]. Aby uzyskać informacje dotyczące korzystania z bazy danych SQL w języku PHP, zobacz [przy użyciu bazy danych SQL z aplikacji PHP][howto_sql_azure_php].
* **Makecall.php** kod zawiera adres URL udostępniony do usługi Twilio ([http://twimlets.com/message][twimlet_message_url]) można podać odpowiedzi usługi Twilio Markup Language (TwiML), który informuje usługi Twilio jak do Przejdź do wywołania. Na przykład może zawierać TwiML, która jest zwracana `<Say>` zlecenia, który daje w tekście jest używany do adresata wywołania. Zamiast używać adresu URL dostarczony do usługi Twilio, można zbudować usługi na odpowiedź na żądanie dla usługi Twilio; Aby uzyskać więcej informacji, zobacz [sposobu użycia usługi Twilio, głosowe i możliwości programu SMS w kodzie PHP][howto_twilio_voice_sms_php]. Więcej informacji na temat TwiML można znaleźć w folderze [http://www.twilio.com/docs/api/twiml][twiml]oraz dodatkowe informacje o `<Say>` i inne usługi Twilio zlecenia można znaleźć w folderze [http:// www.twilio.com/docs/API/twiml/Say][twilio_say].
* Zapoznaj się ze wskazówkami zabezpieczeń usługi Twilio w [https://www.twilio.com/docs/security][twilio_docs_security].

Aby uzyskać dodatkowe informacje na temat usługi Twilio, zobacz [https://www.twilio.com/docs][twilio_docs].

## <a name="see-also"></a>Zobacz też
* [Jak używać usługi Twilio głosowe i możliwości programu SMS w języku PHP](partner-twilio-php-how-to-use-voice-sms.md)

[twilio_console]: https://www.twilio.com/console
[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/docs/api
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified
[twimlet_message_url]: http://twimlets.com/message
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api_service]: http://api.twilio.com
[build_php_azure_app]: http://azurephp.interoperabilitybridges.com/articles/build-and-deploy-a-windows-azure-php-application
[howto_twilio_voice_sms_php]: partner-twilio-php-how-to-use-voice-sms.md
[howto_blob_storage_php]: http://azure.microsoft.com/documentation/articles/storage-php-how-to-use-blobs/
[howto_sql_azure_php]: http://azure.microsoft.com/documentation/articles/sql-database-php-how-to-use/
[twilio_call_properties]: https://www.twilio.com/docs/api/rest/call#instance-properties
[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
[twilio_php]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPCallForm.jpg
[twilio_php_response]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPMakeCall.jpg
[twilio_php_github]: https://github.com/twilio/twilio-php
