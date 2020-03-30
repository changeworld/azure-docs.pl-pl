---
title: Architektura Azure FarmBeats
description: Opisuje architekturę usługi Azure FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: d64c2175072d9979cfda2ea5f75beb34d3ad0d6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482376"
---
# <a name="integration-patterns"></a>Wzorce integracji

Azure FarmBeats to oferta biznesowa dostępna w portalu Azure Marketplace. FarmBeats umożliwia agregację zestawów danych rolnictwa między dostawcami i generowanie użytecznych informacji poprzez tworzenie modeli sztucznej inteligencji (AI) lub uczenia maszynowego (ML) przez łączenie zestawów danych.

![Projekt Farm Beats](./media/architecture-for-farmbeats/farmbeats-architecture-1.png)

W poniższych sekcjach opisano wzorzec integracji dla usługi Azure FarmBeats.

## <a name="why-integrate-with-azure-farmbeats"></a>Dlaczego warto integrować się z platformą Azure FarmBeats?

Ta sekcja koncentruje się na partnerach, którzy chcą zintegrować swoje systemy danych (takie jak czujniki, drony, stacje pogodowe) z platformą Azure FarmBeats.

Azure FarmBeats to rozszerzalna oferta, która umożliwia firmom rolniczym dodawanie różnych historycznych i w czasie rzeczywistym zestawów danych rolniczych do jednej platformy. Usługa Azure FarmBeats pomaga firmie rolniczej normalizować, contextualize i agregować swoje dane w kontekście farmy.

Stając się partnerem danych za pomocą usługi Azure FarmBeats, możesz otworzyć swoje systemy do szerszego wdrożenia i dotrzeć do większej liczby klientów dzięki ofertom danych. Usługa Azure FarmBeats udostępnia rozszerzalną warstwę interfejsu API o nazwie Datahub, która ułatwia systematyczne i systematyczne przymierzanie danych z urządzeń do standardowego schematu.

Gdy dane są dostępne w wystąpieniu Azure FarmBeats klientów, klienci mogą tworzyć bogatsze analizy i narzędzia na podstawie danych.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat integracji danych czujników, zobacz [integracja danych czujników](sensor-partner-integration-in-azure-farmbeats.md) i integracja partnerów z obrazami, zobacz [integracja partnerów obrazów](imagery-partner-integration-in-azure-farmbeats.md).
