---
title: Najlepsze rozwiązania dotyczące wdrażania — Azure App Service | Microsoft Docs
description: Dowiedz się więcej o najważniejszych składnikach wdrożenia do Azure App Service.
keywords: usługa Azure App Service, aplikacja sieci Web, wdrażanie, wdrażanie, potoki, kompilacja
services: app-service
documentationcenter: ''
author: jasonfreeberg
manager: ''
editor: ''
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.custom: ''
ms.openlocfilehash: d1b6444b8512b1b55ac46370e805f8f662f5f555
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70070678"
---
# <a name="deployment-best-practices"></a>Najlepsze rozwiązania dotyczące wdrażania

Każdy zespół programistyczny ma unikatowe wymagania, które mogą spowodować, że wdrożenie wydajnego potoku wdrożenia jest trudne dla każdej usługi w chmurze. W tym artykule przedstawiono trzy główne składniki wdrażania do App Service: źródła wdrożenia, potoki kompilacji i mechanizmy wdrażania. W tym artykule opisano również najlepsze rozwiązania i wskazówki dotyczące konkretnych stosów języka.

## <a name="deployment-components"></a>Składniki wdrożenia

### <a name="deployment-source"></a>Źródło wdrożenia

Źródłem wdrożenia jest lokalizacja kodu aplikacji. W przypadku aplikacji produkcyjnych źródłem wdrożenia jest zwykle repozytorium hostowane przez oprogramowanie kontroli wersji, takie jak [GitHub, BitBucket lub Azure Repos](deploy-continuous-deployment.md). W przypadku scenariuszy deweloperskich i testowych źródłem wdrożenia może być [projekt na komputerze lokalnym](deploy-local-git.md). App Service obsługuje również [foldery OneDrive i Dropbox](deploy-content-sync.md) jako źródła wdrożenia. Foldery w chmurze ułatwiają rozpoczęcie pracy z App Service, jednak zazwyczaj nie zaleca się używania tego źródła dla aplikacji produkcyjnych na poziomie przedsiębiorstwa. 

### <a name="build-pipeline"></a>Potok kompilacji

Po podjęciu decyzji dotyczącej źródła wdrożenia następnym krokiem jest wybranie potoku kompilacji. Potok kompilacji odczytuje kod źródłowy ze źródła wdrożenia i wykonuje serię kroków (takich jak kompilowanie kodu, minifikacja HTML i JavaScript, uruchamianie testów i składniki pakowania) w celu uzyskania aplikacji w stanie możliwy do uruchomienia. Określone polecenia wykonywane przez potok kompilacji zależą od stosu języka. Te operacje mogą być wykonywane na serwerze kompilacji, takim jak Azure Pipelines lub wykonywane lokalnie.

### <a name="deployment-mechanism"></a>Mechanizm wdrażania

Mechanizm wdrażania to akcja użyta do umieszczenia skompilowanej aplikacji w katalogu */Home/site/wwwroot* aplikacji sieci Web. Katalog */wwwroot* to zainstalowana lokalizacja magazynu udostępniana przez wszystkie wystąpienia aplikacji sieci Web. Gdy mechanizm wdrażania umieści aplikację w tym katalogu, Twoje wystąpienia otrzymają powiadomienie w celu zsynchronizowania nowych plików. App Service obsługuje następujące mechanizmy wdrażania:

- Kudu punkty końcowe: [Kudu](https://github.com/projectkudu/kudu/wiki) to narzędzie do wydajnego produktywności dla deweloperów typu "open source", które działa jako oddzielny proces w systemie Windows App Service i jako drugi kontener w App Service systemu Linux. Kudu obsługuje ciągłe wdrożenia i udostępnia punkty końcowe HTTP do wdrożenia, takie jak zipdeploy.
- FTP i webdeploy: Za pomocą [poświadczeń witryny lub użytkownika](deploy-configure-credentials.md)można przekazywać pliki [za pośrednictwem protokołu FTP](deploy-ftp.md) lub webdeploy. Te mechanizmy nie przechodzą przez kudu.  

Narzędzia wdrażania, takie jak Azure Pipelines, Jenkins i wtyczki edytora, korzystają z jednego z tych mechanizmów wdrażania.

## <a name="language-specific-considerations"></a>Uwagi dotyczące języka

### <a name="java"></a>Java

Użyj kudu [zipdeploy/](deploy-zip.md) API do wdrażania aplikacji jar i [Narzędzia WARDEPLOY/](deploy-zip.md#deploy-war-file) for War. Jeśli używasz Jenkins, możesz używać tych interfejsów API bezpośrednio w fazie wdrażania. Więcej informacji znajduje się w [tym artykule](../jenkins/execute-cli-jenkins-pipeline.md).

### <a name="node"></a>Węzeł

Domyślnie kudu wykonuje kroki kompilacji dla aplikacji węzła (`npm install`). Jeśli używasz usługi kompilacji, takiej jak Azure DevOps, kompilacja kudu jest niepotrzebna. Aby wyłączyć kompilację kudu, Utwórz ustawienie `SCM_DO_BUILD_DURING_DEPLOYMENT`aplikacji o `false`wartości.

### <a name="net"></a>.NET 

Domyślnie kudu wykonuje kroki kompilacji dla aplikacji .NET (`dotnet build`). Jeśli używasz usługi kompilacji, takiej jak Azure DevOps, kompilacja kudu jest niepotrzebna. Aby wyłączyć kompilację kudu, Utwórz ustawienie `SCM_DO_BUILD_DURING_DEPLOYMENT`aplikacji o `false`wartości.

## <a name="other-deployment-considerations"></a>Inne zagadnienia dotyczące wdrażania

### <a name="use-deployment-slots"></a>Korzystanie z miejsc wdrożenia

Jeśli to możliwe, należy używać [miejsc wdrożenia](deploy-staging-slots.md) podczas wdrażania nowej kompilacji produkcyjnej. W przypadku korzystania ze standardowej warstwy planu App Service lub lepszej można wdrożyć aplikację w środowisku przejściowym, zweryfikować zmiany i przeprowadzić testy dymu. Gdy wszystko będzie gotowe, możesz wymienić miejsca przejściowe i produkcyjne. Operacja wymiany rozgrzewa niezbędne wystąpienia procesów roboczych w celu dopasowania do skali produkcyjnej, eliminując w ten sposób przestoje. 

### <a name="local-cache"></a>Lokalna pamięć podręczna

Azure App Service zawartość jest przechowywana w usłudze Azure Storage i jest wystawiona w sposób trwały jako udział zawartości. Jednak niektóre aplikacje potrzebują tylko magazynu zawartości o wysokiej wydajności, który może działać z wysoką dostępnością. Te aplikacje mogą korzystać z [lokalnej pamięci](overview-local-cache.md)podręcznej. Lokalna pamięć podręczna nie jest zalecana w przypadku witryn zarządzania zawartością, takich jak WordPress.

Zawsze używaj lokalnej pamięci podręcznej w połączeniu z [miejscami wdrożenia] (Wdróż-przemieszczanie-miejscowe MD), aby zapobiec przestojom. Zapoznaj się z [tą sekcją](overview-local-cache.md#best-practices-for-using-app-service-local-cache) , aby uzyskać informacje na temat korzystania z tych funkcji razem.

### <a name="high-cpu-or-memory"></a>Wysoki procesor lub pamięć

Jeśli plan App Service jest używany przez ponad 90% dostępnego procesora lub pamięci, źródłowa maszyna wirtualna może mieć problemy z przetwarzaniem wdrożenia. W takim przypadku należy tymczasowo skalować liczbę wystąpień w celu przeprowadzenia wdrożenia. Po zakończeniu wdrożenia można zwrócić liczbę wystąpień do poprzedniej wartości.
