---
title: 'Azure Active Directory Domain Services: Aktualizowanie ustawień DNS dla sieci wirtualnej platformy Azure | Dokumentacja firmy Microsoft'
description: Wprowadzenie do usługi Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: d4f3e82c-6807-4690-b298-4eabad2b7927
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: ergreenl
ms.openlocfilehash: 4727c24c603e95aeee6214546e25a273aa652f4c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60417332"
---
# <a name="enable-azure-active-directory-domain-services"></a>Włączanie usług Azure Active Directory Domain Services

## <a name="task-4-update-dns-settings-for-the-azure-virtual-network"></a>Zadanie 4. Aktualizowanie ustawień DNS dla sieci wirtualnej platformy Azure
W poprzednich zadaniach konfiguracji pomyślnie włączono usługi Azure Active Directory Domain Services dla katalogu. Następnym krokiem jest umożliwienie komputerom w sieci wirtualnej łączenia się z tymi usługami i korzystania z nich. W tym artykule zaktualizujesz ustawienia serwera DNS dla sieci wirtualnej, wskazując dwa adresy IP, pod którymi usługi Azure Active Directory Domain Services są dostępne w sieci wirtualnej.

Wykonaj poniższe kroki, aby zaktualizować ustawienia serwera DNS dla sieci wirtualnej, w której włączono usługi Azure Active Directory Domain Services:


1. Karta **Przegląd** zawiera zestaw **wymaganych kroków konfiguracji**, które należy wykonać po pełnym zaaprowizowaniu domeny zarządzanej. Pierwszym krokiem konfiguracji jest **zaktualizowanie ustawień serwera DNS dla sieci wirtualnej**.

    ![Usługi Reliable Services — karta Przegląd](./media/getting-started/domain-services-provisioned-overview.png)

    > [!TIP]
    > Nie widzisz tego kroku konfiguracji? Jeśli ustawienia serwera DNS dla sieci wirtualnej są aktualne, kafelek „Zaktualizuj ustawienia serwera DNS dla sieci wirtualnej” na karcie Przegląd nie będzie wyświetlany.
    >
    >

2. Kliknij przycisk **Konfiguruj**, aby zaktualizować ustawienia serwera DNS dla sieci wirtualnej.

> [!NOTE]
> Maszyny wirtualne w sieci otrzymują nowe ustawienia DNS dopiero po ponownym uruchomieniu. Jeśli chcesz, aby od razu miały zaktualizowane ustawienia usługi DNS, wyzwól ich ponowne uruchomienie przez portal, program PowerShell lub interfejs wiersza polecenia.
>
>

## <a name="next-step"></a>Następny krok
[Zadanie 5. Włączanie synchronizacji skrótów haseł w usługach Azure Active Directory Domain Services](active-directory-ds-getting-started-password-sync.md)
