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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60742141"
---
Blokad zasobów uniemożliwić użytkownikom w organizacji przypadkowo usuwanie i modyfikowanie zasoby o znaczeniu krytycznym. W przeciwieństwie do kontroli dostępu opartej na rolach blokady zasobów stosują ograniczenie do wszystkich użytkowników i ról. 

Poziom blokady można ustawić na wartość **CanNotDelete** lub **ReadOnly**. W portalu, poziomy blokady są wyświetlane jako **Usuń** i **tylko do odczytu** odpowiednio.

* **CanNotDelete** oznacza, że autoryzowani użytkownicy nadal może odczytywać i modyfikować zasobu, ale ich nie można usunąć zasobu. 
* **Tylko do odczytu** oznacza, że autoryzowani użytkownicy mogą odczytywać zasobu, ale nie mogą usunąć ani zaktualizować zasobu. Zastosowanie Ta blokada jest podobny do ograniczania wszystkim uprawnionym użytkownikom uprawnienia przyznane przez **czytnika** roli. 

> [!TIP]
> Należy zachować ostrożność podczas stosowania **tylko do odczytu** blokady. Niektóre operacje, które promieniowe wydają się być odczytu operacje rzeczywiście wymagają dodatkowych akcji. Na przykład **tylko do odczytu** blokadę konta magazynu uniemożliwia wszystkim użytkownikom wyświetlanie listy kluczy. Na liście, którą zwrócone klucze nie są dostępne dla operacji klucze odbywa się za pomocą żądania POST operacji zapisu. A **tylko do odczytu** blokady zasobu usługi App Service uniemożliwia wyświetlanie plików dla zasobu, ponieważ interakcji wymaga dostępu do zapisu Eksploratora serwera w usłudze Visual Studio.

Po zastosowaniu blokady w zakresie nadrzędnej wszystkie zasoby w tym zakresie dziedziczenia tego samego blokady. Nawet zasoby, które zostaną dodane później dziedziczą blokadę z obiektu nadrzędnego. Najbardziej restrykcyjne blokady w dziedziczenia ma pierwszeństwo.

Blokad usługi Resource Manager mają zastosowanie tylko do operacji, które odbywa się w płaszczyzny zarządzania, który składa się z operacji wysyłane do `https://management.azure.com`. Blokady nie Ograniczaj, jak zasoby przetwarzania własnych funkcji. Zmiany zasobu są ograniczone, ale operacje zasobów nie są ograniczone. Na przykład blokadę tylko do odczytu na bazę danych SQL uniemożliwia usunięcie lub zmodyfikowanie bazy danych. Nie uniemożliwia z tworzenia, aktualizowania lub usuwania danych w bazie danych. Transakcje są przepuszczane, ponieważ te operacje nie są wysyłane do `https://management.azure.com`.

### <a name="who-can-create-or-delete-locks-in-your-organization"></a>Kto może utworzyć lub usunąć blokady w Twojej organizacji
Aby móc tworzyć lub usuwać blokady zarządzania, musisz mieć dostęp do akcji `Microsoft.Authorization/locks/*`. Spośród wbudowanych ról tylko **Właściciel** i **Administrator dostępu użytkowników** mają dostęp do tych akcji.
