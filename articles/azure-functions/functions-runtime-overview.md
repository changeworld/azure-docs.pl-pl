---
title: Azure Functions — Omówienie środowiska uruchomieniowego | Dokumentacja firmy Microsoft
description: Omówienie podglądu środowiska uruchomieniowego usługę Azure Functions
services: functions
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: 557f071e2cd8d4f639c881274e6e74a8fb745859
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2017
ms.locfileid: "26290230"
---
# <a name="azure-functions-runtime-overview"></a>Azure Functions — Omówienie środowiska uruchomieniowego

Środowisko uruchomieniowe Functions Azure udostępnia nową metodę umożliwiające wykorzystują łatwość i elastyczność funkcji Azure programowania modelu lokalnego. Oparty na tej samej korzenie typu open source jako usługi Azure Functions, środowisko uruchomieniowe Functions Azure jest wdrożony lokalnymi zapewnia środowisko rozwoju niemal identyczne jako usługa w chmurze.

![Portal w wersji zapoznawczej usługi Azure Functions środowiska wykonawczego][1]

Środowisko uruchomieniowe Functions Azure udostępnia sposób środowisko Azure Functions przed zatwierdzeniem do chmury. W ten sposób zasobów kodu, które tworzysz można podejmowana z Tobą w chmurze podczas migracji.  Środowisko uruchomieniowe otwiera również nowe opcje, takie jak przy użyciu mocy obliczeniowej zapasowe komputerów lokalnych do uruchamiania procesów partii noc. Można też używać urządzeń w organizacji warunkowo wysyłać danych do innych systemów, zarówno lokalnie, jak i w chmurze.

Środowisko uruchomieniowe Functions Azure składa się z dwóch części:

* Rola zarządzania Azure Functions środowiska wykonawczego
* Azure Functions roli procesu roboczego środowiska wykonawczego

## <a name="azure-functions-management-role"></a>Rola zarządzania Azure Functions

Rola zarządzania funkcji Azure udostępnia hosta w celu zarządzania lokalnym funkcje. Ta rola wykonuje następujące zadania:

* Hosting w portalu zarządzania Azure funkcji, która jest taka sama jak widać w [portalu Azure](https://portal.azure.com). Portal zawiera spójne środowisko, które umożliwia tworzenie funkcji w taki sam sposób jak w przypadku w portalu Azure.
* Dystrybucja funkcje na wiele funkcji pracowników.
* Udostępnia punkt końcowy publikowania tak, aby z funkcji bezpośrednio z programu Microsoft Visual Studio można publikować przez pobieranie i importowanie profilu publikowania.

## <a name="azure-functions-worker-role"></a>Środowisko Azure Functions roli procesu roboczego

Role procesów roboczych funkcji Azure są wdrażane w kontenerach systemu Windows i są, gdzie wykonuje kodu funkcji.  Można wdrożyć wiele ról procesów roboczych w całej organizacji, ta opcja jest klucza sposób, w którym ułatwia klientom użycia mocy obliczeniowej zapasowe.  Jeden przykład gdzie zapasowe obliczeń istnieje w wielu organizacjach jest maszyny stale włączone, ale nie jest używany dla dużych okresów.

## <a name="minimum-requirements"></a>Minimalne wymagania

Aby rozpocząć pracę ze środowiskiem uruchomieniowym funkcji platformy Azure, musi mieć komputer z systemem Windows Server 2016 lub Windows 10 twórców Update dostęp do wystąpienia programu SQL Server.

## <a name="next-steps"></a>Następne kroki

Zainstaluj [środowisko uruchomieniowe Functions Azure w wersji zapoznawczej](https://aka.ms/azafrdoc)

<!--Image references-->
[1]: ./media/functions-runtime-overview/AzureFunctionsRuntime_Portal.png
