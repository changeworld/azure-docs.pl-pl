---
title: Jak używać usługi e-mail SendGrid (Node.js) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wysyłać wiadomości e-mail za pomocą usługi e-mail SendGrid na platformie Azure. Przykłady kodu napisane przy użyciu interfejsu API środowiska Node.js.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60931721"
---
# <a name="how-to-send-email-using-sendgrid-from-nodejs"></a>Jak wysyłać pocztę E-mail za pomocą usługi SendGrid z poziomu środowiska Node.js

Ten przewodnik pokazuje sposób wykonywania typowych zadań programistycznych za pomocą usługi e-mail SendGrid na platformie Azure. Przykłady są napisane przy użyciu interfejsu API środowiska Node.js. Omówione scenariusze obejmują **konstruowanie e-mail**, **wysyłania wiadomości e-mail**, **dodawania załączników**, **przy użyciu filtrów**i **aktualizowanie właściwości**. Aby uzyskać więcej informacji na temat usługi SendGrid i wysyłania wiadomości e-mail, zobacz [następne kroki](#next-steps) sekcji.

## <a name="what-is-the-sendgrid-email-service"></a>Co to jest usługa SendGrid poczty E-mail?

Usługa SendGrid jest [usługa oparta na chmurze poczty e-mail] zapewniająca niezawodne [dostarczania wiadomości e-mail dotyczącej transakcji], skalowalność i analizę w czasie rzeczywistym oraz udostępnia elastyczne interfejsy API, które umożliwiają łatwe niestandardową integrację. Typowe scenariusze użycia usługi SendGrid obejmują:

* Nie są automatycznie wysyłane potwierdzenia do klientów
* Administrowanie dystrybucji Wyświetla wysyłania klientom miesięczne e-rozsyłanych materiałach marketingowych firmowych i ofert specjalnych
* Zbieranie metryk w czasie rzeczywistym w przypadku elementów, np. blokowania wiadomości e-mail i szybkość reakcji klientów
* Generowanie raportów, aby ułatwić identyfikację trendów
* Przekazywanie zapytania klientów
* Powiadomienia e-mail z aplikacji

Aby uzyskać więcej informacji, zobacz [https://sendgrid.com](https://sendgrid.com).

## <a name="create-a-sendgrid-account"></a>Utwórz konto usługi SendGrid

[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-nodejs-module"></a>Odwołanie moduł Node.js usługi SendGrid

Moduł usługi SendGrid dla środowiska Node.js można zainstalować za pomocą narzędzia node package manager (npm) przy użyciu następującego polecenia:

```bash
npm install sendgrid
```

Po zakończeniu instalacji może wymagać modułu w aplikacji przy użyciu następującego kodu:

```javascript
var sendgrid = require('sendgrid')(sendgrid_username, sendgrid_password);
```

Moduł usługi SendGrid eksportuje **SendGrid** i **E-mail** funkcji.
**SendGrid** jest odpowiedzialny za wysyłanie wiadomości e-mail za pośrednictwem interfejsu API sieci Web, podczas gdy **E-mail** hermetyzuje wiadomości e-mail.

## <a name="how-to-create-an-email"></a>Instrukcje: Utwórz wiadomość E-mail

Tworzenie wiadomości e-mail przy użyciu modułu usługi SendGrid obejmuje, najpierw tworząc wiadomości e-mail przy użyciu funkcji poczty E-mail, a następnie wysyłając je przy użyciu funkcji usługi SendGrid. Oto przykład tworzy nowy komunikat przy użyciu funkcji wiadomości E-mail:

```javascript
var email = new sendgrid.Email({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});
```

Można również określić wiadomości w formacie HTML w przypadku klientów, które go obsługują, ustawiając właściwość html. Na przykład:

```javascript
html: This is a sample <b>HTML<b> email message.
```

Ustawianie właściwości tekst i html zawiera łagodne powrót do zawartości tekstowej w przypadku klientów, które nie obsługują wiadomości w formacie HTML.

Aby uzyskać więcej informacji na temat wszystkich właściwości obsługiwane przez funkcję poczty E-mail, zobacz [sendgrid nodejs][sendgrid-nodejs].

## <a name="how-to-send-an-email"></a>Instrukcje: Wyślij wiadomość E-mail

Po utworzeniu wiadomości e-mail przy użyciu funkcji poczty E-mail, możesz wysłać go za pomocą interfejsu API sieci Web udostępniane przez usługi SendGrid. 

### <a name="web-api"></a>Interfejs API sieci Web

```javascript
sendgrid.send(email, function(err, json){
    if(err) { return console.error(err); }
    console.log(json);
});
```

> [!NOTE]
> Podczas w powyższych przykładach przekazywanie w funkcji wywołania zwrotnego i obiektu poczty e-mail, można również bezpośrednio wywołać funkcja wysyłania, bezpośrednio określać właściwości wiadomości e-mail. Na przykład:  
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

## <a name="how-to-add-an-attachment"></a>Instrukcje: Dodaj załącznik
Załączniki można dodawać do wiadomości przez określenie nazwy pliku i ścieżki w **pliki** właściwości. W poniższym przykładzie pokazano wysyłania załącznika:

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
> Korzystając z **pliki** właściwości, plik musi być dostępny za pośrednictwem [fs.readFile](https://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile). Jeśli plik, który chcesz dołączyć jest hostowana w usłudze Azure Storage, takich jak kontener obiektów Blob, najpierw należy skopiować plik do lokalnego magazynu lub dysku z systemem Azure zanim będzie można wysłać jako załącznik przy użyciu **pliki** właściwości.
> 
> 

## <a name="how-to-use-filters-to-enable-footers-and-tracking"></a>Instrukcje: Użyj filtrów w celu śledzenia i Włącz stopki

Usługa SendGrid umożliwia funkcje dodatkowe poczty e-mail przy użyciu filtrów. Są to ustawienia, które można dodać do wiadomości e-mail, aby włączyć określonych funkcji, takich jak umożliwianie śledzenie kliknięć, usługi Google analytics, subskrypcji, śledzenia i tak dalej. Aby uzyskać pełną listę filtrów, zobacz [ustawienia filtru][Filter Settings].

Filtry można stosować do wiadomości przy użyciu **filtry** właściwości.
Każdy filtr jest określony przez skrót zawierający ustawienia specyficzne dla filtru.
Poniższe przykłady pokazują stopki i kliknij śledzenia filtrów:

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

### <a name="click-tracking"></a>Śledzenie kliknięć

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

## <a name="how-to-update-email-properties"></a>Instrukcje: Aktualizowanie właściwości wiadomości E-mail

Niektóre właściwości wiadomości e-mail może zostać zastąpiona przy użyciu **setProperty** lub dołączonych przy użyciu **addProperty**. Na przykład można dodać dodatkowych adresatów za pomocą

```javascript
email.addTo('jeff@contoso.com');
```

lub ustawić filtr przy użyciu

```javascript
email.addFilter('footer', 'enable', 1);
email.addFilter('footer', 'text/html', '<strong>boo</strong>');
```

Aby uzyskać więcej informacji, zobacz [sendgrid nodejs][sendgrid-nodejs].

## <a name="how-to-use-additional-sendgrid-services"></a>Instrukcje: Za pomocą usługi SendGrid dodatkowych usług

Usługa SendGrid oferuje oparte na sieci web interfejsów API, które można korzystać z dodatkowych funkcji usługi SendGrid z aplikacji systemu Azure. Aby uzyskać szczegółowe informacje, zobacz [dokumentacji interfejsu API usługi SendGrid][SendGrid API documentation].

## <a name="next-steps"></a>Następne kroki

Teraz, kiedy znasz już podstawy usługi E-mail SendGrid, skorzystaj z poniższych linków, aby dowiedzieć się więcej.

* Repozytorium moduł Node.js usługi SendGrid: [nodejs usługi sendgrid][sendgrid-nodejs]
* Dokumentacja interfejsu API usługi SendGrid: <https://sendgrid.com/docs>
* Usługa SendGrid specjalna oferta dla klientów platformy Azure: [http://sendgrid.com/azure.html](https://sendgrid.com/windowsazure.html)

[special offer]: https://sendgrid.com/windowsazure.html
[sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
[Filter Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs
[Usługa oparta na chmurze poczty e-mail]: https://sendgrid.com/email-solutions
[dostarczania wiadomości e-mail dotyczącej transakcji]: https://sendgrid.com/transactional-email
