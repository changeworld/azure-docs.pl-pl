---
title: Najlepsze rozwiązania dotyczące usługi Azure App Service
description: Dowiedz się, najlepsze rozwiązania i rozwiązywanie problemów dotyczących usługi Azure App Service.
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
ms.openlocfilehash: 7c5eb6190d4a4cdfa47779d2c4d7aadac5a2fb80
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/18/2018
ms.locfileid: "27868322"
---
# <a name="best-practices-for-azure-app-service"></a>Najlepsze rozwiązania dotyczące usługi Azure App Service
W tym artykule przedstawiono najlepsze rozwiązania dotyczące używania [usłudze Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714). 

## <a name="colocation"></a>Wspólnej lokalizacji
Podczas tworzenia rozwiązania, takie jak aplikacji sieci web i bazy danych zasobów platformy Azure znajdują się w różnych regionach, może mieć następujące skutki:

* Większe opóźnienia w łączności między zasobami
* Opłaty finansowe dla danych wychodzących transferu między regionu wspomnianego w [Azure cennikiem](https://azure.microsoft.com/pricing/details/data-transfers).

Wspólnej lokalizacji, w tym samym regionie jest najlepsze w przypadku tworzenia rozwiązania, takie jak aplikacji sieci web i bazy danych lub magazyn konto używane do przechowywania danych zasobów platformy Azure. Podczas tworzenia zasobów, upewnij się, że znajdują się w tym samym regionie Azure, chyba że masz powód biznesowe lub projekt do nich nie. Aplikacji usługi app Service można przenieść do tego samego regionu bazy danych przy użyciu [usługi aplikacji — funkcja klonowania](app-service-web-app-cloning.md) obecnie dostępne dla aplikacji Plan usługi aplikacji — warstwa Premium.   

## <a name="memoryresources"></a>Gdy aplikacje korzystać z większej ilości pamięci, niż oczekiwano
Jeśli zauważysz aplikacji wymaga więcej pamięci niż oczekiwano jako wskazywane przez zalecenia dotyczące monitorowania lub usługi, należy wziąć pod uwagę [aplikacji usługi automatyczne naprawianie funkcji](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites). Jedną z opcji dla funkcji Automatyczne naprawianie trwa akcje niestandardowe w oparciu próg pamięci. Akcje obejmują spektrum z powiadomienia e-mail do badania za pośrednictwem zrzutu pamięci, aby ograniczenie na miejscu przez odtwarzania procesu roboczego. Automatyczne naprawianie można skonfigurować za pomocą pliku web.config i za pośrednictwem interfejsu użytkownika przyjazną zgodnie z opisem w w tym blogu dla [aplikacji usługi pomocy technicznej witryny rozszerzenia](https://azure.microsoft.com/blog/additional-updates-to-support-site-extension-for-azure-app-service-web-apps).   

## <a name="CPUresources"></a>Gdy aplikacje korzystać więcej procesorów niż oczekiwano
Jeśli zauważysz aplikacja wykorzystuje więcej procesorów niż oczekiwano lub powtórzony nagłego Procesora środowiska wskazane za pośrednictwem zalecenia dotyczące monitorowania lub usługi, należy wziąć pod uwagę skalowanie w i skalowania plan usługi aplikacji. Jeśli aplikacja jest stanowa, skalowanie w jest jedyną opcją w przypadku aplikacji bezstanowych, skalowania w poziomie daje większą elastyczność i wyższe możliwości skalowania. 

Aby uzyskać więcej informacji o aplikacjach "bezstanowych", "stanowe" vs można Obejrzyj ten film: [planowanie skalowalności End-to-End wielowarstwowej aplikacji w aplikacji sieci Web platformy Microsoft Azure](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B414#fbid=?hashlink=fbid). Aby uzyskać więcej informacji na temat opcji skalowania i skalowanie automatyczne usługi aplikacji, zobacz [skalowanie aplikacji sieci Web w usłudze Azure App Service](web-sites-scale.md).  

## <a name="socketresources"></a>Gdy wyczerpania zasobów gniazda
Typową przyczyną wyczerpuje wychodzące połączenia TCP jest korzystanie z bibliotek klienta, nie są zaimplementowane do ponownego użycia połączenia TCP lub gdy nie jest używany protokół wyższego poziomu, takich jak HTTP - Keep-Alive. Zapoznaj się z dokumentacją dla poszczególnych bibliotek odwołuje się do aplikacji w Twojego planu usługi App Service zapewnienie są skonfigurowane lub dostępne w kodzie wydajne ponowne połączeń wychodzących. Wykonaj również biblioteki dokumentacji wskazówki dotyczące utworzenia prawidłowego i wersji lub czyszczenia, aby uniknąć przeciek połączenia. Dochodzenia takie klienta biblioteki są w toku, wpływ może skorygowane przez skalowanie w poziomie do wielu wystąpień.

### <a name="nodejs-and-outgoing-http-requests"></a>Node.js i wychodzących żądań http
Podczas pracy z wielu wychodzących żądań http i Node.js, ważne jest zajmowanie HTTP - Keep-Alive. Można użyć [agentkeepalive](https://www.npmjs.com/package/agentkeepalive) `npm` pakietu w celu ułatwienia w kodzie.

Zawsze obsługi `http` odpowiedzi, nawet jeśli nie wykonasz żadnej obsługi czynności. Jeśli odpowiedzi nie obsługują poprawnie, aplikacja zablokowała po pewnym czasie nie więcej gniazda nie są dostępne.

Na przykład podczas pracy z `http` lub `https` pakietu:

```
var request = https.request(options, function(response) {
    response.on('data', function() { /* do nothing */ });
});
```

Jeśli używasz w usłudze aplikacji w systemie Linux na maszynie z wieloma rdzeniami, inny najlepszym rozwiązaniem jest Użyj PM2, aby uruchomić wiele procesów Node.js do wykonywania aplikacji. Możesz zrobić to, określając polecenie uruchomienia z kontenera.

Na przykład, aby uruchomić czterech wystąpień:

```
pm2 start /home/site/wwwroot/app.js --no-daemon -i 4
```

## <a name="appbackup"></a>Gdy aplikacja kopii zapasowej uruchamia się niepowodzeniem
Dwie najbardziej typowe przyczyny niepowodzenia tworzenia kopii zapasowej aplikacji są: nieprawidłowy miejsca do magazynowania i nieprawidłowa konfiguracja bazy danych. Takie błędy zazwyczaj się zdarzyć, gdy istnieją zmiany do magazynu lub bazy danych zasobów lub zmiany dotyczące dostępu do tych zasobów (na przykład poświadczenia zaktualizowane dla wybranej w ustawieniach tworzenia kopii zapasowej bazy danych). Tworzenie kopii zapasowych zwykle uruchamiane zgodnie z harmonogramem i wymagają dostępu do bazy danych i magazynu (na generowanie plików kopii zapasowej) (w przypadku kopiowania i odczytu zawartości do uwzględnienia w kopii zapasowej). Wynik nie może uzyskać dostępu do żadnego z tych zasobów będą zgodne niepowodzenia wykonywania kopii zapasowej. 

Gdy wystąpi niepowodzenie wykonywania kopii zapasowej, należy przejrzeć najnowsze wyniki, aby zrozumieć, jakiego typu błędu jest wykonywane. Niepowodzeń dostępu do magazynu należy przejrzeć i zaktualizować ustawienia magazynu używane w konfiguracji kopii zapasowej. Dla niepowodzenia dostępu do bazy danych należy przejrzeć i zaktualizować Twojego ciągi połączenia w ustawieniach aplikacji; Przejdź do aktualizacji konfiguracji kopii zapasowej, aby poprawnie obejmują wymagane bazy danych. Aby uzyskać więcej informacji dotyczących tworzenia kopii zapasowych aplikacji, zobacz [kopii zapasowej aplikacji sieci web w usłudze Azure App Service](web-sites-backup.md).

## <a name="nodejs"></a>Podczas wdrażania nowych aplikacji Node.js usługi aplikacji Azure
Usługi aplikacji Azure domyślnej konfiguracji dla aplikacji Node.js ma najlepiej odpowiadający potrzebom najbardziej typowych aplikacji. Jeśli Konfiguracja aplikacji Node.js korzystałby z spersonalizowane dostrajanie do zwiększenia wydajności lub optymalizować wykorzystanie zasobów dla zasobów Procesora i pamięci/sieci, zobacz [najlepsze praktyki i przewodnik rozwiązywania problemów dla węzła aplikacji w aplikacji Azure Usługa](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md). W tym artykule opisano ustawienia programu iisnode, może być konieczne skonfigurowanie aplikacji Node.js, w tym artykule opisano różne scenariusze lub problemy mogą być dostępne w aplikacji i przedstawia sposób rozwiązania tych problemów.

