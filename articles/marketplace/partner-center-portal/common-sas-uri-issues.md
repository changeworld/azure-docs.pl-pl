---
title: Typowe problemy i poprawki identyfikatora URI sygnatury dostępu Współdzielonego — azure marketplace
description: Typowe problemy napotkane i sugerowane rozwiązania podczas pracy z podpisami dostępu współdzielonego.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: fb86b1c5ec3be5a423dc2abd295aa8beb8f23f47
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266242"
---
# <a name="common-sas-uri-issues-and-fixes"></a>Typowe problemy i poprawki identyfikatora URI sygnatury dostępu Współdzielonego

> [!IMPORTANT]
> Przenosimy zarządzanie ofertami twojej maszyny wirtualnej platformy Azure z portalu cloud partner do centrum partnerów. Dopóki oferty nie zostaną zmigrowane, postępuj zgodnie z instrukcjami [dotyczącymi wspólnych problemów z identyfikatorem URI sas i poprawek](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-sas-url-issues) dla portalu cloud partner, aby zarządzać ofertami.

Poniżej przedstawiono typowe problemy napotkane podczas pracy z podpisami dostępu współdzielonego (które są używane do identyfikowania i udostępniania przekazanych identyfikatorów VHD dla rozwiązania) wraz z sugerowanymi rozwiązaniami.

| **Problem** | **Komunikat o błędzie** | **Poprawka** |
| --------- | ------------------- | ------- |
| *Niepowodzenie kopiowania obrazów* |  |  |
| "?" nie znajduje się w identyfikatorze URI sygnatury dostępu Współdzielonego | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Zaktualizuj identyfikator URI sygnatury dostępu Współdzielonego przy użyciu zalecanych narzędzi. |
| parametry "st" i "se" nie w identyfikatorze URI SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Zaktualizuj identyfikator URI sygnatury dostępu **Współdzielonego** z odpowiednimi wartościami Data rozpoczęcia i **Data zakończenia.** |
| "sp=rl" nie w identyfikatorze URI sygnatury dostępu Współdzielonego | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aktualizowanie identyfikatora URI sygnatury dostępu Współdzielonego z uprawnieniami ustawionymi jako `Read` i `List`. |
| Identyfikator URI SAS ma białe spacje w nazwie VHD | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Zaktualizuj identyfikator URI sygnatury dostępu Współdzielonego, aby usunąć białe spacje. |
| Błąd autoryzacji identyfikatora URI sygnatury dostępu Współdzielonego | `Failure: Copying Images. Not able to download blob due to authorization error.` | Przejrzyj i popraw format identyfikatora URI sygnatury dostępu Współdzielonego. W razie potrzeby zregeneruj. |
| SAS Parametry URI "st" i "se" nie mają pełnej specyfikacji daty i godziny | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | Parametry **daty rozpoczęcia** i **daty zakończenia** identyfikatora SAS URI (i`st` `se` podciągi) muszą mieć pełny format daty i godziny, taki jak `11-02-2017T00:00:00Z`. Skrócone wersje są nieprawidłowe (niektóre polecenia w usłudze Azure CLI mogą domyślnie generować skrócone wartości). |
|  |  |  |

Aby uzyskać szczegółowe informacje, zobacz [Korzystanie z sygnatur dostępu współdzielonego (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
