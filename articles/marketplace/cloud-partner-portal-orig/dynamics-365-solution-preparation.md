---
title: Przygotowanie rozwiązania Dynamics 365
description: Ramy pakowania, instalowania i odinstalowywania komponentów
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: ac1e4fa541e945f20904ced114a36b58d14585ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278590"
---
# <a name="dynamics-365-solution-preparation"></a>Przygotowanie rozwiązania Dynamics 365

System rozwiązań Dynamics 365 to struktura pakowania, instalowania i odinstalowywania składników, które zapewniają określone funkcje biznesowe. Rozwiązania są używane przez dostawców oprogramowania i innych partnerów systemu Microsoft Dynamics 365 do dystrybucji utworzonych przez siebie rozszerzeń.

Jeśli jesteś istniejącym użytkownikiem isv Dynamics 365 (xRM), najprawdopodobniej utworzono już rozwiązanie zarządzane i plik solution.zip. W rozwiązaniu upewnij się, że pola "Nazwa wyświetlana" i "Opis" odzwierciedlają to, co mają zobaczyć klienci. Są one wyświetlane w Centrum administracyjnym crm online.

![Zdjęcie CRMScreenShot1](media/CRMScreenShot1.png)

_**Uwaga:** W przykładzie pakietu, który następuje, zakładamy, że nazwa rozwiązania to "SampleSolution.zip"_

Jeśli jesteś nowym isv, można uzyskać więcej szczegółów na temat tworzenia rozwiązania tutaj:[https://msdn.microsoft.com/library/gg334530.aspx](https://msdn.microsoft.com/library/gg334530.aspx)

Jeśli rozwiązanie wymaga danych pomocniczych:

* Tworzenie przykładowych danych w środowisku testowym
* Narzędzie migracji konfiguracji służy do tworzenia schematu z regułami porównania danych.
* Zapisz schemat konfiguracji z plikami projektu. Będzie to potrzebne później, jeśli zaktualizujesz dane konfiguracyjne.
* Wyeksportuj dane konfiguracyjne. Pamiętaj, aby nadać plikowi eksportu nazwę, która ma znaczenie dla eksportu.
