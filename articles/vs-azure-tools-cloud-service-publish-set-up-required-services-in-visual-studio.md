---
title: Przygotowanie do publikowania lub wdrażania usługi w chmurze w programie Visual Studio | Dokumentacja firmy Microsoft
description: Dowiedz się procedury konfigurowania chmur i konto usługi magazynu i konfigurowania aplikacji platformy Azure.
services: visual-studio-online
documentationcenter: na
author: ghogen
manager: douge
editor: ''
ms.assetid: 92ee2f9e-ec49-4c7a-900d-620abe5e9d8a
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/10/2017
ms.author: ghogen
ms.openlocfilehash: 8a7d6f114bfa10170cdfe7126e01a35b02affd20
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="prepare-to-publish-or-deploy-a-cloud-service-from-visual-studio"></a>Przygotowanie do publikowania lub wdrażania usługi w chmurze w programie Visual Studio

Aby opublikować projekt usługi w chmurze, należy skonfigurować następujące usługi zgodnie z opisem w tym artykule:

* A **usługi w chmurze** do uruchamiania w środowisku platformy Azure, ról i 
* A **konta magazynu** , który zapewnia dostęp do usług obiektów Blob, kolejki i tabeli.

## <a name="create-a-cloud-service"></a>Tworzenie usługi w chmurze

Usługi w chmurze uruchamia ról w środowisku platformy Azure. Można utworzyć usługi w chmurze w programie Visual Studio lub za pomocą [portalu Azure](https://portal.azure.com/) zgodnie z opisem w poniższych sekcjach.

### <a name="create-a-cloud-service-from-visual-studio"></a>Tworzenie usługi w chmurze w programie Visual Studio

1. Z wcześniej utworzonej projektu usługi w chmurze, kliknij prawym przyciskiem myszy projekt wybierz **publikowania**.
1. Jeśli to konieczne, zaloguj się przy użyciu Microsoft lub konta organizacyjnego skojarzone z subskrypcją platformy Azure, a następnie wybierz **dalej** aby przejść do **ustawienia** strony.
1. A **Tworzenie usługi w chmurze i konto magazynu** zostanie wyświetlone okno dialogowe (Jeśli nie, wybierz **Utwórz nowy** z **usługi w chmurze** listy).
1. Wprowadź nazwę bez uwzględniania wielkości liter dla usługi w chmurze, który stanowi część adresu URL i musi być unikatowa. Również wybrać Region lub grupę koligacji, a następnie wybierz opcję replikacji.

### <a name="create-a-cloud-service-through-the-azure-portal"></a>Tworzenie usługi w chmurze za pośrednictwem portalu Azure

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Wybierz **usługi w chmurze (klasyczne)** w lewej części strony.
1. Wybierz **+ Dodaj**, następnie podaj wymagane informacje (nazwa DNS, subskrypcji, grupy zasobów i lokalizacja). Nie jest konieczne w celu przekazania pakietu w tym momencie, ponieważ można to zrobić później w programie Visual Studio.
1. Wybierz **Utwórz** aby ukończyć proces.

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Konto magazynu zapewnia dostęp do usług obiektów Blob, kolejki i tabeli. Można utworzyć konta magazynu za pomocą programu Visual Studio lub [portalu Azure](https://portal.azure.com/).

### <a name="create-a-storage-account-from-visual-studio"></a>Utwórz konto magazynu w programie Visual Studio

1. W **Eksploratora rozwiązań** z wcześniej utworzonej projektu usługi w chmurze, zlokalizuj **usług połączonych** węzła w projekcie roli, kliknij prawym przyciskiem myszy i wybierz **dodać podłączonej usługi**. (W programie Visual Studio 2015, kliknij prawym przyciskiem myszy **magazynu** a następnie wybierz węzeł **Utwórz konto magazynu**.)
1. W **usług połączonych** listy, wybierz **magazynu w chmurze z usługą Azure Storage**.
1. W oknie dialogowym usługi Azure Storage, który pojawia się, wybierz **+ Utwórz nowe konto magazynu**, który wyświetla okno dialogowe z określonymi subskrypcji, nazwę fo konta, cenową warstwy, grupy zasobów i lokalizacji.
1. Wybierz **Utwórz** po zakończeniu. Nowe konto magazynu zostanie wyświetlony na liście kont magazynu dostępne w ramach subskrypcji.
1. Wybierz konto, a następnie zaznacz **Dodaj**.

### <a name="create-a-storage-account-through-the-azure-portal"></a>Tworzenie konta magazynu za pośrednictwem portalu Azure

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Wybierz **+ nowy** w lewej górnej części.
1. Wybierz **magazynu** w obszarze "Azure Marketplace," następnie **konta magazynu — obiekt blob, plików, tabeli, kolejki** z prawej strony.
1. Podaj wymagane informacje (nazwa, model wdrażania i tak dalej.
1. Wybierz **Utwórz** aby ukończyć proces.

## <a name="configure-your-app-to-use-the-storage-account"></a>Skonfiguruj aplikację, aby użyć konta magazynu

Po utworzeniu konta magazynu, nawiązując z nią połączenie za pomocą programu Visual Studio automatycznie aktualizuje konfiguracje usług dla projektu, w tym adresy URL i klucze dostępu.

Jeśli usługa w chmurze utworzony przy użyciu programu Visual Studio przy użyciu **dodać podłączonej usługi**, połączenia można sprawdzić, otwierając `ServiceConfiguration.Cloud.cscfg` i `ServiceConfiguration.Local.cscfg`.

Jeśli utworzono usługi w chmurze za pośrednictwem portalu Azure, wykonaj te same czynności w [Utwórz konto magazynu w programie Visual Studio](#create-a-storage-account-from-visual-studio) , ale wybierz istniejące konto, zamiast tworzenia nowej. Visual Studio następnie aktualizuje konfigurację za Ciebie.

Aby skonfigurować ustawienia ręcznie, użyj stron właściwości w programie Visual Studio dla odpowiednich roli w projekcie usługi w chmurze (kliknij prawym przyciskiem myszy rolę i wybierz **właściwości**). Aby uzyskać więcej informacji, zobacz [Konfigurowanie parametry połączenia z kontem magazynu](https://docs.microsoft.com/azure/vs-azure-tools-multiple-services-project-configurations#configuring-a-connection-string-to-a-storage-account).

### <a name="about-access-keys"></a>Klawisze dostępu — informacje

Portalu Azure zawiera adresy URL, w której można uzyskać dostęp do zasobów w każdej z usług Azure storage, a także klucze podstawowe i pomocnicze dostępu dla konta. Te klucze służy do uwierzytelniania żądań wysyłanych z usług magazynu.

Pomocniczy klucz dostępu zawiera takie same prawa dostępu do konta magazynu jako podstawowy klucz dostępu i jest generowany na nazwę kopii zapasowej klucza podstawowego dostępu zagrożenia bezpieczeństwa. Ponadto zaleca się ponowne generowanie kluczy dostępu na bieżąco. Można zmodyfikować ustawienie parametrów połączenia, aby używać klucza pomocniczego podczas ponownego generowania klucza podstawowego, a następnie zmodyfikować, aby użyć wygenerowano ponownie klucz podstawowy, podczas ponownego generowania klucza pomocniczego.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat publikowania aplikacji na platformie Azure w programie Visual Studio, zobacz [publikowania usługi w chmurze przy użyciu narzędzia Azure](vs-azure-tools-publishing-a-cloud-service.md).
