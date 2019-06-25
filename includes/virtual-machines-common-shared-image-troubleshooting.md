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
ms.openlocfilehash: 1476830313296615591a69a2cadd04bcc56b22bc
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183168"
---
Jeśli napotkasz problemy podczas wykonywania jakichkolwiek operacji na galeriach obrazów udostępnionych, definicjach obrazów i wersjach obrazów, uruchom ponownie w trybie debugowania polecenie powodujące niepowodzenie. Tryb debugowania jest aktywowana przez przekazanie **— debugowania** przełączyć się z interfejsem wiersza polecenia i **-Debug** Przełącz przy użyciu programu PowerShell. Po zlokalizowaniu błąd, postępuj zgodnie z tym dokumentem, aby rozwiązać problemy z błędami.


## <a name="unable-to-create-a-shared-image-gallery"></a>Nie można utworzyć galerii udostępnionego obrazu

Możliwe przyczyny:

*Nazwa galerii jest nieprawidłowa.*

Dozwolone znaki dla nazwy galerii są wielkie i małe litery, cyfry, kropki i okresów. Nazwa galerii nie może zawierać łączników. Zmień nazwę galerii i spróbuj ponownie. 

*Nazwa galerii nie jest unikatowa w ramach Twojej subskrypcji.*

Wybierz inną nazwę galerii, a następnie spróbuj ponownie.


## <a name="unable-to-create-an-image-definition"></a>Nie można utworzyć definicję obrazu 

Możliwe przyczyny:

*Nazwa definicji obrazu jest nieprawidłowa.*

Dozwolone znaki definicję obrazu są wielkie i małe litery, cyfry, kropki, łączniki i kropki. Zmień nazwę definicji obraz i spróbuj ponownie.

*Właściwości wymagane do utworzenia definicję obrazu nie zostały wypełnione.*

Właściwości, takie jak nazwy, wydawcy, oferty, jednostki sku i typu systemu operacyjnego są obowiązkowe. Upewnij się, jeśli wszystkie właściwości są przekazywane.

Upewnij się, że **OSType**, Linux lub Windows definicji obrazu jest taki sam jak źródłowy zarządzany obraz, którego używasz, aby utworzyć wersję obrazu. 


## <a name="unable-to-create-an-image-version"></a>Nie można utworzyć wersję obrazu 

Możliwe przyczyny:

*Nazwa wersji obrazu jest nieprawidłowa.*

Dozwolone znaki wersję obrazu są liczby i kropki. Numery muszą należeć do zakresu 32-bitową liczbę całkowitą. Format: *MajorVersion.MinorVersion.Patch*. Zmień nazwę wersji obrazu i spróbuj ponownie.

*Nie można odnaleźć zarządzanego obrazu źródłowego, z której utworzono wersję obrazu.* 

Sprawdź, czy obraz źródłowy istnieje i znajduje się w tym samym regionie, co wersja obrazu.

*Nie ukończono obrazu zarządzanego aprowizowana.*

Upewnij się, stan inicjowania obsługi administracyjnej zarządzanych obrazu źródłowego jest **Powodzenie**.

*Na liście region docelowy nie ma w regionie źródłowym.*

Na liście region docelowy musi zawierać region źródła wersję obrazu. Upewnij się, że w regionie źródłowym zostały uwzględnione na liście regiony docelowe, w której chcesz platformy Azure, aby replikować używana wersja obrazu do.

*Replikacja do wszystkich regionów docelowych nie jest ukończona.*

Użyj **— rozwiń ma wartość** flagi, aby sprawdzić, jeśli replikacja do wszystkich regionów określone miejsce docelowe zostało ukończone. W przeciwnym razie oczekiwania na ukończenie zadania do 6 godzin. Jeśli nie powiedzie się, uruchom polecenie ponownie, aby utworzyć i replikować wersję obrazu. Jeśli istnieje wiele regionów docelowych, którą wersję obrazu jest replikowana na, należy rozważyć przeprowadzenie replikacji w fazach.

## <a name="unable-to-create-a-vm-or-a-scale-set"></a>Nie można utworzyć Maszynę wirtualną lub skalowania zestawu 

Możliwe przyczyny:

*Użytkownik próbujący Tworzenie zestawu skalowania maszyny Wirtualnej lub maszyny wirtualnej nie ma dostępu do odczytu do wersji obrazu.*

Skontaktuj się z właścicielem subskrypcji i poproś o przyznanie dostępu do odczytu do wersji obrazu lub nadrzędnego zasobów (takich jak Galeria obrazów udostępnionych lub definicję obrazu) za pośrednictwem [kontroli dostępu opartej na rolach](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles) (RBAC). 

*Nie można odnaleźć wersję obrazu.*

Sprawdź, czy regionu, o których chcesz utworzyć skalę maszyny Wirtualnej lub maszyny wirtualnej w jest dołączone do listy regiony docelowe wersji obrazu. Jeśli region jest już na liście regiony docelowe, sprawdź, jeśli zadanie replikacji zostało ukończone. Możesz użyć **ma — wartość** flagi, aby sprawdzić, jeśli replikacja do wszystkich regionów określone miejsce docelowe zostało ukończone. 

*Trwa tworzenie długo zestawu skalowania maszyny Wirtualnej lub maszyny wirtualnej.*

Upewnij się, że **OSType** obrazu wersji, który chcesz utworzyć maszynę Wirtualną lub zestawu skalowania maszyn wirtualnych z ma taką samą **OSType** źródła zarządzanego obrazu, który został użyty do utworzenia wersję obrazu. 

## <a name="unable-to-share-resources"></a>Nie można udostępnić zasoby

Udostępnianie galerii obrazów udostępnionych, definicję obrazu i obraz wersji zasobów między subskrypcjami jest włączane przy użyciu [kontroli dostępu opartej na rolach](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles) (RBAC). 

## <a name="replication-is-slow"></a>Replikacja przebiega powoli

Użyj **— rozwiń ma wartość** flagi, aby sprawdzić, jeśli replikacja do wszystkich regionów określone miejsce docelowe zostało ukończone. Jeśli nie, poczekaj na ukończenie zadania do 6 godzin. Jeśli nie powiedzie się, należy uruchomić polecenie ponownie, aby utworzyć i replikować wersję obrazu. Jeśli istnieje wiele regionów docelowych, którą wersję obrazu jest replikowana na, należy rozważyć przeprowadzenie replikacji w fazach.

## <a name="azure-limits-and-quotas"></a>Limity przydziału i ograniczenia platformy Azure 

[Usługa Azure limity przydziału i ograniczenia](https://docs.microsoft.com/azure/azure-subscription-service-limits) mają zastosowanie do galerii obrazów udostępnionych, definicję obrazu i zasoby wersji obrazu. Upewnij się, że znajdujesz się w ramach subskrypcji. 



