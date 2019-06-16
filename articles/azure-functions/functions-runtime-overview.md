---
title: Przegląd środowiska uruchomieniowego usługi Azure Functions | Dokumentacja firmy Microsoft
description: Omówienie usługi Azure Functions Runtime — wersja zapoznawcza
services: functions
author: apwestgarth
manager: stefsch
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: 2af9575c50ee522d6330ddf46c75b666132b7a84
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61021234"
---
# <a name="azure-functions-runtime-overview-preview"></a>Omówienie środowiska uruchomieniowego usługi Azure Functions (wersja zapoznawcza)

[!INCLUDE [intro](../../includes/functions-runtime-preview-note.md)]

Środowisko uruchomieniowe funkcji platformy Azure (wersja zapoznawcza) oferuje nowy sposób korzystać z zalet prostotę i elastyczności usługi Azure Functions programowania modelu w środowisku lokalnym. Oparta na tych samych obiektów głównych "open source", jako rozwiązań Azure Functions, środowisko uruchomieniowe usługi Azure Functions jest wdrożone w środowisku lokalnym zapewniając rozwoju niemal identyczne środowisko jako usługa w chmurze.

![Portal w wersji zapoznawczej środowiska uruchomieniowego usługi Azure Functions][1]

Środowisko uruchomieniowe usługi Azure Functions zapewnia sposób środowisko Azure Functions przed zatwierdzeniem do chmury. W ten sposób zasoby kodu, które tworzysz może następnie nastąpi przekierowanie z Tobą w chmurze podczas migracji.  Środowisko uruchomieniowe otwiera również nowe opcje, takie jak nocy procesów wsadowych za pomocą moc obliczeniową wolnym komputerów w środowisku lokalnym. Umożliwia także urządzeń w Twojej organizacji warunkowo wysyłać dane do innych systemów, zarówno lokalnie, jak i w chmurze.

Środowisko uruchomieniowe usługi Azure Functions składa się z dwóch części:

* Rola zarządzania środowiska uruchomieniowego usługi Azure Functions
* Rola procesu roboczego środowiska uruchomieniowego usługi Azure Functions

## <a name="azure-functions-management-role"></a>Rola zarządzania usługi Azure Functions

Rola zarządzania usługi Azure Functions udostępnia hosta zarządzania usługi Functions w środowisku lokalnym. Ta rola wykonuje następujące zadania:

* Hosting w portalu zarządzania usługą Azure Functions, który jest taki sam, zostanie wyświetlony w [witryny Azure portal](https://portal.azure.com). Portal zapewnia spójne środowisko, które pozwala tworzyć funkcje w taki sam sposób jak w witrynie Azure portal.
* Dystrybucja funkcji na wielu pracowników funkcji.
* Udostępnia punkt końcowy publikowania tak, że możesz publikować swoje bezpośrednio funkcje z programu Microsoft Visual Studio, pobierania i importowania profilu publikowania.

## <a name="azure-functions-worker-role"></a>Rola procesu roboczego usługi Azure Functions

Role procesów roboczych usługi Azure Functions są wdrażane w kontenerach Windows i są, w którym wykonuje kodu funkcji.  Możesz wdrożyć wiele ról procesów roboczych w całej organizacji, a ta opcja jest sposób kluczy, w którym można zapewnić klientom korzystanie z mocy obliczeniowej wolnym.  Jeden przykład której istnieje wolnym obliczeń w wielu organizacjach jest włączony stale maszynach, ale nie są używane przez duże okresy.

## <a name="minimum-requirements"></a>Minimalne wymagania

Aby rozpocząć pracę ze środowiskiem uruchomieniowym funkcji platformy Azure, konieczne jest posiadanie maszyny z systemem Windows Server 2016 lub Windows 10 Creators Update z dostępem do wystąpienia programu SQL Server.

## <a name="next-steps"></a>Następne kroki

Zainstaluj [środowisko uruchomieniowe usługi Azure Functions (wersja zapoznawcza)](https://aka.ms/azafrdoc)

<!--Image references-->
[1]: ./media/functions-runtime-overview/AzureFunctionsRuntime_Portal.png
