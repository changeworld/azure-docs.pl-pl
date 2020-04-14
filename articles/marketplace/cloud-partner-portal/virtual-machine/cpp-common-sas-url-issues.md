---
title: Typowe problemy z adresami URL sygnatury dostępu Współdzielonego i poprawki w portalu Azure Marketplace
description: Lista typowych problemów wokół przy użyciu identyfikatorów identyfikatorów URI podpisu dostępu współdzielonego i możliwych rozwiązań.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/27/2018
ms.author: dsindona
ms.openlocfilehash: c575389538230218e1e6e4f172ebcfbee8ee51dc
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273464"
---
# <a name="common-sas-url-issues-and-fixes"></a>Typowe problemy i poprawki adresów URL sygnatury dostępu Współdzielonego

> [!IMPORTANT]
> Od 13 kwietnia 2020 r. rozpoczniemy przenoszenie zarządzania ofertami maszyny wirtualnej platformy Azure do centrum partnerskiego. Po migracji utworzysz oferty i zarządzasz nimi w Centrum partnerów. Postępuj zgodnie z instrukcjami [dotyczącymi typowych problemów z adresami URL sygnatury dostępu Współdzielonego i poprawek,](https://aka.ms/AzureSAS_URL_FAQ) aby zarządzać zmigrowanymi ofertami.

W poniższej tabeli wymieniono niektóre typowe problemy napotkane podczas pracy z podpisami dostępu współdzielonego (które są używane do identyfikowania i udostępniania przesłanych identyfikatorów VHD dla rozwiązania) wraz z sugerowanymi rozwiązaniami.

| **Problem** | **Komunikat o błędzie** | **Poprawka** | 
| --------- | ------------------- | ------- | 
| &emsp;  *Niepowodzenie kopiowania obrazów* |  |  |
| "?" nie znajduje się w adresie URL sygnatury dostępu Współdzielonego | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Zaktualizuj adres URL sygnatury dostępu Współdzielonego przy użyciu zalecanych narzędzi. |
| parametry "st" i "se" nie w adresie URL sygnatury dostępu Współdzielonego | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Zaktualizuj adres URL sygnatury dostępu **Współdzielonego** z odpowiednimi wartościami Data początkowa i **Data zakończenia.** | 
| "sp=rl" nie w adresie URL sygnatury dostępu Współdzielonego | `Failure: Copying Images. Not able to download blob using provided SAS Uri` | Zaktualizuj adres URL `Read` `List`sygnatury dostępu Współdzielonego z uprawnieniami ustawionymi jako i . | 
| Adres URL sygnatury dostępu Współdzielonego ma białe spacje w nazwie VHD | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Zaktualizuj adres URL sygnatury dostępu Współdzielonego, aby usunąć białe spacje. |
| Błąd autoryzacji adresu URL sygnatury dostępu Współdzielon | `Failure: Copying Images. Not able to download blob due to authorization error` | Przejrzyj i popraw format identyfikatora URI sygnatury dostępu Współdzielonego. W razie potrzeby zregeneruj. |
| Parametry adresu URL SAS "st" i "se" nie mają pełnej specyfikacji daty i godziny | `Failure: Copying Images. Not able to download blob due to incorrect SAS URL` | Parametry **daty rozpoczęcia** i`st` **daty zakończenia** adresu URL sygnatury dostępu Współdzielonego (i `se` podciągów) muszą mieć pełny format datetime, na przykład `11-02-2017T00:00:00Z`. Skrócone wersje są nieprawidłowe. (Niektóre polecenia w usłudze Azure CLI mogą domyślnie generować skrócone wartości). | 
|  |  |  |

Aby uzyskać więcej informacji, zobacz [Korzystanie z sygnatur dostępu współdzielonego (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
