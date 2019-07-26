---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: f188f2c7bea511f1109d37ef49563e0f745a770e
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385745"
---
Za pomocą Azure Resource Manager można zdefiniować parametry dla wartości, które będą używane podczas wdrażania szablonu. Szablon zawiera `parameters` sekcję zawierającą wszystkie wartości parametrów. Każda wartość parametru jest używana przez szablon do definiowania zasobów, które mają zostać wdrożone.

> [!NOTE]
> Nie należy definiować parametrów dla wartości, które pozostają niezmienione. Definiuj parametry tylko dla wartości, które różnią się w zależności od wdrożonego projektu lub w oparciu o środowisko, w którym jest wdrażany.

Podczas definiowania parametrów:

* Aby określić dozwolone wartości, które użytkownik może podać podczas wdrażania, użyj pola **allowedValues** .

* Aby przypisać wartości domyślne do parametru, jeśli podczas wdrażania nie podano żadnych wartości,  Użyj pola DefaultValue. 
