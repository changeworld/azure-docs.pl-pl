---
title: Usługa Azure SQL stosu zasobów dostawcy 1.1.30.0 informacje o wersji | Dokumentacja firmy Microsoft
description: Więcej informacji na temat nowości w najnowszej usługi Azure Stack SQL zasobów dostawcy aktualizacji, w tym wszelkich znanych problemach i gdzie można go pobrać.
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
ms.date: 1/09/2019
ms.author: jeffgilb
ms.reviewer: georgel
ms.openlocfilehash: 5cbbc3c1c2371264e3e29028d4b9d68533cc59ff
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2019
ms.locfileid: "54154950"
---
# <a name="sql-resource-provider-11300-release-notes"></a>Informacje o wersji dostawcy 1.1.30.0 zasobów SQL

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Te informacje o wersji opisano ulepszenia i znane problemy w wersji dostawcy zasobów 1.1.30.0 SQL.

## <a name="build-reference"></a>Dokumentacja kompilowania
Pobierz binarne dostawcy zasobów bazy danych SQL, a następnie uruchom samodzielnej wyodrębniania, aby wyodrębnić zawartość do katalogu tymczasowego. Dostawca zasobów ma minimalne odpowiednie usługi Azure Stack kompilacji. Minimalna wersja wersji usługi Azure Stack, trzeba zainstalować tę wersję dostawcy zasobów bazy danych SQL znajduje się poniżej:

> |Minimalna wersja usługi Azure Stack|Wersja dostawcy zasobów SQL|
> |-----|-----|
> |Wersja 1808 (1.1808.0.97)|[1.1.30.0](https://aka.ms/azurestacksqlrp11300)|
> |     |     |

> [!IMPORTANT]
> Dotyczą minimalnej obsługiwanej aktualizacji usługi Azure Stack system zintegrowany z usługi Azure Stack lub wdrożyć najnowszą usługi Azure Stack Development Kit (ASDK) przed wdrożeniem najnowszą wersję dostawcy zasobów bazy danych SQL.

## <a name="new-features-and-fixes"></a>Nowe funkcje i poprawki
Ta wersja dostawcy zasobów usługi Azure Stack SQL obejmuje następujące ulepszenia i poprawki:

- **Dane telemetryczne włączone dla wdrożeń dostawcy zasobów programu SQL**. Zbieranie danych telemetrycznych została włączona dla wdrożeń dostawcy zasobów programu SQL. Danych telemetrycznych zebranych obejmuje wdrażanie dostawcy zasobów, uruchomić i zatrzymać razy, Zamknij szczegóły błędu, stan i komunikaty o zakończeniu (jeśli dotyczy).

- **Aktualizacja szyfrowania TLS 1.2**. Włączona obsługa protokołu TLS 1.2 — tylko do komunikacji dostawcy zasobów za pomocą wewnętrznych składników usługi Azure Stack. 

### <a name="fixes"></a>Poprawki

- **SQL zasobów dostawcy usługi Azure Stack PowerShell zgodności**. Dostawcy zasobów bazy danych SQL została zaktualizowana do pracy z profilem stosu 2018-03-01-hybrydowej platformy Azure PowerShell i w celu zapewnienia zgodności przy użyciu usługi AzureRM 1.3.0 lub nowszy.

- **Blok hasło zmiany nazwy logowania SQL**. Rozwiązano problem, w którym bloku zmiany hasła, w którym nie można zmienić hasła. Powiadomienia o zmianie usunięto łączy na podstawie hasła.

- **Hostowanie aktualizacji bloku ustawień serwera SQL**. Rozwiązano problem, w którym bloku ustawienia został niepoprawnie zatytułowany jako "Password".

## <a name="known-issues"></a>Znane problemy 

- **Jednostki SKU SQL może potrwać do godziny mają być wyświetlane w portalu**. Może potrwać do godziny dla nowo utworzonej jednostki SKU były widoczne do użycia podczas tworzenia nowych baz danych SQL. 

    **Obejście**: Brak.

- **Ponownie nazw logowania SQL**. Próba utworzenia nowego SQL Zaloguj się przy użyciu tej samej nazwy użytkownika jako istniejące dane logowania w tej samej subskrypcji spowoduje ponowne użycie tych samych danych logowania i hasło istniejącego. 

    **Obejście**: Używać różnych nazw użytkowników, podczas tworzenia nowych nazw logowania w ramach tej samej subskrypcji lub utworzyć nazwy logowania przy użyciu tej samej nazwy użytkownika w różnych subskrypcjach.

- **Udostępnione nazw logowania SQL spowodować niespójność danych**. Jeśli identyfikator logowania SQL jest udostępniony dla wielu baz danych SQL w ramach tej samej subskrypcji, zmiana hasła logowania spowoduje niespójność danych.

    **Obejście**: Zawsze należy używać różnych identyfikatorów logowania dla różnych baz danych w ramach tej samej subskrypcji.

- **Wymaganie obsługi protokołu TLS 1.2**. Jeśli zostanie podjęta próba wdrożeniem lub aktualizację dostawcy zasobu języka SQL z komputera, na którym nie włączono protokołu TLS 1.2, operacja może zakończyć się niepowodzeniem. Uruchom następujące polecenie programu PowerShell na komputerze, używany do wdrażania lub zaktualizuj dostawcę zasobów, aby sprawdzić, czy protokół TLS 1.2, jest zwracana jako obsługiwane:

  ```powershell
  [System.Net.ServicePointManager]::SecurityProtocol
  ```

  Jeśli **Tls12** jest nie jest uwzględniony w danych wyjściowych polecenia, protokołu TLS 1.2 nie jest włączona na komputerze.

    **Obejście**: Uruchom następujące polecenie programu PowerShell w celu włączenia protokołu TLS 1.2 i następnie Rozpocznij wdrażanie dostawcy zasobów lub zaktualizować skrypt z tej samej sesji programu PowerShell:

    ```powershell
    [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
    ```
- **Dostawcy zasobów bazy danych SQL nie można dodać odbiornik programu SQL Server Always On**. Korzystając z adresu IP odbiornika, SQL Server zawsze na odbiornika, dostawcy zasobów bazy danych SQL maszyny Wirtualnej nie można rozpoznać nazwy hosta odbiornika.

    **Obejście**: Upewnij się, że DNS działa prawidłowo rozpoznać adres IP odbiornika odbiornik o nazwie hosta.
    
### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>Znane problemy dotyczące administratorów chmury, działania usługi Azure Stack
Zapoznaj się z dokumentacją w [informacje o wersji programu Azure Stack](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się więcej o dostawcy zasobów bazy danych SQL](azure-stack-sql-resource-provider.md).

[Przygotowanie do wdrożenia dostawcy zasobów bazy danych SQL](azure-stack-sql-resource-provider-deploy.md#prerequisites).

[Uaktualnienie dostawcy zasobów bazy danych SQL z poprzedniej wersji](azure-stack-sql-resource-provider-update.md). 