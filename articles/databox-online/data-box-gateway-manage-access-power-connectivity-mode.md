---
title: Dostęp do urządzenia usługi Azure Data Box Gateway, tryb zasilania i łączności
description: W tym artykule opisano sposób zarządzania trybem dostępu, zasilania i łączności dla urządzenia usługi Azure Data Box Gateway, które ułatwia przesyłanie danych na platformę Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: e4d85bd460c39964c9f42ac946e3522f5f129c1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474445"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-gateway"></a>Zarządzanie trybem dostępu, zasilania i łączności bramy usługi Azure Data Box Gateway

W tym artykule opisano sposób zarządzania trybem dostępu, zasilania i łączności bramy usługi Azure Data Box Gateway. Te operacje są wykonywane za pośrednictwem lokalnego interfejsu użytkownika sieci web lub witryny Azure portal.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Zarządzanie dostępem do urządzenia
> * Zarządzanie trybem łączności
> * Zarządzanie zasilaniem

## <a name="manage-device-access"></a>Zarządzanie dostępem do urządzenia

Dostęp do urządzenia Data Box Gateway jest kontrolowany przez użycie hasła urządzenia. Hasło można zmienić za pośrednictwem lokalnego interfejsu użytkownika sieci Web. Hasło urządzenia można również zresetować w witrynie Azure portal.

### <a name="change-device-password"></a>Zmienianie hasła urządzenia

Wykonaj następujące kroki w lokalnym interfejsie użytkownika, aby zmienić hasło urządzenia.

1. W lokalnym interfejsie użytkownika sieci Web przejdź do **ustawienia konserwacji > zmiana hasła**.
2. Wprowadź bieżące hasło, a następnie nowe hasło. Podane hasło musi zawierać od 8 do 16 znaków. Hasło musi zawierać 3 z następujących znaków: wielkie, małe, numeryczne i znaki specjalne. Potwierdź nowe hasło.

    ![Zmień hasło](media/data-box-gateway-manage-access-power-connectivity-mode/change-password-1.png)

3. Kliknij **pozycję Zmień hasło**.
 
### <a name="reset-device-password"></a>Resetowanie hasła urządzenia

Przepływ pracy resetowania nie wymaga od użytkownika odwołania starego hasła i jest przydatny w przypadku utraty hasła. Ten przepływ pracy jest wykonywany w witrynie Azure portal.

1. W witrynie Azure portal przejdź do **pozycji Przegląd > Resetowanie hasła administratora**.

    ![Resetowanie hasła](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-1.png)

 
2. Wprowadź nowe hasło, a następnie potwierdź. Podane hasło musi zawierać od 8 do 16 znaków. Hasło musi zawierać 3 z następujących znaków: wielkie, małe, numeryczne i znaki specjalne. Kliknij **przycisk Resetuj**.

    ![Resetowanie hasła](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>Zarządzenie dostępem do zasobów

Aby utworzyć bramę usługi Data Box Edge/Data Box Gateway, IoT Hub i zasób usługi Azure Storage, potrzebujesz uprawnień jako współautor lub wyższy na poziomie grupy zasobów. Należy również odpowiednich dostawców zasobów, które mają być zarejestrowane. W przypadku wszystkich operacji, które obejmują klucz aktywacji i poświadczenia, wymagane są również uprawnienia do interfejsu API wykresu usługi Azure Active Directory. Są one opisane w poniższych sekcjach.

### <a name="manage-microsoft-graph-api-permissions"></a>Zarządzanie uprawnieniami interfejsu API programu Microsoft Graph

Podczas generowania klucza aktywacji dla urządzenia Data Box Edge lub wykonywania operacji, które wymagają poświadczeń, potrzebujesz uprawnień do interfejsu API programu Microsoft Graph. Operacje, które wymagają poświadczeń może być:

-  Tworzenie udziału ze skojarzonym kontem magazynu.
-  Tworzenie użytkownika, który może uzyskać dostęp do udziałów na urządzeniu.

Powinien mieć `User` dostęp do dzierżawy usługi Active Directory, ponieważ trzeba być w stanie `Read all directory objects`. Nie możesz być użytkownikiem-gościem, ponieważ nie `Read all directory objects`ma uprawnień do . Jeśli jesteś gościem, niektóre operacje, takie jak generowanie klucza aktywacji, tworzenie udziału na urządzeniu Data Box Edge i tworzenie użytkownika, zakończą się niepowodzeniem.

Aby uzyskać więcej informacji na temat zapewniania użytkownikom dostępu do interfejsu API programu Microsoft Graph, zobacz [Odwołanie do uprawnień programu Microsoft Graph](https://docs.microsoft.com/graph/permissions-reference).

### <a name="register-resource-providers"></a>Rejestrowanie dostawców zasobów

Aby aprowizować zasób na platformie Azure (w modelu usługi Azure Resource Manager), potrzebujesz dostawcy zasobów, który obsługuje tworzenie tego zasobu. Na przykład, aby aprowizować maszynę wirtualną, powinien mieć dostawca zasobów "Microsoft.Compute" dostępny w ramach subskrypcji.
 
Dostawcy zasobów są rejestrowani na poziomie subskrypcji. Domyślnie każda nowa subskrypcja platformy Azure ma wstępnie zarejestrowaną listę najczęściej stosowanych dostawców zasobów. Dostawca zasobów dla firmy Microsoft.DataBoxEdge nie znajduje się na tej liście.

Nie musisz udzielać uprawnień dostępu do poziomu subskrypcji, aby użytkownicy mogli tworzyć zasoby, takie jak "Microsoft.DataBoxEdge" w swoich grupach zasobów, na których mają prawa właściciela, o ile dostawcy zasobów dla tych zasobów są już Zarejestrowany.

Przed podjęciem próby utworzenia dowolnego zasobu upewnij się, że dostawca zasobów jest zarejestrowany w subskrypcji. Jeśli dostawca zasobów nie jest zarejestrowany, należy upewnić się, że użytkownik tworzący nowy zasób ma wystarczająco dużo praw, aby zarejestrować wymaganego dostawcy zasobów na poziomie subskrypcji. Jeśli nie zrobiłeś tego, a następnie zobaczysz następujący błąd:

*Nazwa \<subskrypcji> nie ma uprawnień do rejestrowania dostawców zasobów: Microsoft.DataBoxEdge.*


Aby uzyskać listę zarejestrowanych dostawców zasobów w bieżącej subskrypcji, uruchom następujące polecenie:

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

W przypadku urządzenia `Microsoft.DataBoxEdge` Data Box Edge należy zarejestrować. Aby `Microsoft.DataBoxEdge`się zarejestrować, administrator subskrypcji powinien uruchomić następujące polecenie:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

Aby uzyskać więcej informacji na temat rejestrowania dostawcy zasobów, zobacz [Rozwiązywanie błędów podczas rejestracji dostawcy zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="manage-connectivity-mode"></a>Zarządzanie trybem łączności

Oprócz domyślnego trybu normalnego urządzenie może również działać w trybie częściowo odłączony lub odłączony. Każdy z tych trybów jest opisany poniżej:

- **Częściowo rozłączone** — w tym trybie urządzenie nie może przekazać żadnych danych do udziałów, jednak można zarządzać za pośrednictwem witryny Azure portal.

    Ten tryb jest zwykle używany w przypadku taryfowej sieci satelitarnej, a celem jest zminimalizowanie zużycia przepustowości sieci. Minimalne zużycie sieci może nadal występować w przypadku operacji monitorowania urządzeń.

- **Rozłączono** — w tym trybie urządzenie jest całkowicie odłączone od chmury i zarówno przesyłanie w chmurze, jak i pobieranie są wyłączone. Urządzeniem można zarządzać tylko za pośrednictwem lokalnego interfejsu użytkownika sieci Web.

    Ten tryb jest zwykle używany, gdy chcesz przetraktować urządzenie w tryb offline.

Aby zmienić tryb urządzenia, wykonaj następujące czynności:

1. W lokalnym interfejsie użytkownika urządzenia przejdź do **ustawień konfiguracji > chmury**.
2. Wyłącz **przesyłanie i pobieranie w chmurze**.
3. Aby uruchomić urządzenie w trybie częściowo rozłączony, włącz **zarządzanie portalem Azure**.

    ![Tryb łączności](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-1.png)
 
4. Aby uruchomić urządzenie w trybie rozłączania, wyłącz **zarządzanie portalem Azure**. Teraz urządzeniem można zarządzać tylko za pośrednictwem lokalnego interfejsu użytkownika sieci Web.

    ![Tryb łączności](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-2.png)

## <a name="manage-power"></a>Zarządzanie zasilaniem

Można wyłączyć lub ponownie uruchomić urządzenie wirtualne przy użyciu lokalnego interfejsu użytkownika sieci Web. Zaleca się, aby przed ponownym uruchomieniem przełączyć udziały w tryb offline na hoście, a następnie na urządzeniu. Ta akcja minimalizuje możliwość uszkodzenia danych.

1. W lokalnym interfejsie użytkownika sieci Web przejdź do **ustawienia konserwacji > zasilania**.
2. Kliknij **przycisk Zamknij** lub Uruchom **ponownie** w zależności od tego, co zamierzasz zrobić.

    ![Ustawienia zasilania](media/data-box-gateway-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak,** aby kontynuować.

> [!NOTE]
> Jeśli wyłączysz urządzenie wirtualne, musisz uruchomić urządzenie za pomocą zarządzania hipernadzorcami.
