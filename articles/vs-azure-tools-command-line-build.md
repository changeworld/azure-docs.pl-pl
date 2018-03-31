---
title: Kompilacji wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Kompilacji wiersza polecenia platformy Azure
services: visual-studio-online
documentationcenter: na
author: ghogen
manager: douge
editor: ''
ms.assetid: 94b35d0d-0d35-48b6-b48b-3641377867fd
ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2017
ms.author: ghogen
ms.openlocfilehash: 4d247be7cebdcf26c9b3d5b729bb74fb114c8034
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="building-azure-projects-from-the-command-line"></a>Kompilowanie projektów platformy Azure z poziomu wiersza polecenia
Aparat kompilacji firmy Microsoft (MSBuild) można tworzyć produktów w środowisku laboratorium kompilacji, których nie zainstalowano programu Visual Studio. Program MSBuild używa formatu XML dla plików projektu, które extensible i w pełni obsługiwane przez firmę Microsoft. Przy użyciu formatu pliku MSBuild, można opisać co elementów musi być skompilowany dla platformy i konfiguracji.

Można również uruchomić programu MSBuild w wierszu polecenia, a w tym temacie opisano tego podejścia. Przez ustawienie właściwości w wierszu polecenia, można utworzyć określonej konfiguracji projektu. Podobnie można także zdefiniować obiekty docelowe, które kompilacje programu MSBuild. Aby uzyskać więcej informacji na temat parametrów wiersza polecenia i MSBuild, zobacz [dotyczące wiersza polecenia programu MSBuild](https://msdn.microsoft.com/library/ms164311.aspx).

## <a name="msbuild-parameters"></a>Parametry MSBuild
Najprostszym sposobem, aby utworzyć pakiet jest uruchomienie programu MSBuild z `/t:Publish` opcji. Domyślnie to polecenie tworzy katalog w odniesieniu do folderu głównego dla projektu, takie jak `<ProjectDirectory>\bin\Configuration\app.publish\`. Podczas tworzenia projektu platformy Azure są generowane dwa pliki: pakiet plików sam i towarzyszący plik konfiguracji:

* Pakiet z pliku (`project.cspkg`)
* Plik konfiguracji (`ServiceConfiguration.TargetProfile.cscfg`)

Domyślnie każdy projekt Azure zawiera jeden plik konfiguracji usługi dla lokalne kompilacje (debugowanie) i drugi dla kompilacji chmury (tymczasowym czy produkcyjnym). Można jednak dodać lub usunąć pliki konfiguracji usługi, zgodnie z potrzebami. Podczas tworzenia pakietu w programie Visual Studio zapyta, które pliku konfiguracji usługi, aby uwzględnić obok pakietu. Podczas tworzenia pakietu przy użyciu programu MSBuild, domyślnie znajduje się lokalnego pliku konfiguracji usługi. Aby dołączyć inny plik konfiguracji usługi, należy ustawić `TargetProfile` właściwości polecenia programu MSBuild (`MSBuild /t:Publish /p:TargetProfile=ProfileName`).

Jeśli chcesz użyć innego katalogu pakietu przechowywane i pliki konfiguracji, należy ustawić ścieżki, za pomocą `/p:PublishDir=Directory\` opcji, w tym końcowe separatora ukośnik odwrotny.

## <a name="next-steps"></a>Kolejne kroki
Po utworzeniu pakietu należy go wdrożyć na platformie Azure.