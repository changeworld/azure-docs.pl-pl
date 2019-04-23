---
title: Konfigurowanie serwera konfiguracji na potrzeby odzyskiwania po awarii serwerów fizycznych na platformie Azure przy użyciu usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania w środowisku lokalnym serwerem konfiguracji na potrzeby odzyskiwania po awarii lokalnych serwerów fizycznych na platformę Azure.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/14/2019
ms.author: ramamill
ms.openlocfilehash: 5f0578026e95378065fc68198434e347a87eb1fe
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60149023"
---
# <a name="set-up-the-configuration-server-for-disaster-recovery-of-physical-servers-to-azure"></a>Konfigurowanie serwera konfiguracji na potrzeby odzyskiwania po awarii serwerów fizycznych na platformę Azure

W tym artykule opisano sposób konfigurowania środowiska lokalnego do uruchamiania replikacji serwerów fizycznych z systemem Windows lub Linux na platformie Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Tego artykułu przyjęto założenie, że masz już:
- Magazyn usługi Recovery Services w [witryny Azure portal](https://portal.azure.com "witryny Azure portal").
- Komputer fizyczny, na którym jest instalowany na serwerze konfiguracji.
- Jeśli protokół TLS 1.0 zostały wyłączone na komputerze, na którym instalujesz serwer konfiguracji, upewnij się, włączenie protokołu TLs 1.2 i że .NET Framework w wersji 4.6 lub nowszy jest zainstalowany na maszynie (za pomocą silnej kryptografii wyłączone). [Dowiedz się więcej](https://support.microsoft.com/help/4033999/how-to-resolve-azure-site-recovery-agent-issues-after-disabling-tls-1).

### <a name="configuration-server-minimum-requirements"></a>Minimalne wymagania dotyczące serwera konfiguracji
W poniższej tabeli wymieniono minimalnych wymagań sprzętowych, oprogramowania i wymagania dotyczące sieci dla serwera konfiguracji.
[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

> [!NOTE]
> Serwery proxy oparty na protokole HTTPS nie są obsługiwane przez serwer konfiguracji.

## <a name="choose-your-protection-goals"></a>Wybranie celów ochrony

1. W witrynie Azure portal przejdź do **usługi Recovery Services** magazyny bloku i wybierz swój magazyn.
2. W **zasobów** menu magazynu kliknij **wprowadzenie** > **Site Recovery** > **krok 1: Przygotowanie infrastruktury** > **Cel ochrony**.

    ![Wybieranie celów](./media/physical-azure-set-up-source/choose-goals.png)
3. W **cel ochrony**, wybierz opcję **na platformę Azure** i **bez wirtualizacji/inne**, a następnie kliknij przycisk **OK**.

    ![Wybieranie celów](./media/physical-azure-set-up-source/physical-protection-goal.png)

## <a name="set-up-the-source-environment"></a>Konfigurowanie środowiska źródłowego

1. W **przygotowywanie źródła**, jeśli nie masz serwera konfiguracji, kliknij przycisk **+ serwer konfiguracji** dodanie.

   ![Konfiguracja źródła](./media/physical-azure-set-up-source/plus-config-srv.png)
2. W **Dodaj serwer** bloku, sprawdź, czy **serwera konfiguracji** pojawia się w **typ serwera**.
4. Pobierz plik instalacyjny Site Recovery Unified Setup.
5. Pobierz klucz rejestracji magazynu. Po uruchomieniu ujednoliconego Instalatora, potrzebujesz klucza rejestracji. Klucz jest ważny przez pięć dni po jego wygenerowaniu.

    ![Konfiguracja źródła](./media/physical-azure-set-up-source/set-source2.png)
6. Na komputerze używasz jako serwera konfiguracji, uruchom **usługi Azure Site Recovery Unified Setup** zainstalował serwer konfiguracji, serwer przetwarzania oraz główny serwer docelowy.

#### <a name="run-azure-site-recovery-unified-setup"></a>Uruchom usługę Azure Site Recovery ujednoliconej konfiguracji

> [!TIP]
> Rejestracja serwera konfiguracji nie powiedzie się, jeśli więcej niż pięć minut zniżki w stosunku do czasu lokalnego czasu zegara systemowego. Synchronizuj zegara systemowego z [serwer czasu](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service) przed rozpoczęciem instalacji.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> Za pomocą wiersza polecenia można zainstalować na serwerze konfiguracji. [Dowiedz się więcej](physical-manage-configuration-server.md#install-from-the-command-line).


## <a name="common-issues"></a>Typowe problemy

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Kolejne kroki

Następny krok polega na [Konfigurowanie środowiska docelowego](physical-azure-set-up-target.md) na platformie Azure.
