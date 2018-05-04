---
title: Co to jest usługa Azure Stack? | Microsoft Docs
description: Stos Azure umożliwia uruchamianie usług platformy Azure w centrum danych.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/22/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: mvc
ms.openlocfilehash: d54f392eddfcca99e7fe0b037b9efd0a4e90433c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="what-is-azure-stack"></a>Co to jest usługa Azure Stack?

Microsoft Azure stos jest hybrydowe platformy w chmurze, który pozwala dostarczyć usług platformy Azure z centrum danych w organizacji.  Stos Azure umożliwiających nowych scenariuszy dla nowoczesnych aplikacji w różnych kluczowych scenariuszy, takich jak edge i środowiskach rozłączonych lub spotkania szczególne wymagania zabezpieczeń i zgodności.  Azure stosu jest oferowany dwie opcje wdrażania, zgodnie z potrzebami.

## <a name="azure-stack-integrated-systems"></a>Zintegrowane systemy usługi Azure Stack
Stos Azure zintegrowanych systemów są oferowane przez powiązanie firmy Microsoft i [dostawców sprzętu będących partnerami](https://azure.microsoft.com/overview/azure-stack/integrated-systems/), tworzenia rozwiązania, który oferuje realizowanych chmury innowacji zrównoważonym z uproszczenia zarządzania.  Ponieważ stos Azure jest oferowany jako zintegrowany system sprzętu i oprogramowania, są oferowane jednostkom elastyczność i kontroli, podczas nadal przyjmowanie innowacji z chmury.  Azure stosu zintegrowanych systemów zakres rozmiaru z 4-12 węzłów i wspólnie są obsługiwane przez partnera sprzętu i firmy Microsoft.  Użyj stosu Azure zintegrowanych systemów, aby włączyć nowe scenariusze dla obciążeń produkcyjnych.    

## <a name="azure-stack-development-kit"></a>Zestaw Azure Stack Development Kit
Microsoft [Azure stosu Development Kit (ASDK)](.\asdk\asdk-what-is.md) to wdrożenie jednowęzłowej stosu Azure, której można użyć do oceny i Dowiedz się więcej o stosu Azure.  Można także używać ASDK jako Środowisko deweloperskie, gdzie można utworzyć przy użyciu interfejsów API i narzędzia zgodne z platformy Azure. ASDK nie jest przeznaczony do użycia jako środowiska produkcyjnego.

ASDK ma następujące ograniczenia:
* ASDK jest skojarzony z jednym Azure Active Directory (Azure AD) lub dostawcy tożsamości usługi Active Directory Federation Services (AD FS). Można utworzyć wielu użytkowników, w tym katalogu i przypisać subskrypcji do poszczególnych użytkowników.
* Ze wszystkimi składnikami wdrożone na jednym komputerze brak dostępnych ograniczone zasoby fizyczne zasoby dzierżawcy. Ta konfiguracja nie jest przeznaczone do oceny skali lub wydajności.
* Scenariusze sieci są ograniczone ze względu na wymagania jednego/kartę Sieciową hosta.  

## <a name="next-steps"></a>Kolejne kroki
[Kluczowe funkcje i pojęcia](azure-stack-key-features.md)

[Azure stosu: Rozszerzenie Azure (pdf)](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)

