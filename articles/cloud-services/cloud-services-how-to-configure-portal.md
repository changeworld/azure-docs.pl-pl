---
title: Jak skonfigurować usługę w chmurze (portal) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować usługi w chmurze na platformie Azure. Dowiedz się zaktualizować konfigurację usługi w chmurze i skonfigurować dostęp zdalny do wystąpień roli. Te przykłady użycia witryny Azure portal.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 7308f3c0-825e-499d-bfa5-c60f86371921
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2016
ms.author: jeconnoc
ms.openlocfilehash: 4d8d3b93ef2a6347076fada53932b5fc56838d20
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61435811"
---
# <a name="how-to-configure-cloud-services"></a>Jak skonfigurować usługi w chmurze

Najczęściej używanych ustawień usługi w chmurze można skonfigurować w witrynie Azure portal. Jeśli wolisz bezpośrednio aktualizować pliki konfiguracji, pobierz odpowiedni plik, a następnie przekaż jego zaktualizowaną wersję i zaktualizuj usługę w chmurze, wprowadzając zmiany w konfiguracji. W obu przypadkach aktualizacje konfiguracji są przekazywane do wszystkich wystąpień ról.

Można również zarządzać wystąpień ról usługi w chmurze lub usług pulpitu zdalnego do nich.

Azure tylko można zapewnić dostępność usługi dostępności wynoszącej 99,95 procent podczas aktualizacji konfiguracji, jeśli masz co najmniej dwóch wystąpień roli dla każdej roli. Który umożliwia jedną maszynę wirtualną do przetwarzania żądań klientów, podczas gdy druga jest aktualizowana. Aby uzyskać więcej informacji, zobacz [umowy dotyczące poziomu usług](https://azure.microsoft.com/support/legal/sla/).

## <a name="change-a-cloud-service"></a>Zmiana usługi w chmurze

Po otwarciu [witryny Azure portal](https://portal.azure.com/), przejdź do usługi w chmurze. W tym miejscu możesz zarządzać wieloma aspektami.

![Strona Ustawienia](./media/cloud-services-how-to-configure-portal/cloud-service.png)

**Ustawienia** lub **wszystkie ustawienia** łącza zostanie otwarty **ustawienia** którym można zmienić **właściwości**, zmień  **Konfiguracja**, zarządzanie **certyfikaty**, skonfiguruj **reguły alertów**i zarządzanie nimi **użytkowników** mających dostęp do tej usługi w chmurze.

![Ustawienia usługi w chmurze platformy Azure](./media/cloud-services-how-to-configure-portal/cs-settings-blade.png)

### <a name="manage-guest-os-version"></a>Zarządzanie wersji systemu operacyjnego gościa

Domyślnie program Azure okresowo aktualizuje Twojego systemu operacyjnego gościa do najnowszych obsługiwanych obrazu w obrębie rodziny systemów operacyjnych, który został określony w konfiguracji usługi (cscfg), takich jak system Windows Server 2016.

Jeśli potrzebujesz pod kątem określonej wersji systemu operacyjnego, można ustawić **konfiguracji**.

![Ustaw wersję systemu operacyjnego](./media/cloud-services-how-to-configure-portal/cs-settings-config-guestosversion.png)

>[!IMPORTANT]
> Wybór określonej wersji systemu operacyjnego spowoduje wyłączenie automatycznego systemu operacyjnego, aktualizacji i sprawia, że stosowanie poprawek odpowiedzialny za. Upewnij się, że wystąpienia roli są otrzymywanie aktualizacji lub może narazić aplikację na luki w zabezpieczeniach.

## <a name="monitoring"></a>Monitorowanie

Alerty można dodać do swojej usługi w chmurze. Kliknij przycisk **ustawienia** > **reguł alertów** > **Dodaj alert dotyczący**.

![](./media/cloud-services-how-to-configure-portal/cs-alerts.png)

W tym miejscu możesz skonfigurować alert. Za pomocą **metryki** pole listy rozwijanej możesz skonfigurować alert dla następujących typów danych.

* Odczyt dysku
* Zapis dysku
* Sieć — wejście
* Sieć — wyjście
* Procent użycia procesora CPU

![](./media/cloud-services-how-to-configure-portal/cs-alert-item.png)

### <a name="configure-monitoring-from-a-metric-tile"></a>Konfigurowanie monitorowania z kafelka metryki

Zamiast używania **ustawienia** > **reguł alertów**, możesz kliknąć jeden z kafelków metryki w **monitorowanie** sekcja usługi w chmurze.

![Monitorowanie usługi w chmurze](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)

W tym miejscu możesz dostosować wykres używane z kafelkiem lub Dodaj regułę alertu.

## <a name="reboot-reimage-or-remote-desktop"></a>Ponowne uruchomienie komputera, odtworzenia z obrazu lub usług pulpitu zdalnego

Możesz skonfigurować usług pulpitu zdalnego za pośrednictwem [witryny Azure portal (Konfigurowanie pulpitu zdalnego)](cloud-services-role-enable-remote-desktop-new-portal.md), [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md), lub za pomocą [programu Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md).

Ponowne uruchomienie, odtworzenia z obrazu lub połącz się zdalnie z usługi w chmurze, wybierz wystąpienie usługi w chmurze.

![Wystąpienia usługi w chmurze](./media/cloud-services-how-to-configure-portal/cs-instance.png)

Następnie zainicjuj połączenie pulpitu zdalnego, zdalne ponowne uruchamianie wystąpienia lub zdalnie odtwarzanie z obrazu (rozpoczynać się od nowa obrazu) wystąpienia.

![Przyciski wystąpienia usługi chmury](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)

## <a name="reconfigure-your-cscfg"></a>Skonfiguruj ponownie swoje cscfg

Może być konieczne ponowne skonfigurowanie usługi w chmurze za pośrednictwem [konfiguracji usługi (cscfg)](cloud-services-model-and-package.md#cscfg) pliku. Najpierw należy pobrać pliku .cscfg, zmodyfikuj go, a następnie przekaż go.

1. Kliknij pozycję **ustawienia** ikonę lub **wszystkie ustawienia** łącze, aby otworzyć **ustawienia**.

    ![Strona Ustawienia](./media/cloud-services-how-to-configure-portal/cloud-service.png)
2. Kliknij pozycję **konfiguracji** elementu.

    ![Blok konfiguracji](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)
3. Kliknij przycisk **Pobierz**.

    ![Do pobrania](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)
4. Po zaktualizowaniu pliku konfiguracji usługi przekazywania i zastosować aktualizacji konfiguracji:

    ![Upload](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png)
5. Wybierz plik .cscfg, a następnie kliknij przycisk **OK**.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [wdrożyć usługę w chmurze](cloud-services-how-to-create-deploy-portal.md).
* Konfigurowanie [niestandardowej nazwy domeny](cloud-services-custom-domain-name-portal.md).
* [Zarządzanie usługą chmurze](cloud-services-how-to-manage-portal.md).
* Konfigurowanie [certyfikaty ssl](cloud-services-configure-ssl-certificate-portal.md).
