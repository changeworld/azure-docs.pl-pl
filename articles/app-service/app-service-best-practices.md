---
title: Najlepsze rozwiązania
description: Poznaj najlepsze rozwiązania i typowe scenariusze rozwiązywania problemów z aplikacją działającą w usłudze Azure App Service.
author: dariagrigoriu
ms.assetid: f3359464-fa44-4f4a-9ea6-7821060e8d0d
ms.topic: article
ms.date: 07/01/2016
ms.author: dariac
ms.custom: seodec18
ms.openlocfilehash: ded812d5d7a0440466e7284b56c90965ea00406e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75768490"
---
# <a name="best-practices-for-azure-app-service"></a>Najlepsze rozwiązania dotyczące usługi Azure App Service
W tym artykule podsumowano najważniejsze wskazówki dotyczące korzystania z [usługi Azure App Service.](https://go.microsoft.com/fwlink/?LinkId=529714) 

## <a name="colocation"></a><a name="colocation"></a>Kolokacji
Gdy zasoby platformy Azure tworzące rozwiązanie, takie jak aplikacja sieci web i baza danych znajdują się w różnych regionach, może mieć następujące efekty:

* Zwiększone opóźnienie komunikacji między zasobami
* Opłaty pieniężne za wychodzący transfer danych między regionami, jak wspomniano na [stronie cennik platformy Azure](https://azure.microsoft.com/pricing/details/data-transfers).

Kolokacja w tym samym regionie jest najlepsza dla zasobów platformy Azure tworzących rozwiązanie, takie jak aplikacja sieci web i konto bazy danych lub magazynu używane do przechowywania zawartości lub danych. Podczas tworzenia zasobów upewnij się, że znajdują się one w tym samym regionie platformy Azure, chyba że masz określone przyczyny biznesowe lub projektowe, aby nie być. Aplikację usługi App Service można przenieść do tego samego regionu co baza danych, korzystając z [funkcji klonowania usługi App Service,](app-service-web-app-cloning.md) która jest obecnie dostępna dla aplikacji planu usługi aplikacji premium.   

## <a name="when-apps-consume-more-memory-than-expected"></a><a name="memoryresources"></a>Gdy aplikacje zużywają więcej pamięci niż oczekiwano
Jeśli zauważysz, że aplikacja zużywa więcej pamięci niż oczekiwano, jak wskazano za pomocą monitorowania lub zaleceń dotyczących usługi, należy wziąć pod uwagę [funkcję automatycznego uzdrawiania usługi App Service.](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites) Jedną z opcji funkcji automatycznego gojenia jest podjęcie akcji niestandardowych na podstawie progu pamięci. Akcje obejmują spektrum od powiadomień e-mail do badania za pośrednictwem zrzutu pamięci do łagodzenia na miejscu przez recykling procesu roboczego. Automatyczne gojenie można skonfigurować za pośrednictwem witryny web.config i przyjaznego interfejsu użytkownika, jak opisano w tym wpisie na blogu, [aby uzyskać rozszerzenie witryny pomocy technicznej usługi app service.](https://azure.microsoft.com/blog/additional-updates-to-support-site-extension-for-azure-app-service-web-apps)   

## <a name="when-apps-consume-more-cpu-than-expected"></a><a name="CPUresources"></a>Gdy aplikacje zużywają więcej procesora niż oczekiwano
Gdy zauważysz, że aplikacja zużywa więcej procesora CPU niż oczekiwano lub doświadcza powtarzających się skoków procesora CPU, jak wskazano za pomocą monitorowania lub zaleceń dotyczących usługi, należy rozważyć skalowanie w górę lub skalowanie w poziomie planu usługi app service. Jeśli aplikacja jest stanowa, skalowanie w górę jest jedyną opcją, podczas gdy jeśli aplikacja jest bezstanowa, skalowanie w poziomie zapewnia większą elastyczność i większy potencjał skalowania. 

Aby uzyskać więcej informacji na temat aplikacji typu "stateful" vs "stateless", można obejrzeć ten klip wideo: [Planowanie skalowalnej wielowarstwowej aplikacji end-to-end w usłudze Azure App Service.](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B414#fbid=?hashlink=fbid) Aby uzyskać więcej informacji na temat opcji skalowania i skalowania automatycznego usługi App Service, zobacz [Skalowanie aplikacji sieci Web w usłudze Azure App Service](manage-scale-up.md).  

## <a name="when-socket-resources-are-exhausted"></a><a name="socketresources"></a>Gdy zasoby gniazda są wyczerpane
Częstą przyczyną wyczerpania wychodzących połączeń TCP jest użycie bibliotek klienckich, które nie są implementowane do ponownego użycia połączeń TCP lub gdy protokół wyższego poziomu, taki jak HTTP - Keep-Alive nie jest używany. Przejrzyj dokumentację dla każdej z bibliotek, do których odwołuje się aplikacje w planie usługi app service, aby upewnić się, że są one skonfigurowane lub dostępne w kodzie w celu efektywnego ponownego użycia połączeń wychodzących. Postępuj również zgodnie ze wskazówkami dokumentacji biblioteki dla prawidłowego tworzenia i zwalniania lub oczyszczania, aby uniknąć przecieków połączeń. Podczas gdy badania bibliotek klienta są w toku, wpływ może być złagodzone przez skalowanie w poziomie do wielu wystąpień.

### <a name="nodejs-and-outgoing-http-requests"></a>Node.js i wychodzące żądania http
Podczas pracy z Node.js i wielu wychodzących żądań http, do czynienia z HTTP - Keep-Alive jest ważne. Można użyć [pakietu agentkeepalive,](https://www.npmjs.com/package/agentkeepalive) `npm` aby ułatwić w kodzie.

Zawsze obsługiwać `http` odpowiedzi, nawet jeśli nic nie robisz w programie obsługi. Jeśli nie obsługuje odpowiedzi poprawnie, aplikacja utknie ostatecznie, ponieważ nie więcej gniazd są dostępne.

Na przykład podczas pracy `http` `https` z lub pakiet:

```javascript
const request = https.request(options, function(response) {
    response.on('data', function() { /* do nothing */ });
});
```

Jeśli korzystasz z usługi App Service w systemie Linux na komputerze z wieloma rdzeniami, inną najlepszą praktyką jest użycie pm2 do uruchamiania wielu procesów Node.js w celu wykonania aplikacji. Można to zrobić, określając polecenie uruchamiania do kontenera.

Na przykład, aby uruchomić cztery wystąpienia:

```
pm2 start /home/site/wwwroot/app.js --no-daemon -i 4
```

## <a name="when-your-app-backup-starts-failing"></a><a name="appbackup"></a>Gdy kopia zapasowa aplikacji zaczyna się niepowodzeniem
Dwa najczęstsze powody niepowodzenia tworzenia kopii zapasowych aplikacji to: nieprawidłowe ustawienia magazynu i nieprawidłowa konfiguracja bazy danych. Te błędy zazwyczaj występują, gdy występują zmiany w zasobach magazynu lub bazy danych lub zmiany dotyczące dostępu do tych zasobów (na przykład poświadczenia zaktualizowane dla bazy danych wybranej w ustawieniach kopii zapasowej). Kopie zapasowe zazwyczaj są uruchamiane zgodnie z harmonogramem i wymagają dostępu do magazynu (do wyprowadzania plików kopii zapasowych) i baz danych (do kopiowania i odczytywania zawartości, które mają być zawarte w kopii zapasowej). W wyniku braku dostępu do jednego z tych zasobów będzie spójne niepowodzenie kopii zapasowej. 

Gdy wystąpiły błędy kopii zapasowej, przejrzyj najnowsze wyniki, aby zrozumieć, jaki typ awarii ma miejsce. W przypadku błędów dostępu do magazynu przejrzyj i zaktualizuj ustawienia magazynu używane w konfiguracji kopii zapasowej. W przypadku błędów dostępu do bazy danych, przejrzyj i zaktualizuj parametry połączeń w ramach ustawień aplikacji; następnie przejdź do aktualizacji konfiguracji kopii zapasowej, aby poprawnie uwzględnić wymagane bazy danych. Aby uzyskać więcej informacji na temat tworzenia kopii zapasowych aplikacji, zobacz [Tworzenie kopii zapasowych aplikacji sieci web w usłudze Azure App Service](manage-backup.md).

## <a name="when-new-nodejs-apps-are-deployed-to-azure-app-service"></a><a name="nodejs"></a>Gdy nowe aplikacje Node.js są wdrażane w usłudze Azure App Service
Domyślna konfiguracja usługi Azure App Service dla aplikacji Node.js jest przeznaczona najlepiej do potrzeb większości typowych aplikacji. Jeśli konfiguracja aplikacji Node.js skorzysta ze spersonalizowanego dostrajania w celu zwiększenia wydajności lub optymalizacji wykorzystania zasobów zasobów procesora/pamięci/sieci, zobacz [Przewodnik po najlepszych rozwiązaniach i rozwiązywaniu problemów dla aplikacji node w usłudze Azure App Service.](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md) W tym artykule opisano ustawienia iisnode, które mogą być konieczne do skonfigurowania aplikacji Node.js, opisano różne scenariusze lub problemy, z którymi może się borykać aplikacja, i pokazano, jak rozwiązać te problemy.


## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat najlepszych rozwiązań, odwiedź [diagnostykę usługi aplikacji,](https://docs.microsoft.com/azure/app-service/overview-diagnostics) aby dowiedzieć się, jakie praktyczne są najlepsze rozwiązania dotyczące danego zasobu.

- Przejdź do aplikacji sieci Web w [witrynie Azure portal](https://portal.azure.com).
- Kliknij przycisk **Diagnozuj i rozwiązuj problemy** w lewej nawigacji, która otwiera diagnostykę usługi app service.
- Wybierz kafelek strony głównej **Najważniejsze wskazówki.**
- Kliknij **przycisk Najważniejsze wskazówki dotyczące dostępności & wydajności** lub najlepszych **rozwiązań dotyczących optymalnej konfiguracji,** aby wyświetlić bieżący stan aplikacji w odniesieniu do tych najlepszych rozwiązań.

Możesz również użyć tego łącza, aby bezpośrednio otworzyć diagnostykę usługi app service dla zasobu: `https://ms.portal.azure.com/?websitesextension_ext=asd.featurePath%3Ddetectors%2FParentAvailabilityAndPerformance#@microsoft.onmicrosoft.com/resource/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{siteName}/troubleshoot`.