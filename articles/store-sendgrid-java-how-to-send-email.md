---
title: Jak używać usługi e-mail SendGrid (Java) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wysyłać wiadomości e-mail za pomocą usługi e-mail SendGrid na platformie Azure. Przykłady kodu napisane w języku Java.
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
ms.openlocfilehash: 0cb75c1acb731432ed524560698e3355699b2500
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60931215"
---
# <a name="how-to-send-email-using-sendgrid-from-java"></a>Jak wysyłać pocztę E-mail za pomocą usługi SendGrid w języku Java
Ten przewodnik pokazuje sposób wykonywania typowych zadań programistycznych za pomocą usługi e-mail SendGrid na platformie Azure. Przykłady są napisane w języku Java. Omówione scenariusze obejmują **konstruowanie e-mail**, **wysyłania wiadomości e-mail**, **dodawania załączników**, **przy użyciu filtrów**i **aktualizowanie właściwości**. Aby uzyskać więcej informacji na temat usługi SendGrid i wysyłania wiadomości e-mail, zobacz [następne kroki](#next-steps) sekcji.

## <a name="what-is-the-sendgrid-email-service"></a>Co to jest usługa SendGrid poczty E-mail?
Usługa SendGrid jest [usługa oparta na chmurze poczty e-mail] zapewniająca niezawodne [dostarczania wiadomości e-mail dotyczącej transakcji], skalowalność i analizę w czasie rzeczywistym oraz udostępnia elastyczne interfejsy API, które umożliwiają łatwe niestandardową integrację. Typowe scenariusze użycia usługi SendGrid obejmują:

* Nie są automatycznie wysyłane potwierdzenia do klientów
* Administrowanie dystrybucji Wyświetla wysyłania klientom miesięczne e-rozsyłanych materiałach marketingowych firmowych i ofert specjalnych
* Zbieranie metryk w czasie rzeczywistym w przypadku elementów, np. blokowania wiadomości e-mail i szybkość reakcji klientów
* Generowanie raportów, aby ułatwić identyfikację trendów
* Przekazywanie zapytania klientów
* Powiadomienia e-mail z aplikacji

Aby uzyskać więcej informacji, zobacz <https://sendgrid.com>.

## <a name="create-a-sendgrid-account"></a>Utwórz konto usługi SendGrid
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="how-to-use-the-javaxmail-libraries"></a>Instrukcje: Korzystanie z bibliotek javax.mail
Uzyskaj javax.mail biblioteki, na przykład z <https://www.oracle.com/technetwork/java/javamail> i zaimportuj je do kodu. Na ogólne proces przy użyciu biblioteki javax.mail do wysyłania wiadomości e-mail przy użyciu protokołu SMTP jest wykonać następujące czynności:

1. Określ wartości SMTP, łącznie z serwera SMTP, czyli firma sendgrid wymaga tego smtp.sendgrid.net.

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

1. Rozszerzanie *javax.mail.Authenticator* klasy i w danej implementacji *getPasswordAuthentication* metody zwracają SendGrid, nazwę użytkownika i hasło.  

       private class SMTPAuthenticator extends javax.mail.Authenticator {
       public PasswordAuthentication getPasswordAuthentication() {
          String username = SMTP_AUTH_USER;
          String password = SMTP_AUTH_PWD;
          return new PasswordAuthentication(username, password);
       }
2. Tworzenia sesji uwierzytelnionych wiadomości e-mail za pośrednictwem *javax.mail.Session* obiektu.  

       Authenticator auth = new SMTPAuthenticator();
       Session mailSession = Session.getDefaultInstance(properties, auth);
3. Utwórz wiadomość i przypisać **do**, **z**, **podmiotu** i zawartości wartości. Jest to pokazane w [How to: Utwórz wiadomość E-mail](#how-to-create-an-email) sekcji.
4. Wysyłanie wiadomości przy użyciu *javax.mail.Transport* obiektu. Jest to pokazane w [How to: Wyślij wiadomość E-mail] [# porad — Wyślij wiadomość — wiadomość e-mail] części.

## <a name="how-to-create-an-email"></a>Instrukcje: Utwórz wiadomość e-mail
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

## <a name="how-to-send-an-email"></a>Instrukcje: Wyślij wiadomość e-mail
Poniżej przedstawiono sposób wysyłania wiadomości e-mail.

    Transport transport = mailSession.getTransport();
    // Connect the transport object.
    transport.connect();
    // Send the message.
    transport.sendMessage(message, message.getAllRecipients());
    // Close the connection.
    transport.close();

## <a name="how-to-add-an-attachment"></a>Instrukcje: Dodaj załącznik
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

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Instrukcje: Użyj filtrów, aby umożliwić stopki, śledzenia i analizy
Usługa SendGrid umożliwia funkcje dodatkowe poczty e-mail za pośrednictwem *filtry*. Są to ustawienia, które można dodać do wiadomości e-mail, aby włączyć określonych funkcji, takich jak umożliwianie śledzenie kliknięć, usługi Google analytics, subskrypcji, śledzenia i tak dalej. Aby uzyskać pełną listę filtrów, zobacz [ustawienia filtru][Filter Settings].

* Poniżej przedstawiono sposób wstawiania filtr stopki, który skutkuje tekst HTML, które pojawiają się w dolnej części wiadomości e-mail są wysyłane.

      message.addHeader("X-SMTPAPI",
          "{\"filters\":
          {\"footer\":
          {\"settings\":
          {\"enable\":1,\"text/html\":
          \"<html><b>Thank you</b> for your business.</html>\"}}}}");
* Innym przykładem filtru jest kliknij śledzenia. Załóżmy, że tekst wiadomości e-mail zawiera hiperłącza, podobny do następującego i chcesz śledzić tempo kliknij pozycję:

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

## <a name="how-to-update-email-properties"></a>Instrukcje: Aktualizowanie właściwości wiadomości e-mail
Niektóre właściwości wiadomości e-mail może zostać zastąpiona przy użyciu **ustaw właściwość** lub dołączonych przy użyciu **Dodaj właściwość**.

Na przykład, aby określić **ReplyTo** adresów, należy użyć następującego:

    InternetAddress addresses[] =
        { new InternetAddress("john@contoso.com"),
          new InternetAddress("wendy@contoso.com") };

    message.setReplyTo(addresses);

Aby dodać **DW** adresata, należy użyć następującego:

    message.addRecipient(Message.RecipientType.CC, new
    InternetAddress("john@contoso.com"));

## <a name="how-to-use-additional-sendgrid-services"></a>Instrukcje: Użyj dodatkowych usługach SendGrid
Usługa SendGrid oferuje oparte na sieci web interfejsów API, które można korzystać z dodatkowych funkcji usługi SendGrid z aplikacji systemu Azure. Aby uzyskać szczegółowe informacje, zobacz [dokumentacji interfejsu API usługi SendGrid][SendGrid API documentation].

## <a name="next-steps"></a>Kolejne kroki
Teraz, kiedy znasz już podstawy usługi E-mail SendGrid, skorzystaj z poniższych linków, aby dowiedzieć się więcej.

* Przykład demonstrujący, za pomocą usługi SendGrid we wdrożeniu platformy Azure: [Jak wysyłać pocztę e-mail przy użyciu usługi SendGrid w języku Java we wdrożeniu platformy Azure](store-sendgrid-java-how-to-send-email-example.md)
* Zestawu SDK Java usługi SendGrid <https://sendgrid.com/docs/Code_Examples/java.html>
* Dokumentacja interfejsu API usługi SendGrid: <https://sendgrid.com/docs/API_Reference/index.html>
* Usługa SendGrid specjalna oferta dla klientów platformy Azure: <https://sendgrid.com/windowsazure.html>

[https://sendgrid.com]: https://sendgrid.com
[https://sendgrid.com/pricing.html]: https://sendgrid.com/pricing.html
[http://www.sendgrid.com/azure.html]: https://www.sendgrid.com/windowsazure.html
[https://sendgrid.com/features]: https://sendgrid.com/features
[https://www.oracle.com/technetwork/java/javamail]: https://www.oracle.com/technetwork/java/javamail/index.html
[Filter Settings]: https://sendgrid.com/docs/API_Reference/Web_API/filter_settings.html
[SendGrid API documentation]: https://sendgrid.com/docs/API_Reference/index.html
[https://sendgrid.com/azure.html]: https://sendgrid.com/windowsazure.html
[Usługa oparta na chmurze poczty e-mail]: https://sendgrid.com/email-solutions
[dostarczania wiadomości e-mail dotyczącej transakcji]: https://sendgrid.com/transactional-email
