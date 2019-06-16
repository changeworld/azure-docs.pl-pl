---
title: Omówienie przekierowania dla usługi Azure Application Gateway
description: Dowiedz się więcej o funkcji przekierowania w usłudze Azure Application Gateway
services: application-gateway
documentationcenter: na
author: amsriva
manager: jpconnock
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/19/2018
ms.author: amsriva
ms.openlocfilehash: 8e88e0e11b3ccab7cc2c68b2617df2d588680780
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60715817"
---
# <a name="application-gateway-redirect-overview"></a>Application Gateway redirect — omówienie

Usługa application gateway umożliwia przekierowywanie ruchu.  Ma ona mechanizm ogólnego przekierowywania, który umożliwia przekierowywanie ruchu odbieranego przez jeden odbiornik do innego odbiornika lub do witryny zewnętrznej. Upraszcza to konfigurację aplikacji, optymalne wykorzystanie zasobów i obsługuje nowe scenariusze przekierowywania w tym globalnym i opartego na ścieżkach przekierowania.

Typowy scenariusz przekierowania w przypadku wielu aplikacji sieci web jest do obsługi automatycznego HTTP do przekierowania protokołu HTTPS do upewnij się, że cała komunikacja między aplikacją i jej użytkownikami odbywa się za pośrednictwem ścieżką zaszyfrowane. W przeszłości klienci wykorzystali technik, takich jak tworzenie puli zaplecza dedykowane, którego jedynym celem jest Przekierowywanie żądań otrzymywanych od protokołu HTTP do HTTPS. Dzięki obsłudze przekierowania w usłudze Application Gateway można to zrobić poprzez dodawanie nowej konfiguracji przekierowania do reguły routingu i określając inny odbiornik przy użyciu protokołu HTTPS jako odbiornik docelowy.

Obsługiwane są następujące typy przekierowania:

- 301 Stałe przekierowanie
- Znaleziono 302
- 303 Zobacz inne
- Przekierowanie tymczasowe 307

Obsługa przekierowania dla usługi Application Gateway oferuje następujące możliwości:

-  **Globalne przekierowania**

   Przekierowuje z jednego odbiornika do innego odbiornika na bramie. Umożliwia to przekierowanie protokołu HTTP do HTTPS w witrynie.
- **Przekierowanie na podstawie ścieżki**

   Ten typ przekierowania pozwala HTTP do przekierowania protokołu HTTPS, tylko w obszarze określonej lokacji, na przykład obszar koszyka zakupów wskazywane przez/koszyka / *.
- **Przekierowanie do zewnętrznej witryny**

![Przekierowanie](./media/redirect-overview/redirect.png)

Dzięki tej zmianie klienci muszą utworzyć nowy obiekt konfiguracji przekierowania, który określa odbiornik docelowy lub zewnętrznej witryny, do którego pożądany jest przekierowania. Element konfiguracji obsługuje również opcje umożliwiające dodanie ciągu identyfikatora URI ścieżkę i zapytanie do adresu URL. Możesz również typ przekierowania. Po utworzeniu tej konfiguracji przekierowania jest dołączony do odbiornika źródła za pomocą nowej reguły. Korzystając z podstawową regułę, konfiguracji przekierowania jest skojarzony z odbiornika źródła i globalne przekierowania. Stosowania reguły na podstawie ścieżki konfiguracji przekierowania jest zdefiniowana na mapie ścieżki adresu URL. Dlatego mają zastosowanie tylko do określonej ścieżki obszaru lokacji.

### <a name="next-steps"></a>Kolejne kroki

[Konfigurowanie przekierowania URL w bramie aplikacji](tutorial-url-redirect-powershell.md)
