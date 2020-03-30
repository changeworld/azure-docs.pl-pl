---
title: Konfigurowanie protokołów interfejsu API — usługa Azure Event Grid IoT Edge | Dokumenty firmy Microsoft
description: Konfigurowanie protokołów interfejsu API udostępnianych przez siatkę zdarzeń w usłudze IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: ''
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 908bc941ee7379de067621e10adf5fd6ee6df559
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841814"
---
# <a name="configure-event-grid-api-protocols"></a>Konfigurowanie protokołów interfejsu API siatki zdarzeń

W tym przewodniku przedstawiono przykłady możliwych konfiguracji protokołu modułu siatki zdarzeń. Moduł siatki zdarzeń udostępnia interfejs API dla jego operacji zarządzania i środowiska uruchomieniowego. W poniższej tabeli przechwytuje protokoły i porty.

| Protocol (Protokół) | Port | Opis |
| ---------------- | ------------ | ------------ |
| HTTP | 5888 | Domyślnie wyłączony. Przydatne tylko podczas testowania. Nie nadaje się do obciążeń produkcyjnych.
| HTTPS | 4438 | Domyślne

Zobacz Przewodnik [zabezpieczeń i uwierzytelniania](security-authentication.md) dla wszystkich możliwych konfiguracji.

## <a name="expose-https-to-iot-modules-on-the-same-edge-network"></a>Udostępnianie modułów IoT w tej samej sieci brzegowej

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ]
}
 ```

## <a name="enable-https-to-other-iot-modules-and-non-iot-workloads"></a>Włączanie protokołu HTTPS do innych modułów IoT i obciążeń innych niż IoT

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
 ```

>[!NOTE]
> Sekcja **PortBindings** umożliwia mapowanie portów wewnętrznych do portów hosta kontenera. Ta funkcja umożliwia dotarcie do modułu Event Grid spoza sieci kontenerów usługi IoT Edge, jeśli urządzenie brzegowe IoT jest dostępne publicznie.

## <a name="expose-http-and-https-to-iot-modules-on-the-same-edge-network"></a>Udostępnianie modułów HTTP i HTTPS na rzecz ioT w tej samej sieci brzegowej

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=enabled",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ]
}
 ```

## <a name="enable-http-and-https-to-other-iot-modules-and-non-iot-workloads"></a>Włączanie protokołu HTTP i HTTPS w innych modułach IoT i obciążeniach innych niż IoT

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=enabled",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ],
      "5888/tcp": [
        {
          "HostPort": "5888"
        }
      ]
    }
  }
}
 ```

>[!NOTE]
> Domyślnie każdy moduł IoT jest częścią środowiska wykonawczego usługi IoT Edge utworzonego przez sieć mostka. Umożliwia różnym modułom IoT w tej samej sieci komunikowanie się ze sobą. **PortBindings** umożliwia mapowanie portu wewnętrznego kontenera na komputerze-hoście, umożliwiając w ten sposób każdemu dostęp do portu modułu Event Grid z zewnątrz.

>[!IMPORTANT]
> Podczas gdy porty mogą być dostępne poza siecią Usługi IoT Edge, uwierzytelnianie klienta wymusza, kto jest rzeczywiście dozwolone do wywołania do modułu.
