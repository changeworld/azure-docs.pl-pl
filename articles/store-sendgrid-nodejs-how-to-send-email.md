---
title: Jak używać usługi poczty e-mail SendGrid (Node.js) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wysłać wiadomość e-mail z usługi poczty e-mail SendGrid na platformie Azure. Przykłady kodu napisane przy użyciu interfejsu API środowiska Node.js.
services: ''
documentationcenter: nodejs
author: erikre
manager: wpickett
editor: ''
ms.assetid: cac444b4-26b0-45ea-9c3d-eca28d57dacb
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 01/05/2016
ms.author: erikre
ms.openlocfilehash: 327cea3a24cc47a9cc463b37cc2346ebc475ef7f
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "23873989"
---
# <a name="how-to-send-email-using-sendgrid-from-nodejs"></a>Sposób wysyłania poczty E-mail przy użyciu SendGrid w oprogramowaniu Node.js
W tym przewodniku przedstawiono sposób wykonywania typowych zadań programowania usługi poczty e-mail SendGrid na platformie Azure. Przykłady są napisane przy użyciu interfejsu API środowiska Node.js. Omówione scenariusze obejmują **konstruowania e-mail**, **wysyłania wiadomości e-mail**, **dodawanie załączników**, **za pomocą filtrów**i **aktualizowanie właściwości**. Aby uzyskać więcej informacji na SendGrid i wysyłania wiadomości e-mail, zobacz [następne kroki](#next-steps) sekcji.

## <a name="what-is-the-sendgrid-email-service"></a>Co to jest usługa SendGrid poczty E-mail?
Jest SendGrid [Usługa poczty e-mail hostowanego w chmurze] zapewnia niezawodne [dostarczania transakcyjnych wiadomości e-mail], skalowalności i analiz w czasie rzeczywistym oraz elastyczne interfejsów API, które umożliwiają łatwe niestandardowej integracji. Typowe scenariusze użycia SendGrid obejmują:

* Potwierdzenia są automatycznie wysyłane do klientów
* Administrowanie dystrybucji wymieniono wysyłania klientów miesięczne e ulotki i oferty specjalne
* Zbieranie metryk w czasie rzeczywistym dla zablokowanych poczty e-mail i czasu odpowiedzi klienta
* Generowanie raportów, aby ułatwić identyfikację trendów
* Zapytania dotyczące przekazywania klienta
* Powiadomienia e-mail z aplikacji

Aby uzyskać więcej informacji, zobacz [https://sendgrid.com](https://sendgrid.com).

## <a name="create-a-sendgrid-account"></a>Utwórz konto SendGrid
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-nodejs-module"></a>Odwołanie do modułu Node.js SendGrid
Moduł SendGrid dla środowiska Node.js może być instalowane za pośrednictwem węzeł Menedżera pakietów (npm) przy użyciu następującego polecenia:

    npm install sendgrid

Po zakończeniu instalacji można wymagać modułu w aplikacji przy użyciu następującego kodu:

    var sendgrid = require('sendgrid')(sendgrid_username, sendgrid_password);

Eksportuje modułu SendGrid **SendGrid** i **E-mail** funkcji.
**SendGrid** jest odpowiedzialny za wysyłanie poczty e-mail za pośrednictwem interfejsu API sieci Web, podczas gdy **E-mail** hermetyzuje wiadomości e-mail.

## <a name="how-to-create-an-email"></a>Porady: Tworzenie wiadomości E-mail
Tworzenie wiadomości e-mail przy użyciu modułu SendGrid obejmuje najpierw Tworzenie wiadomości e-mail przy użyciu funkcji poczty E-mail, a następnie wysyła je przy użyciu funkcji SendGrid. Poniżej przedstawiono przykład tworzenia nowej wiadomości przy użyciu funkcji poczty E-mail:

    var email = new sendgrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });

Można również określić wiadomości w formacie HTML dla klientów obsługujących go przez ustawienie właściwości html. Na przykład:

    html: This is a sample <b>HTML<b> email message.

Ustawianie właściwości tekst i html zapewnia płynne powrotu do zawartości tekstowej dla klientów, które nie obsługują wiadomości w formacie HTML.

Aby uzyskać więcej informacji na wszystkie właściwości obsługiwane przez funkcję poczty E-mail, zobacz [sendgrid nodejs][sendgrid-nodejs].

## <a name="how-to-send-an-email"></a>Porady: wysyłanie wiadomości E-mail
Po utworzeniu wiadomości e-mail przy użyciu funkcji poczty E-mail, możesz wysłać go za pomocą interfejsu API sieci Web udostępniane przez włączenie. 

### <a name="web-api"></a>Interfejs API sieci Web
    sendgrid.send(email, function(err, json){
        if(err) { return console.error(err); }
        console.log(json);
    });

> [!NOTE]
> Gdy w powyższych przykładach pokazano przekazywanie w funkcji i wywołanie zwrotne obiektu poczty e-mail, można również bezpośrednio wywołanie funkcji wysyłania bezpośrednio, określając właściwości wiadomości e-mail. Na przykład:  
> 
> `````
> sendgrid.send({
> to: 'john@contoso.com',
> from: 'anna@contoso.com',
> subject: 'test mail',
> text: 'This is a sample email message.'
> });
> `````
> 
> 

## <a name="how-to-add-an-attachment"></a>Porady: Dodawanie załącznika
Można dodawać załączników do wiadomości przez określenie nazwy pliku i ścieżki w **pliki** właściwości. W poniższym przykładzie pokazano, wysyłania załącznika:

    sendgrid.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.',
        files: [
            {
                filename:     '',           // required only if file.content is used.
                contentType:  '',           // optional
                cid:          '',           // optional, used to specify cid for inline content
                path:         '',           //
                url:          '',           // == One of these three options is required
                content:      ('' | Buffer) //
            }
        ],
    });

> [!NOTE]
> Korzystając z **pliki** właściwości, plik musi być dostępny za pośrednictwem [fs.readFile](http://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile). Jeśli chcesz dołączyć plik znajduje się w usłudze Azure Storage, taki jak kontenera obiektów Blob, należy najpierw skopiować plik do lokalnej pamięci masowej lub dysku platformy Azure przed mógł zostać wysłany jako załącznik przy użyciu **pliki** właściwości.
> 
> 

## <a name="how-to-use-filters-to-enable-footers-and-tracking"></a>Porady: należy używać filtrów do śledzenia i Włącz stopek
SendGrid udostępnia funkcje dodatkowe poczty e-mail przy użyciu filtrów. Są to ustawienia, które mogą zostać dodane do wiadomości e-mail, aby włączyć określonych funkcji, takich jak włączenie kliknij śledzenia, Google analytics, subskrypcji, śledzenia i tak dalej. Aby uzyskać pełną listę filtrów, zobacz [ustawienia filtru][Filter Settings].

Filtry można stosować do wiadomości, przy użyciu **filtry** właściwości.
Każdy filtr jest określany przez skrót zawierający ustawienia specyficzne dla filtru.
Poniższe przykłady pokazują, stopka i kliknij przycisk śledzenia filtrów:

### <a name="footer"></a>Stopka
    var email = new sendgrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });

    email.setFilters({
        'footer': {
            'settings': {
                'enable': 1,
                'text/plain': 'This is a text footer.'
            }
        }
    });

    sendgrid.send(email);

### <a name="click-tracking"></a>Kliknij przycisk śledzenia
    var email = new sendgrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });

    email.setFilters({
        'clicktrack': {
            'settings': {
                'enable': 1
            }
        }
    });

    sendgrid.send(email);

## <a name="how-to-update-email-properties"></a>Porady: aktualizowanie właściwości wiadomości E-mail
Niektóre właściwości wiadomości e-mail może zostać zastąpiona przy użyciu **ustawić * właściwość*** lub dołączonych za pomocą **dodać*właściwości ***. Na przykład można dodać dodatkowych adresatów za pomocą

    email.addTo('jeff@contoso.com');

lub ustawić filtr przy użyciu

    email.addFilter('footer', 'enable', 1);
    email.addFilter('footer', 'text/html', '<strong>boo</strong>');

Aby uzyskać więcej informacji, zobacz [sendgrid nodejs][sendgrid-nodejs].

## <a name="how-to-use-additional-sendgrid-services"></a>Porady: Użyj SendGrid dodatkowych usług
SendGrid oferuje opartych na sieci web interfejsów API, które można wykorzystać dodatkowe funkcje włączenie w aplikacji platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [dokumentacji interfejsu API SendGrid][SendGrid API documentation].

## <a name="next-steps"></a>Następne kroki
Teraz, kiedy znasz już podstawy usługi poczty E-mail SendGrid, skorzystaj z poniższych linków, aby dowiedzieć się więcej.

* Repozytorium modułu SendGrid Node.js: [sendgrid nodejs][sendgrid-nodejs]
* Dokumentacja interfejsu API SendGrid: <https://sendgrid.com/docs>
* Oferta specjalna SendGrid dla klientów platformy Azure: [http://sendgrid.com/azure.html](https://sendgrid.com/windowsazure.html)

[special offer]: https://sendgrid.com/windowsazure.html
[sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
[Filter Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs
[Usługa poczty e-mail hostowanego w chmurze]: https://sendgrid.com/email-solutions
[dostarczania transakcyjnych wiadomości e-mail]: https://sendgrid.com/transactional-email
