---
title: Zabezpieczenie interfejsów API przy użyciu klienta uwierzytelniania certyfikatów w usłudze API Management — zarządzanie interfejsami API Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak bezpieczny dostęp do interfejsów API przy użyciu certyfikatów klienta
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
ms.date: 02/01/2017
ms.author: apimpm
ms.openlocfilehash: 841825923819bdb257e5b5983071d999cca805e9
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2017
ms.locfileid: "26406747"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Jak zabezpieczyć interfejsy API za pomocą klienta uwierzytelniania certyfikatów w usłudze API Management

Zarządzanie interfejsami API oferuje możliwość bezpiecznego dostępu do interfejsów API (np. od klienta do usługi API Management) przy użyciu certyfikatów klienta. Obecnie można sprawdzić odcisk palca certyfikatu klienta na żądaną wartość. Można również sprawdzić odcisk palca z istniejących certyfikatów przekazanych do interfejsu API zarządzania.  

Aby uzyskać informacji na temat zabezpieczania dostępu do usługi zaplecza interfejsu API przy użyciu certyfikatów klienta (np. interfejsu API Management do wewnętrznej), zobacz [zabezpieczania usług zaplecza za pomocą klienta uwierzytelnianie certyfikatu](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

## <a name="checking-the-expiration-date"></a>Sprawdzanie daty wygaśnięcia

Aby sprawdzić, czy certyfikat wygasł można skonfigurować poniżej zasad:

```
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.NotAfter < DateTime.Now)" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-the-issuer-and-subject"></a>Sprawdzanie wystawcy i podmiotu

Poniżej zasad można skonfigurować w celu sprawdzenia wystawcy i podmiotu certyfikatu klienta:

```
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName != "expected-subject-name")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-the-thumbprint"></a>Sprawdzanie odcisk palca

Poniżej zasad można skonfigurować w celu Sprawdź odcisk palca certyfikatu klienta:

```
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.Thumbprint != "desired-thumbprint")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Sprawdzanie odcisk palca przed certyfikatami, przekazany do interfejsu API zarządzania

Poniższy przykład przedstawia sposób Sprawdź odcisk palca certyfikatu klienta przed przekazany do interfejsu API zarządzania certyfikatami: 

```
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

## <a name="next-step"></a>Następny krok

*  [Jak zabezpieczyć za pomocą klienta usług zaplecza uwierzytelnianie certyfikatu](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
*  [Jak przekazywać certyfikatów](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates#a-namestep1-aupload-a-client-certificate)

