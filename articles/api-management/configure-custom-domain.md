---
title: Konfigurowanie niestandardowej nazwy domeny dla wystąpienia usługi Azure API Management
titleSuffix: Azure API Management
description: W tym temacie opisano sposób konfigurowania niestandardowej nazwy domeny dla wystąpienia usługi Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 01/13/2020
ms.author: apimpm
ms.openlocfilehash: 4587909ad6fca6cdf21d54d11d89f797bbb29833
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335833"
---
# <a name="configure-a-custom-domain-name"></a>Konfigurowanie niestandardowej nazwy domeny

Podczas tworzenia wystąpienia usługi Azure API Management, platforma Azure `azure-api.net` przypisuje mu `apim-service-name.azure-api.net`poddomenę (na przykład). Można jednak udostępnić punkty końcowe usługi API Management przy użyciu własnej niestandardowej nazwy domeny, takiej jak **contoso.com**. W tym samouczku pokazano, jak mapować istniejącą niestandardową nazwę DNS na punkty końcowe udostępniane przez wystąpienie usługi API Management.

> [!IMPORTANT]
> Usługa API Management akceptuje tylko żądania z wartościami [nagłówka hosta](https://tools.ietf.org/html/rfc2616#section-14.23) odpowiadającymi domyślnej nazwie domeny lub dowolnej skonfigurowanej niestandardowej nazwie domen.

> [!WARNING]
> Klienci, którzy chcą używać przypinania certyfikatów w celu zwiększenia bezpieczeństwa swoich aplikacji, muszą używać niestandardowej nazwy domeny i certyfikatu, którymi zarządzają, a nie certyfikatu domyślnego. Klienci, którzy przypnieją domyślny certyfikat, będą musieli uzależnić się od właściwości certyfikatu, którego nie kontrolują, co nie jest zalecaną praktyką.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, musisz mieć:

-   Aktywna subskrypcja platformy Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   Wystąpienie zarządzania interfejsami API. Aby uzyskać więcej informacji, zobacz [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).
-   Niestandardowa nazwa domeny należąca do Ciebie lub Twojej organizacji. W tym temacie nie zawiera instrukcji dotyczących zakupu niestandardowej nazwy domeny.
-   Rekord CNAME hostowany na serwerze DNS, który mapuje niestandardową nazwę domeny na domyślną nazwę domeny wystąpienia zarządzania interfejsami API. W tym temacie nie zawiera instrukcji dotyczących hosta rekordu CNAME.
-   Musisz mieć ważny certyfikat z kluczem publicznym i prywatnym (. PFX). Nazwa alternatywna podmiotu lub podmiotu (SAN) musi być zgodna z nazwą domeny (dzięki temu wystąpienie usługi API Management może bezpiecznie udostępniać adresy URL za 12.

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Ustawianie niestandardowej nazwy domeny za pomocą portalu Azure

1. Przejdź do wystąpienia usługi API Management w [witrynie Azure portal](https://portal.azure.com/).
1. Wybierz **pozycję Domeny niestandardowe**.

    Istnieje wiele punktów końcowych, do których można przypisać niestandardową nazwę domeny. Obecnie dostępne są następujące punkty końcowe:

    - **Brama** (domyślnie: `<apim-service-name>.azure-api.net`),
    - **Portal** (domyślnie: `<apim-service-name>.portal.azure-api.net`),
    - **Zarządzanie** (domyślnie: `<apim-service-name>.management.azure-api.net`),
    - **SCM** (domyślnie: `<apim-service-name>.scm.azure-api.net`),
    - **NewPortal** (domyślnie: `<apim-service-name>.developer.azure-api.net`).

    > [!NOTE]
    > Tylko punkt końcowy **bramy** jest dostępny dla konfiguracji w warstwie Zużycie.
    > Można zaktualizować wszystkie punkty końcowe lub niektóre z nich. Zazwyczaj klienci **aktualizują bramę** (ten adres URL jest używany do wywoływania interfejsu API udostępniane za pośrednictwem zarządzania interfejsami API) i **portal** (adres URL portalu dewelopera).
    > Punkty końcowe **zarządzania** i **SCM** są używane wewnętrznie tylko przez właścicieli wystąpień usługi API Management i w związku z tym rzadziej przypisywano niestandardową nazwę domeny.
    > Warstwa **Premium** obsługuje ustawianie wielu nazw hostów dla punktu końcowego **bramy.**

1. Wybierz punkt końcowy, który chcesz zaktualizować.
1. W oknie po prawej stronie kliknij pozycję **Niestandardowe**.

    - W **niestandardowej nazwie domeny**określ nazwę, której chcesz użyć. Na przykład `api.contoso.com`.
    - W **certyfikacie**wybierz certyfikat z magazynu kluczy. Możesz również przesłać prawidłowy plik . PFX i podać **hasło**, jeśli certyfikat jest chroniony hasłem.

    > [!NOTE]
    > Nazwy domen z symbolami `*.contoso.com` wieloznaczymi, na przykład są obsługiwane we wszystkich warstwach z wyjątkiem warstwy Zużycie.

    > [!TIP]
    > Zalecamy używanie usługi Azure Key Vault do zarządzania certyfikatami i ustawiania ich do autorotate.
    > Jeśli używasz usługi Azure Key Vault do zarządzania certyfikatem TLS/SSL domeny niestandardowej, upewnij się, że certyfikat jest wstawiany do Magazynu [kluczy jako _certyfikat,_](https://docs.microsoft.com/rest/api/keyvault/CreateCertificate/CreateCertificate)a nie _tajny._
    >
    > Aby pobrać certyfikat TLS/SSL, usługa API Management musi mieć listę i uzyskać uprawnienia do wpisów tajnych w magazynie kluczy azure zawierającego certyfikat. Podczas korzystania z witryny Azure portal wszystkie niezbędne kroki konfiguracji zostaną wykonane automatycznie. W przypadku korzystania z narzędzi wiersza polecenia lub interfejsu API zarządzania te uprawnienia muszą być przyznane ręcznie. Odbywa się to w dwóch etapach. Najpierw użyj strony Tożsamości zarządzane w wystąpieniu usługi API Management, aby upewnić się, że tożsamość zarządzana jest włączona, i zanotuj identyfikator główny wyświetlany na tej stronie. Po drugie należy podać listę uprawnień i uzyskać uprawnienia do wpisów tajnych do tego identyfikatora głównego w magazynie azure key vault zawierający certyfikat.
    >
    > Jeśli certyfikat jest ustawiony na autorotate, zarządzanie interfejsem API automatycznie odbierze najnowszą wersję bez żadnych przestojów do usługi (jeśli warstwa zarządzania interfejsem API ma SLA — czyli we wszystkich warstwach z wyjątkiem warstwy dewelopera).

1. Kliknij przycisk Zastosuj.

    > [!NOTE]
    > Proces przypisywania certyfikatu może potrwać 15 minut lub więcej w zależności od rozmiaru wdrożenia. SKU dewelopera ma przestoje, podstawowe i wyższe jednostki SKU nie mają przestojów.

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="dns-configuration"></a>Konfiguracja usługi DNS

Podczas konfigurowania systemu DNS dla niestandardowej nazwy domeny dostępne są dwie opcje:

-   Skonfiguruj rekord CNAME, który wskazuje punkt końcowy skonfigurowanej niestandardowej nazwy domeny.
-   Skonfiguruj rekord A, który wskazuje adres IP bramy usługi API Management.

> [!NOTE]
> Mimo że adres IP wystąpienia managment interfejsu API jest statyczny, może ulec zmianie w kilku scenariuszach. Z tego powodu zaleca się używanie CNAME podczas konfigurowania domeny niestandardowej. Należy wziąć to pod uwagę przy wyborze metody konfiguracji DNS. Przeczytaj więcej w [artykule dokumentacji IP](api-management-howto-ip-addresses.md#changes-to-the-ip-addresses) i faq [zarządzania interfejsami API](api-management-faq.md#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services).

## <a name="next-steps"></a>Następne kroki

[Uaktualnianie i skalowanie usługi](upgrade-and-scale.md)
