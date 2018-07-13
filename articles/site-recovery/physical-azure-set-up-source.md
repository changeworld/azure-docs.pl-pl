---
title: Konfigurowanie środowiska źródłowego (serwerów fizycznych na platformę Azure) | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania środowiska lokalnego do uruchamiania replikacji serwerów fizycznych z systemem Windows lub Linux na platformie Azure.
services: site-recovery
documentationcenter: ''
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: anoopkv
ms.openlocfilehash: 00b09db97e597521de5c73eeefab77b0dfa1304d
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38671026"
---
# <a name="set-up-the-source-environment-physical-server-to-azure"></a>Konfigurowanie środowiska źródłowego (serwera fizycznego na platformę Azure)

W tym artykule opisano sposób konfigurowania środowiska lokalnego do uruchamiania replikacji serwerów fizycznych z systemem Windows lub Linux na platformie Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Tego artykułu przyjęto założenie, że masz już:
1. Magazyn usługi Recovery Services w [witryny Azure portal](http://portal.azure.com "witryny Azure portal").
3. Komputer fizyczny, na którym jest instalowany na serwerze konfiguracji.

### <a name="configuration-server-minimum-requirements"></a>Minimalne wymagania dotyczące serwera konfiguracji
W poniższej tabeli wymieniono minimalnych wymagań sprzętowych, oprogramowania i wymagania dotyczące sieci dla serwera konfiguracji.
[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

> [!NOTE]
> Serwery proxy oparty na protokole HTTPS nie są obsługiwane przez serwer konfiguracji.

## <a name="choose-your-protection-goals"></a>Wybranie celów ochrony

1. W witrynie Azure portal przejdź do **usługi Recovery Services** magazyny bloku i wybierz swój magazyn.
2. W **zasobów** menu magazynu kliknij **wprowadzenie** > **Site Recovery** > **krok 1: Przygotowanie infrastruktury**   >  **Cel ochrony**.

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
> Za pomocą wiersza polecenia można zainstalować na serwerze konfiguracji. Aby uzyskać więcej informacji, zobacz [Instalowanie serwera konfiguracji za pomocą narzędzia wiersza polecenia](http://aka.ms/installconfigsrv).


## <a name="common-issues"></a>Typowe problemy

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Kolejne kroki

Następny krok polega na [Konfigurowanie środowiska docelowego](physical-azure-set-up-target.md) na platformie Azure.
