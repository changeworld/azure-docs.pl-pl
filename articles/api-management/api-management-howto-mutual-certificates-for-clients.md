---
title: Bezpieczne interfejsy API przy użyciu uwierzytelniania certyfikatów klienta w usłudze API Management
titleSuffix: Azure API Management
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
ms.date: 01/13/2020
ms.author: apimpm
ms.openlocfilehash: 8c1d126f01580574a83850e63945aa7e513eaeda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76713134"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Jak zabezpieczać interfejsy API przy użyciu uwierzytelniania za pomocą certyfikatów klienta w usłudze API Management

Zarządzanie interfejsami API zapewnia możliwość bezpiecznego dostępu do interfejsów API (tj. client to API Management) przy użyciu certyfikatów klientów. Za pomocą wyrażeń zasad można sprawdzać poprawność certyfikatu przychodzącego i sprawdzać właściwości certyfikatu względem żądanych wartości.

Aby uzyskać informacje dotyczące zabezpieczania dostępu do usługi zaplecza interfejsu API przy użyciu certyfikatów klientów (tj. zarządzanie interfejsami API do wewnętrznej bazy danych), zobacz [Jak zabezpieczyć usługi zaplecza przy użyciu uwierzytelniania certyfikatów klienta](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

> [!IMPORTANT]
> Aby odbierać i weryfikować certyfikaty klientów za pośrednictwem protokołu HTTP/2 w warstwach Deweloper, Podstawowy, Standardowy lub Premium, należy włączyć ustawienie "Negocjuj certyfikat klienta" w bloku "Domeny niestandardowe", jak pokazano poniżej.

![Negocjuj certyfikat klienta](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

> [!IMPORTANT]
> Aby odbierać i weryfikować certyfikaty klientów w warstwie Zużycie, należy włączyć ustawienie "Żądaj certyfikatu klienta" w bloku "Domeny niestandardowe", jak pokazano poniżej.

![Żądanie certyfikatu klienta](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

## <a name="checking-the-issuer-and-subject"></a>Sprawdzanie emitenta i tematu

Poniższe zasady można skonfigurować tak, aby sprawdzały wystawcę i temat certyfikatu klienta:

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
> Aby wyłączyć sprawdzanie listy odwołania `context.Request.Certificate.VerifyNoRevocation()` certyfikatów, `context.Request.Certificate.Verify()`użyj zamiast pliku .
> Jeśli certyfikat klienta jest podpisany samodzielnie, główne (lub pośrednie) certyfikaty urzędu `context.Request.Certificate.Verify()` `context.Request.Certificate.VerifyNoRevocation()` certyfikacji muszą zostać [przekazane](api-management-howto-ca-certificates.md) do usługi API Management w celu uzyskania i do pracy.

## <a name="checking-the-thumbprint"></a>Sprawdzanie odcisku palca

Poniższe zasady można skonfigurować tak, aby sprawdzały odcisk palca certyfikatu klienta:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Thumbprint != "DESIRED-THUMBPRINT-IN-UPPER-CASE")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Aby wyłączyć sprawdzanie listy odwołania `context.Request.Certificate.VerifyNoRevocation()` certyfikatów, `context.Request.Certificate.Verify()`użyj zamiast pliku .
> Jeśli certyfikat klienta jest podpisany samodzielnie, główne (lub pośrednie) certyfikaty urzędu `context.Request.Certificate.Verify()` `context.Request.Certificate.VerifyNoRevocation()` certyfikacji muszą zostać [przekazane](api-management-howto-ca-certificates.md) do usługi API Management w celu uzyskania i do pracy.

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Sprawdzanie odcisku palca względem certyfikatów przekazanych do usługi API Management

W poniższym przykładzie pokazano, jak sprawdzić odcisk palca certyfikatu klienta względem certyfikatów przekazanych do usługi API Management:

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
> Aby wyłączyć sprawdzanie listy odwołania `context.Request.Certificate.VerifyNoRevocation()` certyfikatów, `context.Request.Certificate.Verify()`użyj zamiast pliku .
> Jeśli certyfikat klienta jest podpisany samodzielnie, główne (lub pośrednie) certyfikaty urzędu `context.Request.Certificate.Verify()` `context.Request.Certificate.VerifyNoRevocation()` certyfikacji muszą zostać [przekazane](api-management-howto-ca-certificates.md) do usługi API Management w celu uzyskania i do pracy.

> [!TIP]
> Problem zakleszczenia certyfikatu klienta opisany w tym [artykule](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672) może objawiać `403 Forbidden` się na kilka `context.Request.Certificate` sposobów, `null`np. Ten problem zwykle `POST` `PUT` dotyczy i żądań o długości zawartości około 60 KB lub większej.
> Aby zapobiec występowaniu tego problemu, włącz ustawienie "Negocjuj certyfikat klienta" dla żądanych nazw hostów w bloku "Domeny niestandardowe", jak pokazano poniżej. Ta funkcja nie jest dostępna w warstwie Zużycie.

![Negocjuj certyfikat klienta](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

## <a name="next-steps"></a>Następne kroki

-   [Jak zabezpieczyć usługi zaplecza przy użyciu uwierzytelniania certyfikatów klienta](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
-   [Jak przesłać certyfikaty](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
