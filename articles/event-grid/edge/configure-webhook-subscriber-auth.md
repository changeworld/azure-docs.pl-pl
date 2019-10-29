---
title: Konfigurowanie uwierzytelniania subskrybenta elementu webhook — Azure Event Grid IoT Edge | Microsoft Docs
description: Konfigurowanie uwierzytelniania subskrybenta elementu webhook
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 97ed1e2ad84d895e9da0d96cd070e14acb46385d
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992485"
---
# <a name="configure-webhook-subscriber-authentication"></a>Konfigurowanie uwierzytelniania subskrybenta elementu webhook

Ten przewodnik zawiera przykłady możliwych konfiguracji subskrybentów elementu webhook dla modułu Event Grid. Domyślnie tylko punkty końcowe HTTPS są akceptowane dla subskrybentów elementu webhook. Moduł Event Grid zostanie odrzucony, jeśli Subskrybenci przedstawiają certyfikat z podpisem własnym.

## <a name="allow-only-https-subscriber"></a>Zezwalaj tylko na subskrybenta HTTPS

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=false"
  ]
}
 ```

## <a name="allow-https-subscriber-with-self-signed-certificate"></a>Zezwalaj na subskrybenta HTTPS z certyfikatem z podpisem własnym

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Ustaw właściwość `outbound:webhook:allowUnknownCA` na `true` tylko w środowiskach testowych, ponieważ zazwyczaj używasz certyfikatów z podpisem własnym. W przypadku obciążeń produkcyjnych zaleca się ich ustawienie na **wartość false**.

## <a name="allow-https-subscriber-but-skip-certificate-validation"></a>Zezwalaj na subskrybenta HTTPS, ale Pomijaj weryfikację certyfikatu

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=true",
    "outbound:webhook:allowUnknownCA=false"
  ]
}
 ```

>[!NOTE]
>Ustaw właściwość `outbound:webhook:skipServerCertValidation` tak, aby `true` tylko w środowiskach testowych, ponieważ użytkownik nie będzie przedprezentować certyfikatu, który musi zostać uwierzytelniony. W przypadku obciążeń produkcyjnych zaleca się ich ustawienie na **wartość FAŁSZ** .

## <a name="allow-both-http-and-https-with-self-signed-certificates"></a>Zezwalaj na protokoły HTTP i HTTPS z certyfikatami z podpisem własnym

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=false",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Ustaw właściwość `outbound:webhook:httpsOnly` na `false` tylko w środowiskach testowych, ponieważ warto najpierw utworzyć subskrybenta protokołu HTTP. W przypadku obciążeń produkcyjnych zaleca się ich ustawienie na **wartość true**
