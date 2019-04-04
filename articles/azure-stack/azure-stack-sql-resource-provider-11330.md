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
ms.date: 01/09/2019
ms.author: jeffgilb
ms.reviewer: jiahan
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: ae5a824fbd96a9a76eb18811a46bfc17afa15073
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58879356"
---
# <a name="sql-resource-provider-11330-release-notes"></a>Informacje o wersji dostawcy 1.1.33.0 zasobów SQL

*Dotyczy Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Te informacje o wersji opisano ulepszenia i znane problemy w wersji dostawcy zasobów 1.1.33.0 SQL.

## <a name="build-reference"></a>Dokumentacja kompilowania
Pobierz binarne dostawcy zasobów bazy danych SQL, a następnie uruchom samodzielnej wyodrębniania, aby wyodrębnić zawartość do katalogu tymczasowego. Dostawca zasobów ma minimalne odpowiednie usługi Azure Stack kompilacji. Minimalna wersja wersji usługi Azure Stack, trzeba zainstalować tę wersję dostawcy zasobów bazy danych SQL znajduje się poniżej:

> |Minimalna wersja usługi Azure Stack|Wersja dostawcy zasobów SQL|
> |-----|-----|
> |Wersja 1808 (1.1808.0.97)|[SQL RP wersji 1.1.33.0](https://aka.ms/azurestacksqlrp11330)|  
> |     |     |

> [!IMPORTANT]
> Dotyczą minimalnej obsługiwanej aktualizacji usługi Azure Stack system zintegrowany z usługi Azure Stack lub wdrożyć najnowszą usługi Azure Stack Development Kit (ASDK) przed wdrożeniem najnowszą wersję dostawcy zasobów bazy danych SQL.

## <a name="new-features-and-fixes"></a>Nowe funkcje i poprawki
Ta wersja dostawcy zasobów usługi Azure Stack SQL obejmuje następujące ulepszenia i poprawki:

### <a name="fixes"></a>Poprawki
- **Rozszerzenie portalu dostawcy zasobów SQL może wybrać niewłaściwej subskrypcji**. Dostawcy zasobów bazy danych SQL korzysta z wywołań usługi Azure Resource Manager, aby określić pierwszy subskrypcji administratora usługi do użycia, które mogą nie być *domyślne subskrypcję dostawcy*. Jeśli tak się stanie, dostawcy zasobów bazy danych SQL nie działa normalnie. 

- **Serwera hostingu SQL nie ma hostowanych baz danych.** Utworzone przez użytkownika bazy danych mogą nie być wyświetlane podczas przeglądania zasoby dzierżawcy do hostowania serwerów SQL.

- **Poprzedniego wdrożenia (1.1.30.0) dostawcy zasobów SQL może się nie powieść, jeśli nie włączono protokołu TLS 1.2**. Zaktualizowano dostawcy zasobów bazy danych SQL 1.1.33.0 umożliwiające protokołu TLS 1.2, wdrażając dostawcy zasobów, aktualizowanie dostawcy zasobów lub rotacji kluczy tajnych. 

- **Obrót tajny dostawcy zasobów SQL nie powiedzie się**. Rozwiązano problem, co w przypadku rotacji kluczy tajnych następujący kod błędu:
`New-AzureRmResourceGroupDeployment - Error: Code=InvalidDeploymentParameterValue; Message=The value of deployment parameter 'StorageAccountBlobUri' is null.`

## <a name="known-issues"></a>Znane problemy 

- **Jednostki SKU SQL może potrwać do godziny mają być wyświetlane w portalu**. Może potrwać do godziny dla nowo utworzonej jednostki SKU były widoczne do użycia podczas tworzenia nowych baz danych SQL. 

    **Obejście**: Brak.

- **Ponownie nazw logowania SQL**. Próba utworzenia nowego SQL Zaloguj się przy użyciu tej samej nazwy użytkownika jako istniejące dane logowania w tej samej subskrypcji spowoduje ponowne użycie tych samych danych logowania i hasło istniejącego. 

    **Obejście**: Używać różnych nazw użytkowników, podczas tworzenia nowych nazw logowania w ramach tej samej subskrypcji lub utworzyć nazwy logowania przy użyciu tej samej nazwy użytkownika w różnych subskrypcjach.

- **Udostępnione nazw logowania SQL spowodować niespójność danych**. Jeśli identyfikator logowania SQL jest udostępniony dla wielu baz danych SQL w ramach tej samej subskrypcji, zmiana hasła logowania spowoduje niespójność danych.

    **Obejście**: Zawsze należy używać różnych identyfikatorów logowania dla różnych baz danych w ramach tej samej subskrypcji.

- **Dostawcy zasobów bazy danych SQL nie można dodać odbiornik programu SQL Server Always On**. Korzystając z adresu IP odbiornika, SQL Server zawsze na odbiornika, dostawcy zasobów bazy danych SQL maszyny Wirtualnej nie można rozpoznać nazwy hosta odbiornika.

    **Obejście**: Upewnij się, że DNS działa prawidłowo rozpoznać adres IP odbiornika odbiornik o nazwie hosta.

### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>Znane problemy dotyczące administratorów chmury, działania usługi Azure Stack
Zapoznaj się z dokumentacją w [informacje o wersji programu Azure Stack](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się więcej o dostawcy zasobów bazy danych SQL](azure-stack-sql-resource-provider.md).

[Przygotowanie do wdrożenia dostawcy zasobów bazy danych SQL](azure-stack-sql-resource-provider-deploy.md#prerequisites).

[Uaktualnienie dostawcy zasobów bazy danych SQL z poprzedniej wersji](azure-stack-sql-resource-provider-update.md). 