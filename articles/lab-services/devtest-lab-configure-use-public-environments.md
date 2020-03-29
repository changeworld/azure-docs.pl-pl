---
title: Konfigurowanie i używanie środowisk publicznych w laboratoriach devtest platformy Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób konfigurowania i używania środowisk publicznych (szablony usługi Azure Resource Manager w repozytorium Git) w laboratoriach devtest platformy Azure.
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
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 127a6986e04cf90f69b2a8ec70b90b877e534708
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721697"
---
# <a name="configure-and-use-public-environments-in-azure-devtest-labs"></a>Konfigurowanie i używanie środowisk publicznych w laboratoriach devtest azure
Usługa Azure DevTest Labs ma [publiczne repozytorium szablonów usługi Azure Resource Manager,](https://github.com/Azure/azure-devtestlab/tree/master/Environments) których można używać do tworzenia środowisk bez konieczności samodzielnego łączenia się z zewnętrznym źródłem usługi GitHub. To repozytorium zawiera często używane szablony, takie jak usługi Azure Web Apps, klaster sieci szkieletowej usług i środowisko programu SharePoint Farm. Ta funkcja jest podobna do publicznego repozytorium artefaktów, który jest dołączony do każdego laboratorium, które tworzysz. Repozytorium środowiska umożliwia szybkie rozpoczęcie pracy z wstępnie utworzone szablony środowiska z minimalnymi parametrami wejściowymi, aby zapewnić bezproblemowe rozpoczęcie pracy dla zasobów PaaS w laboratoriach. 

## <a name="configuring-public-environments"></a>Konfigurowanie środowisk publicznych
Jako właściciel laboratorium możesz włączyć repozytorium środowiska publicznego dla laboratorium podczas tworzenia laboratorium. Aby włączyć środowiska publiczne dla laboratorium, wybierz **włącz** dla **środowiska publicznego** pola podczas tworzenia laboratorium. 

![Włączanie środowiska publicznego dla nowego laboratorium](media/devtest-lab-configure-use-public-environments/enable-public-environment-new-lab.png)


W istniejących laboratoriach repozytorium środowiska publicznego nie jest włączone. Ręcznie włącz go do korzystania z szablonów w repozytorium. W przypadku laboratoriów utworzonych przy użyciu szablonów Menedżera zasobów repozytorium jest domyślnie wyłączone.

Można włączyć/wyłączyć środowiska publiczne dla laboratorium, a także udostępnić tylko określone środowiska użytkownikom laboratorium, wykonując następujące kroki: 

1. Wybierz **konfigurację i zasady** dla swojego laboratorium. 
2. W sekcji **BAZY MASZYN WIRTUALNYCH** wybierz pozycję **Środowiska publiczne**.
3. Aby włączyć środowiska publiczne dla laboratorium, **wybierz**tak . W przeciwnym razie wybierz opcję **Nie**. 
4. Jeśli włączono środowiska publiczne, wszystkie środowiska w repozytorium są włączone domyślnie. Można odeszła, aby wybrać środowisko, aby nie było dostępne dla użytkowników laboratorium. 

![Strona środowiska publiczne](media/devtest-lab-configure-use-public-environments/public-environments-page.png)

## <a name="use-environment-templates-as-a-lab-user"></a>Używanie szablonów środowiska jako użytkownika laboratorium
Jako użytkownik laboratorium możesz utworzyć nowe środowisko z włączonej listy szablonów środowiska, po prostu wybierając **+Dodaj** z paska narzędzi na stronie laboratorium. Lista baz zawiera szablony środowisk publicznych włączone przez administratora laboratorium u góry listy.

![Szablony środowiska publicznego](media/devtest-lab-configure-use-public-environments/public-environment-templates.png)

## <a name="next-steps"></a>Następne kroki
To repozytorium jest repozytorium typu open source, które można przyczynić się do dodawania często używanych i pomocnych szablonów Menedżera zasobów własnych. Aby dodać, po prostu prześlij żądanie ściągnięcia w repozytorium.  
