---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/18/2019
ms.author: cephalin
ms.openlocfilehash: 0691b1a531ffebbb2c368bdb37dd4d8025fb4a4e
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69623705"
---
W przypadku klonowania konfiguracji z innego miejsca wdrożenia sklonowana konfiguracja jest edytowalna. Niektóre elementy konfiguracji są zgodne z zawartością w wymianie (nie w konkretnym gnieździe), podczas gdy inne elementy konfiguracji pozostają w tym samym miejscu po wymianie (specyficzne dla miejsca). Poniższe listy pokazują ustawienia, które zmieniają się w przypadku wymiany miejsc.

**Ustawienia, które zostały zamienione**:

* Ustawienia ogólne, takie jak wersja platformy, 32/64-bitowa, gniazda sieci Web
* Ustawienia aplikacji (można skonfigurować do nalepki do miejsca)
* Parametry połączenia (można je skonfigurować do nawiązania połączenia z gniazdem)
* Mapowania procedur obsługi
* Ustawienia monitorowania i diagnostyki
* Certyfikaty publiczne
* Zawartość zadań WebJob
* Połączenia hybrydowe *
* Integracja sieci wirtualnej *
* Punkty końcowe usługi *
* Azure Content Delivery Network *

Funkcje oznaczone gwiazdką (*) są planowane do wprowadzenia do gniazda. 

**Ustawienia, które nie zostały zamienione**:

* Publikowanie punktów końcowych
* Nazwy domen niestandardowych
* Certyfikaty prywatne i powiązania SSL
* Ustawienia skalowania
* Harmonogramy zadań WebJob
* Ograniczenia adresów IP
* Zawsze włączone
* Ustawienia protokołu (HTTPS, wersja protokołu TLS, certyfikaty klienta)
* Ustawienia dziennika diagnostycznego
* Współużytkowanie zasobów między źródłami (CORS)

<!-- VNET and hybrid connections not yet sticky to slot -->