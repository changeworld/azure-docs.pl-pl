---
title: Jak skonfigurować usługę w chmurze (portal) | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować usługi w chmurze na platformie Azure. Dowiedz się, jak zaktualizować konfigurację usługi w chmurze i skonfigurować dostęp zdalny do wystąpień ról. W tych przykładach użyto witryny Azure portal.
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 12/07/2016
ms.author: tagore
ms.openlocfilehash: 554d3e465b42ca889ba03565e87193f80e89ed1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75361011"
---
# <a name="how-to-configure-cloud-services"></a>Jak skonfigurować usługi w chmurze

Można skonfigurować najczęściej używane ustawienia usługi w chmurze w witrynie Azure portal. Jeśli chcesz zaktualizować pliki konfiguracyjne bezpośrednio, pobierz plik konfiguracji usługi do aktualizacji, a następnie przekaż zaktualizowany plik i zaktualizuj usługę w chmurze ze zmianami konfiguracji. Tak czy inaczej aktualizacje konfiguracji są wypychane do wszystkich wystąpień roli.

Można również zarządzać wystąpieniami ról usługi w chmurze lub pulpitu zdalnego do nich.

Platforma Azure może zapewnić dostępność usługi na poziomie 99,95 procent podczas aktualizacji konfiguracji, jeśli masz co najmniej dwa wystąpienia roli dla każdej roli. Dzięki temu jedna maszyna wirtualna może przetwarzać żądania klientów, podczas gdy druga jest aktualizowana. Aby uzyskać więcej informacji, zobacz [Umowy dotyczące poziomu usług](https://azure.microsoft.com/support/legal/sla/).

## <a name="change-a-cloud-service"></a>Zmienianie usługi w chmurze

Po otwarciu [witryny Azure portal](https://portal.azure.com/)przejdź do usługi w chmurze. W tym miejscu zarządzasz wieloma jego aspektami.

![Strona ustawień](./media/cloud-services-how-to-configure-portal/cloud-service.png)

Łącza **Ustawienia** lub **Wszystkie ustawienia** otworzą **ustawienia,** w których można zmienić właściwości, zmienić **konfigurację,** zarządzać **certyfikatami,** skonfigurować **Certificates** **reguły alertów**i zarządzać **użytkownikami,** którzy mają dostęp do tej usługi w chmurze.

![Ustawienia usługi w chmurze platformy Azure](./media/cloud-services-how-to-configure-portal/cs-settings-blade.png)

### <a name="manage-guest-os-version"></a>Zarządzanie wersją systemu operacyjnego gościa

Domyślnie platforma Azure okresowo aktualizuje system operacyjny gościa do najnowszego obsługiwanego obrazu w rodzinie systemów operacyjnych określonych w konfiguracji usługi (cscfg), takich jak Windows Server 2016.

Jeśli chcesz kierować reklamy na określoną wersję systemu operacyjnego, możesz ustawić ją w **programie Configuration**.

![Ustawianie wersji systemu operacyjnego](./media/cloud-services-how-to-configure-portal/cs-settings-config-guestosversion.png)

>[!IMPORTANT]
> Wybranie konkretnej wersji systemu operacyjnego powoduje wyłączenie automatycznych aktualizacji systemu operacyjnego i wprowadzanie poprawek do odpowiedzialności. Należy upewnić się, że wystąpienia roli otrzymują aktualizacje lub może narazić aplikację na luki w zabezpieczeniach.

## <a name="monitoring"></a>Monitorowanie

Alerty można dodawać do usługi w chmurze. Kliknij **pozycję Ustawienia** > **Reguły** > **alertów Dodaj alert**.

![](./media/cloud-services-how-to-configure-portal/cs-alerts.png)

W tym miejscu można skonfigurować alert. Z pola rozwijanego **Metryki** można skonfigurować alert dla następujących typów danych.

* Odczyt dysku
* Zapis dysku
* Sieć w
* Wyjście sieciowe
* Procent użycia procesora CPU

![](./media/cloud-services-how-to-configure-portal/cs-alert-item.png)

### <a name="configure-monitoring-from-a-metric-tile"></a>Konfigurowanie monitorowania z kafelka metryki

Zamiast używać**reguł alertów** **ustawień,** > możesz kliknąć jeden z kafelków metryk w sekcji **Monitorowanie** usługi w chmurze.

![Monitorowanie usług w chmurze](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)

W tym miejscu można dostosować wykres używany z kafelkiem lub dodać regułę alertu.

## <a name="reboot-reimage-or-remote-desktop"></a>Ponowne uruchamianie, ponowne imaż lub pulpit zdalny

Pulpit zdalny można skonfigurować za pośrednictwem [witryny Azure Portal (konfigurowanie pulpitu zdalnego),](cloud-services-role-enable-remote-desktop-new-portal.md) [programu PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)lub programu [Visual Studio.](cloud-services-role-enable-remote-desktop-visual-studio.md)

Aby ponownie uruchomić, ponownie zamów lub zdalnie do usługi w chmurze, wybierz wystąpienie usługi w chmurze.

![Wystąpienie usługi w chmurze](./media/cloud-services-how-to-configure-portal/cs-instance.png)

Następnie można zainicjować połączenie pulpitu zdalnego, zdalnie ponownie uruchomić wystąpienie lub zdalnie odtworzyć (zacznij od świeżego obrazu) wystąpienie.

![Przyciski wystąpienia usługi w chmurze](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)

## <a name="reconfigure-your-cscfg"></a>Ponowne konfigurowanie pliku cscfg

Może być konieczne ponowne skonfigurowanie usługi w chmurze za pośrednictwem pliku [konfiguracji usługi (cscfg).](cloud-services-model-and-package.md#cscfg) Najpierw musisz pobrać plik cscfg, zmodyfikować go, a następnie przesłać.

1. Kliknij ikonę **Ustawienia** lub łącze **Wszystkie ustawienia,** aby otworzyć **okno Ustawienia**.

    ![Strona ustawień](./media/cloud-services-how-to-configure-portal/cloud-service.png)
2. Kliknij element **Konfiguracja.**

    ![Blok konfiguracyjny](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)
3. Kliknij przycisk **Pobierz**.

    ![Pobierz](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)
4. Po zaktualizowaniu pliku konfiguracji usługi przekaż i zastosuj aktualizacje konfiguracji:

    ![Upload](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png)
5. Zaznacz plik cscfg i kliknij przycisk **OK**.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [wdrożyć usługę w chmurze](cloud-services-how-to-create-deploy-portal.md).
* Konfigurowanie [niestandardowej nazwy domeny](cloud-services-custom-domain-name-portal.md).
* [Zarządzanie usługą w chmurze](cloud-services-how-to-manage-portal.md).
* Konfigurowanie [certyfikatów ssl](cloud-services-configure-ssl-certificate-portal.md).



