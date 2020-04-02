---
title: Zarządzanie rozszerzeniami języka w klastrze usługi Azure Data Explorer.
description: Użyj rozszerzenia języka, aby zintegrować inne języki w kwerendach KQL usługi Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: orhasban
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 88c3047afebf3d31b50145c6df47776fdc1ddcd4
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522474"
---
# <a name="manage-language-extensions-in-your-azure-data-explorer-cluster-preview"></a>Zarządzanie rozszerzeniami języków w klastrze usługi Azure Data Explorer (wersja zapoznawcza)

Funkcja rozszerzeń języka umożliwia używanie wtyczek rozszerzenia języka do integrowania innych języków z zapytaniami KQL usługi Azure Data Explorer. Po uruchomieniu funkcji zdefiniowanej przez użytkownika (UDF) przy użyciu odpowiedniego skryptu skrypt pobiera dane tabelaryczne jako dane wejściowe i oczekuje się, że do produkcji danych losowych. Środowisko uruchomieniowe wtyczki jest hostowane w [piaskownicy](/azure/kusto/concepts/sandboxes), odizolowanym i bezpiecznym środowisku, działającym w węzłach klastra. W tym artykule zarządzasz wtyczką rozszerzeń języka w klastrze usługi Azure Data Explorer w witrynie Azure portal.

> [!NOTE]
> Rozszerzenia języka Usługi Azure Data Explorer, które są obecnie obsługiwane są Python i R.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) przed rozpoczęciem.
* Tworzenie [klastra i bazy danych usługi Azure Data Explorer](create-cluster-database-portal.md).

## <a name="enable-language-extensions-on-your-cluster"></a>Włączanie rozszerzeń języka w klastrze

> [!WARNING]
> Zapoznaj się z [ograniczeniami](#limitations) przed włączeniem rozszerzenia języka.

Wykonaj następujące czynności, aby włączyć rozszerzenia języka w klastrze:

1. W witrynie Azure portal przejdź do klastra usługi Azure Data Explorer. 
1. W **obszarze Ustawienia**wybierz pozycję **Konfiguracje**. 
1. W okienku **Konfiguracje** wybierz pozycję **Włącz,** aby włączyć rozszerzenie języka.
1. Wybierz **pozycję Zapisz**.
 
    ![rozszerzenie języka na](media/language-extensions/configurations-enable-extension.png)

> [!NOTE]
> Włączenie procesu rozszerzenia języka może potrwać kilka minut. W tym czasie klaster zostanie zawieszony.
 
## <a name="run-language-extension-integrated-queries"></a>Uruchamianie zapytań zintegrowanych z rozszerzeniem języka

* Dowiedz się, jak [uruchamiać zintegrowane zapytania KQL języka Python.](/azure/kusto/query/pythonplugin)
* Dowiedz się, jak [uruchamiać zintegrowane zapytania KQL R](/azure/kusto/query/rplugin). 

## <a name="disable-language-extensions-on-your-cluster"></a>Wyłączanie rozszerzeń języka w klastrze

> [!NOTE]
> Wyłączenie rozszerzeń języka może potrwać kilka minut.

Wykonaj następujące czynności, aby wyłączyć rozszerzenia języka w klastrze:

1. W witrynie Azure portal przejdź do klastra usługi Azure Data Explorer. 
1. W **obszarze Ustawienia**wybierz pozycję **Konfiguracje**. 
1. W okienku **Konfiguracje** wybierz pozycję **Wyłączone,** aby wyłączyć rozszerzenie języka.
1. Wybierz **pozycję Zapisz**.

    ![Rozszerzenie języka wyłączone](media/language-extensions/configurations-disable-extension.png)

## <a name="limitations"></a>Ograniczenia

* Funkcja rozszerzeń języka nie obsługuje [szyfrowania dysku](manage-cluster-security.md). 
* Piaskownica środowiska uruchomieniowego rozszerzeń języka przydziela miejsce na dysku, nawet jeśli żadne zapytanie nie jest uruchamiane w zakresie odpowiedniego języka.

