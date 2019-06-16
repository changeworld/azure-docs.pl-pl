---
title: Konfigurowanie i używanie środowiskach publicznych w usłudze Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Informacje o sposobie konfigurowania i używania środowiskach publicznych w usłudze Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/13/2018
ms.author: spelluru
ms.openlocfilehash: 2cd6998c7ac11638ead67fde384bdf4599692781
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64706032"
---
# <a name="configure-and-use-public-environments-in-azure-devtest-labs"></a>Konfigurowanie i używanie środowiskach publicznych w usłudze Azure DevTest Labs
Usługa Azure DevTest Labs ma [publicznego repozytorium szablonów usługi Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/Environments) używanego do tworzenia środowisk bez konieczności nawiązywania połączenia z zewnętrznym źródłem GitHub samodzielnie. To repozytorium zawiera często używane szablony, takich jak Azure Web Apps, klaster usługi Service Fabric i Programowanie w środowisku farmy programu SharePoint. Ta funkcja jest podobne do publicznego repozytorium artefaktów, które jest uwzględniany w przypadku każdego tworzenia laboratorium. Repozytorium środowiska umożliwia szybkie rozpoczynanie pracy z szablonami wstępnie przygotowane środowiska minimalne parametrów wejściowych do przedstawienia bezproblemowe środowisko dla zasobów PaaS w labs. 

## <a name="configuring-public-environments"></a>Konfigurowanie środowisk publiczne
Jako właściciel laboratorium aby umożliwić repozytorium publicznego środowiska dla swojego laboratorium podczas tworzenia laboratorium. Aby włączyć publiczny środowisk na potrzeby środowiska laboratoryjnego, zaznacz **na** dla **środowiskach publicznych** pola podczas tworzenia laboratorium. 

![Włącz środowisko publicznego dla nowego laboratorium](media/devtest-lab-configure-use-public-environments/enable-public-environment-new-lab.png)


W laboratoriach istniejącego repozytorium publicznego środowiska nie jest włączona. Ręcznie włączyć ją za pomocą szablonów w repozytorium. W laboratoriach utworzone za pomocą szablonów usługi Resource Manager repozytorium jest domyślnie wyłączona, a także.

Można włączyć/wyłączyć środowiskach publicznych dla swojego laboratorium i również udostępnić tylko konkretnych środowisk użytkowników laboratorium wykonując następujące kroki: 

1. Wybierz **konfiguracji i zasad** dla swojego laboratorium. 
2. W **BAZAMI maszyn wirtualnych** zaznacz **środowiskach publicznych**.
3. Aby włączyć środowiskach publicznych dla laboratorium, zaznacz **tak**. W przeciwnym razie wybierz **nie**. 
4. Jeśli włączono środowiskach publicznych wszystkich środowisk w repozytorium są włączone, wartości domyślne. Usuń zaznaczenie pola można wybrać środowisko, aby stał się niedostępny użytkownikom laboratorium. 

![Strona w środowiskach publicznych](media/devtest-lab-configure-use-public-environments/public-environments-page.png)

## <a name="use-environment-templates-as-a-lab-user"></a>Użyj szablonów środowiska jako użytkownik laboratorium
Jako użytkownik laboratorium, można utworzyć nowe środowisko z listy włączonych szablonów środowiska, po prostu wybierając **+ Dodaj** z paska narzędzi na stronie laboratorium. Listy klas podstawowych zawiera szablony środowiskach publicznych, włączony przez administratora laboratorium w górnej części listy.

![Szablonów środowiska publiczne](media/devtest-lab-configure-use-public-environments/public-environment-templates.png)

## <a name="next-steps"></a>Kolejne kroki
To repozytorium jest repozytorium typu open source, który możesz współtworzyć, aby dodać często używane i przydatne szablonów usługi Resource Manager własne. Aby współtworzyć, po prostu przesłać żądanie ściągnięcia względem repozytorium.  
