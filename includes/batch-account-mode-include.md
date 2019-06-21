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
ms.openlocfilehash: b5bb5da2b68bae36353b81a04993814b395ce67b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183585"
---
> [!NOTE]
> Podczas tworzenia konta usługi Batch możesz wybrać jeden z dwóch typów *alokacji puli*: **subskrypcję użytkownika** lub **usługę Batch**. W większości przypadków należy użyć domyślnego trybu usługi Batch, który polega na alokowaniu pul w tle w subskrypcjach zarządzanych przez platformę Azure. W alternatywnym trybie subskrypcji użytkownika maszyny wirtualne i inne zasoby usługi Batch są tworzone bezpośrednio w Twojej subskrypcji po utworzeniu puli. Tryb subskrypcji użytkownika jest wymagany przy tworzeniu pul usługi Batch przy użyciu usługi [Azure Reserved VM Instances](https://azure.microsoft.com/pricing/reserved-vm-instances/). Aby utworzyć konto usługi Batch w trybie subskrypcji użytkownika, musisz również zarejestrować subskrypcję w usłudze Azure Batch i powiązać konto z usługą Azure Key Vault.