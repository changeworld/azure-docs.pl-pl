---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/16/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: b4b06119b9d46781b967fc8d98808c60d2b41ccb
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
Chcesz upewnić się, że użytkownicy w organizacji mają odpowiedni poziom dostępu do tych zasobów. Nie chcesz udzielić nieograniczonego dostępu do użytkowników, ale należy również upewnij się, że mogą je z siecią firmową. Kontrola dostępu oparta na rolach (RBAC) pozwala na zarządzanie, którzy użytkownicy mają uprawnienia do wykonania określonych czynności w zakresie. Rola definiuje zestaw dozwolonych akcji. Przypisz rolę do zakresu, a następnie określ użytkowników, którzy należą do tej roli w ramach zakresu.

Podczas planowania strategii kontroli dostępu, należy przyznać użytkownikom najniższych uprawnień do ich pracy. Na poniższej ilustracji przedstawiono sugerowany wzorzec przypisywania RBAC.

![Zakres](./media/resource-manager-governance-rbac/role-examples.png)

Istnieją trzy role, które są stosowane do wszystkich zasobów - właściciela, współautora ani czytnika. Wszystkie konta przypisane do roli właściciela należy ściśle kontrolowane i rzadko używane. Użytkownicy, którzy tylko należy obserwować stan rozwiązania może być przyznany rolę czytelnika.

Większość użytkowników są przyznawane [ról określonych zasobów](../articles/role-based-access-control/built-in-roles.md) lub [role niestandardowe](../articles/role-based-access-control/custom-roles.md) na poziomie grupy subskrypcja lub zasób. Te role definiują ściśle akcje dozwolone. Przypisywanie użytkowników do tych ról, można przyznać wymagany dostęp do użytkowników bez umożliwiający zbyt dużo kontrolę. Konto można przypisać do więcej niż jednej roli, a użytkownik pobiera Scalonej uprawnienia ról. Udzielanie dostępu na poziomie zasobów często jest zbyt restrykcyjna dla użytkowników, ale mogą działać w przypadku zautomatyzowanego procesu przeznaczony dla określonego zadania.

### <a name="who-can-assign-roles"></a>Który może przypisywać role

Aby utworzyć i usunąć przypisania roli, użytkownicy muszą mieć `Microsoft.Authorization/roleAssignments/*` dostępu. Ten dostęp za pomocą ról właściciel lub Administrator dostępu użytkowników.