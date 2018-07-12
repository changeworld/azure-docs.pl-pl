---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: batch
author: dlepow
ms.service: batch
ms.topic: include
ms.date: 05/04/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 62bb91a2e51c39caf31719f72d68a6edab9205bc
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38944062"
---
> [!NOTE]
> Podczas tworzenia konta usługi Batch możesz wybrać jeden z dwóch typów *alokacji puli*: **subskrypcję użytkownika** lub **usługę Batch**. W większości przypadków należy użyć domyślnego trybu usługi Batch, który polega na alokowaniu pul w tle w subskrypcjach zarządzanych przez platformę Azure. W alternatywnym trybie subskrypcji użytkownika maszyny wirtualne i inne zasoby usługi Batch są tworzone bezpośrednio w Twojej subskrypcji po utworzeniu puli. Tryb subskrypcji użytkownika jest wymagany przy tworzeniu pul usługi Batch przy użyciu usługi [Azure Reserved VM Instances](https://azure.microsoft.com/pricing/reserved-vm-instances/). Aby utworzyć konto usługi Batch w trybie subskrypcji użytkownika, musisz również zarejestrować subskrypcję w usłudze Azure Batch i powiązać konto z usługą Azure Key Vault.