---
title: Wymagania wstępne usługi Azure Data Catalog
description: Dowiedz się więcej o wymaganiach wstępnych, że chcesz rozpocząć pracę z usługą Azure Data Catalog.
services: data-catalog
author: steelanddata
ms.author: maroche
ms.assetid: ef497a54-dc4d-4820-b5bf-c361b64b964d
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 168083ed6226d8e1d55e116297dde5884875945b
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43041306"
---
# <a name="azure-data-catalog-prerequisites"></a>Wymagania wstępne usługi Azure Data Catalog

Należy zadbać o kilka rzeczy, przed rozpoczęciem konfigurowania usługi Azure Data Catalog. Nie martw się, ten proces nie trwa długo.

## <a name="azure-subscription"></a>Subskrypcja platformy Azure
Aby skonfigurować wykazu danych, musi być właścicielem lub współwłaścicielem subskrypcji platformy Azure.

Subskrypcje platformy Azure umożliwiają konfigurowanie dostępu do zasobów usługi w chmurze, takich jak Data Catalog. Subskrypcje pomocy można kontrolować sposób wykorzystania zasobów jest zgłaszane, rozliczane i opłacony. Każda subskrypcja może mieć oddzielne ustawienia rozliczeń i płatności, aby mogły być subskrypcji i planów, które zależą od działów, projektów, biur regionalnych i tak dalej. Do każdej usługi w chmurze należy do subskrypcji i musisz mieć subskrypcję, aby móc skonfigurować wykazu danych. Aby dowiedzieć się więcej, zobacz artykuł [Manage accounts, subscriptions, and administrative roles](../active-directory/users-groups-roles/directory-assign-admin-roles.md) (Zarządzanie kontami, subskrypcjami i rolami administracyjnymi).

## <a name="azure-active-directory"></a>Usługa Azure Active Directory
Aby skonfigurować Data Catalog, muszą być podpisane przy użyciu konta użytkownika usługi Azure Active Directory (Azure AD).

Usługa Azure AD umożliwia firmom łatwe zarządzanie tożsamościami i dostępem, zarówno w chmurze, jak i lokalnie. Użytkownicy mogą używać jednego konta firmowego lub szkolnego do pojedynczego logowania w dowolnej chmurze i lokalnych aplikacji sieci web. Data Catalog używa usługi Azure AD do uwierzytelniania logowania. Aby dowiedzieć się więcej, zobacz [co to jest Azure Active Directory?](../active-directory/fundamentals/active-directory-whatis.md).

> [!NOTE]
> Za pomocą [witryny Azure portal](http://portal.azure.com/), możesz zalogować się przy użyciu osobistego konta Microsoft lub usługi Azure Active Directory konto służbowe. Aby skonfigurować wykazu danych przy użyciu witryny Azure portal lub [portalu Data Catalog](http://www.azuredatacatalog.com), należy zalogować się przy użyciu konta usługi Azure Active Directory, a nie konta osobistego.
>
>

## <a name="active-directory-policy-configuration"></a>Konfiguracja zasad w usłudze Active Directory
Może wystąpić sytuacja, w przypadku, gdy należy zalogować się do portalu usługi Data Catalog, ale gdy spróbujesz zalogować się do narzędzia rejestracji źródła danych, możesz napotkać komunikat o błędzie, który uniemożliwia logowanie. To zachowanie problem może wystąpić tylko wtedy, gdy jesteś w sieci firmowej lub może wystąpić, tylko wtedy, gdy łączysz się z spoza sieci firmowej.

Narzędzia rejestracji źródła danych korzysta z uwierzytelniania formularzy do sprawdzania poprawności poświadczeń użytkownika w usłudze Active Directory. Aby ułatwić Ci się pomyślnie zalogować, administrator usługi Active Directory musi włączyć uwierzytelnianie formularzy, w ramach globalnych zasad uwierzytelniania.

Globalne zasady uwierzytelniania metody uwierzytelniania można włączyć oddzielnie dla połączeń intranetowych i ekstranetowych, jak pokazano na poniższym zrzucie ekranu. Błędy logowania może wystąpić, jeśli nie włączono uwierzytelniania formularzy dla sieci, z którego jest nawiązywane połączenie.

 ![Usługi Active Directory globalnych zasad uwierzytelniania](./media/data-catalog-prerequisites/global-auth-policy.png)

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji, zobacz [Konfigurowanie zasad uwierzytelniania](https://technet.microsoft.com/library/dn486781.aspx).
