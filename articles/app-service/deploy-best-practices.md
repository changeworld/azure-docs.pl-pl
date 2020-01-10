---
title: Najlepsze praktyki dotyczące wdrażania
description: Zapoznaj się z najważniejszymi mechanizmami wdrażania do Azure App Service. Znajdź zalecenia specyficzne dla języka i inne zastrzeżenia.
keywords: usługa Azure App Service, aplikacja sieci Web, wdrażanie, wdrażanie, potoki, kompilacja
author: jasonfreeberg
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.openlocfilehash: 14946a05f021a9b155fd9a9621f73bde980970fa
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750468"
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

- Punkty końcowe kudu: [kudu](https://github.com/projectkudu/kudu/wiki) to narzędzie do wydajnej produktywności dla deweloperów typu "open source", które działa jako oddzielny proces w systemie Windows App Service i jako drugi kontener w App Service systemu Linux. Kudu obsługuje ciągłe wdrożenia i udostępnia punkty końcowe HTTP do wdrożenia, takie jak zipdeploy.
- FTP i webdeploy: używanie [poświadczeń witryny lub użytkownika](deploy-configure-credentials.md)umożliwia przekazywanie plików [za pośrednictwem protokołu FTP](deploy-ftp.md) lub webdeploy. Te mechanizmy nie przechodzą przez kudu.  

Narzędzia wdrażania, takie jak Azure Pipelines, Jenkins i wtyczki edytora, korzystają z jednego z tych mechanizmów wdrażania.

## <a name="language-specific-considerations"></a>Uwagi dotyczące języka

### <a name="java"></a>Java

Użyj kudu [zipdeploy/](deploy-zip.md) API do wdrażania aplikacji jar i [Narzędzia WARDEPLOY/](deploy-zip.md#deploy-war-file) for War. Jeśli używasz Jenkins, możesz używać tych interfejsów API bezpośrednio w fazie wdrażania. Więcej informacji znajduje się w [tym artykule](../jenkins/execute-cli-jenkins-pipeline.md).

### <a name="node"></a>Węzeł

Domyślnie kudu wykonuje kroki kompilacji dla aplikacji node (`npm install`). Jeśli używasz usługi kompilacji, takiej jak Azure DevOps, kompilacja kudu jest niepotrzebna. Aby wyłączyć kompilację kudu, Utwórz ustawienie aplikacji `SCM_DO_BUILD_DURING_DEPLOYMENT`z wartością `false`.

### <a name="net"></a>.NET 

Domyślnie kudu wykonuje kroki kompilacji dla aplikacji .NET (`dotnet build`). Jeśli używasz usługi kompilacji, takiej jak Azure DevOps, kompilacja kudu jest niepotrzebna. Aby wyłączyć kompilację kudu, Utwórz ustawienie aplikacji `SCM_DO_BUILD_DURING_DEPLOYMENT`z wartością `false`.

## <a name="other-deployment-considerations"></a>Inne zagadnienia dotyczące wdrażania

### <a name="use-deployment-slots"></a>Korzystanie z miejsc wdrożenia

Jeśli to możliwe, należy używać [miejsc wdrożenia](deploy-staging-slots.md) podczas wdrażania nowej kompilacji produkcyjnej. W przypadku korzystania ze standardowej warstwy planu App Service lub lepszej można wdrożyć aplikację w środowisku przejściowym, zweryfikować zmiany i przeprowadzić testy dymu. Gdy wszystko będzie gotowe, możesz wymienić miejsca przejściowe i produkcyjne. Operacja wymiany rozgrzewa niezbędne wystąpienia procesów roboczych w celu dopasowania do skali produkcyjnej, eliminując w ten sposób przestoje. 

### <a name="local-cache"></a>Lokalna pamięć podręczna

Azure App Service zawartość jest przechowywana w usłudze Azure Storage i jest wystawiona w sposób trwały jako udział zawartości. Jednak niektóre aplikacje potrzebują tylko magazynu zawartości o wysokiej wydajności, który może działać z wysoką dostępnością. Te aplikacje mogą korzystać z [lokalnej pamięci podręcznej](overview-local-cache.md). Lokalna pamięć podręczna nie jest zalecana w przypadku witryn zarządzania zawartością, takich jak WordPress.

Zawsze używaj lokalnej pamięci podręcznej w połączeniu z miejscami [wdrożenia](deploy-staging-slots.md) , aby zapobiec przestojom. Zapoznaj się z [tą sekcją](overview-local-cache.md#best-practices-for-using-app-service-local-cache) , aby uzyskać informacje na temat korzystania z tych funkcji razem.

### <a name="high-cpu-or-memory"></a>Wysoki procesor lub pamięć

Jeśli plan App Service jest używany przez ponad 90% dostępnego procesora lub pamięci, źródłowa maszyna wirtualna może mieć problemy z przetwarzaniem wdrożenia. W takim przypadku należy tymczasowo skalować liczbę wystąpień w celu przeprowadzenia wdrożenia. Po zakończeniu wdrożenia można zwrócić liczbę wystąpień do poprzedniej wartości.

Aby uzyskać więcej informacji na temat najlepszych rozwiązań, odwiedź stronę [App Service Diagnostics](https://docs.microsoft.com/azure/app-service/overview-diagnostics) , aby dowiedzieć się, jakie są najlepsze rozwiązania, które są odpowiednie dla danego zasobu.

- Przejdź do aplikacji sieci Web w [Azure Portal](https://portal.azure.com).
- Kliknij przycisk **diagnozowanie i rozwiązywanie problemów** w lewym okienku nawigacji, co spowoduje otwarcie App Service diagnostyki.
- Wybierz kafelek strony głównej **najlepszych** rozwiązań.
- Kliknij pozycję **najlepsze rozwiązania dotyczące dostępności & wydajność** lub **najlepsze rozwiązania w zakresie optymalnej konfiguracji** , aby wyświetlić bieżący stan aplikacji w odniesieniu do tych najlepszych rozwiązań.

Za pomocą tego linku można również bezpośrednio otworzyć App Service Diagnostics dla zasobu: `https://ms.portal.azure.com/?websitesextension_ext=asd.featurePath%3Ddetectors%2FParentAvailabilityAndPerformance#@microsoft.onmicrosoft.com/resource/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{siteName}/troubleshoot`.
