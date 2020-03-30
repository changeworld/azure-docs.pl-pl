---
title: Konfigurowanie niestandardowej nazwy domeny dla własnej hostowej bramy usługi Azure API Management | Dokumenty firmy Microsoft
description: W tym temacie opisano kroki konfigurowania niestandardowej nazwy domeny dla samodzielnej bramy usługi Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: 1f2184c7c62887a98a76877528b167d173c3d75b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335945"
---
# <a name="configure-a-custom-domain-name"></a>Konfigurowanie niestandardowej nazwy domeny

Podczas inicjowania obsługi administracyjnej [samodzielnie hostowane bramy usługi Azure API Management](self-hosted-gateway-overview.md) nie jest przypisana nazwa hosta i musi być odwołuje się do jego adresu IP. W tym artykule pokazano, jak mapować istniejącą niestandardową nazwę DNS (nazywaną również nazwą hosta) bramę hostowane samodzielnie.

> [!NOTE]
> Funkcja bramy hostowanego samodzielnie jest w wersji zapoznawczej. W wersji zapoznawczej brama hostowana samodzielnie jest dostępna tylko w warstwach Deweloper i Premium bez dodatkowych opłat. Warstwa deweloperów jest ograniczona do pojedynczego wdrożenia bramy hostowanej samodzielnie.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, musisz mieć:

-   Aktywna subskrypcja platformy Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   Wystąpienie zarządzania interfejsami API. Aby uzyskać więcej informacji, zobacz [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).
- Brama hostowana samodzielnie. Aby uzyskać więcej informacji, zobacz [Jak aprowizować bramę hostowane samodzielnie](api-management-howto-provision-self-hosted-gateway.md)
-   Niestandardowa nazwa domeny należąca do Ciebie lub Twojej organizacji. W tym temacie nie zawiera instrukcji dotyczących zakupu niestandardowej nazwy domeny.
-   Rekord DNS hostowany na serwerze DNS, który mapuje niestandardową nazwę domeny na adres IP bramy hostowanego przez siebie. W tym temacie nie zawiera instrukcji dotyczących hosta rekordu DNS.
-   Musisz mieć ważny certyfikat z kluczem publicznym i prywatnym (. PFX). Nazwa alternatywna podmiotu lub podmiotu (SAN) musi być zgodna z nazwą domeny (dzięki temu wystąpienie usługi API Management może bezpiecznie udostępniać adresy URL za 12.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-custom-domain-certificate-to-your-api-management-service"></a>Dodawanie niestandardowego certyfikatu domeny do usługi api Management

1. Wybierz **certyfikaty** z obszarze **Zabezpieczenia**.
2. Wybierz pozycję **+ Dodaj**.
3. Wprowadź nazwę zasobu certyfikatu w polu **identyfikatora.**
4. Wybierz plik zawierający certyfikat (. PFX) wybierając pole **Certyfikat** lub ikonę folderu obok niego.
5. Wprowadź hasło certyfikatu w polu **Hasło.**
6. Wybierz **pozycję Utwórz,** aby dodać certyfikat do usługi api Management.

## <a name="use-the-azure-portal-to-set-a-custom-domain-name-for-your-self-hosted-gateway"></a>Użyj witryny Azure Portal, aby ustawić niestandardową nazwę domeny dla bramy hostowanego samodzielnie

1. Wybierz **bramy** z sekcji **Ustawienia**.
2. Wybierz bramę hostowane samodzielnie, dla której chcesz skonfigurować nazwę domeny.
3. Wybierz **pozycję Nazwy hostów** w obszarze **Ustawienia**.
4. Wybierz **+ Dodaj**
5. Wprowadź nazwę zasobu nazwy hosta w polu **Nazwa.**
6. Wprowadź nazwę domeny w polu **Nazwa hosta.**
7. Wybierz certyfikat z listy rozwijanej **Certyfikat.**
8. Zaznacz pole wyboru **Negocjuj certyfikat klienta,** jeśli którykolwiek z interfejsów API udostępniane za pośrednictwem tej bramy używa uwierzytelniania certyfikatu klienta.
    > [!WARNING]
    > To ustawienie jest współużytkowane przez wszystkie nazwy domen skonfigurowane dla bramy.
9. Wybierz **pozycję Dodaj,** aby przypisać niestandardową nazwę domeny do wybranej bramy hostującej samodzielnie.

## <a name="next-steps"></a>Następne kroki

[Uaktualnianie i skalowanie usługi](upgrade-and-scale.md)
