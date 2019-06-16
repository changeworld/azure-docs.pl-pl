---
title: Rozwiązywanie problemów z bramy aplikacji na platformie Azure — środowisko ASE z wewnętrznym modułem równoważenia obciążenia | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z bramą aplikacji przy użyciu wewnętrznego modułu równoważenia obciążenia za pomocą środowiska usługi App Service na platformie Azure
services: vpn-gateway
documentationCenter: na
author: genlin
manager: cshepard
editor: ''
tags: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2018
ms.author: genli
ms.openlocfilehash: baed2b23a321c53a614303d3085fbb3a4bf6ad0b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60831099"
---
# <a name="back-end-server-certificate-is-not-whitelisted-for-an-application-gateway-using-an-internal-load-balancer-with-an-app-service-environment"></a>Certyfikat serwera zaplecza nie jest umieszczona na białej liście dla bramy aplikacji przy użyciu wewnętrznego modułu równoważenia obciążenia w środowisku usługi App Service

Ten artykuł pomaga w rozwiązaniu następujący problem: Certyfikat nie jest umieszczona na białej liście, podczas tworzenia bramy aplikacji przy użyciu wewnętrznego obciążenia Balancer (ILB) wraz z App Service Environment (ASE) na zapleczu, korzystając z protokołu SSL end-to-end na platformie Azure.

## <a name="symptoms"></a>Objawy

Po utworzeniu bramy aplikacji przy użyciu wewnętrznego modułu równoważenia obciążenia środowiska ASE na zapleczu serwera zaplecza może stać się złej kondycji. Ten problem występuje, jeśli certyfikat uwierzytelniania usługi application gateway nie jest zgodny skonfigurowany certyfikat na serwerze zaplecza. Na przykład, zobacz następujący scenariusz:

**Konfiguracja bramy aplikacji:**

- **Odbiornik:** Połączenia obejmujące wiele lokacji
- **Port:** 443
- **Nazwa hosta:** test.appgwtestase.com
- **Certyfikat SSL:** CN=test.appgwtestase.com
- **Pula zaplecza:** Adres IP lub nazwa FQDN
- **Adres IP:** : 10.1.5.11
- **Ustawienia HTTP:** HTTPS
- **Port:** : 443
- **Niestandardowe sondy:** Hostname – test.appgwtestase.com
- **Certyfikat uwierzytelniania:** cer test.appgwtestase.com
- **Kondycja wewnętrznej bazy danych:** Zła — certyfikatu serwera wewnętrznej bazy danych nie jest na liście dozwolonych usługi Application Gateway.

**Konfigurowanie środowiska ASE:**

- **ADRES IP WEWNĘTRZNEGO MODUŁU RÓWNOWAŻENIA OBCIĄŻENIA:** 10.1.5.11
- **Nazwa domeny:** appgwtestase.com
- **Usługa App Service:** test.appgwtestase.com
- **Powiązania SSL:** SNI SSL – CN=test.appgwtestase.com

Gdy uzyskujesz dostęp do bramy aplikacji, otrzymasz następujący komunikat o błędzie, ponieważ serwer zaplecza jest w złej kondycji:

**502 — Serwer sieci web odebrał nieprawidłową odpowiedź, działając jako brama lub serwer proxy.**

## <a name="solution"></a>Rozwiązanie

Gdy nazwa hosta nie umożliwia dostęp do witryny sieci Web protokołu HTTPS, serwerów zaplecza zwróci skonfigurowany certyfikat w domyślnej witrynie sieci Web, w przypadku, gdy SNI jest wyłączona. Środowisko ASE z wewnętrznym modułem równoważenia obciążenia, aby uzyskać domyślny certyfikat pochodzi z certyfikatu wewnętrznego modułu równoważenia obciążenia. Jeśli nie ma żadnych certyfikatów skonfigurowanych dla wewnętrznego modułu równoważenia obciążenia, certyfikat pochodzi z certyfikatu usługi App środowiska ASE.

Gdy używasz w pełni kwalifikowaną nazwę domeny (FQDN) na dostęp do wewnętrznego modułu równoważenia obciążenia serwerów zaplecza zwróci poprawny certyfikat, który jest przekazywany w ustawieniach protokołu HTTP. Jeśli to znaczy nie tak, należy wziąć pod uwagę następujące opcje:

- Użyj nazwy FQDN w puli zaplecza bramy aplikacji, aby wskazywał na adres IP wewnętrznego modułu równoważenia obciążenia. Tej opcji tylko wtedy, gdy masz prywatnej strefy DNS lub niestandardowy serwer DNS skonfigurowany. W przeciwnym razie należy utworzyć rekord "A" na publiczną usługą DNS.

- W systemie przekazany certyfikat wewnętrznego modułu równoważenia obciążenia lub domyślnego certyfikatu (certyfikat wewnętrznego modułu równoważenia obciążenia) w ustawieniach protokołu HTTP. Application gateway pobiera certyfikat, gdy uzyskuje dostęp do IP wewnętrznego modułu równoważenia obciążenia firmy sondowania.

- W systemie certyfikat wieloznaczny wewnętrznego modułu równoważenia obciążenia i serwerów zaplecza, aby dla wszystkich witryn internetowych, certyfikat jest wspólne. Jednak to rozwiązanie jest możliwe tylko w przypadku domen podrzędnych i nie, jeśli każdej z witryn sieci Web wymagają różnych nazw hostów.

- Wyczyść **usługi App service na użytek** opcji dla usługi application gateway, w przypadku, gdy używasz adres IP wewnętrznego modułu równoważenia obciążenia.

Aby zmniejszyć obciążenie, możesz przekazać certyfikat wewnętrznego modułu równoważenia obciążenia w ustawieniach protokołu HTTP, aby ścieżka sondy pracy. (Ten krok jest tylko na potrzeby umieszczania na białej liście. Nie będzie używany do komunikacji SSL.) Możesz pobrać certyfikat wewnętrznego modułu równoważenia obciążenia, uzyskując dostęp do wewnętrznego modułu równoważenia obciążenia przy użyciu jego adresu IP, z poziomu przeglądarki przy użyciu protokołu HTTPS, a następnie eksportowanie certyfikatu protokołu SSL w Base-64 kodowany w formacie CER i przekazywanie certyfikatu na odpowiednie ustawienia protokołu HTTP.

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną

Jeśli nadal potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem.
