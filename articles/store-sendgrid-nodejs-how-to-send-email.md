---
title: Jak korzystać z usługi sendgrid poczty e-mail (Node.js) | Dokumenty firmy Microsoft
description: Dowiedz się, jak wysyłać wiadomości e-mail za pomocą usługi sendgrid na platformie Azure. Przykłady kodu napisane przy użyciu interfejsu API node.js.
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
ms.openlocfilehash: f2d653441598a47986913d525057672eed24b435
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60931721"
---
# <a name="how-to-send-email-using-sendgrid-from-nodejs"></a>Jak wysyłać wiadomości e-mail za pomocą SendGrid z Node.js

W tym przewodniku pokazano, jak wykonywać typowe zadania programowania za pomocą usługi sendgrid poczty e-mail na platformie Azure. Przykłady są zapisywane przy użyciu interfejsu API node.js. Scenariusze obejmują **konstruowanie wiadomości e-mail,** **wysyłanie wiadomości e-mail,** **dodawanie załączników,** **używanie filtrów**i **aktualizowanie właściwości.** Aby uzyskać więcej informacji na temat SendGrid i wysyłania wiadomości e-mail, zobacz [następne kroki](#next-steps) sekcji.

## <a name="what-is-the-sendgrid-email-service"></a>Co to jest usługa poczty e-mail SendGrid?

SendGrid to [chmurowa usługa poczty e-mail,] która zapewnia niezawodne [transakcyjne dostarczanie wiadomości e-mail,]skalowalność i analizę w czasie rzeczywistym wraz z elastycznymi interfejsami API, które ułatwiają niestandardową integrację. Typowe scenariusze użycia SendGrid obejmują:

* Automatyczne wysyłanie potwierdzeń do odbiorców
* Administrowanie listami dystrybucyjnymi do wysyłania klientom miesięcznych e-ulotek i ofert specjalnych
* Zbieranie danych w czasie rzeczywistym dla takich rzeczy, jak zablokowana poczta e-mail i responsywność klienta
* Generowanie raportów ułatwiających identyfikację trendów
* Przekazywanie zapytań klientów
* Powiadomienia e-mail z aplikacji

Aby uzyskać więcej [https://sendgrid.com](https://sendgrid.com)informacji, zobacz .

## <a name="create-a-sendgrid-account"></a>Tworzenie konta SendGrid

[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-nodejs-module"></a>Odwoł się do modułu SendGrid Node.js

Moduł SendGrid dla node.js można zainstalować za pomocą menedżera pakietów węzłów (npm) za pomocą następującego polecenia:

```bash
npm install sendgrid
```

Po instalacji można wymagać modułu w aplikacji przy użyciu następującego kodu:

```javascript
var sendgrid = require('sendgrid')(sendgrid_username, sendgrid_password);
```

Moduł SendGrid eksportuje funkcje **SendGrid** i **Email.**
**SendGrid** jest odpowiedzialny za wysyłanie wiadomości e-mail za pośrednictwem interfejsu API sieci Web, podczas gdy **poczta e-mail** hermetyzuje wiadomość e-mail.

## <a name="how-to-create-an-email"></a>Jak: Tworzenie wiadomości e-mail

Tworzenie wiadomości e-mail przy użyciu modułu SendGrid polega najpierw na utworzeniu wiadomości e-mail za pomocą funkcji Poczta e-mail, a następnie wysłaniu jej za pomocą funkcji SendGrid. Poniżej przedstawiono przykład tworzenia nowej wiadomości przy użyciu funkcji Poczta e-mail:

```javascript
var email = new sendgrid.Email({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});
```

Można również określić komunikat HTML dla klientów, którzy go obsługują, ustawiając właściwość html. Przykład:

```javascript
html: This is a sample <b>HTML<b> email message.
```

Ustawienie właściwości tekstu i html zapewnia pełne wdzięku powrót do zawartości tekstowej dla klientów, którzy nie mogą obsługiwać wiadomości HTML.

Aby uzyskać więcej informacji na temat wszystkich właściwości obsługiwanych przez funkcję Poczta e-mail, zobacz [sendgrid-nodejs][sendgrid-nodejs].

## <a name="how-to-send-an-email"></a>Jak: Wyślij wiadomość e-mail

Po utworzeniu wiadomości e-mail za pomocą funkcji Poczta e-mail można ją wysłać za pomocą interfejsu API sieci Web dostarczonego przez SendGrid. 

### <a name="web-api"></a>Interfejs API sieci Web

```javascript
sendgrid.send(email, function(err, json){
    if(err) { return console.error(err); }
    console.log(json);
});
```

> [!NOTE]
> Podczas gdy powyższe przykłady pokazują przekazywanie w obiekcie wiadomości e-mail i funkcji wywołania zwrotnego, można również bezpośrednio wywołać funkcję wysyłania, określając bezpośrednio właściwości wiadomości e-mail. Przykład:  
> 
> ```javascript
> sendgrid.send({
> to: 'john@contoso.com',
> from: 'anna@contoso.com',
> subject: 'test mail',
> text: 'This is a sample email message.'
> });
> ```
>

## <a name="how-to-add-an-attachment"></a>Jak: Dodawanie załącznika
Załączniki można dodać do wiadomości, określając nazwy plików i ścieżki we właściwości **plików.** Poniższy przykład pokazuje wysyłanie załącznika:

```javascript
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
```

> [!NOTE]
> Podczas korzystania z właściwości **files** plik musi być dostępny za pośrednictwem [pliku fs.readFile](https://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile). Jeśli plik, który chcesz dołączyć, jest hostowany w usłudze Azure Storage, na przykład w kontenerze obiektów Blob, należy najpierw skopiować plik do magazynu lokalnego lub na dysk platformy Azure, zanim będzie można go wysłać jako załącznik przy użyciu właściwości **plików.**
> 
> 

## <a name="how-to-use-filters-to-enable-footers-and-tracking"></a>Jak: Użyj filtrów, aby włączyć stopki i śledzenie

SendGrid zapewnia dodatkowe funkcje poczty e-mail za pomocą filtrów. Są to ustawienia, które można dodać do wiadomości e-mail, aby włączyć określone funkcje, takie jak włączanie śledzenia kliknięć, google analytics, śledzenie subskrypcji i tak dalej. Aby uzyskać pełną listę filtrów, zobacz [Ustawienia filtrów][Filter Settings].

Filtry można zastosować do wiadomości przy użyciu **właściwości filters.**
Każdy filtr jest określony przez skrót zawierający ustawienia specyficzne dla filtra.
Poniższe przykłady pokazują filtry śledzenia stopki i kliknięć:

### <a name="footer"></a>Stopka

```javascript
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
```

### <a name="click-tracking"></a>Kliknij pozycję Śledzenie

```javascript
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
```

## <a name="how-to-update-email-properties"></a>Jak: Aktualizowanie właściwości wiadomości e-mail

Niektóre właściwości wiadomości e-mail można nadpisywały za pomocą **setProperty** lub dołączane za pomocą **addProperty**. Można na przykład dodać dodatkowych adresatów za pomocą

```javascript
email.addTo('jeff@contoso.com');
```

lub ustaw filtr za pomocą

```javascript
email.addFilter('footer', 'enable', 1);
email.addFilter('footer', 'text/html', '<strong>boo</strong>');
```

Aby uzyskać więcej informacji, zobacz [sendgrid-nodejs][sendgrid-nodejs].

## <a name="how-to-use-additional-sendgrid-services"></a>Jak: Korzystanie z dodatkowych usług SendGrid

SendGrid oferuje interfejsy API oparte na sieci Web, których można użyć do wykorzystania dodatkowych funkcji SendGrid z aplikacji platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [dokumentację interfejsu API SendGrid][SendGrid API documentation].

## <a name="next-steps"></a>Następne kroki

Teraz, gdy nauczyłeś się podstaw usługi SendGrid Email, skorzystaj z tych linków, aby dowiedzieć się więcej.

* Repozytorium modułu SendGrid Node.js: [sendgrid-nodejs][sendgrid-nodejs]
* SendGrid API dokumentacji:<https://sendgrid.com/docs>
* Oferta specjalna SendGrid dla klientów platformy Azure:[http://sendgrid.com/azure.html](https://sendgrid.com/windowsazure.html)

[special offer]: https://sendgrid.com/windowsazure.html
[sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
[Filter Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs
[chmurowa usługa poczty e-mail]: https://sendgrid.com/email-solutions
[transakcyjna dostawa wiadomości e-mail]: https://sendgrid.com/transactional-email
