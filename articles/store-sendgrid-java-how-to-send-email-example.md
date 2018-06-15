---
title: store-sendgrid-Java-How-to-send-email-example
description: Sposób wysyłania poczty e-mail przy użyciu SendGrid za pomocą języka Java we wdrożeniu usługi Azure
services: ''
documentationcenter: java
author: thinkingserious
manager: sendgrid
editor: mollybos
ms.assetid: af096a73-6985-4350-92e4-ce1722c8d72f
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/30/2014
ms.author: vibhork;dominic.may@sendgrid.com;elmer.thomas@sendgrid.com
ms.openlocfilehash: d80d7d9c54bad12a4d26d8623eeccdf9bc2a743a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873947"
---
# <a name="how-to-send-email-using-sendgrid-from-java-in-an-azure-deployment"></a>Sposób wysyłania poczty E-mail przy użyciu SendGrid za pomocą języka Java w wdrożenia usługi Azure
Poniższy przykład pokazuje, jak SendGrid umożliwia wysyłanie wiadomości e-mail ze strony sieci web hostowanej na platformie Azure. Wynikowy aplikacji zostanie monit o podanie wartości poczty e-mail, jak pokazano na poniższym zrzucie ekranu.

![Formularz poczty e-mail][emailform]

Wynikowa poczty e-mail będzie wyglądać podobnie jak na poniższym zrzucie ekranu.

![Wiadomości e-mail][emailsent]

Należy wykonać następujące czynności, aby użyć kodu w tym temacie:

1. Na przykład uzyskać słoików javax.mail z <http://www.oracle.com/technetwork/java/javamail/index.html>.
2. Dodaj słoików do ścieżki kompilacji języka Java.
3. Jeśli używasz programu Eclipse do tworzenia aplikacji w języku Java, bibliotek SendGrid można uwzględnić w pliku wdrożenia aplikacji (plik WAR) za pomocą funkcji zestawu wdrożenia w środowisku Eclipse. Jeśli nie używasz programu Eclipse do tworzenia aplikacji w języku Java, upewnij się, biblioteki są zawarte w tej samej roli Azure jako aplikacji w języku Java i dodawany do ścieżki klasy aplikacji.

Musi również mieć własne SendGrid nazwę użytkownika i hasło, aby można było wysyłać wiadomości e-mail. Aby rozpocząć sendgrid, zobacz [sposób wysyłania poczty e-mail przy użyciu SendGrid za pomocą języka Java](store-sendgrid-java-how-to-send-email.md).

Ponadto znajomość informacji w [tworzenia aplikacji Hello World na platformie Azure w programie Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944), lub z innych technik do hostowania aplikacji Java na platformie Azure, jeśli nie używasz programu Eclipse jest zdecydowanie zalecane.

## <a name="create-a-web-form-for-sending-email"></a>Tworzenie formularza sieci web do wysyłania wiadomości e-mail
Poniższy kod przedstawia sposób tworzenia formularzy sieci web do pobierania danych użytkownika do wysyłania wiadomości e-mail. Dla celów tej zawartości, nosi nazwę pliku JSP **emailform.jsp**.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Email form</title>
    </head>
    <body>
     <p>Fill in all fields and click <b>Send this email</b>.</p>
     <br/>
      <form action="sendemail.jsp" method="post">
       <table>
         <tr>
           <td>To:</td>
           <td><input type="text" size=50 name="emailTo">
           </td>
         </tr>
         <tr>
           <td>From:</td>
           <td><input type="text" size=50 name="emailFrom">
           </td>
         </tr>
         <tr>
           <td>Subject:</td>
           <td><input type="text" size=100 name="emailSubject" value="My email subject">
           </td>
         </tr>
         <tr>
           <td>Text:</td>
           <td><input type="text" size=400 name="emailText" value="Hello,<p>This is my message.</p>Thank you." />
           </td>
         </tr>
         <tr>
           <td>SendGrid user name:</td>
           <td><input type="text" name="sendGridUser">
           </td>
         </tr>
         <tr>
           <td>SendGrid password:</td>
           <td><input type="password" name="sendGridPassword">
           </td>
         </tr>
         <tr>
           <td colspan=2><input type="submit" value="Send this email">
           </td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

## <a name="create-the-code-to-send-the-email"></a>Utwórz kod do wysyłania wiadomości e-mail
Następujący kod, który jest wywoływana po wypełnieniu formularza w emailform.jsp, tworzy wiadomości e-mail i wysyła je. Dla celów tej zawartości, nosi nazwę pliku JSP **sendemail.jsp**.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" import="javax.activation.*, javax.mail.*, javax.mail.internet.*, java.util.Date, java.util.Properties" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Email processing happens here</title>
    </head>
    <body>
        <b>This is my send mail page.</b><p/>
     <%

     final String sendGridUser = request.getParameter("sendGridUser");
     final String sendGridPassword = request.getParameter("sendGridPassword");

     class SMTPAuthenticator extends Authenticator
     {
       public PasswordAuthentication getPasswordAuthentication()
       {
            String username = sendGridUser;
            String password = sendGridPassword;

            return new PasswordAuthentication(username, password);   
       }
     }
     try
     {

         // The SendGrid SMTP server.
         String SMTP_HOST_NAME = "smtp.sendgrid.net";

         Properties properties;

         properties = new Properties();

         // Specify SMTP values.
         properties.put("mail.transport.protocol", "smtp");
         properties.put("mail.smtp.host", SMTP_HOST_NAME);
         properties.put("mail.smtp.port", 587);
         properties.put("mail.smtp.auth", "true");

         // Display the email fields entered by the user. 
         out.println("Value entered for email Subject: " + request.getParameter("emailSubject") + "<br/>");        
         out.println("Value entered for email      To: " + request.getParameter("emailTo") + "<br/>");
         out.println("Value entered for email    From: " + request.getParameter("emailFrom") + "<br/>");
         out.println("Value entered for email    Text: " + "<br/>" + request.getParameter("emailText") + "<br/>");

         // Create the authenticator object.
         Authenticator authenticator = new SMTPAuthenticator();

         // Create the mail session object.
         Session mailSession;
         mailSession = Session.getDefaultInstance(properties, authenticator);

         // Display debug information to stdout, useful when using the
         // compute emulator during development.
         mailSession.setDebug(true);

         // Create the message and message part objects.
         MimeMessage message;
         Multipart multipart;
         MimeBodyPart messagePart; 

         message = new MimeMessage(mailSession);

         multipart = new MimeMultipart("alternative");
         messagePart = new MimeBodyPart();
         messagePart.setContent(request.getParameter("emailText"), "text/html");
         multipart.addBodyPart(messagePart);            

         // Specify the email To, From, Subject and Content. 
         message.setFrom(new InternetAddress(request.getParameter("emailFrom")));
         message.addRecipient(Message.RecipientType.TO, new InternetAddress(request.getParameter("emailTo")));
         message.setSubject(request.getParameter("emailSubject")); 
         message.setContent(multipart);

         // Uncomment the following if you want to add a footer.
         // message.addHeader("X-SMTPAPI", "{\"filters\": {\"footer\": {\"settings\": {\"enable\":1,\"text/html\": \"<html>This is my <b>email footer</b>.</html>\"}}}}");

         // Uncomment the following if you want to enable click tracking.
         // message.addHeader("X-SMTPAPI", "{\"filters\": {\"clicktrack\": {\"settings\": {\"enable\":1}}}}");

         Transport transport;
         transport = mailSession.getTransport();
         // Connect the transport object.
         transport.connect();
         // Send the message.
         transport.sendMessage(message,  message.getRecipients(Message.RecipientType.TO));
         // Close the connection.
         transport.close();

        out.println("<p>Email processing completed.</p>");

     }
     catch (Exception e)
     {
         out.println("<p>Exception encountered: " + 
                            e.getMessage()     +
                            "</p>");   
     }
    %>

    </body>
    </html>

Oprócz wysyłania wiadomości e-mail, emailform.jsp udostępnia wynik dla użytkownika; Przykładem jest na poniższym zrzucie ekranu:

![Wysyłanie poczty wyników][emailresult]

## <a name="next-steps"></a>Następne kroki
Wdrażanie aplikacji w usłudze emulatora obliczeń i w przeglądarce Uruchom emailform.jsp, wprowadź wartości w formularzu, kliknij przycisk **wysłać tę wiadomość e-mail**, a następnie sprawdź wyniki w sendemail.jsp.

Ten kod został podany pokazanie sposobu używania SendGrid w języku Java na platformie Azure. Przed wdrożeniem na platformie Azure w środowisku produkcyjnym, możesz dodać więcej obsługi błędów lub innych funkcji. Na przykład: 

* Możesz użyć obiektów blob magazynu Azure lub bazy danych SQL do przechowywania adresów e-mail i wiadomości e-mail zamiast za pomocą formularza sieci web. Aby dowiedzieć się, jak za pomocą obiektów blob magazynu Azure w języku Java, zobacz [jak używać usługi magazynu obiektów Blob w języku Java](https://azure.microsoft.com/develop/java/how-to-guides/blob-storage/). Aby uzyskać informacje dotyczące korzystania z bazy danych SQL w języku Java, zobacz [przy użyciu bazy danych SQL w języku Java](https://azure.microsoft.com/develop/java/how-to-guides/using-sql-azure-in-java/).
* Można użyć `RoleEnvironment.getConfigurationSettings` pobrać ustawienia konfiguracji w danym wdrożeniu, zamiast wartości te są pobierane za pomocą formularza sieci web SendGrid nazwy użytkownika i hasła. Informacje o `RoleEnvironment` , zobacz [za pomocą biblioteki środowiska uruchomieniowego usługi Azure w JSP](http://msdn.microsoft.com/library/windowsazure/hh690948) i dokumentacji pakietu środowiska uruchomieniowego usługi Azure w <http://dl.windowsazure.com/javadoc>.
* Aby uzyskać więcej informacji o używaniu SendGrid w języku Java, zobacz [sposób wysyłania poczty e-mail przy użyciu SendGrid za pomocą języka Java](store-sendgrid-java-how-to-send-email.md).

[emailform]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailform.jpg
[emailsent]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailSent.jpg
[emailresult]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaResult.jpg
