---
title: Jak używać usługi poczty e-mail SendGrid (Java) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wysłać wiadomość e-mail z usługi poczty e-mail SendGrid na platformie Azure. Przykłady kodu napisane w języku Java.
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
ms.author: elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork
ms.openlocfilehash: 85a0e302626ca14ac039ee6f662f372ddbeb62c5
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="how-to-send-email-using-sendgrid-from-java"></a>Sposób wysyłania poczty E-mail przy użyciu SendGrid za pomocą języka Java
W tym przewodniku przedstawiono sposób wykonywania typowych zadań programowania usługi poczty e-mail SendGrid na platformie Azure. Przykłady są napisane w języku Java. Omówione scenariusze obejmują **konstruowania e-mail**, **wysyłania wiadomości e-mail**, **dodawanie załączników**, **za pomocą filtrów**i **aktualizowanie właściwości**. Aby uzyskać więcej informacji na SendGrid i wysyłania wiadomości e-mail, zobacz [następne kroki](#next-steps) sekcji.

## <a name="what-is-the-sendgrid-email-service"></a>Co to jest usługa SendGrid poczty E-mail?
Jest SendGrid [e-mail opartej na chmurze usługa] zapewnia niezawodne [dostarczania transakcyjnych wiadomości e-mail], skalowalności i analiz w czasie rzeczywistym oraz elastyczne interfejsów API, które umożliwiają łatwe niestandardowej integracji. Typowe scenariusze użycia SendGrid obejmują:

* Potwierdzenia są automatycznie wysyłane do klientów
* Administrowanie dystrybucji wymieniono wysyłania klientów miesięczne e ulotki i oferty specjalne
* Zbieranie metryk w czasie rzeczywistym dla zablokowanych poczty e-mail i czasu odpowiedzi klienta
* Generowanie raportów, aby ułatwić identyfikację trendów
* Zapytania dotyczące przekazywania klienta
* Powiadomienia e-mail z aplikacji

Aby uzyskać więcej informacji, zobacz <http://sendgrid.com>.

## <a name="create-a-sendgrid-account"></a>Utwórz konto SendGrid
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="how-to-use-the-javaxmail-libraries"></a>Porady: Użyj biblioteki javax.mail
Na przykład uzyskać bibliotek javax.mail z <http://www.oracle.com/technetwork/java/javamail> i zaimportuj je do kodu. W wysokiego poziomu proces za pomocą biblioteki javax.mail do wysyłania wiadomości e-mail za pomocą protokołu SMTP jest wykonanie następujących czynności:

1. Określ wartości SMTP, w tym do serwera SMTP, który SendGrid jest smtp.sendgrid.net.

```
        import java.util.Properties;
        import javax.activation.*;
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

1. Rozszerzanie *javax.mail.Authenticator* klasy w implementacji *getPasswordAuthentication* metody zwrócić SendGrid nazwę użytkownika i hasło.  

       private class SMTPAuthenticator extends javax.mail.Authenticator {
       public PasswordAuthentication getPasswordAuthentication() {
          String username = SMTP_AUTH_USER;
          String password = SMTP_AUTH_PWD;
          return new PasswordAuthentication(username, password);
       }
2. Tworzenia sesji uwierzytelnionych poczty e-mail za pośrednictwem *javax.mail.Session* obiektu.  

       Authenticator auth = new SMTPAuthenticator();
       Session mailSession = Session.getDefaultInstance(properties, auth);
3. Utwórz wiadomość i przypisać **do**, **z**, **podmiotu** i zawartości wartości. Przedstawiono to w [jak: utworzyć wiadomości E-mail](#how-to-create-an-email) sekcji.
4. Wysłać komunikatu za pośrednictwem *javax.mail.Transport* obiektu. Przedstawiono to w [jak: Wyślij wiadomość E-mail] [porady: wysyłanie wiadomości E-mail] sekcji.

## <a name="how-to-create-an-email"></a>Porady: Tworzenie wiadomości e-mail
Poniżej przedstawiono sposób określania wartości dla wiadomości e-mail.

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

## <a name="how-to-send-an-email"></a>Porady: wysyłanie wiadomości e-mail
Poniżej przedstawiono sposób wysyłania wiadomości e-mail.

    Transport transport = mailSession.getTransport();
    // Connect the transport object.
    transport.connect();
    // Send the message.
    transport.sendMessage(message, message.getAllRecipients());
    // Close the connection.
    transport.close();

## <a name="how-to-add-an-attachment"></a>Porady: Dodawanie załącznika
Poniższy kod przedstawia sposób dodawania załącznika.

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

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Porady: Użyj filtrów, aby włączyć stopki, śledzenia i analiza
SendGrid udostępnia funkcje dodatkowe poczty e-mail za pośrednictwem *filtry*. Są to ustawienia, które mogą zostać dodane do wiadomości e-mail, aby włączyć określonych funkcji, takich jak włączenie kliknij śledzenia, Google analytics, subskrypcji, śledzenia i tak dalej. Aby uzyskać pełną listę filtrów, zobacz [ustawienia filtru][Filter Settings].

* Poniżej przedstawiono sposób wstawiania stopki filtr, który powoduje znajdujących się w dolnej części wiadomości e-mail wysyłane tekstu w formacie HTML.

      message.addHeader("X-SMTPAPI",
          "{\"filters\":
          {\"footer\":
          {\"settings\":
          {\"enable\":1,\"text/html\":
          \"<html><b>Thank you</b> for your business.</html>\"}}}}");
* Innym przykładem filtr jest kliknij śledzenia. Załóżmy, że tekst wiadomości e-mail zawiera hiperłącza, takie jak wymienione poniżej, i chcesz śledzić tempo kliknij:

      messagePart.setContent(
          "Hello,
          <p>This is the body of the message. Visit
          <a href='http://www.contoso.com'>http://www.contoso.com</a>.</p>
          Thank you.",
          "text/html");
* Aby włączyć kliknij śledzenia, należy użyć poniższego kodu:

      message.addHeader("X-SMTPAPI",
          "{\"filters\":
          {\"clicktrack\":
          {\"settings\":
          {\"enable\":1}}}}");

## <a name="how-to-update-email-properties"></a>Porady: Aktualizacja właściwości wiadomości e-mail
Niektóre właściwości wiadomości e-mail może zostać zastąpiona przy użyciu **ustawić * właściwość*** lub dołączonych za pomocą **dodać*właściwości ***.

Na przykład, aby określić **ReplyTo** adresów, należy użyć następującego:

    InternetAddress addresses[] =
        { new InternetAddress("john@contoso.com"),
          new InternetAddress("wendy@contoso.com") };

    message.setReplyTo(addresses);

Aby dodać **DW** adresata, należy użyć następującego:

    message.addRecipient(Message.RecipientType.CC, new
    InternetAddress("john@contoso.com"));

## <a name="how-to-use-additional-sendgrid-services"></a>Porady: Użyj dodatkowych usług SendGrid
SendGrid oferuje opartych na sieci web interfejsów API, które można wykorzystać dodatkowe funkcje włączenie w aplikacji platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [dokumentacji interfejsu API SendGrid][SendGrid API documentation].

## <a name="next-steps"></a>Kolejne kroki
Teraz, kiedy znasz już podstawy usługi poczty E-mail SendGrid, skorzystaj z poniższych linków, aby dowiedzieć się więcej.

* Przykład przedstawiający we wdrożeniu Azure przy użyciu SendGrid: [sposób wysyłania poczty e-mail przy użyciu SendGrid za pomocą języka Java w wdrożenia usługi Azure](store-sendgrid-java-how-to-send-email-example.md)
* Włączenie Java SDK: <https://sendgrid.com/docs/Code_Examples/java.html>
* Dokumentacja interfejsu API SendGrid: <https://sendgrid.com/docs/API_Reference/index.html>
* Oferta specjalna SendGrid dla klientów platformy Azure: <https://sendgrid.com/windowsazure.html>

[http://sendgrid.com]: https://sendgrid.com
[http://sendgrid.com/pricing.html]: http://sendgrid.com/pricing.html
[http://www.sendgrid.com/azure.html]: https://www.sendgrid.com/windowsazure.html
[http://sendgrid.com/features]: https://sendgrid.com/features
[http://www.oracle.com/technetwork/java/javamail]: http://www.oracle.com/technetwork/java/javamail/index.html
[Filter Settings]: https://sendgrid.com/docs/API_Reference/Web_API/filter_settings.html
[SendGrid API documentation]: https://sendgrid.com/docs/API_Reference/index.html
[http://sendgrid.com/azure.html]: https://sendgrid.com/windowsazure.html
[e-mail opartej na chmurze usługa]: https://sendgrid.com/email-solutions
[dostarczania transakcyjnych wiadomości e-mail]: https://sendgrid.com/transactional-email
