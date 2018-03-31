---
title: Zarządzanie rolami w usług w chmurze Azure z programem Visual Studio | Dokumentacja firmy Microsoft
description: Informacje o sposobie dodawania i usuwania ról w usług w chmurze Azure z programem Visual Studio.
services: visual-studio-online
documentationcenter: na
author: ghogen
manager: douge
editor: ''
ms.assetid: 5ec9ae2e-8579-4e5d-999e-8ae05b629bd1
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 03/21/2017
ms.author: ghogen
ms.openlocfilehash: fb49eaa69f2265f6b312ecbdab1435e7c50236b8
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="managing-roles-in-azure-cloud-services-with-visual-studio"></a>Zarządzanie rolami w usług w chmurze Azure z programem Visual Studio
Po utworzeniu usługi w chmurze platformy Azure, można dodać do niego nowych ról lub usuwać istniejących ról. Możesz także zaimportować istniejący projekt i przekształcić ją do roli. Można na przykład zaimportować aplikację sieci web platformy ASP.NET i wyznaczanie roli sieci web.

## <a name="adding-a-role-to-an-azure-cloud-service"></a>Dodawanie roli do usługi w chmurze Azure
Poniższe kroki prowadzące przez Dodawanie roli sieci web lub procesu roboczego do projektu usługi w chmurze platformy Azure w programie Visual Studio.

1. Utwórz lub Otwórz projekt usługi w chmurze platformy Azure w programie Visual Studio.

1. W **Eksploratora rozwiązań**, rozwiń węzeł projektu

1. Kliknij prawym przyciskiem myszy **ról** węzeł, aby wyświetlić menu kontekstowe. Wybierz z menu kontekstowego **Dodaj**, następnie wybierz istniejącą rolę sieci web lub roli proces roboczy z bieżącego rozwiązania lub Utwórz projekt roli sieci web lub procesu roboczego. Można również wybrać odpowiedni projekt, takich jak projekt aplikacji sieci web platformy ASP.NET i skojarzyć go z projektu roli.

    ![Opcje menu, aby dodać rolę do projektu usługi w chmurze Azure](media/vs-azure-tools-cloud-service-project-managing-roles/add-role.png)

## <a name="removing-a-role-from-an-azure-cloud-service"></a>Usunięcie roli z usługi w chmurze Azure
Poniższe kroki prowadzące przez usunięcie roli sieci web lub procesu roboczego z projektu usługi w chmurze platformy Azure w programie Visual Studio.

1. Utwórz lub Otwórz projekt usługi w chmurze platformy Azure w programie Visual Studio.

1. W **Eksploratora rozwiązań**, rozwiń węzeł projektu

1. Rozwiń węzeł **ról** węzła.

1. Kliknij prawym przyciskiem myszy węzeł, który chcesz usunąć, a z menu kontekstowego wybierz **Usuń**. 

    ![Opcje menu, aby dodać rolę do usługi w chmurze Azure](media/vs-azure-tools-cloud-service-project-managing-roles/remove-role.png)

## <a name="readding-a-role-to-an-azure-cloud-service-project"></a>Ponowne Dodawanie roli do projektu usługi w chmurze Azure
Jeśli usunąć rolę z projektu usługi w chmurze, ale później zdecyduje się Dodaj rolę projektu, tylko deklaracji roli i Podstawowe atrybuty, takie jak punkty końcowe i informacje diagnostyczne, zostaną dodane. Nie dodatkowe zasoby lub odwołania zostały dodane do `ServiceDefinition.csdef` pliku lub `ServiceConfiguration.cscfg` pliku. Jeśli chcesz dodać te informacje, należy ręcznie dodać do tych plików.

Na przykład można usunąć roli usługi sieci web, a później zdecydujesz się dodać tej roli do rozwiązania. Jeśli to zrobisz, występuje błąd. Aby uniknąć tego błędu, należy dodać `<LocalResources>` pokazano w poniższych XML do elementu `ServiceDefinition.csdef` pliku. Użyj nazwy roli usługi sieci web, który został dodany do projektu jako część atrybutu nazwy dla **<LocalStorage>** elementu. W tym przykładzie nazwa roli usługi sieci web jest **WCFServiceWebRole1**.

    <WebRole name="WCFServiceWebRole1">
        <Sites>
          <Site name="Web">
            <Bindings>
              <Binding name="Endpoint1" endpointName="Endpoint1" />
            </Bindings>
          </Site>
        </Sites>
        <Endpoints>
          <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <Imports>
          <Import moduleName="Diagnostics" />
        </Imports>
       <LocalResources>
          <LocalStorage name="WCFServiceWebRole1.svclog" sizeInMB="1000" cleanOnRoleRecycle="false" />
       </LocalResources>
    </WebRole>

## <a name="next-steps"></a>Kolejne kroki
- [Konfigurowanie ról dla usługi w chmurze Azure z programem Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md)
