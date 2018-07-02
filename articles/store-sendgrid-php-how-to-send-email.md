---
title: Jak używać usługi poczty e-mail SendGrid (PHP) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wysłać wiadomość e-mail z usługi poczty e-mail SendGrid na platformie Azure. Przykłady kodu napisane w języku PHP.
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
ms.openlocfilehash: 523b986f66a2e48685e9707903194856f0dcf4a2
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "23874031"
---
# <a name="how-to-use-the-sendgrid-email-service-from-php"></a>Jak używać usługi SendGrid poczty E-mail za pomocą języka PHP
W tym przewodniku przedstawiono sposób wykonywania typowych zadań programowania usługi poczty e-mail SendGrid na platformie Azure. Przykłady są zapisywane w kodzie PHP.
Omówione scenariusze obejmują **konstruowania e-mail**, **wysyłania wiadomości e-mail**, i **dodawanie załączników**. Aby uzyskać więcej informacji na SendGrid i wysyłania wiadomości e-mail, zobacz [następne kroki](#next-steps) sekcji.

## <a name="what-is-the-sendgrid-email-service"></a>Co to jest usługa SendGrid poczty E-mail?
Jest SendGrid [Usługa poczty e-mail hostowanego w chmurze] zapewnia niezawodne [dostarczania transakcyjnych wiadomości e-mail], skalowalności i analiz w czasie rzeczywistym oraz elastyczne interfejsów API, które umożliwiają łatwe niestandardowej integracji. Typowe scenariusze użycia SendGrid obejmują:

* Potwierdzenia są automatycznie wysyłane do klientów
* Administrowanie dystrybucji wymieniono wysyłania klientów miesięczne e ulotki i oferty specjalne
* Zbieranie metryk w czasie rzeczywistym dla zablokowanych poczty e-mail i czasu odpowiedzi klienta
* Generowanie raportów, aby ułatwić identyfikację trendów
* Zapytania dotyczące przekazywania klienta
* Powiadomienia e-mail z aplikacji

Aby uzyskać więcej informacji, zobacz [ https://sendgrid.com ] [ https://sendgrid.com].

## <a name="create-a-sendgrid-account"></a>Utwórz konto SendGrid
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="using-sendgrid-from-your-php-application"></a>Przy użyciu SendGrid z aplikacji PHP
Włączenie w aplikacji Azure PHP korzystania z żadnych specjalnych konfiguracji lub kodowania. Ponieważ usługa jest SendGrid, jest dostępny w taki sam sposób, z poziomu aplikacji w chmurze, jak go z aplikacji lokalnej.

## <a name="how-to-send-an-email"></a>Porady: wysyłanie wiadomości E-mail
Możesz wysłać wiadomości e-mail przy użyciu protokołu SMTP lub udostępniane przez włączenie interfejsu API sieci Web.

### <a name="smtp-api"></a>INTERFEJS API SMTP
Do wysyłania wiadomości e-mail przy użyciu interfejsu API SMTP SendGrid, użyj *programu Swift poczty*, biblioteki platformy składników do wysyłania wiadomości e-mail z aplikacji PHP. Możesz pobrać *programu Swift poczty* biblioteki z [ http://swiftmailer.org/download ] [ http://swiftmailer.org/download] v5.3.0 (Użyj [Composer] do zainstalowania programu poczty Swift). Wysyłanie wiadomości e-mail z biblioteką obejmuje utworzenie wystąpienia <span class="auto-style2">Swift\_SmtpTransport</span>, <span class="auto-style2">Swift\_programu poczty</span>, i <span class="auto-style2">Swift\_wiadomości </span> klas, ustawienie odpowiednie właściwości i wywoływania <span class="auto-style2">Swift\_Mailer::send</span> metody.

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

### <a name="web-api"></a>Interfejs API sieci Web
Użyj tego PHP [curl funkcja] [ curl function] do wysyłania wiadomości e-mail przy użyciu interfejsu API sieci Web SendGrid.

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

Interfejs API sieci Web firmy SendGrid jest bardzo podobny do interfejsu API REST, chociaż nie jest rzeczywiście interfejsu API RESTful ponieważ w większości wywołania zarówno GET i POST zlecenia, które mogą być używane zamiennie.

## <a name="how-to-add-an-attachment"></a>Porady: Dodawanie załącznika
### <a name="smtp-api"></a>INTERFEJS API SMTP
Wysyłanie wiadomości przy użyciu interfejsu API SMTP polega na jeden wiersz dodatkowy kod przykładowy skrypt do wysyłania wiadomości e-mail z programu Swift poczty.

    <?php
     include_once "vendor/autoload.php";
     /*
      * Create the body of the message (a plain-text and an HTML version).
      * $text is your plain-text email
      * $html is your html version of the email
      * If the reciever is able to view html emails then only the html
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

Dodatkowe wiersz kodu jest następujący:

     $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName('file_name'));

Ten wiersz kodu wywołuje metodę attach <span class="auto-style2">Swift\_komunikat</span> obiektu i używa metody statycznej <span class="auto-style2">fromPath</span> na <span class="auto-style2">Swift\_załącznika</span> Klasa do pobrania i dołączyć plik do wiadomości.

### <a name="web-api"></a>Interfejs API sieci Web
Wysyłanie wiadomości przy użyciu interfejsu API sieci Web jest bardzo podobny do wysyłania wiadomości e-mail przy użyciu interfejsu API sieci Web. Pamiętaj jednak, w następującym przykładzie Tablica parametrów musi zawierać ten element:

    'files['.$fileName.']' => '@'.$filePath.'/'.$fileName

Przykład:

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

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Porady: Użyj filtrów, aby włączyć stopki, śledzenia i analiza
SendGrid udostępnia funkcje dodatkowe poczty e-mail przy użyciu "filtrów". Są to ustawienia, które mogą zostać dodane do wiadomości e-mail, aby włączyć określonych funkcji, takich jak włączenie kliknij śledzenia, Google analytics, subskrypcji, śledzenia i tak dalej.

Filtry może odnosić się do wiadomości przy użyciu właściwości filtrów. Każdy filtr jest określany przez skrót zawierający ustawienia specyficzne dla filtru. Poniższy przykład umożliwia filtru stopki i określa wiadomość tekstowa, która zostanie dołączona do dolnej części wiadomości e-mail.
W tym przykładzie używamy [Biblioteka sendgrid php].
Użyj [Composer] zainstalować biblioteki:

    php composer.phar require sendgrid/sendgrid 2.1.1

Przykład:    

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

     // If you do not specify a sender list above, you can specifiy the user here. If
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

## <a name="next-steps"></a>Następne kroki
Teraz, kiedy znasz już podstawy usługi poczty E-mail SendGrid, skorzystaj z poniższych linków, aby dowiedzieć się więcej.

* Dokumentacja SendGrid: <https://sendgrid.com/docs>
* Biblioteka SendGrid PHP: <https://github.com/sendgrid/sendgrid-php>
* Oferta specjalna SendGrid dla klientów platformy Azure: <https://sendgrid.com/windowsazure.html>

Aby uzyskać więcej informacji, zobacz też [Centrum deweloperów języka PHP](/develop/php/).

[https://sendgrid.com]: https://sendgrid.com
[https://sendgrid.com/transactional-email/pricing]: https://sendgrid.com/transactional-email/pricing
[special offer]: https://www.sendgrid.com/windowsazure.html
[Packaging and Deploying PHP Applications for Azure]: http://msdn.microsoft.com/library/windowsazure/hh674499(v=VS.103).aspx
[http://swiftmailer.org/download]: http://swiftmailer.org/download
[curl function]: http://php.net/curl
[Usługa poczty e-mail hostowanego w chmurze]: https://sendgrid.com/email-solutions
[dostarczania transakcyjnych wiadomości e-mail]: https://sendgrid.com/transactional-email
[Biblioteka sendgrid php]: https://github.com/sendgrid/sendgrid-php/tree/v2.1.1
Program [Composer]: https://getcomposer.org/download/
