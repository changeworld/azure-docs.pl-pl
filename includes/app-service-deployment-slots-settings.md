---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 09/18/2019
ms.author: cephalin
ms.openlocfilehash: e00db06346b19ef85eb77626eb2ed169d2224b6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "71129689"
---
Podczas klonowania konfiguracji z innego gniazda wdrażania, sklonowana konfiguracja jest edytowalna. Niektóre elementy konfiguracji są zgodne z zawartością w całej wymiany (nie specyficzne dla gniazda), podczas gdy inne elementy konfiguracji pozostają w tym samym gnieździe po zamiany (specyficzne dla gniazda). Na poniższych listach przedstawiono ustawienia, które zmieniają się podczas wymiany gniazd.

**Ustawienia, które są zamienione:**

* Ustawienia ogólne, takie jak wersja ramowa, 32/64-bitowe, gniazda internetowe
* Ustawienia aplikacji (można skonfigurować tak, aby trzymać się gniazda)
* Parametry połączenia (można skonfigurować tak, aby przywierały do gniazda)
* Mapowania obsługi
* Certyfikaty publiczne
* Zawartość WebJobs
* Połączenia hybrydowe *
* Integracja z siecią wirtualną *
* Punkty końcowe usługi *
* Sieć dostarczania zawartości platformy Azure *

Obiekty oznaczone gwiazdką (*) mają być odsunięty od zamszu. 

**Ustawienia, które nie są zamienione:**

* Publikowanie punktów końcowych
* Niestandardowe nazwy domen
* Certyfikaty niepubliczne i ustawienia TLS/SSL
* Ustawienia skalowania
* Harmonogramy WebJobs
* Ograniczenia własności intelektualnej
* Zawsze włączone
* Ustawienia dziennika diagnostycznego
* Współużytkowy przydział zasobów między źródłami (CORS)

> [!NOTE]
> Niektóre ustawienia aplikacji, które mają zastosowanie do niezamapowanych ustawień również nie są zamieniane. Na przykład ponieważ ustawienia dziennika diagnostycznego nie są `WEBSITE_HTTPLOGGING_RETENTION_DAYS` `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` zamienione, powiązane ustawienia aplikacji, takie jak i nie są również zamienione, nawet jeśli nie są wyświetlane jako ustawienia gniazda.
>
