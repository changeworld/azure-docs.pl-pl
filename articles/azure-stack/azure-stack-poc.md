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
ms.date: 06/04/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: mvc
ms.openlocfilehash: 848df59b01cc0c03b9a5f3dae2644d469c8651bb
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234890"
---
# <a name="what-is-azure-stack"></a>Co to jest usługa Azure Stack?

Microsoft Azure Stack to hybrydowa platforma w chmurze, która pozwala dostarczać usługi platformy Azure w centrum danych. Ta platforma jest przeznaczona do zmieniających się potrzeb biznesowych. Azure Stack można włączyć nowe scenariusze dla nowoczesnych aplikacji, takich jak edge i w środowiskach rozłączonych lub spełniać określone wymagania dotyczące zabezpieczeń i zgodności.

Azure Stack jest oferowany dwie opcje wdrażania, zgodnie z potrzebami.

## <a name="azure-stack-integrated-systems"></a>Zintegrowane systemy usługi Azure Stack
Azure Stack zintegrowanych systemów są oferowane przez powiązanie firmy Microsoft i [dostawców sprzętu będących partnerami](https://azure.microsoft.com/overview/azure-stack/integrated-systems/), tworzenia rozwiązania, które oferuje realizowanych chmury innowacji i obliczanie uproszczenia zarządzania. Ponieważ Azure Stack jest oferowany jako zintegrowane sprzętu i oprogramowania systemu, masz elastyczność i kontroli, którą chcesz dodać, wraz z możliwością innowacji z chmury. Azure Stack zintegrowanych systemów zakres rozmiaru z 4-12 węzłów i wspólnie są obsługiwane przez partnera sprzętu i firmy Microsoft.  Azure Stack zintegrowanych systemów umożliwia tworzenie nowych scenariuszy i wdrażanie nowych rozwiązań dla obciążeń produkcyjnych.

## <a name="azure-stack-development-kit"></a>Zestaw Azure Stack Development Kit

Microsoft [Azure Stack Development Kit (ASDK)](.\asdk\asdk-what-is.md) jest wdrożenie jednowęzłowej usługi Azure Stack, można użyć do oceny i Dowiedz się więcej o usłudze Azure Stack.  Można również użyć ASDK jako Środowisko deweloperskie do tworzenia aplikacji przy użyciu interfejsów API i narzędzia, która jest zgodna z platformą Azure.

>[!Note]
>ASDK nie jest przeznaczona do użycia jako środowiska produkcyjnego.

ASDK ma następujące ograniczenia:

* ASDK jest skojarzony z jednym Azure Active Directory (Azure AD) lub dostawcy tożsamości usługi Active Directory Federation Services (AD FS). Można utworzyć wielu użytkowników, w tym katalogu i przypisać subskrypcji do poszczególnych użytkowników.
* Ponieważ składniki usługi Azure Stack są wdrażane na jednym komputerze-hoście, Brak dostępnych ograniczone zasoby fizyczne zasoby dzierżawcy. Ta konfiguracja nie jest przeznaczona do oceny skali lub wydajności.
* Scenariusze sieci są ograniczone ze względu na jednym hoście i wymagania dotyczące wdrażania kart interfejsu Sieciowego.

## <a name="next-steps"></a>Kolejne kroki

- [Kluczowe funkcje i pojęcia](azure-stack-key-features.md)
- [Azure Stack: Rozszerzenie Azure (pdf)](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)
