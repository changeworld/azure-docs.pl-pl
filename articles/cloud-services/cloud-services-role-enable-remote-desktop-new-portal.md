---
title: Włącz pulpit zdalny dla roli za pomocą portalu
titleSuffix: Azure Cloud Services
description: Jak skonfigurować aplikację usługi azure cloud service, aby umożliwić połączenia pulpitu zdalnego
services: cloud-services
documentationcenter: ''
author: mmccrory
ms.service: cloud-services
ms.topic: article
ms.date: 11/28/2016
ms.author: memccror
ms.openlocfilehash: d65f4b55be317234c10a0e90cfe413d9e38a6a90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247464"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services"></a>Włączanie połączenia pulpitu zdalnego dla roli w usługach w chmurze platformy Azure

> [!div class="op_single_selector"]
> * [Portal Azure](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [Powershell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Pulpit zdalny umożliwia dostęp do pulpitu roli uruchomionej na platformie Azure. Za pomocą połączenia pulpitu zdalnego można rozwiązywać i diagnozować problemy z aplikacją, gdy jest uruchomiona.

Połączenie pulpitu zdalnego w roli podczas tworzenia, dołączając moduły pulpitu zdalnego do definicji usługi lub włącz pulpit zdalny za pomocą rozszerzenia pulpitu zdalnego. Preferowanym podejściem jest użycie rozszerzenia Pulpit zdalny, ponieważ można włączyć pulpit zdalny nawet po wdrożeniu aplikacji bez konieczności ponownego wdrażania aplikacji.

## <a name="configure-remote-desktop-from-the-azure-portal"></a>Konfigurowanie pulpitu zdalnego z witryny Azure portal

Portal Azure używa metody rozszerzenia pulpitu zdalnego, dzięki czemu można włączyć pulpit zdalny nawet po wdrożeniu aplikacji. Ustawienia **pulpitu zdalnego** usługi w chmurze umożliwiają włączenie pulpitu zdalnego, zmianę lokalnego konta administratora używanego do łączenia się z maszynami wirtualnymi, certyfikat używany w uwierzytelniania i ustawienie daty wygaśnięcia.

1. Kliknij **pozycję Usługi w chmurze**, wybierz nazwę usługi w chmurze, a następnie wybierz pozycję Pulpit **zdalny**.

    ![Pulpit zdalny usług w chmurze](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop.png)

2. Wybierz, czy chcesz włączyć pulpit zdalny dla pojedynczej roli, czy dla wszystkich ról, a następnie zmień wartość przełącznika na **Włączone**.

3. Wypełnij wymagane pola dla nazwy użytkownika, hasła, wygaśnięcia i certyfikatu.

    ![Pulpit zdalny usług w chmurze](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Details.png)

   > [!WARNING]
   > Wszystkie wystąpienia roli zostaną ponownie uruchomione po pierwszym włączeniu pulpitu zdalnego i **wybraniu przycisku OK** (znacznik wyboru). Aby zapobiec ponownemu uruchomieniu, certyfikat używany do szyfrowania hasła musi być zainstalowany w roli. Aby zapobiec ponownemu uruchomieniu, [przekaż certyfikat dla usługi w chmurze,](cloud-services-configure-ssl-certificate-portal.md#step-3-upload-a-certificate) a następnie wróć do tego okna dialogowego.

4. W **obszarze Role**wybierz rolę, którą chcesz zaktualizować, lub wybierz pozycję **Wszystkie** dla wszystkich ról.

5. Po zakończeniu aktualizacji konfiguracji wybierz pozycję **Zapisz**. Zanim wystąpienia roli będą gotowe do odbierania połączeń, zajmie kilka chwil.

## <a name="remote-into-role-instances"></a>Zdalne do wystąpień roli

Po włączeniu pulpitu zdalnego w rolach można zainicjować połączenie bezpośrednio z witryny Azure portal:

1. Kliknij **pozycję Wystąpienia,** aby otworzyć ustawienia **wystąpień.**
2. Wybierz wystąpienie roli z skonfigurowanym pulpitem zdalnym.
3. Kliknij **przycisk Połącz,** aby pobrać plik RDP dla wystąpienia roli.

    ![Pulpit zdalny usług w chmurze](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Connect.png)

4. Kliknij **przycisk Otwórz,** a następnie kliknij przycisk **Połącz,** aby uruchomić połączenie pulpitu zdalnego.

>[!NOTE]
> Jeśli usługa w chmurze znajduje się za siecią sieciową, może być konieczne utworzenie reguł umożliwiających ruch na portach **3389** i **20000**.  Pulpit zdalny używa portu **3389**.  Wystąpienia usługi w chmurze są równoważenia obciążenia, więc nie można bezpośrednio kontrolować, które wystąpienie, aby połączyć się z.  Agenci *RemoteForwarder* i *RemoteAccess* zarządzają ruchem RDP i umożliwiają klientowi wysyłanie pliku cookie RDP i określanie pojedynczego wystąpienia, z które ma się połączyć.  *RemoteForwarder* i *RemoteAccess* agentów wymagają, aby port **20000*** jest otwarty, które mogą być blokowane, jeśli masz nsg.

## <a name="additional-resources"></a>Zasoby dodatkowe

[Jak skonfigurować usługi w chmurze](cloud-services-how-to-configure-portal.md)
