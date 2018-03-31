---
title: Uaktualnianie projektów do bieżącej wersji narzędzi platformy Azure | Dokumentacja firmy Microsoft
description: Informacje o sposobie uaktualniania projektu platformy Azure w programie Visual Studio do bieżącej wersji narzędzi platformy Azure
services: visual-studio-online
documentationcenter: na
author: ghogen
manager: douge
editor: ''
ms.assetid: 1d64070a-078d-468a-87f4-e6715de6475f
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/18/2016
ms.author: ghogen
ms.openlocfilehash: baa85d8f525701e8e9c23324e9d2619a2c50360b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-upgrade-projects-to-the-current-version-of-the-azure-tools-for-visual-studio"></a>Jak uaktualnić projekty na bieżącą wersję narzędzia Azure dla programu Visual Studio
## <a name="overview"></a>Przegląd
Po zainstalowaniu bieżącej wersji narzędzi Azure (lub poprzedniej wersji, która jest nowsza niż 1.6), wszystkie projekty, które zostały utworzone przy użyciu narzędzia Azure Zwolnij przed 1.6 (listopad 2011) zostaną automatycznie uaktualnione jak je otworzyć. Nadal masz zainstalowany zwolnienia projektów został utworzony za pomocą wersji 1.6 (listopad 2011) tych narzędzi, możesz otworzyć te projekty w starszej wersji i zdecyduje później, czy należy uaktualnić ich.

## <a name="how-your-project-changes-when-you-upgrade-it"></a>Jak projekt zmiany po uaktualnieniu
Jeśli projekt zostanie automatycznie uaktualniony, określ, czy chcesz ją uaktualnić projektu są modyfikowane w celu pracy z bieżącymi wersjami niektóre zestawy, a niektóre właściwości również są zmieniane, zgodnie z opisem w tej sekcji. Jeśli projekt wymaga inne zmiany, aby był zgodny z nowszą wersją narzędzia, musisz wprowadzić te zmiany ręcznie.

* Plik web.config dla ról sieć web i plik app.config dla roli proces roboczy są aktualizowane do nowszej wersji Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitoirTraceListener.dll odwołania.
* Zestawy Microsoft.WindowsAzure.StorageClient.dll, Microsoft.WindowsAzure.Diagnostics.dll i Microsoft.WindowsAzure.ServiceRuntime.dll zostaną uaktualnione do nowej wersji.
* Profilów publikowania, które były przechowywane w pliku projektu platformy Azure (ccproj) są przenoszone do osobnego pliku z .azurePubXml rozszerzenia w **publikowania** podkatalogu.
* Niektóre właściwości w profilu publikowania zostały zaktualizowane do obsługi nowych i zmienionych funkcji. **AllowUpgrade** zastępuje **DeploymentReplacementMethod** ponieważ równocześnie lub przyrostowo można zaktualizować usługi w chmurze wdrożone.
* Właściwość **UseIISExpressByDefault** zostanie dodany i wartość false, aby serwer sieci web, który jest używany do debugowania zmienią się automatycznie z Internet Information Services (IIS) do programu IIS Express. Usługi IIS Express to domyślnego serwera sieci web dla projektów, które zostały utworzone z nowszą wersją narzędzi.
* Jeśli buforowanie Azure znajduje się w co najmniej jeden z projektów ról, niektóre właściwości w konfiguracji usługi (plik .cscfg) i definicji usługi (plik csdef) są zmieniane po uaktualnieniu projektu. Projekt korzysta z pakietu NuGet buforowanie Azure, projekt jest uaktualniony do najnowszej wersji pakietu. Należy otworzyć plik web.config i sprawdź, czy konfiguracja klienta została zachowana poprawnie podczas procesu uaktualniania. Jeśli dodano odwołań do buforowania Azure zestawy klientów bez użycia pakietu NuGet, te zestawy nie zostaną zaktualizowane; należy ręcznie zaktualizować te odwołania do nowej wersji.

> [!IMPORTANT]
> Dla projektów języka F # należy ręcznie zaktualizować odwołania do zestawów platformy Azure, aby odwołujących się do nowszych wersji tych zestawów.
> 
> 

### <a name="how-to-upgrade-an-azure-project-to-the-current-release"></a>Jak uaktualnić projekt platformy Azure do bieżącej wersji
1. Zainstaluj bieżącą wersję narzędzia Azure do instalacji programu Visual Studio, który ma być używany w uaktualnionym projekcie, a następnie otworzyć projekt, który chcesz uaktualnić. Jeżeli projekt został utworzony przy użyciu narzędzi Azure Zwolnij przed 1.6 (listopad 2011), projekt zostanie automatycznie uaktualniony do wersji bieżącej. Jeżeli projekt został utworzony z listopad 2011 wersji i zwolnienia nadal jest zainstalowany, projekt zostanie otwarty w tej wersji.
2. W Eksploratorze rozwiązań Otwórz menu skrótów węzła projektu, wybierz pozycję **właściwości**, a następnie wybierz pozycję **aplikacji** kartę w wyświetlonym oknie dialogowym.
   
    **Aplikacji** karta jest wyświetlana wersja narzędzia skojarzoną z projektem. Jeśli pojawi się bieżącą wersję narzędzia Azure, Projekt już został uaktualniony. Jeśli po zainstalowaniu nowsza wersja narzędzi niż jakie karcie są wyświetlane, **uaktualnienia** pojawi się przycisk.
3. Wybierz **uaktualnienia** przycisk, aby uaktualnić projekt na bieżącą wersję narzędzia.
4. Skompiluj projekt, a następnie adresować błędów wynikających z interfejsu API zmiany. Aby uzyskać informacje dotyczące modyfikowania kodu dla nowej wersji zobacz dokumentację dla określonego interfejsu API.

