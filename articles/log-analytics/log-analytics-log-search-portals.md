---
title: Portale tworzenia i edytowania dziennika zapytań w programie Azure Log Analytics | Dokumentacja firmy Microsoft
description: W tym artykule opisano portali, których można używać w Azure Log Analytics można tworzyć i edytować dziennika wyszukiwania.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: magoedte; bwren
ms.component: na
ms.openlocfilehash: e2ea0bf1fb3f1c63f4e6f037e465e8fdfd9a4374
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37133028"
---
# <a name="portals-for-creating-and-editing-log-queries-in-azure-log-analytics"></a>Portale tworzenia i edytowania dziennika zapytań w programie Azure Log Analytics

Dziennik wyszukiwania w różnych miejscach analizy dzienników służy do pobierania danych z obszaru roboczego.  Faktycznie tworzenie i edytowanie zapytań oprócz pracy interakcyjnie z dane zwrotne jednak, masz dwie opcje, które są opisane poniżej.  

## <a name="log-search"></a>Wyszukiwanie w dzienniku 
Strona wyszukiwania dziennika jest dostępna z portalu Azure.  Jest ona odpowiednia dla tworzenia podstawowych zapytań, które mogą zostać utworzone w jednym wierszu.  Dziennik wyszukiwania może być używany bez uruchamiania zewnętrznego portalu i służy do wykonywania różnych funkcji z dziennika wyszukiwania w tym tworzenie reguły alertu, tworzenie grup komputerów i eksportowanie wyników zapytania.  

Dziennik wyszukiwania zawiera wiele funkcji do edycji zapytanie bez pełnej znajomości języka kwerend.  Można uzyskać podsumowanie informacji o tych funkcji w [Utwórz dziennik wyszukiwania w Analiza dzienników Azure przy użyciu wyszukiwania dziennika](log-analytics-log-search-log-search-portal.md).


![Strona wyszukiwania dziennika](media/log-analytics-log-search-portals/log-search-portal.png)

## <a name="advanced-analytics-portal"></a>Zaawansowane portal analityka
Portal zaawansowana analityka jest dedykowany portal, który udostępnia zaawansowane funkcje, które nie jest dostępne w dzienniku wyszukiwania z portalu Azure.  Funkcje obejmują możliwość edytowania zapytania w wielu wierszach, selektywnego wykonywania kodu, kontekstowego używania funkcji IntelliSense oraz korzystania z funkcji analizy inteligentnej.  Portal analityka zaawansowane jest najbardziej odpowiednie dla projektowania złożonych zapytań, które są albo zapisywane jako dziennik wyszukiwania lub kopiować i wklejać do innych elementów analizy dzienników.  Można uruchomić portal analityka Zaawansowane z łącza na stronie dziennik wyszukiwania.

![Zaawansowane portal analityka](media/log-analytics-log-search-portals/advanced-analytics-portal.png)


Ze względu na jego funkcje zaawansowane zwykle użyjesz portalu zaawansowana analityka jako narzędzia do głównej tworzenia i edytowania zapytania.  Po określeniu, czy zapytanie działa zgodnie z oczekiwaniami, a następnie należy go skopiować i wkleić go w innym miejscu takich jak strony wyszukiwania dziennika lub Widok projektanta.  

### <a name="firewall-requirements"></a>Wymagania dotyczące zapory
Przeglądarka wymaga dostępu do następujących adresów do dostępu do portalu analityka zaawansowane.  Jeśli przeglądarka uzyskuje dostęp do portalu Azure za pośrednictwem zapory, należy włączyć dostęp do tych adresów.

| Identyfikator URI | Adres IP | Porty |
|:---|:---|:---|
| portal.loganalytics.io | Dynamiczny | 80,443 |
| api.loganalytics.io    | Dynamiczny | 80,443 |
| docs.loganalytics.io   | Dynamiczny | 80,443 |


## <a name="next-steps"></a>Kolejne kroki

- Zapoznaj się z artykułem samouczek na temat używania [wyszukiwania dziennika](log-analytics-tutorial-viewdata.md) Aby dowiedzieć się, jak tworzyć zapytania przy użyciu języka zapytań
- Zapoznaj się z [portal analityka zaawansowane](https://go.microsoft.com/fwlink/?linkid=856587) można tworzyć zaawansowane zapytania i używać jako środowisko projektowe dla wyszukiwań dziennika.

