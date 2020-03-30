---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 40ba5a935e78cd75c4fcd7729e44f1cdf6c2859b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75772994"
---
Jeśli napotkasz problemy podczas wykonywania jakichkolwiek operacji na galeriach obrazów udostępnionych, definicjach obrazów i wersjach obrazów, uruchom ponownie w trybie debugowania polecenie powodujące niepowodzenie. Tryb debugowania jest aktywowany przez przekazanie przełącznika **-debug** z cli i **przełącznika -Debug** z programem PowerShell. Po zlokalizowaniu błędu postępuj zgodnie z tym dokumentem, aby rozwiązać problemy z błędami.


## <a name="unable-to-create-a-shared-image-gallery"></a>Nie można utworzyć galerii obrazów udostępnionych

Możliwe przyczyny:

*Nazwa galerii jest nieprawidłowa.*

Dozwolone znaki nazwy galerii to wielkie lub małe litery, cyfry, kropki i kropki. Nazwa galerii nie może zawierać kresek. Zmień nazwę galerii i spróbuj ponownie. 

*Nazwa galerii nie jest unikatowa w ramach subskrypcji.*

Wybierz inną nazwę galerii i spróbuj ponownie.


## <a name="unable-to-create-an-image-definition"></a>Nie można utworzyć definicji obrazu 

Możliwe przyczyny:

*nazwa definicji obrazu jest nieprawidłowa.*

Dozwolone znaki definicji obrazu to wielkie lub małe litery, cyfry, kropki, kreski i kropki. Zmień nazwę definicji obrazu i spróbuj ponownie.

*Właściwości obowiązkowe do tworzenia definicji obrazu nie są wypełniane.*

Właściwości, takie jak nazwa, wydawca, oferta, sku i typ systemu operacyjnego są obowiązkowe. Sprawdź, czy wszystkie właściwości są przekazywane.

Upewnij się, że **system operacyjny**, Linux lub Windows, definicji obrazu jest taki sam jak źródło zarządzanego obrazu, który jest używany do tworzenia wersji obrazu. 


## <a name="unable-to-create-an-image-version"></a>Nie można utworzyć wersji obrazu 

Możliwe przyczyny:

*Nazwa wersji obrazu jest nieprawidłowa.*

Dozwolone znaki dla wersji obrazu to liczby i kropki. Liczby muszą mieszczeć się w zakresie 32-bitowej liczby całkowitej. Format: *MajorVersion.MinorVersion.Patch*. Zmień nazwę wersji obrazu i spróbuj ponownie.

*Nie można odnaleźć obrazu zarządzanego ze źródła, z którego tworzona jest wersja obrazu.* 

Sprawdź, czy obraz źródłowy istnieje i znajduje się w tym samym regionie co wersja obrazu.

*Obraz zarządzany nie jest wykonywane aprowizowana.*

Upewnij się, że stan inicjowania obsługi administracyjnej obrazu zarządzanego źródła to **Powodzenie**.

*Lista regionów docelowych nie zawiera regionu źródłowego.*

Lista regionów docelowych musi zawierać region źródłowy wersji obrazu. Upewnij się, że region źródłowy został uwzględniony na liście regionów docelowych, w których chcesz, aby platforma Azure replikować wersję obrazu.

*Replikacja do wszystkich regionów docelowych nie została ukończona.*

Flaga **--expand ReplicationStatus** służy do sprawdzania, czy replikacja do wszystkich określonych regionów docelowych została ukończona. Jeśli nie, poczekaj do 6 godzin na zakończenie zadania. Jeśli to się nie powiedzie, uruchom polecenie ponownie, aby utworzyć i zreplikować wersję obrazu. Jeśli istnieje wiele regionów docelowych, do których jest replikowana wersja obrazu, należy rozważyć wykonanie replikacji w fazach.

## <a name="unable-to-create-a-vm-or-a-scale-set"></a>Nie można utworzyć maszyny wirtualnej lub zestawu skalowania 

Możliwe przyczyny:

*Użytkownik próbujący utworzyć zestaw skalowania maszyny wirtualnej lub maszyny wirtualnej nie ma dostępu do odczytu do wersji obrazu.*

Skontaktuj się z właścicielem subskrypcji i poproś go o udzielenie dostępu do odczytu wersji obrazu lub zasobów nadrzędnych (takich jak galeria obrazów udostępnionych lub definicja obrazu) za pośrednictwem [kontroli dostępu opartej na rolach](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles) (RBAC). 

*Nie znaleziono wersji obrazu.*

Sprawdź, czy region, w którego próbujesz utworzyć skalę maszyny wirtualnej lub maszyny wirtualnej, znajduje się na liście regionów docelowych wersji obrazu. Jeśli region znajduje się już na liście regionów docelowych, sprawdź, czy zadanie replikacji zostało ukończone. Flagę **-ReplicationStatus** można użyć, aby sprawdzić, czy replikacja do wszystkich określonych regionów docelowych została ukończona. 

*Tworzenie zestawu skalowania maszyny wirtualnej lub maszyny wirtualnej zajmuje dużo czasu.*

Sprawdź, czy **typ systemu operacyjnego** wersji obrazu, z którego próbujesz utworzyć zestaw skalowania maszyny wirtualnej lub maszyny wirtualnej, ma ten sam **typ systemu operacyjnego** źródłowego obrazu zarządzanego, który został użyty do utworzenia wersji obrazu. 

## <a name="unable-to-share-resources"></a>Nie można udostępnić zasobów

Udostępnianie udostępnionych galerii obrazów, definicji obrazu i zasobów wersji obrazu w ramach subskrypcji jest włączone przy użyciu [kontroli dostępu opartej na rolach](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles) (RBAC). 

## <a name="replication-is-slow"></a>Replikacja jest powolna

Flaga **--expand ReplicationStatus** służy do sprawdzania, czy replikacja do wszystkich określonych regionów docelowych została ukończona. Jeśli nie, poczekaj do 6 godzin na zakończenie zadania. Jeśli to się nie powiedzie, wyzwolić polecenie ponownie, aby utworzyć i zreplikować wersję obrazu. Jeśli istnieje wiele regionów docelowych, do których jest replikowana wersja obrazu, należy rozważyć wykonanie replikacji w fazach.

## <a name="azure-limits-and-quotas"></a>Limity przydziału i ograniczenia platformy Azure 

[Limity i przydziały platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) mają zastosowanie do wszystkich udostępnionych zasobów galerii obrazów, definicji obrazu i wersji obrazu. Upewnij się, że mieścisz się w granicach swoich subskrypcji. 



