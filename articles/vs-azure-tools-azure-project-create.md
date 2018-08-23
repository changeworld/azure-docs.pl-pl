---
title: Tworzenie projektu usługi w chmurze platformy Azure za pomocą programu Visual Studio | Dokumentacja firmy Microsoft
description: Dowiedz się teraz, jak utworzyć projekt usługi w chmurze platformy Azure z programem Visual Studio
services: visual-studio-online
author: ghogen
manager: douge
assetId: ec580df7-3dcc-45a9-a1d9-8c110678dfb5
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/21/2017
ms.author: ghogen
ms.openlocfilehash: f41628c29f40443cc213892a0cc457eda110ebca
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/18/2018
ms.locfileid: "42057372"
---
# <a name="creating-an-azure-cloud-service-project-with-visual-studio"></a>Tworzenie projektu usługi w chmurze platformy Azure za pomocą programu Visual Studio
Azure Tools dla programu Visual Studio zawiera szablon projektu umożliwiający tworzenie usługi w chmurze platformy Azure. Po utworzeniu projektu programu Visual Studio pozwala konfigurować, debugować i wdrożyć usługę w chmurze na platformie Azure.

## <a name="steps-to-create-an-azure-cloud-service-project-in-visual-studio"></a>Kroki, aby utworzyć projekt usługi w chmurze platformy Azure w programie Visual Studio
Ta sekcja przeprowadzi Cię przez tworzenie projektu usługi w chmurze platformy Azure w programie Visual Studio za pomocą co najmniej jedną rolę sieci web.  

1. Uruchom program Visual Studio jako administrator.

1. W menu głównym wybierz **pliku** > **New** > **projektu**.

1. Wybierz **chmury** z Visual C# lub Visual Basic projektu węzłów szablonu, a następnie wybierz **usługa w chmurze** z listy szablonów.

    ![Nowa usługa w chmurze platformy Azure](./media/vs-azure-tools-azure-project-create/new-project-wizard-for-cloud-service.png)

1. Określenie, która wersja programu .NET Framework, którego chcesz użyć do tworzenia projektu.

1. Wprowadź nazwę i lokalizację projektu i nazwę rozwiązania. 

1. Kliknij przycisk **OK**.

1. W **nową usługę w chmurze Azure Microsoft** okno dialogowe, wybierz role, które chcesz dodać, a następnie wybierz przycisk strzałki w prawo, aby dodać je do swojego rozwiązania.

    ![Wybierz nowy ról usługi w chmurze platformy Azure](./media/vs-azure-tools-azure-project-create/new-cloud-service.png)

1. Aby zmienić nazwę roli, które zostały dodane, umieść kursor na rolę w **nową usługę w chmurze Azure Microsoft** okno dialogowe i z menu kontekstowego wybierz **Zmień nazwę**. Można również zmienić nazwę roli w ramach rozwiązania usługi (w **Eksploratora rozwiązań**) po został dodany.

    ![Zmień nazwę roli usługi w chmurze platformy Azure](./media/vs-azure-tools-azure-project-create/new-cloud-service-rename.png)

Projekt programu Visual Studio na platformie Azure ma skojarzenia z projektów ról w rozwiązaniu. Zawiera także projekt *pliku definicji usługi* i *pliku konfiguracji usługi*:

- **Plik definicji usługi** — definiuje ustawienia środowiska uruchomieniowego dla aplikacji, w tym, jakie role są wymagane, punkty końcowe i rozmiar maszyny wirtualnej. 
- **Plik konfiguracji usługi** -Określa, ile wystąpień roli są wykonywania i wartości ustawienia zdefiniowane dla roli. 

Aby uzyskać więcej informacji o tych plikach, zobacz [konfigurowania ról usługi w chmurze platformy Azure z programem Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).

## <a name="next-steps"></a>Kolejne kroki
- [Zarządzanie rolami w projekty usługi w chmurze platformy Azure z programem Visual Studio](./vs-azure-tools-cloud-service-project-managing-roles.md)
