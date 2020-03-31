---
title: Konfigurowanie uwierzytelniania klientów połączeń przychodzących — usługa Azure Event Grid IoT Edge | Dokumenty firmy Microsoft
description: Konfigurowanie protokołów interfejsu API udostępnianych przez siatkę zdarzeń w usłudze IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 3363db4557dd19e8d72747ccd62bb535abb7b1e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841795"
---
# <a name="configure-client-authentication-of-incoming-calls"></a>Konfigurowanie uwierzytelniania połączeń przychodzących przez klienta

W tym przewodniku przedstawiono przykłady możliwych konfiguracji uwierzytelniania klienta dla modułu siatki zdarzeń. Moduł Event Grid obsługuje dwa typy uwierzytelniania klienta:

* Oparty na kluczu podpis dostępu współdzielonego (SAS)
* Oparte na certyfikatach

Zobacz Przewodnik [zabezpieczeń i uwierzytelniania](security-authentication.md) dla wszystkich możliwych konfiguracji.

## <a name="enable-certificate-based-client-authentication-no-self-signed-certificates"></a>Włączanie uwierzytelniania klienta opartego na certyfikatach, brak certyfikatów z podpisem własnym

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=false"
  ]
}
 ```

## <a name="enable-certificate-based-client-authentication-allow-self-signed-certificates"></a>Włączanie uwierzytelniania klienta opartego na certyfikatach, zezwalanie na certyfikaty z podpisem własnym

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true"
  ]
}
```

>[!NOTE]
>Ustaw właściwość **inbound__clientAuth__clientCert__allowUnknownCA** **true** tylko w środowiskach testowych, ponieważ zazwyczaj można użyć certyfikatów z podpisem własnym. W przypadku obciążeń produkcyjnych zaleca się ustawienie tej właściwości na **fałsz** i certyfikaty z urzędu certyfikacji.For production workloads, we recommend that you set this property to false and certificates from a certificate authority (CA).

## <a name="enable-certificate-based-and-sas-key-based-client-authentication"></a>Włączanie uwierzytelniania klienta opartego na certyfikatach i kluczach sas

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=true",
    "inbound__clientAuth__sasKeys__key1=<some-secret1-here>",
    "inbound__clientAuth__sasKeys__key2=<some-secret2-here>",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Uwierzytelnianie klienta oparte na kluczu SAS umożliwia modułowi brzegowe nienawiązanym IoT do zarządzania i operacji w czasie wykonywania, przy założeniu, że porty interfejsu API są dostępne poza siecią Usługi IoT Edge.
