---
title: Konfigurowanie projektu usługi w chmurze Azure z programem Visual Studio | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować projekt usługi w chmurze platformy Azure w programie Visual Studio, w zależności od wymagań dla tego projektu.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 609d6965-05cc-47b1-82dc-c76a92d4f295
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 03/06/2017
ms.author: ghogen
ms.openlocfilehash: 0b2b39897407875d2722c7fbaebebc1698f931a9
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="configure-an-azure-cloud-service-project-with-visual-studio"></a>Konfigurowanie projektu usługi w chmurze platformy Azure przy użyciu programu Visual Studio
Projekt usługi w chmurze platformy Azure, można skonfigurować w zależności od wymagań dla tego projektu. Można ustawić właściwości projektu w ramach następujących kategorii:

- **Publikowanie usługi w chmurze na platformie Azure** — można ustawić właściwości, aby upewnić się, że istniejącą usługę w chmurze wdrożeniu na platformie Azure nie jest przypadkowo usunięty.
- **Uruchomić ani debugować usługi w chmurze na komputerze lokalnym** — można wybierać z konfiguracją usługi i wskazuje, czy można uruchomić emulatora magazynu Azure.
- **Sprawdzanie poprawności pakietu usług chmury po utworzeniu** — można wybrać Traktuj ostrzeżenia jako błędy, dzięki czemu można zapewnić, że pakiet usługi w chmurze wdraża się bez żadnych problemów. 

## <a name="steps-to-configure-an-azure-cloud-service-project"></a>Kroki, aby skonfigurować projekt usługi w chmurze Azure
1. Otwarcia lub utworzenia projektu usługi w chmurze w programie Visual Studio

1. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy projekt i wybierz z menu kontekstowego **właściwości**.
   
1. Na stronie właściwości projektu, zaznacz **programowanie** kartę.

    ![Menu Właściwości projektu](./media/vs-azure-tools-configuring-an-azure-project/solution-explorer-project-properties-menu.png)

1. Ustaw **Monituj przed usunięciem istniejącego wdrożenia** do **True**. To ustawienie pozwala upewnić się, że nie został przypadkowo usunięty istniejącego wdrożenia na platformie Azure

1. Wybierz żądaną **konfiguracji usługi** wskaż konfigurację usługi, która ma być używany podczas uruchamiania lub debugowania usługi w chmurze lokalnie. Aby uzyskać więcej informacji na temat sposobu modyfikowania konfiguracji usługi roli, zobacz [sposobu konfigurowania ról dla usługi w chmurze Azure z programem Visual Studio](./vs-azure-tools-configure-roles-for-cloud-service.md).

1. Ustaw **emulatora magazynu Start Azure** do **True** można uruchomić emulatora magazynu Azure, podczas uruchamiania lub debugowania usługi w chmurze lokalnie.

1. Ustaw **Traktuj ostrzeżenia jako błędy** do **True** się upewnić, że nie można opublikować, jeśli występują błędy sprawdzania poprawności pakietu.

1. Ustaw **korzystania z portów projektu sieci web** do **True** aby upewnić się, że roli sieci web używa tego samego portu każdym uruchomieniu go lokalnie w usługach IIS Express.

1. Na pasku narzędzi programu Visual Studio wybierz **zapisać**.

## <a name="next-steps"></a>Kolejne kroki
- [Konfigurowanie projektu platformy Azure przy użyciu wielu konfiguracji usługi](vs-azure-tools-multiple-services-project-configurations.md)

