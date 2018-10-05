---
title: Jednostki danych | Dokumentacja firmy Microsoft
description: Przegląd entites danych.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: c7b321ab04df405c56cab0952942b0d6e142da6d
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48810453"
---
# <a name="data-entities"></a>Obiekty danych

W tym artykule definiuje i zawiera omówienie jednostek danych. Zawiera informacje o funkcji jednostek danych, scenariusze, które obsługują, kategorie, które są używane do oraz metody ich tworzenia.

## <a name="overview"></a>Przegląd

Jednostki danych to Abstrakcja z fizyczna implementacja tabel bazy danych. Na przykład w znormalizowanych tabel ilości danych dla każdego klienta mogą być przechowywane w tabeli klientów, a następnie pozostałych mogą znajdować się w taki sposób, w niewielki zestaw powiązanych tabel. W tym przypadku jednostki danych dla koncepcji klienta jest wyświetlana jako jeden nieznormalizowany widok, w którym każdy wiersz zawiera wszystkie dane z tabeli klientów i jej powiązane tabele. Jednostki danych hermetyzuje koncepcji biznesowych do formatu, który ułatwia programowania i integracji. Abstrakcyjną rodzaj jednostki danych można uprościć dostosowywanie i tworzenie aplikacji. Później pozyskiwania również powoduje, że kod aplikacji z nieuniknione postęp dokonany w fizycznych tabel między wersjami.

Podsumowując: jednostki danych zapewnia abstrakcji koncepcyjne i hermetyzacji (Widok nieznormalizowany) podstawowych schematów tabel do reprezentowania danych kluczowe pojęcia i funkcje.

## <a name="capabilities"></a>Możliwości

Jednostki danych ma następujące możliwości:

- Zastępuje on programy rozbieżności i fragmentacji koncepcji AXD, jednostek danych Framework Import/Export (DIXF) i agregacji zapytania za pomocą pojedynczego koncepcji.
- Zapewnia pojedynczy stosu do przechwytywania logiki biznesowej i obsługiwać scenariusze takie jak importu/eksportu, integracji i programowania.
- Staje się podstawowym mechanizmem eksportowania i importowania pakietów danych dla zarządzania cyklem życia aplikacji (ALM) i pokaz scenariuszy danych.
- Można go udostępniane jako usługi OData i następnie używane w scenariuszach integracji synchroniczne tabelarycznych styl i integracji programu Microsoft Office.

Zobacz [jednostek danych](https://docs.microsoft.com/dynamics365/operations/dev-itpro/data-entities/data-entities) Aby uzyskać więcej informacji.
