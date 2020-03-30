---
title: Konfigurowanie serwera konfiguracji do odzyskiwania po awarii serwerów fizycznych na platformie Azure przy użyciu usługi Azure Site Recovery | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób konfigurowania lokalnego serwera konfiguracji do odzyskiwania po awarii lokalnych serwerów fizycznych na platformie Azure.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: ramamill
ms.openlocfilehash: 902c14211e91a1500c6b50cd790b347e337c4f70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257877"
---
# <a name="set-up-the-configuration-server-for-disaster-recovery-of-physical-servers-to-azure"></a>Konfigurowanie serwera konfiguracji do odzyskiwania po awarii serwerów fizycznych na platformie Azure

W tym artykule opisano sposób konfigurowania środowiska lokalnego w celu rozpoczęcia replikowania serwerów fizycznych z systemem Windows lub Linux na platformę Azure.

## <a name="prerequisites"></a>Wymagania wstępne

W artykule założono, że masz już:
- Magazyn usług odzyskiwania w [witrynie Azure portal](https://portal.azure.com "Portal Azure").
- Komputer fizyczny, na którym ma być zainstalowany serwer konfiguracji.
- Jeśli na komputerze, na którym instalujesz serwer konfiguracji, wyłączono protokół TLS 1.0, upewnij się, że jest włączona funkcja TLs 1.2 i że na komputerze jest zainstalowana .NET Framework w wersji 4.6 lub nowszej (z włączoną silną kryptografią). [Dowiedz się więcej](https://support.microsoft.com/help/4033999/how-to-resolve-azure-site-recovery-agent-issues-after-disabling-tls-1).

### <a name="configuration-server-minimum-requirements"></a>Minimalne wymagania serwera konfiguracji
W poniższej tabeli wymieniono minimalne wymagania dotyczące sprzętu, oprogramowania i sieci dla serwera konfiguracji.
[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

> [!NOTE]
> Serwery proxy oparte na https nie są obsługiwane przez serwer konfiguracji.

## <a name="choose-your-protection-goals"></a>Wybierz swoje cele ochrony

1. W witrynie Azure portal przejdź do bloku magazynów **usług odzyskiwania** i wybierz magazyn.
2. W menu **Zasoby** przechowalni kliknij**krok 1** > **odzyskiwania** >  **witryny wprowadzenie:** > **Przygotuj cel Ochrona**infrastruktury .

    ![Wybieranie celów](./media/physical-azure-set-up-source/choose-goals.png)
3. W **obszarze Cel Ochrony**wybierz pozycję Do platformy **Azure** i **Nie zwirtualizowany/Inne**, a następnie kliknij przycisk **OK**.

    ![Wybieranie celów](./media/physical-azure-set-up-source/physical-protection-goal.png)

## <a name="set-up-the-source-environment"></a>Konfigurowanie środowiska źródłowego

1. W **aplikacji Przygotuj źródło**, jeśli nie masz serwera konfiguracji, kliknij **+Serwer konfiguracji,** aby go dodać.

   ![Konfiguracja źródła](./media/physical-azure-set-up-source/plus-config-srv.png)
2. W bloku **Dodaj serwer** sprawdź, czy **serwer konfiguracji** jest wyświetlany w **typie serwera**.
4. Pobierz plik instalacyjny ujednoliconej instalacji odzyskiwania witryny.
5. Pobierz klucz rejestracji magazynu. Klucz rejestracji jest potrzebny po uruchomieniu Instalatora ujednoliconego. Klucz jest ważny przez pięć dni po jego wygenerowaniu.

    ![Konfiguracja źródła](./media/physical-azure-set-up-source/set-source2.png)
6. Na komputerze używanym jako serwer konfiguracji uruchom **Instalatora ujednoliconego usługi Azure Site Recovery,** aby zainstalować serwer konfiguracji, serwer przetwarzania i główny serwer docelowy.

#### <a name="run-azure-site-recovery-unified-setup"></a>Uruchamianie ujednoliconej instalacji usługi Azure Site Recovery

> [!TIP]
> Rejestracja serwera konfiguracji kończy się niepowodzeniem, jeśli czas w zegarze systemowym komputera jest więcej niż pięć minut czasu lokalnego. Przed rozpoczęciem instalacji zsynchronizuj zegar systemowy z [serwerem czasu.](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service)

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> Serwer konfiguracji można zainstalować za pomocą wiersza polecenia. [Dowiedz się więcej](physical-manage-configuration-server.md#install-from-the-command-line).


## <a name="common-issues"></a>Typowe problemy

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Następne kroki

Następny krok obejmuje [konfigurowanie środowiska docelowego na](physical-azure-set-up-target.md) platformie Azure.
