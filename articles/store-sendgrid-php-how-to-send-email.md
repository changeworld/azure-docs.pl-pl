---
title: Jak używać usługi e-mail SendGrid (PHP) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wysyłać wiadomości e-mail za pomocą usługi e-mail SendGrid na platformie Azure. Przykłady kodu napisane w języku PHP.
documentationcenter: php
services: ''
manager: sendgrid
editor: mollybos
author: thinkingserious
ms.assetid: 51a9928a-4c9e-4b0a-aca8-9a408aeb6f47
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 10/30/2014
ms.author: elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork; matt.bernier@sendgrid.com
ms.openlocfilehash: db3333aa52782ceb949ef3f46a903b618f6e3f2f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60931232"
---
# <a name="how-to-use-the-sendgrid-email-service-from-php"></a>Jak używać usługi E-mail SendGrid za pomocą języka PHP

Ten przewodnik pokazuje sposób wykonywania typowych zadań programistycznych za pomocą usługi e-mail SendGrid na platformie Azure. Przykłady są napisane w języku PHP.
Omówione scenariusze obejmują **konstruowanie e-mail**, **wysyłania wiadomości e-mail**, i **dodawania załączników**. Aby uzyskać więcej informacji na temat usługi SendGrid i wysyłania wiadomości e-mail, zobacz [następne kroki](#next-steps) sekcji.

## <a name="what-is-the-sendgrid-email-service"></a>Co to jest usługa SendGrid poczty E-mail?
Usługa SendGrid jest [usługa oparta na chmurze poczty e-mail] zapewniająca niezawodne [dostarczania wiadomości e-mail dotyczącej transakcji], skalowalność i analizę w czasie rzeczywistym oraz udostępnia elastyczne interfejsy API, które umożliwiają łatwe niestandardową integrację. Typowe scenariusze użycia usługi SendGrid obejmują:

* Nie są automatycznie wysyłane potwierdzenia do klientów
* Administrowanie dystrybucji Wyświetla wysyłania klientom miesięczne e-rozsyłanych materiałach marketingowych firmowych i ofert specjalnych
* Zbieranie metryk w czasie rzeczywistym w przypadku elementów, np. blokowania wiadomości e-mail i szybkość reakcji klientów
* Generowanie raportów, aby ułatwić identyfikację trendów
* Przekazywanie zapytania klientów
* Powiadomienia e-mail z aplikacji

Aby uzyskać więcej informacji, zobacz [ https://sendgrid.com ] [ https://sendgrid.com].

## <a name="create-a-sendgrid-account"></a>Utwórz konto usługi SendGrid

[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="using-sendgrid-from-your-php-application"></a>Za pomocą usługi SendGrid z aplikacji PHP

Za pomocą usługi SendGrid w aplikacji PHP na platformie Azure wymaga żadnych specjalnych konfiguracji lub kodowania. Ponieważ usługi SendGrid, uzyskiwania w taki sam sposób, z aplikacji w chmurze możliwie najszybsza z aplikacji w środowisku lokalnym.

## <a name="how-to-send-an-email"></a>Instrukcje: Wyślij wiadomość E-mail

Możesz wysłać wiadomość e-mail przy użyciu SMTP lub interfejsu API sieci Web udostępniane przez usługi SendGrid.

### <a name="smtp-api"></a>SMTP API

Aby wysłać wiadomość e-mail przy użyciu interfejsu API usługi SendGrid SMTP, należy użyć *Swift Mailer*, oparty na komponentach biblioteki do wysyłania wiadomości e-mail z aplikacji PHP. Możesz pobrać [biblioteki Swift Mailer](https://swiftmailer.symfony.com/) v5.3.0 (Użyj [Program Composer] do zainstalowania języka Swift masowo wiadomości E-mail). Wysyłanie wiadomości e-mail z biblioteką wymaga utworzenia instancji `Swift\_SmtpTransport`, `Swift\_Mailer`, i `Swift\_Message` klasy, ustawienie odpowiednie właściwości i wywoływania `Swift\_Mailer::send` metody.

```php
<?php
 include_once "vendor/autoload.php";
 /*
  * Create the body of the message (a plain-text and an HTML version).
  * $text is your plain-text email
  * $html is your html version of the email
  * If the receiver is able to view html emails then only the html
  * email will be displayed
  */
 $text = "Hi!\nHow are you?\n";
 $html = "<html>
       <head></head>
       <body>
           <p>Hi!<br>
               How are you?<br>
           </p>
       </body>
       </html>";
 // This is your From email address
 $from = array('someone@example.com' => 'Name To Appear');
 // Email recipients
 $to = array(
       'john@contoso.com'=>'Destination 1 Name',
       'anna@contoso.com'=>'Destination 2 Name'
 );
 // Email subject
 $subject = 'Example PHP Email';

 // Login credentials
 $username = 'yoursendgridusername';
 $password = 'yourpassword';

 // Setup Swift mailer parameters
 $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
 $transport->setUsername($username);
 $transport->setPassword($password);
 $swift = Swift_Mailer::newInstance($transport);

 // Create a message (subject)
 $message = new Swift_Message($subject);

 // attach the body of the email
 $message->setFrom($from);
 $message->setBody($html, 'text/html');
 $message->setTo($to);
 $message->addPart($text, 'text/plain');

 // send message
 if ($recipients = $swift->send($message, $failures))
 {
     // This will let us know how many users received this message
     echo 'Message sent out to '.$recipients.' users';
 }
 // something went wrong =(
 else
 {
     echo "Something went wrong - ";
     print_r($failures);
 }
```

### <a name="web-api"></a>Interfejs API sieci Web
Korzystanie z języka PHP firmy [curl funkcja] [ curl function] do wysyłania wiadomości e-mail za pomocą internetowego interfejsu API usługi SendGrid.

```php
<?php

 $url = 'https://api.sendgrid.com/';
 $user = 'USERNAME';
 $pass = 'PASSWORD';

 $params = array(
      'api_user' => $user,
      'api_key' => $pass,
      'to' => 'john@contoso.com',
      'subject' => 'testing from curl',
      'html' => 'testing body',
      'text' => 'testing body',
      'from' => 'anna@contoso.com',
   );

 $request = $url.'api/mail.send.json';

 // Generate curl request
 $session = curl_init($request);

 // Tell curl to use HTTP POST
 curl_setopt ($session, CURLOPT_POST, true);

 // Tell curl that this is the body of the POST
 curl_setopt ($session, CURLOPT_POSTFIELDS, $params);

 // Tell curl not to return headers, but do return the response
 curl_setopt($session, CURLOPT_HEADER, false);
 curl_setopt($session, CURLOPT_RETURNTRANSFER, true);

 // obtain response
 $response = curl_exec($session);
 curl_close($session);

 // print everything out
 print_r($response);
```

Interfejs API sieci Web usługi SendGrid jest bardzo podobny do interfejsu API REST, chociaż nie jest naprawdę interfejsu API RESTful, że większość wywołań zarówno i jak WPIS zlecenia, które mogą być używane zamiennie.

## <a name="how-to-add-an-attachment"></a>Instrukcje: Dodaj załącznik

### <a name="smtp-api"></a>SMTP API

Wysyłanie załącznika przy użyciu interfejsu API SMTP polega na jeden wiersz dodatkowy kod przykładowy skrypt do wysyłania wiadomości e-mail przy użyciu języka Swift masowo wiadomości E-mail.

```php
<?php
 include_once "vendor/autoload.php";
 /*
  * Create the body of the message (a plain-text and an HTML version).
  * $text is your plain-text email
  * $html is your html version of the email
  * If the receiver is able to view html emails then only the html
  * email will be displayed
  */
 $text = "Hi!\nHow are you?\n";
  $html = "<html>
      <head></head>
      <body>
         <p>Hi!<br>
            How are you?<br>
         </p>
      </body>
      </html>";

 // This is your From email address
 $from = array('someone@example.com' => 'Name To Appear');

 // Email recipients
 $to = array(
      'john@contoso.com'=>'Destination 1 Name',
      'anna@contoso.com'=>'Destination 2 Name'
 );
 // Email subject
 $subject = 'Example PHP Email';

 // Login credentials
 $username = 'yoursendgridusername';
 $password = 'yourpassword';

 // Setup Swift mailer parameters
 $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
 $transport->setUsername($username);
 $transport->setPassword($password);
 $swift = Swift_Mailer::newInstance($transport);

 // Create a message (subject)
 $message = new Swift_Message($subject);

 // attach the body of the email
 $message->setFrom($from);
 $message->setBody($html, 'text/html');
 $message->setTo($to);
 $message->addPart($text, 'text/plain');
 $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName("file_name"));

 // send message
 if ($recipients = $swift->send($message, $failures))
 {
      // This will let us know how many users received this message
      echo 'Message sent out to '.$recipients.' users';
 }
 // something went wrong =(
 else
 {
      echo "Something went wrong - ";
      print_r($failures);
 }
```

Dodatkowy wiersz kodu jest następująca:

```php
 $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName('file_name'));
```

Ten wiersz kodu wywołuje metodę Dołącz `Swift\_Message` obiektu i używa metody statycznej `fromPath` na `Swift\_Attachment` klasy, aby pobrać i dołączyć plik do wiadomości.

### <a name="web-api"></a>Interfejs API sieci Web

Wysyłanie załącznika przy użyciu interfejsu API sieci Web jest bardzo podobny do wysyłania wiadomości e-mail przy użyciu interfejsu API sieci Web. Pamiętaj jednak, w poniższym przykładzie Tablica parametrów musi zawierać ten element:

```php
    'files['.$fileName.']' => '@'.$filePath.'/'.$fileName
```

#### <a name="example"></a>Przykład

```php
<?php

 $url = 'https://api.sendgrid.com/';
 $user = 'USERNAME';
 $pass = 'PASSWORD';

 $fileName = 'myfile';
 $filePath = dirname(__FILE__);

 $params = array(
     'api_user' => $user,
     'api_key' => $pass,
     'to' =>'john@contoso.com',
     'subject' => 'test of file sends',
     'html' => '<p> the HTML </p>',
     'text' => 'the plain text',
     'from' => 'anna@contoso.com',
     'files['.$fileName.']' => '@'.$filePath.'/'.$fileName
 );

 print_r($params);

 $request = $url.'api/mail.send.json';

 // Generate curl request
 $session = curl_init($request);

 // Tell curl to use HTTP POST
 curl_setopt ($session, CURLOPT_POST, true);

 // Tell curl that this is the body of the POST
 curl_setopt ($session, CURLOPT_POSTFIELDS, $params);

 // Tell curl not to return headers, but do return the response
 curl_setopt($session, CURLOPT_HEADER, false);
 curl_setopt($session, CURLOPT_RETURNTRANSFER, true);

 // obtain response
 $response = curl_exec($session);
 curl_close($session);

 // print everything out
 print_r($response);
```

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Instrukcje: Użyj filtrów, aby umożliwić stopki, śledzenia i analizy

Usługa SendGrid umożliwia funkcje dodatkowe poczty e-mail za pośrednictwem *filtry*. Są to ustawienia, które można dodać do wiadomości e-mail, aby włączyć określonych funkcji, takich jak umożliwianie śledzenie kliknięć, usługi Google analytics, subskrypcji, śledzenia i tak dalej.

Filtry można stosować do wiadomości za pomocą filtrów właściwości. Każdy filtr jest określony przez skrót zawierający ustawienia specyficzne dla filtru. Poniższy przykład umożliwia filtr stopki i określa wiadomość SMS, który zostanie dołączony do dolnej części wiadomości e-mail. W tym przykładzie użyjemy [biblioteki sendgrid php].

Użyj [Program Composer] Aby zainstalować bibliotekę:

```bash
php composer.phar require sendgrid/sendgrid 2.1.1
```

### <a name="example"></a>Przykład  

```php
<?php
 /*
  * This example is used for sendgrid-php V2.1.1 (https://github.com/sendgrid/sendgrid-php/tree/v2.1.1)
  */
 include "vendor/autoload.php";

 $email = new SendGrid\Email();
 // The list of addresses this message will be sent to
 // [This list is used for sending multiple emails using just ONE request to SendGrid]
 $toList = array('john@contoso.com', 'anna@contoso.com');

 // Specify the names of the recipients
 $nameList = array('Name 1', 'Name 2');

 // Used as an example of variable substitution
 $timeList = array('4 PM', '5 PM');

 // Set all of the above variables
 $email->setTos($toList);
 $email->addSubstitution('-name-', $nameList);
 $email->addSubstitution('-time-', $timeList);

 // Specify that this is an initial contact message
 $email->addCategory("initial");

 // You can optionally setup individual filters here, in this example, we have
 // enabled the footer filter
 $email->addFilter('footer', 'enable', 1);
 $email->addFilter('footer', "text/plain", "Thank you for your business");
 $email->addFilter('footer', "text/html", "Thank you for your business");

 // The subject of your email
 $subject = 'Example SendGrid Email';

 // Where is this message coming from. For example, this message can be from
 // support@yourcompany.com, info@yourcompany.com
 $from = 'someone@example.com';

 // If you do not specify a sender list above, you can specify the user here. If
 // a sender list IS specified above, this email address becomes irrelevant.
 $to = 'john@contoso.com';

 # Create the body of the message (a plain-text and an HTML version).
 # text is your plain-text email
 # html is your html version of the email
 # if the receiver is able to view html emails then only the html
 # email will be displayed

 /*
  * Note the variable substitution here =)
  */
 $text = "
 Hello -name-,
 Thank you for your interest in our products. We have set up an appointment to call you at -time- EST to discuss your needs in more detail.
 Regards,
 Fred";

 $html = "
 <html>
 <head></head>
 <body>
 <p>Hello -name-,<br>
 Thank you for your interest in our products. We have set up an appointment
 to call you at -time- EST to discuss your needs in more detail.

 Regards,

 Fred<br>
 </p>
 </body>
 </html>";

 // set subject
 $email->setSubject($subject);

 // attach the body of the email
 $email->setFrom($from);
 $email->setHtml($html);
 $email->addTo($to);
 $email->setText($text);

 // Your SendGrid account credentials
 $username = 'sendgridusername@yourdomain.com';
 $password = 'example';

 // Create SendGrid object
 $sendgrid = new SendGrid($username, $password);

 // send message
 $response = $sendgrid->send($email);

 print_r($response);
 ```

## <a name="next-steps"></a>Następne kroki

Teraz, kiedy znasz już podstawy usługi E-mail SendGrid, skorzystaj z poniższych linków, aby dowiedzieć się więcej.

* Dokumentacja usługi SendGrid: <https://sendgrid.com/docs>
* Biblioteka usługi SendGrid w języku PHP: <https://github.com/sendgrid/sendgrid-php>
* Usługa SendGrid specjalna oferta dla klientów platformy Azure: <https://sendgrid.com/windowsazure.html>

Aby uzyskać więcej informacji, zobacz też [Centrum deweloperów języka PHP](https://azure.microsoft.com/develop/php/).

[https://sendgrid.com]: https://sendgrid.com
[https://sendgrid.com/transactional-email/pricing]: https://sendgrid.com/transactional-email/pricing
[special offer]: https://www.sendgrid.com/windowsazure.html
[Packaging and Deploying PHP Applications for Azure]: https://msdn.microsoft.com/library/windowsazure/hh674499(v=VS.103).aspx
[http://swiftmailer.org/download]: http://swiftmailer.org/download
[curl function]: https://php.net/curl
[Usługa oparta na chmurze poczty e-mail]: https://sendgrid.com/email-solutions
[dostarczania wiadomości e-mail dotyczącej transakcji]: https://sendgrid.com/transactional-email
[biblioteki sendgrid php]: https://github.com/sendgrid/sendgrid-php/tree/v2.1.1
[Program Composer]: https://getcomposer.org/download/
