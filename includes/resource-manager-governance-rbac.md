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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38753670"
---
Upewnij się, że użytkownicy w organizacji mają odpowiedni poziom dostępu do tych zasobów. Nie udzielaj użytkownikom nieograniczonego dostępu, ale upewnij się, że mogą oni wykonywać swoją pracę. Kontrola dostępu oparta na rolach (RBAC) umożliwia zarządzanie, którzy użytkownicy mają uprawnienia do wykonania określonych czynności w zakresie. Rola definiuje zestawem dozwolonych akcji. Przypisywanie roli do zakresu, a następnie określ użytkowników, którzy należą do tej roli dla zakresu.

Podczas planowania strategii kontroli dostępu, należy udzielić użytkownikom najniższych uprawnień, aby wykonać swoją pracę. Na poniższej ilustracji przedstawiono sugerowany wzorzec do przypisywania RBAC.

![Zakres](./media/resource-manager-governance-rbac/role-examples.png)

Istnieją trzy role, które są stosowane do wszystkich zasobów - właściciela, współautora i czytelnika. Wszystkie konta przypisane do roli właściciel powinien być ściśle kontrolowane i rzadko używane. Użytkownicy, którzy muszą tylko obserwować stan rozwiązania może być przyznany Rola czytelnika.

Większość użytkowników są przyznawane [ról określonych zasobów](../articles/role-based-access-control/built-in-roles.md) lub [ról niestandardowych](../articles/role-based-access-control/custom-roles.md) na poziomie grupy subskrypcji lub zasobu. Te role definiują ściśle dozwolone akcje. Przypisywanie użytkowników do tych ról, można przyznać wymagany dostęp do użytkowników bez umożliwiający kontrolę zbyt dużej ilości. Konto można przypisać do więcej niż jednej roli, a użytkownik pobiera połączone uprawnienia ról. Udzielanie dostępu na poziomie zasobów jest często zbyt restrykcyjna dla użytkowników, ale mogą działać w przypadku zautomatyzowanego procesu, przeznaczony dla określonego zadania.

### <a name="who-can-assign-roles"></a>Kto może przypisywać role

Aby móc tworzyć i usuwać przypisania roli, użytkownicy muszą mieć dostęp `Microsoft.Authorization/roleAssignments/*`. Ten dostęp jest udzielany za pośrednictwem ról Właściciel lub Administrator dostępu użytkowników.