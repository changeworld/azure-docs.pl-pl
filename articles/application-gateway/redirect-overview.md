---
title: Omówienie przekierowania bramy aplikacji platformy Azure
description: Dowiedz się więcej o możliwości przekierowania w usłudze Azure Application Gateway, aby przekierować ruch odebrany przez jednego odbiornika do innego odbiornika lub do witryny zewnętrznej.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 5943d8aad4d5dd0d981fae9b2325dd3fc75b31e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74129885"
---
# <a name="application-gateway-redirect-overview"></a>Omówienie przekierowania bramy aplikacji

Bramy aplikacji można użyć do przekierowania ruchu.  Ma ona mechanizm ogólnego przekierowywania, który umożliwia przekierowywanie ruchu odbieranego przez jeden odbiornik do innego odbiornika lub do witryny zewnętrznej. Upraszcza to konfigurację aplikacji, optymalizuje użycie zasobów i obsługuje nowe scenariusze przekierowania, w tym przekierowanie globalne i oparte na ścieżce.

Typowy scenariusz przekierowania dla wielu aplikacji sieci web jest obsługa automatycznego http do https przekierowania, aby upewnić się, że cała komunikacja między aplikacją i jej użytkowników odbywa się za pośrednictwem zaszyfrowanej ścieżki. W przeszłości klienci używali technik, takich jak tworzenie dedykowanej puli zaplecza, której jedynym celem jest przekierowanie żądań otrzymywanych na http do HTTPS. Dzięki obsłudze przekierowań w bramie aplikacji można to osiągnąć po prostu dodając nową konfigurację przekierowania do reguły routingu i określając innego odbiornika z protokołem HTTPS jako odbiornik docelowy.

Obsługiwane są następujące typy przekierowań:

- 301 Stałe przekierowanie
- 302 Znaleziono
- 303 Zobacz inne
- 307 Przekierowanie tymczasowe

Obsługa przekierowania dla usługi Application Gateway oferuje następujące możliwości:

-  **Przekierowanie globalne**

   Przekierowuje z jednego odbiornika do innego odbiornika na bramie. Umożliwia to przekierowanie protokołu HTTP do HTTPS w witrynie.
- **Przekierowanie oparte na ścieżce**

   Ten typ przekierowania umożliwia przekierowanie http do HTTPS tylko w określonym obszarze witryny, na przykład obszar koszyka na zakupy oznaczony przez /cart/*.
- **Przekierowanie do witryny zewnętrznej**

![Przekierowanie](./media/redirect-overview/redirect.png)

Dzięki tej zmianie klienci muszą utworzyć nowy obiekt konfiguracji przekierowania, który określa odbiornik docelowy lub lokację zewnętrzną, do której pożądane jest przekierowanie. Element konfiguracji obsługuje również opcje umożliwiające dołączanie ścieżki identyfikatora URI i ciągu zapytania do przekierowanej wiadomości URL. Można również wybrać typ przekierowania. Po utworzeniu ta konfiguracja przekierowania jest dołączona do odbiornika źródłowego za pomocą nowej reguły. Podczas korzystania z reguły podstawowej konfiguracja przekierowania jest skojarzona z odbiornikiem źródłowym i jest przekierowaniem globalnym. Gdy używana jest reguła oparta na ścieżce, konfiguracja przekierowania jest definiowana na mapie ścieżki adresu URL. Dotyczy to tylko określonego obszaru ścieżki witryny.

### <a name="next-steps"></a>Następne kroki

[Konfigurowanie przekierowania adresów URL w bramie aplikacji](tutorial-url-redirect-powershell.md)
