---
title: Konfigurowanie niestandardowej nazwy domeny dla swojego wystąpienia usługi Azure API Management | Dokumentacja firmy Microsoft
description: W tym temacie opisano, jak skonfigurować niestandardową nazwę domeny dla swojego wystąpienia usługi Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 07/01/2019
ms.author: apimpm
ms.openlocfilehash: 59b44dcc9ec3a1f7c274f426a19aa8ed2258db3e
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509295"
---
# <a name="configure-a-custom-domain-name"></a>Konfigurowanie niestandardowej nazwy domeny

Podczas tworzenia wystąpienia usługi Azure API Management, platforma Azure przypisuje jej poddomeny azure-api.net (na przykład `apim-service-name.azure-api.net`). Jednakże, należy udostępnić punktami końcowymi usługi API Management przy użyciu własnej nazwy domeny niestandardowej, takich jak **contoso.com**. W tym samouczku przedstawiono sposób mapowania istniejącej niestandardowej nazwy DNS do punktów końcowych udostępnianych przez wystąpienia usługi API Management.

> [!WARNING]
> Klienci, którzy mają być używane przypinania certyfikatu, aby poprawić bezpieczeństwo swoich aplikacji, należy użyć niestandardowej nazwy domeny > i certyfikatu, który zarządza, nie domyślnego certyfikatu. Klienci, którzy przypiąć domyślnego certyfikatu zamiast tego będzie > zależna twardych we właściwościach certyfikatu nie kontrolują one, który nie jest zalecanym rozwiązaniem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, musisz mieć:

-   Aktywna subskrypcja platformy Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   Wystąpienia usługi API Management. Aby uzyskać więcej informacji, zobacz [Utwórz wystąpienie usługi Azure API Management](get-started-create-service-instance.md).
-   Nazwa domeny niestandardowej, którego właścicielem jest użytkownik. Nazwa domeny niestandardowej, którego chcesz użyć, należy kupowane niezależnie i hostowany na serwerze DNS. W tym temacie nie daje instrukcje na temat sposobu hostowania niestandardowej nazwy domeny.
-   Musi mieć ważny certyfikat przy użyciu klucza publicznego i prywatnego (. PLIK PFX). Podmiotu lub alternatywna nazwa podmiotu (SAN) musi być zgodna z nazwą domeny (umożliwia bezpieczne Uwidacznianie adresy URL za pośrednictwem protokołu SSL wystąpienia usługi API Management).

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Użyj witryny Azure portal, aby ustawić niestandardową nazwę domeny

1. Przejdź do wystąpienia usługi API Management w [witryny Azure portal](https://portal.azure.com/).
1. Wybierz **domeny niestandardowe i protokół SSL**.

    Istnieje kilka punktów końcowych, do których można przypisać niestandardową nazwę domeny. Obecnie dostępne są następujące punkty końcowe:

    - **Serwer proxy** (wartość domyślna to: `<apim-service-name>.azure-api.net`),
    - **Portal** (wartość domyślna to: `<apim-service-name>.portal.azure-api.net`),
    - **Zarządzanie** (wartość domyślna to: `<apim-service-name>.management.azure-api.net`),
    - **Menedżer sterowania usługami** (wartość domyślna to: `<apim-service-name>.scm.azure-api.net`).

    > [!NOTE]
    > Możesz zaktualizować wszystkie punkty końcowe lub niektóre z nich. Często klienci zaktualizować **Proxy** (ten adres URL jest używany do wywoływania interfejsu API dostępne za pośrednictwem usługi API Management) i **Portal** (portalu dla deweloperów adres URL). **Zarządzanie** i **SCM** punkty końcowe są używane wewnętrznie tylko właściciele wystąpienia usługi API Management i dlatego rzadziej przypisanych niestandardowej nazwy domeny. W większości przypadków można ustawić tylko jednego niestandardowej nazwy domeny dla danego punktu końcowego. Jednak **Premium** warstwa obsługuje ustawienie wielu nazw hostów dla **Proxy** punktu końcowego.

1. Wybierz punkt końcowy, który chcesz zaktualizować.
1. W oknie po prawej stronie kliknij **niestandardowe**.

    - W **niestandardowej nazwy domeny**, określ nazwę, której chcesz użyć. Na przykład `api.contoso.com`. Nazwy domen z symbolami wieloznacznymi (na przykład \*. domena.com) są również obsługiwane.
    - W **certyfikatu**, wybierz certyfikat z magazynu Key Vault. Możesz również przekazać prawidłowy. Plik PFX pliku i zapewnić jej **hasło**, jeśli certyfikat jest chroniony hasłem.

    > [!TIP]
    > Zalecamy używanie usługi Azure Key Vault do zarządzania certyfikatami i ustawić dla nich autorotate.
    > Jeśli używasz usługi Azure Key Vault do zarządzania certyfikat SSL domen niestandardowych, upewnij się, certyfikat jest wstawiany do usługi Key Vault [jako _certyfikatu_](https://docs.microsoft.com/rest/api/keyvault/CreateCertificate/CreateCertificate), a nie _klucz tajny_.
    >
    > Można pobrać certyfikatu SSL, API Management uprawnienia listy get wpisów tajnych w usłudze Azure Key Vault zawierające certyfikat. Przy użyciu witryny Azure portal wszystkie wymagane kroki konfiguracji zostaną wypełnione automatycznie. Korzystając z narzędzi wiersza polecenia lub interfejsu API zarządzania, te muszą być przyznane uprawnienia ręcznie. Jest to realizowane w dwóch krokach. Najpierw użyj upewnij się, że włączono tożsamości zarządzanej i Zanotuj identyfikator podmiotu zabezpieczeń wyświetlane na tej stronie stronie tożsamości zarządzanego wystąpienia usługi API Management. Po drugie nadaj listy uprawnień i uzyskać uprawnienia wpisów tajnych identyfikatorowi podmiotu zabezpieczeń w usłudze Azure Key Vault zawierające certyfikat.
    >
    > Jeśli certyfikat jest równa autorotate, API Management przejmą najnowszą wersję automatycznie bez żadnych przestojów w usłudze (jeśli warstwę usługi API Management jest objęta umową SLA — tzn. we wszystkich warstwach, z wyjątkiem warstwy Deweloper).

1. Kliknij przycisk Zastosuj.

    > [!NOTE]
    > Proces przypisywania certyfikatu może potrwać 15 minut lub dłużej w zależności od rozmiaru wdrożenia. Deweloper jednostki SKU ma Przestój, podstawowa i wyższych wersji bez przestojów.

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="next-steps"></a>Kolejne kroki

[Uaktualnij i zmieniać skalę swojej usługi](upgrade-and-scale.md)
