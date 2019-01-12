---
title: Usługa Azure Stack MySQL zasobów dostawcy 1.1.30.0 informacje o wersji | Dokumentacja firmy Microsoft
description: Więcej informacji na temat nowości w najnowszej Azure Stack MySQL zasobów dostawcy aktualizacji, w tym wszystkie znane problemy i gdzie można go pobrać.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2018
ms.author: jeffgilb
ms.reviewer: jiahan
ms.openlocfilehash: 0b8bf94ba99f36662f03a48ca1f09366ddf2aa40
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2019
ms.locfileid: "54243512"
---
# <a name="mysql-resource-provider-11300--release-notes"></a>Informacje o wersji 1.1.30.0 dostawcy zasobów MySQL

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Te informacje o wersji opisano ulepszenia i znane problemy w bazie danych MySQL w wersja dostawcy zasobów 1.1.30.0.

## <a name="build-reference"></a>Dokumentacja kompilowania
Pobierz binarne dostawcy zasobów bazy danych MySQL, a następnie uruchom samodzielnej wyodrębniania, aby wyodrębnić zawartość do katalogu tymczasowego. Dostawca zasobów ma minimalne odpowiednie usługi Azure Stack kompilacji. Minimalna wersja wersji usługi Azure Stack, trzeba zainstalować tę wersję dostawcy zasobów MySQL znajduje się poniżej:

> |Minimalna wersja usługi Azure Stack|Wersja dostawcy zasobów MySQL|
> |-----|-----|
> |Aktualizacja usługi Azure Stack 1808 (1.1808.0.97)|[1.1.30.0](https://aka.ms/azurestackmysqlrp11300)|
> |     |     |

> [!IMPORTANT]
> Dotyczą minimalnej obsługiwanej aktualizacji usługi Azure Stack system zintegrowany z usługi Azure Stack lub wdrożyć najnowszą usługi Azure Stack Development Kit (ASDK) przed wdrożeniem najnowszą wersję dostawcy zasobów bazy danych MySQL.

## <a name="new-features-and-fixes"></a>Nowe funkcje i poprawki
Ta wersja dostawcy zasobów usługi Azure Stack MySQL obejmuje następujące ulepszenia i poprawki:

- **Dane telemetryczne włączona dla wdrożenia dostawcy zasobów MySQL**. Zbieranie danych telemetrycznych została włączona dla wdrożenia dostawcy zasobów MySQL. Danych telemetrycznych zebranych obejmuje wdrażanie dostawcy zasobów, uruchomić i zatrzymać razy, Zamknij szczegóły błędu, stan i komunikaty o zakończeniu (jeśli dotyczy).

- **Aktualizacja szyfrowania TLS 1.2**. Włączona obsługa protokołu TLS 1.2 — tylko do komunikacji dostawcy zasobów za pomocą wewnętrznych składników usługi Azure Stack. 

### <a name="fixes"></a>Poprawki

- **Zgodność platformy Azure Stack PowerShell dostawcy zasobów MySQL**. Dostawcy zasobów bazy danych MySQL została zaktualizowana do pracy z profilem stosu 2018-03-01-hybrydowej platformy Azure PowerShell i w celu zapewnienia zgodności przy użyciu usługi AzureRM 1.3.0 lub nowszy.

- **MySQL logowania zmiany hasła bloku**. Rozwiązano problem, w którym bloku zmiany hasła, w którym nie można zmienić hasła. Powiadomienia o zmianie usunięto łączy na podstawie hasła.

## <a name="known-issues"></a>Znane problemy 

- **Jednostek SKU MySQL może potrwać do godziny mają być wyświetlane w portalu**. Może potrwać do godziny dla nowo utworzonej jednostki SKU były widoczne do użycia podczas tworzenia nowych baz danych MySQL. 

    **Obejście**: Brak.

- **Ponownie logowania MySQL**. Próba utworzenia nowego MySQL Zaloguj się przy użyciu tej samej nazwy użytkownika jako istniejące dane logowania w tej samej subskrypcji spowoduje ponowne użycie tych samych danych logowania i hasło istniejącego. 

    **Obejście**: Używać różnych nazw użytkowników, podczas tworzenia nowych nazw logowania w ramach tej samej subskrypcji lub utworzyć nazwy logowania przy użyciu tej samej nazwy użytkownika w różnych subskrypcjach.

- **Wymaganie obsługi protokołu TLS 1.2**. Jeśli zostanie podjęta próba wdrożeniem lub aktualizację dostawcy zasobów MySQL z poziomu komputera, na którym nie włączono protokołu TLS 1.2, operacja może zakończyć się niepowodzeniem. Uruchom następujące polecenie programu PowerShell na komputerze, używany do wdrażania lub zaktualizuj dostawcę zasobów, aby sprawdzić, czy protokół TLS 1.2, jest zwracana jako obsługiwane:

  ```powershell
  [System.Net.ServicePointManager]::SecurityProtocol
  ```

  Jeśli **Tls12** jest nie jest uwzględniony w danych wyjściowych polecenia, protokołu TLS 1.2 nie jest włączona na komputerze.

    **Obejście**: Uruchom następujące polecenie programu PowerShell w celu włączenia protokołu TLS 1.2 i następnie Rozpocznij wdrażanie dostawcy zasobów lub zaktualizować skrypt z tej samej sesji programu PowerShell:

    ```powershell
    [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
    ```
 
### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>Znane problemy dotyczące administratorów chmury, działania usługi Azure Stack
Zapoznaj się z dokumentacją w [informacje o wersji programu Azure Stack](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się więcej o dostawcy zasobów MySQL](azure-stack-mysql-resource-provider.md).

[Przygotowanie do wdrożenia dostawcy zasobów MySQL](azure-stack-mysql-resource-provider-deploy.md#prerequisites).

[Uaktualnienie dostawcy zasobów MySQL z poprzedniej wersji](azure-stack-mysql-resource-provider-update.md). 