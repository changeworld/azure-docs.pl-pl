---
title: store-sendgrid-java-how-to-send-email-example
description: Jak wysyłać pocztę e-mail przy użyciu usługi SendGrid w języku Java we wdrożeniu platformy Azure
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
ms.openlocfilehash: 79cb9bb82862f5720d5ec2262ba30dbbcf3e3f66
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60930176"
---
# <a name="how-to-send-email-using-sendgrid-from-java-in-an-azure-deployment"></a>Jak wysyłać pocztę E-mail przy użyciu usługi SendGrid w języku Java we wdrożeniu platformy Azure
Poniższy przykład pokazuje, jak SendGrid umożliwia wysyłanie wiadomości e-mail ze strony sieci web hostowanych na platformie Azure. Wynikłej aplikacji zostanie wyświetlony monit dla wartości adresów e-mail, jak pokazano na poniższym zrzucie ekranu.

![Formularz wiadomości e-mail][emailform]

Wynikowy poczty e-mail będzie wyglądać podobnie do poniższej zrzut ekranu.

![Wiadomość e-mail][emailsent]

Należy wykonać następujące czynności, aby użyć kodu, w tym temacie:

1. Uzyskaj plikach JAR javax.mail, na przykład z <https://www.oracle.com/technetwork/java/javamail/index.html>.
2. Dodaj plikach JAR do ścieżki kompilacji języka Java.
3. Jeśli używasz środowiska Eclipse do tworzenia tej aplikacji Java, może zawierać bibliotek usługi SendGrid w pliku wdrożenia aplikacji (WAR) przy użyciu funkcji zestawu wdrażania w środowisku Eclipse. Jeśli nie używasz środowiska Eclipse do tworzenia tej aplikacji Java, upewnij się, bibliotek są uwzględnione w ramach tej samej roli platformy Azure jako swoją aplikację Java i dodany do ścieżki klas w aplikacji.

Musi również mieć własne SendGrid użytkownika i hasło, aby móc wysyłać wiadomości e-mail. Aby rozpocząć korzystanie z usługi SendGrid, zobacz [jak wysyłać pocztę e-mail za pomocą usługi SendGrid w języku Java](store-sendgrid-java-how-to-send-email.md).

Ponadto znajomość odpowiednie informacje zawiera temat [tworzenie aplikacji Hello World na platformie Azure w środowisku Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app?view=azure-java-stable), lub z innych technik do hostowania aplikacji Java na platformie Azure, jeśli nie używasz środowiska Eclipse, zdecydowanie zaleca się.

## <a name="create-a-web-form-for-sending-email"></a>Tworzenie formularza sieci web, wysyłania poczty e-mail
Poniższy kod przedstawia sposób tworzenia formularza sieci web do pobierania danych użytkownika do wysyłania wiadomości e-mail. Dla celów tej zawartości, nosi nazwę pliku JSP **emailform.jsp**.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
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

## <a name="create-the-code-to-send-the-email"></a>Tworzenie kodu, aby wysłać wiadomość e-mail
Następujący kod, który jest wywoływany po wypełnieniu formularza w emailform.jsp, tworzy wiadomości e-mail i wysyła je. Dla celów tej zawartości, nosi nazwę pliku JSP **sendemail.jsp**.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" import="javax.activation.*, javax.mail.*, javax.mail.internet.*, java.util.Date, java.util.Properties" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
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

Oprócz wysyłania wiadomości e-mail, emailform.jsp zawiera wynik dla użytkownika; Przykładem jest poniższy zrzut ekranu:

![Wysyłanie poczty wynik][emailresult]

## <a name="next-steps"></a>Kolejne kroki
Wdróż aplikację w emulatorze obliczeń i w przeglądarce sieci uruchomić emailform.jsp, wprowadź wartości w formularzu, kliknij przycisk **Wyślij tę wiadomość e-mail**, a następnie zobacz wyniki w sendemail.jsp.

Ten kod podano WAM, jak używać usługi SendGrid w języku Java na platformie Azure. Przed wdrożeniem na platformie Azure w środowisku produkcyjnym, można dodać więcej obsługę błędów lub innych funkcji. Na przykład: 

* Można użyć usługi Azure storage blob lub bazy danych SQL do przechowywania adresów e-mail i wiadomości e-mail, a nie za pomocą formularza sieci web. Aby dowiedzieć się, jak za pomocą usługi Azure storage BLOB w języku Java, zobacz [sposób użycia usługi Blob Storage w języku Java](https://azure.microsoft.com/develop/java/how-to-guides/blob-storage/). Aby uzyskać informacje dotyczące korzystania z bazy danych SQL w języku Java, zobacz [przy użyciu bazy danych SQL w języku Java](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-java).
* Aby uzyskać więcej informacji na temat za pomocą usługi SendGrid w języku Java, zobacz [jak wysyłać pocztę e-mail za pomocą usługi SendGrid w języku Java](store-sendgrid-java-how-to-send-email.md).

[emailform]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailform.jpg
[emailsent]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailSent.jpg
[emailresult]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaResult.jpg
