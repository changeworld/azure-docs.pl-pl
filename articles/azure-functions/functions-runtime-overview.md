---
title: Omówienie środowiska wykonawczego funkcji platformy Azure
description: Omówienie wersji uruchomieniowej funkcji Azure Functions
author: apwestgarth
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: ab04aa4ca7f54e8de120d078a313c3096a350aa5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74226626"
---
# <a name="azure-functions-runtime-overview-preview"></a>Omówienie środowiska uruchomieniowego funkcji platformy Azure (wersja zapoznawcza)

[!INCLUDE [intro](../../includes/functions-runtime-preview-note.md)]

Środowisko uruchomieniowe funkcji platformy Azure (wersja zapoznawcza) zapewnia nowy sposób, aby skorzystać z prostoty i elastyczności modelu programowania usługi Azure Functions lokalnie. Zbudowany na tych samych korzeniach open source co usługi Azure Functions środowisko wykonawcze usługi Azure Functions jest wdrażane lokalnie, aby zapewnić niemal identyczne środowisko programistyczne, co usługa w chmurze.

![Portal w wersji uruchomieniowych usług Azure Functions Runtime][1]

Środowisko uruchomieniowe funkcji platformy Azure umożliwia środowisko azure functions przed zatwierdzeniem do chmury. W ten sposób zasoby kodu, które tworzysz, mogą być następnie zabierane ze sobą do chmury podczas migracji.  Środowisko wykonawcze otwiera również nowe opcje, takie jak używanie zapasowej mocy obliczeniowej komputerów lokalnych do uruchamiania procesów wsadowych przez noc. Można również używać urządzeń w organizacji do warunkowego wysyłania danych do innych systemów, zarówno lokalnie, jak i w chmurze.

Środowisko uruchomieniowe funkcji azure składa się z dwóch elementów:

* Rola zarządzania czasem wykonywania funkcji Azure
* Rola procesu roboczego środowiska wykonawczego usług Azure Functions

## <a name="azure-functions-management-role"></a>Rola zarządzania funkcjami platformy Azure

Rola zarządzania funkcjami platformy Azure zapewnia hosta do zarządzania funkcjami lokalnymi. Ta rola wykonuje następujące zadania:

* Hosting portalu zarządzania funkcjami platformy Azure, który jest taki sam, jaki widzisz w [witrynie Azure portal](https://portal.azure.com). Portal zapewnia spójne środowisko, które umożliwia tworzenie funkcji w taki sam sposób, jak w witrynie Azure portal.
* Dystrybucja funkcji między wieloma pracownikami funkcji.
* Zapewnienie punktu końcowego publikowania, dzięki czemu można publikować funkcje bezpośrednio z programu Microsoft Visual Studio, pobierając i importując profil publikowania.

## <a name="azure-functions-worker-role"></a>Rola procesu roboczego funkcji platformy Azure

Role procesu roboczego funkcji platformy Azure są wdrażane w kontenerach systemu Windows i są tam, gdzie wykonuje się kod funkcji.  Można wdrożyć wiele ról procesu roboczego w całej organizacji i ta opcja jest kluczowym sposobem, w którym klienci mogą korzystać z zapasowej mocy obliczeniowej.  Jednym z przykładów, gdzie w wielu organizacjach istnieje zapasowe obliczenia, są maszyny stale zasilane, ale nie używane przez dłuższy czas.

## <a name="minimum-requirements"></a>Minimalne wymagania

Aby rozpocząć pracę ze środowiska uruchomieniowego funkcji platformy Azure, musisz mieć komputer z systemem Windows Server 2016 lub Windows 10 Creators Update z dostępem do wystąpienia programu SQL Server.

## <a name="next-steps"></a>Następne kroki

Instalowanie [wersji zapoznawczej środowiska uruchomieniowego funkcji Platformy Azure](https://aka.ms/azafrdoc)

<!--Image references-->
[1]: ./media/functions-runtime-overview/AzureFunctionsRuntime_Portal.png
