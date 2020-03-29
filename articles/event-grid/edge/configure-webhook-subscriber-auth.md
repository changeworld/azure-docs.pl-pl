---
title: Konfigurowanie uwierzytelniania subskrybentów elementu webhook — usługa Azure Event Grid IoT Edge | Dokumenty firmy Microsoft
description: Konfigurowanie uwierzytelniania subskrybenta elementu webhook
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 101dcae5870322878cec48098f2efae32cc68c14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841734"
---
# <a name="configure-webhook-subscriber-authentication"></a>Konfigurowanie uwierzytelniania subskrybenta elementu webhook

W tym przewodniku przedstawiono przykłady możliwych konfiguracji subskrybentów elementu webhook dla modułu siatki zdarzeń. Domyślnie tylko punkty końcowe HTTPS są akceptowane dla subskrybentów elementu webhook. Moduł siatki zdarzeń zostanie odrzucony, jeśli subskrybent przedstawi certyfikat z podpisem własnym.

## <a name="allow-only-https-subscriber"></a>Zezwalaj tylko subskrybentowi HTTPS

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=false"
  ]
}
 ```

## <a name="allow-https-subscriber-with-self-signed-certificate"></a>Zezwalaj subskrybentowi HTTPS z certyfikatem z podpisem własnym

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Ustaw `outbound__webhook__allowUnknownCA` właściwość `true` tylko w środowiskach testowych, jak zazwyczaj można użyć certyfikatów z podpisem własnym. W przypadku obciążeń produkcyjnych zaleca się ustawienie ich na **false**.

## <a name="allow-https-subscriber-but-skip-certificate-validation"></a>Zezwalaj subskrybentowi HTTPS na pomijanie sprawdzania poprawności certyfikatu

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=true",
    "outbound__webhook__allowUnknownCA=false"
  ]
}
 ```

>[!NOTE]
>Ustaw `outbound__webhook__skipServerCertValidation` właściwość `true` tylko w środowiskach testowych, ponieważ może nie być przedstawienie certyfikatu, który musi być uwierzytelniony. W przypadku obciążeń produkcyjnych zaleca się ustawienie ich **na**

## <a name="allow-both-http-and-https-with-self-signed-certificates"></a>Zezwalaj na zarówno protokół HTTP, jak i protokół HTTPS z certyfikatami z podpisem własnym

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=false",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Ustaw `outbound__webhook__httpsOnly` właściwość `false` tylko w środowiskach testowych, jak można wywołać subskrybenta HTTP pierwszy. W przypadku obciążeń produkcyjnych zalecamy ustawienie ich **na prawdziwe**
