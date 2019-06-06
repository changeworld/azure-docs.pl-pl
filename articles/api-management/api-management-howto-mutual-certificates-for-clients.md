---
title: Zabezpieczanie interfejsów API przy użyciu klienta uwierzytelniania certyfikatów w usłudze API Management — usłudze Azure API Management | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zabezpieczyć dostęp do interfejsów API przy użyciu certyfikatów klienta
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2019
ms.author: apimpm
ms.openlocfilehash: 5427c4050b6b70c18da7a1899d16e448c41e81c6
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427352"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Zabezpieczanie interfejsów API przy użyciu klienta uwierzytelniania certyfikatów w usłudze API Management

Usługa API Management umożliwia bezpieczny dostęp do interfejsów API (czyli od klienta do usługi API Management) przy użyciu certyfikatów klienta. Można sprawdzić poprawność certyfikatu przychodzących i sprawdzić właściwości certyfikatu przed odpowiednie wartości przy użyciu wyrażeń zasad.

Aby uzyskać informacje o zabezpieczaniu dostępu do usługi zaplecza interfejsu API przy użyciu certyfikatów klientów (czyli interfejs API zarządzania do wewnętrznej bazy danych), zobacz [sposób zabezpieczania usług zaplecza za pomocą klienta uwierzytelniania certyfikatów](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

> [!IMPORTANT]
> Do odbierania i zweryfikuj certyfikaty klienta w warstwie zużycie należy najpierw włączyć funkcję "Żądania certyfikatu klienta" Ustawienia w bloku "Domen niestandardowych", jak pokazano poniżej.

![Żądanie certyfikatu klienta](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

## <a name="checking-the-issuer-and-subject"></a>Sprawdzanie wystawcy i tematu

Poniższe zasady można skonfigurować w celu sprawdzenia wystawcy i tematu certyfikatu klienta:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName.Name != "expected-subject-name")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Aby wyłączyć sprawdzanie, czy korzystają z listy odwołania certyfikatów `context.Request.Certificate.VerifyNoRevocation()` zamiast `context.Request.Certificate.Verify()`.
> Jeśli certyfikat klienta jest certyfikatem z podpisem własnym, główny (lub pośredniego) urzędów certyfikacji musi być [przekazany](api-management-howto-ca-certificates.md) usłudze API Management dla `context.Request.Certificate.Verify()` i `context.Request.Certificate.VerifyNoRevocation()` do pracy.

## <a name="checking-the-thumbprint"></a>Sprawdzanie odcisk palca

Poniższe zasady można skonfigurować w celu Sprawdź odcisk palca certyfikatu klienta:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Thumbprint != "desired-thumbprint")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Aby wyłączyć sprawdzanie, czy korzystają z listy odwołania certyfikatów `context.Request.Certificate.VerifyNoRevocation()` zamiast `context.Request.Certificate.Verify()`.
> Jeśli certyfikat klienta jest certyfikatem z podpisem własnym, główny (lub pośredniego) urzędów certyfikacji musi być [przekazany](api-management-howto-ca-certificates.md) usłudze API Management dla `context.Request.Certificate.Verify()` i `context.Request.Certificate.VerifyNoRevocation()` do pracy.

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Sprawdzanie odcisku palca względem certyfikatów przekazanych do usługi API Management

Poniższy przykład pokazuje, jak Sprawdź odcisk palca certyfikatu klienta względem certyfikatów przekazanych do usługi API Management:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify()  || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

> [!NOTE]
> Aby wyłączyć sprawdzanie, czy korzystają z listy odwołania certyfikatów `context.Request.Certificate.VerifyNoRevocation()` zamiast `context.Request.Certificate.Verify()`.
> Jeśli certyfikat klienta jest certyfikatem z podpisem własnym, główny (lub pośredniego) urzędów certyfikacji musi być [przekazany](api-management-howto-ca-certificates.md) usłudze API Management dla `context.Request.Certificate.Verify()` i `context.Request.Certificate.VerifyNoRevocation()` do pracy.

> [!TIP]
> Zakleszczenie wystawienie certyfikatu klienta, opisano w tym [artykułu](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672) może objawiać na kilka sposobów, np. żądań zablokować, wynik żądania `403 Forbidden` kod stanu po przekroczeniem limitu czasu, `context.Request.Certificate` jest `null`. Ten problem zazwyczaj dotyczy `POST` i `PUT` żądania o długości zawartości około 60 KB lub większy.
> Aby uniknąć tego problemu występowaniu Włącz ustawienie "Negotiate certyfikatu klienta" dla żądanej nazwy hostów w bloku "Domen niestandardowych", jak pokazano poniżej. Ta funkcja nie jest dostępne w warstwie zużycia.

![Negocjuj certyfikat klienta](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

## <a name="next-steps"></a>Kolejne kroki

-   [Zabezpieczanie usług zaplecza za pomocą klienta uwierzytelniania certyfikatów](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
-   [Sposób przekazywania certyfikatów](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
