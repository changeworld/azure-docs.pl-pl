---
title: Eksportowanie alertów dzienników do obszaru roboczego usługi Azure Log Analytics | Dokumentacja firmy Microsoft
description: W tym artykule wyjaśniono, jak eksportu alertów, które zostały wygenerowane przez usługę Azure Security Center obszar roboczy usługi Log Analytics.
services: security-center
documentationcenter: na
author: monhaber
manager: mbaldwin
editor: ''
ms.assetid: 2bc67ce5-ec3a-49df-afc3-b4bad5d8ce21
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/13/2019
ms.author: monhaber
ms.openlocfilehash: 12b8b376a0ff149cbfafc7fe69dd8da636280de8
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2019
ms.locfileid: "56653712"
---
# <a name="export-azure-security-center-alerts"></a>Eksportowanie alertów usługi Azure Security Center
W tym artykule wyjaśniono, jak eksportu alertów generowanych przez usługi Azure Security Center do lokalizacji, która może być używany przez inne narzędzia.

Niektóre z Centrum zabezpieczeń klientów wyrazili swoje potrzeby korzystanie z danych generowanych przez usługę Security Center z centralnej lokalizacji, który zawiera wszystkie zagregowane i przetworzonych danych (np. alerty wykrywania zagrożeń, zalecenia dotyczące zasad zabezpieczeń, pokrycia, Zabezpiecz wynik itp.). Dzięki temu dane do dalszej analizy i przetwarzane. Alternatywnie można też programowo korzystają z danych, zamiast korzystać z portalu Centrum zabezpieczeń w inny sposób.

Ponieważ obszary robocze usługi Azure Log Analytics są używane do przechowywania i przetwarzania nieprzetworzone dane zebrane z maszyn wirtualnych, **eksportowanie do usługi Log Analytics** funkcja zapewnia rozwiązanie te potrzeby, przesyłanie strumieniowe danych do obszaru roboczego zdefiniowanego przez użytkownika.

## <a name="prerequisites"></a>Wymagania wstępne
Należy obszar roboczy usługi Log Analytics. Zaleca się, że używasz tego samego obszaru roboczego usługi Log Analytics, zdefiniowaną w ustawienia zbierania danych. Podczas definiowania jedną do zbierania danych. [Obszar roboczy usługi log Analytics do zbierania danych](security-center-enable-data-collection.md)


## <a name="export-the-alerts"></a>Eksportowanie alertów
1. Kliknij przycisk **zasady zabezpieczeń**.
1. W wierszu subskrypcję, dla której chcesz użyć, kliknij przycisk **edytowanie ustawień**.
  ![Edytuj ustawienia](./media/security-center-alert-export/edit_settings.png "Edytuj ustawienia")
1. Kliknij przycisk **zbierania danych**.
1. Przewiń w dół do **Centrum zabezpieczeń Store wzbogacony danych** i kliknij opcję przełącznik, aby **na**.

   ![Opcja danych Store](./media/security-center-alert-export/store_data_option.png "zasad zabezpieczeń")
1. Wybierz obszar roboczy, który chcesz wyeksportować alerty.

    > [!NOTE]
    > Nie można wyeksportować alertów dzienników do domyślnego obszaru roboczego usługi Security Center. Jak wspomniano w [wymagania wstępne](#Prerequisites), należy użyć obszaru roboczego usługi Log Analytics.

1. Kliknij przycisk **Zapisz** (w górnej części ekranu).