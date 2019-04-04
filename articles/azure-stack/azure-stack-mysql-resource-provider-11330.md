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
ms.date: 01/09/2019
ms.author: jeffgilb
ms.reviewer: jiahan
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: e0101aebadcaef71f35c72b54f9126e69cff0f61
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58882839"
---
# <a name="mysql-resource-provider-11330--release-notes"></a>Informacje o wersji 1.1.33.0 dostawcy zasobów MySQL

*Dotyczy Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Te informacje o wersji opisano ulepszenia i znane problemy w bazie danych MySQL w wersja dostawcy zasobów 1.1.33.0.

## <a name="build-reference"></a>Dokumentacja kompilowania
Pobierz binarne dostawcy zasobów bazy danych MySQL, a następnie uruchom samodzielnej wyodrębniania, aby wyodrębnić zawartość do katalogu tymczasowego. Dostawca zasobów ma minimalne odpowiednie usługi Azure Stack kompilacji. Minimalna wersja wersji usługi Azure Stack, trzeba zainstalować tę wersję dostawcy zasobów MySQL znajduje się poniżej:

> |Minimalna wersja usługi Azure Stack|Wersja dostawcy zasobów MySQL|
> |-----|-----|
> |Wersja 1808 (1.1808.0.97)|[MySQL RP wersji 1.1.33.0](https://aka.ms/azurestackmysqlrp11330)|  
> |     |     |

> [!IMPORTANT]
> Dotyczą minimalnej obsługiwanej aktualizacji usługi Azure Stack system zintegrowany z usługi Azure Stack lub wdrożyć najnowszą usługi Azure Stack Development Kit (ASDK) przed wdrożeniem najnowszą wersję dostawcy zasobów bazy danych MySQL.

## <a name="new-features-and-fixes"></a>Nowe funkcje i poprawki
Ta wersja dostawcy zasobów usługi Azure Stack MySQL obejmuje następujące ulepszenia i poprawki:

### <a name="fixes"></a>Poprawki
- **Rozszerzenie portalu dostawcy zasobów MySQL wybrać niewłaściwej subskrypcji**. Dostawcy zasobów bazy danych MySQL korzysta z wywołań usługi Azure Resource Manager, aby określić pierwszy subskrypcji administratora usługi do użycia, które mogą nie być *domyślne subskrypcję dostawcy*. Jeśli tak się stanie, dostawcy zasobów bazy danych MySQL nie działają normalnie. 

- **MySQL, który nie ma serwera hostingu hostowane bazy danych.** Utworzone przez użytkownika bazy danych mogą nie być wyświetlane podczas przeglądania zasoby dzierżawcy do hostowania serwerów MySQL.

- **Poprzedniego wdrożenia (1.1.30.0) dostawcy zasobów MySQL może się nie powieść, jeśli nie włączono protokołu TLS 1.2**. Zaktualizowano dostawcy zasobów MySQL 1.1.33.0 umożliwiające protokołu TLS 1.2, wdrażając dostawcy zasobów, aktualizowanie dostawcy zasobów lub rotacji kluczy tajnych. 

- **Obrót tajny dostawcy zasobów MySQL nie powiodło się**. Rozwiązano problem, co w przypadku rotacji kluczy tajnych następujący kod błędu:
`New-AzureRmResourceGroupDeployment - Error: Code=InvalidDeploymentParameterValue; Message=The value of deployment parameter 'StorageAccountBlobUri' is null.`

## <a name="known-issues"></a>Znane problemy 

- **Jednostek SKU MySQL może potrwać do godziny mają być wyświetlane w portalu**. Może potrwać do godziny dla nowo utworzonej jednostki SKU były widoczne do użycia podczas tworzenia nowych baz danych MySQL. 

    **Obejście**: Brak.

- **Ponownie logowania MySQL**. Próba utworzenia nowego MySQL Zaloguj się przy użyciu tej samej nazwy użytkownika jako istniejące dane logowania w tej samej subskrypcji spowoduje ponowne użycie tych samych danych logowania i hasło istniejącego. 

    **Obejście**: Używać różnych nazw użytkowników, podczas tworzenia nowych nazw logowania w ramach tej samej subskrypcji lub utworzyć nazwy logowania przy użyciu tej samej nazwy użytkownika w różnych subskrypcjach.

- **Udostępnione logowania MySQL spowodować niespójność danych**. Jeśli logowanie MySQL jest udostępniony dla wielu baz danych MySQL w ramach tej samej subskrypcji, zmiana hasła logowania spowoduje niespójność danych.

    **Obejście**: Zawsze należy używać różnych identyfikatorów logowania dla różnych baz danych w ramach tej samej subskrypcji.


### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>Znane problemy dotyczące administratorów chmury, działania usługi Azure Stack
Zapoznaj się z dokumentacją w [informacje o wersji programu Azure Stack](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się więcej o dostawcy zasobów MySQL](azure-stack-mysql-resource-provider.md).

[Przygotowanie do wdrożenia dostawcy zasobów MySQL](azure-stack-mysql-resource-provider-deploy.md#prerequisites).

[Uaktualnienie dostawcy zasobów MySQL z poprzedniej wersji](azure-stack-mysql-resource-provider-update.md). 