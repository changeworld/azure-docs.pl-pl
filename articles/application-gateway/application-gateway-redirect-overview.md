---
title: Przekierowania usługi Azure Application Gateway — omówienie | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o funkcji przekierowania w usłudze Azure Application Gateway
services: application-gateway
documentationcenter: na
author: amsriva
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 07/18/2017
ms.date: 01/15/2019
ms.author: v-junlch
ms.openlocfilehash: d05d509b67fd26c958e0e2fa2bbd877db26e6521
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60831765"
---
# <a name="application-gateway-redirect-overview"></a>Application Gateway redirect — omówienie

Jest to typowy scenariusz w przypadku wielu aplikacji sieci web do obsługi automatycznego HTTP do przekierowania protokołu HTTPS do upewnij się, że cała komunikacja między aplikacją i jej użytkownikami odbywa się za pośrednictwem ścieżki zaszyfrowanego. W przeszłości klienci wykorzystali technik, takich jak tworzenie puli zaplecza dedykowane, którego jedynym celem jest Przekierowywanie żądań otrzymywanych od protokołu HTTP do HTTPS.  Usługa Application gateway obsługuje teraz możliwość przekierowywania ruchu sieciowego w usłudze Application Gateway. Upraszcza to konfigurację aplikacji, optymalne wykorzystanie zasobów i obsługuje nowe scenariusze przekierowywania w tym globalnym i opartego na ścieżkach przekierowania. Obsługa przekierowania bramy aplikacji nie jest ograniczony do protokołu HTTP -> samodzielnie przekierowania protokołu HTTPS. Jest to mechanizm ogólnego przekierowania, umożliwia przekierowywanie ruchu odbieranego u jednego odbiornika do innego odbiornika dla usługi Application Gateway. Obsługiwane jest również przekierowanie do zewnętrznej witryny. Obsługa przekierowania dla usługi Application Gateway oferuje następujące możliwości:

1. Globalne przekierowanie z jednego odbiornika innego połączenia z odbiornikiem w bramie. Umożliwia to przekierowanie protokołu HTTP do HTTPS w witrynie.
2. Przekierowanie na podstawie ścieżki. Ten typ przekierowania pozwala HTTP do przekierowania protokołu HTTPS, tylko w obszarze określonej lokacji, na przykład obszar koszyka zakupów wskazywane przez/koszyka / *.
3. Przekierowanie do zewnętrznej witryny.

![Przekierowanie](./media/application-gateway-redirect-overview/redirect.png)

Dzięki tej zmianie klienci muszą utworzyć nowy obiekt konfiguracji przekierowania, który określa odbiornik docelowy lub zewnętrznej witryny, do którego pożądany jest przekierowania. Element konfiguracji obsługuje również opcje umożliwiające dodanie ciągu identyfikatora URI ścieżkę i zapytanie do adresu URL. Klienci mogą również wybrać, czy Przekierowanie tymczasowe (kod stanu HTTP 302) lub trwałe przekierowanie (kod stanu HTTP 301). Po utworzeniu tej konfiguracji przekierowania jest dołączony do odbiornika źródła za pomocą nowej reguły. Korzystając z podstawową regułę, konfiguracji przekierowania jest skojarzony z odbiornika źródła i globalne przekierowania. Stosowania reguły na podstawie ścieżki konfiguracji przekierowania jest zdefiniowana na mapie ścieżki adresu URL i dlatego ma zastosowanie tylko do określonej ścieżki obszaru lokacji.

### <a name="next-steps"></a>Kolejne kroki

[Konfigurowanie protokołu HTTP do przekierowania protokołu HTTPS w bramie aplikacji](redirect-http-to-https-portal.md)

<!-- Update_Description: wording update -->