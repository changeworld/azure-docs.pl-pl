---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 09/18/2019
ms.author: cephalin
ms.openlocfilehash: e00db06346b19ef85eb77626eb2ed169d2224b6c
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71129689"
---
W przypadku klonowania konfiguracji z innego miejsca wdrożenia sklonowana konfiguracja jest edytowalna. Niektóre elementy konfiguracji są zgodne z zawartością w wymianie (nie w konkretnym gnieździe), podczas gdy inne elementy konfiguracji pozostają w tym samym miejscu po wymianie (specyficzne dla miejsca). Poniższe listy pokazują ustawienia, które zmieniają się w przypadku wymiany miejsc.

**Ustawienia, które zostały zamienione**:

* Ustawienia ogólne, takie jak wersja platformy, 32/64-bitowa, gniazda sieci Web
* Ustawienia aplikacji (można skonfigurować do nalepki do miejsca)
* Parametry połączenia (można je skonfigurować do nawiązania połączenia z gniazdem)
* Mapowania procedur obsługi
* Certyfikaty publiczne
* Zawartość zadań WebJob
* Połączenia hybrydowe *
* Integracja sieci wirtualnej *
* Punkty końcowe usługi *
* Azure Content Delivery Network *

Funkcje oznaczone gwiazdką (*) są planowane jako niezamienione. 

**Ustawienia, które nie zostały zamienione**:

* Publikowanie punktów końcowych
* Nazwy domen niestandardowych
* Certyfikaty inne niż publiczne i ustawienia protokołu TLS/SSL
* Ustawienia skalowania
* Harmonogramy zadań WebJob
* Ograniczenia adresów IP
* Zawsze włączone
* Ustawienia dziennika diagnostycznego
* Współużytkowanie zasobów między źródłami (CORS)

> [!NOTE]
> Niektóre ustawienia aplikacji, które mają zastosowanie do niewymienianych ustawień, również nie są zamieniane. Na przykład, ponieważ ustawienia dziennika diagnostycznego nie są zamieniane, powiązane ustawienia aplikacji `WEBSITE_HTTPLOGGING_RETENTION_DAYS` , `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` takie jak i, również nie są zamieniane, nawet jeśli nie są wyświetlane jako ustawienia gniazda.
>
