---
title: Sprawdzanie poprawności alertów (plik testowy EICAR) w usłudze Azure Security Center | Dokumenty firmy Microsoft
description: Ten dokument zawiera informacje pomocne podczas walidowania alertów zabezpieczeń w usłudze Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: f8f17a55-e672-4d86-8ba9-6c3ce2e71a57
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 5146878adf10e452f38fecb115ec40792ffa84f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79140001"
---
# <a name="alert-validation-eicar-test-file-in-azure-security-center"></a>Walidacja alertów (plik testowy EICAR) w usłudze Azure Security Center
Ten dokument zawiera informacje dotyczące sposobu weryfikacji systemu pod kątem prawidłowej konfiguracji alertów usługi Azure Security Center.

## <a name="what-are-security-alerts"></a>Czym są alerty zabezpieczeń?
Alerty to powiadomienia generowane przez usługę Security Center w razie wykrycia zagrożeń dotyczących zasobów. Nadaje priorytet i wymienia alerty wraz z informacjami potrzebnymi do szybkiego zbadania problemu. Usługa Security Center udostępnia również zalecenia dotyczące sposobu korygowania ataku.
Aby uzyskać więcej informacji, zobacz [Alerty zabezpieczeń w umykarze i](security-center-alerts-overview.md) [zarządzanie alertami zabezpieczeń i reagowanie na nie](security-center-managing-and-responding-alerts.md)

## <a name="alert-validation"></a>Walidacja alertu

* [Windows](#validate-windows)
* [Linux](#validate-linux)
* [Kubernetes](#validate-kubernetes)

## <a name="validate-alerts-on-windows-vms"></a>Sprawdzanie poprawności alertów na maszynach wirtualnych systemu Windows<a name="validate-windows"></a>

Po zainstalowaniu agenta usługi Security Center na komputerze wykonaj następujące kroki z komputera, na którym chcesz być zaatakowanym zasobem alertu:

1. Skopiuj plik wykonywalny (na przykład **calc.exe)** na pulpit komputera lub inny katalog, który jest dla ciebie wygodny, i zmień jego nazwę na **ASC_AlertTest_662jfi039N.exe**.
1. Otwórz wiersz polecenia i wykonaj ten plik za pomocą argumentu (tylko fałszywa nazwa argumentu), takiego jak:```ASC_AlertTest_662jfi039N.exe -foo```
1. Zaczekaj od 5 do 10 minut, a następnie otwórz alerty usługi Security Center. Powinien zostać wyświetlony alert podobny do poniższego [przykładu:](#alert-validate)

> [!NOTE]
> Podczas przeglądania tego alertu testowego dla systemu Windows upewnij się, że pole **Włączone inspekcje argumentów** jest **prawdziwe**. Jeśli jest **false**, należy włączyć inspekcję argumentów wiersza polecenia. Aby ją włączyć, użyj następującego polecenia:
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## <a name="validate-alerts-on-linux-vms"></a>Sprawdzanie poprawności alertów na maszynach wirtualnych z systemem Linux<a name="validate-linux"></a>

Po zainstalowaniu agenta usługi Security Center na komputerze wykonaj następujące kroki z komputera, na którym chcesz być zaatakowanym zasobem alertu:
1. Skopiuj plik wykonywalny do dogodnej lokalizacji i zmień jego nazwę na **./asc_alerttest_662jfi039n**, na przykład:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. Otwórz wiersz polecenia i wykonaj ten plik:

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. Zaczekaj od 5 do 10 minut, a następnie otwórz alerty usługi Security Center. Powinien zostać wyświetlony alert podobny do poniższego [przykładu:](#alert-validate)

### <a name="alert-example"></a>Przykład alertu<a name="alert-validate"></a>

![Przykład sprawdzania poprawności alertów](./media/security-center-alert-validation/security-center-alert-validation-fig2.png) 


## <a name="validate-alerts-on-kubernetes"></a>Sprawdzanie poprawności alertów w umięsienia<a name="validate-kubernetes"></a>

Jeśli używasz funkcji w wersji zapoznawczej Usługi Microsoft w trybie podglądu integrowania usługi Azure Kubernetes, uruchom następujące polecenie kubectl, aby sprawdzić, czy alerty działają:

```kubectl get pods --namespace=asc-alerttest-662jfi039n```

Aby uzyskać więcej informacji na temat integracji usługi Azure Kubernetes i Usługi Azure Security Center, zobacz [ten artykuł](azure-kubernetes-service-integration.md).

## <a name="next-steps"></a>Następne kroki
Ten artykuł zawiera wprowadzenie do procesu walidacji alertów. Teraz, kiedy znasz już usługę Security Center, zapoznaj się z następującymi artykułami:

* [Sprawdzanie poprawności wykrywania zagrożeń w usłudze Azure Key Vault w usłudze Azure Security Center](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)
* [Zarządzanie alertami zabezpieczeń i odpowiadanie na nie w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) — dowiedz się, jak zarządzać alertami i reagować na zdarzenia związane z zabezpieczeniami w usłudze Security Center.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md) — dowiedz się, jak monitorować kondycję zasobów platformy Azure.
* [Opis alertów zabezpieczeń w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) — dowiedz się więcej o różnych typach alertów zabezpieczeń.
