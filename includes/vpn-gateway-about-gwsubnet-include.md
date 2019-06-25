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
ms.openlocfilehash: 0334f9fd2d749b88580ff3857d705de2ae961902
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183085"
---
Brama sieci wirtualnej używa specyficznej podsieci nazywanej podsiecią bramy. Podsieć bramy jest częścią zakresu adresów IP sieci wirtualnej, który jest wybierany podczas konfigurowania sieci wirtualnej. Zawiera ona adresy IP używane przez zasoby i usługi bramy sieci wirtualnej. Aby platforma Azure wdrożyła zasoby bramy, podsieć musi mieć nazwę „GatewaySubnet”. Nie można określić innej podsieci w celu wdrożenia w niej zasobów bramy. Jeśli nie będziesz mieć podsieci o nazwie „GatewaySubnet”, tworzenie bramy sieci VPN nie powiedzie się.

Podczas tworzenia podsieci bramy należy określić liczbę zawartych w niej adresów IP. Liczba potrzebnych adresów IP zależy od konfiguracji bramy sieci VPN, którą chce się utworzyć. Niektóre konfiguracje wymagają większej liczby adresów IP niż inne. Zalecamy utworzenie podsieci bramy korzystającej z maski /27 lub /28.

Jeśli zobaczysz błąd z informacją o tym, że przestrzeń adresowa nakłada się z podsiecią lub że podsieć nie zawiera się w przestrzeni adresowej sieci wirtualnej, sprawdź zakres adresów sieci wirtualnej. Możliwe, że w zakresie adresów utworzonym dla sieci wirtualnej nie masz wystarczającej liczby adresów IP. Na przykład jeśli podsieć domyślna obejmuje cały zakres adresów, nie ma już adresów IP do utworzenia dodatkowych podsieci. Możesz albo dostosować swoje podsieci w obrębie istniejącej przestrzeni adresowej w celu zwolnienia adresów IP, albo określić dodatkowy zakres adresów i w nim utworzyć podsieć bramy.