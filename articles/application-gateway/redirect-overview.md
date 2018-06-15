---
title: Omówienie przekierowania bramy aplikacji Azure
description: Więcej informacji na temat możliwości przekierowania bramę aplikacji Azure
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
ms.openlocfilehash: 65c631ca9beb5eab5d8fe2b7e71daa0cf3b768fa
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
ms.locfileid: "33204381"
---
# <a name="application-gateway-redirect-overview"></a>Omówienie przekierowania bramy aplikacji

Typowy scenariusz dla wielu aplikacji sieci web jest obsługuje automatyczne HTTP do przekierowania protokołu HTTPS do upewnij się, że cała komunikacja między aplikacją a jego użytkowników odbywa się za pośrednictwem ścieżki zaszyfrowane. W przeszłości klienci użyto technik, takich jak tworzenie puli dedykowanych wewnętrznej bazy danych, którego jedynym celem jest Przekierowywanie żądań dotyczących otrzymanej od protokołu HTTP, HTTPS.

Brama aplikacji w obsługuje teraz możliwość przekierowania ruchu w bramie. Upraszcza konfigurację aplikacji, optymalizuje wykorzystanie zasobów i obsługę nowych scenariuszy przekierowania tym globalnych i opartych na ścieżce przekierowania. Obsługa przekierowania bramy aplikacji nie jest ograniczona do HTTP -> wyłącznie przekierowania protokołu HTTPS. Ma ona mechanizm ogólnego przekierowania, co umożliwia obsługę przekierowywania ruchu odebraniu jednego odbiornika do innego odbiornika dla bramy aplikacji. Przekierowanie do zewnętrznej witryny jest również obsługiwany.

Obsługa przekierowania bramy aplikacji oferuje następujące możliwości:

-  **Globalne przekierowania**

   Przekierowuje z jednego odbiornika do innego odbiornika w bramie. Dzięki temu HTTP HTTPS przekierowania w witrynie.
- **Na podstawie Ścieżka przekierowania**

   Ten typ przekierowania pozwala HTTP przekierowania HTTPS tylko w obszarze określonej lokacji, na przykład obszar koszyka zakupów wskazywane przez/koszyka / *.
- **Przekieruj do zewnętrznej witryny**

![przekierowania](./media/redirect-overview/redirect.png)

Z tą zmianą klientów należy utworzyć nowy obiekt konfiguracji przekierowania, określający odbiornika docelowego lub witryny zewnętrznej, z którym wymagane jest przekierowanie. Element konfiguracji również obsługuje opcje umożliwiające dodanie ciągu identyfikatora URI ścieżka i kwerenda do adresu URL. Można również wybrać, czy Przekierowanie tymczasowe (kod stanu HTTP 302) lub przekierowanie trwałe (kod stanu HTTP 301). Po utworzeniu tej konfiguracji przekierowania jest dołączony do odbiornika źródła za pomocą nowej reguły. Używając podstawowe reguły, przekierowanie konfiguracja jest skojarzony z odbiornika źródła i globalnych przekierowania. Użycie reguły na podstawie ścieżki konfiguracji przekierowania jest zdefiniowana na mapie ścieżki adresu URL. Dlatego dotyczy tylko obszarze określonej ścieżki witryny.

### <a name="next-steps"></a>Kolejne kroki

[Skonfiguruj adres URL przekierowania na bramy aplikacji](tutorial-url-redirect-powershell.md)
