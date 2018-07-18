---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/05/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9a17f34333503436d3da340670abdde154e45ef6
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38727534"
---
Od 1 lipca 2018 r. z usługi Azure VPN Gateway zostanie usunięta obsługa techniczna protokołów TLS 1.0 i 1.1. Usługa VPN Gateway będzie obsługiwać tylko protokół TLS 1.2. W celu zachowania obsługi protokołu TLS i łączności z klientami punkt-lokacja systemów Windows 7 i Windows 8, które używają protokołu TLS, zaleca się zainstalowanie następujących aktualizacji:

•   [Aktualizacja implementacji protokołu EAP firmy Microsoft, która umożliwia korzystanie z protokołu TLS](https://support.microsoft.com/help/2977292/microsoft-security-advisory-update-for-microsoft-eap-implementation-th)

•   [Aktualizacja umożliwiająca włączenie protokołów TLS 1.1 i TLS 1.2 jako domyślnych bezpiecznych protokołów w usługach WinHTTP](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-a-default-secure-protocols-in)

Następujące przestarzałe algorytmy również zostaną wycofane z protokołu TLS 1 lipca 2018 r.:

* RC4 (Rivest Cipher 4)
* DES (Data Encryption Algorithm)
* 3DES (Triple Data Encryption Algorithm)
* MD5 (Message Digest 5)
