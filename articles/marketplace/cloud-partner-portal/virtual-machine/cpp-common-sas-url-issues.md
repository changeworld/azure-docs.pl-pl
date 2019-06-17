---
title: Typowe problemy adresu URL sygnatury dostępu Współdzielonego i poprawki dla witryny Azure Marketplace
description: Listę typowych problemów w całym przy użyciu sygnatury dostępu współdzielonego identyfikatory URI i możliwe rozwiązania.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
ms.service: marketplace
ms.topic: article
ms.date: 09/27/2018
ms.author: pabutler
ms.openlocfilehash: 4f2770312624e1ca4c939ade458a451eb03f9d20
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938381"
---
# <a name="common-sas-url-issues-and-fixes"></a>Typowe problemy adresu URL sygnatury dostępu Współdzielonego i poprawki

W poniższej tabeli wymieniono niektóre typowe problemy występujące podczas pracy z sygnatury dostępu współdzielonego, (które są używane do identyfikowania i udostępniać przekazanych dysków VHD dla rozwiązania) wraz z sugerowanymi metodami rozwiązania.

| **Problem** | **Komunikat o błędzie** | **Fix** | 
| --------- | ------------------- | ------- | 
| &emsp;  *Wystąpił błąd podczas kopiowania obrazów* |  |  |
| "?" nie znajduje się w adresie URL sygnatury dostępu Współdzielonego | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Zaktualizować adres URL sygnatury dostępu Współdzielonego za pomocą narzędzia zalecane. |
| parametrów "st" i "se" nie w URL sygnatury dostępu Współdzielonego | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Zaktualizuj adres URL sygnatury dostępu Współdzielonego przy użyciu własnych **Data rozpoczęcia** i **Data zakończenia** wartości w nim. | 
| "sp = rl" nie w URL sygnatury dostępu Współdzielonego | `Failure: Copying Images. Not able to download blob using provided SAS Uri` | Zaktualizuj adres URL sygnatury dostępu Współdzielonego z uprawnieniami ustawionymi jako `Read` i `List`. | 
| Adres URL SAS ma białych znaków w nazwie wirtualnego dysku twardego | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aktualizacja adresu URL sygnatury dostępu Współdzielonego, można usunąć spacji. |
| Błąd autoryzacji adresów URL sygnatury dostępu Współdzielonego | `Failure: Copying Images. Not able to download blob due to authorization error` | Przejrzyj i Popraw format identyfikatora URI sygnatury dostępu Współdzielonego. Wygeneruj ponownie, jeśli to konieczne. |
| Adres URL sygnatury dostępu Współdzielonego "st" i "se" parametry bez specyfikacji pełnej daty i godziny | `Failure: Copying Images. Not able to download blob due to incorrect SAS URL` | Adres URL sygnatury dostępu Współdzielonego **Data rozpoczęcia** i **Data zakończenia** parametrów (`st` i `se` podciągów) muszą mieć format pełnej daty/godziny, takich jak `11-02-2017T00:00:00Z`. Skrócona wersje nie są prawidłowe. (Niektóre polecenia w interfejsie wiersza polecenia platformy Azure może generować skróconą wartości domyślne.) | 
|  |  |  |

Aby uzyskać więcej informacji, zobacz [Using shared access signatures (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
