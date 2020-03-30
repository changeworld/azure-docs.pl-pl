---
title: Monitorowanie delegowanych zasobów na dużą skalę
description: Dowiedz się, jak skutecznie korzystać z dzienników usługi Azure Monitor w skalowalny sposób w przypadku dzierżawców klientów, których zarządzasz.
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 217df3c55ab54b6569bae8cacb338764ecb7125d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122898"
---
# <a name="monitor-delegated-resources-at-scale"></a>Monitorowanie delegowanych zasobów na dużą skalę

Jako dostawca usług może mieć wbudowane wielu dzierżawców klientów do zarządzania zasobami delegowanymi platformy Azure. [Latarnia morska platformy Azure](../overview.md) umożliwia dostawcom usług wykonywanie operacji na dużą skalę w kilku dzierżawach jednocześnie, dzięki czemu zadania zarządzania są bardziej wydajne.

W tym temacie pokazano, jak używać [dzienników usługi Azure Monitor](../../azure-monitor/platform/data-platform-logs.md) w skalowalny sposób w dzierżawach klientów, których zarządzasz.

## <a name="create-log-analytics-workspaces"></a>Tworzenie obszarów roboczych usługi Log Analytics

Aby zbierać dane, musisz utworzyć obszary robocze usługi Log Analytics. Te obszary robocze usługi Log Analytics są unikatowymi środowiskami dla danych gromadzonych przez usługę Azure Monitor. Każdy obszar roboczy ma własne repozytorium danych i konfigurację, a źródła danych i rozwiązania są skonfigurowane do przechowywania danych w określonym obszarze roboczym.

Zaleca się tworzenie tych obszarów roboczych bezpośrednio w dzierżawców klientów. W ten sposób ich dane pozostają w ich dzierżaw, a nie są eksportowane do Twojej. Umożliwia to również scentralizowane monitorowanie wszelkich zasobów lub usług obsługiwanych przez usługi Log Analytics, co zapewnia większą elastyczność w zakresie typów danych, które monitorujesz.

Obszar roboczy usługi Log Analytics można utworzyć przy użyciu [portalu Azure](../../azure-monitor/learn/quick-create-workspace.md), przy użyciu [interfejsu wiersza polecenia platformy Azure](../../azure-monitor/learn/quick-create-workspace-cli.md)lub za pomocą programu Azure [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md).

## <a name="deploy-policies-that-log-data"></a>Wdrażanie zasad, które rejestrują dane

Po utworzeniu obszarów roboczych usługi Log Analytics można wdrożyć [zasady platformy Azure](../../governance/policy/index.yml) w hierarchiach klientów, aby dane diagnostyczne były wysyłane do odpowiedniego obszaru roboczego w każdej dzierżawie. Dokładne zasady, które można wdrożyć może się różnić w zależności od typów zasobów, które chcesz monitorować.

Aby dowiedzieć się więcej na temat tworzenia zasad, zobacz [Samouczek: Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](../../governance/policy/tutorials/create-and-manage.md). To [narzędzie społeczności](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/tools/azure-diagnostics-policy-generator) udostępnia skrypt ułatwiające tworzenie zasad służących do monitorowania określonych typów zasobów.

Po określeniu zasad, które należy wdrożyć, można [je wdrożyć w delegowanych subskrypcji na dużą skalę.](policy-at-scale.md)

## <a name="analyze-the-gathered-data"></a>Analizowanie zebranych danych

Po wdrożeniu zasad dane będą rejestrowane w obszarach roboczych usługi Log Analytics utworzonych w każdej dzierżawie klienta. Aby uzyskać szczegółowe informacje we wszystkich zarządzanych klientach, można użyć narzędzi, takich jak [skoroszyty usługi Azure Monitor,](../../azure-monitor/platform/workbooks-overview.md) aby zbierać i analizować informacje z wielu źródeł danych.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [usłudze Azure Monitor](../../azure-monitor/index.yml).
- Dowiedz się więcej o [dziennikach monitora platformy Azure](../../azure-monitor/platform/data-platform-logs.md).
- Dowiedz się więcej o [środowiskach zarządzania między dzierżawcami](../concepts/cross-tenant-management-experience.md).
