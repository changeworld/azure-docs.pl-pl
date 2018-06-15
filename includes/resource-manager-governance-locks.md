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
ms.openlocfilehash: 0cb3de7d893ccfe638468110b1b6f5fb61b2bc7c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2018
ms.locfileid: "29402496"
---
Blokowania zasobów uniemożliwić użytkownikom w organizacji przypadkowo usuwanie i modyfikowanie kluczowych zasobów. W przeciwieństwie do kontroli dostępu opartej na rolach blokowania zasobów mają zastosowanie ograniczenia we wszystkich użytkowników i ról. 

Można ustawić poziom blokady **CanNotDelete** lub **tylko do odczytu**. W portalu, poziomy blokady są wyświetlane jako **usunąć** i **tylko do odczytu** odpowiednio.

* **CanNotDelete** oznacza, że autoryzowani użytkownicy mogą nadal odczytywać i modyfikować zasobu, ale ich nie można usunąć tego zasobu. 
* **Tylko do odczytu** oznacza, że autoryzowani użytkownicy mogą odczytywać zasobu, ale nie można usunąć lub zaktualizować zasobu. Stosowanie Ta blokada jest podobny do ograniczania wszystkim uprawnionym użytkownikom uprawnienia przyznane przez **czytnika** roli. 

> [!TIP]
> Należy zachować ostrożność podczas stosowania **tylko do odczytu** blokady. Niektóre operacje, które się wydawać odczytu operacje rzeczywiście wymagają dodatkowych czynności. Na przykład **tylko do odczytu** blokady na koncie magazynu uniemożliwia wyświetlanie kluczy wszystkich użytkowników. Listy kluczy operacji jest obsługiwany za pomocą żądania POST, ponieważ zwrócony klucze są dostępne do zapisu. A **tylko do odczytu** blokada na zasób usługi aplikacji uniemożliwia wyświetlanie plików dla zasobu, ponieważ dostęp do zapisu wymaga interakcji Eksploratora serwera w usłudze Visual Studio.

Po zastosowaniu blokady w zakresie nadrzędnym, wszystkie zasoby w ramach tego zakresu dziedziczą tego samego blokady. Nawet zasoby, które później zostaną dodane dziedziczą blokady z obiektu nadrzędnego. Najbardziej restrykcyjne blokady w dziedziczenia ma pierwszeństwo.

Menedżer zasobów blokad mają zastosowanie tylko do operacji, które pojawiają się w płaszczyźnie zarządzania, która składa się z operacji wysyłane do `https://management.azure.com`. Blokad nie Ograniczaj sposobu zasobów procesu własnych funkcji. Zmiany zasobu jest ograniczony, ale operacje zasobów nie są ograniczone. Na przykład blokady w bazie danych SQL tylko do odczytu uniemożliwia usuwanie i modyfikowanie bazy danych. Nie uniemożliwia z tworzenie, aktualizowanie lub usuwanie danych z bazy danych. Transakcje danych są dozwolone, ponieważ te operacje nie są wysyłane do `https://management.azure.com`.

### <a name="who-can-create-or-delete-locks-in-your-organization"></a>Który można utworzyć lub usunąć blokady w organizacji
Aby utworzyć lub usunąć blokady zarządzania, musi mieć dostęp do `Microsoft.Authorization/locks/*` akcje. Wbudowanych ról, tylko **właściciela** i **Administrator dostępu użytkowników** otrzymują te akcje.
