---
title: Jak korzystać z usługi poczty e-mail SendGrid (Java) | Dokumenty firmy Microsoft
description: Dowiedz się, jak wysyłać wiadomości e-mail za pomocą usługi sendgrid na platformie Azure. Przykłady kodu napisane w języku Java.
services: ''
documentationcenter: java
author: thinkingserious
manager: sendgrid
editor: mollybos
ms.assetid: cc75c43e-ede9-492b-98c2-9147fcb92c21
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/30/2014
ms.author: erikre
ms.reviewer: elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork
ms.openlocfilehash: 8ae948e9c79cff4cd0c896b250743fd9dc521752
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67876511"
---
# <a name="how-to-send-email-using-sendgrid-from-java"></a>Jak wysłać e-mail za pomocą SendGrid z Java
W tym przewodniku pokazano, jak wykonywać typowe zadania programowania za pomocą usługi sendgrid poczty e-mail na platformie Azure. Przykłady są zapisywane w języku Java. Scenariusze obejmują **konstruowanie wiadomości e-mail,** **wysyłanie wiadomości e-mail,** **dodawanie załączników,** **używanie filtrów**i **aktualizowanie właściwości.** Aby uzyskać więcej informacji na temat SendGrid i wysyłania wiadomości e-mail, zobacz [następne kroki](#next-steps) sekcji.

## <a name="what-is-the-sendgrid-email-service"></a>Co to jest usługa poczty e-mail SendGrid?
SendGrid to [chmurowa usługa poczty e-mail,] która zapewnia niezawodne [transakcyjne dostarczanie wiadomości e-mail,]skalowalność i analizę w czasie rzeczywistym wraz z elastycznymi interfejsami API, które ułatwiają niestandardową integrację. Typowe scenariusze użycia SendGrid obejmują:

* Automatyczne wysyłanie potwierdzeń do odbiorców
* Administrowanie listami dystrybucyjnymi do wysyłania klientom miesięcznych e-ulotek i ofert specjalnych
* Zbieranie danych w czasie rzeczywistym dla takich rzeczy, jak zablokowana poczta e-mail i responsywność klienta
* Generowanie raportów ułatwiających identyfikację trendów
* Przekazywanie zapytań klientów
* Powiadomienia e-mail z aplikacji

Aby uzyskać więcej informacji, zobacz <https://sendgrid.com>.

## <a name="create-a-sendgrid-account"></a>Tworzenie konta SendGrid
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="how-to-use-the-javaxmail-libraries"></a>Jak: Korzystanie z bibliotek javax.mail
Uzyskaj biblioteki javax.mail, na <https://www.oracle.com/technetwork/java/javamail> przykład i zaimportuj je do kodu. Na wysokim poziomie proces wysyłania wiadomości e-mail przy użyciu biblioteki javax.mail przy użyciu protokołu SMTP jest następujący:

1. Określ wartości SMTP, w tym serwer SMTP, który dla sendgrid jest smtp.sendgrid.net.

```
        import java.util.Properties;
        import javax.mail.*;
        import javax.mail.internet.*;

        public class MyEmailer {
           private static final String SMTP_HOST_NAME = "smtp.sendgrid.net";
           private static final String SMTP_AUTH_USER = "your_sendgrid_username";
           private static final String SMTP_AUTH_PWD = "your_sendgrid_password";

           public static void main(String[] args) throws Exception{
               new MyEmailer().SendMail();
           }

           public void SendMail() throws Exception
           {
              Properties properties = new Properties();
                 properties.put("mail.transport.protocol", "smtp");
                 properties.put("mail.smtp.host", SMTP_HOST_NAME);
                 properties.put("mail.smtp.port", 587);
                 properties.put("mail.smtp.auth", "true");
                 // …
```

1. Rozszerz klasę *javax.mail.Authenticator,* a w implementacji metody *getPasswordAuthentication* zwróć nazwę użytkownika i hasło SendGrid.  

       private class SMTPAuthenticator extends javax.mail.Authenticator {
       public PasswordAuthentication getPasswordAuthentication() {
          String username = SMTP_AUTH_USER;
          String password = SMTP_AUTH_PWD;
          return new PasswordAuthentication(username, password);
       }
2. Utwórz uwierzytelnioną sesję wiadomości e-mail za pośrednictwem obiektu *javax.mail.Session.*  

       Authenticator auth = new SMTPAuthenticator();
       Session mailSession = Session.getDefaultInstance(properties, auth);
3. Utwórz wiadomość i przypisz do wartości tematu i zawartości **do**, **od**, **tematu** i zawartości. Jest to pokazane w sekcji [Jak: Tworzenie wiadomości e-mail.](#how-to-create-an-email)
4. Wyślij wiadomość za pośrednictwem obiektu *javax.mail.Transport.* Jest to pokazane w sekcji [Jak: Wyślij wiadomość e-mail][#how do wysłania wiadomości e-mail].

## <a name="how-to-create-an-email"></a>Jak: Tworzenie wiadomości e-mail
Poniżej przedstawiono sposób określania wartości wiadomości e-mail.

    MimeMessage message = new MimeMessage(mailSession);
    Multipart multipart = new MimeMultipart("alternative");
    BodyPart part1 = new MimeBodyPart();
    part1.setText("Hello, Your Contoso order has shipped. Thank you, John");
    BodyPart part2 = new MimeBodyPart();
    part2.setContent(
        "<p>Hello,</p>
        <p>Your Contoso order has <b>shipped</b>.</p>
        <p>Thank you,<br>John</br></p>",
        "text/html");
    multipart.addBodyPart(part1);
    multipart.addBodyPart(part2);
    message.setFrom(new InternetAddress("john@contoso.com"));
    message.addRecipient(Message.RecipientType.TO,
       new InternetAddress("someone@example.com"));
    message.setSubject("Your recent order");
    message.setContent(multipart);

## <a name="how-to-send-an-email"></a>Jak: Wyślij wiadomość e-mail
Poniżej przedstawiono sposób wysyłania wiadomości e-mail.

    Transport transport = mailSession.getTransport();
    // Connect the transport object.
    transport.connect();
    // Send the message.
    transport.sendMessage(message, message.getAllRecipients());
    // Close the connection.
    transport.close();

## <a name="how-to-add-an-attachment"></a>Jak: Dodawanie załącznika
Poniższy kod pokazuje, jak dodać załącznik.

    // Local file name and path.
    String attachmentName = "myfile.zip";
    String attachmentPath = "c:\\myfiles\\";
    MimeBodyPart attachmentPart = new MimeBodyPart();
    // Specify the local file to attach.
    DataSource source = new FileDataSource(attachmentPath + attachmentName);
    attachmentPart.setDataHandler(new DataHandler(source));
    // This example uses the local file name as the attachment name.
    // They could be different if you prefer.
    attachmentPart.setFileName(attachmentName);
    multipart.addBodyPart(attachmentPart);

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Jak: użyj filtrów, aby włączyć stopki, śledzenie i analizę
SendGrid zapewnia dodatkową funkcjonalność poczty e-mail za pomocą *filtrów.* Są to ustawienia, które można dodać do wiadomości e-mail, aby włączyć określone funkcje, takie jak włączanie śledzenia kliknięć, google analytics, śledzenie subskrypcji i tak dalej. Aby uzyskać pełną listę filtrów, zobacz [Ustawienia filtrów][Filter Settings].

* Poniżej przedstawiono sposób wstawienia filtru stopki, który powoduje wyświetlenie tekstu HTML u dołu wysyłanej wiadomości e-mail.

      message.addHeader("X-SMTPAPI",
          "{\"filters\":
          {\"footer\":
          {\"settings\":
          {\"enable\":1,\"text/html\":
          \"<html><b>Thank you</b> for your business.</html>\"}}}}");
* Innym przykładem filtru jest śledzenie kliknięć. Załóżmy, że tekst wiadomości e-mail zawiera hiperłącze, takie jak następujące, i chcesz śledzić współczynnik kliknięć:

      messagePart.setContent(
          "Hello,
          <p>This is the body of the message. Visit
          <a href='http://www.contoso.com'>http://www.contoso.com</a>.</p>
          Thank you.",
          "text/html");
* Aby włączyć śledzenie kliknięć, użyj następującego kodu:

      message.addHeader("X-SMTPAPI",
          "{\"filters\":
          {\"clicktrack\":
          {\"settings\":
          {\"enable\":1}}}}");

## <a name="how-to-update-email-properties"></a>Jak: Aktualizowanie właściwości poczty e-mail
Niektóre właściwości wiadomości e-mail można nadpisywały za pomocą **właściwości set Property** lub dołączane za pomocą funkcji add **Property**.

Na przykład, aby określić **adresy ReplyTo,** należy użyć następujących czynności:

    InternetAddress addresses[] =
        { new InternetAddress("john@contoso.com"),
          new InternetAddress("wendy@contoso.com") };

    message.setReplyTo(addresses);

Aby dodać adresata **DW,** użyj następujących czynności:

    message.addRecipient(Message.RecipientType.CC, new
    InternetAddress("john@contoso.com"));

## <a name="how-to-use-additional-sendgrid-services"></a>Jak: Korzystanie z dodatkowych usług SendGrid
SendGrid oferuje interfejsy API oparte na sieci Web, których można użyć do wykorzystania dodatkowych funkcji SendGrid z aplikacji platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [dokumentację interfejsu API SendGrid][SendGrid API documentation].

## <a name="next-steps"></a>Następne kroki
Teraz, gdy nauczyłeś się podstaw usługi SendGrid Email, skorzystaj z tych linków, aby dowiedzieć się więcej.

* Przykład, który pokazuje przy użyciu SendGrid we wdrożeniu platformy Azure: [Jak wysłać posoń posyłów e-mail przy użyciu SendGrid z oprogramowania Java we wdrożeniu platformy Azure](store-sendgrid-java-how-to-send-email-example.md)
* SendGrid Java SDK:<https://sendgrid.com/docs/Code_Examples/java.html>
* SendGrid API dokumentacji:<https://sendgrid.com/docs/API_Reference/index.html>
* Oferta specjalna SendGrid dla klientów platformy Azure:<https://sendgrid.com/windowsazure.html>

[https://sendgrid.com]: https://sendgrid.com
[https://sendgrid.com/pricing.html]: https://sendgrid.com/pricing.html
[http://www.sendgrid.com/azure.html]: https://www.sendgrid.com/windowsazure.html
[https://sendgrid.com/features]: https://sendgrid.com/features
[https://www.oracle.com/technetwork/java/javamail]: https://www.oracle.com/technetwork/java/javamail/index.html
[Filter Settings]: https://sendgrid.com/docs/API_Reference/Web_API/filter_settings.html
[SendGrid API documentation]: https://sendgrid.com/docs/API_Reference/index.html
[https://sendgrid.com/azure.html]: https://sendgrid.com/windowsazure.html
[chmurowa usługa poczty e-mail]: https://sendgrid.com/email-solutions
[transakcyjna dostawa wiadomości e-mail]: https://sendgrid.com/transactional-email
