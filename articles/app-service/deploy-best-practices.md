---
title: Najlepsze praktyki dotyczące wdrażania
description: Dowiedz się więcej o kluczowych mechanizmach wdrażania w usłudze Azure App Service. Znajdź zalecenia specyficzne dla języka i inne zastrzeżenia.
keywords: usługa aplikacji platformy Azure, aplikacja internetowa, wdrażanie, wdrażanie, potoki, kompilacja
author: jasonfreeberg
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.openlocfilehash: 14946a05f021a9b155fd9a9621f73bde980970fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750468"
---
# <a name="deployment-best-practices"></a>Najważniejsze wskazówki dotyczące wdrażania

Każdy zespół deweloperów ma unikatowe wymagania, które mogą utrudnić wdrożenie potoku wdrażania wydajnego w dowolnej usłudze w chmurze. W tym artykule przedstawiono trzy główne składniki wdrażania w usłudze App Service: źródła wdrażania, potoki kompilacji i mechanizmy wdrażania. W tym artykule opisano również niektóre najlepsze rozwiązania i porady dotyczące stosów określonych języków.

## <a name="deployment-components"></a>Składniki wdrażania

### <a name="deployment-source"></a>Źródło wdrożenia

Źródło wdrożenia jest lokalizacją kodu aplikacji. W przypadku aplikacji produkcyjnych źródłem wdrożenia jest zwykle repozytorium hostowane przez oprogramowanie do kontroli wersji, takie jak [GitHub, BitBucket lub Azure Repos](deploy-continuous-deployment.md). W scenariuszach programowania i testowania źródłem wdrożenia może być [projekt na komputerze lokalnym.](deploy-local-git.md) Usługa App Service obsługuje również [foldery OneDrive i Dropbox](deploy-content-sync.md) jako źródła wdrożenia. Podczas gdy foldery w chmurze można ułatwić rozpoczęcie pracy z usługą App Service, zazwyczaj nie zaleca się używać tego źródła dla aplikacji produkcyjnych na poziomie przedsiębiorstwa. 

### <a name="build-pipeline"></a>Potok kompilacji

Po podjęciu decyzji o źródle wdrożenia następnym krokiem jest wybranie potoku kompilacji. Potok kompilacji odczytuje kod źródłowy ze źródła wdrożenia i wykonuje serię kroków (takich jak kompilacja kodu, minifying HTML i JavaScript, uruchamianie testów i składników pakowania), aby uzyskać aplikację w stanie runnable. Określone polecenia wykonywane przez potok kompilacji zależą od stosu języka. Te operacje mogą być wykonywane na serwerze kompilacji, takich jak usługi Azure Potoki, lub wykonywane lokalnie.

### <a name="deployment-mechanism"></a>Mechanizm wdrażania

Mechanizm wdrażania jest działaniem używanym do umieszczenia wbudowanej aplikacji w katalogu */home/site/wwwroot* aplikacji sieci web. Katalog */wwwroot* jest zainstalowaną lokalizacją magazynu współużytkowanym przez wszystkie wystąpienia aplikacji sieci web. Gdy mechanizm wdrażania umieszcza aplikację w tym katalogu, wystąpienia otrzymują powiadomienie o synchronizacji nowych plików. Usługa App Service obsługuje następujące mechanizmy wdrażania:

- Punkty końcowe Kudu: [Kudu](https://github.com/projectkudu/kudu/wiki) to narzędzie do produktywności dla deweloperów typu open source, które działa jako oddzielny proces w usłudze Windows App Service i jako drugi kontener w usłudze Linux App Service. Kudu obsługuje ciągłe wdrożenia i zapewnia punkty końcowe HTTP do wdrożenia, takie jak zipdeploy.
- FTP i WebDeploy: Za pomocą [poświadczeń witryny lub użytkownika](deploy-configure-credentials.md)można przesyłać pliki [za pośrednictwem ftp](deploy-ftp.md) lub WebDeploy. Mechanizmy te nie przechodzą przez Kudu.  

Narzędzia wdrażania, takie jak potoki platformy Azure, jenkins i wtyczki edytora używają jednego z tych mechanizmów wdrażania.

## <a name="language-specific-considerations"></a>Zagadnienia specyficzne dla języka

### <a name="java"></a>Java

Użyj [zipdeploy](deploy-zip.md) Kudu / API do wdrażania aplikacji JAR i [wardeploy /](deploy-zip.md#deploy-war-file) dla aplikacji WAR. Jeśli używasz usługi Jenkins, można użyć tych interfejsów API bezpośrednio w fazie wdrażania. Aby uzyskać więcej informacji, zobacz [ten artykuł](../jenkins/execute-cli-jenkins-pipeline.md).

### <a name="node"></a>Węzeł

Domyślnie Kudu wykonuje kroki kompilacji dla`npm install`aplikacji Node ( ). Jeśli używasz usługi kompilacji, takich jak Azure DevOps, następnie kompilacji Kudu jest niepotrzebne. Aby wyłączyć kompilację Kudu, utwórz ustawienie aplikacji, `SCM_DO_BUILD_DURING_DEPLOYMENT`o wartości . `false`

### <a name="net"></a>.NET 

Domyślnie Kudu wykonuje kroki kompilacji dla aplikacji`dotnet build`.Net ( ). Jeśli używasz usługi kompilacji, takich jak Azure DevOps, następnie kompilacji Kudu jest niepotrzebne. Aby wyłączyć kompilację Kudu, utwórz ustawienie aplikacji, `SCM_DO_BUILD_DURING_DEPLOYMENT`o wartości . `false`

## <a name="other-deployment-considerations"></a>Inne zagadnienia dotyczące wdrażania

### <a name="use-deployment-slots"></a>Korzystanie z gniazd wdrożeniowych

Jeśli to możliwe, należy używać gniazd wdrażania podczas wdrażania nowej [kompilacji](deploy-staging-slots.md) produkcyjnej. Korzystając z warstwy Standardowy plan usługi aplikacji lub lepiej, można wdrożyć aplikację w środowisku przejściowym, sprawdzić poprawność zmian i zrobić testy dymu. Gdy będziesz gotowy, możesz zamienić swoje miejsca przejściowe i produkcyjne. Operacja wymiany rozgrzewa wystąpienia niezbędne do pracy, aby dopasować je do skali produkcyjnej, eliminując w ten sposób przestoje. 

### <a name="local-cache"></a>Lokalna pamięć podręczna

Zawartość usługi Azure App Service jest przechowywana w usłudze Azure Storage i jest udostępniana w trwały sposób jako udział zawartości. Jednak niektóre aplikacje potrzebują tylko wydajnego magazynu zawartości tylko do odczytu, który można uruchomić z wysoką dostępnością. Te aplikacje mogą korzystać z [lokalnej pamięci podręcznej](overview-local-cache.md). Lokalna pamięć podręczna nie jest zalecana dla witryn do zarządzania treścią, takich jak WordPress.

Zawsze używaj lokalnej pamięci podręcznej w połączeniu z [gniazdami wdrażania,](deploy-staging-slots.md) aby zapobiec przestojom. Zobacz [tę sekcję,](overview-local-cache.md#best-practices-for-using-app-service-local-cache) aby uzyskać informacje na temat korzystania z tych funkcji razem.

### <a name="high-cpu-or-memory"></a>Wysoki poziom procesora lub pamięci

Jeśli plan usługi aplikacji używa ponad 90% dostępnego procesora CPU lub pamięci, podstawowa maszyna wirtualna może mieć problemy z przetwarzaniem wdrożenia. W takim przypadku tymczasowo skaluj liczbę wystąpień w górę, aby wykonać wdrożenie. Po zakończeniu wdrażania można zwrócić liczbę wystąpień do poprzedniej wartości.

Aby uzyskać więcej informacji na temat najlepszych rozwiązań, odwiedź [diagnostykę usługi aplikacji,](https://docs.microsoft.com/azure/app-service/overview-diagnostics) aby dowiedzieć się, jakie praktyczne są najlepsze rozwiązania dotyczące danego zasobu.

- Przejdź do aplikacji sieci Web w [witrynie Azure portal](https://portal.azure.com).
- Kliknij przycisk **Diagnozuj i rozwiązuj problemy** w lewej nawigacji, która otwiera diagnostykę usługi app service.
- Wybierz kafelek strony głównej **Najważniejsze wskazówki.**
- Kliknij **przycisk Najważniejsze wskazówki dotyczące dostępności & wydajności** lub najlepszych **rozwiązań dotyczących optymalnej konfiguracji,** aby wyświetlić bieżący stan aplikacji w odniesieniu do tych najlepszych rozwiązań.

Możesz również użyć tego łącza, aby bezpośrednio otworzyć diagnostykę usługi app service dla zasobu: `https://ms.portal.azure.com/?websitesextension_ext=asd.featurePath%3Ddetectors%2FParentAvailabilityAndPerformance#@microsoft.onmicrosoft.com/resource/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{siteName}/troubleshoot`.
