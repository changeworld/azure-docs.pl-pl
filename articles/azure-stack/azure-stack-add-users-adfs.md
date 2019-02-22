---
title: Dodawanie użytkowników do usług AD FS Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodawać użytkowników do wdrożenia usług AD FS usługi Azure Stack
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2019
ms.author: patricka
ms.reviewer: unknown
ms.lastreviewed: 02/21/2019
ms.openlocfilehash: 1b47739200c79317273ea0c788f21a7ee4a3b818
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2019
ms.locfileid: "56648510"
---
# <a name="add-azure-stack-users-in-ad-fs"></a>Dodaj użytkowników usługi Azure Stack w usługach AD FS
Możesz użyć **użytkownicy usługi Active Directory i komputery** przystawki można dodać użytkowników do środowiska usługi Azure Stack, korzystając z usług AD FS jako dostawcy tożsamości.

## <a name="add-windows-server-active-directory-users"></a>Dodawanie użytkowników usługi Active Directory systemu Windows Server
> [!TIP]
> W tym przykładzie używa domyślnego azurestack.local ASDK usługi active directory. 

1. Zaloguj się do komputera przy użyciu konta, zapewniając dostęp do narzędzi administracyjnych Windows i Otwórz nowe Microsoft Management Console (MMC).
2. Wybierz **Plik > Dodaj lub Usuń przystawkę**.
3. Wybierz **użytkowników usługi Active Directory i komputery** > **AzureStack.local** > **użytkowników**.
4. Wybierz **akcji** > **nowe** > **użytkownika**.
5. W nowy obiekt — użytkownik, zapewniają szczegółowe informacje o użytkowniku. Wybierz opcję **Dalej**.
6. Podaj i Potwierdź hasło.
7. Wybierz **dalej** Aby sfinalizować wartości. Wybierz **Zakończ** do utworzenia użytkownika.


## <a name="next-steps"></a>Kolejne kroki
[Tworzenie jednostek usługi](azure-stack-create-service-principals.md)