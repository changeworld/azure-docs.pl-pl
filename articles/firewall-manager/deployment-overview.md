---
title: Omówienie wdrożenia programu Azure firewall Manager w wersji zapoznawczej
description: Informacje o krokach wdrożenia wysokiego poziomu wymaganych przez usługę Azure firewall Manager w wersji zapoznawczej
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: df87e652d2969d4ae12e97a2b455648cf39382c3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502030"
---
# <a name="azure-firewall-manager-preview-deployment-overview"></a>Omówienie wdrożenia programu Azure firewall Manager w wersji zapoznawczej

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Istnieje więcej niż jeden sposób wdrożenia wersji zapoznawczej Menedżera zapory platformy Azure, ale zalecany jest następujący proces ogólny.

## <a name="prerequisites"></a>Wymagania wstępne

> [!IMPORTANT]
> Wersja zapoznawcza Menedżera zapory platformy Azure musi być jawnie włączona przy użyciu polecenia `Register-AzProviderFeature` PowerShell.
>W wierszu polecenia programu PowerShell uruchom następujące polecenia:
>
>```azure-powershell
>connect-azaccount
>Register-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network
>```
>Ukończenie rejestracji funkcji może potrwać do 30 minut. Uruchom następujące polecenie, aby sprawdzić stan rejestracji >:
>
>`Get-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network`



## <a name="general-deployment-process"></a>Ogólny proces wdrażania

1. Tworzenie architektury gwiazdy i szprych

   - Utwórz zabezpieczone centrum wirtualne przy użyciu Menedżera zapory platformy Azure i Dodaj połączenia sieci wirtualnej.<br>*oraz*<br>
   - Tworzenie wirtualnego centrum sieci WAN i Dodawanie połączeń sieci wirtualnej.
2. Wybierz dostawców zabezpieczeń

   - Gotowe podczas tworzenia bezpiecznego koncentratora wirtualnego.<br>*oraz*<br>
   - Przekonwertuj istniejące wirtualne koncentrator sieci WAN na zabezpieczenie koncentratora wirtualnego.
3. Tworzenie zasad zapory i kojarzenie ich z centrum

   - Ma zastosowanie tylko w przypadku korzystania z zapory platformy Azure.
   - Zasady zabezpieczeń jako usługi (SECaaS) innych firm są konfigurowane przez środowisko zarządzania partnerami.
4. Konfigurowanie ustawień trasy w celu kierowania ruchu do bezpiecznego centrum

   - Łatwo kieruj ruch do bezpiecznego centrum w celu filtrowania i rejestrowania bez tras zdefiniowanych przez użytkownika (UDR) w sieciach wirtualnych szprych przy użyciu strony ustawień bezpiecznej trasy wirtualnego centrum.

> [!NOTE]
> - Na każdym regionie nie może być więcej niż jeden koncentrator na wirtualną sieć WAN. Można jednak dodać wiele wirtualnych sieci WAN w regionie, aby to osiągnąć.
> - Nie ma nakładających się przestrzeni adresowych IP dla centrów w vWAN.
> - Połączenia sieci wirtualnej koncentratora muszą znajdować się w tym samym regionie co centrum.

## <a name="next-steps"></a>Następne kroki

- [Samouczek: Zabezpieczanie sieci w chmurze za pomocą usługi Azure firewall Manager w wersji zapoznawczej przy użyciu Azure Portal](secure-cloud-network.md)