---
title: Wyłącz Azure Active Directory Domain Services | Microsoft Docs
description: Wyłącz Azure Active Directory Domain Services przy użyciu Azure Portal
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: iainfou
ms.openlocfilehash: d5917ad94212c8b18d4362528bdfbafb02aec808
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70171967"
---
# <a name="disable-azure-active-directory-domain-services-using-the-azure-portal"></a>Wyłącz Azure Active Directory Domain Services przy użyciu Azure Portal
W tym artykule pokazano, jak za pomocą Azure Portal wyłączyć usługi domenowe Azure Active Directory (AD) dla katalogu usługi Azure AD.

> [!WARNING]
> **Usuwanie jest trwałe i nie można go cofnąć.**
> Kontynuuj ostrożność. Po usunięciu domeny zarządzanej:
>   * Kontrolery domeny dla domeny zarządzanej są nieobsługiwane i usuwane z sieci wirtualnej.
>   * Dane w domenie zarządzanej są trwale usuwane. Obejmuje to niestandardowe jednostki organizacyjne, obiekty zasad grupy, niestandardowe rekordy DNS, nazwy główne usług, kont gMSA itp. utworzone w domenie zarządzanej.
>   * Maszyny połączone z domeną zarządzaną tracą swoją relację zaufania z domeną i muszą zostać odłączone od domeny.
>   * Nie można zalogować się na tych maszynach przy użyciu firmowych poświadczeń usługi AD. Zamiast tego Użyj poświadczeń administratora lokalnego dla komputera.
> Usunięcie domeny zarządzanej nie powoduje usunięcia katalogu usługi Azure AD lub w inny sposób nie wpływa na katalog.

Wykonaj następujące kroki, aby usunąć Azure AD Domain Services domenę zarządzaną:
1. Przejdź do [rozszerzenia Azure AD Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) w Azure Portal.
2. Kliknij nazwę domeny zarządzanej.

    ![Wybierz domenę do usunięcia](./media/getting-started/domain-services-delete-select-domain.png)

3. Na stronie **Przegląd** kliknij przycisk **Usuń** .

    ![Usuwanie domeny](./media/getting-started/domain-services-delete-domain.png)

4. Aby potwierdzić usunięcie, wpisz nazwę domeny DNS domeny zarządzanej. Po zakończeniu kliknij przycisk **Usuń** .

    ![Potwierdzenie usunięcia domeny](./media/getting-started/domain-services-delete-domain-confirm.png)

Usunięcie domeny zarządzanej może potrwać 15-20 minut lub dłużej.

Rozważ [udostępnienie opinii](contact-us.md) , aby pomóc nam zrozumieć, jakie funkcje ułatwią Azure AD Domain Services w przyszłości. Ta opinia pomoże nam w rozmieszczeniu usługi, aby lepiej odpowiadała potrzebom związanym z wdrażaniem i przypadkom użycia.
