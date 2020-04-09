---
title: 'Praca zdalna z usługą P2S: Brama sieci VPN platformy Azure'
description: Na tej stronie opisano, jak można wykorzystać bramę sieci VPN, aby umożliwić pracę zdalną ze względu na pandemię COVID-19.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: alzam
ms.openlocfilehash: 2d07a13c654f30e48c37d2e8d3e801166e26f4f4
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886589"
---
# <a name="remote-work-using-azure-vpn-gateway-point-to-site"></a>Praca zdalna przy użyciu usługi Azure VPN Gateway Point-to-site

>[!NOTE]
>W tym artykule opisano, jak można korzystać z usługi Azure VPN Gateway, Azure, sieci Firmy Microsoft i ekosystemu partnerów platformy Azure do pracy zdalnej i łagodzenia problemów sieciowych, które występują z powodu kryzysu COVID-19.
>

W tym artykule opisano opcje, które są dostępne dla organizacji, aby skonfigurować dostęp zdalny dla swoich użytkowników lub uzupełnić istniejące rozwiązania o dodatkowe pojemności podczas epidemii COVID-19.

Rozwiązanie platformy Azure typu punkt-lokacja jest oparte na chmurze i można je szybko aprowizować, aby zaspokoić zwiększone zapotrzebowanie użytkowników na pracę z domu. Można go łatwo skalować i wyłączać równie łatwo i szybko, gdy zwiększona pojemność nie jest już potrzebna.

## <a name="about-point-to-site-vpn"></a><a name="p2s"></a>Informacje o sieci VPN typu punkt-lokacja

Połączenie bramy VPN Gateway typu punkt-lokacja pozwala utworzyć bezpieczne połączenie z siecią wirtualną z indywidualnego komputera klienckiego. Połączenie typu punkt-lokacja jest ustanawiane przez uruchomienie z komputera klienckiego. To rozwiązanie jest przydatne dla osób pracujących zdalnie, którzy chcą połączyć się z sieciami wirtualnymi platformy Azure lub lokalnymi centrami danych z lokalizacji zdalnej, takiej jak strona główna lub konferencja. W tym artykule opisano, jak umożliwić użytkownikom pracę zdalną na podstawie różnych scenariuszy.

W poniższej tabeli przedstawiono klienckie systemy operacyjne i dostępne opcje uwierzytelniania. Byłoby pomocne, aby wybrać metodę uwierzytelniania na podstawie systemu operacyjnego klienta, który jest już w użyciu. Na przykład wybierz OpenVPN z uwierzytelnianiem opartym na certyfikatach, jeśli masz mieszankę klienckich systemów operacyjnych, które muszą się połączyć. Należy również pamiętać, że sieć VPN typu punkt-lokacja jest obsługiwana tylko w bramkach sieci VPN opartych na trasach.

![punkt do lokacji](./media/working-remotely-support/ostable.png "System operacyjny")

## <a name="scenario-1---users-need-access-to-resources-in-azure-only"></a><a name="scenario1"></a>Scenariusz 1 — użytkownicy potrzebują dostępu tylko do zasobów na platformie Azure

W tym scenariuszu użytkownicy zdalni tylko trzeba uzyskać dostęp do zasobów, które znajdują się na platformie Azure.

![punkt do lokacji](./media/working-remotely-support/scenario1.png "Scenariusz 1")

Na wysokim poziomie są potrzebne następujące kroki, aby umożliwić użytkownikom bezpieczne łączenie się z zasobami platformy Azure:

1. Utwórz bramę sieci wirtualnej (jeśli jej nie ma).
2. Skonfiguruj sieć VPN typu punkt-lokacja w bramie.

   * W przypadku uwierzytelniania certyfikatów kliknij [ten link](vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw).
   * W przypadku OpenVPN kliknij [ten link](vpn-gateway-howto-openvpn.md).
   * W przypadku uwierzytelniania usługi Azure AD kliknij [ten link](openvpn-azure-ad-tenant.md).
   * Aby uzyskać informacje na temat rozwiązywania problemów z połączeniami typu "punkt-lokacja", kliknij [ten link](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
3. Pobierz i rozpowszechniaj konfigurację klienta sieci VPN.
4. Rozesłaj certyfikaty (jeśli wybrano uwierzytelnianie certyfikatów) do klientów.
5. Połącz się z siecią VPN platformy Azure.

## <a name="scenario-2---users-need-access-to-resources-in-azure-andor-on-prem-resources"></a><a name="scenario2"></a>Scenariusz 2 — użytkownicy potrzebują dostępu do zasobów na platformie Azure i/lub zasobów przedwczesnych

W tym scenariuszu użytkownicy zdalni muszą uzyskać dostęp do zasobów, które znajdują się na platformie Azure i w lokalnych centrach danych.

![punkt do lokacji](./media/working-remotely-support/scenario2.png "Scenariusz 2")

Na wysokim poziomie są potrzebne następujące kroki, aby umożliwić użytkownikom bezpieczne łączenie się z zasobami platformy Azure:

1. Utwórz bramę sieci wirtualnej (jeśli jej nie ma).
2. Konfigurowanie sieci VPN typu punkt-lokacja w bramie (patrz [scenariusz 1](#scenario1)).
3. Skonfiguruj tunel lokacja-lokacja na bramie sieci wirtualnej platformy Azure z włączoną usługą BGP.
4. Skonfiguruj urządzenie lokalne do łączenia się z bramą sieci wirtualnej platformy Azure.
5. Pobierz profil punktu lokacji z witryny Azure portal i rozpowszechniaj wśród klientów

Aby dowiedzieć się, jak skonfigurować tunel sieci VPN typu lokacja lokacja, zobacz [to łącze](vpn-gateway-howto-site-to-site-resource-manager-portal.md).

## <a name="faq-for-native-azure-certificate-authentication"></a><a name="faqcert"></a>Często zadawane pytania dotyczące natywnego uwierzytelniania certyfikatów platformy Azure

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faq-for-radius-authentication"></a><a name="faqradius"></a>Często zadawane pytania dotyczące uwierzytelniania RADIUS

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie połączenia P2S — uwierzytelnianie usługi Azure AD](openvpn-azure-ad-tenant.md)

* [Konfigurowanie połączenia P2S — uwierzytelnianie RADIUS](point-to-site-how-to-radius-ps.md)

* [Konfigurowanie połączenia P2S — uwierzytelnianie certyfikatów natywnych platformy Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN" jest znakiem towarowym firmy OpenVPN Inc.**