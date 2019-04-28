---
title: Najlepsze praktyki — usługa Azure App Service
description: Poznaj najlepsze rozwiązania i rozwiązywanie problemów z usługi Azure App Service.
services: app-service
documentationcenter: ''
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.assetid: f3359464-fa44-4f4a-9ea6-7821060e8d0d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/01/2016
ms.author: dariagrigoriu
ms.custom: seodec18
ms.openlocfilehash: fc3749a9ebfbf0319a57b471b6fce9f62042ba27
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60852686"
---
# <a name="best-practices-for-azure-app-service"></a>Najlepsze rozwiązania dotyczące usługi Azure App Service
Ten artykuł zawiera podsumowanie najlepsze rozwiązania dotyczące używania [usługi Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). 

## <a name="colocation"></a>Wspólnej
Podczas tworzenia rozwiązania takie jak aplikacja sieci web i bazy danych zasobów platformy Azure znajdują się w różnych regionach, może mieć następujące skutki:

* Większe opóźnienia w komunikacji między zasobami
* Pieniężnych opłaty za dane wychodzące transferu między regionami wymienionych na [platformy Azure, cennik](https://azure.microsoft.com/pricing/details/data-transfers).

Wspólną lokalizację, w tym samym regionie jest najlepsze dla zasobów platformy Azure, tworzenie rozwiązania takie jak aplikacja sieci web i bazy danych lub konta magazynu używany do przechowywania danych. Podczas tworzenia zasobów, upewnij się, że są w tym samym regionie platformy Azure, chyba że masz firmy lub projektowanie, przyczyna nie można je. Aplikacja usługi App Service można przenieść do tego samego regionu co baza danych, za pomocą [usługi App Service, funkcji klonowania](app-service-web-app-cloning.md) obecnie dostępny dla planu usługi aplikacji w warstwie Premium aplikacji.   

## <a name="memoryresources"></a>Gdy aplikacje zużywać więcej pamięci niż oczekiwano
Jeśli zauważysz, że aplikacja zużywa więcej pamięci niż oczekiwano jako wskazywane przez zalecenia dotyczące monitorowania lub usługi, należy wziąć pod uwagę [funkcji App Service automatycznego naprawiania](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites). Jedną z opcji dla funkcji automatycznego naprawiania trwa akcje niestandardowe na podstawie progu pamięci. Akcje obejmują spektrum z powiadomienia e-mail do badań za pomocą zrzutu pamięci do ograniczania ryzyka na miejscu, odtwarzania procesu roboczego. Automatyczne naprawianie można skonfigurować za pomocą pliku web.config i za pomocą interfejsu użytkownika przyjazna zgodnie z opisem na w tym wpisie w blogu dla [rozszerzenia witryny pomocy technicznej usługi aplikacji](https://azure.microsoft.com/blog/additional-updates-to-support-site-extension-for-azure-app-service-web-apps).   

## <a name="CPUresources"></a>Gdy aplikacje używać większej mocy Procesora niż oczekiwano
Jeśli zauważysz, że aplikacja zużywa większej mocy Procesora niż oczekiwano lub środowiska powtarzanych wzrostów użycia Procesora wskazywane przez zalecenia dotyczące monitorowania lub usługi, należy wziąć pod uwagę skalowanie w górę lub skalowania w poziomie plan usługi App Service. Jeśli aplikacja jest stanowa, skalowanie w górę jest jedyną opcją, jeśli Twoja aplikacja znajduje się poza bezstanowych, skalowania daje większą elastyczność i wyższych możliwości skalowania. 

Aby uzyskać więcej informacji o aplikacjach "bezstanowe" "stanowych" vs możesz obejrzeć ten film wideo: [Planowanie skalowalnej aplikacji wielowarstwowych End-to-End w usłudze Azure App Service](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B414#fbid=?hashlink=fbid). Aby uzyskać więcej informacji na temat opcji skalowania i automatycznym skalowaniem usługi App Service, zobacz [skalowanie aplikacji sieci Web w usłudze Azure App Service](web-sites-scale.md).  

## <a name="socketresources"></a>Po wyczerpaniu zasobów gniazda
Typową przyczyną wyczerpuje połączenia wychodzące TCP jest korzystanie z bibliotek klienckich, które nie są implementowane na ponowne użycie połączenia protokołu TCP lub gdy nie jest używany protokół wyższego poziomu, takich jak HTTP - Keep-Alive. Przejrzyj dokumentację dla każdej biblioteki przywoływane przez aplikacje na Plan usługi App Service do upewnij się, są one skonfigurowane lub dostępne w kodzie w poszukiwaniu wydajne ponownemu połączeń wychodzących. Również zgodnie z wytycznymi dokumentacji biblioteki dla utworzenia prawidłowego i wersji lub czyszczenia wyciekiem połączeń. Podczas takich dochodzeń biblioteki klienta jest w toku, wpływ może zminimalizować, skalując w poziomie do wielu wystąpień.

### <a name="nodejs-and-outgoing-http-requests"></a>Środowisko node.js i wychodzące żądania http
Jeśli pracujesz w języku Node.js i wiele wychodzące żądania http, ważne jest zajmowanie się HTTP - Keep-Alive. Możesz użyć [agentkeepalive](https://www.npmjs.com/package/agentkeepalive) `npm` pakietu, aby ułatwić w kodzie.

Obsługa zawsze `http` odpowiedzi, nawet jeśli to zrobisz, żadne postanowienie program obsługi. Odpowiedź nie obsługują prawidłowo, aplikacja pobiera zatrzymany po pewnym czasie nie więcej gniazda nie są dostępne.

Na przykład podczas pracy z `http` lub `https` pakietu:

```javascript
const request = https.request(options, function(response) {
    response.on('data', function() { /* do nothing */ });
});
```

Jeśli w usłudze App Service w systemie Linux są uruchomione na komputerze z wieloma rdzeniami, inny, najlepszym rozwiązaniem jest Użyj PM2, aby uruchomić wiele procesów Node.js do wykonywania aplikacji. Należy go przez określenie polecenie uruchamiania, do kontenera.

Na przykład można uruchomić wystąpień czterech:

```
pm2 start /home/site/wwwroot/app.js --no-daemon -i 4
```

## <a name="appbackup"></a>Gdy aplikacja kopii zapasowej rozpoczyna się kończy się niepowodzeniem
Dwie najbardziej typowe przyczyny, dlaczego są niepowodzenia tworzenia kopii zapasowej aplikacji: nieprawidłowych ustawień magazynu i nieprawidłowa konfiguracja bazy danych. Te błędy najczęściej występuje, gdy istnieją zmiany w zasobach magazynu lub bazy danych lub zmiany dotyczące dostępu do tych zasobów (na przykład poświadczenia zaktualizowane dla wybranej w ustawieniach kopii zapasowej bazy danych). Kopie zapasowe zazwyczaj uruchamiane zgodnie z harmonogramem i wymagają dostępu do magazynu (w przypadku podawania pliki kopii zapasowej) i bazy danych (kopiowanie i odczytu zawartości, które mają zostać uwzględnione w kopii zapasowej). Wynikiem niepowodzenie dostęp do jednej z tych zasobów będzie spójny niepowodzenia wykonywania kopii zapasowej. 

Gdy wystąpi niepowodzeniami tworzenia kopii zapasowych, należy przejrzeć najnowsze wyniki, aby zrozumieć, jakiego typu błędu jest wykonywane. Dla błędów dostępu do magazynu Przejrzyj i zaktualizuj ustawienia magazynu, używane w konfiguracji kopii zapasowej. Dla błędów dostępu do bazy danych Przejrzyj i zaktualizuj swoje ciągi połączenia w ustawieniach aplikacji; następnie można zaktualizować konfiguracji kopii zapasowej, aby prawidłowo obejmują wymagane bazy danych. Aby uzyskać więcej informacji na temat kopii zapasowych aplikacji, zobacz [tworzenie kopii zapasowej aplikacji sieci web w usłudze Azure App Service](manage-backup.md).

## <a name="nodejs"></a>Gdy nowe aplikacje Node.js są wdrażane w usłudze Azure App Service
Usługa Azure App Service domyślną konfigurację aplikacji Node.js jest przeznaczona do najlepiej spełniały potrzeby najbardziej typowych aplikacji. Jeśli Konfiguracja aplikacji Node.js używającym spersonalizowane dostrajania zwiększyć wydajność lub zoptymalizować użycie zasobów dla zasobów procesora CPU i pamięci/sieci, zobacz [najlepsze praktyki i przewodnik rozwiązywania problemów aplikacji Node w usłudze Azure App Usługa](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md). W tym artykule opisano ustawienia programu iisnode, może być konieczne skonfigurowanie aplikacji Node.js, w tym artykule opisano różne scenariusze lub problemy mogą miała dostęp do aplikacji i pokazuje, jak rozwiązać te problemy.

