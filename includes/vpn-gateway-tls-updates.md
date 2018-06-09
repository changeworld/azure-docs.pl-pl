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
ms.openlocfilehash: 383c4a53913333a3e25006980dd7533b9e243a4a
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35236695"
---
Uruchamianie 1 lipca 2018 pomocy technicznej jest usuwana dla protokołu TLS 1.0, 1.1 z bramy sieci VPN platformy Azure. Brama sieci VPN będzie obsługiwał tylko protokołu TLS 1.2. Aby zachować obsługę protokołu TLS i łączności dla klientów z systemem Windows 7 i Windows 8 punkt lokacja, korzystających z protokołu TLS, zaleca się zainstalowanie następujące aktualizacje:

• [Aktualizacji dla implementacji EAP firmy Microsoft, która umożliwia korzystanie z protokołu TLS](https://support.microsoft.com/help/2977292/microsoft-security-advisory-update-for-microsoft-eap-implementation-th)

• [Aktualizację, aby umożliwić protokołu TLS 1.1 i TLS 1.2 jako domyślny bezpiecznych protokołów w WinHTTP](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-a-default-secure-protocols-in)

Następujące algorytmy starszych zostanie również przestarzałą dla protokołu TLS na 1 lipca 2018:

* RC4 (Rivest szyfrowania 4)
* DES (algorytm szyfrowania danych)
* 3DES (potrójne algorytm szyfrowania danych)
* MD5 (komunikatu Digest 5)
* Algorytm SHA-1 (skrótu Secure Hash Algorithm 1)