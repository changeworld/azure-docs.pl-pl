---
title: Przekieruj bramy aplikacji Azure — omówienie | Dokumentacja firmy Microsoft
description: Więcej informacji na temat możliwości przekierowania bramę aplikacji Azure
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
ms.date: 07/18/2017
ms.author: amsriva
ms.openlocfilehash: e6352873ea055965b433fbf3e6e46162890e5fec
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
ms.locfileid: "33200709"
---
# <a name="application-gateway-redirect-overview"></a>Omówienie przekierowania bramy aplikacji

Typowy scenariusz dla wielu aplikacji sieci web jest obsługuje automatyczne HTTP do przekierowania protokołu HTTPS do upewnij się, że cała komunikacja między aplikacją a jego użytkowników odbywa się za pośrednictwem ścieżki zaszyfrowane. W przeszłości klienci użyto technik, takich jak tworzenie puli dedykowanych wewnętrznej bazy danych, którego jedynym celem jest Przekierowywanie żądań dotyczących otrzymanej od protokołu HTTP, HTTPS.  Brama aplikacji w obsługuje teraz możliwość przekierować ruch z bramy aplikacji. Upraszcza konfigurację aplikacji, optymalizuje wykorzystanie zasobów i obsługę nowych scenariuszy przekierowania tym globalnych i opartych na ścieżce przekierowania. Obsługa przekierowania bramy aplikacji nie jest ograniczona do HTTP -> wyłącznie przekierowania protokołu HTTPS. Jest to mechanizm ogólnego przekierowania, co umożliwia obsługę przekierowywania ruchu odebraniu jednego odbiornika do innego odbiornika dla bramy aplikacji. Obsługuje ona również przekierowanie do zewnętrznej witryny. Obsługa przekierowania bramy aplikacji oferuje następujące możliwości:

1. Globalne przekierowanie z jednego odbiornika dla innego odbiornika w bramie. Dzięki temu HTTP HTTPS przekierowania w witrynie.
2. Przekierowanie na podstawie ścieżki. Ten typ przekierowania pozwala HTTP przekierowania HTTPS tylko w obszarze określonej lokacji, na przykład obszar koszyka zakupów wskazywane przez/koszyka / *.
3. Przekieruj do zewnętrznej witryny.

![przekierowania](./media/application-gateway-redirect-overview/redirect.png)

Z tą zmianą klienci muszą tworzyć nowe przekierowania konfiguracji obiektu, który określa odbiornika docelowego lub witryny zewnętrznej, z którym wymagane jest przekierowanie. Element konfiguracji również obsługuje opcje umożliwiające dodanie ciągu identyfikatora URI ścieżka i kwerenda do adresu URL. Klientów można również wybrać, czy Przekierowanie tymczasowe (kod stanu HTTP 302) lub przekierowanie trwałe (kod stanu HTTP 301). Po utworzeniu tej konfiguracji przekierowania jest dołączony do odbiornika źródła za pomocą nowej reguły. Używając podstawowe reguły, przekierowanie konfiguracja jest skojarzony z odbiornika źródła i globalnych przekierowania. Stosowania reguły na podstawie ścieżki konfiguracji przekierowania jest zdefiniowana na mapie ścieżki adresu URL i dlatego ma zastosowanie tylko do określonej ścieżki obszaru lokacji.

### <a name="next-steps"></a>Kolejne kroki

[Skonfiguruj adres URL przekierowania na bramy aplikacji](application-gateway-configure-redirect-powershell.md)
