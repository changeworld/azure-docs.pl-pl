---
title: Przygotowywanie rozwiązania Dynamics 365 | Dokumentacja firmy Microsoft
description: Umożliwiająca pakowanie, instalowanie i odinstalowywanie składników
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricarod.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 2ea62a826b9f99e264cb1b2242f8d8108453d8e7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60397337"
---
# <a name="dynamics-365-solution-preparation"></a>Przygotowywanie rozwiązania Dynamics 365

System solutioning Dynamics 365 to architektura służąca do tworzenia pakietów, instalowanie i odinstalowywanie składników, które udostępniają funkcje firmy. Rozwiązania są używane przez niezależnych dostawców oprogramowania i innymi partnerami firmy Microsoft Dynamics 365 do dystrybucji rozszerzenia, które tworzą.

Jeśli masz istniejące Dynamics 365 (xRM) niezależnych dostawców oprogramowania, najprawdopodobniej już masz utworzone rozwiązanie zarządzane i pliku solution.zip. W rozwiązaniu upewnij się, że "Display Name" i "Description" pola będą odzwierciedlać potrzeby klientów, aby wyświetlić. Są one widoczne w Centrum administracyjnym usługi CRM Online.

![CRMScreenShot1](media/CRMScreenShot1.png)

_**Uwaga:** W poniższym przykładzie pakietu firma Microsoft jest założenie, że nazwa rozwiązania jest "SampleSolution.zip"_

Jeśli masz nowe niezależnych dostawców oprogramowania, można uzyskać szczegółowe informacje na temat tworzenia rozwiązania, w tym miejscu: [https://msdn.microsoft.com/library/gg334530.aspx](https://msdn.microsoft.com/library/gg334530.aspx)

Jeśli rozwiązanie wymaga obsługi danych:

* Tworzenie danych przykładowych w środowisku testowym
* Narzędzie do migracji konfiguracji umożliwia tworzenie schematu za pomocą reguł porównywania danych.
* Zapisz schematu konfiguracji z plików projektu. Będzie on potrzebny później w przypadku aktualizacji danych konfiguracji.
* Wyeksportować dane konfiguracji. Pamiętaj nadać nazwę, która jest zrozumiały dla eksportu pliku eksportu.
