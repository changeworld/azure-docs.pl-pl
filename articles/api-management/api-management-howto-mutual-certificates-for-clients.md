---
title: Zabezpieczanie interfejsów API przy użyciu uwierzytelniania certyfikatów klientów w API Management API Management platformy Azure | Microsoft Docs
description: Dowiedz się, jak zabezpieczyć dostęp do interfejsów API przy użyciu certyfikatów klienta
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/30/2019
ms.author: apimpm
ms.openlocfilehash: 263f8495b9dbb0a1c5b3c54301b4b4deab425e31
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072360"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Jak zabezpieczyć interfejsy API przy użyciu uwierzytelniania certyfikatów klientów w API Management

API Management zapewnia możliwość zabezpieczenia dostępu do interfejsów API (np. klient API Management) przy użyciu certyfikatów klienta. Można sprawdzić poprawność certyfikatu przychodzącego i sprawdzić właściwości certyfikatu przed żądanymi wartościami przy użyciu wyrażeń zasad.

Aby uzyskać informacje o zabezpieczaniu dostępu do usługi zaplecza interfejsu API przy użyciu certyfikatów klienta (tj. API Management zaplecza), zobacz [jak zabezpieczyć usługi zaplecza przy użyciu uwierzytelniania certyfikatu klienta](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates) .

> [!IMPORTANT]
> Aby otrzymywać i weryfikować certyfikaty klienta w warstwie zużycia, należy najpierw włączyć ustawienie "Żądaj certyfikatu klienta" w bloku "domeny niestandardowe", jak pokazano poniżej.

![Żądaj certyfikatu klienta](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

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
> Aby wyłączyć sprawdzanie użycia `context.Request.Certificate.VerifyNoRevocation()` listy odwołania certyfikatów `context.Request.Certificate.Verify()`zamiast.
> Jeśli certyfikat klienta jest podpisany z podpisem własnym, certyfikaty urzędu certyfikacji głównego (lub pośredniego) muszą zostać [przekazane](api-management-howto-ca-certificates.md) do `context.Request.Certificate.Verify()` API Management `context.Request.Certificate.VerifyNoRevocation()` programu i do pracy.

## <a name="checking-the-thumbprint"></a>Sprawdzanie odcisku palca

Poniższe zasady można skonfigurować w celu sprawdzenia odcisku palca certyfikatu klienta:

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
> Aby wyłączyć sprawdzanie użycia `context.Request.Certificate.VerifyNoRevocation()` listy odwołania certyfikatów `context.Request.Certificate.Verify()`zamiast.
> Jeśli certyfikat klienta jest podpisany z podpisem własnym, certyfikaty urzędu certyfikacji głównego (lub pośredniego) muszą zostać [przekazane](api-management-howto-ca-certificates.md) do `context.Request.Certificate.Verify()` API Management `context.Request.Certificate.VerifyNoRevocation()` programu i do pracy.

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Sprawdzanie odcisku palca w odniesieniu do certyfikatów przekazanych do API Management

Poniższy przykład pokazuje, jak sprawdzić odcisk palca certyfikatu klienta względem certyfikatów przekazanych do API Management:

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
> Aby wyłączyć sprawdzanie użycia `context.Request.Certificate.VerifyNoRevocation()` listy odwołania certyfikatów `context.Request.Certificate.Verify()`zamiast.
> Jeśli certyfikat klienta jest podpisany z podpisem własnym, certyfikaty urzędu certyfikacji głównego (lub pośredniego) muszą zostać [przekazane](api-management-howto-ca-certificates.md) do `context.Request.Certificate.Verify()` API Management `context.Request.Certificate.VerifyNoRevocation()` programu i do pracy.

> [!TIP]
> Problem zakleszczenia certyfikatu klienta opisany w [tym artykule](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672) może być manifestem na kilka sposobów, np. żądania zawieszania `403 Forbidden` , żądania powodują powstanie kodu stanu `context.Request.Certificate` po `null`upływie limitu czasu. Ten problem ma zwykle `POST` wpływ `PUT` na żądania z zawartością o długości około 60KB lub większą.
> Aby zapobiec wystąpieniu tego problemu, Włącz ustawienie "Negocjuj certyfikat klienta" dla żądanych nazw hostów w bloku "domeny niestandardowe", jak pokazano poniżej. Ta funkcja nie jest dostępna w warstwie zużycia.

![Negocjuj certyfikat klienta](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

## <a name="next-steps"></a>Następne kroki

-   [Jak zabezpieczyć usługi zaplecza przy użyciu uwierzytelniania certyfikatu klienta](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
-   [Jak przekazać certyfikaty](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
