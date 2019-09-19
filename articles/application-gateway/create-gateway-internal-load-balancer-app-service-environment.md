---
title: Rozwiązywanie problemów z Application Gateway na platformie Azure — ILB ASE | Microsoft Docs
description: Dowiedz się, jak rozwiązywać problemy z bramą aplikacji przy użyciu wewnętrznego Load Balancer z App Service Environment na platformie Azure
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
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71121951"
---
# <a name="back-end-server-certificate-is-not-whitelisted-for-an-application-gateway-using-an-internal-load-balancer-with-an-app-service-environment"></a>Certyfikat serwera zaplecza nie jest listy dozwolonych dla bramy aplikacji przy użyciu wewnętrznego Load Balancer z App Service Environment

Ten artykuł rozwiązuje następujący problem: Certyfikat nie jest listy dozwolonych podczas tworzenia bramy aplikacji przy użyciu wewnętrznego Load Balancer (ILB) wraz z App Service Environment (ASE) na zapleczu w przypadku korzystania z kompleksowego protokołu SSL na platformie Azure.

## <a name="symptoms"></a>Objawy

Po utworzeniu bramy aplikacji przy użyciu ILB z środowiskiem ASE na zapleczu serwer zaplecza może stać się w złej kondycji. Ten problem występuje, jeśli certyfikat uwierzytelniania bramy aplikacji nie jest zgodny ze skonfigurowanym certyfikatem na serwerze zaplecza. Zapoznaj się z poniższym scenariuszem:

**Konfiguracja Application Gateway:**

- **Odbiornik:** Wiele lokacji
- **Port:** 443
- **Nazwa hosta:** test.appgwtestase.com
- **Certyfikat SSL:** CN=test.appgwtestase.com
- **Pula zaplecza:** Adres IP lub nazwa FQDN
- **Adres IP:** : 10.1.5.11
- **Ustawienia protokołu HTTP:** HTTPS
- **Port:** : 443
- **Sonda niestandardowa:** Nazwa hosta — test.appgwtestase.com
- **Certyfikat uwierzytelniania:** cer z test.appgwtestase.com
- **Kondycja zaplecza:** Zła kondycja — certyfikat serwera zaplecza nie jest listy dozwolonych z Application Gateway.

**Konfiguracja środowiska ASE:**

- **ILB IP:** 10.1.5.11
- **Nazwa domeny:** appgwtestase.com
- **App Service:** test.appgwtestase.com
- **Powiązanie SSL:** SNI SSL – CN = test. appgwtestase. com

Gdy uzyskujesz dostęp do bramy aplikacji, zostanie wyświetlony następujący komunikat o błędzie, ponieważ serwer zaplecza jest w złej kondycji:

**502 — serwer sieci Web odebrał nieprawidłową odpowiedź, działając jako brama lub serwer proxy.**

## <a name="solution"></a>Rozwiązanie

Jeśli nie używasz nazwy hosta w celu uzyskania dostępu do witryny sieci Web HTTPS, serwer zaplecza zwróci skonfigurowany certyfikat w domyślnej witrynie sieci Web, w przypadku gdy SNI jest wyłączone. W przypadku środowiska ILB ASE certyfikat domyślny pochodzi z certyfikatu ILB. Jeśli dla ILB nie ma skonfigurowanych certyfikatów, certyfikat pochodzi z certyfikatu aplikacji środowiska ASE.

W przypadku używania w pełni kwalifikowanej nazwy domeny (FQDN) do uzyskiwania dostępu do ILB serwer zaplecza zwróci poprawny certyfikat, który został przekazany w ustawieniach protokołu HTTP. Jeśli tak nie jest, weź pod uwagę następujące opcje:

- Użyj nazwy FQDN w puli zaplecza bramy aplikacji, aby wskazać adres IP ILB. Ta opcja działa tylko w przypadku, gdy masz prywatną strefę DNS lub skonfigurowaną niestandardową usługę DNS. W przeciwnym razie należy utworzyć rekord "A" dla publicznego systemu DNS.

- Użyj przekazanego certyfikatu ILB lub domyślnego certyfikatu (ILB Certificate) w ustawieniach protokołu HTTP. Brama aplikacji pobiera certyfikat, gdy uzyskuje dostęp do adresu IP ILB dla sondy.

- Użyj certyfikatu wieloznacznego na ILB i serwerze zaplecza, tak aby dla wszystkich witryn sieci Web był powszechny certyfikat. To rozwiązanie jest jednak możliwe tylko w przypadku poddomen, a nie jeśli każda z witryn sieci Web wymaga innych nazw hostów.

- Wyczyść opcję **Użyj dla usługi App Service** dla bramy aplikacji na wypadek użycia adresu IP ILB.

Aby zmniejszyć obciążenie, można przekazać certyfikat ILB w ustawieniach protokołu HTTP, aby umożliwić działanie ścieżki sondy. (Ten krok dotyczy tylko listy dozwolonych. Nie będzie używany do komunikacji SSL. Certyfikat ILB można pobrać, uzyskując dostęp do ILB przy użyciu jego adresu IP z przeglądarki przy użyciu protokołu HTTPS, a następnie eksportując certyfikat SSL w formacie CER z kodowaniem Base-64 i przekazując certyfikat do odpowiednich ustawień protokołu HTTP.

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną

Jeśli nadal potrzebujesz pomocy, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), aby szybko rozwiązać problem.
