---
title: Najlepsze rozwiązania — Azure App Service
description: Poznaj najlepsze rozwiązania i rozwiązywanie problemów dotyczących Azure App Service.
services: app-service
documentationcenter: ''
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.assetid: f3359464-fa44-4f4a-9ea6-7821060e8d0d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/01/2016
ms.author: dariac
ms.custom: seodec18
ms.openlocfilehash: c40191c8682d6ff93f70e0853e767c89248ae887
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70071621"
---
# <a name="best-practices-for-azure-app-service"></a>Najlepsze rozwiązania dotyczące usługi Azure App Service
W tym artykule opisano najlepsze rozwiązania dotyczące korzystania z [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). 

## <a name="colocation"></a>Wspólnej lokalizacji
Gdy zasoby platformy Azure tworzące rozwiązanie, takie jak aplikacja sieci Web i baza danych znajdują się w różnych regionach, mogą mieć następujące skutki:

* Zwiększone opóźnienie komunikacji między zasobami
* Opłaty pieniężne za transfer danych wychodzących dla ruchu wychodzącego na [stronie cennika platformy Azure](https://azure.microsoft.com/pricing/details/data-transfers).

Współlokalizacja w tym samym regionie jest Najlepsza w przypadku zasobów platformy Azure składających się na rozwiązanie, takie jak aplikacja internetowa i baza danych lub konto magazynu używane do przechowywania zawartości lub danych. Podczas tworzenia zasobów upewnij się, że znajdują się one w tym samym regionie świadczenia usługi Azure, chyba że masz konkretną przyczynę biznesową lub konstrukcyjną. Aplikację App Service można przenieść do tego samego regionu, w którym znajduje się baza danych, za pomocą [funkcji klonowania App Service](app-service-web-app-cloning.md) dostępnej obecnie dla aplikacji w warstwie Premium App Service plan.   

## <a name="memoryresources"></a>Gdy aplikacje zużywają więcej pamięci niż oczekiwano
Jeśli zauważysz, że aplikacja zużywa więcej pamięci niż oczekiwano, jak to zostało wskazane przez monitorowanie lub zalecenia dotyczące usług, weź pod uwagę [App Service funkcję autonaprawy](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites). Jedną z opcji funkcji autonaprawy jest podejmowanie akcji niestandardowych na podstawie progu pamięci. Akcje obejmują zakres od powiadomień e-mail w celu przeprowadzenia badania przez zrzut pamięci w celu uzyskania środków zaradczych przez odtwarzanie procesu roboczego. Funkcja autonaprawy można skonfigurować za pośrednictwem pliku Web. config i za pośrednictwem przyjaznego interfejsu użytkownika, zgodnie z opisem w tym wpisie w blogu dotyczącym [rozszerzenia witryny App Service Support](https://azure.microsoft.com/blog/additional-updates-to-support-site-extension-for-azure-app-service-web-apps).   

## <a name="CPUresources"></a>Gdy aplikacje zużywają więcej niż oczekiwany procesor CPU
Jeśli zauważysz, że aplikacja zużywa więcej czasu procesora CPU niż oczekiwano lub w coraz większej liczbie procesorów CPU, jak wskazano w zaleceniach dotyczących monitorowania lub usług, rozważ skalowanie w górę lub skalowanie planu App Service. Jeśli aplikacja jest stanowa, skalowanie w górę jest jedyną opcją, ale jeśli aplikacja jest bezstanowa, skalowanie w poziomie zapewnia większą elastyczność i większą możliwość skalowania. 

Aby uzyskać więcej informacji o aplikacjach "stanowych" vs "bezstanowe", możesz obejrzeć ten film wideo: [Zaplanuj skalowalną kompleksową aplikację wielowarstwową na Azure App Service](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B414#fbid=?hashlink=fbid). Aby uzyskać więcej informacji na temat App Service skalowania i opcji skalowania automatycznego, zobacz [skalowanie aplikacji sieci Web w Azure App Service](manage-scale-up.md).  

## <a name="socketresources"></a>Gdy zasoby gniazda są wyczerpane
Typowym powodem wyczerpania wychodzących połączeń TCP jest użycie bibliotek klienckich, które nie są zaimplementowane do ponownego użycia połączeń TCP, lub gdy protokół wyższego poziomu, taki jak HTTP-Keep-Alive nie jest używany. Zapoznaj się z dokumentacją dla każdej biblioteki, do której odwołują się aplikacje w planie App Service, aby upewnić się, że są one skonfigurowane lub dostępne w kodzie w celu wydajnego ponownego użycia połączeń wychodzących. Postępuj zgodnie ze wskazówkami dotyczącymi dokumentacji biblioteki, aby zapewnić prawidłowe tworzenie i wydawanie lub czyszczenie, aby uniknąć przecieków połączeń. Chociaż takie badania bibliotek klienta są w toku, wpływ może być skorygowany przez skalowanie do wielu wystąpień.

### <a name="nodejs-and-outgoing-http-requests"></a>Node. js i wychodzące żądania http
Podczas pracy z węzłem Node. js i wieloma wychodzącymi żądaniami HTTP, należy wziąć pod uwagę zachowanie protokołu HTTP-Keep-Alive. Możesz użyć pakietu [agentkeepalive](https://www.npmjs.com/package/agentkeepalive) `npm` , aby ułatwić jego wykonywanie w kodzie.

Zawsze Obsługuj `http` odpowiedź, nawet jeśli nie wykonujesz żadnej operacji w obsłudze. Jeśli odpowiedź nie jest prawidłowo obsługiwana, aplikacja zostanie zablokowana, ponieważ nie są dostępne żadne dodatkowe gniazda.

Na przykład podczas pracy z `http` pakietem lub: `https`

```javascript
const request = https.request(options, function(response) {
    response.on('data', function() { /* do nothing */ });
});
```

Jeśli pracujesz w systemie App Service w systemie Linux na maszynie z wieloma rdzeniami, kolejną najlepszym rozwiązaniem jest użycie PM2 do uruchomienia wielu procesów Node. js w celu wykonania aplikacji. Można to zrobić, określając polecenie uruchamiania w kontenerze.

Na przykład, aby uruchomić cztery wystąpienia:

```
pm2 start /home/site/wwwroot/app.js --no-daemon -i 4
```

## <a name="appbackup"></a>Gdy tworzenie kopii zapasowej aplikacji kończy się niepowodzeniem
Dwa najczęstsze przyczyny niepowodzenia tworzenia kopii zapasowej aplikacji: nieprawidłowe ustawienia magazynu i nieprawidłowa konfiguracja bazy danych. Te błędy są zwykle wykonywane w przypadku zmiany zasobów magazynu lub bazy danych lub zmiany sposobu uzyskiwania dostępu do tych zasobów (na przykład poświadczenia zostały zaktualizowane dla bazy danych wybranej w ustawieniach kopii zapasowej). Kopie zapasowe są zazwyczaj uruchamiane zgodnie z harmonogramem i wymagają dostępu do magazynu (w celu umieszczenia plików kopii zapasowej) i baz danych (do kopiowania i odczytywania zawartości, która ma zostać uwzględniona w kopii zapasowej). Wynikiem niepowodzenia dostępu do jednego z tych zasobów będzie spójny błąd kopii zapasowej. 

Gdy wystąpią błędy kopii zapasowej, przejrzyj najnowsze wyniki, aby zrozumieć, który typ awarii występuje. W przypadku błędów dostępu do magazynu Przejrzyj i zaktualizuj ustawienia magazynu używane w konfiguracji kopii zapasowej. W przypadku błędów dostępu do bazy danych Przejrzyj i zaktualizuj parametry połączeń w ramach ustawień aplikacji. Następnie należy zaktualizować konfigurację kopii zapasowej, aby prawidłowo dołączyć wymagane bazy danych. Aby uzyskać więcej informacji na temat kopii zapasowych aplikacji, zobacz [Tworzenie kopii zapasowej aplikacji internetowej w Azure App Service](manage-backup.md).

## <a name="nodejs"></a>Po wdrożeniu nowych aplikacji node. js do Azure App Service
Azure App Service konfigurację domyślną dla aplikacji node. js najlepiej odpowiadają potrzebom najpopularniejszych aplikacji. Jeśli konfiguracja aplikacji node. js będzie czerpać korzyści z spersonalizowanego dostrajania w celu zwiększenia wydajności lub zoptymalizowania użycia zasobów dla zasobów procesora/pamięci/sieci, zobacz [najlepsze rozwiązania i wskazówki dotyczące rozwiązywania problemów z aplikacjami węzłów na Azure App Service](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md). W tym artykule opisano ustawienia programu iisnode, które mogą być potrzebne do skonfigurowania aplikacji node. js, opisano różne scenariusze lub problemy, na które może się znajdować aplikacja, i pokazano, jak rozwiązać te problemy.

