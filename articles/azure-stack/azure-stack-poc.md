---
title: Co to jest usługa Azure Stack? | Microsoft Docs
description: Usługa Azure Stack umożliwia uruchamianie usług platformy Azure w centrum danych.
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
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: mvc
ms.openlocfilehash: b2b81938eafe104369e52e72f9958e2adf2cca6f
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2018
ms.locfileid: "49345224"
---
# <a name="what-is-azure-stack"></a>Co to jest usługa Azure Stack?

Microsoft Azure Stack to hybrydowa platforma w chmurze, która pozwala dostarczać usługi platformy Azure w centrum danych. Ta platforma jest przeznaczony do obsługi rozwijające się wymagania biznesowe. Usługa Azure Stack można włączyć nowych scenariuszy dla nowoczesnych aplikacji, takie jak krawędź i środowiskach rozłączonych lub wymagań dotyczących zabezpieczeń i zgodności.

Usługa Azure Stack jest oferowana w dwóch opcji wdrażania do własnych potrzeb.

## <a name="azure-stack-integrated-systems"></a>Zintegrowane systemy usługi Azure Stack
Azure Stack zintegrowanych systemów są oferowane przez powiązanie firmy Microsoft i [dostawców sprzętu będących partnerami](https://azure.microsoft.com/overview/azure-stack/integrated-systems/), tworzenia rozwiązania, które oferuje realizowanych chmury innowacji i obliczanie uproszczenia zarządzania. Ponieważ Azure Stack jest oferowany jako zintegrowane sprzętu i oprogramowania systemu, masz elastyczność i kontroli, którą chcesz dodać, wraz z możliwością innowacji z chmury. Azure Stack zintegrowanych systemów zakres rozmiaru z 4-12 węzłów i wspólnie są obsługiwane przez partnera sprzętu i firmy Microsoft.  Azure Stack zintegrowanych systemów umożliwia tworzenie nowych scenariuszy i wdrażanie nowych rozwiązań dla obciążeń produkcyjnych.

## <a name="azure-stack-development-kit"></a>Zestaw Azure Stack Development Kit

Microsoft [usługi Azure Stack Development Kit (ASDK)](.\asdk\asdk-what-is.md) to wdrożenie pojedynczego węzła usługi Azure Stack, można użyć do oceny i Dowiedz się więcej o usłudze Azure Stack.  Można również użyć ASDK jako środowiska deweloperskiego do tworzenia aplikacji, korzystając z interfejsów API i narzędzia, która jest zgodna z platformą Azure.

>[!Note]
>ASDK nie jest przeznaczona do użycia jako środowisko produkcyjne.

ASDK ma następujące ograniczenia:

* ASDK jest skojarzony z jednej usługi Azure Active Directory (Azure AD) lub dostawcy tożsamości usługi Active Directory Federation Services (AD FS). Można utworzyć wielu użytkowników w tym katalogu i przypisywanie subskrypcji do poszczególnych użytkowników.
* Składniki usługi Azure Stack są wdrażane na jednym komputerze-hoście, dlatego są ograniczone zasoby fizyczne dostępne dla zasobów dzierżawy. Ta konfiguracja nie jest przeznaczona do oceny skalę i wydajność.
* Scenariusze sieci są ograniczone ze względu na jednym hoście i wymagania dotyczące wdrażania kart interfejsu Sieciowego.

## <a name="next-steps"></a>Kolejne kroki

[Kluczowe funkcje i pojęcia](azure-stack-key-features.md)

[Azure Stack: Rozszerzenie Azure (pdf)](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)
