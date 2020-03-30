---
title: Rozwiązywanie problemów z bramą aplikacji na platformie Azure — ILB ASE | Dokumenty firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z bramą aplikacji przy użyciu wewnętrznego modułu równoważenia obciążenia ze środowiskiem usługi app service na platformie Azure
services: vpn-gateway
documentationCenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2018
ms.author: genli
ms.openlocfilehash: 9c3216af283ebd9d84a5469d4d50d18c19f67534
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71121951"
---
# <a name="back-end-server-certificate-is-not-whitelisted-for-an-application-gateway-using-an-internal-load-balancer-with-an-app-service-environment"></a>Certyfikat serwera zaplecza nie jest umieszczony na białej liście bramy aplikacji przy użyciu wewnętrznego modułu równoważenia obciążenia ze środowiskiem usługi aplikacji

W tym artykule rozwiązano następujące problemy: Certyfikat nie jest umieszczany na białej liście podczas tworzenia bramy aplikacji przy użyciu wewnętrznego modułu równoważenia obciążenia (ILB) wraz ze środowiskiem usługi aplikacji (ASE) na zapleczu podczas korzystania z kompleksowego ssl na platformie Azure.

## <a name="symptoms"></a>Objawy

Podczas tworzenia bramy aplikacji przy użyciu równoważenia obciążenia sieciowego z ase na zapleczu, serwer zaplecza może stać się w złej kondycji. Ten problem występuje, jeśli certyfikat uwierzytelniania bramy aplikacji nie jest zgodny ze skonfigurowanym certyfikatem na serwerze zaplecza. Zobacz następujący scenariusz jako przykład:

**Konfiguracja bramy aplikacji:**

- **Słuchacz:** Wielozadaniowe
- **Port:** 443
- **Nazwa hosta:** test.appgwtestase.com
- **Certyfikat SSL:** CN = test.appgwtestase.com
- **Pula zaplecza:** Adres IP lub FQDN
- **Adres IP:**: 10.1.5.11
- **Ustawienia HTTP:** Protokół HTTPS
- **Port:**: 443
- **Sonda niestandardowa:** Nazwa hosta – test.appgwtestase.com
- **Certyfikat uwierzytelniania:** .cer test.appgwtestase.com
- **Kondycja zaplecza:** W złej kondycji — certyfikat serwera wewnętrznej bazy danych nie jest umieszczony na białej liście z bramą aplikacji.

**Konfiguracja ASE:**

- **Ip równoważenia obciążenia:** 10.1.5.11
- **Nazwa domeny:** appgwtestase.com
- **Usługa aplikacji:** test.appgwtestase.com
- **Powiązanie SSL:** SNI SSL – CN=test.appgwtestase.com

Podczas uzyskiwania dostępu do bramy aplikacji pojawia się następujący komunikat o błędzie, ponieważ serwer zaplecza jest w złej kondycji:

**502 — serwer sieci Web otrzymał nieprawidłową odpowiedź, działając jako brama lub serwer proxy.**

## <a name="solution"></a>Rozwiązanie

Jeśli nie używasz nazwy hosta do uzyskiwania dostępu do witryny HTTPS, serwer zaplecza zwróci skonfigurowany certyfikat w domyślnej witrynie sieci Web, w przypadku wyłączenia sieci SNI. W przypadku ase równoważenia obciążenia sieciowego certyfikat domyślny pochodzi z certyfikatu równoważenia obciążenia. Jeśli nie ma skonfigurowanych certyfikatów dla równoważenia obciążenia sieciowego, certyfikat pochodzi z certyfikatu aplikacji ASE.

Jeśli używasz w pełni kwalifikowanej nazwy domeny (FQDN) do uzyskiwania dostępu do równoważenia obciążenia sieciowego, serwer zaplecza zwróci poprawny certyfikat, który jest przekazywany w ustawieniach HTTP. Jeśli tak nie jest, rozważ następujące opcje:

- Użyj funkcji FQDN w puli zaplecza bramy aplikacji, aby wskazać adres IP modułu równoważenia obciążenia sieciowego. Ta opcja działa tylko wtedy, gdy skonfigurowano prywatną strefę DNS lub niestandardowy system DNS. W przeciwnym razie należy utworzyć rekord "A" dla publicznego dns.

- Użyj przekazanego certyfikatu na równoważeniu obciążenia obciążenia sieciowego lub certyfikatu domyślnego (certyfikatu równoważenia obciążenia), w ustawieniach HTTP. Brama aplikacji pobiera certyfikat, gdy uzyskuje dostęp do adresu IP równoważenia obciążenia dla sondy.

- Użyj certyfikatu symboli wieloznacznych na przywt. Jednak to rozwiązanie jest możliwe tylko w przypadku subdomen, a nie wtedy, gdy każda ze stron internetowych wymaga różnych hostów.

- Wyczyść opcję **Użyj dla usługi aplikacji** dla bramy aplikacji w przypadku, gdy używasz adresu IP równoważenia obciążenia sieciowego.

Aby zmniejszyć obciążenie, można przekazać certyfikat równoważenia obciążenia w ustawieniach HTTP, aby ścieżka sondy działała. (Ten krok jest tylko dla białej listy. Nie będzie używany do komunikacji SSL.) Certyfikat Wewnętrznego można pobrać, uzyskując dostęp do ilb z jego adresem IP z przeglądarki na https, a następnie eksportując certyfikat SSL w formacie CER zakodowanym base-64 i przesyłając certyfikat w odpowiednich ustawieniach HTTP.

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli nadal potrzebujesz pomocy, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), aby szybko rozwiązać problem.
