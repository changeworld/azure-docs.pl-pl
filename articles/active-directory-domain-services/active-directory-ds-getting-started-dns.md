---
title: 'Azure Active Directory Domain Services: Aktualizowanie ustawień DNS na potrzeby sieci wirtualnej platformy Azure | Microsoft Docs'
description: Wprowadzenie do usługi Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: d4f3e82c-6807-4690-b298-4eabad2b7927
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: ergreenl
ms.openlocfilehash: 7d2902c997259fc115a1f204f123983038821887
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2018
ms.locfileid: "50157347"
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
