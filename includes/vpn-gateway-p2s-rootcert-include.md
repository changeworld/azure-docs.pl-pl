---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 12/11/2018
ms.date: 12/24/2018
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: 4c8e7e5272f180c482ca7fdd44302f49eb888b25
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60320235"
---
Użyj certyfikatu głównego wygenerowanego za pomocą rozwiązania przedsiębiorstwa (zalecane) albo wygeneruj certyfikat z podpisem własnym. Po utworzeniu certyfikatu głównego wyeksportuj dane certyfikatu publicznego (nie klucz prywatny) jako plik cer X.509 z kodowaniem Base64. Następnie przekaż dane certyfikatu publicznego na serwer platformy Azure.

* **Certyfikat przedsiębiorstwa:** Jeśli korzystasz z rozwiązania dla przedsiębiorstwa, możesz użyć istniejącego łańcucha certyfikatów. Uzyskaj plik cer dla certyfikatu głównego, którego chcesz użyć.
* **Certyfikat główny z podpisem własnym:** Jeśli nie używasz rozwiązania z certyfikatem przedsiębiorstwa, utwórz certyfikat główny z podpisem własnym. W przeciwnym razie utworzone przez Ciebie certyfikaty nie będą zgodne z połączeniami punkt-lokacja i będzie wyświetlany błąd połączenia podczas próby nawiązania połączenia. Możesz użyć programu Azure PowerShell, MakeCert lub protokołu OpenSSL. W poniższych artykułach opisano sposób generowania zgodnego certyfikatu głównego z podpisem własnym:

  * [Instrukcje dotyczące programu Windows 10 PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md): W przypadku tych instrukcji do generowania certyfikatów wymagany jest system Windows 10 i program PowerShell. Certyfikaty klienta generowane na podstawie certyfikatu głównego można instalować na dowolnym obsługiwanym kliencie typu punkt-lokacja.
  * [Instrukcje dotyczące narzędzia MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): Użyj programu MakeCert, jeśli nie masz dostępu do komputera z systemem Windows 10 w celu wygenerowania certyfikatów. Mimo iż narzędzie MakeCert jest przestarzałe, przy jego użyciu można nadal generować certyfikaty. Certyfikaty klienta generowane na podstawie certyfikatu głównego można instalować na dowolnym obsługiwanym kliencie typu punkt-lokacja.
  * [Instrukcje dotyczące systemu Linux](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md)
