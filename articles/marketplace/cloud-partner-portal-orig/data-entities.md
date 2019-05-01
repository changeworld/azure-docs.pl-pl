---
title: Obiekty danych
description: Omówienie jednostek danych.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: a382f9b3ce08bba266311c2cc1d5f868f1bc3143
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64934932"
---
# <a name="data-entities"></a>Obiekty danych

W tym artykule definiuje i zawiera omówienie jednostek danych. Zawiera informacje o funkcji jednostek danych, scenariusze, które obsługują, kategorie, które są używane do oraz metody ich tworzenia.

## <a name="overview"></a>Omówienie

Jednostki danych to Abstrakcja z fizyczna implementacja tabel bazy danych. Na przykład w znormalizowanych tabel ilości danych dla każdego klienta mogą być przechowywane w tabeli klientów, a następnie pozostałych mogą znajdować się w taki sposób, w niewielki zestaw powiązanych tabel. W tym przypadku jednostki danych dla koncepcji klienta jest wyświetlana jako jeden nieznormalizowany widok, w którym każdy wiersz zawiera wszystkie dane z tabeli klientów i jej powiązane tabele. Jednostki danych hermetyzuje koncepcji biznesowych do formatu, który ułatwia programowania i integracji. Abstrakcyjną rodzaj jednostki danych można uprościć dostosowywanie i tworzenie aplikacji. Później pozyskiwania również powoduje, że kod aplikacji z nieuniknione postęp dokonany w fizycznych tabel między wersjami.

Podsumowując: Jednostki danych zapewnia abstrakcji koncepcyjne i hermetyzacji (Widok nieznormalizowany) podstawowych schematów tabel do reprezentowania danych kluczowe pojęcia i funkcje.

## <a name="capabilities"></a>Możliwości

Jednostki danych ma następujące możliwości:

- Zastępuje on programy rozbieżności i fragmentacji koncepcji AXD, jednostek danych Framework Import/Export (DIXF) i agregacji zapytania za pomocą pojedynczego koncepcji.
- Zapewnia pojedynczy stosu do przechwytywania logiki biznesowej i obsługiwać scenariusze takie jak importu/eksportu, integracji i programowania.
- Staje się podstawowym mechanizmem eksportowania i importowania pakietów danych dla zarządzania cyklem życia aplikacji (ALM) i pokaz scenariuszy danych.
- Można go udostępniane jako usługi OData i następnie używane w scenariuszach integracji synchroniczne tabelarycznych styl i integracji programu Microsoft Office.

Zobacz [jednostek danych](https://docs.microsoft.com/dynamics365/operations/dev-itpro/data-entities/data-entities) Aby uzyskać więcej informacji.
