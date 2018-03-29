---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3d76f955f8d8d3d2b269c09387717a6571adf8c3
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
---
Brama sieci wirtualnej używa specyficznej podsieci nazywanej podsiecią bramy. Podsieć bramy jest częścią zakresu adresów IP sieci wirtualnej, który jest wybierany podczas konfigurowania sieci wirtualnej. Zawiera ona adresy IP używane przez zasoby i usługi bramy sieci wirtualnej. Aby platforma Azure wdrożyła zasoby bramy, podsieć musi mieć nazwę „GatewaySubnet”. Nie można określić innej podsieci w celu wdrożenia w niej zasobów bramy. Jeśli nie będziesz mieć podsieci o nazwie „GatewaySubnet”, tworzenie bramy sieci VPN nie powiedzie się.

Podczas tworzenia podsieci bramy należy określić liczbę zawartych w niej adresów IP. Liczba potrzebnych adresów IP zależy od konfiguracji bramy sieci VPN, którą chce się utworzyć. Niektóre konfiguracje wymagają większej liczby adresów IP niż inne. Zalecamy utworzenie podsieci bramy korzystającej z maski /27 lub /28.

Jeśli zobaczysz błąd z informacją o tym, że przestrzeń adresowa nakłada się z podsiecią lub że podsieć nie zawiera się w przestrzeni adresowej sieci wirtualnej, sprawdź zakres adresów sieci wirtualnej. Możliwe, że w zakresie adresów utworzonym dla sieci wirtualnej nie masz wystarczającej liczby adresów IP. Na przykład jeśli podsieć domyślna obejmuje cały zakres adresów, nie ma już adresów IP do utworzenia dodatkowych podsieci. Możesz albo dostosować swoje podsieci w obrębie istniejącej przestrzeni adresowej w celu zwolnienia adresów IP, albo określić dodatkowy zakres adresów i w nim utworzyć podsieć bramy.