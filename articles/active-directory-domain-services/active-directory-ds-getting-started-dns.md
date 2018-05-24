---
title: 'Azure Active Directory Domain Services: Aktualizowanie ustawień DNS na potrzeby sieci wirtualnej platformy Azure | Microsoft Docs'
description: Wprowadzenie do usługi Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: d4f3e82c-6807-4690-b298-4eabad2b7927
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/26/2017
ms.author: maheshu
ms.openlocfilehash: 972b995d3768e765b95c136b3cfbee91ab0a88ab
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="enable-azure-active-directory-domain-services"></a>Włączanie usług Azure Active Directory Domain Services

## <a name="task-4-update-dns-settings-for-the-azure-virtual-network"></a>Zadanie 4. Aktualizowanie ustawień DNS dla sieci wirtualnej platformy Azure
W poprzednich zadaniach konfiguracji pomyślnie włączono usługi Azure Active Directory Domain Services dla katalogu. Następnym krokiem jest zapewnienie komputerom w sieci wirtualnej możliwości łączenia się z tymi usługami i korzystania z nich. W tym artykule zaktualizujesz ustawienia serwera DNS dla sieci wirtualnej, wskazując dwa adresy IP, pod którymi usługi Azure Active Directory Domain Services są dostępne w sieci wirtualnej.

Wykonaj poniższe kroki, aby zaktualizować ustawienia serwera DNS dla sieci wirtualnej, w której włączono usługi Azure Active Directory Domain Services:

1. Karta **Przegląd** zawiera zestaw **wymaganych kroków konfiguracji**, które należy wykonać po pełnym zaaprowizowaniu domeny zarządzanej. Pierwszym krokiem konfiguracji jest **zaktualizowanie ustawień serwera DNS dla sieci wirtualnej**.

    ![Usługi Domain Services — karta Przegląd po pełnej aprowizacji](./media/getting-started/domain-services-provisioned-overview.png)

2. Gdy domena zostanie w pełni zaaprowizowana, na tym kafelku będą wyświetlane dwa adresy IP. Każdy z tych adresów IP reprezentuje kontroler domeny dla domeny zarządzanej.

3. Aby skopiować pierwszy adres IP do schowka, kliknij przycisk kopiowania obok niego. Następnie kliknij przycisk **Skonfiguruj serwery DNS**.

4. Wklej pierwszy adres IP w polu tekstowym **Dodaj serwer DNS** w bloku **Serwery DNS**. Przewiń ekran w lewo, aby skopiować drugi adres IP, i wklej go w polu **Dodaj serwer DNS**.

    ![Usługi Domain Services — aktualizacja usługi DNS](./media/getting-started/domain-services-update-dns.png)

5. Gdy wszystko będzie gotowe do aktualizacji serwerów DNS dla sieci wirtualnej, kliknij pozycję **Zapisz**.

> [!NOTE]
> Maszyny wirtualne w sieci otrzymują nowe ustawienia DNS dopiero po ponownym uruchomieniu. Jeśli chcesz, aby od razu miały zaktualizowane ustawienia usługi DNS, wyzwól ich ponowne uruchomienie przez portal, program PowerShell lub interfejs wiersza polecenia.
>
>

## <a name="next-step"></a>Następny krok
[Zadanie 5. Włączanie synchronizacji skrótów haseł w usługach Azure Active Directory Domain Services](active-directory-ds-getting-started-password-sync.md)
