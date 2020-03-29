---
title: Jak korzystać z usługi sendgrid (.NET) | Dokumenty firmy Microsoft
description: Dowiedz się, jak wysyłać wiadomości e-mail za pomocą usługi sendgrid na platformie Azure. Przykłady kodu napisane w języku C# i użyj interfejsu API platformy .NET.
services: ''
documentationcenter: .net
author: georgewallace
ms.author: gwallace
editor: ''
ms.assetid: 21bf4028-9046-476b-9799-3d3082a0f84c
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/15/2017
ms.reviewer: dx@sendgrid.com
ms.openlocfilehash: 33df6b5c8c5c16a6eb896944de05068affc2b407
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062201"
---
# <a name="how-to-send-email-using-sendgrid-with-azure"></a>Jak wysyłać wiadomości e-mail przy użyciu sendgrid z platformą Azure
## <a name="overview"></a>Omówienie
W tym przewodniku pokazano, jak wykonywać typowe zadania programowania za pomocą usługi sendgrid poczty e-mail na platformie Azure. Przykłady są zapisywane\# w języku C i obsługuje .NET Standard 1.3. Scenariusze obejmują konstruowanie poczty e-mail, wysyłanie wiadomości e-mail, dodawanie załączników i włączanie różnych ustawień poczty i śledzenia. Aby uzyskać więcej informacji na temat SendGrid i wysyłania wiadomości e-mail, zobacz [następne kroki][Next steps] sekcji.

## <a name="what-is-the-sendgrid-email-service"></a>Co to jest usługa poczty e-mail SendGrid?
SendGrid to [chmurowa usługa poczty e-mail,] która zapewnia niezawodne [transakcyjne dostarczanie wiadomości e-mail,]skalowalność i analizę w czasie rzeczywistym wraz z elastycznymi interfejsami API, które ułatwiają niestandardową integrację. Typowe przypadki użycia SendGrid obejmują:

* Automatyczne wysyłanie potwierdzeń lub potwierdzeń zakupu do odbiorców.
* Administrowanie listami dystrybucyjnymi do wysyłania klientom miesięcznych ulotek i promocji.
* Zbieranie danych w czasie rzeczywistym na takie rzeczy jak zablokowana poczta e-mail i zaangażowanie klientów.
* Przekazywanie zapytań klientów.
* Przetwarzanie przychodzących wiadomości e-mail.

Aby uzyskać więcej [https://sendgrid.com](https://sendgrid.com) informacji, odwiedź lub SendGrid w [C# biblioteki][sendgrid-csharp] GitHub repozytorium.

## <a name="create-a-sendgrid-account"></a>Tworzenie konta SendGrid
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-net-class-library"></a>Odwoływanie się do biblioteki klas SendGrid .NET
[Pakiet SendGrid NuGet](https://www.nuget.org/packages/Sendgrid) jest najprostszym sposobem, aby uzyskać interfejs API SendGrid i skonfigurować aplikację ze wszystkimi zależnościami. NuGet to rozszerzenie programu Visual Studio dołączone do programu Microsoft Visual Studio 2015 lub powyżej, które ułatwia instalowanie i aktualizowanie bibliotek i narzędzi.

> [!NOTE]
> Aby zainstalować NuGet, jeśli korzystasz z wersji programu Visual Studio wcześniej [https://www.nuget.org](https://www.nuget.org)niż Visual Studio 2015, odwiedź i kliknij przycisk **Zainstaluj NuGet.**
>
>

Aby zainstalować pakiet SendGrid NuGet w aplikacji, wykonaj następujące czynności:

1. Kliknij **nowy projekt** i wybierz **szablon**.

   ![Tworzenie nowego projektu][create-new-project]
2. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy **pozycję Odwołania**, a następnie kliknij polecenie **Zarządzaj pakietami NuGet**.

   ![Pakiet SendGrid NuGet][SendGrid-NuGet-package]
3. Wyszukaj **sendgrid** i wybierz element **SendGrid** na liście wyników.
4. Wybierz najnowszą stabilną wersję pakietu Nuget z listy rozwijanej wersji, aby móc pracować z modelem obiektów i interfejsami API zademonstrowanym w tym artykule.

   ![Pakiet SendGrid][sendgrid-package]
5. Kliknij **przycisk Zainstaluj,** aby zakończyć instalację, a następnie zamknij to okno dialogowe.

Biblioteka klas .NET sendgrid nazywa się **SendGrid**. Zawiera następujące przestrzenie nazw:

* **SendGrid** do komunikowania się z API SendGrid.SendGrid for communicating with SendGrid's API.
* **SendGrid.Helpers.Mail** dla metod pomocnika, aby łatwo utworzyć SendGridMessage obiektów, które określają sposób wysyłania wiadomości e-mail.

Dodaj następujące deklaracje obszaru nazw kodu do góry dowolnego pliku Języka C#, w którym chcesz programowo uzyskać dostęp do usługi sendgrid poczty e-mail.

    using SendGrid;
    using SendGrid.Helpers.Mail;

## <a name="how-to-create-an-email"></a>Jak: Tworzenie wiadomości e-mail
Użyj **obiektu SendGridMessage,** aby utworzyć wiadomość e-mail. Po utworzeniu obiektu wiadomości można ustawić właściwości i metody, w tym nadawcę wiadomości e-mail, adresata wiadomości e-mail oraz temat i treść wiadomości e-mail.

W poniższym przykładzie pokazano, jak utworzyć w pełni wypełniony obiekt poczty e-mail:

    var msg = new SendGridMessage();

    msg.SetFrom(new EmailAddress("dx@example.com", "SendGrid DX Team"));

    var recipients = new List<EmailAddress>
    {
        new EmailAddress("jeff@example.com", "Jeff Smith"),
        new EmailAddress("anna@example.com", "Anna Lidman"),
        new EmailAddress("peter@example.com", "Peter Saddow")
    };
    msg.AddTos(recipients);

    msg.SetSubject("Testing the SendGrid C# Library");

    msg.AddContent(MimeType.Text, "Hello World plain text!");
    msg.AddContent(MimeType.Html, "<p>Hello World!</p>");

Aby uzyskać więcej informacji na temat wszystkich właściwości i metod obsługiwanych przez **sendgrid** typu, zobacz [sendgrid-csharp][sendgrid-csharp] na GitHub.

## <a name="how-to-send-an-email"></a>Jak: Wyślij wiadomość e-mail
Po utworzeniu wiadomości e-mail można ją wysłać za pomocą interfejsu API SendGrid. Alternatywnie można użyć [. Net jest wbudowany w bibliotece][NET-library].

Wysyłanie wiadomości e-mail wymaga podania klucza interfejsu API SendGrid. Jeśli potrzebujesz szczegółowych informacji na temat konfigurowania kluczy INTERFEJSU API, odwiedź [dokumentację][documentation]kluczy API Sendgrid.

Poświadczenia te można przechowywać za pośrednictwem witryny Azure portal, klikając ustawienia aplikacji i dodając pary klucz/wartość w ustawieniach aplikacji.

 ![Ustawienia aplikacji platformy Azure][azure_app_settings]

 Następnie można uzyskać do nich dostęp w następujący sposób:

    var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
    var client = new SendGridClient(apiKey);

Poniższe przykłady pokazują, jak wysłać wiadomość e-mail przy użyciu interfejsu API sieci Web SendGrid za pomocą aplikacji konsoli.

    using System;
    using System.Threading.Tasks;
    using SendGrid;
    using SendGrid.Helpers.Mail;

    namespace Example
    {
        internal class Example
        {
            private static void Main()
            {
                Execute().Wait();
            }

            static async Task Execute()
            {
                var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
                var client = new SendGridClient(apiKey);
                var msg = new SendGridMessage()
                {
                    From = new EmailAddress("test@example.com", "DX Team"),
                    Subject = "Hello World from the SendGrid CSharp SDK!",
                    PlainTextContent = "Hello, Email!",
                    HtmlContent = "<strong>Hello, Email!</strong>"
                };
                msg.AddTo(new EmailAddress("test@example.com", "Test User"));
                var response = await client.SendEmailAsync(msg);
            }
        }
    }
    
## <a name="how-to-send-email-from-asp-net-core-api-using-mailhelper-class"></a>Jak: Wysyłanie wiadomości e-mail z interfejsu API programu ASP .NET Core przy użyciu klasy MailHelper

Poniższy przykład może służyć do wysyłania pojedynczej wiadomości e-mail do `MailHelper` wielu `SendGrid.Helpers.Mail` osób z interfejsu API programu .NET CORE ASP przy użyciu klasy obszaru nazw. W tym przykładzie używamy programu ASP .NET Core 1.0. 

W tym przykładzie klucz interfejsu API `appsettings.json` został zapisany w pliku, który można zastąpić z witryny Azure portal, jak pokazano w powyższych przykładach.

Zawartość `appsettings.json` pliku powinna wyglądać podobnie do:

    {
       "Logging": {
       "IncludeScopes": false,
       "LogLevel": {
       "Default": "Debug",
       "System": "Information",
       "Microsoft": "Information"
         }
       },
     "SENDGRID_API_KEY": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
    }

Najpierw musimy dodać poniższy kod `Startup.cs` w pliku projektu interfejsu API .NET Core. Jest to wymagane, abyśmy `SENDGRID_API_KEY` mogli `appsettings.json` uzyskać dostęp do pliku z pliku przy użyciu iniekcji zależności w kontrolerze interfejsu API. Interfejs `IConfiguration` można wstrzyknąć w konstruktorze sterownika po dodaniu go w poniższej `ConfigureServices` metodzie. Zawartość `Startup.cs` pliku wygląda następująco po dodaniu wymaganego kodu:

        public IConfigurationRoot Configuration { get; }

        public void ConfigureServices(IServiceCollection services)
        {
            // Add mvc here
            services.AddMvc();
            services.AddSingleton<IConfiguration>(Configuration);
        }

Na kontrolerze, po `IConfiguration` wstrzyknięciu interfejsu, `CreateSingleEmailToMultipleRecipients` możemy użyć `MailHelper` metody klasy, aby wysłać jedną wiadomość e-mail do wielu adresatów. Metoda akceptuje jeden dodatkowy parametr `showAllRecipients`logiczny o nazwie . Ten parametr może służyć do kontrolowania, czy adresaci wiadomości e-mail będą mogli zobaczyć siebie nawzajem adres e-mail w sekcji Do nagłówka wiadomości e-mail. Przykładowy kod sterownika powinien być podobny do 

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using SendGrid;
    using SendGrid.Helpers.Mail;
    using Microsoft.Extensions.Configuration;

    namespace SendgridMailApp.Controllers
    {
        [Route("api/[controller]")]
        public class NotificationController : Controller
        {
           private readonly IConfiguration _configuration;

           public NotificationController(IConfiguration configuration)
           {
             _configuration = configuration;
           }      
        
           [Route("SendNotification")]
           public async Task PostMessage()
           {
              var apiKey = _configuration.GetSection("SENDGRID_API_KEY").Value;
              var client = new SendGridClient(apiKey);
              var from = new EmailAddress("test1@example.com", "Example User 1");
              List<EmailAddress> tos = new List<EmailAddress>
              {
                  new EmailAddress("test2@example.com", "Example User 2"),
                  new EmailAddress("test3@example.com", "Example User 3"),
                  new EmailAddress("test4@example.com","Example User 4")
              };
            
              var subject = "Hello world email from Sendgrid ";
              var htmlContent = "<strong>Hello world with HTML content</strong>";
              var displayRecipients = false; // set this to true if you want recipients to see each others mail id 
              var msg = MailHelper.CreateSingleEmailToMultipleRecipients(from, tos, subject, "", htmlContent, false);
              var response = await client.SendEmailAsync(msg);
          }
       }
    }
    
## <a name="how-to-add-an-attachment"></a>Jak: Dodawanie załącznika
Załączniki można dodać do wiadomości, wywołując **AddAttachment** metody i minimalnie określając nazwę pliku i Base64 zakodowanej zawartości, które chcesz dołączyć. Można dołączyć wiele załączników, wywołując tę metodę raz dla każdego pliku, który chcesz dołączyć lub przy użyciu **AddAttachments** metody. Poniższy przykład pokazuje dodawanie załącznika do wiadomości:

    var banner2 = new Attachment()
    {
        Content = Convert.ToBase64String(raw_content),
        Type = "image/png",
        Filename = "banner2.png",
        Disposition = "inline",
        ContentId = "Banner 2"
    };
    msg.AddAttachment(banner2);

## <a name="how-to-use-mail-settings-to-enable-footers-tracking-and-analytics"></a>Jak: używanie ustawień poczty w celu włączenia stopek, śledzenia i analizy
SendGrid zapewnia dodatkową funkcjonalność poczty e-mail poprzez korzystanie z ustawień poczty i śledzenia ustawień. Te ustawienia można dodać do wiadomości e-mail, aby włączyć określone funkcje, takie jak śledzenie kliknięć, google analytics, śledzenie subskrypcji i tak dalej. Aby uzyskać pełną listę aplikacji, zobacz [Dokumentację ustawień][settings-documentation].

Aplikacje mogą być stosowane do **sendgrid** wiadomości e-mail przy użyciu metod zaimplementowanych jako część **SendGridMessage** klasy. Poniższe przykłady pokazują filtry śledzenia stopki i kliknięć:

Poniższe przykłady pokazują filtry śledzenia stopki i kliknięć:

### <a name="footer-settings"></a>Ustawienia stopki
    msg.SetFooterSetting(
                         true,
                         "Some Footer HTML",
                         "<strong>Some Footer Text</strong>");

### <a name="click-tracking"></a>Śledzenie kliknięć
    msg.SetClickTracking(true);

## <a name="how-to-use-additional-sendgrid-services"></a>Jak: Korzystanie z dodatkowych usług SendGrid
SendGrid oferuje kilka interfejsów API i zestawów webhook, których można użyć do wykorzystania dodatkowych funkcji w aplikacji platformy Azure. Aby uzyskać więcej informacji, zobacz [odwołanie interfejsu API SendGrid][SendGrid API documentation].

## <a name="next-steps"></a>Następne kroki
Teraz, gdy nauczyłeś się podstaw usługi SendGrid Email, skorzystaj z tych linków, aby dowiedzieć się więcej.

* SendGrid\# C biblioteki repo: [sendgrid-csharp][sendgrid-csharp]
* SendGrid API dokumentacji:<https://sendgrid.com/docs>

[Next steps]: #next-steps
[What is the SendGrid Email Service?]: #whatis
[Create a SendGrid Account]: #createaccount
[Reference the SendGrid .NET Class Library]: #reference
[How to: Create an Email]: #createemail
[How to: Send an Email]: #sendemail
[How to: Add an Attachment]: #addattachment
[How to: Use Filters to Enable Footers, Tracking, and Analytics]: #usefilters
[How to: Use Additional SendGrid Services]: #useservices

[create-new-project]: ./media/sendgrid-dotnet-how-to-send-email/new-project.png
[SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/reference.png
[sendgrid-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid-package.png
[azure_app_settings]: ./media/sendgrid-dotnet-how-to-send-email/azure-app-settings.png
[sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
[SMTP vs. Web API]: https://sendgrid.com/docs/Integrate/index.html
[App Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs/API_Reference/api_v3.html
[NET-library]: https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html#-Using-NETs-Builtin-SMTP-Library
[documentation]: https://sendgrid.com/docs/Classroom/Send/api_keys.html
[settings-documentation]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html

[chmurowa usługa poczty e-mail]: https://sendgrid.com/solutions
[transakcyjna dostawa wiadomości e-mail]: https://sendgrid.com/use-cases/transactional-email

