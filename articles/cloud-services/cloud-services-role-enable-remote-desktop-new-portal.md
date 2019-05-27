---
title: Włączanie połączeń usług pulpitu zdalnego dla roli w usługach Azure Cloud Services | Dokumentacja firmy Microsoft
description: Jak skonfigurować aplikację usługi chmury platformy azure, aby zezwolić na połączenia pulpitu zdalnego
services: cloud-services
documentationcenter: ''
author: mmccrory
manager: timlt
editor: ''
ms.assetid: 73ea1d64-1529-4d72-b58e-f6c10499e6bb
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: memccror
ms.openlocfilehash: 0c36dc5fb6b2754fc93a02e29d8d8ae74df36da7
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65963268"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services"></a>Włączanie połączeń usług pulpitu zdalnego dla roli w usługach Azure Cloud Services

> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [Program PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Program Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Pulpit zdalny zapewnia dostęp do pulpitu roli działających na platformie Azure. Podłączanie pulpitu zdalnego umożliwia rozwiązywanie problemów i diagnozowanie problemów z aplikacją, gdy jest on uruchomiony.

Można włączyć Podłączanie pulpitu zdalnego w roli podczas tworzenia przez moduły usług pulpitu zdalnego w definicji usługi lub użytkownik może włączyć Pulpit zdalny za pośrednictwem rozszerzenia usług pulpitu zdalnego. Jest preferowanym podejściem jest użycie rozszerzenia usług pulpitu zdalnego, jak można włączyć pulpitu zdalnego, nawet w przypadku, po wdrożeniu aplikacji bez konieczności ponownego wdrażania aplikacji.

## <a name="configure-remote-desktop-from-the-azure-portal"></a>Konfigurowanie pulpitu zdalnego w witrynie Azure portal

Witryna Azure portal korzysta z metody zdalnego pulpitu rozszerzenia, dzięki czemu można włączyć pulpitu zdalnego, nawet w przypadku, po wdrożeniu aplikacji. **Pulpitu zdalnego** ustawienia dla usług cloud Services umożliwia włączanie pulpitu zdalnego, zmienianie konta administratora lokalnego, używane do łączenia się z maszynami wirtualnymi, certyfikat używany w przypadku uwierzytelniania i ustaw czas wygaśnięcia Data.

1. Kliknij przycisk **usług w chmurze**, wybierz nazwę usługi w chmurze, a następnie wybierz **pulpitu zdalnego**.

    ![Pulpit zdalny usług chmury](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop.png)

2. Wybierz, czy chcesz włączyć Pulpit zdalny dla poszczególnych ról lub dla wszystkich ról, a następnie zmień wartość przełącznika do **włączone**.

3. Wypełnij wymagane pola dla nazwy użytkownika, hasła, wygaśnięcia i certyfikatu.

    ![Pulpit zdalny usług chmury](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Details.png)

   > [!WARNING]
   > Wszystkie wystąpienia roli zostanie ponownie uruchomiona podczas najpierw włączyć Pulpit zdalny i wybierz **OK** (znacznik wyboru). Aby uniknąć ponownego uruchomienia systemu, certyfikat używany do szyfrowania hasła musi być instalowany w roli. Aby uniknąć ponownego uruchomienia, [Przekaż certyfikat usługi w chmurze](cloud-services-configure-ssl-certificate-portal.md#step-3-upload-a-certificate) , a następnie wróć do tego okna dialogowego.

4. W **role**, wybierz rolę, którą chcesz zaktualizować, lub wybierz **wszystkich** dla wszystkich ról.

5. Po zakończeniu aktualizacji konfiguracji, wybierz **Zapisz**. Potrwa kilka chwil, zanim wystąpienia roli są gotowe do odbierania połączeń.

## <a name="remote-into-role-instances"></a>Połącz się zdalnie z wystąpień roli

Po włączeniu ról usług pulpitu zdalnego należy zainicjować połączenie bezpośrednio z witryny Azure portal:

1. Kliknij przycisk **wystąpień** otworzyć **wystąpień** ustawienia.
2. Wybierz wystąpienie roli, które ma pulpitu zdalnego skonfigurowane.
3. Kliknij przycisk **Connect** można pobrać pliku RDP dla wystąpienia roli.

    ![Pulpit zdalny usług chmury](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Connect.png)

4. Kliknij przycisk **Otwórz** i następnie **Connect** zacząć Podłączanie pulpitu zdalnego.

>[!NOTE]
> Jeśli usługa w chmurze znajdują się za zaporą sieciowej grupy zabezpieczeń, może być konieczne utworzenie reguły zezwalające na ruch na portach **3389** i **20000**.  Pulpit zdalny używa portu **3389**.  Wystąpienia usługi w chmurze jest równoważone, więc nie można bezpośrednio kontrolować, które wystąpienie, aby nawiązać połączenie.  *RemoteForwarder* i *RemoteAccess* agentów zarządzania ruchem RDP i umożliwić klientom Wyślij plik cookie protokołu RDP i określ poszczególnych wystąpień, aby nawiązać połączenie.  *RemoteForwarder* i *RemoteAccess* agenci wymagają tego portu **20000*** jest otwarty, które mogą być zablokowane, jeśli sieciowa grupa zabezpieczeń.

## <a name="additional-resources"></a>Dodatkowe zasoby

[Jak skonfigurować usługi w chmurze](cloud-services-how-to-configure-portal.md)
