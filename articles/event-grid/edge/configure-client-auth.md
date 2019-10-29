---
title: Konfigurowanie uwierzytelniania klientów wywołań przychodzących — Azure Event Grid IoT Edge | Microsoft Docs
description: Skonfiguruj protokoły interfejsu API udostępniane przez Event Grid w IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: b5456130e89bf77e2c2ba41880323e38f6b27f4c
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992511"
---
# <a name="configure-client-authentication-of-incoming-calls"></a>Konfigurowanie uwierzytelniania klienta dla wywołań przychodzących

Ten przewodnik zawiera przykłady możliwych konfiguracji uwierzytelniania klienta dla modułu Event Grid. Moduł Event Grid obsługuje dwa typy uwierzytelniania klienta:-

* Oparty na kluczu sygnatury dostępu współdzielonego (SAS)
* oparte na certyfikatach

Wszystkie możliwe konfiguracje można znaleźć w przewodniku dotyczącym [zabezpieczeń i uwierzytelniania](security-authentication.md) .

## <a name="enable-certificate-based-client-authentication-no-self-signed-certificates"></a>Włączanie uwierzytelniania klientów opartych na certyfikatach, bez certyfikatów z podpisem własnym

```json
 {
  "Env": [
    "inbound:clientAuth:sasKeys:enabled=false",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=false"
  ]
}
 ```

## <a name="enable-certificate-based-client-authentication-allow-self-signed-certificates"></a>Włączanie uwierzytelniania klientów opartych na certyfikatach, Zezwalaj na certyfikaty z podpisem własnym

```json
 {
  "Env": [
    "inbound:clientAuth:sasKeys:enabled=false",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=true"
  ]
}
```

>[!NOTE]
>Ustaw właściwość **przychodzące: clientAuth: clientCert: allowUnknownCA** na **true** tylko w środowiskach testowych, ponieważ zazwyczaj używasz certyfikatów z podpisem własnym. W przypadku obciążeń produkcyjnych zaleca się ustawienie dla tej właściwości **wartości false** i Certificates urzędu certyfikacji.

## <a name="enable-certificate-based-and-sas-key-based-client-authentication"></a>Włączanie uwierzytelniania klientów opartych na certyfikatach i sygnaturach dostępu współdzielonego

```json
 {
  "Env": [
    "inbound:clientAuth:sasKeys:enabled=true",
    "inbound:clientAuth:sasKeys:key1=<some-secret1-here>",
    "inbound:clientAuth:sasKeys:key2=<some-secret2-here>",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Uwierzytelnianie klienta oparte na kluczach SAS umożliwia modułowi nieiot Edge zarządzanie i wykonywanie operacji w czasie wykonywania przy założeniu, że porty interfejsu API są dostępne poza siecią IoT Edge.
