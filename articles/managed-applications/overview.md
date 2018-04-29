---
title: Omówienie usługi Azure zarządzane aplikacje | Dokumentacja firmy Microsoft
description: Zawiera opis pojęć dla platformy Azure zarządzanych aplikacji
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 04/13/2018
ms.author: tomfitz
ms.openlocfilehash: 35e30700270fb5757a80c4a6b8f231042f7640f3
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="azure-managed-applications-overview"></a>Omówienie aplikacji zarządzanych platformy Azure

Zarządzane aplikacje platformy Azure umożliwiają oferują rozwiązaniach w chmurze, które są łatwe w konsumentach napisanych wdrażania i obsługi. Wdrażanie infrastruktury i trwającej obsługi. Aby udostępnić zarządzanej aplikacji dla wszystkich klientów, należy opublikować w portalu Azure marketplace. Aby było to możliwe tylko do użytkowników w organizacji, należy opublikować do katalogu wewnętrznego. 

Zarządzanej aplikacji jest podobny do szablon rozwiązania w witrynie Marketplace, z jedną różnicą klucza. W aplikacji zarządzanej zasoby obsługi administracyjnej do grupy zasobów, które są zarządzane przez wydawcy aplikacji. Grupa zasobów jest obecny w subskrypcji klienta, ale tożsamości w dzierżawie wydawcy ma dostęp do grupy zasobów. Jako wydawca należy określić koszt pracownikiem pomocy technicznej rozwiązania.

## <a name="advantages-of-managed-applications"></a>Korzyści wynikające z zarządzanych aplikacji

Zarządzane aplikacje zmniejszyć bariery z klientami przy użyciu rozwiązań. Nie ma potrzeby doświadczenia w infrastrukturze chmury, aby użyć rozwiązania. Konsumenci mają ograniczony dostęp do kluczowych zasobów. Ich nie trzeba martwić popełnienia błędu podczas zarządzania nią. 

Zarządzane aplikacje umożliwiają ustanowić stałe relacje z przez użytkowników. Zdefiniuj warunki dla zarządzania aplikacji, a wszystkie opłaty są obsługiwane za pośrednictwem rozliczenia Azure.

Mimo że klienci wdrażać te aplikacje zarządzane w swoich subskrypcji, nie ma do obsługi, aktualizacji lub ich obsługi. Aby zapewnić, że wszyscy klienci używają wersji zatwierdzonych. Klienci nie mają do opracowywania wiedzy domeny specyficzne dla aplikacji do zarządzania tymi aplikacjami. Klienci automatycznie pobierać aktualizacje aplikacji bez konieczności martwić się o Rozwiązywanie problemów i diagnozowanie problemów z aplikacjami. 

Dla zespołów IT zarządzanych aplikacji umożliwiają oferowanie wstępnie zatwierdzonych rozwiązań dla użytkowników w organizacji. Upewnij się, że tych rozwiązań jest zgodne ze standardami w organizacji.

## <a name="types-of-managed-applications"></a>Typy zarządzane aplikacje

Możesz opublikować aplikacji zarządzanej zewnętrznie lub wewnętrznie.

![Publikowanie wewnętrznie i zewnętrznie](./media/overview/manage_app_options.png)

### <a name="service-catalog"></a>Katalog usług

Katalog usług jest wewnętrzny wykazu zatwierdzonych rozwiązań dla użytkowników w organizacji. Możesz używać katalogu w celu zapewnienia zgodności z niektórych standardów organizacji podczas ich zapewnienie rozwiązań dla organizacji. Pracownicy korzystają z katalogu można łatwo wykryć bogaty zestaw aplikacji, które są zalecane i zatwierdzone przez ich działów IT. Zostanie wyświetlona zarządzanych aplikacji, które udostępnić innym osobom w organizacji.

Aby dowiedzieć się, jak publikowanie aplikacji zarządzanych katalogu usług, zobacz [tworzenie aplikacji katalogu usługi](publish-service-catalog-app.md).

### <a name="marketplace"></a>Portal Marketplace

Dostawców chcą naliczać opłaty za swoje usługi można udostępnić zarządzanych aplikacji za pośrednictwem portalu Azure marketplace. Dostawcy publikuje aplikacji, jest dostępny dla użytkowników spoza organizacji. Z tego podejścia, dostawców usług zarządzanych (MSPs) niezależnym dostawcom oprogramowania (ISV) i system integratory (SIs) może oferować ich rozwiązania wszystkich klientów platformy Azure.

Informacje o publikowaniu zarządzanej aplikacji do witryny Marketplace, zobacz [tworzenie aplikacji marketplace](publish-marketplace-app.md).

## <a name="resource-groups-for-managed-applications"></a>Grupy zasobów zarządzanych aplikacji

Zazwyczaj zasobów zarządzanych aplikacji znajdują się w dwie grupy zasobów. Użytkownik zarządza jedna grupa zasobów oraz wydawcy zarządza grupie zasobów. Podczas definiowania zarządzanej aplikacji, wydawca Określa poziomy dostępu. Na poniższej ilustracji przedstawiono scenariusz, w którym wydawcy żądań rolę właściciela grupy zasobów zarządzanych. Wydawcy umieścić blokady tylko do odczytu w tej grupie zasobów dla konsumentów. Tożsamości wydawcy, które mają prawo dostępu do grupy zasobów zarządzanych są wykluczone blokady.

![Dostęp do grupy zasobów](./media/overview/access.png)

### <a name="application-resource-group"></a>Grupa zasobów aplikacji

Ta grupa zasobów zawiera wystąpienie zarządzanych aplikacji. Ta grupa zasobów może zawierać tylko jeden zasób. Typ zasobu w zarządzanej aplikacji jest **Microsoft.Solutions/applications**.

Klient ma pełny dostęp do grupy zasobów i używa go do zarządzania cyklem życia aplikacji zarządzanych.

### <a name="managed-resource-group"></a>Zarządzana grupa zasobów

Ta grupa zasobów zawiera zasoby, które są wymagane przez aplikację. Na przykład ta grupa zasobów zawiera maszyny wirtualne, konta magazynu i sieci wirtualne dla rozwiązania. Konsument ma ograniczony dostęp do tej grupy zasobów, ponieważ konsumenta nie zarządza poszczególnych zasobów zarządzanych aplikacji. Rola określona w definicji aplikacji zarządzanych odpowiada wydawcy dostęp do tej grupy zasobów. Na przykład wydawcy może zażądać roli właściciela lub współautora dla tej grupy zasobów.

Gdy użytkownik usunie aplikacja zarządzana, grupa zasobów zarządzanych również zostanie usunięta.

## <a name="next-steps"></a>Kolejne kroki

* Aby obejrzeć wprowadzenie do definiowania i wdrażania aplikacji zarządzanych, zobacz [tworzenie i wdrażanie platformy Azure zarządzanych aplikacji przy użyciu wiersza polecenia platformy Azure](managed-apps-quickstart-cli.md)
* Informacje dotyczące publikowania aplikacji wewnętrznych, zobacz [Utwórz aplikację usługi wykazu](publish-service-catalog-app.md).
* Uzyskać informacji dotyczących publikowania aplikacji zarządzanych do witryny marketplace, zobacz [tworzenie aplikacji marketplace](publish-marketplace-app.md).
