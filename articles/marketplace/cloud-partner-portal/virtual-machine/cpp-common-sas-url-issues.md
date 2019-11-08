---
title: Typowe problemy dotyczące adresów URL i poprawki dla portalu Azure Marketplace
description: Wyświetl listę typowych problemów dotyczących używania identyfikatorów URI sygnatury dostępu współdzielonego i możliwych rozwiązań.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 09/27/2018
ms.author: pabutler
ms.openlocfilehash: 502ba1a65f9b0740a51c7a4da219cc87af494f27
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73813315"
---
# <a name="common-sas-url-issues-and-fixes"></a>Typowe problemy i poprawki dotyczące adresów URL sygnatury dostępu współdzielonego

W poniższej tabeli przedstawiono niektóre typowe problemy występujące podczas pracy z sygnaturami dostępu współdzielonego (które są używane do identyfikowania i udostępniania przekazanych dysków VHD dla danego rozwiązania) wraz z sugerowanymi rozwiązaniami.

| **Wykonaj** | **Komunikat o niepowodzeniu** | **Wiązane** | 
| --------- | ------------------- | ------- | 
| *Wystąpił błąd &emsp;kopiowania obrazów* |  |  |
| nie znaleziono "?" w adresie URL sygnatury dostępu współdzielonego | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Zaktualizuj adres URL sygnatury dostępu współdzielonego za pomocą zalecanych narzędzi. |
| parametry "St" i "SE" nie są w adresie URL sygnatury dostępu współdzielonego | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Zaktualizuj adres URL sygnatury dostępu współdzielonego o poprawne wartości **daty rozpoczęcia** i **zakończenia** . | 
| "Sp = RL" nie jest adresem URL sygnatury dostępu współdzielonego | `Failure: Copying Images. Not able to download blob using provided SAS Uri` | Zaktualizuj adres URL sygnatury dostępu współdzielonego z uprawnieniami ustawionymi jako `Read` i `List`. | 
| Adres URL sygnatury dostępu współdzielonego zawiera białe znaki w nazwie dysku VHD | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Zaktualizuj adres URL sygnatury dostępu współdzielonego, aby usunąć białe znaki. |
| Błąd autoryzacji adresu URL sygnatury dostępu współdzielonego | `Failure: Copying Images. Not able to download blob due to authorization error` | Przejrzyj i popraw format identyfikatora URI sygnatury dostępu współdzielonego. Wygeneruj ponownie w razie potrzeby. |
| Parametry adresu URL sygnatury dostępu współdzielonego "St" i "SE" nie mają pełnej specyfikacji daty i godziny | `Failure: Copying Images. Not able to download blob due to incorrect SAS URL` | **Data rozpoczęcia** i Data **zakończenia** adresu URL sygnatury dostępu współdzielonego (`st` i `se` podciągów) muszą mieć pełny format datetime, taki jak `11-02-2017T00:00:00Z`. Skrócone wersje są nieprawidłowe. (Niektóre polecenia w interfejsie wiersza polecenia platformy Azure mogą domyślnie generować skrócone wartości). | 
|  |  |  |

Aby uzyskać więcej informacji, zobacz [Używanie sygnatur dostępu współdzielonego (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
