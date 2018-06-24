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
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "36336030"
---
Uruchamianie 1 lipca 2018 pomocy technicznej jest usuwana dla protokołu TLS 1.0, 1.1 z bramy sieci VPN platformy Azure. Brama sieci VPN będzie obsługiwał tylko protokołu TLS 1.2. Aby zachować obsługę protokołu TLS i łączności dla klientów z systemem Windows 7 i Windows 8 punkt lokacja, korzystających z protokołu TLS, zaleca się zainstalowanie następujące aktualizacje:

• [Aktualizacji dla implementacji EAP firmy Microsoft, która umożliwia korzystanie z protokołu TLS](https://support.microsoft.com/help/2977292/microsoft-security-advisory-update-for-microsoft-eap-implementation-th)

• [Aktualizację, aby umożliwić protokołu TLS 1.1 i TLS 1.2 jako domyślny bezpiecznych protokołów w WinHTTP](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-a-default-secure-protocols-in)

Następujące algorytmy starszych zostanie również przestarzałą dla protokołu TLS na 1 lipca 2018:

* RC4 (Rivest szyfrowania 4)
* DES (algorytm szyfrowania danych)
* 3DES (potrójne algorytm szyfrowania danych)
* MD5 (komunikatu Digest 5)
